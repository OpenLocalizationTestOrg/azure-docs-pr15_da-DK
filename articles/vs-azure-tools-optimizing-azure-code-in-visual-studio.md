<properties
   pageTitle="Optimere din Azure kode i Visual Studio | Microsoft Azure"
   description="Få mere at vide om, hvordan Azure kode optimering værktøjer i Visual Studio at forbedre din kode, mere robust og – ydeevne."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="optimizing-your-azure-code"></a>Optimere din Azure-kode

Når du programmering apps, der bruger Microsoft Azure, er der nogle metoder skal du følge for at undgå problemer med app skalerbarhed, funktionsmåde og ydeevne i et skybaseret miljø. Microsoft yder et Azure kode-analyseværktøj, der genkender og identificerer flere af disse ofte stødt på problemer og hjælper dig med at løse dem. Du kan hente værktøjet i Visual Studio via NuGet.

## <a name="azure-code-analysis-rules"></a>Azure kode analyse regler

Analyseværktøjet Azure kode bruger følgende regler til at markere din Azure kode automatisk, når der er angivet kendte påvirker problemer. Registreret problemer vises som en advarsler eller kompilerfejl. Kode løsninger eller forslag til at løse advarsel eller fejlmeddelelse, der ofte leveres via et ikon for pære.

## <a name="avoid-using-default-in-process-session-state-mode"></a>Undgå at bruge tilstand standardtilstanden (i-proces) session

### <a name="id"></a>ID

AP0000

### <a name="description"></a>Beskrivelse

Hvis du bruger standardtilstanden (i-proces) session tilstand til skyen programmer, kan du miste sessionstilstand.

