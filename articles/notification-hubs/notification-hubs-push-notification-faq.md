<properties
    pageTitle="Azure meddelelse Hubs – ofte stillede spørgsmål (ofte stillede spørgsmål)"
    description="Ofte stillede spørgsmål om design/implementering af løsninger på meddelelse Hubs"
    services="notification-hubs"
    documentationCenter="mobile"
    authors="ysxu"
    manager="erikre"
    keywords="Push-meddelelse, pushmeddelelser, iOS pushmeddelelser, android pushmeddelelser, ios opslagsnål, android opslagsnål"
    editor="" />

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="yuaxu" />

#<a name="push-notifications-with-azure-notification-hubs---frequently-asked-questions"></a>Push-beskeder med Azure meddelelse Hubs – ofte stillede spørgsmål

##<a name="general"></a>Generelt
###<a name="1---what-is-the-price-model-for-notification-hubs"></a>1. Hvad er prismodellen for meddelelse Hubs?
Meddelelse om Hubs tilbydes i tre niveauer:

* **Gratis** – få op til 1 million skubber per abonnement en måned.
* **Grundlæggende** - få 10 millioner skubber per abonnement måneden som en oprindelig plan, med kvote forøgelse indstillinger.
* **Standard** – få 10 millioner skubber per abonnement måneden som en oprindelig plan, med kvote øge indstillinger samt omfattende telemetri capabilties.

De seneste oplysninger kan findes på siden [Meddelelse Hubs priser] . De priser er etableret på niveauet for abonnement og er baseret på antallet opslagsnål meddelelse indledning, så det er ligegyldigt, hvor mange navneområder eller vises hubs, som du har oprettet i abonnementet Azure.

**Gratis** niveau tilbydes til udvikling formål med ingen garanti for SERVICENIVEAUAFTALE. Dette trin kan være et godt udgangspunkt for dem, der vil udforske funktionerne i pushmeddelelser via Azure meddelelse Hubs, men det kan ikke være det bedste valg til mellemstore og store programmer.

**Grundlæggende** & **Standard** niveauer, der tilbydes af fremstilling brugen med følgende funktioner aktiveret *kun for standarden klasse*:

- *RTF-telemetri* - meddelelse Hubs tilbud en række funktioner til at eksportere dataene telemetri samt push meddelelse registreringsoplysninger til offlinevisning og analyse.
- *Flere arkitekturer* - ideelt, hvis du opretter en mobilapp, ved hjælp af besked om Hubs til at understøtte flere lejere. Dette gør det muligt at angive legitimationsoplysninger for Push meddelelse tjenester (PNS) på niveauet meddelelse Hub navneområdet for app, og derefter kan du udskille lejere at give dem individuelle hubs under dette almindelige navneområde. Dette gør det nemmere at vedligeholdelse, mens holde tasterne SAS send og modtag pushmeddelelser fra meddelelse hubberne adskilt for hver lejer sikrer ikke kryds-lejer overlap.
- *Planlagt Push* - gør det muligt at planlægge pushmeddelelser, der skal være efterfølgende i kø og sendt ud.
- *Importere flere* - gør det muligt at importere registreringer ad gangen.

###<a name="2---what-is-the-notification-hubs-sla"></a>2. Hvad er meddelelse Hubs SLA?
For **grundlæggende** og **Standard** meddelelse Hubs lag en vi, at mindst 99,9% af tid, en konfigureret programmer vil kunne sende pushmeddelelser eller udføre registrering management handlinger i forhold til en meddelelse om Hub implementeret i et understøttede trin. Hvis du vil vide mere om vores SERVICENIVEAUAFTALE, skal du gå til siden [Meddelelse Hubs SERVICENIVEAUAFTALE] .

> [AZURE.NOTE] Der er ingen SLA garantier for ben mellem Beskedtjeneste Platform og enheden, da meddelelse Hubs er afhængige af eksterne platform udbydere til at levere meddelelsen til opslagsnål på enheden.

###<a name="3---which-customers-are-using-notification-hubs"></a>3. hvilke kunder bruger meddelelse Hubs?
Vi har et stort antal kunder, der bruger meddelelse Hubs med et par væsentlige dem nedenfor:

