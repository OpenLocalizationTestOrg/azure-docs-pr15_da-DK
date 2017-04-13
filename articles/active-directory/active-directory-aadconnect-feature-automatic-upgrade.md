<properties
   pageTitle="Azure AD-forbindelse: Automatisk opgradering | Microsoft Azure"
   description="Dette emne beskrives den indbyggede funktion til automatisk opgradering i Azure AD-forbindelse synkronisering."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/24/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD-forbindelse: Automatisk opgradering
Denne funktion blev introduceret med build 1.1.105.0 (udgivet februar 2016).

## <a name="overview"></a>Oversigt
At sikre, at installationen af Azure AD-forbindelse altid er opdateret har aldrig været nemmere med funktionen **automatisk opgradering** . Denne funktion er aktiveret som standard for udtrykkelig installationer og DirSync opgraderinger. Når en ny version er udgivet, opgraderet din installation automatisk.

Automatisk opgradering er aktiveret som standard for følgende:

- Express indstillinger installations- og DirSync opgraderinger.
- Brug af SQL Express LocalDB, som er, hvad Express indstillinger Brug altid. DirSync med SQL Express også bruge LocalDB.
- AD-konto er standardkontoen MSOL_ oprettet af Express indstillinger og DirSync.
- Har mindre end 100.000 objekter i metaverse.

Du kan få vist den aktuelle status for automatisk opgradering med PowerShell-cmdlet `Get-ADSyncAutoUpgrade`. Det har følgende tilstande:

Stat | Kommentar
---- | ----
Aktiveret | Automatisk opgradering er aktiveret.
Suspenderet | Angiv ved systemet kun. Systemet ikke længere er berettiget til at modtage automatiske opgraderinger.
Deaktiveret | Automatisk opgradering er deaktiveret.

Du kan skifte mellem **aktiveret** og **deaktiveret** med `Set-ADSyncAutoUpgrade`. Kun systemet skal angive tilstanden **afbrudt**.

Automatisk opgradering bruger Azure AD forbinde tilstand for opgradering infrastrukturen. Kontrollér, at du har åbnet URL-adresserne på din proxyserver til **Azure AD forbinde sundhed** , som beskrevet i [Office 365 URL-adresser og IP-adresseområder](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)for automatisk opgradering til at arbejde.

Hvis **Synkronisering Service Manager** brugergrænseflade, der kører på serveren, derefter opgraderingen afbrydes midlertidigt, indtil Brugergrænsefladen er lukket.

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med
Hvis din forbinde installation ikke kan opgradere sig selv som forventet, følg derefter disse trin for at finde ud af, hvad kan være forkerte.

Først skal forventer du ikke den automatiske opgradering til at blive forsøgt den første dag er udgivet en ny version. Der findes en bevidst tilfældigheden ved, før en opgradering forsøges, så ikke forhindres, hvis din installation ikke er opgraderet med det samme.

Hvis du tror noget ikke er rigtigt, derefter først køre `Get-ADSyncAutoUpgrade` at sikre, at automatisk opgradering er aktiveret.

