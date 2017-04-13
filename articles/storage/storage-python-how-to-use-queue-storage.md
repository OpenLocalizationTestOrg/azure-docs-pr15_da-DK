<properties
    pageTitle="Sådan bruger du kø lagerplads fra Python | Microsoft Azure"
    description="Lær, hvordan du bruger tjenesten Azure kø fra Python til at oprette og slette køer, og Indsæt, hente og slette meddelelser."
    services="storage"
    documentationCenter="python"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-queue-storage-from-python"></a>Sådan bruger du kø lagerplads fra Python

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Oversigt

Denne vejledning viser, hvordan du udfører almindelige scenarier ved hjælp af tjenesten Azure kø lagerplads. Eksemplerne er skrevet i Python og bruge [Microsoft Azure lagerplads SDK for Python]. De scenarier, der er omfattet omfatter **indsættelse**, **visning**, **hente**og **slette** beskeder i kø samt **oprettelse og sletning af køer**. Se afsnittet [næste trin] kan finde flere oplysninger om køer.

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="how-to-create-a-queue"></a>Sådan: Oprette en kø

Objektet **QueueService** kan du arbejde med køer. Følgende kode opretter et **QueueService** objekt. Tilføj følgende i øverst del af en Python-fil, som du ønsker fra et program adgang til Azure-lager:

    from azure.storage.queue import QueueService

Følgende kode opretter et **QueueService** objekt ved hjælp af lagerplads konto navn og konto nøgle. Erstat 'min konto' og 'mykey' med dit kontonavn og din nøgle.

    queue_service = QueueService(account_name='myaccount', account_key='mykey')

    queue_service.create_queue('taskqueue')


## <a name="how-to-insert-a-message-into-a-queue"></a>Sådan: Indsætte en meddelelse i en kø

Hvis du vil indsætte en meddelelse i en kø, skal du bruge den **placere\_meddelelse** metode til at oprette en ny meddelelse og tildele den til køen.

    queue_service.put_message('taskqueue', u'Hello World')


## <a name="how-to-peek-at-the-next-message"></a>Sådan: Kig på den næste meddelelse

Du kan vise meddelelsen foran en kø uden at fjerne den fra køen ved at ringe på **oversigten\_meddelelser** metode. Som standard **oversigten\_meddelelser** peeks på en enkelt meddelelse.

    messages = queue_service.peek_messages('taskqueue')
    for message in messages:
        print(message.content)


## <a name="how-to-dequeue-messages"></a>Sådan: Annullering i kø meddelelser

Din kode fjerner en meddelelse fra en kø i to trin. Når du kalder **få\_meddelelser**, du får den næste meddelelse i en kø som standard. En meddelelse, der returneres fra **få\_meddelelser** ikke er synligt for andre kode, der læser meddelelser fra denne kø. Som standard forbliver denne meddelelse usynlige i 30 sekunder. For at afslutte fjerner meddelelsen fra køen, skal du også kontakte **slette\_meddelelse**. Denne proces for at fjerne en meddelelse sikrer, at når din kode ikke at behandle en meddelelse på grund af hardware og software fejl, kan en anden forekomst af din kode få den samme meddelelse og prøve igen. Dine kode opkald **slette\_meddelelse** højre når meddelelsen er blevet behandlet.

    messages = queue_service.get_messages('taskqueue')
    for message in messages:
        print(message.content)
        queue_service.delete_message('taskqueue', message.id, message.pop_receipt)

Der er to måder, du kan tilpasse hentning af meddelelse fra en kø.
Først, kan du få en mængde meddelelser (op til 32). Andet, kan du angive timeout for en længere eller kortere invisibility, så din kode, mere eller mindre tid til at behandle fuldt hver meddelelse. Følgende kode eksempler på Brug den **få\_meddelelser** metode til at få 16 meddelelser i et opkald. Vælg derefter det behandler hver meddelelse ved hjælp af et for-løkke. Invisibility timeouten angives også til fem minutter for hver meddelelse.

    messages = queue_service.get_messages('taskqueue', num_messages=16, visibility_timeout=5*60)
    for message in messages:
        print(message.content)
        queue_service.delete_message('taskqueue', message.id, message.pop_receipt)      


## <a name="how-to-change-the-contents-of-a-queued-message"></a>Sådan: Ændre indholdet af en meddelelse i køen

Du kan ændre indholdet af en meddelelse direkte i køen. Hvis meddelelsen repræsenterer en arbejdsopgave, kan du bruge denne funktion til at opdatere status for arbejdsopgaven. Koden under bruger den **opdatere\_meddelelse** metode til at opdatere en meddelelse. Synlighed timeout er angivet til 0, hvilket betyder, at meddelelsen vises med det samme og indholdet opdateres.

    messages = queue_service.get_messages('taskqueue')
    for message in messages:
        queue_service.update_message('taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')

## <a name="how-to-get-the-queue-length"></a>Sådan: Få længde kø

Du kan få en estimering af antallet af beskeder i en kø. Den **få\_kø\_metadata** metode beder om tjenesten kø til at returnere metadata om køen og **approximate_message_count**. Resultatet er kun tilnærmet, fordi meddelelser kan tilføjes eller fjernes, når tjenesten kø reagerer på din anmodning.

    metadata = queue_service.get_queue_metadata('taskqueue')
    count = metadata.approximate_message_count

## <a name="how-to-delete-a-queue"></a>Sådan: Slette en kø

Hvis du vil slette en kø og alle de meddelelser, der er indeholdt i den, skal du ringe på **slette\_kø** metode.

    queue_service.delete_queue('taskqueue')

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært grundlæggende kø lagerplads, skal du følge disse links for at få mere at vide.

- [Python Developer Center](/develop/python/)
- [Azure-lager Services REST-API](http://msdn.microsoft.com/library/azure/dd179355)
- [Azure-lager-teamets Blog]
- [Microsoft Azure-lager SDK for Python]

[Azure-lager-teamets Blog]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure-lager SDK for Python]: https://github.com/Azure/azure-storage-python
