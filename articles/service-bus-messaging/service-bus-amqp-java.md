<properties 
    pageTitle="Bus og Java med AMQP 1.0 | Microsoft Azure"
    description="Brug af Service Bus fra Java med AMQP"
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/29/2016"
    ms.author="sethm" />

# <a name="use-service-bus-from-java-with-amqp-10"></a>Bruge Service Bus fra Java med AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Java meddelelse Service (JMS) er et standard API til at arbejde med meddelelse rundt programmer på en Java-platform. Microsoft Azure Service Bus er testet med AMQP 1.0 baseret JMS klientbibliotek udviklet af Apache Qpid projektet. Dette bibliotek understøtter fuld JMS 1.1 API og kan bruges sammen med en hvilken som helst AMQP 1.0 kompatibel tjeneste til SMS-beskeder. Dette scenarie understøttes også i [Tjenesten Bus til Windows Server](https://msdn.microsoft.com/library/dn282144.aspx) (lokalt Service Bus). Du kan finde flere oplysninger [AMQP i tjenesten Bus til Windows Server][].

## <a name="download-the-apache-qpid-amqp-10-jms-client-library"></a>Hente biblioteket Apache Qpid AMQP 1.0 JMS klient

Finde oplysninger om at hente den nyeste version af biblioteket Apache Qpid JMS AMQP 1.0-klienten på [http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html](http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html).

Du skal føje følgende fire glas filer fra Apache Qpid JMS AMQP 1.0 fordeling arkiv til Java CLASSPATH, når opbygning og kører JMS programmer med tjenesten Bus:

-   geronimo jms\_1.1\_specifikation-[version] .jar

-   qpid-amqp-1-0-Client-[version].JAR

-   qpid-amqp-1-0-Client-jms-[version].JAR

-   qpid-amqp-1-0-Common-[version].JAR

## <a name="work-with-service-bus-queues-topics-and-subscriptions-from-jms"></a>Arbejde med Service Bus køer, emner og abonnementer fra JMS

### <a name="java-naming-and-directory-interface-jndi"></a>Java navngive og Directory Interface (JNDI)

JMS bruger Java navngive og Directory (JNDI) til at oprette en adskillelse mellem logiske navne og fysiske navne. To typer JMS objekter løses ved hjælp af JNDI: **ConnectionFactory** og **Destination**. JNDI bruger en udbyder model, hvor du sætter forskellige katalogtjenester for at håndtere navn opløsning opgaver. Biblioteket Apache Qpid JMS AMQP 1.0 leveres med en enkel egenskaber er baseret på filer JNDI-udbyder, der er konfigureret ved hjælp af en tekstfil.

Qpid egenskaber fil JNDI udbyder er konfigureret ved hjælp af en egenskaber fil i følgende format:

```
# servicebus.properties – sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCONNECTIONFACTORY = amqps://[username]:[password]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
topic.TOPIC = topic1
queue.QUEUE = queue1
```

#### <a name="configure-the-connection-factory"></a>Konfigurere forbindelse factory

Den post, der bruges til at definere et **ConnectionFactory** i Qpid egenskaber fil JNDI udbyder er følgende format:

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

Hvor `[jndi\_name]` og `[ConnectionURL]` har følgende betydning:

| Navn            | Betydning                                                                                                                                    |   |   |   |   |
|-----------------|--------------------------------------------------------------------------------------------------------------------------------------------|---|---|---|---|
| `[jndi\_name]`    | Logiske navnet på den forbindelse fabrik. Dette navn er blevet løst i Java-program ved hjælp af JNDI `IntialContext.lookup()` metode. |   |   |   |   |
| `[ConnectionURL]` | En URL-adresse, der giver biblioteket JMS de nødvendige oplysninger til AMQP broker.                                                      |   |   |   |   |

Formatet af forbindelsen URL-adressen er som følger:

```
amqps://[username]:[password]@[namespace].servicebus.windows.net
```

Hvor `[namespace]`, `[username]`, og `[password]` har følgende betydning:

| Navn          | Betydning                                                                        |   |   |   |   |
|---------------|--------------------------------------------------------------------------------|---|---|---|---|
| `[namespace]` | Navneområdet Service Bus der fås fra [Azure-portalen][].                      |   |   |   |   |
| `[username]`  | Tjenesten Bus SAS vigtige navnet der fås fra [Azure-portalen][].                    |   |   |   |   |
| `[password]`  | URL-kodet form for tasten Service Bus SAS, som fås fra [Azure-portalen][]. |   |   |   |   |

