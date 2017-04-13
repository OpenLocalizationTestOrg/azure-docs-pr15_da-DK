<properties
   pageTitle="Logik app scenarie: oprette en Azure funktioner Service Bus udløser | Microsoft Azure"
   description="Brug Azure funktioner til at oprette en Service Bus udløser til en logik-app"
   services="logic-apps,functions"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/23/2016"
   ms.author="jehollan"/>

# <a name="logic-app-scenario-create-an-azure-service-bus-trigger-by-using-azure-functions"></a>Logik app scenarie: oprette en Azure Service Bus udløser ved hjælp af Azure funktioner

Du kan bruge Azure til at oprette en udløser til en logik app, når du skal installere en længerevarende lytteren eller opgave. For eksempel kan du oprette en funktion, der skal Lyt til en kø og med det samme brand en logik app som en push-udløser.

## <a name="build-the-logic-app"></a>Udvikle logik-app

I dette eksempel har du en funktion, der kører for hver logik-app, der skal udløses. Opret først en logik app, der har en HTTP-anmodning udløser. Funktionen kalder pågældende slutpunkt, når der modtages en meddelelse i køen.  

1. Oprette en ny logik-app Vælg **Manuel - når en HTTP-anmodning, der modtages** udløser.  
   Du kan også angive et JSON skema til brug med meddelelsen kø ved hjælp af et værktøj som [jsonschema.net](http://jsonschema.net). Indsæt udløseren i skemaet. Dette hjælper med at forstå dataene form designer og mere dataflow nemt egenskaber gennem arbejdsprocessen.
1. Tilføj eventuelle ekstra trin, der skal udføres, når der modtages en meddelelse i køen. For eksempel sende en mail via Office 365.  
1. Gemme appen logik for at generere tilbagekald URL-adressen for udløser til denne logik app. URL-adressen vises på kortet udløser.

![Tilbagekaldet URL-adressen vises på kortet udløser][1]

## <a name="build-the-function"></a>Opbygge funktionen

Derefter skal du oprette en funktion, der skal fungere som udløser og lyt til køen.

1. Vælg **Ny funktion**i [Azure funktioner portal](https://functions.azure.com/signin), og derefter vælge **ServiceBusQueueTrigger - C#** -skabelonen.

    ![Azure funktioner portal][2]

2. Konfigurere forbindelsen til Service Bus køen (som anvender Azure Service Bus SDK `OnMessageReceive()` lytteren).
3. Skriv en simpel funktion for at ringe logik app slutpunktet (oprettet tidligere) ved at bruge kø meddelelsen som en udløser. Her er en fuld eksempel på en funktion. I eksemplet bruges en `application/json` meddelelse indholdstype, men du kan ændre dette Hvis det er nødvendigt.

   ```
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";

   public static void Run(string myQueueItem, TraceWriter log)
   {

       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
       using (var client = new HttpClient())
       {
           var response = client.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
       }
   }
   ```

Tilføj en kø meddelelse via et værktøj som [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer)for at teste. Se logik app'en brand umiddelbart efter funktionen modtager meddelelsen.

<!-- Image References -->
[1]: ./media/app-service-logic-scenario-function-sb-trigger/manualTrigger.PNG
[2]: ./media/app-service-logic-scenario-function-sb-trigger/newQueueTriggerFunction.PNG
