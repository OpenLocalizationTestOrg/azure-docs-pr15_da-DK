<properties 
    pageTitle="Service Bus formidlet SMS .NET selvstudium | Microsoft Azure"
    description="Formidlet SMS .NET-selvstudium."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# <a name="service-bus-brokered-messaging-net-tutorial"></a>Service Bus formidlet SMS .NET-selvstudium

Azure Service Bus indeholder to omfattende messaging-løsninger – en gennem en centralt "relay" kører i skyen, der understøtter en række forskellige transportprotokoller og Web services standarder, herunder SOAP, WS-*, og RESTEN. Klienten ikke skal bruge en direkte forbindelse til tjenesten lokalt eller behøver det, hvor tjenesten er placeret, og den lokale tjeneste behøver ikke nogen indgående porte åbne firewallen.

Den anden SMS løsning aktiverer "formidlet" messaging-funktionerne. Disse kan betragtes som asynkron eller frakoblet SMS-funktioner, der understøtter udgivelse-abonnement, tidsmæssig afkobling og belastning situationer, hvor tjenesten Bus messaging-infrastrukturen. Afkoblet kommunikation har mange fordele for eksempel kan klienter og servere oprette forbindelse efter behov og udføre deres handlinger i en asynkron måde.

Dette selvstudium er beregnet til at give dig en oversigt over og praktisk erfaring med køer, en af de vigtigste komponenter af Service Bus formidlet messaging. Når du arbejder via rækkefølge af emner i dette selvstudium, har du et program, der udfylder en liste over meddelelser, opretter en kø og sender meddelelser til den pågældende computer. Til sidst skal programmet modtager og viser meddelelser fra køen og derefter rydder op i dens ressourcer og afslutter. Et tilsvarende selvstudium, der beskriver, hvordan du opretter et program, der bruger tjenesten Bus Relay, se [Service Bus videresendes SMS selvstudium](../service-bus-relay/service-bus-relay-tutorial.md).

## <a name="introduction-and-prerequisites"></a>Introduktion og forudsætninger

Køer tilbyder First In, først ud (FIFU) levering af meddelelser til en eller flere konkurrerende forbrugere. FIFO betyder, at meddelelser er typisk forventet modtaget og behandlet af modtagere i den tidsmæssige rækkefølge, hvori de blev sat i kø, og alle meddelelser, der modtages og behandles som en enkelt meddelelse forbruger. En fordel ved at bruge køer er at opnå *tidsmæssig afkobling* af programkomponenter: med andre ord producenter og forbrugere behøver ikke at sende og modtage meddelelser på samme tid, da meddelelser gemmes solidt i køen. En relateret fordel er *indlæse udjævning*, som gør det muligt for producenter og forbrugere til at sende og modtage meddelelser til forskellig rente.

Følgende er nogle administrative og nødvendige trin, skal du følge inden du starter selvstudiet. Først er til at oprette et navneområde med tjenesten, og du får fat i en delt adgang signatur (SAS) nøgle. Et navneområde indeholder en programmet grænse for hvert program fremvises Service Bus. En SAS nøgle genereres automatisk af systemet, når der oprettes et service navneområde. Kombination af tjenesten navneområde og SAS nøgle indeholder en legitimationsoplysninger, som godkender Service Bus adgang til et program.

### <a name="create-a-service-namespace-and-obtain-a-sas-key"></a>Oprette et service navneområde og hente en SAS nøgle

