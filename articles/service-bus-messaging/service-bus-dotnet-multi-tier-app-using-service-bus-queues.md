<properties
    pageTitle=".NET med flere lag programmet | Microsoft Azure"
    description="Et .NET-selvstudium, der hjælper dig med at udvikle en med flere lag app i Azure, der bruger Service Bus køer til at kommunikere mellem niveauer."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="sethm"/>

# <a name="net-multi-tier-application-using-azure-service-bus-queues"></a>.NET med flere lag program ved hjælp af Azure Service Bus køer

## <a name="introduction"></a>Introduktion

Det er nemt ved hjælp af Visual Studio og gratis Azure SDK til .NET udvikling til Microsoft Azure. Dette selvstudium vejleder dig gennem trinnene til at oprette et program, der bruger flere Azure ressourcer, der kører i din lokale miljø. Trin forudsætter, at du ikke har nogen foregående erfaring med Azure.

Du kan lære følgende:

-   Hvordan du aktiverer din computer til Azure udvikling med en enkelt Hent og Installer.
-   Sådan bruges Visual Studio til at udvikle til Azure.
-   Sådan oprettes et program med flere lag i Azure ved hjælp af internettet og arbejder roller.
-   Sådan kommunikere mellem ved hjælp af Service Bus køer.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

I dette selvstudium skal du oprette og køre programmet med flere lag i en Azure skybaseret tjeneste. Front end bliver en ASP.NET MVC web rolle og back-end bliver en kollega-rolle, der bruger en tjeneste Bus kø. Du kan oprette det samme med flere lag program med front end som et webprojekt, der er installeret på en Azure websted i stedet for en skybaseret tjeneste. Du kan finde oplysninger om, hvad du skal gøre anderledes på en Azure websted front-end, afsnittet [Næste trin](#nextsteps) . Du kan også prøve [.NET på-lokale/skyen hybrid programmet](../service-bus-relay/service-bus-dotnet-hybrid-app-using-service-bus-relay.md) selvstudiet.

Følgende skærmbillede viser det færdige program.

![][0]

## <a name="scenario-overview-inter-role-communication"></a>Oversigt over scenarie: mellem rolle kommunikation

Hvis du vil sende en rækkefølge for behandling, skal front end-komponent i Brugergrænsefladen, kører i rollen web interagere med midterste niveau logik kører i rollen arbejder. I dette eksempel bruges Service Bus formidlet messaging af kommunikation mellem lag.

Brug af formidlet messaging mellem internettet og midterste niveauer decouples de to komponenter. I modsætning til direkte messaging (det vil sige, TCP- eller HTTP), kan web niveauet kan ikke oprette forbindelse til det midterste lag direkte; i stedet flytter enheder af arbejde, som meddelelser til tjenesten Bus, der bevarer dem pålideligt, indtil det midterste modul er klar til at bruge og behandle dem.

Service Bus indeholder to objekter for at understøtte formidlet messaging: køer og emner. Med køer bruges hver meddelelse sendes til køen af en enkelt modtager. Emner understøtter Udgiv/Abonner mønstret hvor meddelelserne publicerede er gjort tilgængelige til et abonnement, der er registreret med emnet. Hvert abonnement fører logisk sin egen kø af meddelelser. Abonnementer kan også være konfigureret med filterregler, der begrænser sæt af meddelelser, der sendes til abonnement køen til dem, der svarer til filteret. I følgende eksempel bruges Service Bus køer.

![][1]

Denne funktion kommunikation har flere fordele i forhold til direkte messaging:

-   **Tidsmæssig afkobling.** Med asynkron SMS mønsteret, behøver producenter og forbrugere ikke være online på samme tid. Service Bus indeholder pålideligt meddelelser, indtil arket personen er klar til at modtage dem. Dette gør det muligt for komponenterne i den distribuerede programmer til at blive afbrudt, enten frivilligt, for eksempel vedligeholdelse, eller på grund af nedbrud komponent uden at påvirke systemet som helhed. Programmet arket muligvis desuden kun kommer online under visse tidspunkter af dagen.

-   **Indlæse udjævning.** I mange programmer varierer system Indlæs hen over tid, mens den behandlingstid, for hver enhed for arbejde er typisk konstant. Intermediating meddelelse producenter og forbrugere med en kø betyder, at programmet arket (arbejder) kun skal klargøres for at imødekomme gennemsnitlige belastning i stedet for spidsbelastning. Køen dybde vokser og aftaler som varierende indgående belastning. Dette gemmer direkte penge med hensyn til mængden infrastruktur nødvendig for at behandle programmet Indlæs.

-   **Justering af belastning.** Som indlæse øges, kan flere arbejdsprocesser tilføjes til at læse fra køen. Hver meddelelse er behandlet af kun én af arbejdsprocesserne. Desuden muliggør denne hente-baserede justering af belastning optimal brug af arbejder maskiner selvom arbejder computere er forskellige med hensyn til behandling potens, som de vil trække meddelelser med deres egen maksimal hastighed. Dette mønster kaldes ofte mønstret *konkurrere forbrugere* .

    ![][2]

I følgende afsnit beskrives den kode, der implementerer denne arkitektur.

## <a name="set-up-the-development-environment"></a>Konfigurere udviklingsmiljøet

Før du kan begynde at Azure udviklingsprogrammer, få værktøjerne, og konfigurere dit udviklingsmiljø.

1.  Installer Azure SDK til .NET på [få værktøjer og SDK][].

2.  Klik på **Installer SDK** for versionen af Visual Studio, du bruger. Trinnene i dette selvstudium bruger Visual Studio-2015.

4.  Når du bliver bedt om at køre eller gemme installationsprogrammet, kan du klikke på **Kør**.

5.  Klik på **Installer** i **Web Platform Installer**, og fortsæt med installationen.

6.  Når installationen er fuldført, har du alt det er nødvendigt at begynde at udvikle appen. SDK indeholder funktioner, som du kan let at udvikle Azure-programmer i Visual Studio. Hvis du ikke har Visual Studio er installeret, installeres SDK også den gratis Visual Studio Express.

## <a name="create-a-namespace"></a>Oprette et navneområde

Det næste trin er at oprette et service navneområde og hente en delt Access signatur (SAS) nøgle. Et navneområde indeholder en programmet grænse for hvert program fremvises Service Bus. Der oprettes en SAS nøgle af systemet, når der oprettes et navneområde. Kombinationen af navneområde og SAS nøgle yder legitimationsoplysninger for tjenesten Bus til at godkende adgang til et program.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-web-role"></a>Oprette en web rolle

I dette afsnit, skal oprette du front-end i dit program. Først skal oprette du de sider, der viser dit program.
Tilføje kode, der sender elementer til en tjeneste Bus kø og viser for statusoplysninger om køen herefter.

### <a name="create-the-project"></a>Oprette projektet

1.  Med administratorrettigheder kan du starte Microsoft Visual Studio. For at starte Visual Studio med administratorrettigheder, skal du højreklikke på programikonet **Visual Studio** , og klik derefter på **Kør som administrator**. Azure Beregn emulatoren, gennemgås senere i denne artikel kræver, at Visual Studio startes med administratorrettigheder.

    Klik på **Ny**i Visual Studio, i menuen **filer** , og klik derefter på **projekt**.

2.  Klik på **skyen** **Installeret skabeloner**under **Visual C#**, og klik derefter på **Azure skybaseret tjeneste**. Navngive projektet **MultiTierApp**. Klik derefter på **OK**.

    ![][9]

3.  **.NET Framework 4.5** roller, dobbeltklik på **ASP.NET Web rolle**.

    ![][10]

4.  Hold markøren over **WebRole1** under **Azure skybaseret tjeneste løsning**, klik på blyantsikonet, og Omdøb web rollen til **FrontendWebRole**. Klik derefter på **OK**. (Sørg for, at du angiver "Frontend" med en små 'e,"ikke"FrontEnd").

    ![][11]

5.  Klik på **MVC**fra dialogboksen **ASP.NET projekt** på listen **Vælg en skabelon** .

    ![][12]

6. Klik på knappen **Skift godkendelse** stadig i dialogboksen **Nyt ASP.NET-projekt** . Klik på **Ingen godkendelse**i dialogboksen **Skift godkendelse** , og klik derefter på **OK**. I dette selvstudium, du anvender en app, som ikke har brug for et brugerlogon.

    ![][16]

7. Klik på **OK** for at oprette projektet tilbage i dialogboksen **Ny ASP.NET-projekt** .

6.  I **Solution Explorer**, i **FrontendWebRole** projektet, skal du højreklikke på **referencer**, og klik derefter på **Administrer NuGet pakker**.

7.  Klik på fanen **Gennemse** , og derefter søge efter `Microsoft Azure Service Bus`. Klik på **Installer**, og Accepter vilkår for anvendelse.

    ![][13]

    Bemærk, at de krævede klient assemblies nu der refereres til, og nogle nye kodefiler er blevet tilføjet.

9.  I **Solution Explorer**, skal du højreklikke på **modeller** og klikke på **Tilføj**, og klik derefter på **klasse**. Skriv navnet **OnlineOrder.cs**i feltet **navn** . Klik derefter på **Tilføj**.

### <a name="write-the-code-for-your-web-role"></a>Skrive programkode for din web rolle

I dette afsnit, skal oprette du de forskellige sider, der viser dit program.

1.  Erstat eksisterende navneområde definitionen med følgende kode i filen OnlineOrder.cs i Visual Studio:

    ```
    namespace FrontendWebRole.Models
    {
        public class OnlineOrder
        {
            public string Customer { get; set; }
            public string Product { get; set; }
        }
    }
    ```

2.  I **Solution Explorer**, skal du dobbeltklikke på **Controllers\HomeController.cs**. Tilføj følgende **ved hjælp af** udtalelser øverst i filen for at medtage navneområder for den model, du lige har oprettet, samt Service Bus.

    ```
    using FrontendWebRole.Models;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.ServiceBus;
    ```

3.  Erstat også eksisterende navneområde definitionen med følgende kode i filen HomeController.cs i Visual Studio. Denne kode indeholder metoder til håndtering af indsendelse af elementer til køen.

    ```
    namespace FrontendWebRole.Controllers
    {
        public class HomeController : Controller
        {
            public ActionResult Index()
            {
                // Simply redirect to Submit, since Submit will serve as the
                // front page of this application.
                return RedirectToAction("Submit");
            }
    
            public ActionResult About()
            {
                return View();
            }
    
            // GET: /Home/Submit.
            // Controller method for a view you will create for the submission
            // form.
            public ActionResult Submit()
            {
                // Will put code for displaying queue message count here.
    
                return View();
            }
    
            // POST: /Home/Submit.
            // Controller method for handling submissions from the submission
            // form.
            [HttpPost]
            // Attribute to help prevent cross-site scripting attacks and
            // cross-site request forgery.  
            [ValidateAntiForgeryToken]
            public ActionResult Submit(OnlineOrder order)
            {
                if (ModelState.IsValid)
                {
                    // Will put code for submitting to queue here.
    
                    return RedirectToAction("Submit");
                }
                else
                {
                    return View(order);
                }
            }
        }
    }
    ```

4.  Klik på menuen **Build** **Opbygge løsning** for at teste nøjagtigheden af dit arbejde hidtil.

5.  Opret nu visning for de `Submit()` metode, du oprettede tidligere. Højreklik i det `Submit()` metode (overbelastning af `Submit()` , der tager ikke parametre), og vælg derefter **Tilføj visning**.

    ![][14]

6.  Der vises en dialogboks til oprettelse af visningen. Vælg **Opret**på listen **skabelon** . Klik på klassen **OnlineOrder** på listen **Model klasse** .

    ![][15]

7.  Klik på **Tilføj**.

8.  Nu, ændre det viste navn på dit program. I **Solution Explorer**skal du dobbeltklikke på den **Views\Shared\\_Layout.cshtml** filen skal åbnes i Visual Studio-editor.

9.  Erstatte alle forekomster af **Mine ASP.NET-program** med **LITWARES produkter**.

10. Fjerne linkene **Home**, **om**og **Kontakt** . Slette den markerede kode:

    ![][28]

11. Til sidst skal ændre bidrag siden for at medtage nogle oplysninger om køen. I **Solution Explorer**skal du dobbeltklikke på filen **Views\Home\Submit.cshtml** for at åbne den i Visual Studio editor. Tilføj følgende linje efter `<h2>Submit</h2>`. Nu skal den `ViewBag.MessageCount` er tom. Du udfylder det senere.

    ```
    <p>Current number of orders in queue waiting to be processed: @ViewBag.MessageCount</p>
    ```

12. Du har nu implementeret din brugergrænseflade. Du kan trykke på **F5** for at køre programmet, og Bekræft, at det ser ud som forventet.

    ![][17]

### <a name="write-the-code-for-submitting-items-to-a-service-bus-queue"></a>Skrive programkode til afsendelse af elementer til en tjeneste Bus kø

Nu skal du tilføje kode til at sende elementer til en kø. Først skal oprette du en klasse, der indeholder forbindelsesoplysningerne Service Bus kø. Derefter initialiseret forbindelsen fra Global.aspx.cs. Til sidst skal opdatere den bidrag kode, du oprettede tidligere i HomeController.cs til at sende faktisk elementer til en tjeneste Bus kø.

1.  I **Solution Explorer**, skal du højreklikke på **FrontendWebRole** (Højreklik i projektet, ikke rollen). Klik på **Tilføj**, og klik derefter på **klasse**.

2.  Navngiv klassen **QueueConnector.cs**. Klik på **Tilføj** for at oprette klassen.

3.  Nu skal du tilføje kode, der indkapsler forbindelsesoplysningerne og starter forbindelsen til en tjeneste Bus kø. Erstatte hele indholdet af QueueConnector.cs med følgende kode, og angiv værdier for `your Service Bus namespace` (kaldt navneområde) og `yourKey`, som er den **primære nøgle** , du tidligere har hentet fra Azure-portalen.

    ```
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.ServiceBus;
    
    namespace FrontendWebRole
    {
        public static class QueueConnector
        {
            // Thread-safe. Recommended that you cache rather than recreating it
            // on every request.
            public static QueueClient OrdersQueueClient;
    
            // Obtain these values from the portal.
            public const string Namespace = "your Service Bus namespace";
    
            // The name of your queue.
            public const string QueueName = "OrdersQueue";
    
            public static NamespaceManager CreateNamespaceManager()
            {
                // Create the namespace manager which gives you access to
                // management operations.
                var uri = ServiceBusEnvironment.CreateServiceUri(
                    "sb", Namespace, String.Empty);
                var tP = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                    "RootManageSharedAccessKey", "yourKey");
                return new NamespaceManager(uri, tP);
            }
    
            public static void Initialize()
            {
                // Using Http to be friendly with outbound firewalls.
                ServiceBusEnvironment.SystemConnectivity.Mode =
                    ConnectivityMode.Http;
    
                // Create the namespace manager which gives you access to
                // management operations.
                var namespaceManager = CreateNamespaceManager();
    
                // Create the queue if it does not exist already.
                if (!namespaceManager.QueueExists(QueueName))
                {
                    namespaceManager.CreateQueue(QueueName);
                }
    
                // Get a client to the queue.
                var messagingFactory = MessagingFactory.Create(
                    namespaceManager.Address,
                    namespaceManager.Settings.TokenProvider);
                OrdersQueueClient = messagingFactory.CreateQueueClient(
                    "OrdersQueue");
            }
        }
    }
    ```

4.  Nu, sikre, at din **initialiseret** metode bliver kaldet. I **Solution Explorer**, skal du dobbeltklikke på **Global.asax\Global.asax.cs**.

5.  Tilføj følgende linje af kode i slutningen af metoden **Application_Start** .

    ```
    FrontendWebRole.QueueConnector.Initialize();
    ```

6.  Til sidst skal du opdatere den webkode, du oprettede tidligere, hvis du vil sende elementer til køen. I **Solution Explorer**, skal du dobbeltklikke på **Controllers\HomeController.cs**.

7.  Opdater den `Submit()` metode (overbelastning, der tager ikke parametre) på følgende måde at få antallet meddelelser for køen.

    ```
    public ActionResult Submit()
    {
        // Get a NamespaceManager which allows you to perform management and
        // diagnostic operations on your Service Bus queues.
        var namespaceManager = QueueConnector.CreateNamespaceManager();
    
        // Get the queue, and obtain the message count.
        var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
        ViewBag.MessageCount = queue.MessageCount;
    
        return View();
    }
    ```

8.  Opdater den `Submit(OnlineOrder order)` metode (overbelastning, der bruger en parameter) på følgende måde til at sende ordreoplysninger til køen.

    ```
    public ActionResult Submit(OnlineOrder order)
    {
        if (ModelState.IsValid)
        {
            // Create a message from the order.
            var message = new BrokeredMessage(order);
    
            // Submit the order.
            QueueConnector.OrdersQueueClient.Send(message);
            return RedirectToAction("Submit");
        }
        else
        {
            return View(order);
        }
    }
    ```

9.  Nu kan du køre programmet igen. Hver gang du sender en rækkefølge, der øger antallet meddelelser.

    ![][18]

## <a name="create-the-worker-role"></a>Oprette rollen arbejder

Nu kan du oprette rollen arbejder, der behandler rækkefølge indsendelser. I dette eksempel bruges **Arbejder rolle med Service Bus kø** Visual Studio project-skabelon. Du allerede har fået de nødvendige legitimationsoplysninger fra portalen.

1. Kontrollér, at du har knyttet Visual Studio til Azure-konto.

2.  Højreklik på mappen **roller** under **MultiTierApp** projektet i **Solution Explorer** i Visual Studio.

3.  Klik på **Tilføj**, og klik derefter på **Ny arbejder rolle projekt**. Dialogboksen **Tilføj ny rolle projekt** vises.

    ![][26]

4.  Klik på **Arbejder rolle med Service Bus kø**i dialogboksen **Tilføj ny rolle projekt** .

    ![][23]

5.  Navngive projektet **OrderProcessingRole**i feltet **navn** . Klik derefter på **Tilføj**.

6.  Kopiér forbindelsesstrengen, som du hentede i trin 9 i afsnittet "Oprette et Service Bus navneområde" til Udklipsholder.

7.  I **Solution Explorer**, skal du højreklikke på **OrderProcessingRole** , du oprettede i trin 5 (Sørg for, at du højreklikker på **OrderProcessingRole** under **roller**og ikke klassen). Klik derefter på **Egenskaber**.

8.  Klik i feltet **værdi** for **Microsoft.ServiceBus.ConnectionString**under fanen **Indstillinger** i dialogboksen **Egenskaber** , og derefter indsætte det slutpunkt værdi, du har kopieret i trin 6.

    ![][25]

9.  Oprette en **OnlineOrder** klasse for at repræsentere ordrerne, som du behandle dem fra køen. Du kan genbruge en klasse, du allerede har oprettet. I **Solution Explorer**, skal du højreklikke på klassen **OrderProcessingRole** (højreklik på ikonet for den klasse, ikke rollen). Klik på **Tilføj**, og derefter klikke på **Eksisterende element**.

10. Gå til undermappen for **FrontendWebRole\Models**, og dobbeltklik derefter på **OnlineOrder.cs** for at føje det til dette projekt.

11. I **WorkerRole.cs**, ændre værdien af variablen **QueueName** fra `"ProcessingQueue"` til `"OrdersQueue"` som vist i følgende kode.

    ```
    // The name of your queue.
    const string QueueName = "OrdersQueue";
    ```

12. Tilføj følgende ved hjælp af sætning øverst i filen WorkerRole.cs.

    ```
    using FrontendWebRole.Models;
    ```

13. I den `Run()` fungerer, inden de `OnMessage()` ringe, erstatte indholdet af den `try` -delsætning med følgende kode.

    ```
    Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
    // View the message as an OnlineOrder.
    OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
    Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
    receivedMessage.Complete();
    ```

14. Du har fuldført programmet. Du kan teste det komplette program ved at højreklikke på MultiTierApp projektet i Solution Explorer, vælge **Angiv som Start projekt**og derefter trykke på F5. Bemærk, at antallet meddelelser ikke øges trinvist, fordi rollen arbejder behandler elementer fra køen og markerer dem som fuldført. Du kan se sporingsoutput fra din kollega rolle ved at få vist Azure beregne Emulator Brugergrænsefladen. Du kan gøre dette ved at højreklikke på emulator-ikonet i meddelelsesområdet på proceslinjen og vælge **Vis beregne Emulator brugergrænseflade**.

    ![][19]

    ![][20]

## <a name="next-steps"></a>Næste trin  

Hvis du vil vide mere om Service Bus skal du se følgende ressourcer:  

* [Azure Service Bus][sbmsdn]  
* [Service Bus service side][sbwacom]  
* [Sådan bruger du Bus servicekøer][sbwacomqhowto]  

Hvis du vil vide mere om scenarier med flere lag, i:  

* [.NET med flere lag program ved hjælp af lagerplads tabeller, forespørgsler og BLOB][mutitierstorage]  

  [0]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [2]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [Få værktøjer og SDK]: http://go.microsoft.com/fwlink/?LinkId=271920


  [GetSetting]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.getsetting.aspx
  [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx
  [NamespaceMananger]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx

  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx

  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx

  [EventHubClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx

  [9]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.png
  [10]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [11]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [12]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [13]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [14]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [15]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [16]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-14.png
  [17]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [18]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png

  [19]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [20]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [23]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [25]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [26]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [28]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png

  [sbmsdn]: http://msdn.microsoft.com/library/azure/ee732537.aspx  
  [sbwacom]: /documentation/services/service-bus/  
  [sbwacomqhowto]: service-bus-dotnet-get-started-with-queues.md  
  [mutitierstorage]: https://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  