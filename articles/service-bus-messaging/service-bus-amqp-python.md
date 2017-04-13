<properties 
    pageTitle="Bus og Python med AMQP 1.0 | Microsoft Azure"
    description="Brug af Service Bus fra Python med AMQP."
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

# <a name="using-service-bus-from-python-with-amqp-10"></a>Brug af Service Bus fra Python med AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Proton Python er en Python sprog binding til Proton-C Det vil sige, implementeres Proton Python som en slikpapir rundt om et program, der er implementeret i C.

## <a name="download-the-proton-client-library"></a>Hente biblioteket Proton klient

Du kan hente Proton-C og dens tilknyttede bindinger (herunder Python) fra [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). Overførslen er i kilde-kode formular. For at opbygge koden skal du følge vejledningen indeholdt i den hentede pakke.

Bemærk, at afkrydsningsfeltet SSL samtidig med denne skriver understøtter i Proton-C er kun tilgængelig for Linux-operativsystemer. Da Azure Service Bus kræver brug af SSL, kan Proton-C (og sprog bindinger) kun bruges til at få adgang til tjenesten Bus fra Linux på nuværende tidspunkt. Arbejde til at aktivere Proton-C med SSL på Windows er foregår så besøg tilbage ofte efter opdateringer.

## <a name="working-with-service-bus-queues-topics-and-subscriptions-from-python"></a>Arbejde med Service Bus køer, emner og abonnementer fra Python

Følgende kode viser, hvordan til at sende og modtage meddelelser fra en tjeneste Bus messaging enhed.

### <a name="send-messages-using-proton-python"></a>Sende meddelelser ved hjælp af Proton Python

Følgende kode viser, hvordan til at sende en meddelelse til en tjeneste Bus messaging enhed.

```
messenger = Messenger()
message = Message()
message.address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"

message.body = u"This is a text string"
messenger.put(message)
messenger.send()
```

### <a name="receive-messages-using-proton-python"></a>Modtage meddelelser ved hjælp af Proton Python

Følgende kode viser, hvordan du modtager en meddelelse fra en tjeneste Bus messaging enhed.

```
messenger = Messenger()
address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"
messenger.subscribe(address)

messenger.start()
messenger.recv(1)
message = Message()
if messenger.incoming:
      messenger.get(message)
messenger.stop()
```

## <a name="messaging-between-net-and-proton-python"></a>Messaging mellem .NET og Proton Python

### <a name="application-properties"></a>Egenskaber for tjenesteprogram

#### <a name="proton-python-to-service-bus-net-apis"></a>Proton-Python til at vedligeholde Bus .NET API'er

Proton Python meddelelser understøtter programmet Egenskaber for følgende typer: **int** **lang** **slæk**, **uuid**, **boolesk**, og **streng**. Følgende Python kode viser, hvordan du kan indstille egenskaber på en meddelelse ved hjælp af de forskellige egenskab.

```
message.properties[u"TestString"] = u"This is a string"    
message.properties[u"TestInt"] = 1
message.properties[u"TestLong"] = 1000L
message.properties[u"TestFloat"] = 1.5    
message.properties[u"TestGuid"] = uuid.uuid1()    
```

Egenskaber for tjenesteprogram meddelelse er medtaget i samlingen **Properties** for [BrokeredMessage][]i tjenesten Bus .NET API. Følgende kode viser, hvordan du kan læse programmet egenskaberne for en meddelelse, der er modtaget fra en Python klient.

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

Den følgende tabel knytter typerne Python egenskab til .NET egenskaben datatyper.

| Type af Python egenskab | .NET egenskabstype |
|----------------------|--------------------|
| heltal                  | heltal                |
| slæk                | dobbelt             |
| lang                 | Int64              |
| UUID                 | GUID               |
| Boolesk                 | Boolesk               |
| streng               | streng             |

#### <a name="service-bus-net-apis-to-proton-python"></a>Tjenesten Bus .NET API'er til Proton Python

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
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
```

Følgende Python kode viser, hvordan du kan læse programmet egenskaberne for en meddelelse, der er modtaget fra en tjeneste Bus .NET-klient.

```
if message.properties != None:
   for k,v in message.properties.items():         
         print "--   %s : %s (%s)" % (k, str(v), type(v))         