Det første trin er at oprette et service navneområde, og at få en [Delt Access signatur](service-bus-sas-overview.md) (SAS) nøgle. Et navneområde indeholder en programmet grænse for hvert program fremvises Service Bus. En SAS nøgle genereres automatisk af systemet, når der oprettes et service navneområde. Kombination af tjenesten navneområde og SAS nøgle indeholder en legitimationsoplysninger for tjenesten Bus til at godkende adgang til et program.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Næste trin er at oprette et Visual Studio-projekt og skrive to hjælpefunktioner, der indlæser en kommasepareret liste over meddelelser til et kraftigt skrevet [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) .NET [listen](https://msdn.microsoft.com/library/6sh2ey19.aspx) objekt.

### <a name="create-a-visual-studio-project"></a>Oprette et Visual Studio-projekt

1. Åbne Visual Studio som administrator ved at højreklikke på programmet i menuen Start og klikke på **Kør som administrator**.

1. Oprette et nyt console programmet projekt. Klik på fanen **filer** og vælge **Ny**og derefter klikke på **projekt**. I dialogboksen **Nyt projekt** , skal du klikke på **Visual C#** (Hvis **Visual C#** ikke vises, se under **Andre sprog**), klik på skabelonen **Console-program** , og kald den **QueueSample**. Brug standard **placering**. Klik på **OK** for at oprette projektet.

1. Brug af NuGet package manager til at føje Service Bus biblioteker til dit projekt:
    1. I Solution Explorer, skal du højreklikke på **QueueSample** projektet, og klik derefter på **Administrere NuGet pakker**.
    2. I dialogboksen **Administrer Nuget-pakker** , klik på fanen **Gennemse** , og Søg efter **Azure Service Bus**derefter klikke på **installation**.
<br />
1. I Solution Explorer skal du dobbeltklikke på filen Program.cs for at åbne den i Visual Studio editor. Ændre navneområdenavnet fra dens standardnavnet på `QueueSample` til `Microsoft.ServiceBus.Samples`.

    ```
    Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. Ændre den `using` sætninger, som vist i følgende kode.

    ```
    using System;
    using System.Collections.Generic;
    using System.Data;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.ServiceBus.Messaging;
    ```

1. Oprette en tekstfil med navnet Data.csv og kopi i den følgende kommasepareret tekst.

    ```
    IssueID,IssueTitle,CustomerID,CategoryID,SupportPackage,Priority,Severity,Resolved
    1,Package lost,1,1,Basic,5,1,FALSE
    2,Package damaged,1,1,Basic,5,1,FALSE
    3,Product defective,1,2,Premium,5,2,FALSE
    4,Product damaged,2,2,Premium,5,2,FALSE
    5,Package lost,2,2,Basic,5,2,TRUE
    6,Package lost,3,2,Basic,5,2,FALSE
    7,Package damaged,3,7,Premium,5,3,FALSE
    8,Product defective,3,2,Premium,5,3,FALSE
    9,Product damaged,4,6,Premium,5,3,TRUE
    10,Package lost,4,8,Basic,5,3,FALSE
    11,Package damaged,5,4,Basic,5,4,FALSE
    12,Product defective,5,4,Basic,5,4,FALSE
    13,Package lost,6,8,Basic,5,4,FALSE
    14,Package damaged,6,7,Premium,5,5,FALSE
    15,Product defective,6,2,Premium,5,5,FALSE
    ```

    Gem og Luk filen Data.csv, og Husk, at den placering, hvor du gemte den.

1. I Solution Explorer, højreklik på navnet på projektet (i dette eksempel **QueueSample**), klik på **Tilføj**, og klik derefter på **Eksisterende element**.

1. Gå til den Data.csv-fil, du oprettede i trin 6. Klik på fil, og derefter klikke på **Tilføj**. Sørg for, at * *alle filer (*.*) ** er markeret på listen over filtyper.

### <a name="create-a-method-that-parses-a-list-of-messages"></a>Oprette en afskrivningsmetode, som fortolker en liste over meddelelser

1. I den `Program` klasse før den `Main()` metode kan erklære to variabler: en af typen **DataTable**skal indeholde på listen over meddelelser i Data.csv. Den anden skal være af typen liste objekt kraftigt skrevet til [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Disse er listen over formidlet meddelelser, der bruges efterfølgende trin i selvstudiet.

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        class Program
        {
    
            private static DataTable issues;
            private static List<BrokeredMessage> MessageList;
    ```

1. Udvendige `Main()`, definere en `ParseCSV()` afskrivningsmetode, som fortolker på listen over meddelelser i Data.csv og indlæser meddelelser til en [DataTable](https://msdn.microsoft.com/library/azure/system.data.datatable.aspx) tabel, som vist her. Metoden returnerer et **DataTable** objekt.

    ```
    static DataTable ParseCSVFile()
    {
        DataTable tableIssues = new DataTable("Issues");
        string path = @"..\..\data.csv";
        try
        {
            using (StreamReader readFile = new StreamReader(path))
            {
                string line;
                string[] row;
    
                // create the columns
                line = readFile.ReadLine();
                foreach (string columnTitle in line.Split(','))
                {
                    tableIssues.Columns.Add(columnTitle);
                }
    
                while ((line = readFile.ReadLine()) != null)
                {
                    row = line.Split(',');
                    tableIssues.Rows.Add(row);
                }
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Error:" + e.ToString());
        }
    
        return tableIssues;
    }
    ```

1. I den `Main()` metode, Tilføj en sætning, der kalder den `ParseCSVFile()` metode:

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
    
    }
    ```

### <a name="create-a-method-that-loads-the-list-of-messages"></a>Oprette en metode, der indlæses, på listen over meddelelser

1. Udvendige `Main()`, definere en `GenerateMessages()` afskrivningsmetode, som tager det **DataTable** objekt, der returneres af `ParseCSVFile()` og indlæser tabellen i en kraftigt skrevet liste over formidlet meddelelser. Metoden, returnerer objektet **liste** , som i følgende eksempel. 

    ```
    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
        // Instantiate the brokered list object
        List<BrokeredMessage> result = new List<BrokeredMessage>();
    
        // Iterate through the table and create a brokered message for each row
        foreach (DataRow item in issues.Rows)
        {
            BrokeredMessage message = new BrokeredMessage();
            foreach (DataColumn property in issues.Columns)
            {
                message.Properties.Add(property.ColumnName, item[property]);
            }
            result.Add(message);
        }
        return result;
    }
    ```

1. I `Main()`, umiddelbart efter opkaldet til `ParseCSVFile()`, Tilføj en sætning, der kalder den `GenerateMessages()` metode med returværdien fra `ParseCSVFile()` som et argument:

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
        MessageList = GenerateMessages(issues);
    }
    ```

