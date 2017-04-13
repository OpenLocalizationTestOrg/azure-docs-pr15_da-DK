<properties 
    pageTitle="Oprette programmer, der bruger Service Bus emner og abonnementer | Microsoft Azure"
    description="Introduktion til Udgiv-abonnere funktioner, som Service Bus emner og abonnementer."
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
    ms.date="10/04/2016"
    ms.author="sethm" />

# <a name="create-applications-that-use-service-bus-topics-and-subscriptions"></a>Oprette programmer, der bruger Service Bus emner og abonnementer

Azure Service Bus understøtter et sæt af skybaseret, meddelelse rundt programmer-teknologier, herunder pålidelige MSMQ og robust Udgiv/Abonner messaging. I denne artikel bygger på oplysningerne i [oprette programmer, der bruger Service Bus køer](service-bus-create-queues.md) og indeholder en introduktion til de Udgiv/Abonner egenskaber, der findes i Service Bus emner.

## <a name="evolving-retail-scenario"></a>Udvikling af detailsalg scenarie

I denne artikel, fortsætter scenariet detailsalg bruges i [oprette programmer, der bruger Service Bus køer](service-bus-create-queues.md). Tilbagekalde salg dataene fra individuelle punkt af salg POS-klienter skal distribueres til et lager administrationssystem som bruger disse data til at afgøre, hvornår aktie der skal genbestilles. Hver POS terminal rapporterer dens salgsdata ved at sende meddelelser til køen **DataCollectionQueue** , hvor de forbliver, indtil de modtages af administrationssystem lager som vist her:

![Service Bus 1](./media/service-bus-create-topics-subscriptions/IC657161.gif)

Hvis du vil udvikle dette scenario, er blevet føjet et nyt krav til systemet: ejeren af store ønsker lov til at overvåge hvordan store klarer sig i realtid.

Du kan løse dette krav ved systemet skal "Tryk på" fra salgsdata strømmen. Vi vil stadig hver meddelelse sendes af POS-klienter skal sendes til lager administrationssystem som før, men vi vil gerne have en anden kopi af alle de meddelelser, som vi kan bruge til at præsentere visningen dashboard til ejeren af store.

Du kan bruge Service Bus *emner*i en situation som dette, hvor du har brug for hver meddelelse for at blive brugt i flere parter. Emnerne et Udgiv/Abonner mønster, hvor meddelelserne publicerede stilles til rådighed for en eller flere abonnementer, der er registreret med emnet. I modsætning med køer hver meddelelse modtages af en enkelt forbruger.

Meddelelser sendes til et emne på samme måde, som de er sendt til en kø. Dog modtages ikke meddelelser fra emnet direkte; de modtages fra abonnementer. Du kan tænke på et abonnement til et emne som en virtuel kø, der modtager kopier af de meddelelser, der er sendt til dette emne. Meddelelser er modtaget fra et abonnement på samme måde som de modtages fra en kø.

Gå tilbage til scenariet detailsalg, køen erstattes af et emne, og et abonnement er tilføjet, som kan bruge komponenten lager management system. Systemet vises nu på følgende måde:

![Service Bus 2](./media/service-bus-create-topics-subscriptions/IC657165.gif)

Konfigurationen her udfører identisk til den forrige kø-baserede design. Meddelelser, der sendes til emnet, det vil sige, distribueres til det abonnement, **lager** , hvorfra **Lager administrationssystem** forbruger dem.

For at understøtte dashboardet management, oprette vi et andet abonnement på emnet, som vist her:

![Service Bus 3](./media/service-bus-create-topics-subscriptions/IC657166.gif)

Med denne konfiguration er hver meddelelse fra POS-klienter gøres tilgængelige for både **Dashboard** og **lager** abonnementer.

## <a name="show-me-the-code"></a>Vis mig koden

