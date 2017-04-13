<properties
    pageTitle="Sådan bruges Service Bus køer med fonetisk | Microsoft Azure"
    description="Lær at bruge Service Bus køer i Azure. Kodeeksempler, der er skrevet i fonetisk."
    services="service-bus"
    documentationCenter="ruby"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-queues"></a>Sådan bruger du Service Bus køer

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Denne vejledning beskriver, hvordan du bruger Service Bus køer. Eksemplerne er skrevet i fonetisk og bruge Azure gem. De scenarier, der er omfattet omfatter **oprettelse af køer, sende og modtage meddelelser**og **slette køer**. Du kan finde flere oplysninger om Service Bus køer, afsnittet [Næste trin](#next-steps) .

## <a name="what-are-service-bus-queues"></a>Hvad er Service Bus køer?

Service Bus køer understøtter en *formidlet messaging* kommunikation model. Med køer Kommuniker komponenterne i et distribueret program ikke direkte med hinanden, dvs. i stedet for exchange de-meddelelser via en kø, der fungerer som mellemliggende. En meddelelse producent (afsenderen) hænder fra en meddelelse til køen og fortsætter derefter dens behandling.
Asynkront, en meddelelse forbruger (modtager) trækker meddelelsen fra køen og behandler den. Producenten har ikke at vente et svar fra forbrugeren for at fortsætte med at behandle og sende meddelelser til yderligere. Køer tilbyder **første i skal du først ud (FIFU)** levering af meddelelser til en eller flere konkurrerende forbrugere. Meddelelser er det vil sige, typisk modtaget og behandles af modtagere i den rækkefølge, hvori de blev føjet til køen, og alle meddelelser er modtaget og behandles som en enkelt meddelelse forbruger.

![QueueConcepts](./media/service-bus-ruby-how-to-use-queues/sb-queues-08.png)

Service Bus køer er en generel teknologi, der kan bruges til en bred vifte af scenarier:

-   Kommunikation mellem internettet og arbejder roller i et [med flere lag Azure-program](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md).
-   Kommunikation mellem lokale apps og Azure hostet apps i en [hybridløsning](service-bus-dotnet-hybrid-app-using-service-bus-relay.md).
-   Kommunikation mellem komponenterne i et distribueret program, der kører lokalt i forskellige organisationer eller afdelinger af en organisation.

Brug af køer kan gør det muligt at skalere dine programmer bedre ud, og aktiver flere fleksibilitet til din arkitektur.

## <a name="create-a-namespace"></a>Oprette et navneområde

Hvis du vil begynde at bruge Service Bus køer i Azure, skal du først oprette et navneområde. Et navneområde indeholder en angiver området objektbeholder til at håndtere Service Bus ressourcer i dit program. Fordi portalen Azure ikke oprette navneområdet med forbindelse til ACS, skal du oprette navneområdet via kommandolinjen.

Sådan oprettes et navneområde:

1. Åbn en Azure Powershell console.

2. Skriv følgende kommando for at oprette et Service Bus navneområde. Angiv din egen navneområde værdi og angive det samme område som dit program.

    ```
    New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true

    ![Create Namespace](./media/service-bus-ruby-how-to-use-queues/showcmdcreate.png)
    ```

## <a name="obtain-management-credentials-for-the-namespace"></a>Få management legitimationsoplysninger for navneområdet

For at udføre handlinger i administration, som opretter en kø på det nye navneområde, skal du anskaffe management legitimationsoplysninger for navneområdet.

PowerShell cmdlet du kørte for at oprette navneområdet Azure service bus viser den nøgle, du kan bruge til at administrere navneområdet. Kopiér værdien **DefaultKey** . Du kan bruge denne værdi i din kode senere i dette selvstudium.

![Kopiere nøgle](./media/service-bus-ruby-how-to-use-queues/defaultkey.png)

> [AZURE.NOTE] Du kan også finde denne nøgle, hvis du logger på [Azure portal](https://portal.azure.com/) og gå til forbindelsesoplysningerne for din Service Bus navneområde.

## <a name="create-a-ruby-application"></a>Oprette et fonetisk tjenesteprogram

Oprette et fonetisk program. Flere oplysninger under [oprette et fonetisk program på Azure](/develop/ruby/tutorials/web-app-with-linux-vm/).

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurere dit program bruge Service Bus

Hvis du vil bruge Azure Service Bus, hente og bruge fonetisk Azure-pakke, som indeholder et sæt af nemmere biblioteker, kommunikerer med lagerplads RESTEN tjenester.

### <a name="use-rubygems-to-obtain-the-package"></a>Brug RubyGems til at hente pakken

1. Brug en kommandolinjeparametre som **PowerShell** (Windows), **Terminal** (Mac) eller **Bash** (Unix).

2. Skriv "gem Installer azure" i kommandovinduet til at installere gem og afhængigheder.

### <a name="import-the-package"></a>Importere pakken

Bruger din foretrukne teksteditor, skal du tilføje følgende til toppen af den fonetisk fil, hvor du vil bruge lagerplads:

```
require "azure"
```

## <a name="set-up-an-azure-service-bus-connection"></a>Konfigurere en forbindelse til Azure Service Bus

Modulet Azure læser miljøvariabler **AZURE\_SERVICEBUS\_NAVNEOMRÅDE** og **AZURE\_SERVICEBUS\_ACCESS_KEY** oplysninger, der kræves for at oprette forbindelse til din Service Bus navneområde. Hvis variablerne miljø ikke er angivet, skal du angive navneområde oplysningerne inden du bruger **Azure::ServiceBusService** med følgende kode:

```
Azure.config.sb_namespace = "<your azure service bus namespace>"
Azure.config.sb_access_key = "<your azure service bus access key>"
```

Angive værdien navneområde til den værdi, du oprettede, i stedet for hele URL-adressen. For eksempel bruge **"yourexamplenamespace"**, ikke "yourexamplenamespace.servicebus.windows.net".

## <a name="how-to-create-a-queue"></a>Sådan oprettes en kø

Objektet **Azure::ServiceBusService** gør det muligt at arbejde med køer. Hvis du vil oprette en kø, skal du bruge metoden **create_queue()** . I følgende eksempel oprettes en kø eller udskriver eventuelle fejl.

```
azure_service_bus_service = Azure::ServiceBusService.new
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Du kan også sende en **Azure::ServiceBus::Queue** objekt med flere indstillinger, som gør det muligt at tilsidesætte standardindstillingerne i kø, som meddelelsen tid til live eller maksimale kø størrelse. I følgende eksempel viser, hvordan du angiver den maksimale kø størrelse til 5GB og tid til live til 1 minut:

```
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Hvordan du kan sende meddelelser til en kø

Sende en meddelelse til en tjeneste Bus kø, du programmet opkald på **sende\_kø\_message()** metode for **Azure::ServiceBusService** -objektet. Meddelelser, der er sendt til (og modtaget fra) Service Bus køer er **Azure::ServiceBus::BrokeredMessage** objekter, og har et sæt standardegenskaber (såsom **etiket** og **tid\_til\_live**), en ordbog, der bruges til at holde brugerdefineret program specifikke egenskaber og en brødteksten i vilkårlig programmet data. Et program kan indstille brødteksten i meddelelsen ved at overføre en strengværdi som meddelelsen, og eventuelle nødvendige standardegenskaber udfyldes med standardværdier.

I følgende eksempel viser, hvordan du kan sende en testmeddelelse til køen med navnet "test-kø" ved hjælp af **sende\_kø\_message()**:

```
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Service Bus køer understøtter en maksimale størrelse på 256 KB [Standard niveau](service-bus-premium-messaging.md) og 1 MB i [Premium niveau](service-bus-premium-messaging.md). Hovedet, som indeholder programmet på computeren standardfarver og brugerdefinerede egenskaber, kan have en maksimal størrelse på 64 KB. Der er ingen begrænsninger på antallet af beskeder i en kø, men der er en knop på den samlede størrelse af de meddelelser, som en kø. Denne kø størrelse er defineret på oprettelsestidspunkt med en øvre grænse på 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Hvordan du kan modtage meddelelser fra en kø

Når der modtages meddelelser fra en kø ved hjælp af den **modtager\_kø\_message()** metode for **Azure::ServiceBusService** -objektet. Som standard meddelelser læses og låst uden at blive slettet fra køen. Men du kan slette meddelelser fra køen, som de læses ved at angive den **: peek_lock** indstilling til **Falsk**.

Standardfunktionen gør tekstens læseretning og sletning af en handling af to faser, som gør det også muligt at understøtte programmer, der ikke kan acceptere manglende meddelelser. Når Service Bus modtager en anmodning, finder den næste meddelelse at blive brugt det, låser den for at forhindre andre forbrugere, modtager den og returnerer den derefter til programmet. Når programmet afsluttes behandling af meddelelsen (eller gemmer den pålideligt til fremtidige behandling), det afsluttes i anden fase af Modtag processen ved at ringe til **slette\_kø\_message()** metode og give meddelelsen, der skal slettes som en parameter. Den **slette\_kø\_message()** metode vil markere meddelelsen som der consumed og fjerne den fra køen.

Hvis den **: oversigten\_Lås** parameter er indstillet til **Falsk**, læse og slette meddelelsen bliver nemmeste modellen og egner sig bedst til scenarier, hvor et program kan acceptere ikke behandle en meddelelse i tilfælde af en fejl. For at forstå, bør du overveje et scenarie, hvor forbrugeren udsteder Modtag anmodningen og derefter går ned, før du behandler den. Fordi Service Bus har markeret meddelelsen som bliver brugt, når programmet genstarter og begynder forbrug meddelelser igen, vil gået glip, den meddelelse, der blev brugt før går ned.

Følgende eksempel viser, hvordan du kan modtage og behandle meddelelser ved hjælp af **modtager\_kø\_message()**. Eksemplet først modtager og sletter en meddelelse ved hjælp af **: oversigten\_Lås** indstillet til **Falsk**, og klik derefter den modtager en ny meddelelse og sletter meddelelse ved hjælp af **slette\_kø\_message()**:

```
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sådan håndteres programmet går ned, og ikke kan læses meddelelser

Service Bus indeholder funktioner for at hjælpe dig med at gendanne problemfrit fra fejl i programmet eller problemer behandling af en meddelelse. Hvis en modtager programmet kan ikke behandle meddelelsen eller anden grund, er det kan ringe til den **låse op\_kø\_message()** metode for **Azure::ServiceBusService** -objektet. Derved Service Bus at låse op meddelelse i køen og gøre det tilgængeligt igen, der modtages, af det samme arket program eller af en anden arket program.

Der er også en timeout, der er knyttet til en meddelelse, der er låst i køen, og hvis der opstår fejl i programmet til at behandle meddelelsen før låsetimeout udløber (for eksempel hvis programmet går ned), så Service Bus låser meddelelsen automatisk, og gør det tilgængeligt for modtagne igen.

I tilfælde af, at programmet går ned efter behandling af meddelelsen men før den **slette\_kø\_message()** metode kaldes, og klik derefter meddelelsen vil være udleveres til programmet, når den genstartes igen. Dette kaldes ofte **Mindst én gang behandling**; Det vil sige, hver meddelelse behandles mindst én gang, men i nogle situationer kan være udleveres igen den samme meddelelse. Hvis dette scenario ikke kan acceptere dublerede behandling, bør derefter programmet udviklere tilføje yderligere logik til deres program tilladelse til at håndtere dublerede meddelelseslevering. Dette opnås ofte ved hjælp af den **meddelelse\_id** egenskab for e-mailen, der konstant på tværs af leveringsforsøg.

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært grundlæggende omkring Service Bus køer, skal du følge disse links for at få mere at vide.

-   Oversigt over [køer, emner og abonnementer](service-bus-queues-topics-subscriptions.md).
-   Besøg [Azure SDK til fonetisk](https://github.com/Azure/azure-sdk-for-ruby) lager på GitHub.

En sammenligning mellem Azure Service Bus køerne beskrevet i denne artikel og Azure køer gennemgås i artiklen [Sådan bruger du kø lagerplads fra fonetisk](../storage/storage-ruby-how-to-use-queue-storage.md) i [Azure køer og Azure Service Bus køer - sammenlignet og Contrasted](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
 
