<properties 
    pageTitle="Bus og PHP med AMQP 1.0 | Microsoft Azure"
    description="Brug af Service Bus fra PHP med AMQP."
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

# <a name="using-service-bus-from-php-with-amqp-10"></a>Brug af Service Bus fra PHP med AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Proton PHP er en PHP sprog binding til Proton-C Det vil sige, implementeres Proton PHP som en slikpapir rundt om et program, der er implementeret i C.

## <a name="downloading-the-proton-client-library"></a>Hentning af Proton klient-biblioteket

Du kan hente Proton-C og dens tilknyttede bindinger (herunder PHP) fra [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). Overførslen er i kilde-kode formular. For at oprette koden, ved at følge vejledningen i hentede pakken.

> [AZURE.IMPORTANT] SSL-understøttelse i Proton-C er kun tilgængelig for Linux-operativsystemer samtidig med denne skrivning. Da Azure Service Bus kræver brug af SSL, kan Proton-C (og sprog bindinger) kun bruges til at få adgang til tjenesten Bus fra Linux på nuværende tidspunkt. Aktivere Proton-C med SSL på Windows der arbejdes i øjeblikket, så besøg tilbage ofte efter opdateringer.

## <a name="working-with-service-bus-queues-topics-and-subscriptions-from-php"></a>Arbejde med Service Bus køer, emner og abonnementer fra PHP

Følgende kode viser, hvordan til at sende og modtage meddelelser fra en tjeneste Bus messaging enhed.

### <a name="sending-messages-using-proton-php"></a>Afsendelse af meddelelser med Proton PHP

Følgende kode viser, hvordan til at sende en meddelelse til en tjeneste Bus messaging enhed.

```
$messenger = new Messenger();
$message = new Message();
$message->address = "amqps://[keyname]:[password]@[namespace].servicebus.windows.net/[entity]";

$message->body = "This is a text string";
$messenger->put($message);
$messenger->send();
```

### <a name="receiving-messages-using-proton-php"></a>Modtage meddelelser ved hjælp af Proton PHP

Følgende kode viser, hvordan du modtager en meddelelse fra en tjeneste Bus messaging enhed.

```
$messenger = new Messenger();
$address = "amqps://[keyname]:[password]@[namespace].servicebus.windows.net/[entity]";
$messenger->subscribe($address);

$messenger->start();
$messenger->recv(1);

if($messenger->incoming())
{
   $message = new Message();
   $messenger->get($message);      
}

$messenger->stop();
```

## <a name="messaging-between-net-and-proton-php"></a>Messaging mellem .NET og Proton PHP

### <a name="application-properties"></a>Egenskaber for tjenesteprogram

#### <a name="protonphp-to-service-bus-net-apis"></a>ProtonPHP til at vedligeholde Bus .NET API'er

Proton PHP meddelelser understøtter programmet Egenskaber for følgende typer: **heltal**, **dobbelt**, **boolesk**, **streng**og **objekt**. Følgende PHP kode viser, hvordan du kan indstille egenskaber på en meddelelse ved hjælp af de forskellige egenskab.

```
$message->properties["TestInt"] = 1;    
$message->properties["TestDouble"] = 1.5;      
$message->properties["TestBoolean"] = False;
$message->properties["TestString"] = "Service Bus";    
$message->properties["TestObject"] = new UUID("1234123412341234");   
```

