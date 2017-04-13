<properties
    pageTitle="Konfiguration af WinRM adgang til virtuelle maskiner i Azure ressourcestyring | Microsoft Azure"
    description="Sådan konfigurerer du WinRM adgang til brug sammen med en Azure ressourcestyring virtuelt"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/16/2016"
    ms.author="singhkay"/>

# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Konfiguration af WinRM adgang til virtuelle maskiner i Azure ressourcestyring.

## <a name="winrm-in-azure-service-management-vs-azure-resource-manager"></a>WinRM i Azure Service Management vs Azure ressourcestyring

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisk implementeringsmodel

* En oversigt over Azure Ressourcestyring, skal du se i denne [artikel](../azure-resource-manager/resource-group-overview.md)
* Forskelle mellem Azure Service Management og Azure Ressourcestyring, finder du i denne [artikel](../resource-manager-deployment-model.md)

Vigtige forskellen i at konfigurere WinRM konfiguration mellem de to stakke er, hvordan certifikatet, der bliver installeret på VM. Stablede Azure ressourcestyring er tilpasset certifikater som ressourcer, der administreres af provideren nøgle samling ressource. Brugeren skal derfor angive deres egne certifikat og overføre den til en samling af legitimationsoplysninger nøgle inden du bruger det i en VM.

Her er de trin, du skal gennemføre for at konfigurere en VM med WinRM connectivity

1. Oprette en vigtige samling
2. Oprette et selvsigneret certifikat
3. Overføre din selvsigneret certifikat til tasten samling
4. Hente URL-adressen til det selvsignerede certifikat i den nøgle samling af legitimationsoplysninger
5. Henvise til din selvsignerede certifikater URL-adresse under oprettelse af en VM

## <a name="step-1-create-a-key-vault"></a>Trin 1: Oprette en vigtige samling

Du kan bruge den under kommandoen til at oprette den nøgle samling af legitimationsoplysninger

```
New-AzureRmKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Trin 2: Oprette et selvsigneret certifikat
Du kan oprette et selvsigneret certifikat ved hjælp af denne PowerShell-script

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Trin 3: Overføre din selvsigneret certifikat til den tast samling af legitimationsoplysninger

Før du overfører certifikatet til den tast samling af legitimationsoplysninger oprettede i trin 1, skal den konverteret til et format, vil kunne forstå provideren Microsoft.Compute ressource. Den under PowerShell script, så du gør dette

```
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

$jsonObject = @"
{
  "data": "$filecontentencoded",
  "dataType" :"pfx",
  "password": "<password>"
}
"@

$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Trin 4: Hente URL-adressen til det selvsignerede certifikat i den nøgle samling af legitimationsoplysninger

Provideren Microsoft.Compute ressource skal have en URL-adressen til hemmeligt i den samling af legitimationsoplysninger nøgle under klargøring af VM. Dette gør det muligt for Microsoft.Compute ressource hos udbyderen for at hente hemmeligheden og oprette det certifikat, der svarer på VM.

>[AZURE.NOTE]Hemmeligheden bag URL-adressen skal indeholde versionen samt. Et eksempel på URL-adressen ligner under https://contosovault.vault.azure.net:443/hemmeligheder/contososecret/01h9db0df2cd4300a20ence585a6s7ve


#### <a name="templates"></a>Skabeloner

Du kan få link til URL-adressen på den skabelon ved hjælp af den under kode

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell

Du kan få denne URL-adresse ved hjælp af den under PowerShell-kommando

    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Trin 5: Henvise til din selvsignerede certifikater URL-adresse under oprettelse af en VM

#### <a name="azure-resource-manager-templates"></a>Azure ressourcestyring skabeloner

Mens du opretter en VM skabeloner igennem, bliver certifikatet, der refereres til i sektionen hemmeligheder og sektionen winRM som nedenfor:

    "osProfile": {
          ...
          "secrets": [
            {
              "sourceVault": {
                "id": "<resource id of the Key Vault containing the secret>"
              },
              "vaultCertificates": [
                {
                  "certificateUrl": "<URL for the certificate you got in Step 4>",
                  "certificateStore": "<Name of the certificate store on the VM>"
                }
              ]
            }
          ],
          "windowsConfiguration": {
            ...
            "winRM": {
              "listeners": [
                {
                  "protocol": "http"
                },
                {
                  "protocol": "https",
                  "certificateUrl": "<URL for the certificate you got in Step 4>"
                }
              ]
            },
            ...
          }
        },

Et eksempel på en skabelon for ovenstående kan findes her [201-vm-winrm-keyvault -](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows) vinduer

Kildekode for denne skabelon kan findes på [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell

    $vm = New-AzureRmVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzureRmKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>Trin 6: Oprette forbindelse til VM
Før du kan oprette forbindelse til VM skal du kontrollere er din computer konfigureret til WinRM fjernadministration. Starte PowerShell som administrator og udføre de under kommandoen at sikre, at du er færdig.

    Enable-PSRemoting -Force

>[AZURE.NOTE] Du muligvis at sikre, at WinRM-tjenesten kører, hvis ovenstående ikke virker. Du kan gøre den med`Get-Service WinRM`

Når installationen er færdig, kan du oprette forbindelse til den VM ved hjælp af den under kommandoen

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate