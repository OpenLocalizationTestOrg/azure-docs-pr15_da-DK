<properties
    pageTitle="Azure meddelelse Hubs"
    description="Lær at bruge pushmeddelelser i Azure. Kodeeksempler, der er skrevet i C#-brug af .NET-API."
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"
    documentationCenter=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="multiple"
    ms.devlang="multiple"
    ms.topic="hero-article"
    ms.date="08/25/2016"
    ms.author="yuaxu"/>


#<a name="azure-notification-hubs"></a>Azure meddelelse Hubs

##<a name="overview"></a>Oversigt

Azure meddelelse Hubs giver en nem Hvis du vil bruge, flere platforme, skaleret push-infrastruktur, der gør det muligt at sende mobile pushmeddelelser fra en hvilken som helst backend-version (i skyen eller lokalt) til en hvilken som helst mobil platform.

Med besked om Hubs, kan du nemt sende på tværs af platforme og personlig pushmeddelelser abstracting oplysninger om de forskellige platform meddelelse systemer (PNS). Du kan målrette individuelle brugere eller hele målgruppe segmenter, der indeholder millioner af brugere, på tværs af alle deres enheder med et enkelt API-opkald.

Du kan bruge meddelelse Hubs for både enterprise og forbrugere scenarier. Eksempel:

- Sende seneste nyheder beskeder til millioner med lav ventetid (besked om Hubs styrer Bing-programmer, der er forudinstalleret på alle Windows og Windows Phone enheder).
- Sende placering-baserede kuponer til bruger målgrupper.
- Sende begivenhed beskeder til brugere eller grupper for/økonomi/sportsspil programmer.
- Informere brugere om enterprise begivenheder som nye meddelelser/mails og kundeemner.
- Sende en-gang-adgangskoder påkrævet til multi-Factor authentication.



##<a name="what-are-push-notifications"></a>Hvad er Push-beskeder?

Smartphones og tabletcomputere kan "" give brugere besked, når en hændelse. Disse meddelelser kan tage mange formularer.

I Windows Store og Windows Phone-programmer beskeden kan være i form af en _toast_: et ikke-modal vindue vises med en lyd til at angive en ny meddelelse. Andre typer af meddelelser, der understøttes medtage _flisen_, _rå_og _badge_ beskeder. Se [fliser, Badges, og meddelelser](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx)kan finde flere oplysninger om typerne meddelelser, der understøttes på Windows-enheder.

