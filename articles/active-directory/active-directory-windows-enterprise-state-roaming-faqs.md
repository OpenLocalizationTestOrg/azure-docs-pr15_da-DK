<properties
    pageTitle="Indstillinger og data roaming ofte stillede spørgsmål om | Microsoft Azure"
    description="Indeholder svar på nogle spørgsmål IT-administratorer kan have om indstillinger og app data synkronisering."
    services="active-directory"
    keywords="Enterprise tilstand globale indstillinger for windows skyen, ofte stillede spørgsmål på enterprise tilstand roaming"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="settings-and-data-roaming-faq"></a>Indstillinger og data roaming ofte stillede spørgsmål

Dette emne finder du svar på nogle spørgsmål, IT-administratorer kan have om indstillinger og app data synkronisering.

## <a name="what-data-roams"></a>Hvilke data logger?
**Windows-indstillinger**: PC-indstillinger, der er indbygget i Windows-operativsystemet. Generelt er indstillingerne, tilpasser din PC, og de omfatter følgende generelle kategorier:

- *Tema*, der omfatter funktioner som indstillingerne for skrivebordet tema og proceslinjen.
- *Indstillinger for Internet Explorer*, herunder senest åbnet faner og favoritter.
- *Kant browserindstillinger*, som favoritter og læseliste.
- *Adgangskoder*, som herunder adgangskoder til internettet, Wi-Fi profiler og andre.
- *Sprogindstillinger*, som omfatter indstillinger for tastaturlayout, systemsprog, dato og klokkeslæt og mere.
- *Øget access-funktioner*, som tema i høj kontrast, Oplæser og Forstørrelsesglas.
- *Andre Windows-indstillinger*, som kommandoprompt indstillinger og programmets liste over filer.