Kontrollér derefter, at du har åbnet de påkrævede URL-adresser i din proxy- eller firewall. Automatisk opdatering bruger Azure AD forbinde sundhed som beskrevet i [Oversigt](#overview). Hvis du bruger en proxy, skal du kontrollere tilstand er blevet konfigureret for at bruge en [proxy-server](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Også teste [sundhed connectivity](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) til Azure AD.

Med forbindelse til Azure AD godkendt, er det tid at undersøge eventlogs. Start Logbog, og se i **programmet** hændelseslog. Tilføje et hændelsesloggen filter for kilden **Azure AD forbinde opgradere** og begivenhed id område **300 399**.  
![Hændelseslog filter for automatisk opgradering](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogfilter.png)  

Du kan nu se den eventlogs, der er knyttet til status for automatisk opgradering.  
![Hændelseslog filter for automatisk opgradering](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogresult.png)  

Resultatkoden er et præfiks med et overblik over tilstanden.

Resultatet kodepræfiks | Beskrivelse
--- | ---
Succes | Installationen er blevet opgraderet.
UpgradeAborted | En midlertidig tilstand standses opgraderingen. Det forsøges igen, og det forventes, at det lykkes senere.
UpgradeNotSupported | Systemet har en konfiguration, der blokerer systemet fra opgraderes automatisk. Det vil blive forsøgt igen for at få vist Hvis tilstanden ændres, men det forventes, at systemet skal opgraderes manuelt.

Her er en liste over de mest almindelige meddelelser, du har fundet. Det viser ikke alle, men meddelelsen resultatet skal være Ryd med hvilke problemet.

Resultatmeddelelse | Beskrivelse
--- | ---
**UpgradeAborted** |
UpgradeAbortedCouldNotSetUpgradeMarker | Kunne ikke skrive i registreringsdatabasen.
UpgradeAbortedInsufficientDatabasePermissions | Gruppen indbyggede administratorer har ikke tilladelser til databasen. Manuelt opgradere til den nyeste version af Azure AD-forbindelse til at løse dette problem.
UpgradeAbortedInsufficientDiskSpace | Der er ikke nok ledig diskplads til at understøtte en opgradering.
UpgradeAbortedSecurityGroupsNotPresent | Kan ikke finde og løse alle sikkerhedsgrupper, der bruges af synkronisering af programmet.
UpgradeAbortedServiceCanNotBeStarted | NT-tjenesten **Microsoft Azure Active Directory-synkronisering** kunne ikke startes.
UpgradeAbortedServiceCanNotBeStopped | NT-tjenesten **Microsoft Azure Active Directory-synkronisering** kunne ikke stoppes.
UpgradeAbortedServiceIsNotRunning | NT-tjenesten **Microsoft Azure Active Directory-synkronisering** kører ikke.
UpgradeAbortedSyncCycleDisabled | Indstillingen SyncCycle i [scheduler](active-directory-aadconnectsync-feature-scheduler.md) er blevet deaktiveret.
UpgradeAbortedSyncExeInUse | [Synkronisering service manager Brugergrænsefladen](active-directory-aadconnectsync-service-manager-ui.md) er åben på serveren.
UpgradeAbortedSyncOrConfigurationInProgress | Installationsguiden kører eller en synkronisering blev planlagt uden for planlæggeren.
**UpgradeNotSupported** |
UpgradeNotSupportedCustomizedSyncRules | Du har tilføjet dit eget brugerdefinerede regler af konfigurationen.
UpgradeNotSupportedDeviceWritebackEnabled | Du har aktiveret funktionen [enhed tilbageførsel](active-directory-aadconnect-feature-device-writeback.md) .
UpgradeNotSupportedGroupWritebackEnabled | Du har aktiveret funktionen [gruppe der ikke er gemt](active-directory-aadconnect-feature-preview.md#group-writeback) .
UpgradeNotSupportedInvalidPersistedState | Installationen er ikke en Express indstillinger eller en DirSync opgradering.
UpgradeNotSupportedMetaverseSizeExceeeded | Du har mere end 100.000 objekter i metaverse.
UpgradeNotSupportedMultiForestSetup | Du opretter forbindelse til mere end én skov. Hurtig konfiguration kun opretter forbindelse til et område.
UpgradeNotSupportedNonLocalDbInstall | Du bruger ikke en SQL Server Express LocalDB-database.
UpgradeNotSupportedNonMsolAccount | [AD Connector-konto](active-directory-aadconnect-accounts-permissions.md#active-directory-account) er ikke MSOL_ standardkontoen længere.
UpgradeNotSupportedStagingModeEnabled | Serveren er indstillet til at være i [arrangere tilstand](active-directory-aadconnectsync-operations.md#staging-mode).
UpgradeNotSupportedUserWritebackEnabled | Du har aktiveret funktionen [bruger tilbageførsel](active-directory-aadconnect-feature-preview.md#user-writeback) .

## <a name="next-steps"></a>Næste trin
Lær mere om at [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).
