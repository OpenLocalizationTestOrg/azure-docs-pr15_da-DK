<properties 
    pageTitle="Sådan bruger du Azure Service Bus med WebJobs SDK" 
    description="Lær at bruge Azure Service Bus køer og emner med WebJobs SDK." 
    services="app-service\web, service-bus" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="how-to-use-azure-service-bus-with-the-webjobs-sdk"></a>Sådan bruger du Azure Service Bus med WebJobs SDK

## <a name="overview"></a>Oversigt

Denne vejledning indeholder C# kodeeksempler, der viser, hvordan du kan udløse en proces, når der modtages en meddelelse til Azure Service Bus. Eksemplerne i brug [WebJobs SDK](websites-dotnet-webjobs-sdk.md) version 1.x.

Vejledningen antages det, at du ved, [hvordan du opretter et WebJob projekt i Visual Studio med strenge, der peger på kontoen lagerplads](websites-dotnet-webjobs-sdk-get-started.md).

Kodestykker Vis kun funktioner, ikke den kode, der opretter den `JobHost` objekt som i dette eksempel:

```
public class Program
{
   public static void Main()
   {
      JobHostConfiguration config = new JobHostConfiguration();
      config.UseServiceBus();
      JobHost host = new JobHost(config);
      host.RunAndBlock();
   }
}
```

Et [Eksempel på fuldført Service Bus kode](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/ServiceBus/Program.cs) er i lageret azure-webjobs-sdk-eksempler på GitHub.com.

## <a id="prerequisites"></a>Forudsætninger

Hvis du vil arbejde med tjenesten Bus, du har installeret pakken [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/) NuGet ud over de andre WebJobs SDK-pakker. 

Du har også angive forbindelsesstrengen AzureWebJobsServiceBus ud over lagerplads forbindelsesstrenge.  Du kan gøre dette i den `connectionStrings` sektion af App.config-filen, som vist i følgende eksempel:

        <connectionStrings>
            <add name="AzureWebJobsDashboard" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
            <add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
            <add name="AzureWebJobsServiceBus" connectionString="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[yourKey]"/>
        </connectionStrings>

