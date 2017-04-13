<properties 
    pageTitle="Sådan bruges AMQP 1.0 med .NET Service Bus API | Microsoft Azure" 
    description="Lær at bruge avancerede meddelelse Queuing Protodol (AMQP) 1.0 med Azure .NET Service Bus API." 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/29/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-amqp-10-with-the-service-bus-net-api"></a>Sådan bruges AMQP 1.0 med Service Bus .NET API

De avancerede meddelelse Queuing Protocol (AMQP) 1.0 er en effektiv, pålidelig og tråd niveau SMS protokol, du kan bruge til at oprette robust, på tværs af platforme, messaging-programmer.

Understøttelse af AMQP 1,0 i Service Bus betyder, at du kan bruge den queuing og Udgiv/Abonner formidlet SMS funktioner fra et område af platforme ved hjælp af en effektiv binære protokol. Desuden kan du oprette programmer, der består af komponenter, der er oprettet ved hjælp af en blanding af sprog, strukturer og operativsystemer.

I denne artikel forklares, hvordan du bruger Service Bus formidlet SMS funktionerne (køer og Udgiv/Abonner emner) fra .NET-programmer, der bruger tjenesten Bus .NET API. Der er en [companion artikel](service-bus-java-how-to-use-jms-api-amqp.md) , der forklarer, hvordan du gør den samme med API'EN standard Java meddelelse Service (JMS). Du kan bruge disse to vejledninger sammen til at få mere for at vide om på tværs af platforme messaging ved hjælp af AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Introduktion til Service Bus

