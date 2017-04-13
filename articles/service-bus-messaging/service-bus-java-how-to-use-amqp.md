<properties 
    pageTitle="Bruge AMQP 1.0 med Java Service Bus API | Microsoft Azure" 
    description="Lær at bruge Java meddelelse Service (JMS) med Azure Service Bus og avancerede meddelelse sætter i kø"
    services="service-bus"
    documentationCenter="java"
    authors="sethmanheim"  
    manager="timlt" 
    editor="" />

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="java" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>Sådan bruges de Java meddelelse Service (JMS) API med Service Bus og AMQP 1.0

De avancerede meddelelse Queuing Protocol (AMQP) 1.0 er en effektiv, pålidelig og tråd niveau SMS protokol, du kan bruge til at oprette robust, på tværs af platforme, messaging-programmer. Understøttelse af AMQP 1.0 blev føjet til Azure Service Bus i oktober 2012 og transitioned til generelle tilgængelighed (GA) i maj 2013.

Tilføjelse af AMQP 1.0 betyder, at det er nu muligt at udnytte det queuing og Udgiv/Abonner formidlet SMS funktioner i Service Bus fra et område af platforme ved hjælp af en effektiv binære protokol. Desuden kan du oprette programmer, der består af komponenter, der er oprettet ved hjælp af en blanding af sprog, strukturer og operativsystemer.

Denne vejledning til vejledning forklarer, hvordan du bruger Service Bus formidlet SMS funktionerne (køer og Udgiv/Abonner emner) fra Java-programmer ved hjælp af de populære Java meddelelse Service (JMS) API standard.

## <a name="get-started-with-service-bus"></a>Introduktion til Service Bus