Du kan finde et eksempelprojekt, som indeholder indstillingen Service Bus forbindelse streng i App.config-filen, [Service Bus eksempel](https://github.com/Azure/azure-webjobs-sdk-samples/tree/master/BasicSamples/ServiceBus). 

Forbindelse strengene kan også angives i Azure runtime-miljø, som tilsidesætter derefter indstillingerne for App.config, når WebJob køres i Azure; Se [Introduktion til WebJobs SDK](websites-dotnet-webjobs-sdk-get-started.md#configure-the-web-app-to-use-your-azure-sql-database-and-storage-account)kan finde flere oplysninger.

## <a id="trigger"></a>Hvordan du kan udløse en funktion, når der modtages en Service Bus kø meddelelse

Hvis du vil skrive en funktion, der kalder WebJobs SDK, når der modtages en meddelelse i køen, bruge den `ServiceBusTrigger` attributter. Attributkonstruktøren tager en parameter, der angiver navnet på den kø til afstemning.

### <a name="how-servicebustrigger-works"></a>Sådan fungerer ServiceBusTrigger

SDK modtager en meddelelse i `PeekLock` tilstand og opkald `Complete` på den meddelelse, hvis funktionen afsluttes korrekt, eller opkald `Abandon` Hvis funktionen mislykkes. Hvis funktionen kører længere end den `PeekLock` timeout, låsen automatisk fornys.

Service Bus understøtter sin egen håndtering af en kø og som ikke kan styres eller konfigureres ved WebJobs SDK. 

### <a name="string-queue-message"></a>Streng kø meddelelse

Følgende eksempel-kode læser en kø meddelelse, der indeholder en streng, og skriver strengen til WebJobs SDK dashboard.

        public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
            TextWriter logger)
        {
            logger.WriteLine(message);
        }

**Note:** Hvis du opretter kø meddelelser i et program, der ikke bruger WebJobs SDK, skal du sørge for at angive [BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) til "almindelig tekst".

### <a name="poco-queue-message"></a>POCO kø meddelelse

SDK bliver automatisk deserialisere meddelelsen kø, der indeholder JSON for en POCO [(almindelig gamle CLR-objekt](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) type. Følgende eksempel-kode læser en kø meddelelse, der indeholder en `BlobInformation` objekt, der har en `BlobName` egenskab:

        public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
            TextWriter logger)
        {
            logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
        }

Kode eksempler viser, hvordan du bruger egenskaberne for POCO til at arbejde med BLOB og tabeller i den samme funktion, skal du se [lagerplads køer version af denne artikel](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#pocoblobs).

Hvis din kode, der opretter kø meddelelsen ikke bruge WebJobs SDK, skal du bruge kode, der ligner følgende eksempel:

        var client = QueueClient.CreateFromConnectionString(ConfigurationManager.ConnectionStrings["AzureWebJobsServiceBus"].ConnectionString, "blobadded");
        BlobInformation blobInformation = new BlobInformation () ;
        var message = new BrokeredMessage(blobInformation);
        client.Send(message);

### <a name="types-servicebustrigger-works-with"></a>Typer ServiceBusTrigger fungerer med

Ud over `string` og POCO typer, du kan bruge den `ServiceBusTrigger` attribut med en bytematrix eller et `BrokeredMessage` objekt.

## <a id="create"></a>Sådan oprettes Service Bus kø meddelelser

Du kan skrive en funktion, der opretter en ny kø meddelelse anvendelse på `ServiceBus` attribut og overføre i kø navn til Attributkonstruktøren. 


### <a name="create-a-single-queue-message-in-a-non-async-function"></a>Oprette en enkelt kø meddelelse i en ikke-asynkron funktion

Følgende eksempel-kode bruger en output-parameter til at oprette en ny meddelelse i køen med navnet "outputqueue" med det samme indhold som den meddelelse, der er modtaget i køen med navnet "inputqueue".

        public static void CreateQueueMessage(
            [ServiceBusTrigger("inputqueue")] string queueMessage,
            [ServiceBus("outputqueue")] out string outputQueueMessage)
        {
            outputQueueMessage = queueMessage;
        }

Outputparameteren til oprettelse af en enkelt kø meddelelse kan være en af følgende typer:

* `string`
* `byte[]`
* `BrokeredMessage`
* En serialiserbar POCO type, som du definerer. Automatisk serialiseret som JSON.

For POCO typeparametre oprettes meddelelsen kø altid, når funktionen slutter. Hvis parameteren er null, opretter en meddelelse om kø, vil returnere null, når meddelelsen er modtaget og deserialiseres SDK. Hvis parameteren er null for de andre typer oprettes ingen kø meddelelse.

### <a name="create-multiple-queue-messages-or-in-async-functions"></a>Oprette flere kø meddelelser eller i asynkron funktioner

Hvis du vil oprette flere meddelelser, skal du bruge den `ServiceBus` attribut med `ICollector<T>` eller `IAsyncCollector<T>`, som vist i følgende eksempel-kode:

        public static void CreateQueueMessages(
            [ServiceBusTrigger("inputqueue")] string queueMessage,
            [ServiceBus("outputqueue")] ICollector<string> outputQueueMessage,
            TextWriter logger)
        {
            logger.WriteLine("Creating 2 messages in outputqueue");
            outputQueueMessage.Add(queueMessage + "1");
            outputQueueMessage.Add(queueMessage + "2");
        }

Alle de meddelelser, kø oprettes med det samme når den `Add` metode kaldes.

## <a id="topics"></a>Sådan arbejder du med Service Bus emner

Du kan skrive en funktion, der kalder SDK, når der modtages en meddelelse om et Service Bus emne ved at bruge den `ServiceBusTrigger` attribut med de parametre, der tager emnenavn og navn på abonnement, som vist i følgende eksempel-kode:

        public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
            TextWriter logger)
        {
            logger.WriteLine("Topic message: " + message);
        }

Hvis du vil oprette en meddelelse om et emne, skal du bruge den `ServiceBus` attribut med et emnenavn på samme måde, du bruger den med et kø navn.

## <a name="features-added-in-release-11"></a>Funktioner, der er tilføjet i version 1.1

Følgende funktioner er tilføjet i version 1.1:

* Tillad deep tilpasning af meddelelse behandling via `ServiceBusConfiguration.MessagingProvider`.
* `MessagingProvider`understøtter tilpasning af Service Bus `MessagingFactory` og `NamespaceManager`.
* A `MessageProcessor` strategi mønster kan du angive en processor per kø/emne.
* Meddelelse på dokumentsammenfald behandling understøttes som standard. 
* Nem tilpasning af `OnMessageOptions` via `ServiceBusConfiguration.MessageOptions`.
* Tillad [AccessRights](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/ServiceBus/Functions.cs#L71) skal angives på `ServiceBusTriggerAttribute` / `ServiceBusAttribute` (for scenarier, hvor du ikke har muligvis administrere rettigheder). 

## <a id="queues"></a>Relaterede emner, der er dækket af lagerplads køer vejledning til artiklen

Finde oplysninger om WebJobs SDK scenarier ikke specifikke for tjenesten Bus, se, [hvordan du bruger Azure kø storage med WebJobs SDK](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Emnerne i denne artikel omfatter følgende:

* Asynkron funktioner
* Flere forekomster
* Lukning
* Brug WebJobs SDK attributter i brødteksten i en funktion
* Angive SDK forbindelse strengene i kode
* Angive værdier for WebJobs SDK parametre parametre i kode
* Udløse en funktion manuelt
* Skrive logfiler

## <a id="nextsteps"></a>Næste trin

Denne vejledning har leveret kodeeksempler, der viser, hvordan du håndterer almindelige scenarier til at arbejde med Azure Service Bus. Du kan finde flere oplysninger om, hvordan du bruger Azure WebJobs og WebJobs SDK [Azure WebJobs anbefalede ressourcer](http://go.microsoft.com/fwlink/?linkid=390226).
 