**Application data**: Universal Windows apps kan skrive indstillinger data til en global mappe og alle data, der er skrevet til denne mappe automatisk blive synkroniseret. Det er op til individuelle appudvikleren til at designe en app til at drage fordel af denne egenskab. Få mere at vide om, hvordan du udvikler en Universal Windows app, der bruger roaming, se [appdata lagerplads API](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) og [Windows 8 appdata globale udvikler blog](http://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx).

## <a name="what-account-is-used-for-settings-sync"></a>Hvilken konto der bruges til indstillinger for synkronisering?
I Windows 8 og Windows 8.1 bruges indstillinger for synkronisering altid consumer Microsoft-konti. Enterprise-brugere havde muligheden for at forbinde en Microsoft-konto til deres Active Directory domænekonto for at få adgang til indstillinger for synkronisering. I Windows 10 forbindelse dette funktionalitet erstattes med en primære/sekundære konto framework Microsoft-konto.

Den primære konto er defineret som den konto, der bruges til at logge på Windows. Det kan være en Microsoft-konto, en Azure Active Directory (Azure AD)-konto, et lokalt Active Directory-konto eller en lokal konto. Windows 10-brugere kan føje en eller flere sekundære skyen konti til deres enhed ud over den primære konto. En sekundær konto er generelt en Microsoft-konto, en Azure AD-konto eller en anden konto som Gmail eller Facebook. Disse sekundære konti giver adgang til flere tjenester som enkeltlogon og Windows Store, men de kan ikke tænd indstillinger for synkronisering.

I Windows 10, kun den primære konto for enheden kan bruges til indstillinger for synkronisering (se [hvordan opgraderer jeg fra Microsoft-konto indstillinger for synkronisering i Windows 8 til Azure AD synkronisering af indstillinger i Windows 10?](active-directory-windows-enterprise-state-roaming-faqs.md#How-do-I-upgrade-from-Microsoft-account-settings-sync-in-Windows-8-to-Azure-AD-settings-sync-in Windows-10?)).

Data er aldrig blandet mellem de forskellige brugerkonti på enheden. Der er to regler for indstillinger for synkronisering:

- Indstillinger for Windows altid er global med den primære konto.
- App-data kan være mærket med den konto, der bruges til at hente appen. Kun apps, der er mærket med den primære konto synkroniseres. App ejerskab mærkning bestemmes, når en app er side indlæst via Windows Store eller administration af mobilenheder (MDM).

Hvis en app ejer ikke kan identificeres, er det global med den primære konto. Hvis en enhed er opgraderet fra Windows 8 eller Windows 8.1 til Windows 10, bliver alle apps mærket, som er anskaffet ved Microsoft-kontoen. Dette skyldes, at de fleste brugere købe apps til Windows Store, og der er ingen Windows Store-understøttelse for Azure AD-konti før Windows 10. Hvis en app er installeret via en offline-licens, tagging appen ved hjælp af den primære konto på enheden.

>[AZURE.NOTE]  
> Windows 10-enheder, der er ejet enterprise og har forbindelse til Azure AD kan ikke længere forbinde deres Microsoft-konto til en domænekonto. Muligheden for at forbinde en Microsoft-konto til en domænekonto og har alle brugerens data Synkroniser med Microsoft-kontoen (det vil sige, den Microsoft-konto roaming via Microsoft-konto og Active Directory-funktionalitet) fjernes fra Windows 10-enheder, der er tilsluttet et forbundne Active Directory eller Azure AD-miljø.

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Hvordan opgraderer jeg fra Microsoft-konto indstillinger for synkronisering i Windows 8 til Azure AD synkronisering af indstillinger i Windows 10?
Hvis du er optaget i Active Directory-domæne, der kører Windows 8 eller Windows 8.1 med en Microsoft-konto, kan du synkronisere indstillinger via din Microsoft-konto. Efter opgradering til Windows 10, fortsætter du med at synkronisere brugerindstillinger via Microsoft-konto, så længe du bruger et medlem af et domæne og Active Directory-domænet kan ikke oprette forbindelse med Azure AD.

Hvis lokale Active Directory-domænet kan oprette forbindelse med Azure AD, enheden vil forsøge at synkronisere indstillinger ved hjælp af den forbundne Azure AD-konto. Hvis administratoren Azure AD ikke aktiverer Enterprise tilstand Roaming, din forbundne Azure AD-konto synkroniseres ikke længere indstillinger. Hvis du bruger Windows 10, og du logger på med en Azure AD-identitet, starter du synkroniseringen windows-indstillinger, så snart din administrator aktiverer indstillinger for synkronisering via Azure AD.

Hvis du har gemt dine personlige data på enheden virksomhedens, skal du være opmærksom på, at Windows OS og programmet data begynder synkroniseret til Azure AD. Dette har følgende konsekvenserne:

- Indstillingerne for din personlige Microsoft-konto kan drift bortset fra indstillingerne på dit arbejde eller skole Azure AD-konti. Dette skyldes, at Microsoft-konto og indstillinger for Azure AD synkronisere nu bruger forskellige konti.
- Personlige data som Wi-Fi adgangskoder, web legitimationsoplysninger og Internet Explorer Favoritter, som tidligere er synkroniseret via en Microsoft-konto synkroniseres via Azure AD.


## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Hvordan får Microsoft-konto og Azure AD Enterprise tilstand global interoperabilitet arbejde?
I November 2015 eller nyere versioner af Windows 10 understøttes Enterprise tilstand Roaming kun for en enkelt konto ad gangen. Hvis du logge på Windows ved hjælp af en arbejds- eller skolekonto Azure AD, synkroniseres alle data via Azure AD. Hvis du logger på Windows ved hjælp af en personlig Microsoft-konto, synkroniserer alle data via Microsoft-konto. Universal appdata er global ved hjælp af kun primære Log på kontoen på enheden, og den global kun, hvis den app licens ejes af den primære konto. Universal appdata for de apps, der er ejet af enhver sekundære firmaer vil ikke blive synkroniseret.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>Synkroniserer indstillinger for Azure AD-konti fra flere-lejere?
Når flere Azure AD-konti fra forskellige Azure AD-lejere er på den samme enhed, skal du opdatere enhedens registreringsdatabasen til at kommunikere med Azure Rights Management (Azure RMS) for hver Azure AD-lejer.  

1. Find GUID'EN for hver Azure AD-lejer. Åbn Azure klassisk-portalen, og vælg en Azure AD-lejer. GUID for lejeren er i URL-adressen i adresselinjen i browseren. Eksempel: `https://manage.windowsazure.com/YourAccount.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/Tenant GUID/directoryQuickStart`
2. Når du har GUID, skal du tilføje registreringsdatabasenøglen **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<lejer ID GUID >**.
Oprette en ny med flere strengværdi (REG-MULTI-SZ) med navnet **AllowedRMSServerUrls**fra nøglen **lejer ID GUID** . For dataene, angive den licensering fordeling punkt URL-adresserne på de Azure lejere enheden har adgang til.
3. Du kan finde licensering fordeling punkt URL ved at køre Cmdletten **Get-AadrmConfiguration** . Hvis værdierne for **LicensingIntranetDistributionPointUrl** og **LicensingExtranetDistributionPointUrl** er forskellige, kan du angive begge værdier. Hvis værdierne, der er den samme, skal du angive værdien kun én gang.


## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Hvad er de globale indstillinger for eksisterende Windows-programmerne?
Globale fungerer kun for Universal Windows apps. Der er to muligheder for at aktivere roaming på en eksisterende Windows-programmet:

- [Skrivebord bro](http://aka.ms/desktopbridge) hjælper dig med at flytte dine eksisterende Windows-programmer til Universal Windows-Platform. Herfra kan er minimale kode ændringerne påkrævet for at drage fordel af Azure AD app data globale. Skrivebord bro indeholder dine apps med en app identitet, som er nødvendig for at aktivere app-data roaming til eksisterende-programmer.
- [Bruger oplevelse Virtualization (UE-V)](https://technet.microsoft.com/library/dn458947.aspx) hjælper dig med at oprette en brugerdefineret indstillinger-skabelon til eksisterende Windows-programmer og aktivere roaming til Win32-apps. Denne indstilling kræver ikke app-udvikleren ændre koden for appen. UE-V er begrænset til lokale Active Directory roaming til kunder, der har købt Microsoft Desktop optimering Pack.

Administratorer kan konfigurere UE-V for at gennemsøge data i Windows-programmet på computeren ved at ændre globale indstillinger for Windows-Operativsystem og Universal app data via [UE-V gruppere politikker](https://technet.microsoft.com/itpro/mdop/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2), herunder:

- Gennemsøge indstillinger for gruppepolitik i Windows
- Synkroniseres ikke Windows Apps Gruppepolitik
- Internet Explorer roaming i sektionen programmer

Fremover, kan Microsoft Undersøg måder til at foretage UE-V dybt integreret i Windows og udvide UE-V for at gennemsøge indstillinger via Azure AD skyen.


## <a name="can-i-store-synced-settings-and-data-on-premises"></a>Kan jeg gemme synkroniserede indstillinger og data lokalt?
Enterprise tilstand Roaming gemmer alle synkroniserede data i Azure skyen. UE-V tilbyder et lokalt roaming løsning.

## <a name="who-owns-the-data-thats-being-roamed"></a>Hvem ejer de data, er der globalt?
De egen virksomheder dataene globalt via Enterprise tilstand Roaming. Data er gemt i en Azure datacenter. Alle brugerdata er krypteret både undervejs og på resten i skyen ved hjælp af Azure RMS. Dette er en forbedring af sammenlignet med Microsoft-konto-baserede indstillinger synkronisering, som krypterer kun visse følsomme oplysninger som brugerlegitimationsoplysninger, før det forlader enheden.

Microsoft er forpligtet til at beskytte kundedata. Indstillinger for en enterprise-brugerdata krypteres automatisk ved Azure RMS, før det forlader en Windows 10-enhed, så ingen andre brugere kan læse disse data. Hvis din organisation har et betalt abonnement til Azure RMS, kan du bruge andre Azure RMS-funktioner, som Registrer og ophæve dokumenter, automatisk beskytte mails, der indeholder følsomme oplysninger og administrere dine egne taster ("få din egen nøgle" løsningen, også kaldet BYOK). Se [Hvad er Azure Rights Management](https://technet.microsoft.com/jj585026.aspx)kan finde flere oplysninger om disse funktioner, og hvordan Azure RMS fungerer.

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>Kan jeg administrere synkronisering af for en bestemt app eller indstilling?
I Windows 10 er der ingen MDM eller en gruppepolitik indstilling til at deaktivere globale for et enkelt program. Lejer administratorer kan deaktivere synkronisering af appdata for alle apps på en enhed med administrerede, men der er ingen bedre kontrol på et-app eller i app-niveau.

## <a name="how-can-i-enable-or-disable-roaming"></a>Hvordan kan jeg aktivere eller deaktivere globale?
I app'en, **Indstillinger** , skal du gå til **konti** > **synkronisere dine indstillinger**. Fra denne side, kan du se, hvilken konto der anvendes til at gennemsøge indstillinger, og du kan aktivere eller deaktivere individuelle grupper af indstillinger for at være globalt.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>Hvad er Microsofts anbefaling for aktivering af globale i Windows 10?
Microsoft har et par forskellige indstillinger for roaming løsninger, der er tilgængelige, herunder globale brugerprofiler, UE-V og Enterprise tilstand Roaming.  Microsoft er sikrer, at en investering i Enterprise tilstand Roaming i fremtidige versioner af Windows. Hvis din organisation ikke er klar eller vant til at flytte data i skyen, derefter anbefaler vi, at du bruger UE-V som din primære globale teknologi. Hvis din organisation kræver globale understøttelse af eksisterende Windows-programmerne på computeren, men er rundvisningen til at flytte til skyen, anbefaler vi, at du bruger både Enterprise tilstand globale og UE-V. Selvom UE-V og Enterprise tilstand globale minder meget om teknologier, er de er ikke udelukker. De supplement hinanden for at sikre, at din organisation leverer de globale tjenester, har du brug for dine brugere.  

Når du bruger både Enterprise tilstand globale og UE-V, gælder følgende regler:

- Enterprise tilstand Roaming er den primære globale agent på enheden. UE-V der anvendes til at tillæg "Win32-mellemrummet."
- UE-V roaming til Windows-indstillinger og moderne UWP app-data skal være deaktiveret, når ved hjælp af gruppen UE-V politikker. Disse er allerede er omfattet af Enterprise tilstand Roaming.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>Hvordan understøtter Enterprise tilstand Roaming virtual desktop-infrastruktur (VDI)?
Enterprise tilstand Roaming understøttes på Windows 10 klient-SKU'er, men ikke på serveren lagerenheder. Hvis en klient VM hostes på en hypervisor maskine, og du eksternt logger på den virtuelle maskine, global dine data. Hvis flere brugere deler de samme OS og brugere fra en fjernplacering logge på en server til en komplette pc oplevelse, globale fungerer muligvis ikke. I denne session-baserede scenario understøttes ikke officielt.


## <a name="what-happens-when-my-organization-purchases-azure-rms-after-using-roaming"></a>Hvad sker der, når organisationen køber Azure RMS efter brug af globale?
Hvis organisationen allerede bruger roaming i Windows 10 med Azure RMS begrænset brug gratis abonnement skal købe et betalt abonnement Azure RMS der vil ikke have en hvilken som helst effekt på funktionaliteten i den globale funktion, og ingen ændringer i konfigurationen vil blive krævet af IT-administratoren.

## <a name="known-issues"></a>Kendte problemer

- Hvis du forsøger at logge på din Windows-enhed ved hjælp af et chipkort eller virtuelle chipkort, stoppe synkronisering af indstillinger for arbejde. Fremtidige opdateringer til Windows 10 kan løse dette problem.
- Du skal den juli kumulative opdatering til Windows 10 (build 10586.494 eller nyere) for Internet Explorer Favoritter synkronisering for at arbejde.
- Data, der er beskyttet med beskyttelse af Windows oplysninger synkroniseres ikke gennem Enterprise tilstand Roaming. Desuden påvirkes maskiner, der har aktiveret beskyttelse af Windows oplysninger ikke tema synkronisering. 
- Under visse omstændigheder kan Enterprise tilstand Roaming ikke synkronisere data Hvis Azure Multi-Factor Authentication er konfigureret.
    - Hvis enheden er konfigureret til at kræve [Multi-Factor Authentication](multi-factor-authentication.md) på Azure Active Directory-portalen, kan du ikke kunne synkronisere indstillinger, mens du logge på en Windows 10-enhed med en adgangskode. Denne type Multi-Factor Authentication konfiguration er beregnet til at beskytte en Azure administratorkonto. Administrator brugere kan stadig ikke kunne synkronisere ved at logge på deres Windows 10-enheder med deres [Microsoft Passport for det arbejde,](active-directory-azureadjoin-passport.md) pinkode eller ved at udføre Multi-Factor Authentication under adgang til andre Azure tjenester som Office 365.
    - Synkronisering kan mislykkes, hvis administratoren konfigurerer Active Directory Federation Services Multi-Factor Authentication betinget access-politik og adgangstoken på enheden udløber.  Sørg for, at logge på og logge af brug af [Microsoft Passport for det arbejde,](active-directory-azureadjoin-passport.md) pinkode eller fuldføre Multi-Factor Authentication under adgang til andre Azure tjenester som Office 365.

- Hvis en maskine er medlem af et domæne med automatisk registrering til Azure Active Directory-enheder, kan det opleve synkronisering fejl, hvis computeren er for længere tid, og ikke kan gennemføre godkendelse af domæner. Du kan løse dette problem, du Forbind maskinen til virksomhedens netværk, så kan genoptage synkronisering.


## <a name="related-topics"></a>Relaterede emner
- [Enterprise tilstand globale oversigt](active-directory-windows-enterprise-state-roaming-overview.md)
- [Aktivere enterprise tilstand roaming i Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
- [Gruppere politik og MDM indstillinger for synkronisering af indstillinger](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
- [Windows 10 globale indstillinger reference](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