Skal du dele ideer og feedback på [Azure kode analyse feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Årsag

Som standard er den session tilstand tilstand, der er angivet i filen web.config i processen. Også, hvis feltet er tomt angivet i konfigurationsfilen, standarder tilstanden Session tilstand for at i processen. Tilstanden i processen gemmer sessionstilstand i hukommelsen på webserveren. Når en forekomst genstartes eller en ny forekomst bruges til justering af belastning eller understøttelse af failover, sessionstilstanden, der er gemt i hukommelsen på webserveren, gemmes ikke. Denne situation forhindrer, at programmet i at blive SVG i skyen.

ASP.NET sessionstilstand understøtter flere forskellige lagerplads indstillinger til session tilstand data: InProc, StateServer, SQL Server, brugerdefineret, og fra. Det anbefales, at du bruger brugerdefineret tilstand til host data på en ekstern Session tilstand store, såsom [Azure Session tilstand-provider til Redis](http://go.microsoft.com/fwlink/?LinkId=401521).

### <a name="solution"></a>Løsning

En anbefalet løsning er at gemme sessionstilstand på en administreret cachetjeneste. Lær at bruge [Azure Session tilstand-provider til Redis](http://go.microsoft.com/fwlink/?LinkId=401521) til at gemme din sessionstilstand. Du kan også gemme sessionstilstand i andre steder at sikre, at dit program er SVG i skyen. Hvis du vil vide mere om alternativ Læs løsninger [Session tilstand funktionsmåder](https://msdn.microsoft.com/library/ms178586).

## <a name="run-method-should-not-be-async"></a>Kør metode må ikke være asynkron

### <a name="id"></a>ID

AP1000

### <a name="description"></a>Beskrivelse

Oprette asynkrone metoder (såsom [venter på at blive](https://msdn.microsoft.com/library/hh156528.aspx)) uden for metoden, der [kører](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) , og kald derefter metoderne asynkron fra [kører](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx). Erklære metoden [[kører](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) som asynkron får rollen arbejder for at angive en genstart løkke.

Skal du dele ideer og feedback på [Azure kode analyse feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Årsag

Ringe til asynkron metoder i metoden [kører](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) bevirker, at skyen service runtime til Papirkurv rollen arbejder. Når en kollega rolle starter, foregår kørsel af alle programmer i metoden, der [kører](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) . Afslutte metoden [kører](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) får rollen arbejder for at genstarte. Når arbejder rolle runtime rammer den asynkron metode, afsender alle handlinger efter den asynkron metode og returnerer derefter. Derved rollen arbejder afslutte fra metoden [[[[kører](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) og genstarte. I den næste gentagelse af udførelse af rollen arbejder rammer den asynkron metode igen og genstarter, forårsager rollen arbejder til Papirkurv samt igen.

### <a name="solution"></a>Løsning

Placer alle asynkrone handlinger uden for metoden, der [kører](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) . Derefter kalde metoden refactored asynkron fra i metoden [[kører](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) som RunAsync () .wait. Analyseværktøjet Azure kode kan hjælpe dig med at løse dette problem.

Følgende kodestykke viser kode fix til dette problem:

```
public override void Run()
{
    RunAsync().Wait();
}

public async Task RunAsync()
{
    //Asynchronous operations code logic
    // This is a sample worker implementation. Replace with your logic.

    Trace.TraceInformation("WorkerRole1 entry point called");

    HttpClient client = new HttpClient();

    Task<string> urlString = client.GetStringAsync("http://msdn.microsoft.com");

    while (true)
    {
        Thread.Sleep(10000);
        Trace.TraceInformation("Working");

        string stream = await urlString;
    }

}
```

## <a name="use-service-bus-shared-access-signature-authentication"></a>Brug Service Bus delt Access signatur godkendelse

### <a name="id"></a>ID

AP2000

### <a name="description"></a>Beskrivelse

Bruge delte Access signatur (SAS) til godkendelse. Access ACS (Control Service) frarådes til tjenesten bus godkendelse.

Skal du dele ideer og feedback på [Azure kode analyse feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Årsag

For at øge sikkerheden erstatter Azure Active Directory ACS godkendelse med SAS godkendelse. Se [Azure Active Directory er fremtiden for ACS](http://blogs.technet.com/b/ad/archive/2013/06/22/azure-active-directory-is-the-future-of-acs.aspx) for oplysninger om overgang planen.

### <a name="solution"></a>Løsning

Bruge SAS godkendelse i dine apps. I følgende eksempel viser, hvordan du bruger et eksisterende SAS token til at få adgang til en tjeneste bus navneområde eller enhed.

```
MessagingFactory listenMF = MessagingFactory.Create(endpoints, new StaticSASTokenProvider(subscriptionToken));
SubscriptionClient sc = listenMF.CreateSubscriptionClient(topicPath, subscriptionName);
BrokeredMessage receivedMessage = sc.Receive();
```

Se følgende artikler for at få mere at vide.

- Se en oversigt, under [Delt Access signatur godkendelse med Service Bus](https://msdn.microsoft.com/library/dn170477.aspx)

- [Sådan bruges delt Access signatur godkendelse med Service Bus](https://msdn.microsoft.com/library/dn205161.aspx)

- Et eksempelprojekt, finder du under [Brug af delte Access signatur (SAS) godkendelse med tjenesten Bus-abonnementer](http://code.msdn.microsoft.com/windowsazure/Using-Shared-Access-e605b37c)

## <a name="consider-using-onmessage-method-to-avoid-receive-loop"></a>Overvej at bruge OnMessage metode til at undgå "modtage løkke"

### <a name="id"></a>ID

AP2002

### <a name="description"></a>Beskrivelse

For at undgå flyttes til en "modtage løkke", når metoden **OnMessage** er en bedre løsning for at modtage meddelelser end kalder metoden **Modtag** . Men hvis du skal bruge metoden **Modtag** , og du angiver en ikke-standard-server ventetid, Sørg for ventetiden server er mere end et minut.

Skal du dele ideer og feedback på [Azure kode analyse feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Årsag

Når du ringer til **OnMessage**, starter klienten en intern meddelelse pumpe, som konstant undersøger den kø eller et andet abonnement. Denne meddelelse pumpe indeholder en uendelig løkke, der udsteder et opkald til at modtage meddelelser. Hvis opkaldet timeout, udsteder et nyt opkald. Interval for timeout bestemmes af værdien af egenskaben [OperationTimeout](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx) for [MessagingFactory](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactory.aspx), der bruges.

Fordelen ved at bruge **OnMessage** sammenlignet med **Modtag** er, at brugere ikke behøver at manuelt afstemning for meddelelser, håndtere undtagelser, behandle flere meddelelser parallelt og fuldføre meddelelser.

Hvis du kalder **Modtag** uden at bruge standardværdien, skal du sørge for værdien *ServerWaitTime* er mere end et minut. Indstille *ServerWaitTime* til forhindrer mere end et minut serveren timeout, før meddelelsen modtages fuldt ud.

### <a name="solution"></a>Løsning

Se eksemplerne nedenfor kode for anbefalede anvendelser. Yderligere oplysninger finder du [QueueClient.OnMessage metode (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.onmessage.aspx)og [QueueClient.Receive (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.receive.aspx).

For at forbedre ydeevnen for Azure messaging-infrastrukturen, se, hvordan designmønsteret [Asynkron Messaging grundlæggende](https://msdn.microsoft.com/library/dn589781.aspx).

Følgende er et eksempel på brug af **OnMessage** til at modtage meddelelser.

```
void ReceiveMessages()
{
    // Initialize message pump options.
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = true; // Indicates if the message-pump should call complete on messages after the callback has completed processing.
    options.MaxConcurrentCalls = 1; // Indicates the maximum number of concurrent calls to the callback the pump should initiate.
    options.ExceptionReceived += LogErrors; // Enables you to get notified of any errors encountered by the message pump.

    // Start receiving messages.
    QueueClient client = QueueClient.Create("myQueue");
    client.OnMessage((receivedMessage) => // Initiates the message pump and callback is invoked for each message that is recieved, calling close on the client will stop the pump.
    {
        // Process the message.
    }, options);
    Console.WriteLine("Press any key to exit.");
    Console.ReadKey();
```

Følgende er et eksempel på brug af **Modtag** med standard server ventetiden.

```
string connectionString =  
CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

QueueClient Client =  
    QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

while (true)  
{   
   BrokeredMessage message = Client.Receive();
   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
```

Følgende er et eksempel på brug af **Modtag** med en ikke-standard server ventetid.

```
while (true)  
{   
   BrokeredMessage message = Client.Receive(new TimeSpan(0,1,0));

   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
}
```
## <a name="consider-using-asynchronous-service-bus-methods"></a>Overvej at bruge asynkron Service Bus metoder

### <a name="id"></a>ID

AP2003

### <a name="description"></a>Beskrivelse

Brug asynkron Service Bus metoder til at forbedre ydeevnen med formidlet messaging.

Skal du dele ideer og feedback på [Azure kode analyse feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Årsag

Brug af asynkrone metoder giver programmet program på dokumentsammenfald, fordi udførelse af hvert opkald ikke blokere den primære tråd. Når du bruger Service Bus messaging metoder, udfører en handling (sende, modtage, slette, osv.) tager tid. Dette tidsrum omfatter behandling af handlingen af tjenesten Service Bus ud over forsinkelse på anmodningen og svaret. Handlinger skal udføre samtidigt for at øge antallet af handlinger i gang. Se [Bedste fremgangsmåder til ydeevne forbedringer ved hjælp af tjenesten Bus formidlet Messaging](https://msdn.microsoft.com/library/azure/hh528527.aspx)kan finde flere oplysninger.

### <a name="solution"></a>Løsning

Du kan finde oplysninger om, hvordan du bruger den anbefalede asynkrone metode i [QueueClient klasse (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.aspx) .

For at forbedre ydeevnen for Azure messaging-infrastrukturen, se, hvordan designmønsteret [Asynkron Messaging grundlæggende](https://msdn.microsoft.com/library/dn589781.aspx).

## <a name="consider-partitioning-service-bus-queues-and-topics"></a>Overvej leverandør Service Bus køer og emner

### <a name="id"></a>ID

AP2004

### <a name="description"></a>Beskrivelse

Partition Service Bus køer og emner for at forbedre ydeevnen med Service Bus messaging.

Skal du dele ideer og feedback på [Azure kode analyse feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Årsag

Tilgængelighed af ydeevnen overførselshastighed og service øges partitionering Service Bus køer og emner, fordi den overordnede overførsel af en partitioneret kø eller emne, er ikke længere begrænset af ydeevnen for en enkelt meddelelse broker eller SMS butik. Desuden gøre en midlertidig afbrydelse af en SMS store ikke en partitioneret kø eller emne utilgængelig. Du kan finde yderligere oplysninger finder [Partitionering Messaging enheder](https://msdn.microsoft.com/library/azure/dn520246.aspx).

### <a name="solution"></a>Løsning

Følgende kodestykke viser, hvordan du partition SMS enheder.

```
// Create partitioned topic.
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Få mere at vide under [opdelt Service Bus køer og emner | Microsoft Azure Blog](https://azure.microsoft.com/blog/2013/10/29/partitioned-service-bus-queues-and-topics/) og se eksemplet [Microsoft Azure Service Bus opdelt kø](https://code.msdn.microsoft.com/windowsazure/Service-Bus-Partitioned-7dfd3f1f) .

## <a name="do-not-set-sharedaccessstarttime"></a>Angiv ikke SharedAccessStartTime

### <a name="id"></a>ID

AP3001

### <a name="description"></a>Beskrivelse

Du skal undgå at bruge SharedAccessStartTimeset til det aktuelle klokkeslæt til at starte umiddelbart delt Access-politik. Du skal kun angive denne egenskab, hvis du vil starte den delte Access-politik på et senere tidspunkt.

Skal du dele ideer og feedback på [Azure kode analyse feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Årsag

Synkronisering af uret medfører et mindre tidsforskellen mellem datacentre. For eksempel du ville logisk mener, angive starttidspunktet for en lagerplads SAS politik som det aktuelle klokkeslæt ved hjælp af DateTime.Now eller en lignende metode medfører politikken SAS kan træde i kraft med det samme. Dog kan mindre tid forskellene mellem datacentre medføre problemer med dette, da nogle gange datacenter kan være lidt senere end starttidspunkt, mens andre forud for den. Som et resultat politikken SAS kan udløbe hurtigt (eller endda straks) Hvis levetiden politik er indstillet for kort.

Se [Introduktion til tabel SAS (delt Access underskrift), kø SAS og opdatering til Blob SAS - Microsoft Azure lagerplads teamets Blog - websted hjem - MSDN Blogs](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)have mere at vide om brug af delte Access signatur på Azure-lager.

### <a name="solution"></a>Løsning

Fjern den sætning, der angiver starttidspunktet for den delte access-politik. Analyseværktøjet Azure kode giver en løsning på dette problem. Du kan finde flere oplysninger om sikkerhedsadministration af, design mønster [Valet nøgle mønster](https://msdn.microsoft.com/library/dn568102.aspx).

Følgende kodestykke demonstrerer kode fix til dette problem.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

## <a name="shared-access-policy-expiry-time-must-be-more-than-five-minutes"></a>Delt Access-politik udløbstid skal være mere end fem minutter

### <a name="id"></a>ID

AP3002

### <a name="description"></a>Beskrivelse

Der kan være så meget som et fem minut forskel i klokkeslæt mellem datacentre på forskellige steder på grund af en betingelse, kaldet "ur skævhed." Hvis du vil forhindre Sikkerhedstilknytningerne angive politik token fra udløber tidligere end planlagt, udløbet tid til at være mere end fem minutter.

Skal du dele ideer og feedback på [Azure kode analyse feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Årsag

Datacentre på forskellige steder over hele verden synkronisere ved et ur signal. Fordi det tager tid på ur signal til forskellige steder, kan der være en tid afvigelse mellem datacentre på forskellige geografiske steder, selvom alt angiveligt ikke er synkroniseret. Denne tidsforskel kan påvirke det delte Access politik start tid og udløb interval. Derfor, for at sikre delt Access-politik træder i kraft med det samme, ikke angive starttidspunktet. Desuden Sørg for, at udløbsdatoen er mere end fem minutter til at forhindre tidlig timeout.

Du kan finde flere oplysninger om brug af delte Access signatur på Azure-lager, [Introduktion til tabel SAS (delt Access underskrift), kø SAS og opdatering til Blob SAS - Microsoft Azure lagerplads teamets Blog - websted hjem - MSDN Blogs](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### <a name="solution"></a>Løsning

Du kan finde flere oplysninger om sikkerhedsadministration af, design mønster [Valet nøgle mønster](https://msdn.microsoft.com/library/dn568102.aspx).

Følgende er et eksempel på ikke angive et starttidspunkt delt Access politik.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

Følgende er et eksempel på, angive et delt Access politik starttidspunkt med en politik udløb-varighed, der er større end fem minutter.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
  SharedAccessStartTime = new DateTime(2014,1,20),   
 SharedAccessExpiryTime = new DateTime(2014, 1, 21),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

Se [oprette og bruge en delt Access signatur](https://msdn.microsoft.com/library/azure/jj721951.aspx)kan finde flere oplysninger.

## <a name="use-cloudconfigurationmanager"></a>Brug CloudConfigurationManager

### <a name="id"></a>ID

AP4000

### <a name="description"></a>Beskrivelse

Ved hjælp af klassen [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager(v=vs.110).aspx) for projekter som Azure websted og Azure mobile tjenester introducere ikke runtime problemer. Som en bedste fremgangsmåde, men er det en god ide at bruge skyen[ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager(v=vs.110).aspx) som en samlet metode til at administrere konfigurationer for alle Azure Cloud-programmer.

Skal du dele ideer og feedback på [Azure kode analyse feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Årsag

CloudConfigurationManager læser den relevante program-miljø konfigurationsfil.

[CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx)

### <a name="solution"></a>Løsning

Refactor koden for at bruge [CloudConfigurationManager klasse](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx). En kode løsning til dette problem er angivet af værktøjet Azure kode analyse.

Følgende kodestykke demonstrerer kode fix til dette problem. Erstat

`var settings = ConfigurationManager.AppSettings["mySettings"];`

med

`var settings = CloudConfigurationManager.GetSetting("mySettings");`

Her er et eksempel på, hvordan du kan gemme konfigurationsindstillingen i en App.config eller Web.config-fil. Føje indstillingerne til sektionen appSettings i konfigurationsfilen. Følgende er filen Web.config for det forrige kodeeksempel.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="mySettings" value="[put_your_setting_here]"/>
  </appSettings>  
```

## <a name="avoid-using-hard-coded-connection-strings"></a>Undgå brug af faste forbindelsesstrenge

### <a name="id"></a>ID

AP4001

### <a name="description"></a>Beskrivelse

Hvis du bruger faste forbindelsesstrenge, og du har brug at opdatere dem senere, skal du skal foretage ændringer i din kildekode og genkompilere programmet. Men hvis du gemmer din forbindelsesstrenge i en konfigurationsfil, du kan ændre dem senere ved blot at opdatere konfigurationsfilen.

Skal du dele ideer og feedback på [Azure kode analyse feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Årsag

Hårde kodningssprog strenge er en ugyldig øvelse, fordi den introducerer problemer, når forbindelsesstrenge skal ændres hurtigt. Desuden, hvis projektet skal tjekkes ind til versionsstyring, introducere faste forbindelsesstrenge sikkerhedsrisici da strengene kan ses i koden.

### <a name="solution"></a>Løsning

Gemmer strenge i den åbne fil eller Azure miljøer.

- Bruge app.config for enkeltstående programmer til at gemme streng forbindelsesindstillinger.

- Brug web.config til at lagre forbindelsesstrenge for IIS-hostede webprogrammer.

- Brug configuration.json til at lagre forbindelsesstrenge til ASP.NET vNext programmer.

Du kan finde oplysninger om brug af konfigurationer filer som web.config eller app.config [Retningslinjer for ASP.NET Web konfiguration](https://msdn.microsoft.com/library/vstudio/ff400235(v=vs.100).aspx). Finde oplysninger om, hvordan Azure miljø variabler arbejde, [Azure websteder: hvordan programmet strenge og forbindelsen strenge fungerer](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/). Finde oplysninger om at gemme forbindelsesstrengen i versionsstyring, se [undgå følsomme oplysninger som strenge i filer, der er gemt i kilde-kode lager](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control).

## <a name="use-diagnostics-configuration-file"></a>Bruge diagnosticering konfigurationsfil

### <a name="id"></a>ID

AP5000

### <a name="description"></a>Beskrivelse

I stedet for konfiguration af diagnosticering indstillinger i din kode som ved hjælp af Microsoft.WindowsAzure.Diagnostics programming API, skal du konfigurere indstillinger for diagnosticering i filen diagnostics.wadcfg. (Eller diagnostics.wadcfgx, hvis du bruger Azure SDK 2,5). Du kan ændre indstillinger for diagnosticering ved at gøre dette, uden at skulle genkompilere din kode.

Skal du dele ideer og feedback på [Azure kode analyse feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Årsag

Før Azure SDK 2,5 (som bruger Azure diagnosticering 1.3), Azure diagnosticering (WAD) kan konfigureres ved hjælp af flere forskellige metoder: føje den til konfiguration af blob i lagring, ved hjælp af vigtigt kode, deklarative konfiguration eller standardkonfigurationen. Den foretrukne metode til at konfigurere diagnosticering er dog at bruge en XML-konfigurationsfil (diagnostics.wadcfg eller diagnositcs.wadcfgx SDK 2,5 og senere) i programmet projektet. I denne metode kan filen diagnostics.wadcfg helt definerer konfigurationen og opdateres og genaktiveres efter behov. Blanding af brugen af konfigurationsfil diagnostics.wadcfg med programmering af indstillingen konfigurationer ved hjælp af [DiagnosticMonitor](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.diagnosticmonitor.aspx)eller [RoleInstanceDiagnosticManager](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.management.roleinstancediagnosticmanager.aspx)klasser kan føre til forvirring. Du kan se [initialisering eller ændre Azure diagnosticering konfiguration](https://msdn.microsoft.com/library/azure/hh411537.aspx) kan finde flere oplysninger.

Begynder med WAD 1.3 (følger med Azure SDK 2,5), der ikke længere muligt at bruge kode til at konfigurere diagnosticering. Som et resultat, kan du kun give dig konfigurationen når anvende eller opdatere filtypenavnet diagnosticering.

### <a name="solution"></a>Løsning

Bruge diagnosticering konfiguration designer til at flytte diagnosticering indstillinger til konfigurationsfil diagnosticering (diagnositcs.wadcfg eller diagnositcs.wadcfgx for SDK 2,5 og nyere). Det anbefales også, at du installerer [Azure SDK 2,5](http://go.microsoft.com/fwlink/?LinkId=513188) og bruge funktionen seneste diagnosticering.

1. Vælg egenskaber i genvejsmenuen for den rolle, du vil konfigurere, og vælg derefter fanen konfiguration.

1. Sørg for, at afkrydsningsfeltet **Aktivér diagnosticering** er markeret i afsnittet **diagnosticering** .

1. Vælg knappen **Konfigurer** .

  ![Få adgang til indstillingen Aktivér diagnosticering](./media/vs-azure-tools-optimizing-azure-code-in-visual-studio/IC796660.png)

  Du kan få flere oplysninger i [Konfigurere diagnosticering til Azure Cloud Services og virtuelle computere](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) .


## <a name="avoid-declaring-dbcontext-objects-as-static"></a>Undgå at erklære DbContext objekter som statisk

### <a name="id"></a>ID

AP6000

### <a name="description"></a>Beskrivelse

For at gemme hukommelse skal du undgå erklære DBContext objekter som statisk.

Skal du dele ideer og feedback på [Azure kode analyse feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Årsag

DBContext objekter hold forespørgselsresultaterne fra hver opkald. Statiske DBContext objekter er ikke fjernet, indtil programmet domænet er fjernet. Et statisk DBContext objekt kan derfor forbruge store mængder hukommelse.

### <a name="solution"></a>Løsning

Erklære DBContext som en lokal variabel eller et ikke-statisk forekomstfelt, bruge den til en opgave, og lad den være kasseret efter anvendelse.

I følgende eksempel MVC controller klasse viser, hvordan du bruger objektet DBContext.

```
public class BlogsController : Controller
    {
        //BloggingContext is a subclass to DbContext        
        private BloggingContext db = new BloggingContext();
        // GET: Blogs
        public ActionResult Index()
        {
            //business logics…
            return View();
        }
        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                db.Dispose();
            }
            base.Dispose(disposing);
        }
    }
```

## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om optimzing og fejlfinding Azure apps skal du se [fejlfinding i forbindelse med en WebApp i Azure App Service ved hjælp af Visual Studio](./app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).