```

Den følgende tabel knytter typerne .NET egenskab til typerne Python egenskab.

| .NET egenskabstype | Type af Python egenskab | Noter                                                                                                                                                               |
|--------------------|----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte               | heltal                  | -                                                                                                                                                                     |
| SByte              | heltal                  | -                                                                                                                                                                     |
| tegn               | tegn                 | Proton Python klasse                                                                                                                                                 |
| kort              | heltal                  | -                                                                                                                                                                     |
| Double             | heltal                  | -                                                                                                                                                                     |
| heltal                | heltal                  | -                                                                                                                                                                     |
| UInt               | heltal                  | -                                                                                                                                                                     |
| lang               | heltal                  | -                                                                                                                                                                     |
| ulong              | lang                 | Proton Python klasse                                                                                                                                                 |
| slæk              | slæk                | -                                                                                                                                                                     |
| dobbelt             | slæk                | -                                                                                                                                                                     |
| decimal            | Streng               | Decimal understøttes ikke i øjeblikket med Proton.                                                                                                                     |
| Boolesk               | Boolesk                 | -                                                                                                                                                                     |
| GUID               | UUID                 | Proton Python klasse                                                                                                                                                 |
| streng             | streng               | -                                                                                                                                                                     |
| Dato og klokkeslæt           | tidsstempel            | Proton Python klasse                                                                                                                                                 |
| DateTimeOffset     | DescribedType        | DateTimeOffset.UtcTicks, der er knyttet til AMQP type:<type name=”datetime-offset” class=restricted source=”long”><descriptor name=”com.microsoft:datetime-offset” /></type> |
| TimeSpan           | DescribedType        | Timespan.Ticks, der er knyttet til AMQP type:<type name=”timespan” class=restricted source=”long”><descriptor name=”com.microsoft:timespan” /></type>                        |
| URI                | DescribedType        | Uri.AbsoluteUri, der er knyttet til AMQP type:<type name=”uri” class=restricted source=”string”><descriptor name=”com.microsoft:uri” /></type>                               |

### <a name="standard-properties"></a>Standardegenskaber

De følgende tabeller viser tilknytningen mellem Proton Python standard meddelelsesegenskaber og de [BrokeredMessage][] standardformularen til meddelelser.

#### <a name="proton-python-to-service-bus-net-apis"></a>Proton-Python til at vedligeholde Bus .NET API'er

| Proton Python        | Service Bus .NET         | Noter                                                     |
|----------------------|--------------------------|-----------------------------------------------------------|
| robust              | i/t.                      | Service Bus understøtter kun robust meddelelser.               |
| prioritet             | i/t.                      | Service Bus understøtter kun en enkelt meddelelsesprioritet.      |
| TTL                  | Message.TimeToLive       | Konvertering, Proton Python TTL er defineret i millisekunder. |
| første\_erhvervet      | i/t.                      | -                                                           |
| levering\_Tæl      | i/t.                      | -                                                           |
| Id                   | Message.MessageID        | -                                                           |
| bruger\_id             | i/t.                      | -                                                           |
| adresse              | Message.To               | -                                                           |
| emne              | Message.Label            | -                                                           |
| svar\_til            | Message.ReplyTo          | -                                                           |
| Korrelations\_id      | Message.CorrelationID    | -                                                           |
| indhold\_type        | Message.ContentType      | -                                                           |
| indhold\_kode    | i/t.                      | -                                                           |
| udløbet\_tid         | i/t.                      | -                                                           |
| oprettelse af\_tid       | i/t.                      | -                                                           |
| gruppen\_id            | Message.SessionId        | -                                                           |
| gruppen\_sekvens      | i/t.                      | -                                                           |
| svar\_til\_gruppe\_id | Message.ReplyToSessionId | -                                                           |
| Formatér               | i/t.                      | -                                                           |

| Service Bus .NET        | Proton                       | Noter                                                     |
|-------------------------|------------------------------|-----------------------------------------------------------|
| ContentType             | Message.Content\_type        | -                                                           |
| CorrelationId           | Message.Correlation\_id      | -                                                           |
| EnqueuedTimeUtc         | i/t.                          | -                                                           |
| Etiket                   | Message.subject              | -                                                           |
| MessageId               | Message.id                   | -                                                           |
| ReplyTo                 | Message.reply\_til            | -                                                           |
| ReplyToSessionId        | Message.reply\_til\_gruppe\_id | -                                                           |
| ScheduledEnqueueTimeUtc | i/t.                          | -                                                           |
| Session-id               | Message.Group\_id            | -                                                           |
| TimeToLive              | Message.TTL                  | Konvertering, Proton Python TTL er defineret i millisekunder. |
| Til                      | Message.address              | -                                                           |

## <a name="next-steps"></a>Næste trin

Klar til at få mere at vide? Besøg følgende links:

- [Tjenesten Bus AMQP oversigt]
- [AMQP i Service Bus til Windows Server]

[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[AMQP i Service Bus til Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

[Tjenesten Bus AMQP oversigt]: service-bus-amqp-overview.md
