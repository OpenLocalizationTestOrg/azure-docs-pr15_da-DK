<properties
    pageTitle="Overvåge dit API'er med Azure API Management, begivenhed Hubs og Runscope"
    description="Northwind demonstrerer politikken log-eventhub ved forbindende Azure API Management, Azure begivenhed Hubs og Runscope for HTTP logføring og overvågning"
    services="api-management"
    documentationCenter=""
    authors="darrelmiller"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="darrmi"/>

# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-runscope"></a>Overvåge dit API'er med Azure API Management, begivenhed Hubs og Runscope

[API Management-tjenesten](api-management-key-concepts.md) indeholder mange funktioner til at forbedre behandling af HTTP-anmodninger sendes til din HTTP-API. Eksistensen af anmodninger om og svar er dog midlertidige. Anmodningen foretages, og teksten flyder gennem API Management-tjenesten til din back end-API. Din API behandler anmodningen og et svar flyder tilbage gennem til API forbruger. Tjenesten API Management bevarer nogle vigtige statistik om API'er til visning i Publisher portalen dashboard, men videre fra, at oplysningerne er forsvundet.

Du kan sende eventuelle oplysninger fra anmodning og svar til en [Azure begivenhed Hub](../event-hubs/event-hubs-what-is-event-hubs.md)ved hjælp af [log-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) [politik](api-management-howto-policies.md) i API Management-tjenesten. Der findes en række forskellige årsager, hvorfor du overveje at oprette begivenheder fra HTTP-meddelelser, der sendes til din API'er. Eksempler kan nævnes overvågningslog over opdateringer, brugsstatistik, undtagelse advarsler og 3 part integrationer.   

Denne artikel beskrives, hvordan du registrere hele HTTP-anmodning og svar meddelelsen, sende det til en begivenhed Hub og derefter videresende meddelelsen til en tredjepart tjeneste, som indeholder HTTP logføring og overvågning tjenester.

## <a name="why-send-from-api-management-service"></a>Hvorfor sende fra API Management-tjenesten?
Det er muligt at skrive http-programmer, som kan tilslutte HTTP API rammer, der kan registrere HTTP-anmodninger og svar og feed dem til logføring og overvågning systemer. Ulempen denne fremgangsmåde er HTTP-program skal integreres i back-end API og skal stemme overens med platformen API. Hvis der er flere API'er skal hver enkelt installere program. Der er ofte årsager hvorfor back-end API'er ikke kan opdateres.

Ved hjælp af tjenesten Azure API Management du integrere systemet med infrastrukturen for logføring er et centralt og platform uafhængige løsning. Det er også SVG delvis på grund af [geografisk gentagelse](api-management-howto-deploy-multi-region.md) funktionerne i Azure API Management.

## <a name="why-send-to-an-azure-event-hub"></a>Hvorfor sende til en Azure begivenhed Hub?
Det er en berettiget at spørge, hvorfor oprette en politik, der er specifikke for Azure begivenhed Hubs? Der er mange forskellige steder, hvor jeg vil muligvis logge min anmodninger. Hvorfor ikke bare sende anmodningerne direkte til den endelige destination?  Det er en indstilling. Når du laver logføring anmodninger fra en API management-tjenesten, er det dog nødvendigt at overveje, hvordan logføring af meddelelser kan påvirke ydeevnen for API. Gradvist øges i belastning kan løses ved at øge tilgængelige forekomster af systemkomponenter eller ved at drage fordel af geografisk gentagelse. Korte spidser i trafik kan dog medføre anmodninger skal forsinkes markant, hvis anmodninger om at logføring infrastruktur begynder at sænke belastning.

Azure begivenhed Hubs er udviklet til vandindtrængen store datamængder, med kapacitet til at håndtere et langt højere antal begivenheder, end antallet HTTP, som de fleste API'er proces. Begivenhed hubben fungerer som en slags avancerede bufferen mellem API management-tjenesten og infrastrukturen, som lagrer og behandle meddelelser. Dette sikrer ikke, at din API går ud over ydeevnen på grund af logføring infrastrukturen.  

Når dataene er blevet overført til en begivenhed Hub det er bevaret og venter på begivenheden Hub forbrugere at behandle den. Begivenhed hubben sig ikke hvordan det behandles, det er interesseret kun i at sikre, at meddelelsen vil blive leveret.     

Begivenhed Hubs har mulighed for at stream begivenheder til flere consumer grupper. Dette giver mulighed for hændelser, der skal behandles af helt forskellige systemer. Dette kan understøtte mange integrationsmuligheder uden at lægge addition forsinkelser i behandlingen af API-anmodning i API Management-tjenesten, som kun én begivenhed skal oprettes.

