<properties 
    pageTitle="Skrive programmer, der bruger Service Bus køer | Microsoft Azure"
    description="Hvordan du kan skrive et enkelt kø-baseret program, der bruger Service Bus."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/03/2016"
    ms.author="sethm" />

# <a name="create-applications-that-use-service-bus-queues"></a>Oprette programmer, der bruger Service Bus køer

Dette emne beskrives Service Bus køer og viser, hvordan du kan skrive et enkelt kø-baseret program, der bruger Service Bus.

Forestil dig en situation fra hele verden af detailsalg, hvori salgsdata fra individuelle Point-of-Sale (POS)-klienter skal distribueres til et lager-system, der bruger dataene til at afgøre, hvornår aktie der skal genbestilles. Denne løsning bruger Service Bus messaging for kommunikationen mellem-klienter og administrationssystem lager som vist i følgende figur:

![Billede af Service Bus køer 1](./media/service-bus-create-queues/IC657161.gif)

Hver POS terminal rapporter dens salgsdata ved at sende meddelelser til **DataCollectionQueue**. Disse meddelelser forbliver i denne kø, indtil de er blevet hentet ved administrationssystem lager. Dette mønster kaldes ofte *asynkron messaging*, fordi POS-klient ikke behøver at vente på et svar fra lager administrationssystem at fortsætte deres arbejde.

## <a name="why-queuing"></a>Hvorfor queuing?

Før vi ser på den kode, der kræves for at konfigurere dette program, skal du overveje fordelen ved at bruge en kø i dette scenarie i stedet for få POS-klienter tale direkte (synkront) administrationssystem lager.

### <a name="temporal-decoupling"></a>Tidsmæssig afkobling

Med asynkron SMS mønsteret behøver producenter og forbrugere ikke at være online på samme tid. Messaging-infrastrukturen indeholder pålideligt meddelelser, indtil arket personen er klar til at modtage dem. Det betyder, at komponenterne i det distribuerede program kan blive afbrudt, enten frivilligt; for eksempel til vedligeholdelse, eller på grund af nedbrud komponent, uden påvirker hele systemet. Desuden må programmet arket kun have til at være online for visse tidspunkter på dagen. For eksempel i dette scenario detailsalg administrationssystem lager kun muligvis kommer online efter slutningen af arbejdsdag.

### <a name="load-leveling"></a>Indlæse udjævning

I mange programmer varierer system Indlæs over tid, mens den behandlingstid, for hver enhed for arbejde er typisk konstant. Intermediating meddelelse producenter og forbrugere med en kø betyder, at programmet arket (arbejder) kun har skal klargøres Hvis du vil vedligeholde en gennemsnitlige belastning i stedet for en spidsbelastning. Dybden af køen vil vokser og kontrakt som varierende indgående belastning. Dette gemmer direkte penge med hensyn til mængden infrastruktur nødvendig for at behandle programmet Indlæs.

![Service Bus køer billede 2](./media/service-bus-create-queues/IC657162.gif)

### <a name="load-balancing"></a>Justering af belastning

Efterhånden som afkrydsningsfeltet Indlæs øges, kan flere arbejdsprocesser tilføjes til at læse fra arbejder køen. Hver meddelelse er behandlet af kun én af arbejdsprocesserne. Desuden denne hente-baserede justering af belastning giver mulighed for optimal brugen af computerne, der arbejder selv, såfremt computerne, der bruger, der indeholder forskellige behandling potens, som de vil trække meddelelser med deres egen maksimal hastighed. Dette mønster kaldes ofte konkurrerende consumer mønstret.

![Billede af Service Bus køer 3](./media/service-bus-create-queues/IC657163.gif)

### <a name="loose-coupling"></a>Løs kobling

Brug af MSMQ til mellemliggende mellem meddelelse producenter og forbrugere indeholder en indbygget Løs kobling mellem komponenterne. Da producenter og forbrugere ikke er klar over hinanden, kan en forbruger opgraderes uden nogen effekt på producenten. Desuden kan SMS topologien udvikle uden at påvirke de eksisterende slutpunkter. Vi vil diskutere dette mere, når vi taler om udgivelse/abonnement.

## <a name="show-me-the-code"></a>Vis mig koden

Følgende afsnit viser, hvordan du bruger Service Bus til at opbygge dette program.

### <a name="sign-up-for-an-azure-account"></a>Tilmelde sig en Azure-konto

