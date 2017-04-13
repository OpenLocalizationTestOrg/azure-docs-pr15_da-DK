<properties
   pageTitle="Få mere at vide om og oprette en BizTalk-regler API-app i din logik App | Microsoft Azure"
   description="Dette emne beskrives funktionerne i BizTalk regler forbindelse og med instruktioner til brugen"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="04/20/2016"
   ms.author="andalmia"/>

#<a name="biztalk-rules"></a>BizTalk-regler

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Forretningsregler indkapsler politikker og beslutninger, der bestemmer forretningsprocesser. Disse politikker formelt kan defineres i procedure brugervejledninger, kontrakter eller aftaler eller findes muligvis som viden eller erfaring, som er indeholdt i medarbejdere. Disse politikker er dynamiske og kan ændres over tid på grund af ændringer i forretningsplaner love eller andre årsager.

Implementering af disse politikker i traditionelt programmeringssprog kræver masser af tid og køre koordineret og giver ikke mulighed for ikke-programmører til at deltage i oprettelse og vedligeholdelse af virksomhedens politikker. BizTalk forretningsregler gør det muligt hurtigt at implementere disse politikker og decouple resten af forretningsprocessen. Dette giver mulighed for at foretage nødvendige ændringer af virksomhedens politikker uden at påvirke resten af forretningsprocessen.

##<a name="why-rules"></a>Hvorfor regler

Der er 3 vigtige grunde til at bruge BizTalk forretningsregler i forretningsproces:

* Decouple forretningslogik fra programkode
- Tillad virksomhedsanalytikere have mere kontrol over logik virksomhedsledelse
+ Ændringer til forretningslogik gå til fremstilling hurtigere

##<a name="rules-concepts"></a>Regler begreber

##<a name="vocabulary"></a>Ordliste

_Ordliste_ er en samling af definitioner bestående af fulde navne for de databehandling objekter, der bruges i reglens betingelser og handlinger. Ordliste definitioner gør det nemmere at læse, forstå og dele af personer i et bestemt forretningsområde reglerne.

Vocabularies er designet til at udjævne forskellene mellem business semantik og implementering. For eksempel kan en databinding for en godkendelsesstatus pege på en bestemt kolonne i en bestemt række i en bestemt database, der repræsenteres som en SQL-forespørgsel. I stedet for indsættelse af denne type komplekse repræsentation i en regel, kan du i stedet oprette en ordliste definition, der er knyttet til data bindingen med et fuldt navn "Status". Derefter kan du medtage "Status" i en hvilken som helst antal regler, og regel program kan hente de tilsvarende data fra tabellen.

##<a name="rule"></a>Regel

Forretningsregler er deklarativ sætninger, som styrer gennemførelse af forretningsprocesser. En regel består af en betingelse og handlinger. Betingelsen evalueres, og hvis det evalueres til sand, regel programmet starter en eller flere handlinger.
Regler i den Business regler, som er defineret ved hjælp af følgende format:

_IF_ _betingelse_ _Derefter_ _handling_

Overvej følgende eksempel:

*Hvis beløb er mindre end eller lig med tilgængelige penge*  
*DEREFTER forestå posteringen og udskrive kvittering*

Denne regel bestemmer, om en transaktion udføres ved at anvende forretningslogik i form af en sammenligning af to pengeværdier, i form af en transaktionsbeløb og penge, der er tilgængelige.
Du kan bruge den forretningsregel til at oprette, redigere og implementere forretningsregler. Du kan også udføre de foregående opgaver fra et program.

###<a name="conditions"></a>Betingelser

En betingelse er sand/falske (booleske) udtryk, der består af en eller flere prædikaterne.
I vores eksempel anvendes prædikat mindre end eller lig med beløb og penge, der er tilgængelige. Denne betingelse evaluerer altid til true eller false.
Prædikaterne kan kombineres med logiske operatorer AND, OR og ikke danne sig et logisk udtryk, der potentielt meget store, men vil altid evalueres til true eller false.

###<a name="actions"></a>Handlinger

