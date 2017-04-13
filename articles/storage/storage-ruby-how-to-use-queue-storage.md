<properties 
    pageTitle="Sådan bruger du kø lagerplads fra fonetisk | Microsoft Azure" 
    description="Lær, hvordan du bruger tjenesten Azure kø til at oprette og slette køer, og Indsæt, hente og slette meddelelser. Eksempler, der er skrevet i fonetisk." 
    services="storage" 
    documentationCenter="ruby" 
    authors="robinsh" 
    manager="carmonm" 
    editor=""/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ruby" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="robinsh"/>


# <a name="how-to-use-queue-storage-from-ruby"></a>Sådan bruger du kø lagerplads fra fonetisk

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Oversigt

Denne vejledning viser, hvordan du udfører almindelige scenarier ved hjælp af tjenesten Microsoft Azure kø lager. Eksemplerne skrives ved hjælp af fonetisk Azure API.
De scenarier, der er omfattet omfatter **indsættelse**, **visning**, **hente**og **slette** beskeder i kø samt **oprettelse og sletning af køer**.

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Oprette et fonetisk tjenesteprogram

Oprette et fonetisk program. Flere oplysninger under [fonetisk på gør webprogram på en Azure VM](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md).

## <a name="configure-your-application-to-access-storage"></a>Konfigurere dit program til Access-lager

Hvis du vil bruge Azure lagerplads, skal du hente og bruge den fonetisk azure pakke, som indeholder et sæt af nemmere biblioteker, kommunikerer med lagerplads RESTEN tjenester.

### <a name="use-rubygems-to-obtain-the-package"></a>Brug RubyGems til at hente pakken

1. Brug en kommandolinjeparametre som **PowerShell** (Windows), **Terminal** (Mac) eller **Bash** (Unix).

2. Skriv "gem Installer azure" i kommandovinduet til at installere gem og afhængigheder.

### <a name="import-the-package"></a>Importere pakken

Brug din foretrukne teksteditor, skal du tilføje følgende til toppen af den fonetisk fil, hvor du vil bruge lagerplads:

    require "azure"

## <a name="setup-an-azure-storage-connection"></a>Konfiguration af forbindelse til Azure-lager

Modulet azure læser miljøvariabler **AZURE\_LAGERPLADS\_konto** og **AZURE\_LAGERPLADS\_ACCESS_KEY** oplysninger, der kræves for at oprette forbindelse til kontoen Azure-lager. Hvis variablerne miljø ikke er angivet, skal du angive kontooplysninger inden du bruger **Azure::QueueService** med følgende kode:

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your Azure storage access key>"

 
Sådan hentes disse værdier fra en klassisk eller Ressourcestyring lagerplads konto i portalen Azure:

