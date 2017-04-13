<properties
   pageTitle="Azure AD-forbindelse synkronisering: Scheduler | Microsoft Azure"
   description="Dette emne beskrives funktionen indbyggede scheduler i Azure AD-forbindelse synkronisering."
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
   ms.date="08/04/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-scheduler"></a>Azure AD-forbindelse synkronisering: Scheduler
I dette emne beskrives indbyggede planlæggeren i synkronisering af Azure AD-forbindelse (kaldes også Synkroniser engine).

Denne funktion blev introduceret med build 1.1.105.0 (udgivet februar 2016).

## <a name="overview"></a>Oversigt
Azure AD-forbindelse Synkroniser synkroniserer ændringer, der sker i din lokale mappe ved hjælp af en scheduler. Der findes to scheduler processer, én til synkronisering af adgangskoder og en anden for objekt/attribut synkronisering og vedligeholdelsesopgaver. Dette emne beskrives disse.

I tidligere versioner planlæggeren for objekter og attributter er ekstern i forhold til synkronisering af programmet og Windows Opgavestyring eller en separat Windows-tjenesten blev brugt til at udløse processen til synkronisering. Planlæggeren er 1.1 versioner indbygget til Synkroniser motorer og Tillad, at nogle af de tilpasninger. Den nye standard synkronisering frekvens er 30 minutter.

Planlæggeren er ansvarlig for to opgaver:

- **Flytte synkronisering**. Processen, der skal importeres, synkronisere og eksportere ændringer.
- **Opgaver til vedligeholdelse**. Forny nøgler og certifikater til nulstilling af adgangskode og enhed registrering Service (DRS). Rydde gamle poster i logfilen for handlinger.

Planlæggeren selve altid er aktiv, men det kan være konfigureret til at kun køre en eller ingen af disse opgaver. Eksempelvis hvis du vil have din egen cyklus synkroniseringen, kan du deaktivere denne opgave i planlæggeren men stadig køre vedligeholdelse opgaven.

## <a name="scheduler-configuration"></a>Scheduler konfiguration
For at se dine aktuelle konfigurationsindstillinger skal du gå til PowerShell og kør `Get-ADSyncScheduler`. Det viser dig nogenlunde sådan ud:

![GetSyncScheduler](./media/active-directory-aadconnectsync-feature-scheduler/getsynccyclesettings.png)

Hvis du kan se **kommandoen Synkroniser eller cmdlet er ikke tilgængelig** , når du kører denne cmdlet, er af PowerShell-modulet ikke indlæst. Det kan ske, hvis du kører Azure AD-forbindelse til et domænenavn fra domænecontrolleren eller på en server med PowerShell begrænsning højere end standardindstillingerne. Hvis du får vist denne fejl, derefter køre `Import-Module ADSync` til rådighed cmdlet.