## <a name="a-policy-to-send-applicationhttp-messages"></a>En politik til at sende programmet/HTTP-meddelelser
En begivenhed Hub accepterer begivenhed data som en simpel streng. Indholdet af denne streng er helt op til dig. Hvis du vil kunne pakke op en HTTP-anmodning, og send den til begivenhed hubber har vi brug for til at formatere strengen med oplysninger om anmodningen eller svar. I situationer Sådan her ud, hvis der er et eksisterende format vi kan genbruge, og derefter vi ikke kan være nødvendigt at skrive vores egen parsing af kode. Har jeg betragtes som ved hjælp af [HAR](http://www.softwareishard.com/blog/har-12-spec/) til at sende HTTP-anmodninger om og svar. Dette format er dog optimeret til lagring af en sekvens af HTTP-anmodninger i JSON baseret format. Den indeholdt en antal obligatoriske elementer, der tilføjet unødvendige kompleksitet for scenarie med HTTP meddelelsen sendes over en netværksforbindelse.  

Et alternativ var at bruge den `application/http` medietype, som beskrevet i HTTP-specifikationen [RFC 7230](http://tools.ietf.org/html/rfc7230). Denne medietype bruger det nøjagtige samme format, der bruges til at sende faktisk HTTP meddelelser via en netværksforbindelse, men hele meddelelsen kan være placeret i brødteksten i en anden HTTP-anmodning. I dette tilfælde går vi blot bruge brødteksten som vores meddelelse til at sende til begivenhed hubber. Nemt, der er en parser, som findes i [Microsoft ASP.NET Web API 2.2 Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) biblioteker, der kan analysere dette format og konvertere den til oprindeligt `HttpRequestMessage` og `HttpResponseMessage` objekter.

Hvis du vil kunne oprette denne meddelelse, vi har brug for at drage fordel af C# baseret [politik udtryk](https://msdn.microsoft.com/library/azure/dn910913.aspx) i Azure API Management. Her er politikken som sender en HTTP anmodningsmeddelelsen til Azure begivenhed hubber.

       <log-to-eventhub logger-id="conferencelogger" partition-id="0">
       @{
           var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                       context.Request.Method,
                                                       context.Request.Url.Path + context.Request.Url.QueryString);

           var body = context.Request.Body?.As<string>(true);
           if (body != null && body.Length > 1024)
           {
               body = body.Substring(0, 1024);
           }

           var headers = context.Request.Headers
                                  .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                                  .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                  .ToArray<string>();

           var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

           return "request:"   + context.Variables["message-id"] + "\n"
                               + requestLine + headerString + "\r\n" + body;
       }
       </log-to-eventhub>

### <a name="policy-declaration"></a>Politik erklæring
Der nogle bestemte ting værd at nævne om denne politikudtryk. Politikken log-eventhub har en attribut, kaldet logføring-id, som henviser til navnet på logføring, der er oprettet i API Management-tjenesten. Oplysninger om hvordan du konfigurerer en begivenhed Hub logføring i API Management-tjenesten kan findes i dokumentet [Sådan logger du begivenheder til Azure begivenhed hubber i Azure API Management](api-management-howto-log-event-hubs.md). Den anden attribut er en valgfri parameter, der får begivenhed Hubs som partition til at gemme meddelelsen. Begivenhed Hubs Brug partitioner til at aktivere scalabilty og kræver mindst to. Sorteret leveringen af meddelelser er kun garanti inden for en partition. Hvis vi ikke Fortæl begivenhed Hub i hvilke partition placere meddelelsen, der skal bruges en round robin-algoritme til at distribuere afkrydsningsfeltet Indlæs. Som kan dog give nogle af vores meddelelser skal behandles defekt.  

### <a name="partitions"></a>Partitioner
For at sikre vores meddelelser leveres til forbrugere i rækkefølge og udnytte muligheden for indlæsning fordeling af partitioner, jeg har valgt at sende HTTP-anmodning om meddelelser til én partition og meddelelser om HTTP-svar til en anden partition. Dette sikrer en lige Indlæs fordeling, og vi kan garantere, at alle anmodninger skal blive brugt i rækkefølge, og alle svar skal blive brugt i rækkefølge. Det er muligt for et svar til at blive brugt, før den tilsvarende anmodningen, men som det ikke er et problem, som vi har en anden metode til korrelere anmodninger til svar, og vi vide, at anmodninger altid kommer før svar.

### <a name="http-payloads"></a>HTTP-overførsler
Efter bygning på `requestLine` vi Kontrollér Hvis anmodningsteksten skal blive afkortet. Anmodningsteksten afkortes til kun 1024. Dette kan øges, men individuelle begivenhed Hub meddelelser er begrænset til 256KB, så det er et sandsynligt, at nogle HTTP-meddelelse organisationer er ikke falder i en enkelt meddelelse. Når du udfører logføring og analytics kan en betydeligt mængde oplysninger udledes fra http-anmodning om linje og overskrifter. Desuden mange API anmodninger kun returnere small organer og tab af oplysninger værdien med afkorter større mængder er så ret minimale sammenlignes med reduktion i filoverførsel, behandling og lagerplads omkostninger for at holde alt indhold i brødteksten. Én endelige note om behandling af brødteksten er, at vi brug for til at videregive `true` til As<string>() metode fordi vi læser oplysningerne i brødteksten, men blev også vil back-end-API til at kunne læse brødteksten. Vi kan medføre brødteksten til at være bufferen, så den kan læses endnu en gang ved at overføre True til denne metode. Dette er vigtigt at være opmærksom på, hvis du har en API, der kræver overførsel af meget store filer eller bruger lang afstemning. I disse tilfælde vil det være bedst at undgå at læse teksten på alle.   

### <a name="http-headers"></a>HTTP-headere
HTTP-headere kan overføres blot via i meddelelsesformatet i et enkelt nøgleværdi par format. Vi har valgt at udelade bestemte sikkerhed følsomme felterne, for at undgå unødvendig lækker legitimationsoplysninger. Det sandsynligvis, API nøgler og andre legitimationsoplysninger skal anvendes til analytics formål. Hvis vi ønsker analyse af brugeren og det produkt de bruger derefter vi kan få, fra den `context` objekt og indsætte det på meddelelsen.     
### <a name="message-metadata"></a>Meddelelse Metadata
Når du opbygger hele meddelelsen skal sendes til begivenhed-hub, den første linje er ikke faktisk en del af den `application/http` meddelelse. Den første linje er ekstra metadata bestående af, om meddelelsen er en anmodning om eller svarmeddelelse og en meddelelses-id, som bruges til at koordinere anmodninger om til svar. Meddelelses-id'et er oprettet ved hjælp af en anden politik, der ser sådan ud:

    <set-variable name="message-id" value="@(Guid.NewGuid())" />

Vi kunne har oprettet anmodningsmeddelelsen, gemmes, i en variabel, indtil svaret blev returneret og derefter skal du blot sendt anmodning og svar som en enkelt meddelelse. Ved at sende anmodning og svar uafhængigt af hinanden, og brug af en meddelelses-id til at koordinere to, skal vi dog få lidt mere fleksibilitet i den meddelelsesstørrelse, muligheden for at drage fordel af flere partitioner, samtidig med at vedligeholde Meddelelsernes rækkefølge og anmodningen vises i vores logføring dashboard hurtigere. Der kan også være visse scenarier, hvor et gyldigt svar sendes aldrig til begivenhed hubben, sandsynligvis på grund af en anmodning om alvorlige fejl i tjenesten API Management, men vi stadig har en post for din anmodning.

Politikken til at sende HTTP svarmeddelelse ligner meget anmodningen, og så konfigurere fuldført politikken ser sådan ud:

      <policies>
        <inbound>
            <set-variable name="message-id" value="@(Guid.NewGuid())" />
            <log-to-eventhub logger-id="conferencelogger" partition-id="0">
              @{
                  var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                              context.Request.Method,
                                                              context.Request.Url.Path + context.Request.Url.QueryString);

                  var body = context.Request.Body?.As<string>(true);
                  if (body != null && body.Length > 1024)
                  {
                      body = body.Substring(0, 1024);
                  }

                  var headers = context.Request.Headers
                                       .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                                       .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                       .ToArray<string>();

                  var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

                  return "request:"   + context.Variables["message-id"] + "\n"
                                      + requestLine + headerString + "\r\n" + body;
              }
          </log-to-eventhub>
        </inbound>
        <backend>
            <forward-request follow-redirects="true" />
        </backend>
        <outbound>
            <log-to-eventhub logger-id="conferencelogger" partition-id="1">
              @{
                  var statusLine = string.Format("HTTP/1.1 {0} {1}\r\n",
                                                      context.Response.StatusCode,
                                                      context.Response.StatusReason);

                  var body = context.Response.Body?.As<string>(true);
                  if (body != null && body.Length > 1024)
                  {
                      body = body.Substring(0, 1024);
                  }

                  var headers = context.Response.Headers
                                                  .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                                  .ToArray<string>();

                  var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

                  return "response:"  + context.Variables["message-id"] + "\n"
                                      + statusLine + headerString + "\r\n" + body;
             }
          </log-to-eventhub>
        </outbound>
      </policies>

