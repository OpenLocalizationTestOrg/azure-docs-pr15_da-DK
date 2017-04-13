<properties 
    pageTitle="Azure RemoteApp ofte stillede spørgsmål om | Microsoft Azure" 
    description="Se svar på ofte stillede spørgsmål om Azure RemoteApp." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="swadhwa" 
    editor=""/>

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/15/2016" 
    ms.author="elizapo"/>

# <a name="azure-remoteapp-faq"></a>Azure RemoteApp ofte stillede spørgsmål

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Vi har modtaget følgende spørgsmål om Azure RemoteApp. Anvende andre? Gå til [RemoteApp fora](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureRemoteApp) og fortælle os, hvad du bør vide, eller Rul en kommentar ned under.

## <a name="cant-find-what-youre-looking-for-have-a-question-we-didnt-answer"></a>Kan ikke finde det, du leder efter? Har vi ikke besvare spørgsmål?
Hvis du ikke kan finde oplysninger du skal bruge, eller du har en yderligere spørgsmål, som vi ikke dækker her, skal du gå til [Azure RemoteApp forum](http://aka.ms/araforum) og stil dit spørgsmål der. Vi kan altid tilføje flere svar her.

## <a name="what-is-azure-remoteapp"></a>Hvad er Azure RemoteApp? ##


- **Hvad er Azure RemoteApp?** RemoteApp er en Azure service hjælper dig med at sikre sikker og ekstern adgang til programmer fra mange forskellige enheder. Læs mere om [Azure RemoteApp](remoteapp-whatis.md).
- **Hvad er indstillingerne for installation?** Der findes to typer af websteder RemoteApp: skyen og hybrid. Hvilken version du skal bruge, afhænger af en række faktorer, som om du har brug for forbindelse til et domæne. Vi taler om alle beslutningerne [her](remoteapp-collections.md).

## <a name="quick-tips-on-using-azure-remoteapp"></a>Hurtige tip til brugen af Azure RemoteApp ##
- **Hvor lang tid før jeg ikke er tilsluttet? Hvor længe kan jeg være inaktiv, før du give mig Start?** 4 timer. Hvis du eller en af dine brugere er inaktiv til 4 timer, bliver du automatisk logget af Azure RemoteApp. Se de andre standardindstillinger i [Azure-abonnement og begrænsninger for tjenesten, kvoter, og begrænsninger](../azure-subscription-service-limits.md).
- **Kan jeg prøve denne tjeneste gratis?** Ja. Der er en gratis prøveversion tilgængelig for de 30 dage. Når prøveversion udløber, kan du skifte til et betalt konto (som du kan bruge i fremstilling) eller stoppe med at bruge tjenesten. Start din gratis prøveversion ved at gå til [portal.azure.com](http://portal.azure.com) – oprette en ny forekomst af RemoteApp. Med den gratis prøveversion, kan du oprette 2 forekomster af RemoteApp med 10 brugere hver forekomst. Husk, at denne prøveversion kun findes i 30 dage.
## <a name="azure-remoteapp-subscription-details"></a>Azure RemoteApp abonnementsoplysninger ##

- **Hvad er begrænsningerne service?** Du kan få mere at vide om de standardindstillinger og grænser for Azure RemoteApp i [Azure-abonnement og begrænsninger for tjenesten, kvoter, og begrænsninger](../azure-subscription-service-limits.md). Fortæl os, hvis du har flere spørgsmål.
- **Hvor mange brugere skal jeg have?** Der findes et minimum af 20 brugere. Lad mig Gentag, hvis du vil være meget Ryd - MINIMUM er 20. Du vil blive faktureret til 20. 
- **Hvor meget koster RemoteApp?** Se [Azure RemoteApp priser detaljer ](https://azure.microsoft.com/pricing/details/remoteapp/).
- **Koster én type af websteder mere end en anden?** Ja, det kan, afhængigt af dine krav til samling. En hybrid af websteder kræver en forbindelse fra Azure RemoteApp til dit lokale netværk. Hvis du bruger en eksisterende VNET/Express rute, er der gratis. Men hvis du bruger en ny Azure VNET og enten en gateway eller Express rute, du vil betale for [VPN-gateway](https://azure.microsoft.com/pricing/details/vpn-gateway) eller [Express ruten](https://azure.microsoft.com/pricing/details/expressroute/). Denne omkostninger (gennemgås i linkene) er oven på din månedlige Azure RemoteApp omkostninger.

## <a name="collections---whats-supported-which-should-you-use-and-others"></a>Samlinger - hvad understøttes, som skal du bruge, og andre
- **Understøttes brugerdefinerede-branche (LOB)-programmer?** Ja. Hvis du vil bruge et brugerdefineret program i Azure RemoteApp, oprette en [brugerdefineret skabelonbillede](remoteapp-create-custom-image.md), og Overfør det derefter til samlingen RemoteApp.
- **Fungerer mine brugerdefinerede LOB-applikationen i Azure RemoteApp?** Den bedste måde at figur, der er at teste den. Se [RD kompatibilitet Center](http://www.rdcompatibility.com/compatibility/default.aspx).
- **Hvilken installationsmetode (skyen eller hybrid) er bedst for min organisation?** Hybrid samlinger får den mest komplette oplevelse, hvis du vil have fuld integration med enkeltlogon (SSO) og secure lokalt netværk connectivity. Skyen samlinger giver en hurtig og nem måde til at isolere din installation ved hjælp af flere metoder til brugergodkendelse. Få mere at vide om de [installationsindstillinger](remoteapp-whatis.md).
- **Vi har SQL- eller en anden database enten lokalt eller i Azure. Hvilken installationstype skal vi bruge?** Det afhænger af, hvor SQL- eller back end-databasen er. Hvis databasen er i et privat netværk, skal du bruge samlingen hybrid. Hvis databasen er være tilgængelig på internettet og giver klienten forbindelser til at oprette forbindelse til den, kan du bruge samlingen skyen.
- **Hvad med drevtilknytning, USB- og seriel port, dele Udklipsholder og printeromdirigering?** Alle disse funktioner understøttes i Azure RemoteApp. Omdirigering af Udklipsholder deling og printer er aktiveret som standard. Du kan læse mere om omdirigering [her](remoteapp-redirection.md). 


## <a name="template-images"></a>Billeder af webstedsskabeloner
- **Kan jeg bruge en skyen eller eksisterende virtuelt som skabelonen for min RemoteApp af websteder?** Ja! Du kan oprette et billede, der er baseret på en Azure VM, bruge en af de billeder, der følger med dit abonnement, eller du kan oprette et brugerdefineret billede. Gå til [RemoteApp billedindstillinger](remoteapp-imageoptions.md).


## <a name="network-options"></a>Netværksindstillinger
- **Samlingen hybrid kræver en VNET. Kan vi bruge vores eksisterende VNET?** Du kan, hvis den eksisterende VNET er en Azure VNET. Se "trin 1: Konfigurere netværket virtuel" [hybrid samling instruktioner](remoteapp-create-hybrid-deployment.md) for at få mere at vide.
- **Kan jeg bruge en VNET med en skyen af websteder?** Du kan faktisk. Se [oprette en samling i skyen](remoteapp-create-cloud-deployment.md), særligt trin 1, kan finde flere oplysninger.

## <a name="authentication-options"></a>Indstillinger for godkendelse



- **Hvad med godkendelse? Hvilke metoder er understøttet?** Samlingen skyen understøtter Microsoft-konti og Azure Active Directory-konti, som er Office 365-konti. Samlingen hybrid understøtter kun Azure Active Directory-konti, der er blevet synkroniseret (ved hjælp af et værktøj som [Azure Active Directory-synkronisering](http://blogs.technet.com/b/ad/archive/2014/09/16/azure-active-directory-sync-is-now-ga.aspx)) fra en Windows Server Active Directory-installation nærmere betegnet kan enten synkroniseret med indstillingen synkronisering af adgangskoder eller synkroniseret med Active Directory Federation Services (AD FS) sammenslutning konfigureret. Du kan også konfigurere [Multi-Factor Authentication (MFA)](https://azure.microsoft.com/services/multi-factor-authentication/).

>[AZURE.NOTE]Azure Active Directory-brugere skal være fra de, der er knyttet til dit abonnement. (Du kan få vist og redigere dit abonnement på fanen **Indstillinger** på portalen. Se [ændre Azure Active Directory-lejer bruges af RemoteApp](remoteapp-changetenant.md) kan finde flere oplysninger).

- **Hvorfor kan jeg give mine adgang til Azure Active Directory-konto?** Azure Active Directory-brugere skal være fra den mappe, der er knyttet til dit abonnement. Du kan få vist eller redigere denne mappe under fanen Indstillinger på portalen. Se [ændre Azure Active Directory-lejer bruges af RemoteApp](remoteapp-changetenant.md) kan finde flere oplysninger.

## <a name="clients---what-device-can-i-use-to-access-azure-remoteapp"></a>Klienter - hvilken enhed kan jeg bruge til at få adgang til Azure RemoteApp?
Du kan finde klientoplysninger om god, herunder trinene til installation af forskellige klienter ved [at få adgang til dine apps i Azure RemoteApp](remoteapp-clients.md).

- **Hvilke enheder og operativsystemer klientprogrammerne understøtter?**
Første, computere og tablets: 
    - Windows 10 (client preview)
    - Windows 8.1 og Windows 8
    - Windows 7 Service Pack 1
    - Mac OS X
    - Windows RT
    - Android-tablets
    - iPads og telefoner:
    - iPhone
    - Android-telefon
    - Windows Phone
 
    [Hente](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx) et RemoteApp-klienten nu.
- **Understøtter Azure RemoteApp tynd kunder?** Ja, understøttes følgende Windows integreret tynde klienter:
    - Windows integreret Standard 7
    - Windows integreret 8 Standard
    - Windows integreret 8.1 branche Pro
    - Windows 10 IoT Enterprise

- **Hvilken version af Windows Server understøttes for Remote Desktop Session Host (RDSH)?** Windows Server 2012 R2.

## <a name="support-and-feedback"></a>Support og feedback


- **Hvad er planen for RemoteApp support?** Support til fakturering og abonnement leveres uden omkostninger. Teknisk support er tilgængelig via [Azure serviceplaner](https://azure.microsoft.com/support/plans/). Du kan også få gratis community support via vores [Azure diskussionsforum](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp). 
- **Hvordan kan jeg sende feedback?** Gå til [feedback-forummet](https://feedback.azure.com/forums/247748-azure-remoteapp/).
- **Hvem kan jeg tale for at få flere oplysninger om Azure RemoteApp?** Ud over vores [diskussionsforum](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp), som er et godt sted at stille spørgsmål, kan du deltage ugentlig [Spørg eksperter webinar](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html), hvor vi taler om alle ting RemoteApp.
- **Hvad med RemoteApp dokumentation?** Vi er så glad for du bliver bedt om. Ud over Hjælp-indhold i Hjælp til portalen skuffe (bare Klik på **?** i følgende artikler findes på en side i portalen), til at undervise dig om RemoteApp:
    - **Kom i gang:**
        - [Hvad er RemoteApp?](remoteapp-whatis.md)
        - [Hvad er RemoteApp skabelon billeder?](remoteapp-images.md)
        - [Hvordan fungerer licenser?](remoteapp-licensing.md)
        - [Hvordan RemoteApp og Office fungerer sammen?](remoteapp-o365.md)
        - [Hvordan fungerer omdirigering arbejde i RemoteApp](remoteapp-redirection.md)?
    - **Installere:**
        - [Oprette en brugerdefineret skabelonbillede](remoteapp-create-custom-image.md)
        - [Oprette en hybrid af websteder](remoteapp-create-hybrid-deployment.md)
        - [Oprette en skyen af websteder](remoteapp-create-cloud-deployment.md)
        - [Konfigurere Azure Active Directory til RemoteApp](remoteapp-ad.md)
        - [Publicere en app i RemoteApp](remoteapp-publish.md)
    - **Administrere:**
        - [Tilføje brugere](remoteapp-user.md)
        - [Bedste fremgangsmåder for at konfigurere og bruge RemoteApp](remoteapp-bestpractices.md)  

    Videoer! Vi har også en række videoer om RemoteApp. Nogle giver Introduktion ([Introduktion til Azure RemoteApp](https://azure.microsoft.com/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/)), mens andre fører dig igennem installation ([skyen installation](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be) og [hybridinstallation](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be)). Se dem!

 
### <a name="help-us-help-you"></a>Hjælp os med at hjælpe dig med at 
Vidste du, at ud over vurdering i denne artikel og foretage kommentarer ned under, du kan foretage ændringer i artiklen selve? Noget gå glip af? Noget galt? Jeg skrive noget, som er lige forvirrende? Rulle op og klikke på **Rediger på GitHub** for at foretage ændringer – dem, der kommer til os til gennemsyn, og klik derefter, når vi logge af dem, får du vist dine ændringer og forbedringer her.