Handlinger er funktionelle konsekvenserne af evaluering af betingelse. Hvis en regel betingelser er opfyldt, startes en tilhørende handling eller handlinger.
I vores eksempel er "lede posteringen" og "udskrive kvittering" handlinger, der udføres, når, og kun når, betingelsen (i dette tilfælde "Hvis pris er mindre end eller lig med tilgængelige penge") er sand.
Handlinger er repræsenteret i Business regler Framework ved at udføre sæt handlinger på XML-dokumenter.

##<a name="policy"></a>Politik

En politik er en logisk gruppering af regler. Du skriver en politik, gemme den, tester den og, når du er tilfreds med resultatet, kan du bruge det i et produktionsmiljø.

###<a name="policy-composition"></a>Politik sammensætning

Du kan skrive politikker på portalen regler. En politik kan indeholde et tilfældigt stort regelsæt, men typisk du skriver en politik fra regler, der vedrører et bestemt forretningsområde i forbindelse med det program, der anvender politikken.

###<a name="policy-testing"></a>Test af politik
Du kan udføre en testkørsel af din politik effektivt, før den kan bruges i et produktionsmiljø. Portalen regler gør det muligt at angive input til en politik, kører politikken og få vist resultatet. Output indeholder logfiler, udførelse af regel, evaluering af betingelse, og resulterende output.

##<a name="sample-scenario---insurance-claims"></a>Eksempel på et Scenario - forsikringskrav
Lad os tage et eksempel på et scenario, og gennemgå det, når vi skriver forretningsfunktionerne til den samme.

![Alternativ tekst][1]

I et scenarie med really simple forsikringskrav sender ansøgeren sin forsikringskrav (ved hjælp af enhver klient som websted, phone App osv.). Dette krav, anmode om bliver sendt til business Kræv behandling af enhed og baseret på resultatet af behandlingen, kravet kan være enten godkendt, afvist eller sendes langs til yderligere manuel behandling.
Kræv behandling af enhed i vores scenario der ville være et omfatter forretningslogik for systemet. Tage et nærmere Kig på denne enhed, kan vi se følgende:

![Alternativ tekst][2]

Lad os nu bruge forretningsregler til at implementere denne forretningslogik.


##<a name="creation-of-rules-api-app"></a>Oprettelse af regler Api App


1. Log på portalen Azure
2. Vælg Ny -> Marketplace og derefter søge efter *BizTalk-regler*
3. Vælg BizTalk regler fra på resultatlisten. Bladet BizTalk regler åbnes
4. Vælg knappen *Opret* ![alternativ tekst][3]
1. Angiv følgende oplysninger i den nye blade, der åbnes:  
    1. Navn – Giv et navn til din regler API-App
    1. App-serviceaftale – Vælg eller Opret en ny App Service planlægge
    1. Priser niveau – Vælg det ønskede denne App til er placeret priser niveau
    1. Ressourcegruppe – Vælg eller Opret ressourcegruppe hvor APP'en skal være medlem af
    2. Abonnement - Vælg det abonnement, du vil bruge
    1. Placering – Vælg den geografiske placering, hvor du vil have Appen skal installeres.
4.  Vælg *Opret*. Din BizTalk regler API-App skal oprettes i et par minutter.

##<a name="vocabulary-creation"></a>Oprettelse af ordliste
Når du har oprettet en BizTalk-regler API-App, er næste trin at oprette vocabularies. Det forventes, at udvikleren, er mere almindelig person til at udføre denne opgave. Her er, hvordan du kan få det gjort:


1. Start din BizTalk regler API App fra portalen ved at gå til Gennemse -> API Apps - ><Your Rules API App>. Det kommer du til regler API App Dashboard svarende til nedenfor:

   ![Alternativ tekst][4]

2. Vælg "Ordliste definitioner". Her skal du se ordliste redigering skærmen 3. Vælg "Tilføj" til at begynde at tilføje nye ordliste definitioner.
Der er 2 typer ordliste definitioner-understøttes i øjeblikket – konstanter og XML.

