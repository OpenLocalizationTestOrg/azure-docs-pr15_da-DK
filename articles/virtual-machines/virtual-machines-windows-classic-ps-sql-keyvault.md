<properties
    pageTitle="Konfigurere Azure vigtige samling Integration til SQL Server på Azure FOS (klassisk)"
    description="Lær at automatisere konfigurationen af SQL Server-kryptering til brug sammen med Azure-tasten samling. Dette emne beskriver, hvordan du bruger Azure-tasten samling Integration med SQL Server virtuelle maskiner oprette i modellen Klassisk installation."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/26/2016"
    ms.author="jroth"/>

# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-classic"></a>Konfigurere Azure vigtige samling Integration til SQL Server på Azure FOS (klassisk)

> [AZURE.SELECTOR]
- [Ressourcestyring](virtual-machines-windows-ps-sql-keyvault.md)
- [Klassisk](virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Oversigt
Der findes flere SQL Server-krypteringsfunktioner, som [gennemsigtig datakryptering (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [kolonne niveau kryptering (KEL)](https://msdn.microsoft.com/library/ms173744.aspx)og [Sikkerhedskopiering kryptering](https://msdn.microsoft.com/library/dn449489.aspx). Disse former for kryptering kræver, at du til at administrere og gemme de cryptographic taster, der bruges til kryptering. Tjenesten Azure nøgle samling (AKV) er udviklet til at forbedre sikkerheden og administration af disse taster på en sikker og fleksibelt placering. [SQL Server Connector](http://www.microsoft.com/download/details.aspx?id=45344) kan SQL Server til at bruge disse taster fra Azure-tasten samling.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Hvis du kører SQL Server med lokale computere, er der [trin, du kan følge for at få adgang til Azure nøgle samling fra computeren lokal SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Men til SQL Server på Azure FOS, kan du spare tid ved hjælp af funktionen *Azure nøgle samling Integration* . Med et par Azure PowerShell-cmdlet'er til at aktivere denne funktion, kan du automatisere konfigurationen nødvendige til en SQL-VM at få adgang til dine vigtige samling.

Når denne funktion er aktiveret, den automatisk installeres SQL Server-forbindelsen, konfigurerer EKM udbyderen for at få adgang til Azure-tasten samling og opretter de legitimationsoplysninger, der gør det muligt at få adgang til din samling af legitimationsoplysninger. Hvis du har set trinnene i den tidligere nævnte lokalt dokumentation, kan du se, at denne funktion automatisere trin 2 og 3. Det eneste du stadig har brug for at gøre det manuelt er at oprette de vigtigste samling og -taster. Hele konfigurationen af din SQL VM er automatiseret derfra. Du kan udføre T-SQL-sætninger for at begynde at kryptere dine databaser eller sikkerhedskopier, som du normalt ville gøre, når denne funktion er afsluttet denne konfiguration.

[AZURE.INCLUDE [AKV Integration Prepare](../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Konfigurere AKV Integration
Bruge PowerShell til at konfigurere Azure-tasten samling Integration. De følgende afsnit indeholder en oversigt over de krævede parametre og et eksempel PowerShell-script.

### <a name="install-the-sql-server-iaas-extension"></a>Installere udvidelsen IaaS til SQL Server

Først skal [installere SQL Server IaaS filtypenavn](virtual-machines-windows-classic-sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Forstå de inputparametre
I følgende tabel vises de parametre, der er nødvendige for at køre PowerShell-scriptet i næste afsnit.

|Parameter|Beskrivelse|Eksempel|
|---|---|---|
|**$akvURL**|**Vigtige samling URL-adressen**|"https://contosokeyvault.vault.azure.net/"|
|**$spName**|**Tjenestens hovednavn**|"fde2b411 - 33d 5-4e11-af04eb07b669ccf2"|
|**$spSecret**|**Tjenesten hovedstolen hemmeligt**|"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM ="|
|**$credName**|**Legitimationsoplysninger for navn**: AKV Integration opretter en legitimationsoplysninger i SQL Server, så VM skal have adgang til den vigtige samling af legitimationsoplysninger. Vælg et navn for denne legitimationsoplysninger.|"mycred1"|
|**$vmName**|**Virtuelt navn**: navnet på et tidligere oprettet SQL VM.|"myvmname"|
|**$serviceName**|**Navnet på tjenesten**: feltet skytjeneste navn, der er knyttet til den SQL VM.|"mycloudservicename"|

### <a name="enable-akv-integration-with-powershell"></a>Aktivere AKV Integration med PowerShell
**Ny AzureVMSqlServerKeyVaultCredentialConfig** cmdlet opretter et konfigurationsobjekt til funktionen Azure-tasten samling Integration. **Angiv AzureVMSqlServerExtension** konfigurerer denne integration med parameteren **KeyVaultCredentialSettings** . Følgende trin viser, hvordan du bruger disse kommandoer.

1. Azure PowerShell først konfigurere de inputparametre med dine specifikke værdier som beskrevet i forrige afsnit i dette emne. Følgende script er et eksempel.

        $akvURL = "https://contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2.  Derefter skal du bruge følgende script til at konfigurere og aktivere AKV Integration.

        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

Filtypenavnet SQL IaaS Agent opdateres SQL VM med denne nye konfiguration.

[AZURE.INCLUDE [AKV Integration Next Steps](../../includes/virtual-machines-sql-server-akv-next-steps.md)]