- **AllowedSyncCycleInterval**. De hyppigst Azure AD, så synkroniseringen skal udføres. Du kan ikke synkronisere oftere end dette og stadig være understøttet.
- **CurrentlyEffectiveSyncCycleInterval**. Tidsplan i øjeblikket i kraft. Det får samme værdi som CustomizedSyncInterval (Hvis angivet) Hvis det ikke er hyppigere end AllowedSyncInterval. Hvis du ændrer CustomizedSyncCycleInterval, vil dette træde i kraft efter næste synkronisering cyklus.
- **CustomizedSyncCycleInterval**. Hvis du vil scheduler til at køre på alle andre frekvens end standardlyden 30 minutter, skal konfigurere du denne indstilling. På billedet ovenfor planlæggeren er angivet til at køre hver time i stedet. Hvis du angiver det til en værdi lavere end AllowedSyncInterval, bruges de.
- **NextSyncCyclePolicyType**. Delta eller indledende. Angiver, om den næste kørsel skal kun proces delta ændringer, eller hvis næste kørsel bør gøre en fuld importere og synkronisere, som vil også genbehandle alle nye eller ændrede regler.
- **NextSyncCycleStartTimeInUTC**. Næste gang planlæggeren starter den næste synkronisering cyklus.
- **PurgeRunHistoryInterval**. Den tid, der skal beholdes logfilerne for handlingerne. Disse kan gennemses i synkronisering service manager. Standard er at holde disse oplysninger til 7 dage.
- **SyncCycleEnabled**. Angiver, om planlæggeren kører Importér, synkronisere og eksportprocesser som en del af driften.
- **MaintenanceEnabled**. Angiver, om processen vedligeholdelse er aktiveret. Det opdaterer certifikater/nøgler og Tøm handlinger log.
- **IsStagingModeEnabled**. Viser Hvis [arrangere tilstand](active-directory-aadconnectsync-operations.md#staging-mode) er aktiveret.

Du kan ændre nogle af disse indstillinger med `Set-ADSyncScheduler`. Du kan ændre følgende parametre:

- CustomizedSyncCycleInterval
- NextSyncCyclePolicyType
- PurgeRunHistoryInterval
- SyncCycleEnabled
- MaintenanceEnabled

Scheduler konfigurationen er gemt i Azure AD. Hvis du har en midlertidig server, vil eventuelle ændringer i den primære server også påvirke midlertidige serveren (med undtagelse af IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Syntaks:`Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d - dage, HH - timer, mm - minutter, ss - sekunder

Eksempel:`Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Ændrer scheduler til at køre hver 3 timer.

Eksempel:`Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
Ændrer automatisk skal køres dagligt.

## <a name="start-the-scheduler"></a>Starte planlæggeren
Planlæggeren kører som standard hver 30 minutter. I nogle tilfælde kan du vil køre en synkronisering cyklus mellem de planlagte skifter, eller du skal køre en anden type.

**Delta Synkroniser cyklus**  
En delta Synkroniser cyklus omfatter følgende trin:

- Delta Importér på alle forbindelser
- Delta Synkroniser på alle forbindelser
- Eksportere på alle forbindelser

Det skyldes, at du har et hastende ændre som skal være synkroniseret med det samme som er grunden til, skal du køre en cyklus manuelt. Hvis du vil køre manuelt en cyklus derefter fra PowerShell køres `Start-ADSyncSyncCycle -PolicyType Delta`.

**Fuld synkronisering cyklus**  
Hvis du har foretaget et af følgende ændringer i konfigurationen, skal du køre en komplet synkronisering cyklus (kaldes også Indledende):

- Tilføjet flere objekter eller attributter, der skal importeres fra en kildemappe
- Har foretaget ændringer til synkronisering af regler
- Ændret [filtrering](active-directory-aadconnectsync-configure-filtering.md) , så et forskelligt antal objekter, der skal medtages

Hvis du har skrevet en af disse ændringer, skal du køre en komplet synkronisering cyklus, så Synkroniser-program har mulighed for at omkonsolidere forbindelse mellemrum. En komplet synkronisering cyklus omfatter følgende trin:

- Fuldstændig Import på alle forbindelser
- Fuld Synkroniser på alle forbindelser
- Eksportere på alle forbindelser

Hvis du vil starte en komplet synkronisering cyklus, skal du køre `Start-ADSyncSyncCycle -PolicyType Initial` fra en PowerShell-prompt. Dette starter en komplet synkronisering cyklus.

## <a name="stop-the-scheduler"></a>Stoppe planlæggeren
Hvis planlæggeren i øjeblikket kører en synkronisering cyklus det være nødvendigt at stoppe den. For eksempel, hvis du starter installationsguiden og du får denne fejl:

![SyncCycleRunningError](./media/active-directory-aadconnectsync-feature-scheduler/synccyclerunningerror.png)

Når en synkronisering cyklus kører, kan du kan ikke foretage ændringer i konfigurationen. Du kan vente, indtil planlæggeren har udført processen, men du kan også stoppe den, så du kan foretage ændringerne med det samme. Stoppe den aktuelle cyklus er ikke skadelige og eventuelle ændringer, der stadig ikke behandlet behandles med næste kørsel.

1. Start ved at fortælle planlæggeren stoppe dens aktuelle cyklus med PowerShell-cmdlet `Stop-ADSyncSyncCycle`.
2. Stoppe planlæggeren stopper ikke den aktuelle forbindelse fra den aktuelle opgave. For at tvinge forbindelsen for at stoppe skal du udføre følgende handlinger: ![StopAConnector](./media/active-directory-aadconnectsync-feature-scheduler/stopaconnector.png)
    - Starte **Tjenesten Sychronization** fra startmenuen. Gå til **forbindelser**, fremhæve forbindelsen med tilstanden **kører** , og vælg **Stop** fra handlingerne.

Planlæggeren stadig er aktiv og starter igen på næste mulighed.

## <a name="custom-scheduler"></a>Brugerdefineret scheduler
Cmdlet'erne beskrevet i dette afsnit er kun tilgængelig i build [1.1.130.0](active-directory-aadconnect-version-history.md#111300) eller nyere.

Hvis den indbyggede scheduler ikke opfylder dine behov, kan du planlægge forbindelserne ved hjælp af PowerShell.

### <a name="invoke-adsyncrunprofile"></a>Kalde ADSyncRunProfile
Du kan starte en profil for en forbindelse på denne måde:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

Navne, der skal bruges til [Connector navne](active-directory-aadconnectsync-service-manager-ui-connectors.md) og [køre profil](active-directory-aadconnectsync-service-manager-ui-connectors.md#configure-run-profiles) kan findes i [Brugergrænsefladen i synkronisering Service Manager](active-directory-aadconnectsync-service-manager-ui.md).

![Kalde køre profil](./media/active-directory-aadconnectsync-feature-scheduler/invokerunprofile.png)  

Den `Invoke-ADSyncRunProfile` cmdlet er synkron, det vil sige det returnerer ikke kontrolelementet indtil forbindelsen er udført handlingen, enten korrekt eller med en fejl.

Når du planlægger dine forbindelser, anbefales det at planlægge dem i følgende rækkefølge:

1. (Fuld/Delta) Importere fra lokale kataloger, som Active Directory
2. (Fuld/Delta) Importere fra Azure AD
3. (Fuld/Delta) Synkronisering fra lokale kataloger, som Active Directory
4. (Fuld/Delta) Synkronisering fra Azure AD
5. Eksportere til Azure AD
6. Eksportere til lokale kataloger, som Active Directory

Hvis du ser på indbyggede planlæggeren, er dette den rækkefølge, forbindelserne køres.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
Du kan også overvåge Synkroniser program at se, om det er optaget eller inaktiv. Denne cmdlet returnerer et tomt resultat, hvis Synkroniser program er inaktiv og ikke kører en forbindelse. Hvis der kører en forbindelse, returnerer navnet på forbindelsen.

```
Get-ADSyncConnectorRunStatus
```

![Forbindelse køre Status](./media/active-directory-aadconnectsync-feature-scheduler/getconnectorrunstatus.png)  
På billedet ovenfor er den første linje fra en tilstand, hvor Synkroniser program er inaktiv. Den anden linje fra når Azure AD-forbindelse kører.

## <a name="scheduler-and-installation-wizard"></a>Guiden Scheduler og installation
Hvis du starter installationsguiden, derefter suspenderet planlæggeren midlertidigt. Dette skyldes, at det antages, du vil foretage ændringer i konfigurationen og disse kan ikke anvendes, hvis Synkroniser program kører aktivt. Derfor må ikke være installationsguiden Åbn siden den stopper synkronisering-program i at udføre en hvilken som helst synkronisering handlinger.

## <a name="next-steps"></a>Næste trin
Lær mere om at [synkronisere Azure AD-forbindelse](active-directory-aadconnectsync-whatis.md) konfigurationen.

Lær mere om at [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).