##<a name="literal-definition"></a>Ordret Definition
1.  Når du klikker på "Tilføj", åbnes en ny "Tilføj Definition" Blade. Angiv følgende værdier
  1.    Navn – kun alfanumeriske tegn der forventes uden nogen specialtegn. Dette bør også være entydige for din eksisterende ordliste definitionsliste.
  2.    Beskrivelse – valgfrit felt.
  3.    Definition af typen – der er 2 datatyper understøttes. Vælg konstant i dette eksempel
  4.    Datatypen – dette gør det muligt for brugerne at vælge datatypen for definitionen. I øjeblikket 4-datatyper understøttes: jeg.  Streng – disse værdier skal angives i dobbelte anførselstegn ("eksempel streng")  
    II. Boolesk – dette kan enten være SAND eller FALSK  
    III.    Nummerere – kan være et decimaltal  
    IV. DateTime-betyder, at definition af typen datotype. Data skal angives ved hjælp af dette format-mm-dd-åååå hh:mm:ss AM\PM  
  5. Skriv – er dette, hvor du angiver værdien af definitionen for din. Den valgte datatype skal overholde de værdier, der er angivet her. Du kan enten angive en enkelt værdi, et sæt af værdier, der er adskilt af komma eller en række værdier ved hjælp af feltet nøgleord *til*. For eksempel kan du angive entydig værdi 1; et sæt 1, 2, 3; eller et område 1 til 5. Bemærk, at området understøttes kun for tallene.
  6. Klik på *OK*.

![Alternativ tekst][5]
##<a name="xml-definition"></a>XML-definitionen
Hvis ordliste Type er valgt som XML, de følgende input skal angives  
  en.    Skema – på på dette, der åbnes en ny blade at tillade bruger enten vælge på en liste over allerede overførte skemaer eller tilladelse til at overføre en ny.
b.    XPATH-dette input henter åbnes ved angivelse kun, når du har valgt et skema i ovenstående trin. At klikke på dette, vises det skema, der er valgt, og gør det muligt at vælge den node, hvor en ordliste definition skal oprettes.  
  c.    FAKULTET – denne input identificerer, hvilket dataobjekt skal indføres til i regler programmet behandling. Dette er en avancerede egenskaber og som standard er indstillet til det overordnede element i den valgte XPATH. FAKULTET bliver særligt vigtig for sammenkædning og samling scenarier.

![Alternativ tekst][6]

### <a name="add-bulk"></a>Tilføje flere
Ovenstående trin har hentet oplevelse til oprettelse af ordliste definitioner. Når oprettet, vises de oprettede ordliste definitioner i listeform. Der er krav, så du kan oprette flere definitioner fra det samme skema stedet ovenstående trin for hver enkelt gang. Dette er hvor tilføje flere egenskab bliver meget nyttigt.
Valg af "Tilføj flere", kommer du til en ny blade. Det første trin er at vælge skemaet, flere definitioner der skal oprettes. Hvis du markerer dette åbnes en ny blade tillade du enten vælge på en liste over skemaer, du allerede er overført eller tilladelse til at overføre en ny.
Egenskaben XPATHS får nu låses op. Hvis du markerer dette, åbnes den skema Fremviser, hvor flere noder kan vælges.
Navne for de flere definitioner, der er oprettet som standard til navnet på den knude, der er markeret. Disse kan altid ændres efter oprettelse af.

![Alternativ tekst][7]

##<a name="policy-creation"></a>Oprettelse af politik
Når udvikleren har oprettet nødvendige vocabularies, er forventningen, virksomhedsanalytikere ville have på én oprettelse af virksomhedens politikker via Azure-portalen.  
    1. i Appen regler, der har oprettet, er der en politik lens at klikke på som brugeren træder politikken oprettelse af siden.  
    2. denne side vises på listen over politikker, der har denne bestemt regler App. Analytikeren kan tilføje en ny politik ved blot at skrive et navn på politik og nå fanen to gange. Flere politikker kan være placeret i en enkelt regler API App.  
    3. vælge politikken oprettede tager brugeren oplysningssiden politik for hvor en kan se de regler, der er i politikken.  
    ![Alternativ tekst][8]
 4.  Vælg "Tilføj" for at tilføje en ny regel. Dette kommer du til en ny blade.