Den `set-variable` politik opretter en værdi, der er tilgængelige for både den `log-to-eventhub` politik i den `<inbound>` sektion og `<outbound>` sektion.  

## <a name="receiving-events-from-event-hubs"></a>Modtager begivenheder fra begivenhed Hubs
Begivenheder fra Azure begivenhed Hub modtages ved hjælp af [AMQP protocol](http://www.amqp.org/). Microsoft Service Bus teamet har foretaget klient biblioteker, der er tilgængelige for nemmere arket begivenheder. Der er to forskellige metoder, der understøttes, en der en *Direkte forbrugere* og den anden bruger den `EventProcessorHost` klasse. Eksempler på disse to metoder kan findes i [Begivenhed Hubs Programming Guide](../event-hubs/event-hubs-programming-guide.md). Den korte version af forskelle er, `Direct Consumer` giver dig fuld kontrol og `EventProcessorHost` betyder noget af VVS-arbejde til du men ud fra bestemte antagelser om, hvordan du vil behandle disse begivenheder.  

### <a name="eventprocessorhost"></a>EventProcessorHost
I dette eksempel vil vi bruge den `EventProcessorHost` for enkel, men den kan ikke den bedste løsning til dette bestemt scenarie. `EventProcessorHost`gør det hårde arbejde, for at sikre, at du ikke behøver at bekymre dig om fører en problemer inden for en bestemt hændelse processor klasse. Dog i vores scenarie skal vi blot konvertere meddelelsen til et andet format og overføre det langs til en anden tjeneste ved hjælp af en asynkron metode. Det er ikke nødvendigt for at opdatere delte stat og derfor ikke er risiko for fører en problemer. For de fleste scenarier `EventProcessorHost` sandsynligvis det bedste valg og det er selvfølgelig indstillingen nemmere.     

### <a name="ieventprocessor"></a>IEventProcessor
Central som når du bruger `EventProcessorHost` er at oprette en en implementering af den `IEventProcessor` grænseflade, som indeholder metoden, `ProcessEventAsync`. Essensen af denne metode er vist her:

  asynkron opgave IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages) {}

           foreach (EventData eventData in messages)
           {
               _Logger.LogInfo(string.Format("Event received from partition: {0} - {1}", context.Lease.PartitionId,eventData.PartitionKey));

               try
               {
                   var httpMessage = HttpMessage.Parse(eventData.GetBodyStream());
                   await _MessageContentProcessor.ProcessHttpMessage(httpMessage);
               }
               catch (Exception ex)
               {
                   _Logger.LogError(ex.Message);
               }
           }
            ... checkpointing code snipped ...
        }