På Apple iOS-enheder, tryk på samme måde giver besked om en dialogboks, brugeren anmoder om brugeren til at få vist eller lukke meddelelsen. Klik på **Vis** åbnet det program, der modtager meddelelsen. Du kan finde flere oplysninger om iOS beskeder, [iOS beskeder](http://go.microsoft.com/fwlink/?LinkId=615245).

Pushmeddelelser Hjælp mobilenheder vise frisk oplysninger under resterende energi effektivt. Meddelelser kan blive sendt af back end-systemer til mobilenheder, selvom tilsvarende apps på en enhed ikke er aktiv. Pushmeddelelser er en vigtig komponent til forbrugere apps, hvor de bruges til at øge app aftale for og brugen. Meddelelser er også nyttig til virksomheder, når opdaterede oplysninger øges medarbejder tilgængelighed i forbindelse med hændelser i virksomheden.

Nogle specifikke eksempler på mobile aftale scenarier er:

1.  Opdatere et felt på Windows 8 eller Windows Phone med aktuelle økonomiske oplysninger.
2.  Advarer en bruger med en hilsen, der er tildelt nogle arbejdsopgave til den pågældende bruger i en arbejdsproces-baseret enterprise-app.
3.  Viser en badge med antallet af aktuelle salg fører i en CRM-app (såsom Microsoft Dynamics CRM).

##<a name="how-push-notifications-work"></a>Hvordan Push-beskeder arbejde

Pushmeddelelser leveres via platform-specifikke infrastruktur kaldet _Platform meddelelse systemer_ (PNS). En PNS tilbyder barebones funktioner (det vil sige, ingen understøttelse af udsendelse, tilpasning) og har en fælles grænseflade. For eksempel skal en udvikler for at sende en meddelelse til en Windows Store app skal kontakte WNS (Windows-Beskedtjeneste). For at sende en meddelelse til en iOS-enhed, er den samme udvikler kontakte APNS (Apple Push meddelelse Service), og send meddelelsen endnu en gang. Azure meddelelse hubs hjælp ved at give en fælles grænseflade, sammen med andre funktioner til at understøtte pushmeddelelser på tværs af hver platform.

På et højt niveau dog følge alle platform meddelelse systemer det samme mønster:

1.  Klient-appen kontakter PNS for at hente dens _håndtere_. Afhænger af typen håndtaget på systemet. Til WNS, er det en URI eller "kanal til meddelelserne". Til APNS er det et token.
2.  Klient-app gemmer denne handle i app _back end -_ til senere brug. For WNS er back end-typisk en skybaseret tjeneste. Til Apple kaldes systemet en _udbyder_.
3.  Hvis du vil sende en meddelelse om push kontakter app back end-PNS ved hjælp af håndtaget målrette en bestemt klient app forekomst.
4.  PNS videresender meddelelsen til den enhed, der er angivet af håndtaget.

![][0]

##<a name="the-challenges-of-push-notifications"></a>Udfordringer i forbindelse med Pushmeddelelser

Selvom disse systemer er meget effektive, lade de stadig meget arbejde til app-udvikleren for at implementere almindelige selv opslagsnål meddelelse scenarier, som udsendelse eller sende pushmeddelelser til segmenteret brugere.

Pushmeddelelser er en af de mest brugte funktioner i skyen services til mobile-apps. Årsagen til dette er, at den infrastruktur, der kræves for at gøre dem til at virke ret komplekse og hovedsageligt vedrører den primære forretningslogik af appen. Nogle af udfordringerne ved at opbygge en efter behov push-infrastruktur er:

- **Platform afhængighed.** Hvis du vil sende beskeder til enheder på forskellige platforme, skal være kodet flere grænseflader i back end. Ikke kun er de laveste-niveau-oplysninger, der er forskellige, men præsentationen i meddelelsen om (felt, toast eller badge) er også platform-afhængige. Disse forskelle kan føre til komplekse og svært at vedligeholde back end-kode.

- **Skala.** Skalering denne infrastruktur har to dele:
    + I henhold til PNS retningslinjer opdateres enhed tokens, hver gang appen startes. Dette medfører en stor mængde trafik (og adgang til deraf følgende database) til Hold enhed tokens opdateret. Når antallet enheder vokser (muligvis til millioner), er omkostninger for at oprette og vedligeholde denne infrastruktur nonnegligible.

    + De fleste PNSs understøtter ikke udsendelse til flere enheder. Det følger, at en udsendelse til millioner af enheder resulterer i millioner af opkald til PNSs. Der kan skalere forespørgslerne skyldes nontrivial, normalt app udviklere vil beholde den samlede ventetid. For eksempel sidste kameraet, for at modtage meddelelsen ikke skal modtage meddelelsen 30 minutter, når du har sendt i meddelelser, som for mange tilfælde vil den besejrer formålet med pushmeddelelser.
- **Routing.** PNSs ikke en metode til at sende en meddelelse til en enhed. Dog rettet beskeder i brugere og/eller interesse grupper (for eksempel alle medarbejdere tildelt til en bestemt kundekonto) i de fleste apps. For at omdirigere meddelelserne til de korrekte enheder, skal app back-end som f.eks bevare en registreringsdatabasen, der knytter interesse grupper til enhed tokens. Denne omkostninger føjer til den samlede tid til marked og vedligeholdelse omkostninger for en app.

##<a name="why-use-notification-hubs"></a>Hvorfor bruge meddelelse Hubs?

Meddelelse om Hubs eliminere kompleksitet: Du behøver ikke at administrere udfordringer i forbindelse med pushmeddelelser. I stedet kan du bruge en meddelelse om Hub. Meddelelse om Hubs Brug en fuldstændig flere platforme, skaleret opslagsnål meddelelse infrastruktur og reducere betydeligt push-specifikke koden, der kører i app back-end. Meddelelse om Hubs implementere alle funktioner i en push-infrastruktur. Enheder er kun ansvarlig for at registrere PNS håndtag, og backend-version er ansvarlig for afsendelse af uafhængige af platformen meddelelser til brugere eller grupper, interesse, som vist i følgende figur:

![][1]


Meddelelse om hubs indeholder en klar til brug opslagsnål meddelelse infrastruktur med følgende fordele:

- **Flere platforme.**
    +  Understøttelse af alle overordnede mobile platforme. Meddelelse om hubs kan sende pushmeddelelser til Windows Store-, iOS-, Android- og Windows Phone-apps.

    +  Meddelelse om hubs give en fælles grænseflade for at sende beskeder til alle understøttede platforme. Platform-specifikke protokoller er ikke påkrævet. App back-end kan sende beskeder i platform-specifikke eller platform uafhængige formater. Programmet kommunikerer kun med besked om Hubs.

    +  Administration af enheder håndtaget. Besked om Hubs fører håndtaget registreringsdatabasen og feedback fra PNSs.

- **Fungerer med en hvilken som helst back end-**: skyen eller en lokal, .NET PHP Java, og Node, osv.

- **Skala.** Meddelelse om hubs Skaler til millioner af enheder uden behov for at ændre arkitekturen eller shard.


- **RTF sæt levering mønstre**:

    - *Udsend*: giver mulighed for nær samtidig udsendelse til millioner af enheder med et enkelt API-opkald.

    - *Unicast/Multicast*: overføre til mærker, der repræsenterer enkelte brugere, herunder alle deres enheder eller bredere gruppe; for eksempel separate formular faktorer (tablet kontra telefon).

    - *Segmentering*: Push kompleks segmentet defineret af mærke udtryk (for eksempel enheder i New York efter Yankees).

    Hver enhed, når du sender sin handle til en meddelelse om hub kan angive et eller flere _mærker_. Du kan finde flere oplysninger om [mærker]. Mærker behøver ikke at være allerede klargjort eller fjernet. Mærker gør det nemt at sende meddelelser til brugere eller grupper, interesse. Da mærker kan indeholde et app-specifikke id (som bruger eller gruppe-id'er), frigør deres brug app back-end fra arbejdsbyrden skulle lagre og administrere enhed håndtag.

- **Tilpasning**: hver enhed kan have en eller flere skabeloner, for at opnå per enhed lokalisering og tilpasning uden at påvirke back end-kode.

- **Sikkerhed**: delt Access hemmeligt (SAS) eller samlet godkendelse.

- **RTF-telemetri**: tilgængelig i portalen og fra et program.


##<a name="integration-with-app-service-mobile-apps"></a>Integration med App Service-Mobilapps

For at lette en problemfri og samlede oplevelse Azure tjenester, har [App Service Mobile-Apps] indbygget understøttelse af pushmeddelelser ved hjælp af besked om Hubs. [App Service Mobile-Apps] tilbyder en meget SVG, globalt tilgængelig mobile program development platform for Enterprise udviklere og systemintegratorer, der knytter et bredt udvalg af funktioner til mobile udviklere.

Mobile-Apps udviklere kan udnytte meddelelse Hubs med arbejdsprocessen for følgende:

1. Hente enhed PNS håndtag
2. Registrere enhed og [skabeloner] med besked om Hubs gennem praktisk Mobile Apps klient SDK register API
    + Bemærk, at Mobile-Apps Rampe ikke til stede alle koder på registreringer af sikkerhedsmæssige årsager. Arbejde med besked om Hubs fra din back-end direkte til knytte mærker til enheder.
3. Sende meddelelser fra din app backend-version med besked om Hubs

Her er nogle fordele, der er hentet ind til udviklere med denne integration:

- **Apps mobilklient SDK'er.** Disse flere platforme SDK'er giver enkel API'er til registrering og tale med besked om hubben sammenkædet med mobilappen automatisk. Udviklere behøver ikke at gå gennem meddelelse Hubs legitimationsoplysninger og arbejde med yderligere en tjeneste.
    + SDK'er mærke automatisk den pågældende enhed med Mobile Apps godkendte bruger-ID til at aktivere opslagsnål til bruger scenario.
    + SDK'er bruge automatisk Mobile Apps installations-ID'ET som GUID til at registrere med besked om Hubs, gemme udviklere problemer med at vedligeholde flere service-GUID.
    
- **Installation model.** Mobile-Apps fungerer med besked om Hubs seneste push model til at repræsentere alle push-egenskaber, der er knyttet til en enhed i en JSON-Installation, justerer med opslagsnål meddelelse tjenester og er nem at bruge.

- **Fleksibilitet.** Udviklere kan altid vælge at arbejde med besked om Hubs direkte selv med integrationen på plads.

- **Integreret oplevelse [Azure]-portalen.** Push som en egenskab er repræsenteret visuelt i Mobile-Apps og udviklere nemt kan arbejde med den tilknyttede meddelelse hub gennem Mobile-Apps.



##<a name="next-steps"></a>Næste trin

Du kan finde mere at vide om meddelelse Hubs i disse emner:

+ **[Hvordan kunder bruger meddelelse Hubs]**

+ **[Meddelelse om Hubs selvstudier og hjælpelinjer]**

+ **Meddelelse om Hubs Introduktion selvstudier** ([iOS], [Android] [Windows Universal] [Windows Phone], [Kindle], [Xamarin.iOS], og [Xamarin.Android])

Relevante .NET managed API referencer til pushmeddelelser er placeret her:

+ [Microsoft.WindowsAzure.Messaging.NotificationHub]
+ [Microsoft.ServiceBus.Notifications]


  [0]: ./media/notification-hubs-overview/registration-diagram.png
  [1]: ./media/notification-hubs-overview/notification-hub-diagram.png
  [Hvordan kunder bruger meddelelse Hubs]: http://azure.microsoft.com/services/notification-hubs
  [Meddelelse om Hubs selvstudier og hjælpelinjer]: http://azure.microsoft.com/documentation/services/notification-hubs
  [iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
  [Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
  [Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
  [Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
  [Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
  [Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
  [Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
  [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
  [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
  [App Service-Mobilapps]: https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-value-prop/
  [skabeloner]: notification-hubs-templates-cross-platform-push-messages.md
  [Azure-portalen]: https://portal.azure.com
  [mærker]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)