1. Log på [Azure-portalen](https://portal.azure.com).
2. Gå til lagerplads-konto, du vil bruge.
3. Klik på **Access-taster**i bladet indstillinger til højre.
4. I den blade Access taster, der vises, får du vist hurtigtast 1 og hurtigtast 2. Du kan bruge en af disse. 
5. Klik på Kopiér for at kopiere nøglen til Udklipsholder. 

Sådan hentes disse værdier fra en klassisk lagerplads konto i klassisk Azure-portalen:

1. Log på [Klassisk Azure portal](https://manage.windowsazure.com).
2. Naviger til kontoen lagerplads, du vil bruge.
3. Klik på **ADMINISTRER ADGANGSTASTER** nederst i navigationsruden.
4. I pop op-dialogboksen, får du vist kontonavn lager, primære hurtigtast og sekundær hurtigtast. Du kan bruge den primære liste eller et sekundær for hurtigtast. 
5. Klik på Kopiér for at kopiere nøglen til Udklipsholder.

## <a name="how-to-create-a-queue"></a>Sådan: Oprette en kø

Følgende kode opretter et **Azure::QueueService** objekt, der gør det muligt at arbejde med køer.

    azure_queue_service = Azure::QueueService.new

Du kan bruge metoden **create_queue()** til at oprette en kø med det angivne navn.

    begin
      azure_queue_service.create_queue("test-queue")
    rescue
      puts $!
    end

## <a name="how-to-insert-a-message-into-a-queue"></a>Sådan: Indsætte en meddelelse i en kø

Hvis du vil indsætte en meddelelse i en kø, skal du bruge metoden **create_message()** til at oprette en ny meddelelse og føje det til køen.

    azure_queue_service.create_message("test-queue", "test message")

## <a name="how-to-peek-at-the-next-message"></a>Sådan: Kig på den næste meddelelse

Du kan vise meddelelsen foran en kø uden at fjerne den fra køen ved at ringe på **oversigten\_messages()** metode. Som standard **oversigten\_messages()** peeks på en enkelt meddelelse. Du kan også angive, hvor mange meddelelser, du vil Kig.

    result = azure_queue_service.peek_messages("test-queue",
      {:number_of_messages => 10})

## <a name="how-to-dequeue-the-next-message"></a>Sådan: Annullering i kø den næste meddelelse

Du kan fjerne en meddelelse fra en kø i to trin.

1. Når du kalder **listen\_messages()**, du får den næste meddelelse i en kø som standard. Du kan også angive, hvor mange meddelelser, du vil have vist. De meddelelser, der returneres fra **listen\_messages()** ikke er synligt for andre kode, der læser meddelelser fra denne kø. Du kan overføre i synlighed timeouten i sekunder som en parameter.

2. For at afslutte fjerner meddelelsen fra køen, skal du også kontakte **delete_message()**.

Denne proces for at fjerne en meddelelse sikrer, at når din kode ikke at behandle en meddelelse på grund af hardware og software fejl, kan en anden forekomst af din kode få den samme meddelelse og prøve igen. Dine kode opkald **slette\_message()** højre når meddelelsen er blevet behandlet.

    messages = azure_queue_service.list_messages("test-queue", 30)
    azure_queue_service.delete_message("test-queue", 
      messages[0].id, messages[0].pop_receipt)

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Sådan: Ændre indholdet af en meddelelse i køen

Du kan ændre indholdet af en meddelelse direkte i køen. Nedenstående kode bruger **update_message()** metoden til at opdatere en meddelelse. Metoden returnerer en tupel, som indeholder pop modtagelse af meddelelsen kø og en UTC dato klokkeslæt-værdi, der repræsenterer når meddelelsen er synlige i køen.

    message = azure_queue_service.list_messages("test-queue", 30)
    pop_receipt, time_next_visible = azure_queue_service.update_message(
      "test-queue", message.id, message.pop_receipt, "updated test message", 
      30)

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Sådan: Yderligere indstillinger for Sådan flytning meddelelser

Der er to måder, du kan tilpasse hentning af meddelelse fra en kø.

1. Du kan få en mængde meddelelse.

2. Du kan angive en længere eller kortere invisibility timeout, så din kode, mere eller mindre tid til at behandle fuldt hver meddelelse.

Følgende kode eksempler på Brug den **listen\_messages()** metode til at få 15 meddelelser i et opkald. Derefter udskriver og sletter hver meddelelse. Invisibility timeouten angives også til fem minutter for hver meddelelse.

    azure_queue_service.list_messages("test-queue", 300
      {:number_of_messages => 15}).each do |m|
      puts m.message_text
      azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
    end

## <a name="how-to-get-the-queue-length"></a>Sådan: Få kø længden

Du kan få en vurdering af antallet af beskeder i køen. Den **få\_kø\_metadata()** metode beder om tjenesten kø til at returnere det antal tilnærmet meddelelser og metadata om køen.

    message_count, metadata = azure_queue_service.get_queue_metadata(
      "test-queue")

## <a name="how-to-delete-a-queue"></a>Sådan: Slette en kø

Hvis du vil slette en kø og alle de meddelelser, der er indeholdt i den, skal du ringe på **slette\_queue()** metode for kø-objektet.

    azure_queue_service.delete_queue("test-queue")

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært grundlæggende kø lagerplads, skal du følge disse links for at lære mere komplekse lagerplads opgaver.

- Gå til [Azure-lager-teamets Blog](http://blogs.msdn.com/b/windowsazurestorage/)
- Besøg [Azure SDK til fonetisk](https://github.com/WindowsAzure/azure-sdk-for-ruby) lager på GitHub

En sammenligning mellem Azure kø Service beskrevet i denne artikel og Azure Service Bus køer gennemgås i artiklen [Sådan bruger du Bus servicekøer](/develop/ruby/how-to-guides/service-bus-queues/) under [Azure køer og Service Bus køer - sammenlignet og Contrasted](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
 
