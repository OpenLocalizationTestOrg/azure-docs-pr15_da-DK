<properties
    pageTitle="Hvordan du kan generere og overføre HSM beskyttet taster til Azure-tasten samling | Microsoft Azure"
    description="Brug denne artikel kan hjælpe dig med at planlægge, generere og derefter overføre din egen HSM beskyttet taster til brug med Azure-tasten samling."
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="cabailey"/>
#<a name="how-to-generate-and-transfer-hsm-protected-keys-for-azure-key-vault"></a>Hvordan du kan generere og overføre HSM beskyttet taster til Azure-tasten samling

##<a name="introduction"></a>Introduktion

For større sikkerhed, når du bruger Azure-tasten samling, kan du importere eller oprette nøgler i hardware sikkerhed moduler (HSMs), som Efterlad aldrig i HSM rammen. Dette scenario er ofte kaldes *flytte din egen nøgle*, eller BYOK. HSMs er FIPS 140-2 niveau 2 valideres. Azure-tasten samling bruger Thales nShield familie af HSMs til at beskytte dine nøgler.

Brug oplysningerne i dette emne kan hjælpe dig med at planlægge, generere og derefter overføre din egen HSM beskyttet taster til brug med Azure-tasten samling.

Denne funktionalitet er ikke tilgængelig for Azure Kina. 

>[AZURE.NOTE] Finde flere oplysninger om Azure-tasten samling [Hvad er Azure-tasten samling?](key-vault-whatis.md)  
>
>Et få Introduktion selvstudium, som omfatter oprettelse af en vigtige samling af legitimationsoplysninger for HSM-beskyttede nøgler, kan du se [Introduktion til Azure nøgle samling](key-vault-get-started.md).

Du kan finde flere oplysninger om oprettelse og overførsel af en beskyttet med HSM nøgle via internettet:

- Du kan generere nøglen fra en offline arbejdsstation, hvilket reducerer angrebsoverfladen.

- Tasten er krypteret med en nøgle Exchange Key (KEK), som forbliver krypterede, indtil det er overført til Azure-tasten samling HSMs. Kun den krypterede version af din nøgle lader oprindelige arbejdsstationen.

- Værktøjer, der angiver egenskaber produktnøglen lejer, som binder produktnøglen til Azure-tasten samling sikkerhed verden. Så når Azure-tasten samling HSMs modtager og dekryptere produktnøglen, kan kun disse HSMs bruge den. Din nøgle kan ikke eksporteres. Denne binding gennemtvinges af Thales HSMs.

- Den nøgle Exchange Key (KEK), der bruges til at kryptere din nøgle genereres i Azure-tasten samling HSMs og kan ikke eksporteres. HSMs gennemtvinge, at der kan være ingen Ryd version af KEK uden for HSMs. Værktøjer, der indeholder desuden erklæring fra Thales, KEK kan ikke eksporteres og blev oprettet i en ægte HSM, der blev produceret af Thales.

- Værktøjer, der indeholder erklæring fra Thales, Azure-tasten samling sikkerhed verden også blev oprettet på en ægte HSM produceret af Thales. Denne erklæring viser sig til dig, at Microsoft bruger ægte hardware.

- Microsoft bruger separat KEKs og adskille sikkerhed verdens i hver geografisk område. Denne adskillelse sikrer, at din nøgle kan bruges kun i datacentre i det område, hvor du krypteret den. For eksempel ikke kan en nøgle fra en europæiske kunde bruges i datacentre i Nordamerika eller Asien.

##<a name="more-information-about-thales-hsms-and-microsoft-services"></a>Du kan finde flere oplysninger om Thales HSMs og Microsoft services

Thales e-sikkerhed er en foranstillet globale udbyder af kryptering af data og cyber sikkerhedsløsninger til finansielle tjenester, høj teknologi, produktionsanlæg, government og teknologi sektorer. Med en 40 års dokumenteret beskytte virksomhedens og government oplysninger, der bruges Thales løsninger af fire af de fem største energi og aerospace firmaer. Løsningen på problemet bruges også af 22 NATO 's lande og sikre mere end 80% af betalingstransaktioner i hele verden.

