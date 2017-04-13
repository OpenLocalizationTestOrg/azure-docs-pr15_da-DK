<properties 
    pageTitle="Sådan bruges Service Bus køer med Python | Microsoft Azure" 
    description="Lær at bruge Azure Service Bus køer fra Python." 
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
    ms.date="09/21/2016" 
    ms.author="sethm;lmazuel"/>


# <a name="how-to-use-service-bus-queues"></a>Sådan bruger du Service Bus køer

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

I denne artikel beskrives, hvordan du bruger Service Bus køer. Eksemplerne er skrevet i Python og bruge [Python Azure Service Bus pakke][]. De scenarier, der er omfattet omfatter **oprettelse af køer, sende og modtage meddelelser**og **slette køer**.

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

> [AZURE.NOTE] For at installere Python eller [Python Azure Service Bus pakke][]skal du se [Python installationsvejledningen](../python-how-to-install.md).

## <a name="create-a-queue"></a>Oprette en kø

Objektet **ServiceBusService** gør det muligt at arbejde med køer. Føj følgende kode i den øverste del af en Python fil, du ønsker fra et program adgang til tjenesten Bus:

```
from azure.servicebus import ServiceBusService, Message, Queue
```

Følgende kode opretter et **ServiceBusService** objekt. Erstatte `mynamespace`, `sharedaccesskeyname`, og `sharedaccesskey` med din navneområde, delt adgang (SAS) vigtige signaturnavn og værdi.

```
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Værdierne for SAS vigtige navnet og værdien kan findes, i [Azure klassisk portal][] forbindelsesoplysningerne eller i ruden Visual Studio **Egenskaber** når du vælger navneområdet Service Bus i Server Explorer (som vist i det forrige afsnit).

```
bus_service.create_queue('taskqueue')
```

**create_queue** understøtter også flere indstillinger, som gør det muligt at tilsidesætte kø standardindstillingerne som meddelelse tid til live (TTL) eller maksimale kø størrelse. I følgende eksempel angiver den maksimale kø 5GB, og TTL-værdien til 1 minut:

```
queue_options = Queue()
queue_options.max_size_in_megabytes = '5120'
queue_options.default_message_time_to_live = 'PT1M'

bus_service.create_queue('taskqueue', queue_options)
```

## <a name="send-messages-to-a-queue"></a>Sende meddelelser til en kø

At sende en meddelelse til en tjeneste Bus kø, programmet opkald på **sende\_kø\_meddelelse** metode for **ServiceBusService** -objektet.

I følgende eksempel viser, hvordan du kan sende en testmeddelelse til køen med navnet *taskqueue ved hjælp af* **sende\_kø\_meddelelse**:

```
msg = Message(b'Test Message')
bus_service.send_queue_message('taskqueue', msg)
```

Service Bus køer understøtter en maksimale størrelse på 256 KB [Standard niveau](service-bus-premium-messaging.md) og 1 MB i [Premium niveau](service-bus-premium-messaging.md). Hovedet, som indeholder programmet på computeren standardfarver og brugerdefinerede egenskaber, kan have en maksimal størrelse på 64 KB. Der er ingen begrænsninger på antallet af beskeder i en kø, men der er en knop på den samlede størrelse af de meddelelser, som en kø. Denne kø størrelse er defineret på oprettelsestidspunkt med en øvre grænse på 5 GB. Du kan finde flere oplysninger om kvoter, [Service Bus kvoter][].

## <a name="receive-messages-from-a-queue"></a>Modtage meddelelser fra en kø

Når der modtages meddelelser fra en kø ved hjælp af den **modtager\_kø\_meddelelse** metode for **ServiceBusService** -objektet:

```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
print(msg.body)
```

Meddelelser slettes fra køen, som de læses når parameteren **oversigten\_Lås** er indstillet til **Falsk**. Du kan læse (oversigten) og låse meddelelsen uden at slette den fra køen ved at angive parameteren **oversigten\_Lås** til **Sand**.

Funktionsmåden for læsning og slette meddelelsen som en del af modtag-handlingen er den nemmeste model, og fungerer bedst for scenarier, hvor et program kan acceptere ikke behandle en meddelelse i tilfælde af en fejl. For at forstå, bør du overveje et scenarie, hvor forbrugeren udsteder Modtag anmodningen og derefter går ned, før du behandler den. Fordi Service Bus har markeret meddelelsen som bliver brugt, og derefter når programmet genstarter og begynder forbrug meddelelser igen, den gået glip den meddelelse, der blev brugt før går ned.

Hvis den **oversigten\_Lås** parameter er angivet til **Sand**, Modtag bliver en handling af to trin, som gør det muligt for understøttelse af programmer, der ikke kan acceptere manglende meddelelser. Når Service Bus modtager en anmodning, finder den næste meddelelse at blive brugt det, låser den for at forhindre andre forbrugere, modtager den og returnerer den derefter til programmet. Når programmet afsluttes behandling af meddelelsen (eller gemmer den pålideligt til fremtidige behandling), er fuldført i anden fase af Modtag processen ved at ringe til metoden **slette** for objektet **meddelelse** . Metoden **slette** vil markere meddelelsen som der consumed og fjerne den fra køen.

```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sådan håndteres programmet går ned, og ikke kan læses meddelelser

Service Bus indeholder funktioner for at hjælpe dig med at gendanne problemfrit fra fejl i programmet eller problemer behandling af en meddelelse. Hvis en modtager programmet er i stand til at behandle meddelelsen eller anden grund, kan det kalde metoden **låse op** for objektet **meddelelse** . Dette medfører Service Bus at låse op meddelelse i køen og gøre det tilgængeligt igen, der modtages, af det samme arket program eller af en anden arket program.

Der er også en timeout, der er knyttet til en meddelelse, der er låst i køen, og hvis der opstår fejl i programmet til at behandle meddelelsen før låsetimeout udløber (f.eks., hvis programmet går ned), og derefter Service Bus vil låse op meddelelsen automatisk og gøre det tilgængeligt skal modtages igen.

I tilfælde af, at programmet går ned, efter behandling af meddelelsen, men før den **slette** metode kaldes, derefter meddelelsen vil være udleveres igen til programmet når genstartes. Dette kaldes ofte **Mindst når behandling**, det vil sige, hver meddelelse behandles mindst én gang, men i nogle situationer kan være udleveres igen den samme meddelelse. Hvis dette scenario ikke kan acceptere dublerede behandling, bør derefter programmet udviklere tilføje yderligere logik til deres program tilladelse til at håndtere dublerede meddelelseslevering. Dette opnås ofte ved hjælp af egenskaben **MessageId** for e-mailen, der forbliver konstante på tværs af leveringsforsøg.

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært grundlæggende omkring Service Bus køer, skal du følge disse links for at få mere at vide.

-   Se [køer, emner og abonnementer][].

[Azure klassisk portal]: https://manage.windowsazure.com
[Python Azure Service Bus pakke]: https://pypi.python.org/pypi/azure-servicebus  
[Køer, emner og abonnementer]: service-bus-queues-topics-subscriptions.md
[Service Bus kvoter]: service-bus-quotas.md
 