##<a name="rule-creation"></a>Oprettelse af regler
En regel er samling af betingelse og handling sætninger. Handlingerne, der udføres, hvis betingelsen evalueres til sand. Give et entydigt regelnavn (for den pågældende politik) og en beskrivelse (valgfrit) i bladet Opret regel.
Feltet betingelse (hvis) kan bruges til at oprette komplekse betinget sætninger. Følgende er de nøgleord, understøttes:  
1.  Og – betinget operator  
2.  Eller -betinget operator  
3.  indeholder\_ikke\_findes  
4.  der findes  
5.  FALSK  
6.  er\_lig\_til  
7.  er\_større\_end  
8.  er\_større\_end\_lig\_til  
9.  er\_i  
10. er\_mindre\_end  
11. er\_mindre\_end\_lig\_til  
12. er\_ikke\_i  
13. er\_ikke\_lig\_til  
14. Rest  
15. SAND  

Feltet Action(THEN) kan indeholde flere sætninger, en hver linje for at oprette handlinger, der skal udføres. Følgende er de nøgleord, understøttes:  
1.  er lig med  
2.  FALSK  
3.  SAND  
4.  standse  
5.  Rest  
6.  Null-værdi  
7.  opdatere  

Felterne betingelse og handling giver Intellisense for at hjælpe dig med at redigere en regel hurtigt. Dette kan udløses ved at trykke på ctrl + mellemrum eller ved lige er begyndt at skrive. Nøgleord skrevet tegn filtreret ned og vises automatisk. Vinduet intellisense viser alle nøgleord og ordliste definitioner.
![Alternativ tekst][9]

##<a name="explicit-forward-chaining"></a>Eksplicitte Videresend sammenkædning
BizTalk-regler understøtter eksplicitte fremad sammenkædning, hvis brugere vil gerne Evaluer regler i forbindelse med visse handlinger, kan de udløse dette ved hjælp af bestemte nøgleord. Følgende er de nøgleord, understøttes:  
   1.   opdatere <vocabulary definition> – dette nøgleord evaluerer alle regler, der bruger den angivne ordliste definition i dets tilstand.  
   2.   Standse – dette nøgleord stopper alle regel udførelser

##<a name="enabledisable-rules"></a>Enable\Disable regler
Hver regel i politikken kan aktiveres eller deaktiveres. Alle regler, der er aktiveret som standard. Deaktiveret regler, der kan udføres under udførelse af politik. Enable\Disable regler kan udføres enten fra bladet regel direkte-kommandoerne er tilgængelige i kommandolinjen på øverst del af bladet eller fra politikken, i genvejsmenuen (højreklik på en regel) har mulighed for at enable\disable.

##<a name="rule-priority"></a>Regelprioritet
Alle regler for en politik udføres i rækkefølge. Prioriteten af udførelse af bestemmes af den rækkefølge, hvori de opstår i politikken. Denne prioriteten kan ændres ved blot at trække og slippe reglen.

##<a name="test-policy"></a>Teste politik
Du kan teste din politikker ved hjælp af kommandoen "Teste politik" i bladet teste politik. I denne blade kan du se en liste over ordliste definitioner, der bruges i politikken, der kræver en brugerinput. Brugere kan manuelt tilføje værdier for disse indgange til deres test-scenarie. Alternativt kan brugerne kan også vælge at importere teste XMLs for input. Når alle input befinder dig i, kan kun køres testen og afgang for hver definition af ordliste vises i outputkolonnen for let sammenligning. Klik på "Vis logfiler" for at få vist udførelse af logfiler for at få vist forretningsanalytiker fulde logfiler. Hvis du vil gemme logfiler, indstillingen "Gem Output" er tilgængelig til at gemme alle teste relaterede data til uafhængige analyser.

## <a name="using-rules-in-logic-apps"></a>Brug af regler i logik Apps
Det er nu klar til forbrug, når politikken har skrevet og testet. Du kan oprette en ny logik App ved at vælge logik Apps i venstre side af startsiden på portalen. Når din logik App er blevet oprettet, Start den og derefter vælge *udløsere og handlinger*. Derefter kan du vælge skabelonen *oprette fra bunden* . Følg trinnene for at tilføje din BizTalk regler API-App til Appen logik. Når dette er færdig, vises der en indstilling for at vælge, hvilken regler API App (handling) til destination. Handlinger med listen over politikker, der skal udføres. Vælge en bestemt regel, hvorefter materialer, der kræves til politikken skal tildeles. Brugere kan bruge output fra API App regler strømmen til yderligere beslutningsprocesser.