En liste over EventData objekter der overføres til metoden, og vi forbedrer over listen. Byte for hver enkelt metode parses til et HttpMessage objekt og objektet der overføres til en forekomst af IHttpMessageProcessor.

### <a name="httpmessage"></a>HttpMessage
Den `HttpMessage` forekomst indeholder tre filer:

      public class HttpMessage
       {
           public Guid MessageId { get; set; }
           public bool IsRequest { get; set; }
           public HttpRequestMessage HttpRequestMessage { get; set; }
           public HttpResponseMessage HttpResponseMessage { get; set; }

        ... parsing code snipped ...

      }

Den `HttpMessage` forekomst indeholder en `MessageId` GUID, der gør det muligt for os til at oprette forbindelse HTTP-anmodningen til den tilsvarende HTTP-svar og en boolesk værdi, der identificerer, hvis objektet, der indeholder en forekomst af en HttpRequestMessage og HttpResponseMessage. Ved hjælp af den indbyggede i HTTP klasser fra `System.Net.Http`, jeg kunne drage fordel af de `application/http` parsing af kode, der er inkluderet i `System.Net.Http.Formatting`.  

### <a name="ihttpmessageprocessor"></a>IHttpMessageProcessor
Den `HttpMessage` forekomst derefter videresendt til implementering af `IHttpMessageProcessor` som er en grænseflade, jeg har oprettet for at decouple modtagelse og fortolkning af begivenheden fra Azure begivenhed Hub og faktisk behandling af den.