> [AZURE.NOTE] Du skal URL-kodning adgangskoden manuelt. Der findes en nyttig URL-adressen kodning værktøj på [http://www.w3schools.com/tags/ref_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp).

Eksempelvis hvis oplysninger, der er hentet fra er portalen som følger:

| Namespace:   | test.servicebus.Windows.NET                  |
|--------------|----------------------------------------------|
| Navn på udsteder: | RootManageSharedAccessKey                                        |
| Udsteder nøgle:  | ABCDEFG |

Derefter for at definere et **ConnectionFactory** objekt med navnet `SBCONNECTIONFACTORY`, strengen konfiguration ville være på følgende måde:

```
connectionfactory.SBCONNECTIONFACTORY = amqps://RootManageSharedAccessKey:abcdefg@test.servicebus.windows.net
```

#### <a name="configure-destinations"></a>Konfigurere destinationer

Den post, der definerer en destination i provideren Qpid egenskaber fil JNDI er følgende format:

```
queue.[jndi_name] = [physical_name]
topic.[jndi_name] = [physical_name]
```

Hvor `[jndi\_name]` og `[physical\_name]` har følgende betydning:

| Navn              | Betydning                                                                                                                                  |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| `[jndi\_name]`    | Det logiske navn på destinationen. Dette er navnet skal oversættes i Java-program ved hjælp af JNDI `IntialContext.lookup()` metode. |
| `[physical\name]` | Navnet på objektet Service Bus, som programmet sender eller modtager meddelelser.                                                  |

Bemærk følgende:

- Den `[physical\name]` værdien kan være en Service Bus kø eller emne.
- Når du modtager fra et Service Bus emne abonnement, skal det fysiske navn, der er angivet i JNDI navnet på emnet. Abonnementsnavnet er angivet, da robust abonnementet er oprettet i programkode JMS.
- Det er også muligt at behandle en Service Bus emne abonnement som en JMS kø. Der er flere fordele ved denne fremgangsmåde: den samme modtager kode kan bruges til køer og emne abonnementer, og alle adresseoplysninger (emne og abonnement navnene) externalized i filen egenskaber.
- Hvis et Service Bus emne abonnement som en JMS kø, angivelsen i Egenskaber for fil skal være i formularen: `queue.[jndi\_name] = [topic\_name]/Subscriptions/[subscription\_name]`. |

Hvis du vil angive en logisk JMS destination med navnet "Emne", der er tilknyttet et Service Bus emne med navnet "emne1", er posten i filen egenskaber på følgende måde:

```
topic.TOPIC = topic1
```

### <a name="send-messages-using-jms"></a>Sende meddelelser ved hjælp af JMS

Følgende kode viser, hvordan du kan sende en meddelelse til en tjeneste Bus emne. Det antages, `SBCONNECTIONFACTORY` og `TOPIC` er defineret i en **servicebus.properties** konfigurationsfil, som beskrevet i forrige afsnit.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, 
        "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
 
InitialContext context = new InitialContext(env); 
 
ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCONNECTIONFACTORY");
Topic topic = (Topic) context.lookup("TOPIC");
Connection connection = cf.createConnection();
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
MessageProducer producer = session.createProducer(topic);
TextMessage message = session.createTextMessage("This is a text string"); 
producer.send(message);
```

### <a name="receive-messages-using-jms"></a>Modtage meddelelser ved hjælp af JMS

Følgende kode viser `how` at modtage en meddelelse fra en tjeneste Bus emne abonnement. Det antages, `SBCONNECTIONFACTORY` og emne er defineret i en **servicebus.properties** konfigurationsfil, som beskrevet i forrige afsnit. Det antages også, at Abonnementsnavnet er `subscription1`.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, 
        "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
 
InitialContext context = new InitialContext(env);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCONNECTIONFACTORY");
Topic topic = (Topic) context.lookup("TOPIC");
Connection connection = cf.createConnection();
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
TopicSubscriber subscriber = session.createDurableSubscriber(topic, "subscription1");
connection.start();
Message message = messageConsumer.receive();
```

### <a name="guidelines-for-building-robust-applications"></a>Retningslinjer for at opbygge robuste programmer

Specifikationen JMS definerer, hvordan der skal skrives undtagelse kontrakten af API metoder og programkode til at håndtere sådanne undtagelser. Her er nogle andre punkter du bør overveje om håndtering af undtagelse:

-   Registrere en **ExceptionListener** JMS forbindelsen ved hjælp af **connection.setExceptionListener**. Dette gør det muligt for en klient have besked om et problem asynkront. Denne meddelelse er særligt vigtig for forbindelser, der kun forbruger meddelelser, som de ville have nogen anden måde at lære, deres forbindelse mislykkes. **ExceptionListener** kaldes, hvis der opstår et problem med den underliggende AMQP forbindelse, session eller link. I dette tilfælde skal programmet Genopret **JMS forbindelse**, **Session**, **MessageProducer** og **MessageConsumer** objekter fra bunden.

-   For at bekræfte, at en meddelelse er blevet sendt fra en **MessageProducer** til en tjeneste Bus enhed, skal du sikre, at programmet er konfigureret med den **qpid.sync\_publicere** system egenskabssæt. Du kan gøre dette ved at starte programmet med den **-Dqpid.sync\_publicere = true** Java VM grupperede indstilling på kommandolinjen, når du starter programmet. Hvis denne indstilling konfigurerer biblioteket til at returnere ikke fra send opkaldet, indtil bekræftelse har modtaget, som meddelelsen er blevet accepteret af Service Bus. Hvis der opstår et problem under handlingen send, opstår der en **JMSException** . Der er to mulige årsager: 
    1. Hvis problemet er på grund af Service Bus afvise bestemt mailen sendes, hæves en **MessageRejectedException** undtagelse. Denne fejl er enten forbigående, eller på grund af problemer med meddelelsen. Den anbefalede fremgangsmåde er, at gøre flere forsøger at prøve igen med nogle tilbage fra logik. Hvis problemet fortsætter, skal meddelelsen annulleret med en fejl, der er logget lokalt. Det er ikke nødvendigt at genskabe **JMS forbindelse**, **Session**eller **MessageProducer** objekterne i problemet. 
    2. Hvis problemet er på grund af Service Bus lukke linket AMQP, hæves en **InvalidDestinationException** undtagelse. Det kan være på grund af et midlertidigt problem eller på grund af objektet meddelelse slettes. I begge tilfælde, skal objekterne **JMS dataforbindelse** **Session**og **MessageProducer** genskabes. Hvis fejlsituationen var midlertidige, derefter bliver denne handling til sidst vellykket. Hvis objektet er blevet slettet, bliver fejl permanent.

## <a name="messaging-between-net-and-jms"></a>Messaging mellem .NET og JMS

### <a name="message-bodies"></a>Meddelelsernes brødtekst

JMS definerer fem forskellige meddelelsestyper: **BytesMessage**, **MapMessage**, **ObjectMessage**, **StreamMessage**og **TextMessage**. Tjenesten Bus .NET API er en enkelt meddelelsestype, [BrokeredMessage][].

#### <a name="jms-to-service-bus-net-api"></a>JMS til Service Bus .NET API

De følgende afsnit viser, hvordan du optager meddelelser på hver af JMS meddelelsestyper fra .NET. Et eksempel på **ObjectMessage** er ikke inkluderet, som brødteksten i en **ObjectMessage** indeholder et serialiserbar objekt i programmeringssproget Java, som ikke er interpretable af en .NET-program.

##### <a name="bytesmessage"></a>BytesMessage

Følgende kode viser, hvordan du kan bruge brødteksten i et **BytesMessage** objekt ved hjælp af tjenesten Bus .NET API'er.

```
Stream stream = message.GetBody<Stream>();
int streamLength = (int)stream.Length;

byte[] byteArray = new byte[streamLength];
stream.Read(byteArray, 0, streamLength);

Console.WriteLine("Length = " + streamLength);
for (int i = 0; i < stream.Length; i++)
{
  Console.Write("[" + (sbyte) byteArray[i] + "]");
}
```

##### <a name="mapmessage"></a>MapMessage

Følgende kode viser, hvordan du kan bruge brødteksten i et **MapMessage** objekt ved hjælp af tjenesten Bus .NET API'er. Denne kode gentager listen over elementer i kortet, viser navnet på og værdien for hvert element.

```
Dictionary<String, Object> dictionary = message.GetBody<Dictionary<String, Object>>();

foreach (String mapItemName in dictionary.Keys)
{
  Object mapItemValue = null;
  if (dictionary.TryGetValue(mapItemName, out mapItemValue))
  {
    Console.WriteLine(mapItemName + ":" + mapItemValue);
  }
}
```

##### <a name="streammessage"></a>StreamMessage

Følgende kode viser, hvordan du kan bruge brødteksten i et **StreamMessage** objekt ved hjælp af tjenesten Bus .NET API'er. Denne kode viser hvert element fra stream sammen med deres type.

```
List<Object> list = message.GetBody<List<Object>>();

foreach (Object item in list)
{
  Console.WriteLine(item + " (" + item.GetType() + ")");
}
```

##### <a name="textmessage"></a>TextMessage

Følgende kode viser, hvordan du kan bruge brødteksten i et **TextMessage** objekt ved hjælp af tjenesten Bus .NET API'er. Denne kode viser den tekststreng, der er indeholdt i brødteksten i meddelelsen.

```
Console.WriteLine("Text: " + message.GetBody<String>());
```

#### <a name="service-bus-net-apis-to-jms"></a>Tjenesten Bus .NET API'er til JMS

De følgende afsnit viser, hvordan et .NET-program kan oprette en meddelelse, der er modtaget i JMS i hver af de forskellige typer af JMS meddelelse. Et eksempel på **ObjectMessage** er ikke inkluderet, som brødteksten i en **ObjectMessage** indeholder et serialiserbar objekt i programmeringssproget Java, som ikke er interpretable af en .NET-program.

##### <a name="bytesmessage"></a>BytesMessage

Følgende kode viser, hvordan du opretter et [BrokeredMessage][] objekt i .NET, der modtages af en JMS klient som en **BytesMessage**.

```
byte[] bytes = { 33, 12, 45, 33, 12, 45, 33, 12, 45, 33, 12, 45 };
message = new BrokeredMessage(bytes);
```

##### <a name="streammessage"></a>StreamMessage

Følgende kode viser, hvordan du opretter et [BrokeredMessage][] objekt i .NET, der modtages af en JMS klient som en **StreamMessage**.

```
List<Object> list = new List<Object>();
list.Add("String 1");
list.Add("String 2");
list.Add("String 3");
list.Add((double)3.14159);
message = new BrokeredMessage(list);
```

##### <a name="textmessage"></a>TextMessage

Følgende kode viser hvordan du kan bruge brødteksten i en **TextMessage** ved hjælp af tjenesten Bus .NET API. Denne kode viser den tekststreng, der er indeholdt i brødteksten i meddelelsen.

```
message = new BrokeredMessage("this is a text string");
```

### <a name="application-properties"></a>Egenskaber for tjenesteprogram

####<a name="jms-to-service-bus-net-apis"></a>JMS til at vedligeholde Bus .NET API'er

JMS meddelelser understøtter programmet Egenskaber for følgende typer: **boolesk**, **byte**, **kort**, **heltal**, **lang**, **flydende**, **dobbelte**og **streng**. Følgende Java-kode viser, hvordan til at indstille egenskaberne for en meddelelse ved hjælp af de forskellige egenskab.

```
message.setBooleanProperty("TestBoolean", true); 
message.setByteProperty("TestByte", (byte) 33); 
message.setDoubleProperty("TestDouble", 3.14159D); 
message.setFloatProperty("TestFloat", 3.13159F); 
message.setIntProperty("TestInt", 100); 
message.setStringProperty("TestString", "Service Bus");
```

Egenskaber for tjenesteprogram meddelelse er medtaget i samlingen **Properties** for [BrokeredMessage][]i tjenesten Bus .NET API'er. Følgende kode viser, hvordan du kan læse programmet egenskaberne for en meddelelse, der er modtaget fra en JMS-klient.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}
```

Tabellen nedenfor viser, hvordan typerne JMS egenskaben knyttes til .NET egenskaben datatyper.

| Type af JMS egenskab | .NET egenskabstype |
|-------------------|--------------------|
| Byte              | SByte              |
| Heltal           | heltal                |
| Flydende             | slæk              |
| Dobbelt            | dobbelt             |
| Boolesk værdi           | Boolesk               |
| Streng            | streng             |

Typen [BrokeredMessage][] understøtter programmet Egenskaber for følgende typer: **byte**, **sbyte**, **tegn**, **kort**, **Double**, **int**, **uint**, **lang**, **ulong**, **flydende**, **dobbelte**, **decimal**, **boolesk**, **Guid**, **streng**, **Uri**, **DateTime**, **DateTimeOffset**, og **TimeSpan**. Følgende kode i .NET viser, hvordan til at indstille egenskaberne i et [BrokeredMessage][] objekt ved hjælp af de forskellige egenskab.

```
message.Properties["TestByte"] = (byte)128;
message.Properties["TestSbyte"] = (sbyte)-22;
message.Properties["TestChar"] = (char) 'X';
message.Properties["TestShort"] = (short)-12345;
message.Properties["TestUshort"] = (ushort)12345;
message.Properties["TestInt"] = (int)-100;
message.Properties["TestUint"] = (uint)100;
message.Properties["TestLong"] = (long)-12345;
message.Properties["TestUlong"] = (ulong)12345;
message.Properties["TestFloat"] = (float)3.14159;
message.Properties["TestDouble"] = (double)3.14159;
message.Properties["TestDecimal"] = (decimal)3.14159;
message.Properties["TestBoolean"] = true;
message.Properties["TestGuid"] = Guid.NewGuid();
message.Properties["TestString"] = "Service Bus";
message.Properties["TestUri"] = new Uri("http://www.bing.com");
message.Properties["TestDateTime"] = DateTime.Now;
message.Properties["TestDateTimeOffSet"] = DateTimeOffset.Now;
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
```

Følgende Java-kode viser hvordan du kan læse programmet egenskaberne for en meddelelse, der er modtaget fra en tjeneste Bus .NET-klient.

```
Enumeration propertyNames = message.getPropertyNames(); 
while (propertyNames.hasMoreElements()) 
{ 
  String name = (String) propertyNames.nextElement(); 
  Object value = message.getObjectProperty(name); 
  System.out.println(name + ": " + value + " (" + value.getClass() + ")"); 
}
```

Tabellen nedenfor viser, hvordan typerne .NET egenskaben knyttes til JMS egenskaben datatyper.

| .NET egenskabstype | Type af JMS egenskab | Noter                                                                                                                                                               |
|--------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte               | UnsignedByte      | -                                                                                                                                                                      |
| SByte              | Byte              | -                                                                                                                                                                     |
| tegn               | Tegn         | -                                                                                                                                                                     |
| kort              | Kort             | -                                                                                                                                                                     |
| Double             | UnsignedShort     | -                                                                                                                                                                     |
| heltal                | Heltal           | -                                                                                                                                                                     |
| UInt               | UnsignedInteger   | -                                                                                                                                                                     |
| lang               | Lang              | -                                                                                                                                                                     |
| ulong              | UnsignedLong      | -                                                                                                                                                                     |
| slæk              | Flydende             | -                                                                                                                                                                     |
| dobbelt             | Dobbelt            | -                                                                                                                                                                     |
| decimal            | BigDecimal        | -                                                                                                                                                                     |
| Boolesk               | Boolesk værdi           | -                                                                                                                                                                     |
| GUID               | UUID              | -                                                                                                                                                                     |
| streng             | Streng            | -                                                                                                                                                                     |
| Dato og klokkeslæt           | Dato              | -                                                                                                                                                                     |
| DateTimeOffset     | DescribedType     | DateTimeOffset.UtcTicks, der er knyttet til AMQP type:<type name=”datetime-offset” class=restricted source=”long”><descriptor name=”com.microsoft:datetime-offset” /></type> |
| TimeSpan           | DescribedType     | Timespan.Ticks, der er knyttet til AMQP type:<type name=”timespan” class=restricted source=”long”><descriptor name=”com.microsoft:timespan” /></type>                        |
| URI                | DescribedType     | Uri.AbsoluteUri, der er knyttet til AMQP type:<type name=”uri” class=restricted source=”string”><descriptor name=”com.microsoft:uri” /></type>                               |

### <a name="standard-headers"></a>Standard sidehoveder

Følgende tabeller viser, hvordan JMS Standard overskrifterne og [BrokeredMessage][] standardegenskaberne er tilknyttet ved hjælp af AMQP 1.0.

#### <a name="jms-to-service-bus-net-apis"></a>JMS til at vedligeholde Bus .NET API'er

| JMS              | Service Bus .NET               | Noter                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|------------------|--------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| JMSCorrelationID | Message.CorrelationID          | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| JMSDeliveryMode  | Ikke tilgængelig i øjeblikket        | Service Bus understøtter kun robust meddelelser. for eksempel DeliveryMode.PERSISTENT, uanset hvad der er angivet.                                                                                                                                                                                                                                                                                                                                                         |
| JMSDestination   | Message.To                     | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| JMSExpiration    | Meddelelse. TimeToLive            | Konvertering                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| JMSMessageID     | Message.MessageID              | Som standard kodes JMSMessageID i binær form i AMQP meddelelsen. Modtager binære meddelelse-id konverterer biblioteket .NET-klienten til en streng, der repræsenterer baseret på Unicode-værdierne for bytene. Hvis du vil skifte biblioteket JMS for at bruge streng meddelelse id'er, skal du tilføje den "binær messageid = falsk" streng, der skal forespørgselsparametre af JNDI ConnectionURL. For eksempel: “amqps://[username]:[password]@[namespace].servicebus.windows.net? binær messageid = falsk ". |
| JMSPriority      | Ikke tilgængelig i øjeblikket        | Service Bus understøtter ikke meddelelsesprioritet.                                                                                                                                                                                                                                                                                                                                                                                                                             |
| JMSRedelivered   | Ikke tilgængelig i øjeblikket        | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| JMSReplyTo       | Meddelelse. ReplyTo               | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| JMSTimestamp     | Message.EnqueuedTimeUtc        | Konvertering                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| JMSType          | Message.Properties ["jms-type"] | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |

#### <a name="service-bus-net-apis-to-jms"></a>Tjenesten Bus .NET API'er til JMS

| Service Bus .NET        | JMS              | Noter                   |
|-------------------------|------------------|-------------------------|
| ContentType             | -                  | Ikke tilgængelig i øjeblikket |
| CorrelationId           | JMSCorrelationID | -                         |
| EnqueuedTimeUtc         | JMSTimestamp     | Konvertering              |
| Etiket                   | i/t.              | Ikke tilgængelig i øjeblikket |
| MessageId               | JMSMessageID     | -                         |
| ReplyTo                 | JMSReplyTo       | -                         |
| ReplyToSessionId        | i/t.              | Ikke tilgængelig i øjeblikket |
| ScheduledEnqueueTimeUtc | i/t.              | Ikke tilgængelig i øjeblikket |
| Session-id               | i/t.              | Ikke tilgængelig i øjeblikket |
| TimeToLive              | JMSExpiration    | Konvertering              |
| Til                      | JMSDestination   | -                         |

## <a name="unsupported-features-and-restrictions"></a>Ikke-understøttede funktioner og begrænsninger

Der findes følgende begrænsninger, når du bruger JMS over AMQP 1.0 med Service Bus:

-   Kun én **MessageProducer** eller **MessageConsumer** er tilladt for hver session. Hvis du vil oprette flere **MessageProducer** eller **MessageConsumer** objekter i et program, skal du oprette dedikeret sessioner for hver af dem.

-   Genberegnes emne abonnementer understøttes ikke i øjeblikket.

-   **MessageSelector** objekter understøttes ikke.

-   Midlertidige destinationer; for eksempel understøttes **TemporaryQueue** eller **TemporaryTopic**, ikke, sammen med **QueueRequestor** og **TopicRequestor** API'er, der bruges.

-   Overførte sessioner understøttes ikke.

-   Distribuerede transaktioner understøttes ikke.

## <a name="next-steps"></a>Næste trin

Klar til at få mere at vide? Besøg følgende links:

- [Tjenesten Bus AMQP oversigt]
- [AMQP i Service Bus til Windows Server]

[AMQP i Service Bus til Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx

[Tjenesten Bus AMQP oversigt]: service-bus-amqp-overview.md
[Azure-portalen]: https://portal.azure.com
