<properties
   pageTitle="PowerShell forbindelse | Microsoft Azure"
   description="I denne artikel beskrives, hvordan du konfigurerer Microsofts Windows PowerShell forbindelse."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/30/2016"
   ms.author="billmath"/>

# <a name="windows-powershell-connector-technical-reference"></a>Teknisk vejledning til Windows PowerShell-forbindelse
I denne artikel beskrives Windows PowerShell forbindelsen. I artiklen gælder for følgende produkter:

- Microsoft identitet Manager 2016 (MIM2016)
- Forefront identitet Manager 2010 R2 (FIM2010R2)
    -   Skal bruge programrettelse 4.1.3671.0 eller nyere [KB3092178](https://support.microsoft.com/kb/3092178).

Forbindelsen er for MIM2016 og FIM2010R2 skal hentes fra [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=717495).

## <a name="overview-of-the-powershell-connector"></a>Oversigt over den PowerShell-forbindelse
PowerShell forbindelsen gør det muligt at integrere tjenesten til synkronisering med eksterne systemer, der tilbyder Windows PowerShell baseret API'er. Forbindelsen indeholder en bro mellem funktionerne i opkald-baserede extensible connectivity management agent 2 (ECMA2) framework og Windows PowerShell. Du kan finde flere oplysninger om ECMA framework [Extensible Connectivity 2.2 Management Agent Reference](https://msdn.microsoft.com/library/windows/desktop/hh859557.aspx).

### <a name="prerequisites"></a>Forudsætninger
Før du bruger forbindelsen, skal du kontrollere følgende på serveren synkronisering være:

- Microsoft .NET 4.5.2 Framework eller nyere
- Windows PowerShell 2.0, 3.0 eller 4.0

Udførelse af politikken på tjenesten Brugerprofilsynkronisering serveren skal være konfigureret til at tillade forbindelsen til at køre Windows PowerShell-scripts. Medmindre de scripts connector kører er blevet signeret digitalt, konfigurere politikken udførelse af ved at køre denne kommando:  
`Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

## <a name="create-a-new-connector"></a>Oprette en ny forbindelse
Hvis du vil oprette en forbindelse til Windows PowerShell i tjenesten til synkronisering, skal du angive en serie af Windows PowerShell-scripts, som udføres de trin, der er blevet anmodet af tjenesten til synkronisering. De scripts, skal du implementere varierer afhængigt af den datakilde, du opretter forbindelse til og den funktionalitet, du ønsker. Dette afsnit beskrives hver af de scripts, som kan implementeres, og når de er påkrævet.

Windows PowerShell-forbindelsen er udviklet til at gemme hver af scriptene i tjenesten Brugerprofilsynkronisering databasen. Selvom det er muligt at køre scripts, der er gemt i filsystemet, er det nemmest at indsætte brødteksten i hvert script direkte i forbindelsens konfigurationen.

Vælg **Administration Agent** og **oprette**for at oprette en PowerShell-forbindelse, i **Tjenesten Brugerprofilsynkronisering** . Markér forbindelsen **PowerShell (Microsoft)** .

![Oprette forbindelse](./media/active-directory-aadconnectsync-connector-powershell/createconnector.png)

### <a name="connectivity"></a>Forbindelse
Angiv konfiguration af parametre til at oprette forbindelse til et eksternt system. Disse værdier er sikkert gemt af tjenesten synkronisering og gøres tilgængelige for dine Windows PowerShell-scripts, når forbindelsen er kører.

![Forbindelse](./media/active-directory-aadconnectsync-connector-powershell/connectivity.png)

Du kan konfigurere følgende Connectivity parametre:

**Forbindelse**

Parameter | Standardværdi | Formål
--- | --- | ---
Server | <Blank> | Servernavn, forbindelsen skal oprettes forbindelse til.
Domæne | <Blank> | Domænet for legitimationsoplysninger til at gemme til brug, når forbindelsen er kører.
Bruger | <Blank> | Brugernavnet for legitimationsoplysninger til at gemme til brug, når forbindelsen er kører.
Adgangskode | <Blank> | Adgangskoden til legitimationsoplysninger til at gemme til brug, når forbindelsen er kører.
Repræsentere Connector-konto | FALSK | Hvis sand, kører tjenesten Brugerprofilsynkronisering Windows PowerShell-scripts i forbindelse med de angivne legitimationsoplysninger. Når det er muligt, anbefales det, at parameteren **$Credentials** der overføres til hvert script bruges i stedet for repræsentation. Du kan finde flere oplysninger om yderligere tilladelser, der er påkrævet for at bruge denne indstilling, [Yderligere konfiguration for repræsentation](#additional-configuration-for-impersonation).
Indlæse brugerprofil, når den repræsenterer | FALSK | Får Windows for at indlæse brugerprofil forbindelsens legitimationsoplysninger under repræsentation. Hvis repræsenteret brugeren har en global profil, indlæses connector ikke den globale profil. Du kan finde flere oplysninger om yderligere tilladelser, der er påkrævet for at bruge denne parameter, [Yderligere konfiguration for repræsentation](#additional-configuration-for-impersonation).
Logontype, når den repræsenterer | Ingen | Logontype under repræsentation. Du kan finde flere oplysninger, der refererer til [dwLogonType] [ dw] dokumentation.
Kun signerede Scripts | FALSK | Hvis sand, validerer Windows PowerShell-forbindelsen, at hvert script har en gyldig digital signatur. Hvis falsk, kan du sikre, at tjenesten Brugerprofilsynkronisering serverens Windows PowerShell udførelse af politik er RemoteSigned eller ubegrænset.

**Almindelige modul**  
Forbindelsen kan du gemme en delt Windows PowerShell-modulet i konfigurationen. Når forbindelsen kører et script, ud af Windows PowerShell-modulet til filsystemet, så den kan importeres ved hvert script.

Importere, eksportere og synkronisering af adgangskoder scripts udtrækkes modulet almindelige til forbindelsens MAData mappe. Generelle modul, der udtrækkes til skemaet, validering, hierarki og Partition registrering scripts, til mappen % TEMP %. I begge tilfælde hedder udpakkede almindelige modul scriptet i overensstemmelse med indstillingen almindelige modul Script-navn.

For at indlæse et modul, der hedder FIMPowerShellConnectorModule.psm1 fra mappen MAData skal du bruge følgende sætning:`Import-Module (Join-Path -Path [Microsoft.MetadirectoryServices.MAUtils]::MAFolder -ChildPath "FIMPowerShellConnectorModule.psm1")`

For at indlæse et modul, der hedder FIMPowerShellConnectorModule.psm1 fra mappen % TEMP %, skal du bruge følgende sætning:`Import-Module (Join-Path -Path $env:TEMP -ChildPath "FIMPowerShellConnectorModule.psm1")`

**Validere parameteren**  
Valideringsscriptet er en valgfri Windows PowerShell-script, som kan bruges til at sikre, at forbindelsen konfiguration af parametre leveret af administratoren er gyldige. Validering af server, forbindelse legitimationsoplysninger og connectivity parametre er almindelige brug af valideringsscriptet. Valideringsscriptet kaldes efter følgende faner og dialogbokse er ændret:

- Forbindelse
- Globale parametre
- Partitionskonfiguration af

Valideringsscriptet modtager følgende parametre fra forbindelsen:

Navn | Datatype | Beskrivelse
--- | --- | ---
ConfigParameterPage | [ConfigParameterPage][cpp] | Under fanen konfiguration eller dialogboksen, som udløste validering anmodningen.
ConfigParameters | [KeyedCollection] [ keyk] [streng, [ConfigParameter][cp]] | Tabel med konfiguration af parametre for forbindelsen.
Legitimationsoplysninger | [PSCredential][pscred] | Indeholder en hvilken som helst legitimationsoplysninger, der er angivet af administratoren under fanen forbindelse.

Valideringsscriptet skal returnere en enkelt ParameterValidationResult objekt til rørledningen.

**Skema registrering**  
Scriptet skema registrering er obligatorisk. Dette script returnerer objekttyper, attributter og attributten begrænsninger, der bruger tjenesten til synkronisering, når du konfigurerer attributregler for mailflow. Skema registrering scriptet køres under oprettelse af forbindelse og udfylder forbindelsens skemaet. Det bruges også af handlingen Opdater skema i synkronisering Service Manager.

Skema registrering scriptet modtager følgende parametre fra forbindelsen:

Navn | Datatype | Beskrivelse
--- | --- | ---
ConfigParameters | [KeyedCollection] [ keyk] [streng, [ConfigParameter][cp]] | Tabel med konfiguration af parametre for forbindelsen.
Legitimationsoplysninger | [PSCredential][pscred] | Indeholder en hvilken som helst legitimationsoplysninger, der er angivet af administratoren under fanen forbindelse.

Scriptet skal returnere et enkelt [skema] [ schema] objekt til rørledningen. Objektet skema består af [SchemaType] [ schemaT] objekter, der repræsenterer objekttyper (for eksempel: brugere og grupper). Objektet SchemaType indeholder en samling af [SchemaAttribute] [ schemaA] objekter, der repræsenterer attributterne (for eksempel: Fornavn, Efternavn og postadresse) af typen.

**Yderligere parametre**  
Ud over de almindelige konfigurationsindstillinger, kan du angive yderligere brugerdefinerede konfigurationsindstillinger, der er specifikke for forekomsten af forbindelsen. Disse parametre kan angives på forbindelsen, partition, eller Kør trin udjævner og adgang til dem fra de relevante Windows PowerShell-script. Brugerdefinerede konfigurationsindstillinger kan være gemt i tjenesten Brugerprofilsynkronisering databasen som almindelig tekst eller de kan være krypteret. Tjenesten Brugerprofilsynkronisering krypterer automatisk og dekrypterer secure konfigurationsindstillinger, når det er påkrævet.

Hvis du vil angive brugerdefinerede konfigurationsindstillinger, adskilt med navnet på hver parameter med et komma (,).

For at få adgang til brugerdefineret konfigurationsindstillinger fra et script, skal du suffiks navn med et understregningstegn ( \_ ) og omfanget af parameteren (Global, Partition eller RunStep). For eksempel bruge denne kodestykke for at få adgang til parameteren globale filnavn:`$ConfigurationParameters["FileName_Global"].Value`

### <a name="capabilities"></a>Funktioner
Fanen funktioner i administration Agent Designer definerer opførsel og funktionalitet for forbindelsen. Med indstillingerne under denne fane kan ikke ændres, når forbindelsen er oprettet. Denne tabel viser indstillingerne for egenskab.

![Funktioner](./media/active-directory-aadconnectsync-connector-powershell/capabilities.png)

Egenskab | Beskrivelse |
--- | --- |
[Entydigt Navnetypografi][dnstyle] | Angiver, hvis forbindelsen understøtter entydige navne, og så hvad typografi.
[Eksportere Type][exportT] | Bestemmer typer objekter, der vises for scriptet Eksportér. <li>AttributeReplace – indeholder det samlede sæt af værdier for attributten med flere værdier, når attributten ændres.</li><li>AttributeUpdate – indeholder kun deltaer til en opslagskolonne med flere valgmuligheder attribut, når attributten ændres.</li><li>MultivaluedReferenceAttributeUpdate - indeholder et komplet sæt af værdier for ikke-reference med flere valgmuligheder attributter og kun deltaer for med flere valgmuligheder referenceattributter.</li><li>ObjectReplace – omfatter alle attributter for et objekt, når nogen ændringer i attributter</li>
[Datanormalisering][DataNorm] | Giver besked om tjenesten til synkronisering skal standardiseres anker attributter, før de kan bruges til scripts.
[Objekt bekræftelse][oconf] | Konfigurerer ventende Importér funktionsmåden i tjenesten til synkronisering. <li>Normal – standardfunktionsmåde, der forventer, at alle eksporterede ændringer skal bekræftes via import</li><li>NoDeleteConfirmation – er når et objekt slettes, der ingen ventende Importér, der er oprettet.</li><li>NoAddAndDeleteConfirmation – er når et objekt, der oprettes eller slettes, der ingen ventende Importér, der er oprettet.</li>
Bruge DN som anker | Hvis typografien entydigt navn er indstillet til LDAP, er attributten anker for forbindelse afstand også det entydige navn.
Samtidige handlinger for flere forbindelser | Hvis markeret, kan køre flere Windows PowerShell-forbindelser samtidigt.
Partitioner | Hvis markeret, understøtter forbindelsen flere partitioner og partition registrering.
Hierarki | Hvis markeret, understøtter forbindelsen en LDAP-typografi hierarkiske struktur.
Aktivere Import | Hvis markeret, importerer forbindelsen data via Importér scripts.
Aktivere Deltaimport | Hvis markeret, kan forbindelsen anmode om deltaer fra Importér scripts.
Aktivere Eksportér | Hvis markeret, eksporterer forbindelsen data via Eksportér scripts.
Aktivere fuld Eksportér | Hvis markeret, understøtter eksport scripts eksportere hele forbindelsen afstanden. Hvis du vil bruge denne indstilling, skal skal aktivere eksportere også være markeret.
Ingen referenceværdier i første Eksportér gennemløb | Hvis markeret, eksporteres referenceattributter i et andet Eksportér gennemløb.
Aktivere objektet Omdøb | Hvis markeret, kan ændres entydige navne.
Slet-Tilføj erstatte | Hvis markeret, Slet-Tilføj handlinger eksporteres som en enkelt erstatning.
Aktivér adgangskode handlinger | Hvis markeret, understøttes adgangskode synkronisering scripts.
Aktivere Eksportér adgangskode på første gang | Hvis markeret, eksporteres adgangskoder, der er angivet under klargøring, når der oprettes på objektet.

### <a name="global-parameters"></a>Globale parametre
Fanen globale parametre i administration Agent Designer kan du konfigurere Windows PowerShell-scripts, som køres af forbindelsen. Du kan også konfigurere globale værdier for brugerdefinerede konfigurationsindstillinger, der er defineret under fanen forbindelse.

**Partition registrering**  
En partition er et separat navneområde i ét delt skema. Hver domæne er for eksempel en partition i et område i Active Directory. En partition er logisk gruppering ved import og eksport. Importer og Eksporter har partition som en kontekst og alle handlinger, der sker der i denne kontekst. Partitioner skal repræsentere et hierarki i LDAP. Det entydige navn på en partition bruges i import til at bekræfte, at alle returnerede objekter er omfattet af en partition. Det entydige partitionsnavn bruges også under klargøringen fra metaverse connector-plads til at bestemme den partition, et objekt skal være knyttet til under Eksportér.

Partition registrering scriptet modtager følgende parametre fra forbindelsen:

Navn | Datatype | Beskrivelse
--- | --- | ---
ConfigParameters  | [KeyedCollection][keyk][streng, [ConfigParameter][cp]] | Tabel med konfiguration af parametre for forbindelsen.
Legitimationsoplysninger | [PSCredential][pscred] | Indeholder en hvilken som helst legitimationsoplysninger, der er angivet af administratoren under fanen forbindelse.

Scriptet skal returnere en enten en enkelt [Partition] [ part] objekt eller en liste over [T] Partition objekter til rørledningen.

**Hierarki registrering**  
Hierarki registrering scriptet bruges kun, når muligheden for entydigt Navnetypografi er LDAP. Scriptet bruges til at kan bruges til at gennemse og vælge et sæt af objektbeholdere, der betragtes som ind eller ud på området for import og eksport. Scriptet kan kun give en liste over noder, der er direkte underordnet for rodnoden leveret til scriptet.

Hierarki registrering scriptet modtager følgende parametre fra forbindelsen:

Navn | Datatype | Beskrivelse
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][streng, [ConfigParameter][cp]] | Tabel med konfiguration af parametre for forbindelsen.
Legitimationsoplysninger | [PSCredential][pscred] | Indeholder en hvilken som helst legitimationsoplysninger, der er angivet af administratoren under fanen forbindelse.
ParentNode | [HierarchyNode][hn] | Noden roden i hierarkiet under som scriptet skal returnere direkte underordnet.

Scriptet skal returnere en enten en enkelt underordnede HierarchyNode objekt eller en liste over [T] af underordnede HierarchyNode objekter til rørledningen.

#### <a name="import"></a>Importér
Forbindelser, der understøtter importhandlinger skal implementere tre scripts.

**Starte importen**  
Start import scriptet køres i starten af en køre Tekstimport, trin. I dette trin kan du oprette forbindelse til kildesystemet og gøre forberedende trin før du importerer data fra det tilsluttede system.

Start import scriptet modtager følgende parametre fra forbindelsen:

Navn | Datatype | Beskrivelse
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][streng, [ConfigParameter][cp]] | Tabel med konfiguration af parametre for forbindelsen.
Legitimationsoplysninger | [PSCredential][pscred] | Indeholder en hvilken som helst legitimationsoplysninger, der er angivet af administratoren under fanen forbindelse.
OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Oplyser scriptet om typen Importér køre (delta eller fuld) partition, hierarki, vandmærke og forventede sidestørrelse.
Datatyper | [Skema][schema] | Skema for forbindelse afstand, der er importeret.

Scriptet skal returnere en enkelt [OpenImportConnectionResults] [ oicres] objekt til rørledning, f.eks.:`Write-Output (New-Object Microsoft.MetadirectoryServices.OpenImportConnectionResults)`

**Importdata**  
Importér data scriptet kaldes via forbindelsen, indtil scriptet angiver, at der er ingen flere data til at importere. Windows PowerShell forbindelsen har en sidestørrelse 9.999 objekter. Hvis dit script returnerer mere end 9.999 objekter til import, skal du understøtter sideinddeling. De forbindelse viser en brugerdefineret egenskab, du kan bruge til et lager til et vandmærke så scriptet Importér data, hver gang der kaldes, dit script genoptages importere objekter, hvor den blev afbrudt.

Importér data scriptet modtager følgende parametre fra forbindelsen:

Navn | Datatype | Beskrivelse
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][streng, [ConfigParameter][cp]] | Tabel med konfiguration af parametre for forbindelsen.
Legitimationsoplysninger | [PSCredential][pscred] | Indeholder en hvilken som helst legitimationsoplysninger, der er angivet af administratoren under fanen forbindelse.
GetImportEntriesRunStep | [ImportRunStep][irs] | Indeholder vandmærket (CustomData), der kan bruges under gruppe importerer og delta importerer.
OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Oplyser scriptet om typen Importér køre (delta eller fuld) partition, hierarki, vandmærke og forventede sidestørrelse.
Datatyper | [Skema][schema] | Skema for forbindelse afstand, der er importeret.

Importér data scriptet skal skrive en liste [[CSEntryChange][csec]] objekt til rørledningen. Denne samling består af CSEntryChange attributter, der repræsenterer de enkelte objekter, der skal importeres. Under en fuldstændig Import har denne af websteder et komplet sæt CSEntryChange objekter, der har alle attributter for hvert objekt. Under en har importeret Delta indeholde objektet CSEntryChange enten attributten niveau deltaer for hvert objekt til at importere eller en komplet repræsentation af de objekter, der er blevet ændret (Erstat tilstand).

**Afslut Import**  
Ved undersøgelsens importen køre, køres scriptet slutningen importere. Dette script skal udføre en hvilken som helst oprydningsopgaver nødvendige (for eksempel Luk forbindelser til systemer) og svare på en fejl.

Afslut Importér scriptet modtager følgende parametre fra forbindelsen:

Navn | Datatype | Beskrivelse
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][streng, [ConfigParameter][cp]] | Tabel med konfiguration af parametre for forbindelsen.
Legitimationsoplysninger | [PSCredential][pscred] | Indeholder en hvilken som helst legitimationsoplysninger, der er angivet af administratoren under fanen forbindelse.
OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Oplyser scriptet om typen Importér køre (delta eller fuld) partition, hierarki, vandmærke og forventede sidestørrelse.
CloseImportConnectionRunStep | [CloseImportConnectionRunStep][cecrs] | Oplyser scriptet om årsagen til importen blev afsluttet.

Scriptet skal returnere en enkelt [CloseImportConnectionResults] [ cicres] objekt til rørledning, f.eks.:`Write-Output (New-Object Microsoft.MetadirectoryServices.CloseImportConnectionResults)`

#### <a name="export"></a>Eksportere
Identisk med Importér arkitekturen i forbindelsen, forbindelser, der understøtter eksport skal implementere tre scripts.

**Starte Eksportér**  
Start Eksportér scriptet køres i starten af et Eksportér køre trin. I dette trin kan du oprette forbindelse til kildesystemet og forestå eventuelle forberedende trin før det eksporteres data til forbundne systemet.

Start Eksportér scriptet modtager følgende parametre fra forbindelsen:

Navn | Datatype | Beskrivelse
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][streng, [ConfigParameter][cp]] | Tabel med konfiguration af parametre for forbindelsen.
Legitimationsoplysninger | [PSCredential][pscred] | Indeholder en hvilken som helst legitimationsoplysninger, der er angivet af administratoren under fanen forbindelse.
OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Oplyser scriptet om typen Eksportér køre (delta eller fuld) partition, hierarki og forventede sidestørrelse.
Datatyper | [Skema][schema] | Skema for forbindelse afstand, der er eksporteret.

Scriptet returnerer skal ikke noget output til rørledningen.

**Eksportere Data**  
Tjenesten Brugerprofilsynkronisering kalder Eksporter Data scriptet så mange gange, som det er nødvendigt at behandle alle ventende Eksporter. Hvis forbindelsen space er flere ventende Eksporter end forbindelsens sidestørrelse, kan scriptet Eksportér data kaldes flere gange og muligvis flere gange for det samme objekt.

Eksportér data scriptet modtager følgende parametre fra forbindelsen:

Navn | Datatype | Beskrivelse
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][streng, [ConfigParameter][cp]] | Tabel med konfiguration af parametre for forbindelsen.
Legitimationsoplysninger | [PSCredential][pscred] | Indeholder en hvilken som helst legitimationsoplysninger, der er angivet af administratoren under fanen forbindelse.
CSEntries | IList[CSEntryChange][csec] | Liste over alle de forbindelse mellemrum objekter med afventer eksport skal behandles under dette gennemløb.
OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Oplyser scriptet om typen Eksportér køre (delta eller fuld) partition, hierarki og forventede sidestørrelse.
Datatyper | [Skema][schema] | Skema for forbindelse afstand, der er eksporteret.

Eksportér data scriptet skal returnere en [PutExportEntriesResults] [ peeres] objekt til rørledningen. Dette objekt behøver ikke at medtage resultatet oplysninger for hver forbindelse, der er eksporteret, medmindre der opstår en fejl eller en ændring af attributten anker. For eksempel, til at returnere en PutExportEntriesResults objekt til rørledningen:`Write-Output (New-Object Microsoft.MetadirectoryServices.PutExportEntriesResults)`

**Afslut Eksportér**  
Ved undersøgelsens Eksportér kører, End eksportere scriptet til at køre. Dette script skal udføre en hvilken som helst oprydningsopgaver nødvendige (for eksempel Luk forbindelser til systemer) og svare på en fejl.

Afslut Eksportér scriptet modtager følgende parametre fra forbindelsen:

Navn | Datatype | Beskrivelse
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][streng, [ConfigParameter][cp]] | Tabel med konfiguration af parametre for forbindelsen.
Legitimationsoplysninger | [PSCredential][pscred] | Indeholder en hvilken som helst legitimationsoplysninger, der er angivet af administratoren under fanen forbindelse.
OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Oplyser scriptet om typen Eksportér køre (delta eller fuld) partition, hierarki og forventede sidestørrelse.
CloseExportConnectionRunStep | [CloseExportConnectionRunStep][cecrs] | Oplyser scriptet om årsagen til eksporten blev afsluttet.

Scriptet returnerer skal ikke noget output til rørledningen.

#### <a name="password-synchronization"></a>Synkronisering af adgangskoder
Windows PowerShell forbindelser kan bruges som destination for ændringer/nulstilling af adgangskoder.

Adgangskode scriptet modtager følgende parametre fra forbindelsen:

Navn | Datatype | Beskrivelse
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][streng, [ConfigParameter][cp]] | Tabel med konfiguration af parametre for forbindelsen.
Legitimationsoplysninger | [PSCredential][pscred] | Indeholder en hvilken som helst legitimationsoplysninger, der er angivet af administratoren under fanen forbindelse.
Partition | [Partition][part] | Directory partition, som CSEntry er i.
CSEntry | [CSEntry][cse] | Forbindelse plads-posten for det objekt, der er modtaget en ændring af adgangskode eller Nulstil.
Handlingstype | Streng | Angiver, hvis handlingen er en Nulstil (**SetPassword**) eller en ændring (**ændring af adgangskode**).
PasswordOptions | [PasswordOptions][pwdopt] | Flag, der angiver den tilsigtede adgangskode nulstille funktionsmåde. Denne parameter er kun tilgængelig, hvis handlingstype er **SetPassword**.
OldPassword | Streng | Udfyldt med objektets gamle adgangskode for ændringer af adgangskode. Denne parameter er kun tilgængelig, hvis handlingstype er **ændring af adgangskode**.
NewPassword | Streng | Udfyldt med objektets nye adgangskode, scriptet skal angives.

Adgangskode scriptet forventes ikke til at returnere resultater til Windows PowerShell-rørledning. Hvis der opstår fejl i scriptet adgangskode, bør scriptet udløse en af følgende undtagelser til at informere tjenesten Brugerprofilsynkronisering om problemet:

- [PasswordPolicyViolationException] [ pwdex1] –, hvis adgangskoden ikke opfylder adgangskodepolitik i den tilsluttede system.
- [PasswordIllFormedException] [ pwdex2] –, hvis adgangskoden ikke er acceptable for det tilsluttede system.
- [PasswordExtension] [ pwdex3] – udløst for alle andre fejl i scriptet adgangskode.

## <a name="sample-connectors"></a>Eksempel forbindelser
Se en komplet oversigt over tilgængelige eksempel forbindelserne, [Windows PowerShell forbindelse eksempel forbindelse samling][samp].

## <a name="other-notes"></a>Andre noter

### <a name="additional-configuration-for-impersonation"></a>Yderligere konfiguration for repræsentation
Giv den bruger, der er repræsenteret følgende tilladelser på tjenesten Brugerprofilsynkronisering serveren:

Læs adgang til de følgende registreringsdatabasenøgler:

- HKEY_USERS\\[SynchronizationServiceServiceAccountSID] \Software\Microsoft\PowerShell
- HKEY_USERS\\[SynchronizationServiceServiceAccountSID] \Environment

For at bestemme id sikkerheds-tjenesten Brugerprofilsynkronisering service-konto skal du køre følgende PowerShell-kommandoer:

```
$account = New-Object System.Security.Principal.NTAccount "<domain>\<username>"
$account.Translate([System.Security.Principal.SecurityIdentifier]).Value
```

Læseadgang til følgende fil systemmapper:

- %ProgramFiles%\Microsoft forefront identitet Manager\2010\Synchronization Service\Extensions
- %ProgramFiles%\Microsoft forefront identitet Manager\2010\Synchronization Service\ExtensionsCache
- %ProgramFiles%\Microsoft forefront identitet Manager\2010\Synchronization Service\MaData\\{ConnectorName}

Erstat navnet på Windows PowerShell forbindelsen pladsholderen {ConnectorName}.

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

-   Du kan finde oplysninger om, hvordan du aktiverer logføring til at foretage fejlfinding af forbindelsen, [hvordan du kan aktivere ETW sporing for forbindelser](http://go.microsoft.com/fwlink/?LinkId=335731).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[cpp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameterpage.aspx
[keyk]: https://msdn.microsoft.com/library/ms132438.aspx
[cp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameter.aspx
[pscred]: https://msdn.microsoft.com/library/system.management.automation.pscredential.aspx
[schema]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schema.aspx
[schemaT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schematype.aspx
[schemaA]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schemaattribute.aspx
[dnstyle]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.madistinguishednamestyle.aspx
[exportT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maexporttype.aspx
[DataNorm]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.manormalizations.aspx
[oconf]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maobjectconfirmation.aspx
[dw]: https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx
[part]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.partition.aspx
[hn]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.hierarchynode.aspx
[oicrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionrunstep.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[oicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionresults.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[cicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeimportconnectionresults.aspx
[oecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openexportconnectionrunstep.aspx
[irs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.importrunstep.aspx
[cse]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentry.aspx
[csec]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentrychange.aspx
[peeres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.putexportentriesresults.aspx
[pwdopt]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordoptions.aspx
[pwdex1]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordpolicyviolationexception.aspx
[pwdex2]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordillformedexception.aspx
[pwdex3]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordextensionexception.aspx
[samp]: http://go.microsoft.com/fwlink/?LinkId=394291