Microsoft har samarbejder med Thales til at forbedre tilstanden på clipart for HSMs. Disse forbedringer gør det muligt at få typisk fordelene ved tjenester uden at oprettelsen kontrol over dine nøgler. Nærmere betegnet kan lade disse forbedringer Microsoft administrere HSMs, så du ikke behøver at. Som en skybaseret tjeneste skaleres Azure-tasten samling med kort varsel til opfylder organisationens brugen spidser. På samme tid, der er beskyttet din nøgle i Microsoft-HSMs: du bevare kontrollen over vigtige livscyklus, fordi du oprette nøglen og overføre den til Microsofts HSMs.

##<a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Implementere få din egen nøgle (BYOK) til Azure-tasten samling

Brug følgende oplysninger om og procedurer, hvis du vil oprette dine egne HSM beskyttet nøgle og derefter overføre den til Azure-tasten samling – Flyt dit eget scenarie nøgle (BYOK).


##<a name="prerequisites-for-byok"></a>Forudsætninger for BYOK

Se tabellen nedenfor for en liste over forudsætninger for at få din egen nøgle (BYOK) til Azure-tasten samling.

|Krav|Få mere at vide|
|---|---|
|Et abonnement på Azure|Hvis du vil oprette en Azure-tasten samling, du har brug for et Azure-abonnement: [Tilmeld dig til gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/)|
|Azure-tasten samling Premium webtjenesten til at understøtte HSM-beskyttede nøgler|Du kan finde flere oplysninger om tjenesten niveauer og -funktioner til Azure-tasten samling på webstedet [Azure nøgle samling priser](https://azure.microsoft.com/pricing/details/key-vault/) .|
|Thales HSM, chipkort og understøttelse af software|Du skal have adgang til en Thales Hardware sikkerhedsmodul og grundlæggende funktionsdygtige kendskab til Thales HSMs. Du kan se [Thales Hardware sikkerhedsmodul](https://www.thales-esecurity.com/msrms/buy) til listen over kompatible modeller, eller til at købe en HSM, hvis du ikke har en.|
|Den følgende hardware og software:<ol><li>En offline x64 arbejdsstation med et minimum Windows-operativsystemet fra Windows 7 og Thales nShield-software, der er mindst version 11.50.<br/><br/>Hvis denne arbejdsstation kører Windows 7, skal du [installere Microsoft .NET Framework 4.5](http://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>En arbejdsstation, der er tilsluttet internettet og har et minimum Windows-operativsystemet af Windows 7.</li><li>Et USB-drev eller andre bærbare lagerplads enhed, der har mindst 16 MB ledig plads.</li></ol>|Af sikkerhedsmæssige årsager anbefales det, at den første arbejdsstation ikke er tilsluttet et netværk. Men denne anbefaling fra et program gennemtvinges ikke.<br/><br/>Bemærk, at i de instruktioner, der skal du følge denne arbejdsstation er kaldes afbrudt arbejdsstationen.</p></blockquote><br/>Desuden, hvis din lejer nøgle er et fremstilling netværk, anbefaler vi, at du bruger en anden, separat arbejdsstation til at hente værktøjer, og Overfør tasten lejer. Men du kan bruge den samme arbejdsstation til testformål, som den første opgave.<br/><br/>Bemærk, at i de instruktioner, der skal du følge denne anden arbejdsstation kaldes arbejdsstationen forbindelse til internettet.</p></blockquote><br/>|

##<a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Oprette og overføre din nøgle til Azure-tasten samling HSM

Du kan bruge følgende fem trin til at generere og overføre din nøgle til en Azure-tasten samling HSM:

- [Trin 1: Forbered arbejdsstationen forbindelse til internettet](#step-1-prepare-your-internet-connected-workstation)
- [Trin 2: Forberede arbejdsstationen afbrudt](#step-2-prepare-your-disconnected-workstation)
- [Trin 3: Opret din nøgle](#step-3-generate-your-key)
- [Trin 4: Forberede din nøgle til overførsel](#step-4-prepare-your-key-for-transfer)
- [Trin 5: Overføre din nøgle til Azure-tasten samling](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Trin 1: Forbered arbejdsstationen forbindelse til internettet
For det første trin skal du gøre følgende fremgangsmåder på computeren, der er forbundet til internettet.


###<a name="step-11-install-azure-powershell"></a>Trin 1.1: Installer Azure PowerShell

Fra din computer med internetforbindelse, hente og installere Azure PowerShell-modulet, der indeholder cmdletter for at administrere Azure-tasten samling. Dette kræver et minimumversion af 0.8.13.

Installationsvejledning, se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).

###<a name="step-12-get-your-azure-subscription-id"></a>Trin 1.2: Gøre dit Azure abonnement-ID

Starte en session med Azure PowerShell og logge på din Azure-konto ved hjælp af følgende kommando:

        Add-AzureAccount
Angiv dit brugernavn til Azure-konto og din adgangskode i pop op-browservinduet. Derefter bruge kommandoen [Get-AzureSubscription](https://msdn.microsoft.com/library/azure/dn790366.aspx) :

        Get-AzureSubscription
Find ID for det abonnement, du vil bruge til Azure-tasten samling fra outputtet. Du skal bruge dette abonnement-ID senere.

Undlad at lukke vinduet Azure PowerShell.

###<a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Trin 1.3: Hente værktøjer, der BYOK til Azure-tasten samling

Gå til Microsoft Download Center og [hente værktøjer, der Azure nøgle samling BYOK](http://www.microsoft.com/download/details.aspx?id=45345) til dit geografiske område eller forekomst af Azure. Brug følgende oplysninger til at identificere pakkenavn hente og dens tilsvarende SHA 256 pakke hash:

---

**Nordamerika:**

KeyVault-BYOK-værktøjer-United States.zip

305F44A78FEB750D1D478F6A0C345B097CD5551003302FA465C73D9497AB4A03

---

**Europe:**

KeyVault-BYOK-værktøjer-Europe.zip

C73BB0628B91471CA7F9ADFCE247561C6016A5103EF1A315D49C3EA23AFC0B9C

---

**Asien:**

KeyVault-BYOK-værktøjer-AsiaPacific.zip

BE9A84B6C76661929F9FDAD627005D892B3B8F9F19F351220BB4F9C356694192

---

**Latinamerika:**

KeyVault-BYOK-værktøjer-LatinAmerica.zip
    
9E8EE11972DECE8F05CD898AF64C070C375B387CED716FDCB788544AE27D3D23

---

**Japan:**

KeyVault-BYOK-værktøjer-Japan.zip

E6B88C111D972A02ABA3325F8969C4E36FD7565C467E9D7107635E3DDA11A8B2

---

**Australien:**

KeyVault-BYOK-værktøjer-Australia.zip

7660D7A675506737857B14F527232BE51DC269746590A4E5AB7D50EDD220675D

---

[**Azure Government:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-værktøjer-USGovCloud.zip

53801A3043B0F8B4A50E8DC01A935C2BFE61F94EE027445B65C52C1ACC2B5E80

---

**Canada:**

KeyVault-BYOK-værktøjer-Canada.zip

A42D9407B490E97693F8A5FA6B60DC1B06B1D1516EDAE7C9A71AA13E12CF1345

---

**Tyskland:**

KeyVault-BYOK-værktøjer-Germany.zip

4795DA855E027B2CA8A2FF1E7AE6F03F772836C7255AFC68E576410BDD28B48E

---
**Indien:**

KeyVault-BYOK-værktøjer-India.zip

26853511EB767A33CF6CD880E78588E9BBE04E619B17FBC77A6B00A5111E800C

---

Brug cmdlet'en [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) til at validere integriteten af din hentede BYOK værktøjer fra sessionen Azure PowerShell.

    Get-FileHash KeyVault-BYOK-Tools-*.zip

Værktøjer, der indeholder følgende:

- En nøgle Exchange Key (KEK)-pakke, der har et navn, der begynder med **BYOK-KEK - pkg-.**
- En sikkerhed verden pakke, der har et navn, der begynder med **BYOK-SecurityWorld - pkg-.**
- Et python script med navnet v**erifykeypackage.py.**
- En kommandolinjen eksekverbare fil navngivne **KeyTransferRemote.exe** og tilknyttede DLL-filer.
- En Visual C++ Redistributable-pakken, med navnet **vcredist_x64.exe.**

Kopiere pakken til et USB-drev eller andre bærbare lagerplads.

##<a name="step-2-prepare-your-disconnected-workstation"></a>Trin 2: Forberede arbejdsstationen afbrudt

For dette andet trin skal du gøre følgende fremgangsmåder på arbejdsstationen, der ikke er tilsluttet et netværk (enten internettet eller dit interne netværk).


###<a name="step-21-prepare-the-disconnected-workstation-with-thales-hsm"></a>Trin 2.1: Forbered afbrudt arbejdsstationen med Thales HSM

Installere nCipher (Thales) support softwaren på en Windows-computer, og derefter vedhæfte en Thales HSM til computeren.

Sikre, at Thales værktøjerne i din sti (**%nfast_home%\bin** og **%nfast_home%\python\bin**). For eksempel Skriv følgende:

        set PATH=%PATH%;”%nfast_home%\bin”;”%nfast_home%\python\bin”

Du kan finde flere oplysninger i brugervejledningen inkluderet med Thales HSM.

###<a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Trin 2.2: Installere værktøjer, der BYOK på afbrudt arbejdsstationen

Kopiér pakken BYOK værktøjer fra USB-drevet eller andre bærbare lagerplads, og gør derefter følgende:

1. Pak filerne i pakken hentede til en hvilken som helst mappe.
2. Køre vcredist_x64.exe fra den pågældende mappe.
3. Følg instruktionerne til installation Visual C++ runtime-komponenter til Visual Studio-2013.

##<a name="step-3-generate-your-key"></a>Trin 3: Opret din nøgle

Benyt følgende fremgangsmåder på afbrudt arbejdsstationen for denne tredje trin.

###<a name="step-31-create-a-security-world"></a>Trin 3.1: Oprette en sikkerhed verden

Starte en kommandoprompt og køre programmet Thales ny verden.

    new-world.exe --initialize --cipher-suite=DLf1024s160mRijndael --module=1 --acs-quorum=2/3

Dette program opretter filen **Sikkerhed verden** ved % NFAST_KMDATA%\local\world, som svarer til mappen C:\ProgramData\nCipher\Key Management Data\local. Du kan bruge forskellige værdier for quorum, men i vores eksempel, bliver du bedt om at angive tre tomme kort og ben for hver af dem. En hvilken som helst kortene Giv derefter fuld adgang til hele verden sikkerhed. Disse kort bliver den **Administrator kort konfigureret** for nye sikkerhed verden.

Gør derefter følgende:

- Sikkerhedskopiere filen verden. Sikre og beskytte filen verden, Administrator kort og deres ben, og Sørg for, at ingen enkelt person har adgang til mere end én kort.

###<a name="step-32-validate-the-downloaded-package"></a>Trin 3.2: Validere pakken hentede

Dette trin er valgfrit, men anbefales så kan du kontrollere følgende:

- Tasten nøgle Exchange, som medfølger i værktøjer, der er oprettet fra en ægte Thales HSM.
- Hashværdien af sikkerhed verden, der er inkluderet i værktøjer, der er oprettet i en ægte Thales HSM.
- Tasten nøgle Exchange kan ikke eksporteres.

>[AZURE.NOTE]For at validere pakken hentede, HSM skal være forbundet, tændt, og skal have en sikkerhed verden på den (som det, du lige har oprettet).

Til at validere pakken hentede:

1.  Du kan køre scriptet verifykeypackage.py ved at knytte et af følgende, afhængigt af dit geografiske område eller en forekomst af Azure:
    - I Nordamerika:

            python verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
    - For Europa:

            python verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
    - For Asien:

            python verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
    - For Latinamerika:

            python verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
    - Til Japan:

            python verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
    - For Australien:

            python verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
    - Til [Azure Government](https://azure.microsoft.com/features/gov/), som bruger den amerikanske regering forekomst af Azure:

            python verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
    - Canada:

            python verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
    - For Tyskland:

            python verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
    - For Indien:

            python verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
    >[AZURE.TIP]Thales softwaren indeholder python på %NFAST_HOME%\python\bin

2.  Bekræft, at du se følgende oplysninger, der angiver bekræftelsen er fuldført: **resultat: succes**

Dette script valideret underskriver kæden op til tasten Thales rod. Hashværdien af denne nøgle er integreret i scriptet og dens værdi skal være **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Du kan også bekræfte denne værdi separat ved at besøge [Thales websted](http://www.thalesesec.com/).

Nu er du klar til at oprette en ny nøgle.

###<a name="step-33-create-a-new-key"></a>Trin 3.3: Oprette en ny nøgle

Oprette en nøgle ved hjælp af programmet Thales **generatekey** .

Kør følgende kommando for at oprette nøglen:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Når du kører denne kommando, du Brug disse instruktioner:

- Parameter *beskytte* skal være indstillet til værdi **modul**, som vist. Dette opretter en modul-beskyttede nøgle. Værktøjer, der BYOK understøtter ikke tilføjelsesprogrammet OCS-beskyttede taster.

- Erstat værdien af *contosokey* for **ident** og **plainname** med en hvilken som helst strengværdi. For at minimere administrative udgifter og reducere risikoen for fejl, anbefaler vi, at du bruger den samme værdi i begge. Værdien **ident** skal indeholde kun tal, stiplet og små bogstaver.

- Pubexp udfyldes tom (standard) i dette eksempel, men du kan angive bestemte værdier. Du kan finde flere oplysninger i dokumentationen til Thales.

Denne kommando opretter en Tokenized nøgle-fil i mappen %NFAST_KMDATA%\local med et navn, der starter med **key_simple_**, efterfulgt af **ident** , der blev angivet i kommandoen. For eksempel: **key_simple_contosokey**. Denne fil indeholder en krypteret nøgle.

Sikkerhedskopiere denne Tokenized nøgle-fil i et sikkert sted.

>[AZURE.IMPORTANT] Når du overfører senere produktnøglen til Azure-tasten samling, kan ikke Microsoft eksportere denne tast tilbage til dig, så det bliver meget vigtigt, at du sikkert sikkerhedskopiere din nøgle og sikkerhed verden. Kontakt Thales for vejledning og bedste fremgangsmåder til at sikkerhedskopiere din nøgle.

Nu er du klar til at overføre din nøgle til Azure-tasten samling.

##<a name="step-4-prepare-your-key-for-transfer"></a>Trin 4: Forberede din nøgle til overførsel

Benyt følgende fremgangsmåder på afbrudt arbejdsstationen for dette fjerde trin.

###<a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Trin 4.1: Oprette en kopi af din nøgle med reduceret tilladelser

Du kan reducere tilladelser på din nøgle fra en kommandoprompt ved at køre en af følgende, afhængigt af dit geografiske område eller en forekomst af Azure:

- I Nordamerika:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
- For Europa:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
- For Asien:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
- For Latinamerika:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
- Til Japan:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
- For Australien:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
- Til [Azure Government](https://azure.microsoft.com/features/gov/), som bruger den amerikanske regering forekomst af Azure:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
- Canada:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
- For Tyskland:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
- For Indien:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1


Når du kører denne kommando, du erstatte *contosokey* med den samme værdi, du har angivet i **trin 3.3: oprette en ny nøgle** fra trinnet [Generer din nøgle](#step-3-generate-your-key) .

Du bliver bedt om at Tilslut din sikkerhed verden administrator kort.

Når kommandoen er fuldført, skal du se **resultat: succes** og kopien af din nøgle med reduceret tilladelser er i filen med navnet key_xferacId_<contosokey>.

###<a name="step-42-inspect-the-new-copy-of-the-key"></a>Trin 4.2: Undersøge den nye kopi af nøglen

Du kan også køre Thales værktøjer for at bekræfte minimale tilladelser på den nye nøgle:

- aclprint.py:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
- kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
Når du kører disse kommandoer, kan du erstatte contosokey med den samme værdi, du har angivet i **trin 3.3: oprette en ny nøgle** fra trinnet [Generer din nøgle](#step-3-generate-your-key) .

###<a name="step-43-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Trin 4,3: Kryptere din nøgle ved hjælp af Microsoft-nøgle Exchange-tasten

Køre en af følgende kommandoer, afhængigt af dit geografiske område eller en forekomst af Azure:

- I Nordamerika:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- For Europa:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- For Asien:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- For Latinamerika:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Til Japan:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- For Australien:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Til [Azure Government](https://azure.microsoft.com/features/gov/), som bruger den amerikanske regering forekomst af Azure:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Canada:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- For Tyskland:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- For Indien:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey


Når du kører denne kommando, du Brug disse instruktioner:

- Erstatte *contosokey* med det id, som du brugte til at generere nøglen i **trin 3.3: oprette en ny nøgle** fra trinnet [Generer din nøgle](#step-3-generate-your-key) .

- Erstat *SubscriptionID* med ID Azure abonnementet, der indeholder dine vigtige samling. Du har hentet denne værdi i tidligere **trin 1.2: få dit Azure abonnement-ID** fra [forberede din internetforbindelse arbejdsstation](#step-1-prepare-your-internet-connected-workstation) trin.

- Erstat *ContosoFirstHSMKey* med en etiket, der bruges til dit outputfilnavn.

Når dette er fuldført, vises der **resultat: succes** , og der er en ny fil i den aktuelle mappe, der indeholder følgende navn: TransferPackage -*ContosoFirstHSMkey*.byok

###<a name="step-44-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Trin 4.4: Kopiér din nøgle filoverførsel pakke til arbejdsstationen forbindelse til internettet

Brug et USB-drev eller andre bærbare lagerplads til at kopiere outputfilen fra det forrige trin (KeyTransferPackage-ContosoFirstHSMkey.byok) til din internetforbindelse arbejdsstation.

##<a name="step-5-transfer-your-key-to-azure-key-vault"></a>Trin 5: Overføre din nøgle til Azure-tasten samling

Bruge [Tilføj AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048\(v=azure.300\).aspx) cmdlet til at overføre pakken vigtige filoverførsel, som du kopierede fra afbrudt arbejdsstationen Azure-tasten samling HSM på din computer med internetforbindelse, for denne sidste trin:

    Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\TransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'

Hvis overførslen er fuldført, får du vist vises egenskaberne for nøglen, som du lige har tilføjet.


##<a name="next-steps"></a>Næste trin

Du kan nu bruge denne HSM beskyttet nøgle i din nøgle samling. Yderligere oplysninger finder du i afsnittet **Hvis du vil bruge et hardware sikkerhed-modul (HSM)** i [Introduktion til Azure nøgle samling](key-vault-get-started.md) selvstudium.
