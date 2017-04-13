<properties 
    pageTitle="Oversigt over begivenhed Hubs godkendelse og sikkerhedsmodel | Microsoft Azure"
    description="Begivenhed Hubs godkendelse og sikkerhed model oversigt."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/16/2016"
    ms.author="sethm;clemensv" />

# <a name="event-hubs-authentication-and-security-model-overview"></a>Begivenhed Hubs godkendelse og sikkerhed model oversigt

Begivenhed Hubs sikkerhedsmodellen opfylder følgende krav:

- Kun enheder, der udgør gyldige legitimationsoplysninger kan sende data til en begivenhed Hub.
- En enhed kan repræsentere en anden enhed.
- En uautoriseret enhed kan også blokeres fra at sende data til en begivenhed Hub.

## <a name="device-authentication"></a>Enhed godkendelse

Begivenhed Hubs sikkerhedsmodellen er baseret på en kombination af [Delt Access signatur (SAS)](../service-bus-messaging/service-bus-shared-access-signature-authentication.md) tokens og *begivenhed udgivere*. En begivenhed publisher definerer et virtuelt slutpunkt for en begivenhed Hub. Udgiveren kan kun bruges til at sende meddelelser til en begivenhed Hub. Det er ikke muligt at modtage meddelelser fra en udgiver.

Typisk anvender en begivenhed Hub en publisher per enhed. Alle de meddelelser, der sendes til en af udgivere af en begivenhed Hub er sat i kø i pågældende begivenhed Hub. Udgivere aktivere detaljerede adgangskontrol og (throttling).

Hver enhed er tildelt et entydigt token, der er overført til enheden. Tokens er oprettet, så hver entydige token giver adgang til en anden entydigt udgiver. En enhed, der har et token kan kun sende til en udgiver, men ingen andre publisher. Hvis flere enheder deler det samme token, deler hver af disse enheder en udgiver.

Det er muligt at give enheder med tokens, som giver direkte adgang til en begivenhed-Hub, selvom det ikke anbefales. Enhver enhed, der indeholder dette token kan sende meddelelser direkte i pågældende begivenhed Hub. Sådan en enhed for vil ikke være underlagt (throttling). Enheden kan desuden være sortlistede fra at sende til denne begivenhed Hub.

Alle tokens er signeret med en SAS nøgle. Typisk er alle tokens signeret med den samme nøgle. Enheder er ikke klar over nøglen. Dette forhindrer, at enheder produktionsproces tokens.

### <a name="create-the-sas-key"></a>Oprette tasten SAS

Når du opretter en begivenhed Hubs navneområde, genererer Azure begivenhed Hubs en 256-bit SAS-nøgle med navnet **RootManageSharedAccessKey**. Denne vigtige giver sende, lytte og administrere rettigheder til navneområdet. Du kan oprette yderligere taster. Det anbefales, at du medfører en nøgle, som giver sender tilladelser til bestemte begivenhed hubben. I resten af dette emne, forudsættes det, at du navngivet denne tast `EventHubSendKey`.

I følgende eksempel oprettes en send kun nøgle, når du opretter hubben begivenhed:

```
// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, namespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create Event Hub with a SAS rule that enables sending to that Event Hub
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, eventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);
```

### <a name="generate-tokens"></a>Generere tokens

Du kan generere tokens ved hjælp af tasten SAS. Du skal oprette kun én token per enhed. Tokens kan derefter produceret ved at bruge følgende metode. Alle tokens er oprettet ved hjælp af tasten **EventHubSendKey** . Hver token tildeles en entydig URI.

```
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

Når du ringer til denne metode, URI skal angives som `//<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`. For alle tokens, URI er identisk med undtagelse af `PUBLISHER_NAME`, som skal være forskellige for hver token. Ideelt set `PUBLISHER_NAME` repræsenterer ID'ET for den enhed, der modtager denne token.

Denne metode genererer en token med følgende struktur:

```
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

Token udløbsdatoen er angivet i sekunder fra Jan 1 1970. Følgende er et eksempel på et token:

```
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

Tokens har typisk en levetid, der minder om eller overstiger levetid på enheden. Hvis enheden har mulighed for at få et nyt id, kan bruges tokens med en kortere levetid.

### <a name="devices-sending-data"></a>Enheder for at sende data

Når tokens, der er oprettet klargjort hver enhed med sin egen entydige token.

Når enheden sender data til en begivenhed-Hub, koder enheden dens token med anmodningen om afsendelse af. Hvis du vil forhindre en hacker aflytning og stjæle tokenet, skal være kommunikationen mellem enheden og begivenhed hubben over en krypteret kanal.

### <a name="blacklisting-devices"></a>Fortrolige enheder

Hvis et token stjæles af en hacker, kan hackeren repræsentere den enhed, hvis token blevet stjæles. Blacklisting en enhed gengiver enheden ubrugelig indtil enheden er angivet et nyt id, der bruger en anden udgiver.

## <a name="authentication-of-back-end-applications"></a>Godkendelse af back end-programmer

Hvis du vil godkende back end-programmer, der forbruger de data, der genereres af enheder, anvender begivenhed Hubs en sikkerhedsmodel, der svarer til den model, der bruges til Service Bus emner. En begivenhed Hubs consumer gruppe er svarer til et abonnement til et emne, Service Bus. En klient kan oprette en gruppe til forbrugere, hvis anmodningen om at oprette gruppen consumer leveres med et token, giver at administrere tilladelser for hubben begivenhed eller det navneområde, som den begivenhed Hub tilhører. En klient har tilladelse til at bruge data fra en gruppe til forbrugere, hvis Modtag anmodningen leveres med et token, som giver Modtag rettigheder på consumer gruppen, den begivenhed Hub eller navneområdet som begivenhed hubben tilhører.

Den aktuelle version af Service Bus understøtter ikke SAS regler for individuelle abonnementer. Det samme gælder for begivenhed Hubs consumer grupper. Understøttelse af SAS, føjes til begge funktioner i fremtiden.

Du kan bruge SAS taster til at sikre alle forbrugergrupper med en fælles nøgle i fravær SAS godkendelsestyper for individuelle forbrugergrupper. Denne fremgangsmåde gør det muligt for et program tilladelse til at bruge data fra en af grupperne forbruger af en begivenhed Hub.

## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om begivenhed Hubs, Besøg følgende emner:

- [Oversigt over Hubs begivenhed]
- En [i kø messaging-løsninger] ved hjælp af Service Bus køer.
- En komplet [Northwind, der bruger begivenhed Hubs].

[Oversigt over Hubs begivenhed]: event-hubs-overview.md
[eksempelprogram, der bruger begivenhed Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[i kø messaging-løsninger]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 