### <a name="obtain-user-credentials"></a>Hente brugerlegitimationsoplysninger

1. Opret først tre globale strengvariabler for at holde disse værdier. Erklære variablerne umiddelbart efter de forrige variabelerklæringer; Eksempel:

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        public class Program
        {
    
            private static DataTable issues;
            private static List<BrokeredMessage> MessageList; 

            // Add these variables
            private static string ServiceNamespace;
            private static string sasKeyName = "RootManageSharedAccessKey";
            private static string sasKeyValue;
            …
    ```

1. Dernæst skal du oprette en funktion, der accepterer og gemmer service navneområde og SAS nøgle. Tilføje denne metode uden for `Main()`. Eksempel: 

    ```
    static void CollectUserInput()
    {
        // User service namespace
        Console.Write("Please enter the namespace to use: ");
        ServiceNamespace = Console.ReadLine();
    
        // Issuer key
        Console.Write("Enter the SAS key to use: ");
        sasKeyValue = Console.ReadLine();
    }
    ```

1. I `Main()`, umiddelbart efter opkaldet til `GenerateMessages()`, Tilføj en sætning, der kalder den `CollectUserInput()` metode:

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
        MessageList = GenerateMessages(issues);
        
        // Collect user input
        CollectUserInput();
    }
    ```

### <a name="build-the-solution"></a>Opbygge løsningen

Du kan klikke **Opbygge løsning** eller trykke på **Ctrl + Shift + B** for at bekræfte nøjagtigheden af dit arbejde hidtil fra menuen **Build** i Visual Studio.

## <a name="create-management-credentials"></a>Oprette management-legitimationsoplysninger

I dette trin skal definere du at håndteringen, du vil bruge til at oprette delt adgang signatur (SAS) legitimationsoplysninger, som vil blive tilladt dit program.

1. For klarhed placeres dette selvstudium alle kø handlinger i en separat metode. Oprette en asynkron `Queue()` metode i den `Program` klasse, når den `Main()` metode. Eksempel:
 
    ```
    public static void Main(string[] args)
    {
    …
    }
    static async Task Queue()
    {
    }
    ```

1. Næste trin er at oprette en SAS legitimationsoplysninger ved hjælp af et objekt i [en TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) . Metoden oprettelse af tager SAS vigtige navn og værdi, der fås i den `CollectUserInput()` metode. Tilføj følgende kode i den `Queue()` metode:

    ```
    static async Task Queue()
    {
        // Create management credentials
        TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
    }
    ```

2. Oprette et nyt navneområde management objekt med en URI, der indeholder navneområdenavnet på og administration af legitimationsoplysningerne i det forrige trin, som argumenter. Du kan tilføje denne kode direkte efter den kode, der er tilføjet i det forrige trin. Sørg for at erstatte `<yourNamespace>` med navnet på din tjeneste navneområde:
    
    ```
    NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    ```

### <a name="example"></a>Eksempel

På dette tidspunkt skal din kode ligne følgende:

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
  public class Program
  {
    private static DataTable issues;
    private static List<BrokeredMessage> MessageList;
    private static string ServiceNamespace;
    private static string sasKeyName = "RootManageSharedAccessKey";
    private static string sasKeyValue;

    static void Main(string[] args)
    {
      // Populate test data
      issues = ParseCSVFile();
      MessageList = GenerateMessages(issues);

      // Collect user input
      CollectUserInput();

      // Add this call
      Task.WaitAll(Queue());
    }

    static async Task Queue()
    {
      // Create management credentials
      TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
      NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    }

    static DataTable ParseCSVFile()
    {
      DataTable tableIssues = new DataTable("Issues");
      string path = @"..\..\data.csv";
      try
      {
        using (StreamReader readFile = new StreamReader(path))
        {
          string line;
          string[] row;

          // create the columns
          line = readFile.ReadLine();
          foreach (string columnTitle in line.Split(','))
          {
            tableIssues.Columns.Add(columnTitle);
          }

          while ((line = readFile.ReadLine()) != null)
          {
            row = line.Split(',');
            tableIssues.Rows.Add(row);
          }
        }
      }
      catch (Exception e)
      {
        Console.WriteLine("Error:" + e.ToString());
      }

      return tableIssues;
    }

    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
      // Instantiate the brokered list object
      List<BrokeredMessage> result = new List<BrokeredMessage>();

      // Iterate through the table and create a brokered message for each row
      foreach (DataRow item in issues.Rows)
      {
        BrokeredMessage message = new BrokeredMessage();
        foreach (DataColumn property in issues.Columns)
        {
          message.Properties.Add(property.ColumnName, item[property]);
        }
        result.Add(message);
      }
      return result;
    }

    static void CollectUserInput()
    {
      // User service namespace
      Console.Write("Please enter the service namespace to use: ");
      ServiceNamespace = Console.ReadLine();

      // Issuer key
      Console.Write("Please enter the issuer key to use: ");
      sasKeyValue = Console.ReadLine();
    }
  }
}
```

## <a name="send-messages-to-the-queue"></a>Sende meddelelser til køen

I dette trin skal du oprette en kø og derefter sende meddelelserne findes på listen over formidlet meddelelser til den pågældende computer.

### <a name="create-queue-and-send-messages-to-the-queue"></a>Oprette kø og sende meddelelser til køen

1. Opret først køen. For eksempel kald den `myQueue`, og Definer umiddelbart efter at du har tilføjet i håndteringen den `Queue()` metode i det sidste trin:

    ```
    QueueDescription myQueue;

    if (namespaceClient.QueueExists("IssueTrackingQueue"))
    {
        namespaceClient.DeleteQueue("IssueTrackingQueue");
    }

    myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
    ```

1. I den `Queue()` metode, oprette et SMS factory-objekt med en nyoprettet Service Bus URI som argument. Tilføj følgende kode umiddelbart efter at du har tilføjet i det sidste trin håndteringen. Sørg for at erstatte `<yourNamespace>` med navnet på din tjeneste navneområde:

    ```
    MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    ```

1. Dernæst skal du oprette objektet kø ved hjælp af klassen [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) . Føj følgende kode lige efter den kode, du tilføjede i det sidste trin:

    ```
    QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");
    ```

1. Dernæst skal tilføje kode, der gentages gennem listen over formidlet meddelelser, du oprettede tidligere, sende hver til køen. Føj følgende kode direkte efter den `CreateQueueClient()` -sætning i det forrige trin:
    
    ```
    // Send messages
    Console.WriteLine("Now sending messages to the queue.");
    for (int count = 0; count < 6; count++)
    {
        var issue = MessageList[count];
        issue.Label = issue.Properties["IssueTitle"].ToString();
        await myQueueClient.SendAsync(issue);
        Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
    }
    ```

## <a name="receive-messages-from-the-queue"></a>Modtage meddelelser fra køen

I dette trin skal hente du listen over meddelelser fra køen du har oprettet i ovenstående trin.

### <a name="create-a-receiver-and-receive-messages-from-the-queue"></a>Oprette en modtager og modtage meddelelser fra køen

I den `Queue()` metode, navigere gennem køen og modtage meddelelser ved hjælp af metoden [QueueClient.ReceiveAsync](https://msdn.microsoft.com/library/azure/dn130423.aspx) , udskriver hver meddelelse til konsollen. Føj følgende kode lige efter den kode, du tilføjede i det forrige trin:

```
Console.WriteLine("Now receiving messages from Queue.");
BrokeredMessage message;
while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();
    
        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

