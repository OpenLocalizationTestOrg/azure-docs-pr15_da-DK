<properties
   pageTitle="Azure AD-forbindelse synkronisering: driftsopgaver og overvejelser i forbindelse med | Microsoft Azure"
   description="Dette emne beskrives driftsopgaver til Azure AD-forbindelse synkronisering og hvordan du forbereder til operativsystem komponenten."
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
   ms.date="09/01/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-operational-tasks-and-consideration"></a>Azure AD-forbindelse synkronisering: driftsopgaver og overvejelser
Formålet med dette emne er til at beskrive driftsopgaver til synkronisering af Azure AD-forbindelse.

## <a name="staging-mode"></a>Midlertidige tilstand
Midlertidige tilstand kan bruges til flere scenarier, herunder:

-   Høj tilgængelighed.
-   Teste og installere nye ændringer i konfigurationen.
-   Introducere en ny server og afvikler gammelt.

Du kan foretage ændringer af konfigurationen og få vist ændringerne, inden du foretager serveren aktive med en server i midlertidige tilstand. Det gør det også muligt at køre fuldstændig import og fuld synkronisering til at kontrollere, at alle ændringer er forventet, før du foretager disse ændringer i dit produktionsmiljø.

Under installationen, kan du vælge serveren, der skal være i **arrangere tilstand**. Denne handling gør serveren aktiv for import og synkronisering, men den kan ikke køre en hvilken som helst Eksporter. En server i midlertidige tilstand kører ikke synkronisering af adgangskoder eller adgangskode der ikke er gemt, selvom du har valgt disse funktioner under installationen. Når du deaktiverer midlertidige tilstand, serveren starter eksport, gør det muligt for synkronisering af adgangskoder, og gør det muligt for adgangskode tilbageførsel.

Du kan stadig gennemtvinge en Eksportér ved hjælp af synkronisering service manager.

En server i midlertidige tilstand fortsætter med at modtage ændringer fra Active Directory og Azure AD. Det har altid en kopi af de seneste ændringer og kan meget hurtigt tage over ansvarsområder for en anden server. Hvis du foretager ændringer i konfigurationen til din primære server, er det dit ansvar at foretage de samme ændringer til serveren i arrangere tilstand.

For dem, for du med kendskab til ældre Synkroniser teknologier er den midlertidige tilstand anderledes, da serveren, der har sin egen SQL-database. Denne arkitektur kan midlertidige tilstand serveren for at være placeret i en anden datacenter.

### <a name="verify-the-configuration-of-a-server"></a>Verificer konfigurationen af en server
Hvis du vil anvende denne metode, skal du følge disse trin:

1. [Forberede](#prepare)
2. [Importere og synkronisere](#import-and-synchronize)
3. [Bekræfte](#verify)
4. [Skift active server](#switch-active-server)

#### <a name="prepare"></a>Forberede

1. Installer Azure AD-forbindelse, Vælg **arrangere tilstand**, og fjern markeringen af **starte synkronisering** på den sidste side i installationsguiden. Denne tilstand kan du køre programmet Synkroniser manuelt.
![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/readytoconfigure.png)
2. Log fra/Log på, og vælg **Tjenesten Brugerprofilsynkronisering**fra startmenuen.

#### <a name="import-and-synchronize"></a>Importere og synkronisere

1. Vælg **forbindelser**, og vælg den første forbindelse med typen **Active Directory Domain Services**. Klik på **Kør**skal du vælge **fuld import**og **OK**. Udføre disse trin for alle forbindelser af denne type.
2. Vælg forbindelsen med type **Azure Active Directory (Microsoft)**. Klik på **Kør**skal du vælge **fuld import**og **OK**.
3. Sørg for, at fanen forbindelser stadig er markeret. Klik på **Kør**for hver forbindelse med type **Active Directory Domain Services**, skal du vælge **Delta synkronisering**og **OK**.
4. Vælg forbindelsen med type **Azure Active Directory (Microsoft)**. Klik på **Kør**skal du vælge **Delta synkronisering**og **OK**.

Du har nu midlertidigt gemt eksport ændres til Azure AD og lokale AD (Hvis du bruger Exchange-hybridinstallation). De næste trin kan du undersøge, hvad er ved at ændre, før du starter faktisk Eksporter til mapperne.

#### <a name="verify"></a>Bekræfte

1. Starte en cmd prompt, og gå til`%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Køre:`csexport "Name of Connector" %temp%\export.xml /f:x`  
Navnet på forbindelsen kan findes i tjenesten Brugerprofilsynkronisering. Den har et navn, der svarer til "contoso.com – AAD" til Azure AD.
3. Køre:`CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. Du har en fil i % temp % med navnet export.csv, der kan undersøges i Microsoft Excel. Denne fil indeholder alle ændringer, der er ved at eksporteres.
5. Foretage ændringer i dataene og konfiguration og køre følgende igen (Importér og Synkroniser og Bekræft), indtil de ændringer, der er ved at eksporteres forventes.

**Forstå filen export.csv**

De fleste af filen er ingen forklaring. Nogle forkortelser at forstå indholdet:

- OMODT – ændring objekttype. Angiver, hvis handlingen på en objektniveau er en Tilføj, Opdater eller Slet.
- AMODT-attribut ændring Type. Angiver, hvis handlingen på en attributniveau er en Tilføj, Opdater eller Slet.

Hvis attributten værdi er med flere værdier, vises ikke alle ændringer. Kun antallet af værdier tilføjes eller fjernes er synlig.

#### <a name="switch-active-server"></a>Skift active server

1. Slå fra serveren (FIM-DirSync/Azure Active Directory-synkronisering), så det ikke er at eksportere til Azure AD eller angive det i arrangere tilstand (Azure AD-forbindelse) på den aktuelt aktive server.
2. Køre installationsguiden på serveren i **arrangere tilstand** , og Deaktiver **arrangere tilstand**.
![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/additionaltasks.png)

## <a name="disaster-recovery"></a>Nedbrud
En del af implementering designet er at planlægge, hvad du skal gøre, hvis der er nedbrud, hvor du har glemt synkronisering af serveren. Der er forskellige modeller til brug og hvilket der skal bruges, afhænger af flere faktorer, herunder:

-   Hvad er din tolerance for ikke at være kan foretage ændringer i objekter Azure AD under nedetiden?
-   Hvis du bruger synkronisering af adgangskoder, brugerne accepterer, at de skal bruge den gamle adgangskode i Azure AD, i tilfælde af, at de ændrer den lokale?
-   Har du en afhængighed på realtid handlinger, som adgangskode tilbageførsel?

Afhængigt af svarene på disse spørgsmål og organisationens politik implementeres en af følgende strategier:

-   Genopbygge, når det er nødvendigt.
-   Har en ekstra standby server, kaldet **arrangere tilstand**.
-   Brug virtuelle computere.

Hvis du ikke bruger indbyggede SQL Express-databasen, skal du også se afsnittet [SQL høj tilgængelighed](#sql-high-availability) .

### <a name="rebuild-when-needed"></a>Genopbygge, når det er nødvendigt
En interessant strategi er at planlægge en server Genopbyg, når det er nødvendigt. Normalt installerer Synkroniser program og gør den første import og synkronisere kan fuldføres inden for et par timer. Hvis der ikke en ekstra server, er det muligt at bruge et domænenavn fra domænecontrolleren midlertidigt for at hoste Synkroniser program.

Synkroniser program serveren gemmer ikke enhver stat, om objekterne, så databasen kan genopbygges fra dataene i Active Directory og Azure AD. Attributten **sourceAnchor** bruges til at deltage i objekterne fra lokale miljø og skyen. Hvis du genopbygger serveren med eksisterende objekter i det lokale miljø og skyen, derefter Synkroniser program svarer til disse objekter sammen igen på geninstallation. De ting, du har brug for at dokumentere og gemme er konfigurationsændringer til serveren, som filtrering og synkronisering regler. Disse brugerdefinerede konfigurationer skal være igen, før du går i gang med at synkronisere.

### <a name="have-a-spare-standby-server---staging-mode"></a>Har en ekstra standby server - arrangere tilstand
Hvis du har en mere kompleks miljø, anbefales derefter har en eller flere standby servere. Under installationen, kan du aktivere en server, der skal være i **arrangere tilstand**.

Yderligere oplysninger finder du [arrangere tilstand](#staging-mode).

### <a name="use-virtual-machines"></a>Bruge virtuelle maskiner
En fælles og understøttede metode er at køre Synkroniser program i et virtuelt. I tilfælde af værten har et problem, kan billedet med synkronisering program serveren overføres til en anden server.

### <a name="sql-high-availability"></a>SQL høj tilgængelighed
Hvis du ikke bruger den SQL Server Express, som følger med Azure AD-forbindelse, bør derefter høj tilgængelighed til SQL Server også overvejes. Kun høj tilgængelighed løsningen understøttes er SQL-klynge. Ikke-understøttede løsninger indeholde spejling og altid på.

## <a name="next-steps"></a>Næste trin

**Oversigt oversigtsemner**  

- [Azure AD-forbindelse synkronisering: forstå og tilpasse synkronisering](active-directory-aadconnectsync-whatis.md)  
- [Integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md)  