## <a name="forwarding-the-http-message"></a>Videresende meddelelsen HTTP
Dette eksempel besluttet jeg det ville være interessant til at overføre HTTP-anmodningen til [Runscope](http://www.runscope.com). Runscope er en skybaseret tjeneste, der er specialiseret i HTTP fejlfinding, logføring og overvågning. De har et gratis trin, så det er nemt at prøve og den giver os mulighed at se HTTP-anmodninger i realtid cirkulation vores API Management-tjenesten.

Den `IHttpMessageProcessor` implementering ser sådan ud

      public class RunscopeHttpMessageProcessor : IHttpMessageProcessor
       {
           private HttpClient _HttpClient;
           private ILogger _Logger;
           private string _BucketKey;
           public RunscopeHttpMessageProcessor(HttpClient httpClient, ILogger logger)
           {
               _HttpClient = httpClient;
               var key = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-KEY", EnvironmentVariableTarget.User);
               _HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("bearer", key);
               _HttpClient.BaseAddress = new Uri("https://api.runscope.com");
               _BucketKey = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-BUCKET", EnvironmentVariableTarget.User);
               _Logger = logger;
           }

           public async Task ProcessHttpMessage(HttpMessage message)
           {
               var runscopeMessage = new RunscopeMessage()
               {
                   UniqueIdentifier = message.MessageId
               };

               if (message.IsRequest)
               {
                   _Logger.LogInfo("Sending HTTP request " + message.MessageId.ToString());
                   runscopeMessage.Request = await RunscopeRequest.CreateFromAsync(message.HttpRequestMessage);
               }
               else
               {
                   _Logger.LogInfo("Sending HTTP response " + message.MessageId.ToString());
                   runscopeMessage.Response = await RunscopeResponse.CreateFromAsync(message.HttpResponseMessage);
               }

               var messagesLink = new MessagesLink() { Method = HttpMethod.Post };
               messagesLink.BucketKey = _BucketKey;
               messagesLink.RunscopeMessage = runscopeMessage;
               var runscopeResponse = await _HttpClient.SendAsync(messagesLink.CreateRequest());
               _Logger.LogDebug("Request sent to Runscope");
           }
       }

Jeg kunne drage fordel af en [eksisterende klientbibliotek til Runscope](http://www.nuget.org/packages/Runscope.net.hapikit/0.9.0-alpha) , der gør det nemt at opslagsnål `HttpRequestMessage` og `HttpResponseMessage` forekomster af deres ibrugtagning. Du skal bruge en konto og en API-nøgle for at få adgang til API Runscope. Instruktioner til at få en API-nøgle kan findes i [Oprette programmer til Access Runscope API](http://blog.runscope.com/posts/creating-applications-to-access-the-runscope-api) screencast.

## <a name="complete-sample"></a>Hele eksemplet
[Kildekode](https://github.com/darrelmiller/ApimEventProcessor) og tester for prøven er på Github. Du skal bruge en [API Management-tjenesten](api-management-get-started.md), [En forbundne begivenhed Hub](api-management-howto-log-event-hubs.md)og en [Lagerplads konto](../storage/storage-create-storage-account.md) til at køre stikprøvernes til dig selv.   

Prøven er bare et enkelt Console-program, der lytter for hændelser, der kommer fra begivenhed Hub konverterer dem til en `HttpRequestMessage` og `HttpResponseMessage` objekter og sender dem til API'EN Runscope.

I følgende animeret billede, kan du se en anmodning om henvisninger til en API i portalen udvikler, programmet Console, der viser den meddelelse, der har modtaget, behandles og videresendt og derefter anmodningen og svaret dukke op i Runscope trafik inspektion.

![Demonstration af anmodning bliver videresendt til Runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Oversigt
Azure API Management-tjenesten giver ideelt at registrere den HTTP-trafik til og fra din API'er. Azure begivenhed Hubs er en meget SVG, lave omkostninger løsning for registrering af pågældende trafik og fødning af det i sekundær behandlingssystemer til logføring, overvågning, og andre avancerede analyser. Oprette forbindelse til 3 part trafik overvågning systemer som Runscope er en enkel som et par dozen kodelinjer.

## <a name="next-steps"></a>Næste trin
-   Lær mere om Azure begivenhed Hubs
    -   [Introduktion til Azure begivenhed Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
    -   [Modtage meddelelser med EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost)
    -   [Begivenhed Hubs programming vejledning](../event-hubs/event-hubs-programming-guide.md)
-   Få flere oplysninger om administration af API og begivenhed Hubs integration
    -   [Sådan logger du begivenheder til Azure begivenhed hubber i Azure API Management](api-management-howto-log-event-hubs.md)
    -   [Logføring enhedsreference](https://msdn.microsoft.com/library/azure/mt592020.aspx)
    -   [Log-eventhub politikreference](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)
    