Denne vejledning forudsætter, at du allerede har en Service Bus navneområde, der indeholder en kø med navnet **Kø1**. Hvis du ikke gør det, derefter kan du [oprette navneområde og kø](service-bus-create-namespace-portal.md) ved hjælp af [Azure-portalen](https://portal.azure.com). Du kan finde flere oplysninger om, hvordan du opretter Service Bus navneområder og køer, se, [hvordan du bruger Service Bus køer](service-bus-dotnet-get-started-with-queues.md).

### <a name="downloading-the-amqp-10-jms-client-library"></a>Hentning af AMQP 1.0 JMS klient-biblioteket

Oplysninger om, hvor du kan hente den nyeste version af biblioteket Apache Qpid JMS AMQP 1.0-klienten, skal du besøge [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html).

Du skal føje følgende fire glas filer fra Apache Qpid JMS AMQP 1.0 fordeling arkiv til Java CLASSPATH, når opbygning og kører JMS programmer med tjenesten Bus:

*    geronimo jms\_1.1\_specifikation 1.0.jar
*    qpid-amqp-1-0-Client-[version].JAR
*    qpid-amqp-1-0-Client-jms-[version].JAR
*    qpid-amqp-1-0-Common-[version].JAR

## <a name="coding-java-applications"></a>Kodningssprog Java-programmer

### <a name="java-naming-and-directory-interface-jndi"></a>Java navngive og Directory Interface (JNDI)

JMS bruger Java navngive og Directory (JNDI) til at oprette en adskillelse mellem logiske navne og fysiske navne. To typer JMS objekter løses ved hjælp af JNDI: ConnectionFactory og Destination. JNDI bruger en udbyder model, hvor du sætter forskellige katalogtjenester for at håndtere navn opløsning opgaver. Apache Qpid JMS AMQP 1.0 bibliotek leveres med en enkel egenskaber er baseret på filer JNDI-udbyder, der er konfigureret ved hjælp af en egenskaber fil af følgende formater:

```
# servicebus.properties – sample JNDI configuration
    
# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[username]:[password]@[namespace].servicebus.windows.net
    
# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="configure-the-connectionfactory"></a>Konfigurere ConnectionFactory

Den post, der bruges til at definere et **ConnectionFactory** i Qpid egenskaber fil JNDI udbyder er følgende format:

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

Hvor **[jndi_name]** og **[ConnectionURL]** har følgende betydning:

- **[jndi_name]**: logiske navnet på ConnectionFactory. Dette er det navn, som vil blive løst i Java programmet på computeren ved hjælp af metoden JNDI IntialContext.lookup().
- **[ConnectionURL]**: en URL-adresse, der giver biblioteket JMS de nødvendige oplysninger til AMQP broker.

Formatet af **ConnectionURL** er som følger:

```
amqps://[username]:[password]@[namespace].servicebus.windows.net
```

Hvor **[navneområde]**, **[brugernavn]** og **[adgangskode]** har følgende betydning:

- **[navneområde]**: The Service Bus navneområde.
- **[brugernavn]**: The Service Bus udstedernavn.
- **[adgangskode]**: URL-kodet form af tasten Service Bus udsteder.

> [AZURE.NOTE] Du skal URL-kodning adgangskoden manuelt. Der findes en nyttig URL-adresse-kodning værktøj på [http://www.w3schools.com/tags/ref_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp).

#### <a name="configure-destinations"></a>Konfigurere destinationer

Den post, der bruges til at definere en destination i provideren Qpid egenskaber fil JNDI er følgende format:

```
queue.[jndi_name] = [physical_name]
```
eller

```
topic.[jndi_name] = [physical_name]
```

Hvor **[jndi\_navn]** og **[fysisk\_navn]** har følgende betydning:

- **[jndi_name]**: det logiske navn på destinationen. Dette er det navn, som vil blive løst i Java programmet på computeren ved hjælp af metoden JNDI IntialContext.lookup().
- **[physical_name]**: navnet på objektet Service Bus, som programmet sender eller modtager meddelelser.

> [AZURE.NOTE] Når du modtager fra et Service Bus emne abonnement, skal det fysiske navn, der er angivet i JNDI navnet på emnet. Abonnementsnavnet er angivet, da robust abonnementet er oprettet i programkode JMS. [Tjenesten Bus AMQP 1.0 udvikler vejledning](service-bus-amqp-dotnet.md) indeholder flere oplysninger om at arbejde med Service Bus emne abonnementer fra JMS.

### <a name="write-the-jms-application"></a>Skriv JMS program

Der er ingen speciel API'er eller indstillinger, der er påkrævet, når du bruger JMS med Service Bus. Der er dog nogle begrænsninger, der skal gennemgås senere. Som med et JMS program er felterne først første obligatorisk konfiguration af JNDI miljøet, lov til at løse en **ConnectionFactory** og destinationer.

#### <a name="configure-the-jndi-initialcontext"></a>Konfigurere JNDI InitialContext

JNDI miljø er konfigureret ved at overføre en hashtabel konfigurationsoplysninger til parametre i klassen javax.naming.InitialContext. To nødvendige elementer i hashtable er klassenavnet den indledende kontekst fabrik og udbyder URL-adresse. Følgende kode viser, hvordan du konfigurerer JNDI miljøet for at bruge filen Qpid egenskaber baseret JNDI udbyder med egenskaber for filen **servicebus.properties**.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
InitialContext context = new InitialContext(env);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>En simpel JMS-program ved hjælp af en Service Bus kø

Følgende eksempel program sender JMS TextMessages til en tjeneste Bus kø med JNDI logiske navnet på kø og modtager meddelelser tilbage.

```
// SimpleSenderReceiver.java
    
import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.Hashtable;
import java.util.Random;
    
public class SimpleSenderReceiver implements MessageListener {
    private static boolean runReceiver = true;
    private Connection connection;
    private Session sendSession;
    private Session receiveSession;
    private MessageProducer sender;
    private MessageConsumer receiver;
    private static Random randomGenerator = new Random();
    
    public SimpleSenderReceiver() throws Exception {
        // Configure JNDI environment
        Hashtable<String, String> env = new Hashtable<String, String>();
        env.put(Context.INITIAL_CONTEXT_FACTORY, 
                   "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
        env.put(Context.PROVIDER_URL, "servicebus.properties");
        Context context = new InitialContext(env);
    
        // Lookup ConnectionFactory and Queue
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        Destination queue = (Destination) context.lookup("QUEUE");
    
        // Create Connection
        connection = cf.createConnection();
    
        // Create sender-side Session and MessageProducer
        sendSession = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
        sender = sendSession.createProducer(queue);
    
        if (runReceiver) {
            // Create receiver-side Session, MessageConsumer,and MessageListener
            receiveSession = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            receiver = receiveSession.createConsumer(queue);
            receiver.setMessageListener(this);
            connection.start();
        }
    }
    
    public static void main(String[] args) {
        try {
    
            if ((args.length > 0) && args[0].equalsIgnoreCase("sendonly")) {
                runReceiver = false;
            }
    
            SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
            System.out.println("Press [enter] to send a message. Type 'exit' + [enter] to quit.");
            BufferedReader commandLine = new java.io.BufferedReader(new InputStreamReader(System.in));
    
            while (true) {
                String s = commandLine.readLine();
                if (s.equalsIgnoreCase("exit")) {
                    simpleSenderReceiver.close();
                    System.exit(0);
                } else {
                    simpleSenderReceiver.sendMessage();
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    
    private void sendMessage() throws JMSException {
        TextMessage message = sendSession.createTextMessage();
        message.setText("Test AMQP message from JMS");
        long randomMessageID = randomGenerator.nextLong() >>>1;
        message.setJMSMessageID("ID:" + randomMessageID);
        sender.send(message);
        System.out.println("Sent message with JMSMessageID = " + message.getJMSMessageID());
    }
    
    public void close() throws JMSException {
        connection.close();
    }
    
    public void onMessage(Message message) {
        try {
            System.out.println("Received message with JMSMessageID = " + message.getJMSMessageID());
            message.acknowledge();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}   
```

### <a name="run-the-application"></a>Køre programmet

Køre programmet giver følgende output:

```
> java SimpleSenderReceiver
Press [enter] to send a message. Type 'exit' + [enter] to quit.
    
Sent message with JMSMessageID = ID:2867600614942270318
Received message with JMSMessageID = ID:2867600614942270318
    
Sent message with JMSMessageID = ID:7578408152750301483
Received message with JMSMessageID = ID:7578408152750301483
    
Sent message with JMSMessageID = ID:956102171969368961
Received message with JMSMessageID = ID:956102171969368961
exit
```

## <a name="cross-platform-messaging-between-jms-and-net"></a>På tværs af platforme messaging mellem JMS og .NET

Denne vejledning viste, hvordan du kan sende og modtage meddelelser til og fra Service Bus ved hjælp af JMS. En af de vigtigste fordele ved AMQP 1.0 er dog, at det giver mulighed for programmer skal anlægges fra komponenter, der er skrevet på et andet sprog med meddelelser, der er udvekslet pålideligt og på samme måde.

Brug af programmet eksempel JMS, der er beskrevet ovenfor og et lignende .NET-program, der tages fra en medfølgende vejledning til, [hvordan du bruger AMQP 1.0 med .NET Service Bus .NET API](service-bus-dotnet-advanced-message-queuing.md), kan du udveksle meddelelser mellem .NET og Java. 

Du kan finde flere oplysninger om detaljerne i tværs af platforme messaging ved hjælp af Service Bus og AMQP 1.0, [Service Bus AMQP 1.0 Developer's Guide](service-bus-amqp-dotnet.md).

### <a name="jms-to-net"></a>JMS til .NET

Til at vise JMS til .NET messaging:

* Starte programmet .NET eksempel uden nogen kommandolinjeparametre.
* Starte programmet Java eksempel med argumentet "sendonly" kommandolinjen. I denne tilstand, programmet ikke modtager meddelelser fra køen, vil det kun sende.
* Tryk på **Enter** nogle gange i konsollen Java-program, hvilket betyder, at fuldføre afsendelsen.
* Disse meddelelser er modtaget af .NET-program.

#### <a name="output-from-jms-application"></a>Output fra JMS program

```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

#### <a name="output-from-net-application"></a>Output fra .NET program

```
> SimpleSenderReceiver.exe  
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

### <a name="net-to-jms"></a>.NET til JMS

Til at vise .NET JMS meddelelser:

* Starte programmet .NET eksempel med argumentet "sendonly" kommandolinjen. I denne tilstand, programmet ikke modtager meddelelser fra køen, vil det kun sende.
* Starte programmet Java eksempel uden nogen kommandolinjeparametre.
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

Der findes følgende begrænsninger, når du bruger JMS over AMQP 1.0 med Service Bus, nemlig:

* Kun én **MessageProducer** eller **MessageConsumer** er tilladt for hver **Session**. Hvis du vil oprette flere **MessageProducers** eller **MessageConsumers** i et program skal du oprette en dedikeret **Session** for hver af dem.
* Genberegnes emne abonnementer understøttes ikke i øjeblikket.
* **MessageSelectors** understøttes ikke i øjeblikket.
* Midlertidige destinationer, det vil sige **TemporaryQueue**, **TemporaryTopic** understøttes ikke i øjeblikket, sammen med **QueueRequestor** og **TopicRequestor** API'er, der bruges.
* Overførte sessioner og distribuerede transaktioner understøttes ikke.

## <a name="summary"></a>Oversigt

I denne artikel viste Sådan bruger du Service Bus messaging funktioner (køer og Udgiv/Abonner emner) fra Java ved hjælp af de populære JMS API og AMQP 1.0.

Du kan også bruge tjenesten Bus AMQP 1.0 fra andre sprog, herunder .NET, C, Python og PHP. Komponenter, der er oprettet ved hjælp af disse sprog kan udveksle meddelelser pålideligt og på fuld pålidelighed ved hjælp af AMQP 1.0 understøtter i Service Bus. Du kan finde yderligere oplysninger finder [Service Bus AMQP 1.0 Developer's Guide](service-bus-amqp-dotnet.md).

## <a name="next-steps"></a>Næste trin

* [Understøttelse af AMQP 1.0 i Azure Service Bus](service-bus-amqp-overview.md)
* [Sådan bruges AMQP 1.0 med Service Bus .NET API](service-bus-dotnet-advanced-message-queuing.md)
* [Tjenesten Bus AMQP 1.0 udvikler vejledning](service-bus-amqp-dotnet.md)
* [Sådan bruger du Service Bus køer](service-bus-dotnet-get-started-with-queues.md)
 