* Sochi 2014 – 100s af interesse grupper, million 3 + enheder, 150 + millioner meddelelse, der er afsendt i 2 uger. [CaseStudy - Sochi]
* Skanska - [CaseStudy - Skanska]
* Seattle gange - [CaseStudy - Seattle gange]
* Mural.ly - [CaseStudy - Mural.ly]
* 7Digital - [CaseStudy - 7Digital]
* Bing-Apps – 10 'er millioner enheder, sende 3 millioner meddelelser/dag.

###<a name="4-how-do-i-upgrade-or-downgrade-my-notification-hubs-to-change-my-service-tier"></a>4. hvordan opgradere eller nedgradere min meddelelse Hubs for at ændre min webtjenesten?
Gå til [Azure klassisk Portal], skal du klikke på Service Bus, og klik på din navneområde derefter din meddelelse hub. Under fanen skala, vil du kunne ændre din meddelelse Hubs webtjenesten.

![](./media/notification-hubs-faq/notification-hubs-classic-portal-scale.png)

##<a name="design--development"></a>Design og udvikling
###<a name="1---which-server-side-platforms-do-you-support"></a>1. hvilke serversiden platforme understøtter i?
Vi giver dig SDK'er og [fuldført eksempler] til .NET Java PHP, Python, og Node.js så en app backend-version kan være konfiguration til at kommunikere med besked om Hubs ved hjælp af disse platforme. Meddelelse om Hubs API'er er baseret på RESTEN grænseflader, så du kan vælge at direkte kontakt til dem, i stedet Hvis du ikke vil tilføje en ekstra afhængighed. Flere oplysninger kan findes på siden [NH - REST API'er] .

###<a name="2---which-client-platforms-do-you-support"></a>2. hvilke klientplatforme understøtter i?
Vi understøtter afsendelse pushmeddelelser til [Apple iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md) [Android](notification-hubs-android-push-notification-google-gcm-get-started.md) [Universal til Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [Android Kina (via Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), og Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) & [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)), [Apps Chrome](notification-hubs-chrome-push-notifications-get-started.md) og [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari) -platforme. Besøg vores [NH - komme i gang selvstudier] side til en komplet liste over få Introduktion selvstudier håndtering af afsendelse pushmeddelelser på disse platforme.

###<a name="3---do-you-support-smsemailweb-notifications"></a>3. understøtter i mail-SMS/web meddelelser?
Meddelelse om Hubs er primært udviklet til at sende meddelelser til mobilapps ved hjælp af de platforme, der er nævnt ovenfor. Vi endnu giver ikke mulighed for at sende mail eller SMS-beskeder fra tredjepart platforme, som understøtter disse funktioner kan dog integreres sammen med besked om Hubs sende oprindelige pushmeddelelser ved hjælp af [Azure Mobile-Apps].

Meddelelse om Hubs giver også ikke en i browseren opslagsnål meddelelse levering service out-of-the-box. Kunderne kan vælge at implementere ved hjælp af SignalR oven på serversiden understøttede platforme. Hvis du vil sende beskeder til browserapps i Chrome sandkassetilstand, se [Chrome Apps selvstudium].

###<a name="4---what-is-the-relation-between-azure-mobile-apps-and-azure-notification-hubs-and-when-do-i-use-what"></a>4. Hvad er forholdet mellem Azure Mobile-Apps og Azure meddelelse Hubs, og hvornår skal jeg bruge hvad?
Hvis du har en eksisterende mobilapp backend-version, og du kun vil give mulighed for at sende pushmeddelelser kan du bruge Azure meddelelse Hubs. Hvis du vil oprette din mobilapp backend-version fra bunden derefter du bør overveje at bruge Azure Mobile-Apps. En Azure Mobile-App danner automatisk en meddelelse om Hub for dig at kunne sende pushmeddelelser nemt fra mobilapp back-end. Priser for Azure Mobile-Apps indeholder de grundlæggende gebyrer for en meddelelse om Hub, og du betaler kun, når du går ud over de inkluderede skubber. Flere oplysninger om omkostningerne, der er tilgængelige på siden [App servicepriser] .

###<a name="5---how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>5. hvor mange enheder kan jeg understøtter, hvis jeg sender pushmeddelelser via meddelelse Hubs?
Se siden [Meddelelse Hubs priser] for oplysninger om antallet enheder, der understøttes.

For visse scenarier, hvis du har brug for mere end 10,000,000 registrerede enheder, skal du [kontakte os](https://azure.microsoft.com/overview/contact-us/) direkte, og vi hjælpe dig skalere din løsning.

###<a name="6---how-many-push-notifications-can-i-send-out"></a>6. hvor mange pushmeddelelser kan jeg sende?
Afhængigt af det valgte niveau skaleres Azure automatisk baseret på antallet af meddelelser, der flyder gennem systemet.

>[AZURE.NOTE] De samlede brug omkostninger kan gå baseret på antallet pushmeddelelser, der served. Sørg for, at du har kendskab til eksisterende niveau begrænsninger, der er beskrevet på siden [Meddelelse Hubs priser] .

Vores eksisterende kunder bruger meddelelse Hubs til at sende millioner af pushmeddelelser dagligt. Du behøver ikke at gøre noget for at tilpasse dine meddelelser når, som du bruger Azure meddelelse Hubs opslagsnål.

###<a name="7---how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>7. hvor lang tid tager det til sendt push-beskeder, når min enhed?
Azure meddelelse Hubs er i stand til at behandle mindst **1 million opslagsnål meddelelse sender et minut** i en normal brug scenario, hvor den indgående belastning er ret ensartet og ikke spikey i art. Denne sats kan variere, afhængigt af antallet mærker, karakter indgående sender og andre eksterne faktorer.

Mens anslåede levering, tjenesten er kan beregne destinationer per platform og distribuere meddelelser til de respektive opslagsnål meddelelse levering-tjenester, der er baseret på de registrerede mærker/mærke udtryk. Herfra på er det ansvarlig for Push-beskeder tjenesterne (PNS) til at sende meddelelsen til enheden.

En PNS garanterer ikke en hvilken som helst SERVICENIVEAUAFTALE til levering af meddelelser. dog typisk en størstedelen af pushmeddelelser er leveret til destinationsenheder inden for et par minutter (som regel inden for 10 minutter) fra det tidspunkt, de er sendt til vores platform. Der kan være et par outliers som det kan tage længere tid.

>[AZURE.NOTE] Azure meddelelse Hubs har en politik i sted at slippe en hvilken som helst pushmeddelelser, som ikke kan blive leveret til PNS på 30 minutter. Denne forsinkelse kan ske af flere årsager til, mest ofte fordi PNS (throttling) dit program.

###<a name="8---is-there-any-latency-guarantee"></a>8. er der en hvilken som helst ventetid GARANTIBRUD?
På grund af funktionsmåden i pushmeddelelser (de leveres af en ekstern, platform-specifikke Pushmeddelelsestjeneste), er der ingen ventetid garanti. Typisk leveres størstedelen af pushmeddelelser inden for et par minutter.

###<a name="9---what-are-the-considerations-i-need-to-take-into-account-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>9. Hvad er de overvejelser, jeg har brug for at tage i betragtning, når du designer en løsning med navneområder og meddelelse Hubs?

####<a name="mobile-appenvironment"></a>Mobile-App/miljø

* Der skal være en meddelelse om Hub per mobilapp per miljø.
* I et scenarie med flere lejer har hver lejer en separat hub.
* Aldrig skal du dele den samme meddelelse Hub mellem test og miljøer, som dette kan medføre problemer på linje ned under afsendelse af meddelelser. Apple tilbyder fx sandkassetilstand og fremstilling Push slutpunkter med hver har separate legitimationsoplysninger.
* Som standard, kan du sende test beskeder til dine registrerede enheder via portalen Azure eller den Azure integrerede komponent i Visual Studio. I grænseværdi for er indstillet til 10-enheder, der er valgt tilfældigt fra puljen registrering.

>[AZURE.NOTE] Hvis hubben blev konfigureret oprindeligt med et Apple sandkassetilstand certifikat og derefter konfigureres igen for at bruge en Apple fremstilling certifikat, vil de gamle enhed tokens blive ugyldige med det nye certifikat og medføre skubber mislykkes. Det er bedst at adskille din fremstilling og teste miljøer og bruge forskellige hubs til forskellige miljøer.

####<a name="pns-credentials"></a>PNS legitimationsoplysninger

Når en mobilapp er registreret med en platform udvikler portal (fx Apple eller Google osv) og får du en app-id og sikkerhed tokens som en app back-end for at give den Platform opslagsnål meddelelse services skal kunne sende pushmeddelelser til enhederne. Disse sikkerhedstokens, som kan være i form af certifikater (fx for Apple iOS eller Windows Phone) eller sikkerhedsnøgler (Google Android osv Windows) skal konfigureres i meddelelse Hubs. Dette er fuldført typisk på niveauet for meddelelse hub, men du kan også udføres på niveauet navneområde i et scenarie med flere lejer.

####<a name="namespaces"></a>Navneområder

Navneområder kan bruges til installation gruppering.  Det kan også bruges til at repræsentere alle besked om Hubs for alle lejere af den samme app i scenariet med flere lejer.

####<a name="geo-distribution"></a>Geografisk-fordeling

Geografisk-fordelingen er ikke altid kritiske i opslagsnål meddelelse scenarier. Det er der nævnes, forskellige opslagsnål meddelelse Services (fx APNS, GCM osv), som leverer i sidste ende pushbeskeder til enhederne, ikke er jævnt fordelt enten.

Hvis du har et program, der bruges på tværs af hele verden, kan du oprette flere hubs i forskellige navneområder at drage fordel af tilgængeligheden af besked om Hubs tjeneste i forskellige Azure områder hele verden.

>[AZURE.NOTE] Dette vil øge management omkostninger - særligt omkring registreringer, så det ikke anbefales virkelig og skal kun udføres, hvis der er en eksplicit har du brug for.

###<a name="10--should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>10. skal jeg gøre registreringer fra app back end- eller direkte via klienten enheder?
Registreringer fra app back-end er nyttige, når du skal gøre klientgodkendelse, før du opretter registreringen, eller når du har mærker, der skal oprettes eller redigeres af app back-baseret på nogle app-logik. Yderligere oplysninger finder du kan læse mere på siderne [back end-registrering vejledning] og [back end-registrering vejledning - 2] .

###<a name="11--what-is-the-push-notification-delivery-security-model"></a>11. Hvad er opslagsnål meddelelse levering sikkerhedsmodel?
Azure meddelelse Hubs bruge en [Delt Access signatur (SAS)](../storage/storage-dotnet-shared-access-signature-part-1.md)-baseret sikkerhedsmodel. Du kan bruge SAS tokens på rodniveau navneområde eller på niveauet for findelt meddelelse Hubs. Disse SAS tokens kan angives med reglerne for forskellige fx send meddelelse tilladelser, lytte meddelelse tilladelser osv. Flere oplysninger er tilgængelige i [NH sikkerhedsmodel] dokumentet.

###<a name="12--how-should-i-handle-sensitive-payload-in-the-push-notifications"></a>12. hvordan skal jeg håndtere følsomme data i pushmeddelelser?
Alle meddelelser leveres til destinationsenheder ved den platform Push meddelelse tjenester (PNS). Når en afsender sender en besked til Azure meddelelse hubber derefter vi behandle og sende meddelelsen til de respektive PNS.

Alle forbindelser fra afsenderen til Azure beskeder Hubs og derefter til PNS bruger HTTPS.

>[AZURE.NOTE] Azure beskeder Hubs registrerer ikke data i meddelelsen på nogen måde.

Til at sende følsomme overførsler, men vi anbefaler et sikkert Push mønster hvor afsenderen leverer en 'ping' meddelelse med en meddelelse et id på enheden uden de følsomme data, og når appen på enheden modtager denne data, er det kan du ringe til en sikker API direkte for at hente meddelelsesoplysninger. En vejledning til, hvordan du implementere mønsteret er beskrevet ovenfor er tilgængelig på siden [NH - Secure Push selvstudium] .

##<a name="operations"></a>Handlinger
###<a name="1---what-is-the-disaster-recovery-dr-story"></a>1. hvad handler om nedbrud gendannelse (DR)?
Vi giver dig metadata nedbrud dækning på vores slutningen (besked om Hub navn, forbindelsesstreng og andre vigtige oplysninger). Når et scenarie med DR udløses, er registreringer dataene **kun segment** af besked om Hubs-infrastrukturen som går tabt. Du skal implementere en løsning for at udfylde disse data til din nye hub efter genoprettelse igen.

- *Trin 1* – oprette en sekundær Hub meddelelse i en anden datacenter. Du kan oprette dette løbende på tidspunktet for hændelsen DR, eller du kan oprette en fra siden gå. Det gøre ikke meget af en forskel hvilken indstilling du vælger, da meddelelse Hub klargøring er en relativ hurtig proces i den rækkefølge, et par sekunder. Har du en fra begyndelsen beskytte du DR begivenheden, som påvirker beskyttelsen din administrationsfunktioner, så det er indstillingen meget anbefales.

- *Trin 2* - hydrat sekundær meddelelse hubben med registreringer fra den primære meddelelse Hub. Det anbefales ikke at forsøge at bevare registreringer på begge hubs, og prøv at synkronisere dem på løbende som registreringer kommer i - typisk, ikke har virket godt på grund af naturlige tendens af registreringer til at udløbe i PNS side. Meddelelse om Hubs rydde dem op, som vi modtager PNS feedback om udløbet eller ugyldige registreringer.  

Anbefales, at bruge en app backend-version, hvor enten:

- Fører et bestemt udvalg af registreringer på slut, så det kan gøre en flere Indsæt til sekundær meddelelse hub i tilfælde af DR

**ELLER**

- Henter en almindelig lagring af registreringer fra den primære hub som en sikkerhedskopi, og derefter indeholder en masse indsætte i det sekundære NH.

>[AZURE.NOTE] Registreringer eksport/Import funktioner er tilgængelige i Standard niveau er beskrevet i dokumentets [Registreringer eksport/Import] .

Hvis du ikke har en back-end derefter, når programmet starter på ethvert af mål enhederne, de udfører en ny registrering i sekundær meddelelse hubben og til sidst sekundært meddelelse Hub har alle de aktive enheder, der er registreret.

Ulempen er, vil der være en tidsperiode, når enheder, hvor apps ikke har åbnet op ikke modtager meddelelser.

###<a name="2---is-there-any-audit-log-capability"></a>2. er der en hvilken som helst log overvågning?
Alle meddelelser Hubs Management handlinger gå til handlingen logfiler, som vises i [Azure klassisk Portal].

##<a name="monitoring--troubleshooting"></a>Overvågning og fejlfinding
###<a name="1---what-troubleshooting-capabilities-are-available"></a>1. hvilke fejlfinding funktioner er tilgængelige?
Azure meddelelse Hubs giver flere funktioner for at gøre almindelige fejlfinding, især i de mest almindelige scenarie omkring mistede meddelelser. Se oplysninger om i vores [NH - fejlfinding i forbindelse med] hvidbog.

###<a name="2---what-telemetry-features-are-available"></a>2. hvilke telemetri-funktioner er tilgængelige?
Azure meddelelse Hubs kan få vist telemetridata i [Azure klassisk Portal]. Oplysninger om de tilgængelige målepunkter er tilgængelige på siden [NH - målepunkter] .

>[AZURE.NOTE] Vellykket meddelelser kun betyder, at pushmeddelelser er blevet leveret til den eksterne Pushmeddelelsestjeneste (fx APNS til Apple, GCM til Google osv). Det er op til PNS til at levere meddelelsen til destinationsenheder. Typisk viser PNS ikke levering målepunkter til tredjeparter.  

Vi giver også muligheden for at eksportere telemetridata fra et program (i **Standard** lag). Se [NH - metrik eksempel] få mere at vide.

[Azure klassisk Portal]: https://manage.windowsazure.com
[Meddelelse om Hubs priser]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Meddelelse om Hubs SERVICENIVEAUAFTALE]: http://azure.microsoft.com/support/legal/sla/
[CaseStudy - Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[CaseStudy - Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[CaseStudy - Seattle gange]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[CaseStudy - Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[CaseStudy - 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[NH - REST API'er]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[NH - Introduktionsselvstudier]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Chrome-Apps selvstudium]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[Vejledning til back end-registrering]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Back end-registrering vejledning - 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[NH sikkerhedsmodel]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[NH - sikker opslagsnål selvstudium]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[NH - fejlfinding]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[NH - metrik]: https://msdn.microsoft.com/library/dn458822.aspx
[NH - metrik eksempel]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[Registreringer eksport/Import]: https://msdn.microsoft.com/library/dn790624.aspx
[Azure Portal]: https://portal.azure.com
[fuldført eksempler]: https://github.com/Azure/azure-notificationhubs-samples
[Azure-Mobilapps]: https://azure.microsoft.com/en-us/services/app-service/mobile/
[App Service priser]: https://azure.microsoft.com/en-us/pricing/details/app-service/