Denne artikel antages det, at du allerede har en Service Bus navneområde, der indeholder en kø med navnet "Kø1." Hvis du ikke gør det, kan du oprette navneområde og kø ved hjælp af [Azure-portalen][]. Du kan finde flere oplysninger om, hvordan du opretter Service Bus navneområder og køer, kan du se [komme i gang med Service Bus køer](service-bus-dotnet-get-started-with-queues.md#1-create-a-namespace-using-the-Azure-portal).

## <a name="download-the-service-bus-sdk"></a>Hente Service Bus SDK

Understøttelse af AMQP 1.0 er tilgængelig i Service Bus SDK version 2.1 eller nyere. Du kan hente de seneste bit fra NuGet på [http://nuget.org/packages/WindowsAzure.ServiceBus/](http://nuget.org/packages/WindowsAzure.ServiceBus/).

## <a name="code-net-applications"></a>Kode .NET-programmer

Som standard kommunikerer biblioteket Service Bus .NET klienten med Service Bus tjenesten ved hjælp af en dedikeret SOAP-baseret protokol. Hvis du vil bruge AMQP 1.0 i stedet for standard kræver protocol eksplicitte konfiguration på forbindelsesstrengen Service Bus som beskrevet i næste afsnit. End ændringen forbliver programkode som uændret, når du bruger AMQP 1.0.

I den aktuelle udgave er der et par API-funktioner, der ikke understøttes, når du bruger AMQP. Disse ikke-understøttede funktioner vises senere i sektionen [ikke-understøttede funktioner og begrænsninger](#unsupported-features-and-restrictions). Nogle af de avancerede konfigurationsindstillinger også have en anden betydning, når du bruger AMQP. Ingen af disse indstillinger er brugt i denne artikel, men der findes i [tjenesten Bus AMQP oversigt](service-bus-amqp-dotnet.md#unsupported-features-restrictions-and-behavioral-differences)flere oplysninger.

### <a name="configure-via-appconfig"></a>Konfigurere via App.config

Det er hensigtsmæssigt anbefalede, programmer bruger konfigurationsfil App.config til at gemme indstillingerne. Du kan bruge App.config gemme Service Bus **ConnectionString**for Service Bus programmer. Dette eksempelprogram anvender også App.config til lagring af navnet på den tjeneste Bus messaging enhed, der bruges.

En eksempelfil App.config er vist her:

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
            <add key="EntityName" value="queue1" />
    </appSettings>
</configuration>
```

### <a name="configure-the-service-bus-connection-string"></a>Konfigurere forbindelsesstrengen Service Bus

Værdien af indstillingen **Microsoft.ServiceBus.ConnectionString** er forbindelsesstrengen Service Bus, der bruges til at konfigurere forbindelsen til Service Bus. Formatet er som følger:

```
Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp
```

Hvor `[namespace]` og `[SAS key]` hentes fra [Azure-portalen][]. Få mere at vide under [Sådan bruges Service Bus køer] [].

Når du bruger AMQP, føjes forbindelsesstrengen med `;TransportType=Amqp`, som fortæller biblioteket klienten til at oprette dens forbindelse til tjenesten Bus bruger AMQP 1.0.

### <a name="configure-the-entity-name"></a>Konfigurere navnet på enhed

Dette eksempelprogram bruger den `EntityName` konfiguration i sektionen **appSettings** i App.config-filen til at konfigurere navnet på den kø, som programmet samtaler meddelelser.

### <a name="a-simple-net-application-using-a-service-bus-queue"></a>Et enkelt .NET-program ved hjælp af en Service Bus kø

I følgende eksempel sender og modtager meddelelser til og fra en tjeneste Bus kø.

```
// SimpleSenderReceiver.cs
    
using System;
using System.Configuration;
using System.Threading;
using Microsoft.ServiceBus.Messaging;
    
namespace SimpleSenderReceiver
{
    class SimpleSenderReceiver
    {
        private static string connectionString;
        private static string entityName;
        private static Boolean runReceiver = true;
        private MessagingFactory factory;
        private MessageSender sender;
        private MessageReceiver receiver;
        private MessageListener messageListener;
        private Thread listenerThread;
    
        static void Main(string[] args)
        {
            try
            {
                if ((args.Length > 0) && args[0].ToLower().Equals("sendonly"))
                    runReceiver = false;
                    
                string ConnectionStringKey = "Microsoft.ServiceBus.ConnectionString";
                string entityNameKey = "EntityName";
                entityName = ConfigurationManager.AppSettings[entityNameKey];
                connectionString = ConfigurationManager.AppSettings[ConnectionStringKey];
                SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
    
                Console.WriteLine("Press [enter] to send a message. " +
                    "Type 'exit' + [enter] to quit.");
    
                while (true)
                {
                    string s = Console.ReadLine();
                    if (s.ToLower().Equals("exit"))
                    {
                        simpleSenderReceiver.Close();
                        System.Environment.Exit(0);
                    }
                    else
                        simpleSenderReceiver.SendMessage();
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Caught exception: " + e.Message);
            }
        }
    
        public SimpleSenderReceiver()
        {
            factory = MessagingFactory.CreateFromConnectionString(connectionString);
            sender = factory.CreateMessageSender(entityName);
    
            if (runReceiver)
            {
                receiver = factory.CreateMessageReceiver(entityName);
                messageListener = new MessageListener(receiver);
                listenerThread = new Thread(messageListener.Listen);
                listenerThread.Start();
            }
        }
    
        public void Close()
        {
            messageListener.RequestStop();
            listenerThread.Join();
            factory.Close();
        }
    
        private void SendMessage()
        {
            BrokeredMessage message = new BrokeredMessage("Test AMQP message from .NET");
            sender.Send(message);
            Console.WriteLine("Sent message with MessageID = " 
                + message.MessageId);
        }

    }
    
    public class MessageListener
    {
        private MessageReceiver messageReceiver;
        public MessageListener(MessageReceiver receiver)
        {
            messageReceiver = receiver;
        }
    
        public void Listen()
        {
            while (!_shouldStop)
            {
                try
                {
                    BrokeredMessage message = 
                        messageReceiver.Receive(new TimeSpan(0, 0, 10));
                    if (message != null)
                    {
                        Console.WriteLine("Received message with MessageID = " + 
                            message.MessageId);
                        message.Complete();
                    }
                }
                catch (Exception e)
                {
                    Console.WriteLine("Caught exception: " + e.Message);
                    break;
                }
            }
        }
    
        public void RequestStop()
        {
            _shouldStop = true;
        }
    
        private volatile bool _shouldStop;
    }
}
```

### <a name="run-the-application"></a>Køre programmet

Køre programmet giver output i formularen:

```
> SimpleSenderReceiver.exe
Press [enter] to send a message. Type 'exit' + [enter] to quit.
    
Sent message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
Received message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
    
Sent message with MessageID = d00e2e088f06416da7956b58310f7a06
Received message with MessageID = d00e2e088f06416da7956b58310f7a06
    
Received message with MessageID = f27f79ec124548c196fd0db8544bca49
Sent message with MessageID = f27f79ec124548c196fd0db8544bca49
exit
```

## <a name="cross-platform-messaging-between-jms-and-net"></a>På tværs af platforme messaging mellem JMS og .NET

Dette emne, der blev vist, hvordan du kan sende meddelelser til Service Bus ved hjælp af .NET, og hvordan du kan modtage disse meddelelser ved hjælp af .NET. En af de vigtigste fordele ved AMQP 1.0 er dog, at det giver mulighed for programmer skal anlægges fra komponenter, der er skrevet på et andet sprog med meddelelser, der er udvekslet pålideligt og på samme måde.

Brug af .NET programmet er beskrevet ovenfor og et lignende Java-program, der tages fra en medfølgende vejledning til, [hvordan du bruger den Java meddelelse Service (JMS) API med Service Bus & AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md), er det muligt at udveksle meddelelser mellem .NET og Java. 

Du kan finde flere oplysninger om detaljerne i tværs af platforme messaging ved hjælp af Service Bus og AMQP 1.0, [Service Bus AMQP 1.0 oversigt](service-bus-amqp-overview.md).

### <a name="jms-to-net"></a>JMS til .NET

Til at vise JMS til .NET messaging:

* Starte programmet .NET eksempel uden nogen kommandolinjeparametre.
* Starte programmet Java eksempel med argumentet "sendonly" kommandolinjen. I denne tilstand, programmet ikke modtager meddelelser fra køen, vil det kun sende.
* Tryk på **Enter** nogle gange i konsollen Java-program, hvilket betyder, at fuldføre afsendelsen.
* Disse meddelelser er modtaget af .NET-program.

### <a name="output-from-jms-application"></a>Output fra JMS program

```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

### <a name="output-from-net-application"></a>Output fra .NET program

```
> SimpleSenderReceiver.exe  
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

## <a name="net-to-jms"></a>.NET til JMS

Til at vise .NET JMS meddelelser:

* Starte programmet .NET eksempel med argumentet "sendonly" kommandolinjen. I denne tilstand, programmet ikke modtager meddelelser fra køen, vil det kun sende.
* Starte programmet Java eksempel uden en hvilken som helst kommandolinjeargumenter.
* Tryk på **Enter** nogle gange i konsollen .NET-program, hvilket betyder, at fuldføre afsendelsen.
* Disse meddelelser er modtaget af Java-program.

#### <a name="output-from-net-application"></a>Output fra .NET program

```
> SimpleSenderReceiver.exe sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3  
Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
exit
```

#### <a name="output-from-jms-application"></a>Output fra JMS program

```
> java SimpleSenderReceiver 
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
exit
```

## <a name="unsupported-features-and-restrictions"></a>Ikke-understøttede funktioner og begrænsninger

Følgende .NET Service Bus API-funktioner understøttes ikke i øjeblikket, når du bruger AMQP:

 * Transaktioner
 * Send via filoverførsel destination

Se [ikke-understøttede funktioner, begrænsninger, og funktionsmæssige forskelle](service-bus-amqp-dotnet.md#unsupported-features-restrictions-and-behavioral-differences)kan finde flere oplysninger.

## <a name="summary"></a>Oversigt

I denne artikel viste, hvordan du kan få adgang til tjenesten Bus formidlet SMS funktionerne (køer og Udgiv/Abonner emner) fra .NET ved hjælp af AMQP 1.0 og Service Bus .NET API.

Du kan også bruge tjenesten Bus AMQP 1.0 fra andre sprog, herunder Java, C, Python og PHP. Komponenter, der er oprettet ved hjælp af disse sprog kan udveksle meddelelser, pålideligt og på fuld pålidelighed ved hjælp af AMQP 1.0 i Service Bus. Du kan finde yderligere oplysninger finder [Service Bus AMQP oversigt](service-bus-amqp-dotnet.md).

## <a name="next-steps"></a>Næste trin

Nu hvor du har læst en oversigt over Service Bus og AMQP med .NET, kan du se følgende links for at få flere oplysninger.

* [Understøttelse af AMQP 1.0 i Azure Service Bus](service-bus-amqp-overview.md)
* [Sådan bruges de Java meddelelse Service (JMS) API med Service Bus & AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
* [Sådan bruger du Service Bus køer](service-bus-dotnet-get-started-with-queues.md)
 
[Azure-portalen]: https://portal.azure.com
