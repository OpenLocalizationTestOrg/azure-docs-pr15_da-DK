<properties
   pageTitle="Azure AD Connect: Opgradere fra DirSync | Microsoft Azure"
   description="Lær, hvordan du opgraderer fra DirSync til Azure AD-forbindelse. I denne artikel beskrives trinnene til opgradering fra DirSync til Azure AD-forbindelse."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.date="08/19/2016"
   ms.author="shoatman;billmath"/>

# <a name="azure-ad-connect-upgrade-from-dirsync"></a>Azure AD Connect: Opgradering fra DirSync
Azure AD-forbindelse er efterfølgende til DirSync. Du finder de måder, hvorpå du kan opgradere fra DirSync i dette emne. Disse trin virker ikke efter opgradering fra en anden version af Azure AD-forbindelse eller fra Azure Active Directory-synkronisering.

Før du går i gang med at installere Azure AD-forbindelse, Sørg for at [hente Azure AD-forbindelse](http://go.microsoft.com/fwlink/?LinkId=615771) , og Fuldfør trinnene i den foreløbige nødvendige [Azure AD-forbindelse: Hardware og forudsætninger, der er](../active-directory-aadconnect-prerequisites.md). Især, vil du læse om følgende, fordi disse områder er forskellig fra DirSync:

- Den nødvendige version af .net og PowerShell. Nyere versioner, der skal være på serveren end hvad DirSync behov.
- Konfigurationen af proxy-server. Hvis du bruger en proxyserver til at få adgang til internettet, kan denne indstilling skal konfigureres, før du opgraderer. DirSync benyttes altid proxy-server, der er konfigureret til den bruger, der installerer det, men Azure AD-forbindelse bruger computerindstillinger i stedet.
- De URL-adresser, der skal være åben i proxy-server. Til grundlæggende scenarier, dem, der understøtter også DirSync, er krav de samme. Hvis du vil bruge en af de nye funktioner, der følger med Azure AD-forbindelse, kan alle nye URL-adresser skal åbnes.

Hvis du ikke opgraderer fra DirSync, kan du se [relateret dokumentation](#related-documentation) til andre scenarier.

## <a name="upgrade-from-dirsync"></a>Opgradering fra DirSync
Afhængigt af det aktuelle DirSync-miljø er der forskellige muligheder for opgraderingen. Hvis den forventede opgradering arbejdstid er mindre end tre timer, er anbefalingen en lokal opgradering. Hvis den forventede opgradering tidspunkt er mere end tre timer, er anbefalingen at foretage en parallel installation på en anden server. Det er beregnede, hvis du har mere end 50.000 objekter kræver det mere end tre timer at gøre opgraderingen.

Scenarie |  
---- | ----
[Opgradering](#in-place-upgrade)  | Foretrukne indstilling, hvis opgraderingen forventes at tage mindre end 3 timer.
[Parallelle installation](#parallel-deployment) | Foretrukne indstilling, hvis opgraderingen forventes at tage mere end 3 timer.

>[AZURE.NOTE] Når du planlægger at opgradere fra DirSync til Azure AD-forbindelse, kan ikke fjerne DirSync selv før opgraderingen. Azure AD-forbindelse læser og overføre konfigurationen fra DirSync og fjerne efter undersøge serveren.

**Opgradering**  
Den forventede tid at gennemføre opgraderingen vises ved hjælp af guiden. Denne vurdering er baseret på den antagelse, at det tager tre timer at gennemføre en opgradering til en database med 50.000 objekter (brugere, kontakter og grupper). Hvis antallet af objekter i databasen er mindre end 50.000, anbefaler Azure AD-forbindelse en opgradering. Hvis du beslutter at fortsætte, de aktuelle indstillinger anvendes automatisk under opgraderingen, og din server genoptages automatisk active synkronisering.

Hvis du vil udføre en konfiguration overførsel, og gør en parallel installation, kan du tilsidesætte den lokalt opgradering anbefaling. Du kan for eksempel tage mulighed for at opdatere den hardware og operativsystem. I afsnittet [parallelle installation](#parallel-deployment) kan finde flere oplysninger.

**Parallelle installation**  
Hvis du har mere end 50.000 objekter, anbefales en parallel installation. Derved undgår funktionsdygtige forsinkelser erfarne af dine brugere. Azure AD-forbindelse installationen forsøger at anslå nedetid for opgraderingen, men hvis du har opgraderet DirSync tidligere, din egen oplevelse der sandsynligvis er den bedste vejledning.

### <a name="supported-dirsync-configurations-to-be-upgraded"></a>Understøttede DirSync konfigurationer til at blive opgraderet
Følgende konfiguration ændringer understøttes med DirSync opgraderes:

- Domæne og OU filtrering
- Alternativt ID (UPN)
- Synkronisering af adgangskoder og indstillinger for Exchange hybrid
- Dine skov/domæne og Azure AD-indstillinger
- Filtrering baseret på brugerattributter

Følgende ændring kan ikke opgraderes. Hvis du har denne konfiguration, er opgraderingen blokeret:

- Ikke-understøttede DirSync ændringer, fjernet for eksempel attributter og bruge en brugerdefineret filtypenavnet DLL

![Opgradering er blokeret](./media/active-directory-aadconnect-dirsync-upgrade-get-started/analysisblocked.png)

I disse tilfælde anbefales det at installere en ny server Azure AD-forbindelse i [arrangere tilstand](../active-directory-aadconnectsync-operations.md#staging-mode) og bekræft den gamle DirSync og nye Azure AD-forbindelse konfiguration. Genanvende eventuelle ændringer ved hjælp af brugerdefinerede konfiguration, som beskrevet i [Azure Active Directory forbinde-synkronisering brugerdefineret konfiguration](../active-directory-aadconnectsync-whatis.md).

De adgangskoder, der bruges af DirSync for-tjenestekonti kan ikke hentes og bliver ikke overflyttet. Disse adgangskoder nulstilles under opgraderingen.

### <a name="high-level-steps-for-upgrading-from-dirsync-to-azure-ad-connect"></a>Overordnede trin til opgradering fra DirSync til Azure AD-forbindelse

1. Velkommen til Azure AD-forbindelse
2. Analyse af aktuelle DirSync-konfiguration
3. Indsamle Azure AD globale administratoradgangskode
4. Indsamle legitimationsoplysninger for en enterprise-administratorkonto (kun bruges under installationen af Azure AD-forbindelse)
5. Installationen af Azure AD-forbindelse
    * Fjerne DirSync (eller midlertidigt at deaktivere den)
    * Installer Azure AD-forbindelse
    * Du kan også starte synkronisering

Der kræves yderligere trin hvornår:

* Du bruger aktuelt fuld SQL Server - lokale eller en fjerndatabase
* Du har mere end 50.000 objekter i området for synkronisering

## <a name="in-place-upgrade"></a>Opgradering

1. Start Azure AD-forbindelse installer (MSI).
2. Gennemse og accepterer du licensvilkårene og erklæring om beskyttelse.
![Velkommen til Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Welcome.png)
3. Klik på Næste for at starte analysen af din eksisterende DirSync-installation.
![Analyse af eksisterende installation af katalogsynkronisering](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Analyze.png)
4. Når analysen er fuldført, kan du se anbefalingerne at fortsætte.  
    - Hvis du bruger SQL Server Express og har mindre end 50.000 objekter, vises følgende skærmbillede: ![analyse af udført klar til at opgradere fra DirSync](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReady.png)
    - Hvis du bruger en fuld SQL Server for DirSync skal du se denne side i stedet: ![analyse af udført klar til at opgradere fra DirSync](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png)  
Oplysninger om den eksisterende SQL Server-databaseserver bruges af DirSync vises. Foretag relevante justeringer, hvis det er nødvendigt. Klik på **Næste** for at fortsætte med installationen.
    - Hvis du har mere end 50.000 objekter, skal du se dette skærmbillede i stedet: ![analyse af udført klar til at opgradere fra DirSync](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)  
Klik på afkrydsningsfeltet ud for denne meddelelse for at fortsætte med en opgradering: **fortsætter med at opgradere DirSync på denne computer.**
For at udføre en [parallel installation](#parallel-deployment) i stedet skal du eksportere konfigurationsindstillinger DirSync og flytte konfigurationen til den nye server.
5. Angive adgangskoden for den konto, du aktuelt bruger til at oprette forbindelse til Azure AD. Det skal være den konto, der er i øjeblikket bruges af DirSync.
![Angiv dine Azure AD-legitimationsoplysninger](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToAzureAD.png)  
Hvis du modtager en fejl og har problemer med forbindelse, kan du se [fejlfinding af forbindelsesproblemer](../active-directory-aadconnect-troubleshoot-connectivity.md).
6. Give en enterprise-administratorkonto til Active Directory.
![Angiv dine legitimationsoplysninger tilføjer](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToADDS.png)
7. Nu er du klar til at konfigurere. Når du klikker på **Opgrader**, DirSync er fjernet, og Azure AD-forbindelse er konfigureret og begynder at synkronisere.
![Klar til at konfigurere](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ReadyToConfigure.png)
8. Når installationen er fuldført, logge af og log på igen til Windows før du bruger synkronisering Service Manager, synkronisering regel Editor, eller forsøger at foretage andre ændringer i konfigurationen.

## <a name="parallel-deployment"></a>Parallelle installation

### <a name="export-the-dirsync-configuration"></a>Eksportere DirSync-konfiguration
**Parallelle-installation med mere end 50.000 objekter**

Hvis du har mere end 50.000 objekter, anbefaler Azure AD-forbindelse installationen en parallel installation.

Der vises et skærmbillede, der ligner følgende:

![Analysen er fuldført](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

Hvis du vil fortsætte med parallelle installation, skal du udføre følgende trin:

- Klik på knappen **eksportere indstillinger** . Når du installerer Azure AD-forbindelse på en anden server, overføres disse indstillinger fra din aktuelle DirSync til installationen af nye Azure AD-forbindelse.

Når du har eksporteret dine indstillinger, kan du afslutte guiden Azure AD-forbindelse på DirSync-server. Fortsæt med det næste trin at [installere Azure AD-forbindelse på en anden server](#installation-of-azure-ad-connect-on-separate-server)

**Parallelle-installation med mindre end 50.000 objekter**

Hvis du har mindre end 50.000 objekter, men stadig vil gøre en parallel installation og derefter gøre følgende:

1. Kør installationsprogrammet til Azure AD-forbindelse (MSI).
2. Når du ser skærmbilledet **Velkommen til Azure AD-forbindelse** , skal du afslutte installationsguiden ved at klikke på "X" i øverste højre hjørne af vinduet.
3. Åbn en kommandoprompt.
4. Fra installationsplaceringen af Azure AD-forbindelse (standard: C:\Program Files\Microsoft Azure Active Directory forbinde) du udføre følgende kommando:  `AzureADConnect.exe /ForceExport`.
5. Klik på knappen **eksportere indstillinger** .  Når du installerer Azure AD-forbindelse på en anden server, overføres disse indstillinger fra din aktuelle DirSync til installationen af nye Azure AD-forbindelse.

![Analysen er fuldført](./media/active-directory-aadconnect-dirsync-upgrade-get-started/forceexport.png)

Når du har eksporteret dine indstillinger, kan du afslutte guiden Azure AD-forbindelse på DirSync-server. Fortsæt med det næste trin at [installere Azure AD-forbindelse på en anden server](#installation-of-azure-ad-connect-on-separate-server)

### <a name="install-azure-ad-connect-on-separate-server"></a>Installer Azure AD-forbindelse på separat server

Når du installerer Azure AD-forbindelse på en ny server, antages det, du vil udføre en ren installation af Azure AD-forbindelse. Da du vil bruge DirSync konfigurationen, er der nogle ekstra trin:

1. Kør installationsprogrammet til Azure AD-forbindelse (MSI).
2. Når du ser skærmbilledet **Velkommen til Azure AD-forbindelse** , skal du afslutte installationsguiden ved at klikke på "X" i øverste højre hjørne af vinduet.
3. Åbn en kommandoprompt.
4. Fra installationsplaceringen af Azure AD-forbindelse (standard: C:\Program Files\Microsoft Azure Active Directory forbinde) du udføre følgende kommando:  `AzureADConnect.exe /migrate`.
    Installationsguiden af Azure AD-forbindelse starter og giver dig følgende skærmbillede: ![Angiv dine Azure AD-legitimationsoplysninger](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ImportSettings.png)
5. Vælg den indstillingsfil, der er eksporteret fra din DirSync-installation.
6. Konfigurere en hvilken som helst avancerede indstillinger, herunder:
    - En brugerdefineret installationsplacering til Azure AD-forbindelse.
    - En eksisterende forekomst af SQL Server (standard: Azure AD-forbindelse installerer SQL Server 2012 Express). Brug ikke den samme database forekomst som DirSync-server.
    - En tjenestekonto, der bruges til at oprette forbindelse til SQL Server (hvis SQL Server-databasen er remote, og derefter denne konto skal være en domænekonto service).
Disse indstillinger kan ses på dette skærmbillede: ![Angiv dine Azure AD-legitimationsoplysninger](./media/active-directory-aadconnect-dirsync-upgrade-get-started/advancedsettings.png)
7. Klik på **Næste**.
8. Lad være **starte synkroniseringen som konfigurationen er fuldført** markeret på siden **klar til at konfigurere** . Serveren er nu i [arrangere tilstand](../active-directory-aadconnectsync-operations.md#staging-mode) , så ændringer ikke eksporteres til Azure AD.
9. Klik på **Installer**.
10. Når installationen er fuldført, logge af og log på igen til Windows før du bruger synkronisering Service Manager, synkronisering regel Editor, eller forsøger at foretage andre ændringer i konfigurationen.

>[AZURE.NOTE] Synkronisering mellem Windows Server Active Directory og Azure Active Directory begynder, men ingen ændringer, der eksporteres til Azure AD. Kun én Synkroniseringsværktøj kan aktivt eksport ændringer ad gangen. Denne tilstand kaldes [arrangere tilstand](../active-directory-aadconnectsync-operations.md#staging-mode).

### <a name="verify-that-azure-ad-connect-is-ready-to-begin-synchronization"></a>Bekræft, at Azure AD-forbindelse er klar til at starte synkronisering

Hvis du vil kontrollere, er klar til at tage over fra DirSync Azure AD-forbindelse, skal du åbne **Synkronisering Service Manager** i gruppen **Azure AD-forbindelse** fra startmenuen.

Gå til fanen **Handlinger** i programmet. Under denne fane kan du bekræfte, at følgende handlinger har fuldført:

- Importér på AD-forbindelse
- Importér på Azure AD-forbindelse
- Fuld Synkroniser på AD-forbindelse
- Fuld Synkroniser på Azure AD-forbindelse

![Importere og synkroniseringen er fuldført.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/importsynccompleted.png)

Gennemse resultatet fra disse handlinger, og sikre, at der ikke er nogen fejl.

Hvis du vil have vist, og Undersøg de ændringer, om der skal eksporteres til Azure AD derefter læses Sådan kontrolleres konfiguration under [midlertidige tilstand](../active-directory-aadconnectsync-operations.md#staging-mode). Foretage ændringer i påkrævet konfigurationen, indtil du ikke kan se noget uventet.

Du er klar til at skifte fra DirSync til Azure AD, når du har udført disse trin, og du er tilfreds med resultatet.

### <a name="uninstall-dirsync-old-server"></a>Fjerne DirSync (gamle server)

- Find **Windows Azure Active Directory-Synkroniseringsværktøj** i **programmer og funktioner**
- Fjerne **Windows Azure Active Directory-Synkroniseringsværktøj**
- Fjernelsen kan tage op til 15 minutter at gennemføre.

Hvis du foretrækker at fjerne DirSync senere, kan du også midlertidigt lukker serveren eller deaktivere tjenesten. Hvis noget går galt, kan denne metode du aktivere den igen. Det forventes dog ikke, at det næste trin mislykkes, så det ikke skal skal bruges.

Med DirSync fjernet eller deaktiveret, er der ingen active server, eksportere til Azure AD. Næste trin for at aktivere Azure AD-forbindelse skal være fuldført, før ændringerne i din lokale Active Directory fortsætter med at blive synkroniseret til Azure AD.

### <a name="enable-azure-ad-connect-new-server"></a>Aktivere Azure AD-forbindelse (nye server)
Efter installationen, genåbne Azure AD forbinde er muligt at foretage ændringer i yderligere konfigurationen. Starte **Azure AD-forbindelse** fra startmenuen eller genvejen på skrivebordet. Kontrollér, at du ikke forsøger at køre installationen MSI igen.

Du burde se følgende:

![Flere opgaver](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AdditionalTasks.png)

- Vælg **Konfigurer arrangere tilstand**.
- Slå fra arrangere ved at fjerne markeringen i afkrydsningsfeltet **aktiveret midlertidige tilstand** .

![Angiv dine Azure AD-legitimationsoplysninger](./media/active-directory-aadconnect-dirsync-upgrade-get-started/configurestaging.png)

- Klik på knappen **Næste**
- Klik på knappen **Installer** på siden confirmation.

Azure AD-forbindelse er nu din active server.

## <a name="next-steps"></a>Næste trin
Nu hvor du har installeret Azure AD-forbindelse kan du [kontrollere installationen og tildele licenser](../active-directory-aadconnect-whats-next.md).

Få flere oplysninger om disse nye funktioner, som er blevet aktiveret til installationen: [automatisk opgradering](../active-directory-aadconnect-feature-automatic-upgrade.md), [Undgå utilsigtede sletninger](../active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)og [Azure AD forbinde tilstand](../active-directory-aadconnect-health-sync.md).

Få flere oplysninger om disse almindelige emner: [scheduler og hvordan du kan udløse synkronisering](../active-directory-aadconnectsync-feature-scheduler.md).

Lær mere om at [integrere dine lokale identiteter med Azure Active Directory](../active-directory-aadconnect.md).

## <a name="related-documentation"></a>Relateret dokumentation

Emne |  
--------- | ---------
Oversigt over Azure AD-forbindelse | [Integrere dine lokale identiteter med Azure Active Directory](../active-directory-aadconnect.md)
Opgradere fra en tidligere version af Connect | [Opgradere fra en tidligere version af Connect](../active-directory-aadconnect-upgrade-previous-version.md)
Installere ved hjælp af indstillinger for Express | [Hurtig installation af Azure AD-forbindelse](active-directory-aadconnect-get-started-express.md)
Installere ved hjælp af brugerdefinerede indstillinger | [Brugerdefineret installation af Azure AD-forbindelse](active-directory-aadconnect-get-started-custom.md)
Konti, der bruges til installation | [Flere oplysninger om Azure AD-forbindelse konti og tilladelser](active-directory-aadconnect-accounts-permissions.md)