Bemærk, at den `Thread.Sleep` bruges kun til at simulere meddelelsen behandling, og du sandsynligvis vil ikke kunne skal det i et reelle SMS til computeren.

### <a name="end-the-queue-method-and-clean-up-resources"></a>Du kan afslutte metoden køen og rydde op i ressourcer

Lige efter den forrige kode, skal du tilføje følgende kode til at rydde op i meddelelsen fabrik og kø ressourcer:

```
factory.Close();
myQueueClient.Close();
namespaceClient.DeleteQueue("IssueTrackingQueue");
```

### <a name="call-the-queue-method"></a>Kalde metoden kø

Det sidste trin er at tilføje en sætning, der ringer op til det `Queue()` metode fra `Main()`. Tilføj følgende fremhævede linje af kode i slutningen af Main():
    
```
public static void Main(string[] args)
{
    // Collect user input
    CollectUserInput();
    
    // Populate test data
    issues = ParseCSVFile();
    MessageList = GenerateMessages(issues);
    
    // Add this call
    Queue();
}
```

### <a name="example"></a>Eksempel

Følgende kode indeholder fuldført **QueueSample** programmet.

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
    public class Program
    {
        private static DataTable issues;
        private static List<BrokeredMessage> MessageList;

        // Add these variables
        private static string ServiceNamespace;
        private static string sasKeyName = "RootManageSharedAccessKey";
        private static string sasKeyValue;

        static void Main(string[] args)
        {

            // Populate test data
            issues = ParseCSVFile();
            MessageList = GenerateMessages(issues);

            // Collect user input
            CollectUserInput();

            Queue();

        }

        static async Task Queue()
        {
            // Create management credentials
            TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
            NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueDescription myQueue;

            if (namespaceClient.QueueExists("IssueTrackingQueue"))
            {
                namespaceClient.DeleteQueue("IssueTrackingQueue");
            }
            
            myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
            
            MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");

            // Send messages
            Console.WriteLine("Now sending messages to the queue.");
            for (int count = 0; count < 6; count++)
            {
                var issue = MessageList[count];
                issue.Label = issue.Properties["IssueTitle"].ToString();
                await myQueueClient.SendAsync(issue);
                Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
            }

            Console.WriteLine("Now receiving messages from Queue.");
            BrokeredMessage message;
            while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
            {
                Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
                message.Complete();

                Console.WriteLine("Processing message (sleeping...)");
                Thread.Sleep(1000);
            }

            factory.Close();
            myQueueClient.Close();
            namespaceClient.DeleteQueue("IssueTrackingQueue");


        }

        static void CollectUserInput()
        {
            // User service namespace
            Console.Write("Please enter the namespace to use: ");
            ServiceNamespace = Console.ReadLine();

            // Issuer key
            Console.Write("Enter the SAS key to use: ");
            sasKeyValue = Console.ReadLine();
        }

        static List<BrokeredMessage> GenerateMessages(DataTable issues)
        {
            // Instantiate the brokered list object
            List<BrokeredMessage> result = new List<BrokeredMessage>();

            // Iterate through the table and create a brokered message for each row
            foreach (DataRow item in issues.Rows)
            {
                BrokeredMessage message = new BrokeredMessage();
                foreach (DataColumn property in issues.Columns)
                {
                    message.Properties.Add(property.ColumnName, item[property]);
                }
                result.Add(message);
            }
            return result;
        }

        static DataTable ParseCSVFile()
        {
            DataTable tableIssues = new DataTable("Issues");
            string path = @"..\..\data.csv";
            try
            {
                using (StreamReader readFile = new StreamReader(path))
                {
                    string line;
                    string[] row;

                    // create the columns
                    line = readFile.ReadLine();
                    foreach (string columnTitle in line.Split(','))
                    {
                        tableIssues.Columns.Add(columnTitle);
                    }

                    while ((line = readFile.ReadLine()) != null)
                    {
                        row = line.Split(',');
                        tableIssues.Rows.Add(row);
                    }
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Error:" + e.ToString());
            }

            return tableIssues;
        }
    }
}
```

## <a name="build-and-run-the-queuesample-application"></a>Oprette og køre programmet QueueSample

Nu hvor du har fuldført de foregående trin, kan du oprette og køre programmet **QueueSample** .

### <a name="build-the-queuesample-application"></a>Opbygge programmet QueueSample

Klik på **Opret løsning**i Visual Studio, fra menuen **Build** , eller tryk på **Ctrl + Shift + B**. Hvis der opstår fejl, skal du kontrollere, at din kode er korrekt baseret på hele eksemplet præsenteres i slutningen af det forrige trin.

## <a name="next-steps"></a>Næste trin

Dette selvstudium viste, hvordan du opretter en Service Bus klient, program og ved hjælp af funktionerne Service Bus formidlet SMS-tjenesten. Et lignende selvstudium, der bruger Service Bus [Relay](service-bus-messaging-overview.md#Relayed-messaging), se [Service Bus videresendes SMS selvstudium](../service-bus-relay/service-bus-relay-tutorial.md).

Hvis du vil vide mere om [Service Bus](https://azure.microsoft.com/services/service-bus/), under følgende emner.

- [Service Bus SMS oversigt](service-bus-messaging-overview.md)
- [Service Bus grundlæggende](service-bus-fundamentals-hybrid-solutions.md)
- [Service Bus arkitektur](service-bus-architecture.md)

