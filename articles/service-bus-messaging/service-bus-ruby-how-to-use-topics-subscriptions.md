<properties
    pageTitle="Sådan bruger du Service Bus emner (fonetisk) | Microsoft Azure"
    description="Lær at bruge Service Bus emner og abonnementer i Azure. Kodeeksempler, der er skrevet til fonetisk programmer."
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

# <a name="how-to-use-service-bus-topicssubscriptions"></a>Sådan bruger du Service Bus emner/abonnementer

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

I denne artikel beskrives, hvordan du bruger Service Bus emner og abonnementer fra fonetisk programmer. De scenarier, der er omfattet omfatter **oprettelse af emner og abonnementer, du opretter abonnement filtre, sende meddelelser** til et emne, **modtager meddelelser fra et abonnement**og **slette emner og abonnementer**. Du kan finde flere oplysninger om emner og abonnementer, afsnittet [Næste trin](#next-steps) .

## <a name="service-bus-topics-and-subscriptions"></a>Service Bus emner og abonnementer

Service Bus emner og abonnementer understøtter en *udgivelse/abonnement* messaging kommunikation model. Når du bruger emner og abonnementer, Kommuniker komponenterne i et distribueret program ikke direkte med hinanden, dvs. de i stedet udveksle meddelelser via et emne, der fungerer som mellemliggende.

![TopicConcepts](./media/service-bus-ruby-how-to-use-topics-subscriptions/sb-topics-01.png)

I modsætning til Service Bus køer, hvor meddelelserne behandles som en enkelt forbruger, giver emner og abonnementer en **en-til-mange** form for kommunikation, ved hjælp af en Udgiv/Abonner mønster. Det er muligt at registrere flere abonnementer til et emne. Når en meddelelse er sendt til et emne, er det derefter gøres tilgængelige for hvert abonnement til at behandle uafhængigt af hinanden.

Et emne abonnement ligner en virtuel kø, der modtager kopier af de meddelelser, der blev sendt til emnet. Du kan også kan du registrere filterregler for et emne på grundlag abonnement, som gør det muligt at filtrere/Begræns hvilke meddelelser der skal et emne modtages af abonnementer, emne.

Service Bus emner og abonnementer gør det muligt at skalere til at behandle et stort antal meddelelser på tværs af et stort antal brugere og programmer.

## <a name="create-a-namespace"></a>Oprette et navneområde

Hvis du vil begynde at bruge Service Bus køer i Azure, skal du først oprette et navneområde. Et navneområde indeholder en angiver området objektbeholder til at håndtere Service Bus ressourcer i dit program. Fordi [Azure portal][] ikke oprette navneområdet med en ACS dataforbindelse, skal du oprette navneområdet via kommandolinjen.

Sådan oprettes et navneområde:

1. Åbn et Azure Powershell console-vindue.

2. Skriv følgende kommando for at oprette et navneområde. Angiv din egen navneområde værdi og angive det samme område som dit program.

    ```
    New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true
    ```

    ![Oprette Namespace](./media/service-bus-ruby-how-to-use-topics-subscriptions/showcmdcreate.png)

## <a name="obtain-default-management-credentials-for-the-namespace"></a>Få standard management legitimationsoplysninger for navneområdet

For at udføre handlinger i administration, som opretter en kø på det nye navneområde, skal du anskaffe management legitimationsoplysninger for navneområdet.

PowerShell cmdlet du kørte for at oprette navneområdet Service Bus viser den nøgle, du kan bruge til at administrere navneområdet. Kopiér værdien **DefaultKey** . Du kan bruge denne værdi i din kode senere i dette selvstudium.

![Kopiere nøgle](./media/service-bus-ruby-how-to-use-topics-subscriptions/defaultkey.png)

> [AZURE.NOTE]
> Du kan også finde denne nøgle, hvis du logger på [Azure portal][] og gå til forbindelsesoplysningerne for din navneområde.

## <a name="create-a-ruby-application"></a>Oprette et fonetisk tjenesteprogram

Flere oplysninger under [oprette et fonetisk program på Azure](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md).

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurere dit program til brug Service Bus

Hvis du vil bruge Service Bus, hente og bruge fonetisk Azure-pakke, som indeholder et sæt af nemmere biblioteker, kommunikerer med lagerplads RESTEN tjenester.

### <a name="use-rubygems-to-obtain-the-package"></a>Brug RubyGems til at hente pakken

1. Brug en kommandolinjeparametre som **PowerShell** (Windows), **Terminal** (Mac) eller **Bash** (Unix).

2. Skriv "gem Installer azure" i kommandovinduet til at installere gem og afhængigheder.

### <a name="import-the-package"></a>Importere pakken

Bruger din foretrukne teksteditor, skal du tilføje følgende til toppen af filen fonetisk, hvor du vil bruge lagerplads:

```
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Konfigurere en forbindelse Service Bus

Modulet Azure læser miljøvariabler **AZURE\_SERVICEBUS\_NAVNEOMRÅDE** og **AZURE\_SERVICEBUS\_ACCESS\_nøgle** oplysninger, der kræves for at oprette forbindelse til din navneområde. Hvis variablerne miljø ikke er angivet, skal du angive navneområde oplysningerne inden du bruger **Azure::ServiceBusService** med følgende kode:

```
Azure.config.sb_namespace = "<your azure service bus namespace>"
Azure.config.sb_access_key = "<your azure service bus access key>"
```

Angive værdien navneområde til den værdi, du har oprettet i stedet for hele URL-adressen. For eksempel bruge **"yourexamplenamespace"**, ikke "yourexamplenamespace.servicebus.windows.net".

## <a name="create-a-topic"></a>Oprette et emne

Objektet **Azure::ServiceBusService** gør det muligt at arbejde med emner. Følgende kode opretter et **Azure::ServiceBusService** objekt. Hvis du vil oprette et emne, skal du bruge den **oprette\_topic()** metode. I følgende eksempel oprettes et emne eller udskriver fejlene, hvis der ikke er nogen.

```
azure_service_bus_service = Azure::ServiceBusService.new
begin
  topic = azure_service_bus_service.create_queue("test-topic")
rescue
  puts $!
end
```

Du kan også sende en **Azure::ServiceBus::Topic** objekt med flere indstillinger, som gør det muligt at tilsidesætte standardindstillingerne emne for som meddelelse tid til live eller maksimale kø størrelse. I følgende eksempel viser angiver den maksimale kø størrelse til 5GB og tid til live til 1 minut:

```
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Oprette abonnementer

Emne abonnementer oprettes også sammen med objektet **Azure::ServiceBusService** . Abonnementer navngives og kan have et valgfrit filter, der begrænser sæt af meddelelser, der er leveret til det abonnement virtuelle kø.

Abonnementer er fast og fortsætter med at findes indtil enten de eller emnet de er forbundet med, slettes. Hvis dit program indeholder logik for at oprette et abonnement, skal den først Kontrollér, om abonnementet, der allerede findes ved hjælp af metoden getSubscription.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Oprette et abonnement med standardfilteret (MatchAll)

Filteret **MatchAll** er standardfilteret, der bruges, hvis Intet filter er angivet, når der oprettes et nyt abonnement. Når filteret **MatchAll** bruges, placeres alle meddelelser, der er publiceret på emnet i det abonnement virtuelle kø. I følgende eksempel oprettes et abonnement med navnet "alle meddelelser" og bruger **MatchAll** standardfilteret.

```
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Oprette abonnementer med filtre

Du kan også angive filtre, der gør det muligt at angive, hvilke meddelelser, der sendes til et emne skal vises i et bestemt abonnement.

Den mest fleksible type filter, der understøttes af abonnementer er **Azure::ServiceBus::SqlFilter**, som anvender et undersæt af SQL92. SQL filtre fungerer på egenskaberne for de meddelelser, der udgives på emnet. Gennemse [SqlFilter.SqlExpression](http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx) syntaksen for flere oplysninger om de udtryk, der kan bruges med en SQL-filter.

Du kan tilføje filtre til et abonnement via den **oprette\_rule()** metode for objektet **Azure::ServiceBusService** . Denne metode kan du tilføje nye filtre til et eksisterende abonnement.

Da standardfilteret anvendes automatisk på alle nye abonnementer, skal du først fjerne standardfilteret, eller **MatchAll** tilsidesætter andre filtre, som du kan angive. Du kan fjerne standardreglen ved hjælp af den **slette\_rule()** metode for **Azure::ServiceBusService** -objektet.

I følgende eksempel oprettes et abonnement med navnet "høj-meddelelser" med en **Azure::ServiceBus::SqlFilter** , der kun markerer meddelelser, der har en brugerdefineret **meddelelse\_tal** egenskab, der er større end 3:

```
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

På samme måde oprettes i følgende eksempel et abonnement med navnet "lav-meddelelser" med en **Azure::ServiceBus::SqlFilter** , der kun markerer meddelelser, der har en **message_number** egenskab mindre end eller lig med 3:

```
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Når en meddelelse sendes nu til "test-emne", er det altid leveret til modtagere, der er oprettet et abonnement til "alle meddelelser" emne abonnementet, og selektivt leveret til modtagere, der er oprettet et abonnement til "høj meddelelser" og "lav meddelelser" emne abonnementerne (afhængigt af meddelelsesindhold).

## <a name="send-messages-to-a-topic"></a>Sende meddelelser til et emne

Hvis du vil sende en meddelelse til et emne, Service Bus, dit program skal bruge den **sende\_emne\_message()** metode for **Azure::ServiceBusService** -objektet. Meddelelser, der sendes til Service Bus emner er forekomster af **Azure::ServiceBus::BrokeredMessage** objekter. **Azure::ServiceBus::BrokeredMessage** objekter har en gruppe af standardegenskaber (såsom **etiket** og **tid\_til\_live**), en ordbog, der bruges til at holde brugerdefineret program specifikke egenskaber og en brødteksten i strengdata. Et program kan indstille brødteksten i meddelelsen ved at overføre en strengværdi til den **sende\_emne\_message()** metode og eventuelle nødvendige standardegenskaber udfyldes ved standardværdier.

Følgende eksempel viser, hvordan du kan sende fem test mails til "test-emne". Bemærk, at den brugerdefinerede egenskabsværdi **message_number** af hver meddelelse varierer på gentagelse i feltet Gentag (Dette afgør, hvilket abonnement modtager den):

```
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Service Bus emner understøtter en maksimale størrelse på 256 KB [Standard niveau](service-bus-premium-messaging.md) og 1 MB i [Premium niveau](service-bus-premium-messaging.md). Hovedet, som indeholder programmet på computeren standardfarver og brugerdefinerede egenskaber, kan have en maksimal størrelse på 64 KB. Der er ingen begrænsninger på antallet af beskeder i et emne, men der er en knop på den samlede størrelse af de meddelelser, som et emne. Dette emne størrelse er defineret på oprettelsestidspunkt med en øvre grænse på 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Modtage meddelelser fra et abonnement

Når der modtages meddelelser fra et abonnement med den **modtager\_abonnement\_message()** metode for **Azure::ServiceBusService** -objektet. Som standard meddelelser read(peak) og låst uden at slette den fra abonnementet. Du kan læse og slette beskeden fra abonnementet ved at angive den **oversigten\_Lås** indstilling til **Falsk**.

Standardfunktionsmåden gør tekstens læseretning og sletning af en handling af to faser, som gør det også muligt at understøtte programmer, der ikke kan acceptere manglende meddelelser. Når Service Bus modtager en anmodning, finder den næste meddelelse at blive brugt det, låser den for at forhindre andre forbrugere, modtager den og returnerer den derefter til programmet. Når programmet afsluttes behandling af meddelelsen (eller gemmer den pålideligt til fremtidige behandling), det afsluttes i anden fase af Modtag processen ved at ringe til **slette\_abonnement\_message()** metode og give meddelelsen, der skal slettes som en parameter. Den **slette\_abonnement\_message()** metode vil markere meddelelsen som der consumed og fjerne det fra abonnementet.

Hvis den **: oversigten\_Lås** parameter er indstillet til **Falsk**, læse og slette meddelelsen bliver nemmeste modellen og egner sig bedst til scenarier, hvor et program kan acceptere ikke behandle en meddelelse i tilfælde af en fejl. For at forstå, bør du overveje et scenarie, hvor forbrugeren udsteder Modtag anmodningen og derefter går ned, før du behandler den. Fordi Service Bus har markeret meddelelsen som bliver brugt, og derefter når programmet genstarter og begynder forbrug meddelelser igen, den gået glip den meddelelse, der blev brugt før går ned.

Følgende eksempel viser, hvordan meddelelser kan modtages og behandlede ved hjælp af **modtager\_abonnement\_message()**. Eksemplet først modtager og sletter en meddelelse fra abonnementets "lav meddelelser" ved hjælp af **: oversigten\_Lås** indstillet til **Falsk**, og klik derefter den modtager en anden meddelelse fra "meddelelserne høj" og derefter sletter meddelelse ved hjælp af **slette\_abonnement\_message()**:

```
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Håndtere programmet går ned, og ikke kan læses meddelelser

Service Bus indeholder funktioner for at hjælpe dig med at gendanne problemfrit fra fejl i programmet eller problemer behandling af en meddelelse. Hvis en modtager programmet kan ikke behandle meddelelsen eller anden grund, er det kan ringe til den **låse op\_abonnement\_message()** metode for **Azure::ServiceBusService** -objektet. Derved Service Bus låse op meddelelsen inden for abonnementet, og gøre det tilgængeligt igen, der modtages, af det samme arket program eller af en anden arket program.

Der er også en timeout, der er knyttet til en meddelelse, der er låst i abonnementet, og hvis der opstår fejl i programmet til at behandle meddelelsen før låsetimeout udløber (for eksempel hvis programmet går ned), og derefter Service Bus vil låse op meddelelsen automatisk og gøre det tilgængeligt skal modtages igen.

I tilfælde af, at programmet går ned efter behandling af meddelelsen men før den **slette\_abonnement\_message()** metode kaldes, og meddelelsen er udleveres igen til programmet, når den genstarter. Dette kaldes ofte **Mindst én gang behandling**; Det vil sige, hver meddelelse behandles mindst én gang, men i nogle situationer kan være udleveres igen den samme meddelelse. Hvis dette scenario ikke kan acceptere dublerede behandling, bør derefter programmet udviklere tilføje yderligere logik til deres program tilladelse til at håndtere dublerede meddelelseslevering. Denne logik opnås ofte ved hjælp af den **meddelelse\_id** egenskab for e-mailen, der forbliver konstante på tværs af leveringsforsøg.

## <a name="delete-topics-and-subscriptions"></a>Slette emner og abonnementer

Emner og abonnementer er fast, og skal slettes eksplicit, enten via [Azure portal][] eller fra et program. I eksemplet nedenfor viser, hvordan slette emnet med navnet "test-emne".

```
azure_service_bus_service.delete_topic("test-topic")
```

Slette et emne, slettes også eventuelle abonnementer, der er registreret med emnet. Abonnementer kan også blive slettet uafhængigt af hinanden. Følgende kode demonstrerer, hvordan du kan slette abonnementet med navnet "høj meddelelser" fra "test-emne" emne:

```
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært grundlæggende omkring Service Bus emner, skal du følge disse links for at få mere at vide.

- Se [køer, emner og abonnementer](service-bus-queues-topics-subscriptions.md).
- API reference til [SqlFilter](http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx).
- Besøg [Azure SDK til fonetisk](https://github.com/Azure/azure-sdk-for-ruby) lager på GitHub.
 
[Azure-portalen]: https://portal.azure.com
