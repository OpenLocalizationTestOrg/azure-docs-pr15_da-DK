<properties 
    pageTitle="Sådan bruges Service Bus emner med Python | Microsoft Azure" 
    description="Lær at bruge Azure Service Bus emner og abonnementer fra Python." 
    services="service-bus" 
    documentationCenter="python" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Sådan bruges Service Bus emner og abonnementer

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

I denne artikel beskrives, hvordan du bruger Service Bus emner og abonnementer. Eksemplerne er skrevet i Python og bruge [Python Azure pakke][]. De scenarier, der er dækket omfatter **oprettelse af emner og abonnementer**, **oprette abonnement filtre**, **sende meddelelser til et emne**, **modtager meddelelser fra et abonnement**og **slette emner og abonnementer**. Du kan finde flere oplysninger om emner og abonnementer, afsnittet [Næste trin](#next-steps) .

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

**Note:** Hvis du har brug at installere Python eller [Python Azure-pakke][], skal du se [Installationsvejledningen til Python](../python-how-to-install.md).

## <a name="create-a-topic"></a>Oprette et emne

Objektet **ServiceBusService** gør det muligt at arbejde med emner. Tilføj følgende i øverst del af en Python-fil, som du ønsker fra et program adgang til tjenesten Bus:

```
from azure.servicebus import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Følgende kode opretter et **ServiceBusService** objekt. Erstatte `mynamespace`, `sharedaccesskeyname`, og `sharedaccesskey` med dine faktiske navneområdet delt Access signatur (SAS) centrale navn og vigtige værdi.

```
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Du kan hente værdierne for SAS navnet på den nøgle og værdi fra [Azure-portalen][].

```
bus_service.create_topic('mytopic')
```

**oprette\_emne** understøtter også flere indstillinger, som gør det muligt at tilsidesætte emne standardindstillingerne som meddelelse tid til live eller maksimale emne størrelse. I følgende eksempel angiver den maksimale emne 5 GB og et klokkeslæt til live (TTL) værdi af 1 minut:

```
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Oprette abonnementer

Abonnementer til emner oprettes også sammen med objektet **ServiceBusService** . Abonnementer navngives og kan have et valgfrit filter, der begrænser sæt af meddelelser, der er leveret til det abonnement virtuelle kø.

> [AZURE.NOTE] Abonnementer er fast og fortsætter med at findes indtil enten de eller det emne, som de er tilmeldt, slettes.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Oprette et abonnement med standardfilteret (MatchAll)

Filteret **MatchAll** er standardfilteret, der bruges, hvis Intet filter er angivet, når der oprettes et nyt abonnement. Når filteret **MatchAll** bruges, placeres alle meddelelser, der er publiceret på emnet i det abonnement virtuelle kø. I følgende eksempel oprettes et abonnement med navnet 'AllMessages' og bruger **MatchAll** standardfilteret.

```
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>Oprette abonnementer med filtre

Du kan også angive filtre, der gør det muligt at angive, hvilke meddelelser, der sendes til et emne skal vises i et bestemt emne abonnement.

Den mest fleksible type filter, der understøttes af abonnementer er en **SqlFilter**, som anvender et undersæt af SQL92. SQL filtre fungerer på egenskaberne for de meddelelser, der udgives på emnet. Du kan finde flere oplysninger om de udtryk, der kan bruges med en SQL-filter, [SqlFilter.SqlExpression][] syntaksen.

Du kan tilføje filtre til et abonnement via den **oprette\_regel** metode for objektet **ServiceBusService** . Denne metode kan du føje nye filtre til et eksisterende abonnement.

> [AZURE.NOTE] Fordi standardfilteret anvendes automatisk på alle nye abonnementer, skal du først fjerne standardfilteret eller **MatchAll** tilsidesætter andre filtre, som du kan angive. Du kan fjerne standardreglen ved hjælp af den **slette\_regel** metode for objektet **ServiceBusService** .

I følgende eksempel oprettes et abonnement med navnet `HighMessages` med **SqlFilter** , der kun markerer meddelelser, der har en brugerdefineret **messagenumber** egenskab, der er større end 3:

```
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

På samme måde i følgende eksempel oprettes et abonnement med navnet `LowMessages` med **SqlFilter** , der kun markerer meddelelser, der har en **messagenumber** egenskab mindre end eller lig med 3:

```
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Nu, hvor en meddelelse er sendt til `mytopic` det altid er leveret til modtagere, der er oprettet et abonnement på **AllMessages** emne abonnementet, og selektivt leveret til modtagere, der er oprettet et abonnement på **HighMessages** og **LowMessages** emne abonnementer (afhængigt af meddelelsesindhold).

## <a name="send-messages-to-a-topic"></a>Sende meddelelser til et emne

Hvis du vil sende en meddelelse til et emne, Service Bus, dit program skal bruge den **sende\_emne\_meddelelse** metode for objektet **ServiceBusService** .

Følgende eksempel viser, hvordan du kan sende fem test mails til `mytopic`. Bemærk, at værdien af hver meddelelse **messagenumber** egenskaben varierer på gentagelse i feltet Gentag (Dette bestemmer, hvilke abonnementer modtager den):

```
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
    bus_service.send_topic_message('mytopic', msg)
```

Service Bus emner understøtter en maksimale størrelse på 256 KB [Standard niveau](service-bus-premium-messaging.md) og 1 MB i [Premium niveau](service-bus-premium-messaging.md). Hovedet, som indeholder programmet på computeren standardfarver og brugerdefinerede egenskaber, kan have en maksimal størrelse på 64 KB. Der er ingen begrænsninger på antallet af beskeder i et emne, men der er en knop på den samlede størrelse af de meddelelser, som et emne. Dette emne størrelse er defineret på oprettelsestidspunkt med en øvre grænse på 5 GB. Du kan finde flere oplysninger om kvoter, [Service Bus kvoter][].

## <a name="receive-messages-from-a-subscription"></a>Modtage meddelelser fra et abonnement

Når der modtages meddelelser fra et abonnement med den **modtager\_abonnement\_meddelelse** metode for **ServiceBusService** -objektet:

```
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Meddelelser slettes fra abonnementet, som de læses når parameteren **oversigten\_Lås** er indstillet til **Falsk**. Du kan læse (oversigten) og låse meddelelsen uden at slette den fra køen ved at angive parameteren **oversigten\_Lås** til **Sand**.

Funktionsmåden for læsning og slette meddelelsen som en del af modtag-handlingen er den nemmeste model, og fungerer bedst for scenarier, hvor et program kan acceptere ikke behandle en meddelelse i tilfælde af en fejl. For at forstå, bør du overveje et scenarie, hvor forbrugeren udsteder Modtag anmodningen og derefter går ned, før du behandler den. Fordi Service Bus har markeret meddelelsen som bliver brugt, og derefter når programmet genstarter og begynder forbrug meddelelser igen, den gået glip den meddelelse, der blev brugt før går ned.

Hvis den **oversigten\_Lås** parameter er angivet til **Sand**, Modtag bliver en handling af to trin, som gør det muligt for understøttelse af programmer, der ikke kan acceptere manglende meddelelser. Når Service Bus modtager en anmodning, finder den næste meddelelse at blive brugt det, låser den for at forhindre andre forbrugere, modtager den og returnerer den derefter til programmet. Når programmet afsluttes behandling af meddelelsen (eller gemmer den pålideligt til fremtidige behandling), er fuldført i anden fase af Modtag processen ved at ringe **Slet** metode for objektet **meddelelse** . Metoden **slette** markerer meddelelsen som der consumed og sletter den fra abonnementet.

```
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sådan håndteres programmet går ned, og ikke kan læses meddelelser

Service Bus indeholder funktioner for at hjælpe dig med at gendanne problemfrit fra fejl i programmet eller problemer behandling af en meddelelse. Hvis en modtager programmet er i stand til at behandle meddelelsen eller anden grund, kan det kalde metoden **låse op** for objektet **meddelelse** . Dette medfører Service Bus låse op meddelelsen inden for abonnementet, og gøre det tilgængeligt igen, der modtages, af det samme arket program eller af en anden arket program.

Der er også en timeout, der er knyttet til en meddelelse, der er låst i abonnementet, og hvis der opstår fejl i programmet til at behandle meddelelsen før låsetimeout udløber (for eksempel hvis programmet går ned), så Service Bus låser meddelelsen automatisk, og gør det tilgængeligt for modtagne igen.

I tilfælde af, at programmet går ned, efter behandling af meddelelsen, men før den **slette** metode kaldes, derefter meddelelsen vil være udleveres igen til programmet når genstartes. Dette kaldes ofte **Mindst når behandling**, det vil sige, hver meddelelse behandles mindst én gang, men i nogle situationer kan være udleveres igen den samme meddelelse. Hvis dette scenario ikke kan acceptere dublerede behandling, bør derefter programmet udviklere tilføje yderligere logik til deres program tilladelse til at håndtere dublerede meddelelseslevering. Dette opnås ofte ved hjælp af egenskaben **MessageId** for e-mailen, der forbliver konstante på tværs af leveringsforsøg.

## <a name="delete-topics-and-subscriptions"></a>Slette emner og abonnementer

Emner og abonnementer er fast, og skal slettes eksplicit, enten via [Azure portal][] eller fra et program. Følgende eksempel viser, hvordan du sletter det emne, der hedder `mytopic`:

```
bus_service.delete_topic('mytopic')
```

Slette et emne, slettes også eventuelle abonnementer, der er registreret med emnet. Abonnementer kan også blive slettet uafhængigt af hinanden. Følgende kode viser, hvordan du kan slette et abonnement med navnet `HighMessages` fra den `mytopic` emne:

```
bus_service.delete_subscription('mytopic', 'HighMessages')
```

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært grundlæggende omkring Service Bus emner, skal du følge disse links for at få mere at vide.

-   Se [køer, emner og abonnementer][].
-   Reference til [SqlFilter.SqlExpression][].

[Azure-portalen]: https://portal.azure.com
[Python Azure-pakke]: https://pypi.python.org/pypi/azure  
[Køer, emner og abonnementer]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Service Bus kvoter]: service-bus-quotas.md 