Du skal en Azure-konto for at begynde at arbejde med Service Bus. Hvis du ikke allerede har en, du kan tilmelde dig en gratis konto [her](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A85619ABF).

### <a name="create-a-namespace"></a>Oprette et navneområde

Når du har et abonnement, kan du [oprette et nyt navneområde](service-bus-create-namespace-portal.md). Hver navneområde fungerer som en angiver området beholder for et sæt Service Bus enheder. Giv din nye navneområde et entydigt navn på tværs af alle Service Bus konti. 

### <a name="install-the-nuget-package"></a>Installere pakken NuGet

Hvis du vil bruge navneområdet Service Bus, skal et program referere til samlingen Service Bus specifikt Microsoft.ServiceBus.dll. Du kan finde denne samling som en del af Microsoft Azure SDK, og overførslen er aktiveret i [Azure SDK overførselssiden](https://azure.microsoft.com/downloads/). Men [tjenesten Bus NuGet pakke](https://www.nuget.org/packages/WindowsAzure.ServiceBus) er den nemmeste måde at få Service Bus API og for at konfigurere dit program med alle Service Bus afhængigheder.

### <a name="create-the-queue"></a>Oprette køen

Administration af handlinger for tjenesten Bus messaging enheder (køer og Udgiv/Abonner emner) udføres via klassen [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) . Service Bus bruger en [Delt Access signatur (SAS)](service-bus-sas-overview.md) baseret sikkerhedsmodel. Klassen [en TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) repræsenterer en sikkerhed token udbyder med indbyggede factory metoder, der returnerer nogle kendte token udbydere. Vi bruger en [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) metode til at indeholde SAS legitimationsoplysningerne. Forekomsten [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) oprettes derefter med basisadressen for navneområdet Service Bus og provideren token.

Klassen [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) indeholder metoder til at oprette, optælle og slette SMS enheder. Den kode, der er vist her viser, hvordan [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) forekomsten oprettes og bruges til at oprette **DataCollectionQueue** køen.

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", 
                "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
 
TokenProvider tokenProvider = 
    TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = 
    new NamespaceManager(uri, tokenProvider);
namespaceManager.CreateQueue("DataCollectionQueue");
```

Bemærk, at der er overloads af metoden [CreateQueue](https://msdn.microsoft.com/library/azure/hh322663.aspx) , der aktiverer egenskaber for køen at finjustere. For eksempel kan du angive time to live (TTL) standardværdien for meddelelser, der sendes til køen.

### <a name="send-messages-to-the-queue"></a>Sende meddelelser til køen

For Runtime-handlinger på Service Bus enheder; sende og modtage meddelelser kan et program skal først f.eks et [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) objekt. Svarer til klassen [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) forekomsten [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) er oprettet fra basisadressen for navneområdet tjenesten og provideren token.

```
 BrokeredMessage bm = new BrokeredMessage(salesData);
 bm.Label = "SalesReport";
 bm.Properties["StoreName"] = "Redmond";
 bm.Properties["MachineID"] = "POS_1";
```

Meddelelser sendes til, og har modtaget fra Service Bus køer er forekomster af klassen [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) . Denne klasse består af et sæt standardegenskaber (såsom [etiket](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) og [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), en ordbog, der bruges til at holde programmet egenskaber og helhed af vilkårlig application data. Et program kan indstille brødteksten ved overførsel af serialiserbar objekter (i følgende eksempel sender i et **SalesData** objekt, der repræsenterer salgsdata fra POS-klient), som skal bruge [DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) ordne sekventielt objektet. Du kan også kan en [Stream](https://msdn.microsoft.com/library/azure/system.io.stream.aspx) -objekt leveres.

Den nemmeste måde at sende meddelelser til en given kø i dette tilfælde **DataCollectionQueue**, er at bruge [CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) til at oprette et [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) objekt direkte fra [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) forekomsten.

```
MessageSender sender = factory.CreateMessageSender("DataCollectionQueue");
sender.Send(bm);
```

### <a name="receiving-messages-from-the-queue"></a>Modtage meddelelser fra køen

Hvis du vil modtage meddelelser fra køen, kan du bruge en [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) objekt, som du opretter direkte fra [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) ved hjælp af [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx). Meddelelse modtagere kan arbejde i to forskellige tilstande: **ReceiveAndDelete** og **PeekLock**. [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) angives, når meddelelsen modtageren er oprettet, som en parameter til [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx) opkaldet.


Når du bruger tilstanden **ReceiveAndDelete** , er Modtag en enkelt billede handling. Det vil sige, når tjenesten Bus modtager anmodningen, den markerer meddelelsen som der consumed og returnerer den til programmet. **ReceiveAndDelete** tilstand er den nemmeste model og fungerer bedst for scenarier, hvor programmet kan acceptere ikke behandling af en meddelelse om en mislykket var skal udføres. For at forstå, bør du overveje et scenarie, hvor forbrugeren udsteder Modtag anmodningen og derefter går ned, før du behandler den. Da Service Bus markeret meddelelsen som bliver brugt, når programmet genstartes og starter forbrug meddelelser igen, den gået glip den meddelelse, der blev brugt, før går ned.

I **PeekLock** tilstand bliver Modtag en handling af to faser, som gør det muligt at understøtte programmer, der ikke kan acceptere manglende meddelelser. Når Service Bus modtager anmodningen, finder den næste meddelelse at blive brugt det, låser den for at forhindre andre forbrugere, modtager den og returnerer den derefter til programmet. Når programmet afsluttes behandling af meddelelsen (eller gemmer den pålideligt til fremtidige behandling), er fuldført i anden fase af Modtag processen ved at ringe [udført](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) på den modtagne meddelelse. Når Service Bus registrerer [færdigt](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) ringe, markerer meddelelsen som blive consumed.

To andre resultater er muligt. Først, hvis programmet er i stand til at behandle meddelelsen eller anden grund, det kan ringe til [afbryde](https://msdn.microsoft.com/library/azure/hh181837.aspx) på den modtagne meddelelse (i stedet for [færdigt](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). Derved Service Bus at låse op meddelelsen og gøre det tilgængeligt igen, der modtages, enten på den samme forbruger eller som en anden fuldfører forbruger. Andet, der er en timeout, der er knyttet til låsen og hvis programmet ikke kan behandle meddelelsen før låsen timeout udløber (for eksempel hvis programmet går ned) og derefter Service Bus vil låse op for meddelelsen og gøre det tilgængeligt skal modtages igen (udføre grundlæggende handlingen [afbryde](https://msdn.microsoft.com/library/azure/hh181837.aspx) som standard).

Bemærk, at hvis programmet går ned, når det behandler meddelelsen, men før [færdigt](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) anmodningen, er udstedt, meddelelsen skal være udleveres igen til programmet når genstartes. Dette kaldes ofte *Mindst én gang* behandling. Det betyder, at alle meddelelser, behandles mindst én gang, men i nogle situationer kan være udleveres igen den samme meddelelse. Hvis dette scenario ikke kan acceptere dublerede behandling, kræves yderligere logik i programmet på computeren til at registrere dubletter. Det kan ske baseret på egenskaben [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) for meddelelsen. Værdien af denne egenskab konstant på tværs af leveringsforsøg. Dette kaldes *Én gang* behandling.

Den kode, der er vist her modtager og behandler en meddelelse ved hjælp af **PeekLock** tilstanden, som er standard, hvis nogen [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) værdi eksplicit ikke er angivet.

```
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionQueue");
BrokeredMessage receivedMessage = receiver.Receive();
try
{
    ProcessMessage(receivedMessage);
    receivedMessage.Complete();
}
catch (Exception e)
{
    receivedMessage.Abandon();
}
```

### <a name="use-the-queue-client"></a>Bruge kø-klienten

Eksemplerne tidligere i dette afsnit oprettet [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) og [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) objekter direkte fra [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) til at sende og modtage meddelelser fra køen, henholdsvis. En alternativ metode er at bruge klassen [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) , som understøtter både sende og modtage handlinger foruden mere avancerede funktioner, som sessioner.

```
QueueClient queueClient = factory.CreateQueueClient("DataCollectionQueue");
queueClient.Send(bm);
            
BrokeredMessage message = queueClient.Receive();

try
{
    ProcessMessage(message);
    message.Complete();
}
catch (Exception e)
{
    message.Abandon();
} 
```

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært det grundlæggende omkring af køer, kan du se [oprette programmer, der bruger Service Bus emner og abonnementer](service-bus-create-topics-subscriptions.md) for at fortsætte diskussionen ved hjælp af funktionerne Udgiv/Abonner i Service Bus emner og abonnementer.