## <a name="using-rules-via-apis"></a>Brug af regler via API'er
Regler API App kan også anvendes ved hjælp af et bredt udvalg af API'er. Denne måden, hvorpå brugere begrænset ikke til kun at bruge logik Apps og kan bruge regler i alle programmer ved at gøre RESTEN opkald. De nøjagtige REST API'er tilgængelige kan ses ved at klikke på objektiv til "API defineres" i dashboardet regler.

![Alternativ tekst][10]

Følgende er et eksempel på, hvordan en kan bruge denne API i C#

            // Constructing the JSON message to use in API call to Rules API App

            // xmlInstance is the XML message instance to be passed as input to our Policy
            string xmlInstance = "<ns0:Patient xmlns:ns0=\"http://tempuri.org/XMLSchema.xsd\">  <ns0:Name>Name_0</ns0:Name>  <ns0:Email>Email_0</ns0:Email>  <ns0:PatientID>PatientID_0</ns0:PatientID>  <ns0:Age>10.4</ns0:Age>  <ns0:Claim>    <ns0:ClaimDate>2012-05-31T13:20:00.000-05:00</ns0:ClaimDate>    <ns0:ClaimID>10</ns0:ClaimID>    <ns0:TreatmentID>12</ns0:TreatmentID>    <ns0:ClaimAmount>10000.0</ns0:ClaimAmount>    <ns0:ClaimStatus>ClaimStatus_0</ns0:ClaimStatus>    <ns0:ClaimStatusReason>ClaimStatusReason_0</ns0:ClaimStatusReason>  </ns0:Claim></ns0:Patient>";

            JObject input = new JObject();

            // The JSON object is to be of form {"<XMLSchemName>_<RootNodeName>":"<XML Instance String>".
            // In the below case, we are using XML Schema - "insruanceclaimsschema" and the root node is "Patient".
            // This is CASE SENSITIVE.
            input.Add("insuranceclaimschema_Patient", xmlInstance);
            string stringContent = JsonConvert.SerializeObject(input);


            // Making REST call to Rules API App
            HttpClient httpClient = new HttpClient();

            // The url is the Host URL of the Rules API App
            httpClient.BaseAddress = new Uri("https://rulesservice77492755b7b54c3f9e1df8ba0b065dc6.azurewebsites.net/");
            HttpContent httpContent = new StringContent(stringContent);
            httpContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json");

            // Invoking API "Execute" on policy "InsruranceClaimPolicy" and getting response JSON object. The url can be gotten from the API Definition Lens
            var postReponse = httpClient.PostAsync("api/Policies/InsuranceClaimPolicy?comp=Execute", httpContent).Result;

Bemærk, at ovenfor regler API App tilhørende sikkerhedsindstillinger, der er angivet til "Anon offentlige". Dette kan konfigureres fra API App ved hjælp af – alle indstillinger -> Programindstillinger -> adgangsniveau.

![Alternativ tekst][11]

## <a name="editing-vocabulary-and-policy"></a>Redigere ordliste og politik
En af de store fordele ved hjælp af forretningsregler er, at ændringer til forretningslogik kan installeres fremstilling meget hurtigere. Eventuelle ændringer ordliste og politikker anvendt øjeblikkeligt bliver i fremstilling. Brugeren skal blot gå til det pågældende ordliste definition eller politik og foretage ændringen har den træde i kraft.

<!--Image references-->
[1]: ./media/app-service-logic-use-biztalk-rules/InsuranceScenario.PNG
[2]: ./media/app-service-logic-use-biztalk-rules/InsuranceBusinessLogic.png
[3]: ./media/app-service-logic-use-biztalk-rules/CreateRuleApiApp.png
[4]: ./media/app-service-logic-use-biztalk-rules/RulesDashboard.png
[5]: ./media/app-service-logic-use-biztalk-rules/LiteralVocab.PNG
[6]: ./media/app-service-logic-use-biztalk-rules/XMLVocab.PNG
[7]: ./media/app-service-logic-use-biztalk-rules/BulkAdd.PNG
[8]: ./media/app-service-logic-use-biztalk-rules/PolicyList.PNG
[9]: ./media/app-service-logic-use-biztalk-rules/RuleCreate.PNG
[10]: ./media/app-service-logic-use-biztalk-rules/APIDef.PNG
[11]: ./media/app-service-logic-use-biztalk-rules/PublicAnon.PNG