I artiklen [oprette programmer, der bruger Service Bus køer](service-bus-create-queues.md) beskriver, hvordan til at tilmelde sig en Azure-konto og oprette et service navneområde. Hvis du vil bruge et Service Bus navneområde, skal et program referere til samlingen Service Bus specifikt Microsoft.ServiceBus.dll. Den nemmeste måde at referere til Service Bus afhængigheder er at installere Service Bus [Nuget pakke](https://www.nuget.org/packages/WindowsAzure.ServiceBus/). Du kan også finde samlingen som en del af Azure SDK. Overførslen er tilgængelig på siden [Azure SDK overførselssiden](https://azure.microsoft.com/downloads/).

### <a name="create-the-topic-and-subscriptions"></a>Oprette emne og abonnementer

Administration af handlinger for tjenesten Bus messaging enheder (køer og Udgiv/Abonner emner) udføres via klassen [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) . Relevante legitimationsoplysninger er påkrævet for at oprette en forekomst af [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) for et bestemt navneområde. Service Bus bruger en [Delt Access signatur (SAS)](service-bus-sas-overview.md) baseret sikkerhedsmodel. Klassen [en TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) repræsenterer en sikkerhed token udbyder med indbyggede factory metoder, der returnerer nogle kendte token udbydere. Vi bruger en [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) metode til at indeholde SAS legitimationsoplysningerne. Forekomsten [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) oprettes derefter med basisadressen for navneområdet Service Bus og provideren token.

Klassen [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) indeholder metoder til at oprette, optælle og slette SMS enheder. Den kode, der er vist her viser, hvordan forekomsten [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) oprettes og bruges til at oprette **DataCollectionTopic** emne.

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
     
TokenProvider tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = new NamespaceManager(uri, tokenProvider);
 
namespaceManager.CreateTopic("DataCollectionTopic");
```

Bemærk, at der er overloads af metoden [CreateTopic](https://msdn.microsoft.com/library/azure/hh293080.aspx) , der gør det muligt at angive egenskaber i emnet. For eksempel kan du angive time to live (TTL) standardværdien for meddelelser, der sendes til emnet. Dernæst skal tilføje **lager** og **Dashboard** -abonnementer.

```
namespaceManager.CreateSubscription("DataCollectionTopic", "Inventory");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard");
```

### <a name="send-messages-to-the-topic"></a>Sende meddelelser til emnet

For Runtime-handlinger på Service Bus enheder; sende og modtage meddelelser kan et program skal først f.eks et [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) objekt. Svarer til klassen [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) forekomsten [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) er oprettet fra basisadressen for navneområdet tjenesten og provideren token.

```
MessagingFactory factory = MessagingFactory.Create(uri, tokenProvider);
```

Meddelelser sendes til, og har modtaget fra Service Bus emner, er forekomster af klassen [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) . Denne klasse består af et sæt standardegenskaber (såsom [etiket](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) og [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), en ordbog, der bruges til at holde programmet egenskaber og helhed af vilkårlig application data. Et program kan indstille brødteksten ved overførsel af serialiserbar objekter (i følgende eksempel sender i et **SalesData** objekt, der repræsenterer salgsdata fra POS-klient), som skal bruge [DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) ordne sekventielt objektet. Du kan også kan en [Stream](https://msdn.microsoft.com/library/azure/system.io.stream.aspx) -objekt leveres.

```
BrokeredMessage bm = new BrokeredMessage(salesData);
bm.Label = "SalesReport";
bm.Properties["StoreName"] = "Redmond";
bm.Properties["MachineID"] = "POS_1";
```

Den nemmeste måde at sende meddelelser til emnet, er at bruge [CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) til at oprette et [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) objekt direkte fra [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) forekomsten.

```
MessageSender sender = factory.CreateMessageSender("DataCollectionTopic");
sender.Send(bm);
```

### <a name="receive-messages-from-a-subscription"></a>Modtage meddelelser fra et abonnement

Svarer til at bruge køer til at modtage meddelelser fra et abonnement, du kan bruge et [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) objekt, du opretter direkte fra [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) ved hjælp af [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx). Du kan bruge en af de to forskellige modtage tilstande (**ReceiveAndDelete** og **PeekLock**), som beskrevet i [oprette programmer, der bruger Service Bus køer](service-bus-create-queues.md).

Bemærk, at når du opretter en [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) for abonnementer, er parameteren *entityPath* i formularen `topicPath/subscriptions/subscriptionName`. Derfor, hvis du vil oprette en [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) for **lager** abonnementet af emnet **DataCollectionTopic** , *entityPath* skal være indstillet til `DataCollectionTopic/subscriptions/Inventory`. Koden vises på følgende måde:

```
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionTopic/subscriptions/Inventory");
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

## <a name="subscription-filters"></a>Abonnement filtre

I dette scenarie er alle meddelelser, som sendes til emnet hidtil, gøres tilgængelige for alle registrerede abonnementer. Vigtige sætningen her stilles"til rådighed." Mens Service Bus abonnementer se alle meddelelser, som sendes til emnet, kan du kopiere kun et undersæt af disse meddelelser til køen virtuelle abonnement. Dette udføres ved hjælp af abonnement *filtre*. Når du opretter et abonnement, kan du angive et filterudtryk i form af et SQL92 typografi prædikat, der fungerer via egenskaberne for meddelelsen, skal både dialogboksen Egenskaber for system (for eksempel [etiket](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx)) og egenskaberne, programmet, som **StoreName** i det forrige eksempel.

Udvikling af scenariet for at illustrere dette, er en anden butik der skal føjes til vores detailsalg scenarie. Salgsdata fra alle POS-klienter fra begge butikker har stadig skal dirigeres til administrationssystem centralt lager, men en store manager ved hjælp af værktøjet dashboard er kun interesseret i store ydeevne. Du kan bruge filtrering for at opnå dette abonnement. Bemærk, at når POS-klienter udgiver meddelelser, de angive egenskaben **StoreName** program i meddelelsesboksen. Givet to butikker, for eksempel **Redmond** og **Seattle**store POS-klienter i Redmond stempel deres salgsdata meddelelser med en **StoreName** lig med **Redmond**, mens Seattle store POS-klienter bruge **StoreName** lig med **Seattle**. Store manager af Redmond store kun vil have vist data fra dens POS-klienter. Systemet ser således ud:

![Tjenesten Bus4](./media/service-bus-create-topics-subscriptions/IC657167.gif)

Hvis du vil konfigurere denne rute, kan du oprette **Dashboard** -abonnement på følgende måde:

```
SqlFilter dashboardFilter = new SqlFilter("StoreName = 'Redmond'");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard", dashboardFilter);
```

Med dette abonnement filter skal kun meddelelser, der har egenskaben **StoreName** , der er angivet til **Redmond** kopieres til den virtuelle kø for **Dashboard** -abonnement. Der er meget mere til filtrering af abonnement, men. Programmer kan have flere filterregler per abonnement ud over muligheden for at ændre egenskaberne for en meddelelse, som den passerer til et abonnement virtuelle kø.

## <a name="summary"></a>Oversigt

Alle årsagerne til at bruge queuing beskrevet i [oprette programmer, der bruger Service Bus køer](service-bus-create-queues.md) gælder også for emner, specifikt:

- Tidsmæssig afkobling – meddelelse producenter og forbrugere behøver ikke at være online på samme tid.

- Indlæse udjævning – spidser i belastning udjævnes af det emne, så lang applikationer skal klargøres til gennemsnitlige belastning i stedet for spidsbelastning.

- Justering af belastning – svarer til en kø, kan du have flere konkurrerende forbrugere lytter på et enkelt abonnement med alle de meddelelser, der er afleveret til kun én af de brugere, og justering af belastning.

- Løs kobling – du kan udvikle SMS netværket uden at påvirke eksisterende slutpunkter; for eksempel tilføje abonnementer eller ændre filtre til et emne for at tillade for nye forbrugere.

## <a name="next-steps"></a>Næste trin

Se [oprette programmer, der bruger Service Bus køer](service-bus-create-queues.md) for oplysninger om, hvordan du bruger køer i scenariet POS detailsalg.