Egenskaber for tjenesteprogram meddelelse er medtaget i samlingen **Properties** for [BrokeredMessage][]i tjenesten Bus .NET API'er. Følgende kode viser, hvordan du kan læse programmet egenskaberne for en meddelelse, der er modtaget fra en PHP klient.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}if (message.Properties.Keys.Count > 0)
{
foreach (string name in message.Properties.Keys)
{
  Object value = message.Properties[name];
  Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
}
Console.WriteLine();
}
```

Den følgende tabel knytter typerne PHP egenskab til .NET egenskaben datatyper.

| Type af PHP egenskab | .NET egenskabstype |
|-------------------|--------------------|
| heltal           | heltal                |
| dobbelt            | dobbelt             |
| Boolesk værdi           | Boolesk               |
| streng            | streng             |
| objekt            | Objekt             |

#### <a name="service-bus-net-apis-to-php"></a>Tjenesten Bus .NET API'er til PHP

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

Følgende PHP kode viser, hvordan du kan læse programmet egenskaberne for en meddelelse, der er modtaget fra en tjeneste Bus .NET-klient.

```
if ($message->properties != null)
{
  foreach($message->properties as $key => $value)
  {
    printf("-- %s : %s (%s) \n", $key, $value, gettype($value));                       
  }         
}
```

Den følgende tabel knytter typerne .NET egenskab til typerne PHP egenskab.

| .NET egenskabstype | Type af PHP egenskab | Noter                                                                                                                                                               |
|--------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte               | heltal           | -                                                                                                                                                                     |
| SByte              | heltal           | -                                                                                                                                                                     |
| tegn               | Tegn              | Proton PHP klasse                                                                                                                                                    |
| kort              | heltal           | -                                                                                                                                                                     |
| Double             | heltal           | -                                                                                                                                                                     |
| heltal                | heltal           | -                                                                                                                                                                     |
| UInt               | Heltal           | -                                                                                                                                                                     |
| lang               | heltal           | -                                                                                                                                                                     |
| ulong              | heltal           | -                                                                                                                                                                     |
| slæk              | dobbelt            | -                                                                                                                                                                     |
| dobbelt             | dobbelt            | -                                                                                                                                                                     |
| decimal            | streng            | Decimal understøttes ikke i øjeblikket med Proton.                                                                                                                     |
| Boolesk               | Boolesk værdi           | -                                                                                                                                                                     |
| GUID               | UUID              | Proton PHP klasse                                                                                                                                                    |
| streng             | streng            | -                                                                                                                                                                     |
| Dato og klokkeslæt           | heltal           | -                                                                                                                                                                     |
| DateTimeOffset     | DescribedType     | DateTimeOffset.UtcTicks, der er knyttet til AMQP type:<type name="datetime-offset" class=restricted source="long"><descriptor name="com.microsoft:datetime-offset" /></type> |
| TimeSpan           | DescribedType     | Timespan.Ticks, der er knyttet til AMQP type:<type name="timespan" class=restricted source="long"><descriptor name="com.microsoft:timespan" /></type>                        |
| URI                | DescribedType     | Uri.AbsoluteUri, der er knyttet til AMQP type:<type name="uri" class=restricted source="string"><descriptor name="com.microsoft:uri" /></type>                               |

### <a name="standard-properties"></a>Standardegenskaber

De følgende tabeller viser tilknytningen mellem Proton PHP standard meddelelsesegenskaber og de [BrokeredMessage][] standardformularen til meddelelser.

| Proton PHP           | Service Bus .NET         | Noter                                                    |
|----------------------|--------------------------|----------------------------------------------------------|
| Robust              | i/t.                      | Service Bus understøtter kun robust meddelelser.          |
| Prioritet             | i/t.                      | Service Bus understøtter kun en enkelt meddelelsesprioritet. |
| TTL                  | Message.TimeToLive       | Konvertering, Proton PHP TTL er defineret i millisekunder.   |
| første\_erhvervet      | -                          | -                                                          |
| levering\_Tæl      | -                          | -                                                          |
| Id                   | Message.Id               | -                                                          |
| bruger\_id             | -                          | -                                                          |
| Adresse              | Message.To               | -                                                          |
| Emne              | Message.Label            | -                                                          |
| svar\_til            | Message.ReplyTo          | -                                                          |
| Korrelations\_id      | Message.CorrelationId    | -                                                          |
| indhold\_type        | Message.ContentType      | -                                                          |
| indhold\_kode    | i/t.                      | -                                                          |
| udløbet\_tid         | Message.ExpiresAtUTC     | -                                                          |
| oprettelse af\_tid       | i/t.                      | -                                                          |
| gruppen\_id            | Message.SessionId        | -                                                          |
| gruppen\_sekvens      | -                          | -                                                          |
| svar\_til\_gruppe\_id | Message.ReplyToSessionId | -                                                          |
| Formatér               | i/t.                      | -

#### <a name="service-bus-net-apis-to-proton-php"></a>Tjenesten Bus .NET API'er til Proton PHP

| Service Bus .NET        | Proton PHP                                             | Noter                                                  |
|-------------------------|--------------------------------------------------------|--------------------------------------------------------|
| ContentType             | Meddelelse -\>indhold\_type                                | -                                                        |
| CorrelationId           | Meddelelse -\>korrelations\_id                              | -                                                        |
| EnqueuedTimeUtc         | Meddelelse -\>anmærkninger [x-vælge-sat i kø-klokkeslæt]             | -                                                        |
| Etiket                   | Meddelelse -\>emne                                      | -                                                        |
| MessageId               | Meddelelse -\>id                                           | -                                                        |
| ReplyTo                 | Meddelelse -\>svar\_til                                    | -                                                        |
| ReplyToSessionId        | Meddelelse -\>svar\_til\_gruppe\_id                         | -                                                        |
| ScheduledEnqueueTimeUtc | Meddelelse -\>anmærkninger ["x-vælge-planlagt-enqueue-klokkeslætsformat"] | -                                                        |
| Session-id               | Meddelelse -\>gruppe\_id                                    | -                                                        |
| TimeToLive              | Meddelelse -\>ttl                                          | Konvertering, Proton PHP TTL er defineret i millisekunder. |
| Til                      | Meddelelse -\>adresse                                      | -                                                        |

## <a name="next-steps"></a>Næste trin

Klar til at få mere at vide? Besøg følgende links:

- [Tjenesten Bus AMQP oversigt]
- [AMQP i Service Bus til Windows Server]


[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[AMQP i Service Bus til Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
[Tjenesten Bus AMQP oversigt]: service-bus-amqp-overview.md
