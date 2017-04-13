<properties
    pageTitle="Installere Azure stak Konceptet | Microsoft Azure"
    description="Lær, hvordan du forbereder Azure stak Konceptet og udfører PowerShell-script for at installere Azure stak Konceptet."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/20/2016"
    ms.author="erikje"/>

# <a name="deploy-azure-stack-poc"></a>Installere Azure stak Konceptet
Hvis du vil installere Azure stak Konceptet, skal du først [forberede installation maskinen](#prepare-the-deployment-machine) og derefter [køre scriptet PowerShell installation](#run-the-powershell-deployment-script).

## <a name="download-and-extract-microsoft-azure-stack-poc-tp2"></a>Hente og udpakke Microsoft Azure stak Konceptet TP2

Før du begynder, skal du kontrollere, som du mindst 85 GB plads.

1. Overførslen af Azure stak Konceptet TP2 består af en zip-fil, der indeholder følgende 12 filer, summering ~ 20 GB:
    - 1 MicrosoftAzureStackPOC.EXE
2. Gennemse licensaftalen skærmen, og oplysninger i guiden Self-Extractor, og klik derefter på **Næste**.
3. Gennemse erklæring skærmen, og oplysninger i guiden Self-Extractor, og klik derefter på **Næste**.
4. Vælg Destination for filerne, der skal udtrækkes skal du klikke på **Næste**.
    - Standard er: <drive letter>:\<aktuelle mappe > \Microsoft Azure stak Konceptet
5. Gennemse Destination placering skærmen, og oplysninger i guiden Self-Extractor, og klik derefter på **udtrække** at udtrække CloudBuilder.vhdx (~44.5 GB) og ThirdPartyLicenses.rtf filer.

> [AZURE.NOTE] Når du har pakket filerne, kan du slette zip-filen for at få mere ledig plads på computeren. Eller du kan flytte zip-filen til en anden placering, så, at hvis du har brug at geninstallere du ikke behøver at hente zip-filer igen.

## <a name="prepare-the-deployment-machine"></a>Forberede installationen maskinen

1. Sørg for, at du fysisk kan oprette forbindelse til installation maskinen, eller har adgang til fysisk konsollen (såsom KVM). Du skal sådan adgang, når du genstarter installation i trin 9.

2. Kontrollér, at installation computeren opfylder [minimumkravene](azure-stack-deploy.md). Du kan bruge [Installation kompatibilitetskontrollen for Azure stak Technical Preview 2](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) for at bekræfte dine behov.

3. Log på som lokal Administrator til din computer og Konceptet.

4. Kopiere filen CloudBuilder.vhdx til C:\CloudBuilder.vhdx.

    > [AZURE.NOTE] Hvis du vælger ikke at bruge anbefalede scriptet til at forberede computeren Konceptet host (trin 5 – trin 7), Angiv ikke en licens tast på siden aktivering. En prøveversion af Windows Server 2016 billede er inkluderet, og at angive en licensnøgle får udløb advarselsmeddelelser.

5. Kør følgende PowerShell-script for at hente filerne til understøttelse af Azure stak TP2 på maskinen Konceptet:

    ```powershell
    # Variables
    $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/'
    $LocalPath = 'c:\AzureStack_TP2_SupportFiles'

    # Create folder
    New-Item $LocalPath -type directory

    # Download files
    ( 'BootMenuNoKVM.ps1', 'PrepareBootFromVHD.ps1', 'Unattend.xml', 'unattend_NoKVM.xml') | foreach { Invoke-WebRequest ($uri + $_) -OutFile ($LocalPath + '\' + $_) } 
    ```

    Dette script henter filerne til understøttelse af Azure stak TP2 til den mappe, der er angivet af parameteren $LocalPath.
    
6. Åbne en øgede PowerShell console og ændre mappen til sted, hvor du kopierede filerne.
    - 11 MicrosoftAzureStackPOC-N.BIN (hvor N er 1-11)
7. Højreklik på MicrosoftAzureStackPOC.EXE > Kør som administrator.

8. Kør scriptet PrepareBootFromVHD.ps1. Dette script og automatiske filer er tilgængelige med de andre support scripts leveres sammen med dette build.
    Der er fem parametre for denne PowerShell-script:
    - CloudBuilderDiskPath (obligatorisk) – stien til CloudBuilder.vhdx på VÆRTEN.
    - DriverPath (valgfrit) – kan du tilføje yderligere drivere til værten i den virtuelle HD.
    - ApplyUnattend (valgfrit) – Angiv denne parameter parameter til at automatisere konfigurationen af operativsystemet. Hvis angivet, skal brugeren have AdminPassword for at konfigurere Operativsystemet ved start (kræver forudsat tilhørende fil unattend_NoKVM.xml).
    Hvis du ikke bruger denne parameter, bruges filen generisk unattend.xml uden yderligere tilpasning. Du skal KVM komplet tilpasning efter den er genstartet.
    - AdminPassword (valgfrit) – bruges kun, når parameteren ApplyUnattend er angivet, kræver mindst seks tegn.
    - VHDLanguage (valgfrit) – angiver det Virtuelle sprog, som standard til "en-US".
    Scriptet er beskrevet og indeholder eksempel på format, selvom den mest almindelige brug er:
    
        `.\PrepareBootFromVHD.ps1 -CloudBuilderDiskPath C:\CloudBuilder.vhdx -ApplyUnattend`
    
        Hvis du kører denne nøjagtige kommando, skal du angive AdminPassword kommandoprompten.

9. Når scriptet er fuldført, skal du bekræfte genstarten. Hvis der er andre brugere, der er logget på, mislykkes denne kommando. Hvis kommandoen mislykkes, skal du køre følgende kommando:`Restart-Computer -force` 

10. VÆRTEN genstarter til OS af CloudBuilder.vhdx, hvor installationen fortsætter.

> [AZURE.IMPORTANT] Azure stak kræver adgang til internettet, skal du enten direkte eller via en gennemsigtig proxy. TP2 Konceptet installationen understøtter ét NIC til netværk. Hvis du har flere netværkskort, Kontrollér, at kun én er aktiveret (og alle andre er deaktiveret) før du kører Installation scriptet i næste afsnit.

## <a name="run-the-powershell-deployment-script"></a>Kør scriptet PowerShell-installation

1. Log på som lokal Administrator til din computer og Konceptet. Bruge de legitimationsoplysninger, der er angivet i de forrige trin.

2. Åbn en øgede PowerShell console.

3. I PowerShell, kører denne kommando:`cd C:\CloudDeployment\Configuration`

4. Kør kommandoen Implementer:`.\InstallAzureStackPOC.ps1`

5. Angiv en adgangskode **Indtast adgangskoden** for kommandoprompten, og derefter bekræfte den. Dette er adgangskoden til de virtuelle computere. Sørg for at registrere den.

6. Angiv legitimationsoplysningerne for din Azure Active Directory-konto. Denne bruger skal være den globale administrator i directory lejeren.

7. Installationsprocessen kan tage et par timer, hvor systemet automatisk genstarter én gang.

    > [AZURE.IMPORTANT] Hvis du vil overvåge forløbet installation, skal du logge på som azurestack\AzureStackAdmin. Hvis du logge på som en lokal administrator når computeren har forbindelse til domænet, kan du ikke se installationsstatus. Ikke køre Installation, i stedet logge på som azurestack\AzureStackAdmin til at validere, at den kører.

    Når installationen lykkes, viser PowerShell-konsollen: **FÆRDIGT: handling "Fordeling"**.

    Hvis installationen mislykkes, kan du prøve at [køre den igen](azure-stack-rerun-deploy.md). Eller du kan [geninstallere](azure-stack-redeploy.md) det fra bunden.

### <a name="deployment-script-examples"></a>Installation scripteksempler

Hvis din AAD identitet kun er tilknyttet en AAD mappe:

    cd C:\CloudDeployment\Configuration
    $adminpass = ConvertTo-SecureString "<LOCAL ADMIN PASSWORD>" -AsPlainText -Force
    $aadpass = ConvertTo-SecureString "<AAD GLOBAL ADMIN ACCOUNT PASSWORD>" -AsPlainText -Force
    $aadcred = New-Object System.Management.Automation.PSCredential ("<AAD GLOBAL ADMIN ACCOUNT>", $aadpass)
    .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass -AADAdminCredential $aadcred

Hvis din AAD identitet er knyttet til mere end én AAD mappe:

    cd C:\CloudDeployment\Configuration
    $adminpass = ConvertTo-SecureString "<LOCAL ADMIN PASSWORD>" -AsPlainText -Force
    $aadpass = ConvertTo-SecureString "<AAD GLOBAL ADMIN ACCOUNT PASSWORD>" -AsPlainText -Force
    $aadcred = New-Object System.Management.Automation.PSCredential ("<AAD GLOBAL ADMIN ACCOUNT> example: user@AADDirName.onmicrosoft.com>", $aadpass)
    .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass -AADAdminCredential $aadcred -AADDirectoryTenantName "<SPECIFIC AAD DIRECTORY example: AADDirName.onmicrosoft.com>"

Hvis dit miljø ikke har DHCP er aktiveret, skal du medtage følgende yderligere parametre til en af indstillingerne over (eksempel på format angivet):

    .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass -AADAdminCredential $aadcred
    -NatIPv4Subnet 10.10.10.0/24 -NatIPv4Address 10.10.10.3 -NatIPv4DefaultGateway 10.10.10.1


### <a name="installazurestackpocps1-optional-parameters"></a>InstallAzureStackPOC.ps1 valgfrie parametre

| Parameter | Påkrævede/valgfrie | Beskrivelse |
| --------- | ----------------- | ----------- |
| AADAdminCredential | Valgfri | Angiver Azure Active Directory-brugernavn og adgangskode. Disse Azure legitimationsoplysninger kan være enten en Org-ID eller en Microsoft-Account. For at bruge Microsoft-Account legitimationsoplysninger skal du Udelad denne parameter i cmdlet. Denne parameter er udeladt beder om Azure godkendelse pop-up-vinduet under installationen. Dette opretter de godkendelse og opdatering tokens, der bruges under installationen. |
| AADDirectoryTenantName | Påkrævet | Angiver lejer mappen. Brug denne parameter til at angive en bestemt mappe, hvor kontoen AAD har tilladelse til at administrere flere mapper. Fulde navn med en AAD Directory lejer i formatet af <directoryName>. onmicrosoft.com. |
| AdminPassword | Påkrævet | Angiver den lokale administratorkonto og alle andre brugerkonti på alle de virtuelle-maskiner, der er oprettet som en del af Konceptet installation. Denne adgangskode skal svare til den aktuelle lokale administratoradgangskode på værten. |
| AzureEnvironment | Valgfri | Vælg det Azure-miljø, som du vil registrere denne Azure stak installation. Indstillingerne for inkluderer *Offentlige Azure*, *Azure - Kina* *Azure - amerikanske*. |
| EnvironmentDNS | Valgfri | Der oprettes en DNS-server som en del af Azure stak installationen. Hvis du vil tillade computere i løsningen til at fortolke navne uden for stemplet, give din eksisterende infrastruktur DNS-server. I stempel DNS-server videresender ukendt anmodninger om navneoversættelse til denne server. |
| NatIPv4Address | Nødvendige til understøttelse af DHCP NAT | Angiver en statisk IP-adresse til MAS BGPNAT01. Brug kun denne parameter, hvis DHCP ikke kan tildele en gyldig IP-adresse at få adgang til internettet. |
| NatIPv4DefaultGateway | Nødvendige til understøttelse af DHCP NAT | Angiver standardgatewayen bruges sammen med den statiske IP-adresse til MAS BGPNAT01. Brug kun denne parameter, hvis DHCP ikke kan tildele en gyldig IP-adresse at få adgang til internettet.  |
| NatIPv4Subnet | Nødvendige til understøttelse af DHCP NAT | IP-undernet præfiks, der bruges til DHCP over NAT support. Brug kun denne parameter, hvis DHCP ikke kan tildele en gyldig IP-adresse at få adgang til internettet.  |
| PublicVLan | Valgfri | Angiver det VLAN-ID. Brug kun denne parameter, hvis host og MAS BGPNAT01 skal konfigurere VLAN ID for at få adgang til de fysiske netværk (og Internet). For eksempel`.\InstallAzureStackPOC.ps1 –Verbose –PublicVLan 305` |
| Kør | Valgfri | Brug dette flag til at køre installationen.  Alle forrige input bruges. Angive data igen tidligere angivne understøttes ikke, fordi flere entydige værdier er oprettet og bruges til installation. |
| Tidsserveren | Valgfri | Brug denne parameter, hvis du vil angive et bestemt tidspunkt server. |

## <a name="next-steps"></a>Næste trin

[Oprette forbindelse til Azure stak](azure-stack-connect-azure-stack.md)
