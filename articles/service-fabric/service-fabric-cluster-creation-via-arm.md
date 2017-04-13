
<properties
   pageTitle="Oprette en sikker Service-strukturen klynge ved hjælp af Azure ressourcestyring | Microsoft Azure"
   description="I denne artikel beskriver, hvordan du konfigurerer en sikker Service-strukturen klynge i Azure med Azure Ressourcestyring, Azure-tasten samling og Azure Active Directory (AAD) til klientgodkendelse."
   services="service-fabric"
   documentationCenter=".net"
   authors="chackdan"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="vturecek"/>

# <a name="create-a-service-fabric-cluster-in-azure-using-azure-resource-manager"></a>Oprette en tjeneste-strukturen klynge i Azure ved hjælp af Azure ressourcestyring

> [AZURE.SELECTOR]
- [Azure ressourcestyring](service-fabric-cluster-creation-via-arm.md)
- [Azure-portalen](service-fabric-cluster-creation-via-portal.md)

Dette er en trinvis vejledning, der vejleder dig gennem trinnene til konfiguration af en sikker Azure Service strukturen klynge i Azure ved hjælp af Azure ressourcestyring. Denne vejledning vejleder dig gennem følgende trin:

 - Konfigurere nøgle samling af legitimationsoplysninger til at administrere taster til klynge og programmet sikkerhed.
 - Oprette en sikker klynge i Azure med Azure ressourcestyring.
 - Godkende brugere med Azure Active Directory (AAD) til at administrere klynge.

En sikker klynge er en klynge, der forhindrer uautoriseret adgang til administration af handlinger, som omfatter implementering, opgradering og sletning af programmer, tjenester og de data, de indeholder. En usikker klynge er en klynge, at alle kan oprette forbindelse til enhver tid og udfører handlinger i administration. Selvom det er muligt at oprette en usikker klynge, er det **anbefaler til at oprette en sikker klynge**. En usikker klynge **kan ikke sikres senere** - skal der oprettes en ny klynge.

Begreberne er de samme for at oprette sikker klynger, uanset om klynger er Linux klynger eller Windows-grupper. Se [oprette sikker klynger på Linux](#secure-linux-clusters) for flere oplysninger og hjælper dig scripts til at oprette sikker Linux klynger

## <a name="log-in-to-azure"></a>Log på Azure
Denne vejledning bruger [Azure PowerShell][azure-powershell]. Når du starter en ny PowerShell-session, log på din Azure-konto, og vælg dit abonnement før udførelse af Azure kommandoer.

Log på din azure-konto:

```powershell
Login-AzureRmAccount
```

Vælg dit abonnement:

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="set-up-key-vault"></a>Konfigurere nøgle samling

Dette afsnit vejleder til at oprette en samling af nøgle legitimationsoplysninger for en tjeneste-strukturen klynge i Azure og for tjenesten strukturen programmer. For en komplet vejledning på tasten samling referere til den [tast samling Introduktion til][key-vault-get-started].

Tjenesten strukturen bruger x.509-certifikater til at sikre en klynge og give programmet sikkerhedsfunktioner. Azure-tasten samling bruges til at administrere certifikater for tjenesten strukturen klynger i Azure. Når en klynge er installeret i Azure, provideren Azure ressource har skabt Service-strukturen klynger trækker certifikater fra nøgle samling og installerer dem på klynge FOS.

I det følgende diagram viser forholdet mellem nøgle samling, en tjeneste-strukturen klynge og provideren Azure ressource, der bruger certifikater, der er gemt i nøgle samling, når der oprettes en klynge:

![Installation af certifikat][cluster-security-cert-installation]

### <a name="create-a-resource-group"></a>Oprette en ressourcegruppe

Det første trin er at oprette en ressourcegruppe specifikt for nøgle samling. Ibrugtagning sin egen ressourcegruppe nøgle samling anbefales. Dette gør det muligt at fjerne de Beregn og lager ressourcegrupper, herunder den ressourcegruppen, der indeholder din tjeneste strukturen klynge uden at miste dit nøgler og hemmeligheder. Ressourcegruppen, der indeholder din nøgle samling skal være i samme område som klynge, der bruger den.

```powershell

    New-AzureRmResourceGroup -Name mycluster-keyvault -Location 'West US'
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    
    ResourceGroupName : mycluster-keyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/mycluster-keyvault

```

### <a name="create-key-vault"></a>Oprette vigtige samling 

Oprette en samling af legitimationsoplysninger nøgle i den nye ressourcegruppe. Den nøgle samling **skal være aktiveret til installation** tillade Service-strukturen ressource hos udbyderen for at få certifikater fra det, og Installér på klyngenoder:

```powershell

    New-AzureRmKeyVault -VaultName 'myvault' -ResourceGroupName 'mycluster-keyvault' -Location 'West US' -EnabledForDeployment
    
    
    Vault Name                       : myvault
    Resource Group Name              : mycluster-keyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault
    Vault URI                        : https://myvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all
    
    
    Tags                             :
```

Hvis du har en eksisterende nøgle samling, kan du aktivere den til installation ved hjælp af Azure CLI:

```cli
> azure login
> azure account set "your account"
> azure config mode arm 
> azure keyvault list
> azure keyvault set-policy --vault-name "your vault name" --enabled-for-deployment true
```

<a id="add-certificate-to-key-vault"></a>
## <a name="add-certificates-to-key-vault"></a>Føje certifikater til tasten samling

Certifikater bruges i tjenesten strukturen til at give godkendelse og kryptering til at sikre forskellige aspekter af en klynge og dens programmer. Se [Service-strukturen klynge sikkerhed scenarier]kan finde flere oplysninger om hvordan certifikater bruges i tjenesten strukturen[service-fabric-cluster-security].

### <a name="cluster-and-server-certificate-required"></a>Klynge og server certifikat (påkrævet) 

Dette certifikat er påkrævet til at sikre en klynge og forhindre uautoriseret adgang til den. Den indeholder klynge sikkerhed i et par måder:
 
 - **Klynge godkendelse:** Godkender node-node-kommunikation til klynge sammenslutning. Kun noder, der kan bevise deres identitet med dette certifikat kan deltage i klyngen.
 - **Servergodkendelse:** Godkender klynge management slutpunkter til en management-klient, så management-klienten ved den taler til den reelle klynge. Dette certifikat indeholder også SSL til HTTPS-styring API og Service-strukturen Explorer over HTTPS.

Hvis du vil disse formål, skal certifikatet, der opfylder følgende krav:

 - Certifikatet, der skal indeholde en privat nøgle.
 - Certifikatet, der skal oprettes til vigtige exchange, eksporteres til en Personal Information Exchange (.pfx)-fil.
 - Certifikatets emnenavn skal svare til det domæne, der bruges til at få adgang til tjenesten strukturen klynge. Denne matchng er påkrævet for at give SSL til klyngen HTTPS management slutpunkter og Service-strukturen Explorer. Du kan ikke hente et SSL-certifikat fra et nøglecenter (CA) for den `.cloudapp.azure.com` domæne. Du skal anskaffe et brugerdefineret domænenavn til din klynge. Når du anmoder om et certifikat fra et Nøglecenter, skal det certifikat emnenavn svarer til det brugerdefinerede domænenavn, der bruges til din klynge.

### <a name="application-certificates-optional"></a>Programmet Certifikater (valgfrit)

Et vilkårligt antal yderligere certifikater kan være installeret på en klynge af programmet sikkerhedsmæssige årsager. Før du opretter din klynge, skal du overveje de programmet sikkerhed scenarier, der kræver et certifikat skal være installeret på noderne, f.eks.:

 - Kryptering og dekryptering af programmet konfigurationsværdier
 - Kryptering af data på tværs af noder under replikering 

### <a name="formatting-certificates-for-azure-resource-provider-use"></a>Formatering certifikater til Azure ressource udbyder

Privat nøgle filer (.pfx) kan tilføjes og bruges direkte via nøgle samling. Men provideren Azure ressource kræver nøgler skal gemmes i et specielt JSON-format, der indeholder .pfx som en base-64-kodet streng og private nøgler adgangskode. For at imødekomme disse krav, nøgler placeret i en streng, der JSON og derefter gemt som *hemmeligheder* i nøgle samling.

Hvis du vil gøre denne proces lettere, en PowerShell-modulet er [tilgængelig på GitHub][service-fabric-rp-helpers]. Følg disse trin for at anvende modul:

  1. Hente hele indholdet af repo til en lokal mappe. 
  2. Importere modulet i PowerShell-vinduet:

  ```powershell
  PS C:\Users\vturecek> Import-Module "C:\users\vturecek\Documents\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
  ```
     
Den `Invoke-AddCertToKeyVault` kommandoen i denne PowerShell-modulet automatisk formaterer en privat certifikatnøgle til en JSON-streng og overfører det til tasten samling. Bruge det til at føje klynge certifikatet og eventuelle ekstra programmet Certifikater til tasten samling. Gentag dette trin for eventuelle yderligere certifikater, du vil installere i din klynge.

```powershell
 Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName mycluster-keyvault -Location "West US" -VaultName myvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"
    
    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup mycluster-keyvault in West US
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    Using existing valut myvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to myvault in vault myvault
    
    
Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

De foregående strenge er alle de nøgle samling forudsætninger til konfiguration af en tjeneste-strukturen klynge ressourcestyring skabelon, der installeres certifikater til node godkendelse, management slutpunkt sikkerhed og godkendelse og eventuelle ekstra programmet sikkerhedsfunktioner, der bruger x.509-certifikater. Du skal på dette tidspunkt har følgende opsætning nu i Azure:

 - Tasten samling ressourcegruppe
   - Vigtige samling
     - Klynge servergodkendelsescertifikat
     - Programmet Certifikater

## <a name="set-up-azure-active-directory-for-client-authentication"></a>Konfigurere Azure Active Directory til klientgodkendelse

AAD giver organisationer mulighed for (kendt som lejere) til at administrere brugeradgang til programmer, som er opdelt i programmer med et webbaseret login Brugergrænsefladen og programmer med en oprindelig klient oplevelse. I dette dokument antager vi, at du allerede har oprettet en lejer. Hvis ikke, starte ved at læse [Sådan får du en Azure Active Directory-lejer][active-directory-howto-tenant].

En tjeneste-strukturen klynge tilbyder flere genveje til dens management-funktioner, herunder webbaseret [Tjeneste strukturen Explorer] [ service-fabric-visualizing-your-cluster] og [Visual Studio][service-fabric-manage-application-in-visual-studio]. Som et resultat, opretter du to AAD-programmer til at styre adgangen til klyngen, ét webprogram og én oprindelige program.

For at forenkle nogle af trinnene til konfiguration af AAD med en tjeneste-strukturen klynge, har vi oprettet et sæt af Windows PowerShell-scripts.

>[AZURE.NOTE] Du skal udføre disse trin du *før du* opretter klyngen det i tilfælde, hvor scriptene forventer klynge navne og slutpunkter, disse skal være planlagte værdier, ikke dem, du allerede har oprettet.

1. [Hent scriptene] [ sf-aad-ps-script-download] til din computer.

2. Skal du højreklikke på zip-filen, Vælg **Egenskaber**, og derefter markere afkrydsningsfeltet **Fjern blokering** og anvende.

3. Udtrække zip-filen.

4. Køre `SetupApplications.ps1`, give TenantId, ClusterName og WebApplicationReplyUrl som parametre. Eksempel:

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    Du kan finde dit **TenantId** ved at køre PowerShell-kommandoen "Get-AzureSubscription" '. Derved vises **TenantId** for hver abonnement.

    **ClusterName** bruges til at præfiks de AAD-programmer, der er oprettet af scriptet. Det nødvendigt ikke at svare til faktisk klyngenavnet præcis, som det er kun beregnet til at gøre det nemmere for dig at tilknytte AAD elementer til den tjeneste strukturen klynge, de er bruges sammen med.

    **WebApplicationReplyUrl** er standardslutpunktet, der AAD returnerer til brugerne, når du har fuldført processen logon. Du skal indstille til tjenesten strukturen Explorer slutpunkt for din klynge, der som standard er:

    https://&lt;cluster_domain&gt;: 19080/Explorer

    Du bliver bedt om at logge på en konto, der har administratorrettigheder for AAD lejeren. Når du gør, fortsætter scriptet til at oprette internettet og oprindelige programmer til at repræsentere din tjeneste strukturen klynge. Hvis du ser på lejerens programmer på [Azure klassisk portal][azure-classic-portal], skal du se afsnittet to nye poster:

    - *ClusterName*\_klynge
    - *ClusterName*\_klient

    Udskriver script Json kræves af skabelonen Azure Ressourcestyring, når du opretter klyngen i næste afsnit så holde PowerShell åbnes.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Oprette en tjeneste-strukturen klynge ressourcestyring skabelon

I dette afsnit anvendes output fra de foregående PowerShell-kommandoer i en tjeneste-strukturen klynge ressourcestyring skabelon.

Eksempel ressourcestyring skabeloner er tilgængelige i [Azure Hurtig start skabelongalleriet på GitHub][azure-quickstart-templates]. Skabelonerne kan bruges som udgangspunkt for din klynge skabelon. 

### <a name="create-the-resource-manager-template"></a>Oprette skabelonen ressourcestyring

Denne vejledning bruger [5-node sikker klynge] [ service-fabric-secure-cluster-5-node-1-nodetype-wad] eksempelskabelon og Skabelonparametre. Hente `azuredeploy.json` og `azuredeploy.parameters.json` til computeren og åbne begge filer i din foretrukne tekstredigeringsprogram.

### <a name="add-certificates"></a>Tilføj certifikater

Certifikater føjes til en klynge ressourcestyring skabelon ved at referere til den samling af legitimationsoplysninger nøgle, der indeholder tasterne certifikat. Det anbefales, at værdierne nøgle samling er placeret i en ressourcestyring skabelonfil parametre beholde ressourcestyring skabelon fil genanvendelig og fri for værdier, der er specifikke for en installation.

#### <a name="add-all-certificates-to-the-vmss-osprofile"></a>Føje alle certifikater til VMSS osProfile

Hver certifikat, der skal være installeret i klyngen skal konfigureres i sektionen osProfile på ressourcen, VMSS (Microsoft.Compute/virtualMachineScaleSets). Dette giver ressource hos udbyderen for at installere certifikatet på FOS. Dette omfatter klynge certifikat samt eventuelle programmet sikkerhedscertifikater, du vil bruge til dine programmer:

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### <a name="configure-service-fabric-cluster-certificate"></a>Konfigurere tjenesten strukturen klynge certifikat

Klynge godkendelsescertifikat skal også være konfigureret i tjenesten strukturen klyngeressourcen (Microsoft.ServiceFabric/clusters) og i filtypenavnet Service-strukturen for VMSS i VMSS ressourcen. Dette giver mulighed for tjenesten strukturen ressource hos udbyderen for at konfigurere den til brug til klynge godkendelse og server-godkendelse for administration af slutpunkter.

##### <a name="vmss-resource"></a>VMSS ressource:

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### <a name="service-fabric-resource"></a>Tjenesten strukturen ressource:

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="insert-aad-config"></a>Indsætte AAD config

AAD konfigurationen oprettede tidligere kan indsættes direkte i skabelonen Ressourcestyring, men det anbefales at uddrage værdier til parametre først i parameterfilen til at holde skabelonen ressourceleder, der kan genbruges og gratis af værdier bestemt til en installation.

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### < en "konfigurere-arm" ></a>konfigurere ressourcestyring skabelonparametre

Til sidst skal bruge outputværdier fra kommandoerne nøgle samling og AAD PowerShell til at udfylde parameterfilen:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```
På dette tidspunkt, bør du nu har følgende:

 - Tasten samling ressourcegruppe
    - Vigtige samling
    - Klynge servergodkendelsescertifikat
    - Data Kodeomsætning af certifikat
 - Azure Active Directory-lejer 
    - AAD-programmet til webbaseret administration og Service-strukturen Explorer
    - AAD-programmet til oprindelige kundeadministration
    - Brugere med roller, der er tildelt 
 - Tjenesten strukturen klynge ressourcestyring skabelon
    - Certifikater, der er konfigureret gennem nøgle samling
    - Azure Active Directory, der er konfigureret 

I følgende diagram vises, hvor tasten samling og AAD konfiguration passer ind i din ressourcestyring skabelon.

![Ressourcestyring afhængighed kort][cluster-security-arm-dependency-map]

## <a name="create-the-cluster"></a>Oprette klyngen

Du er nu klar til at oprette den klynge ved hjælp af [ARM installation][resource-group-template-deploy].

#### <a name="test-it"></a>Test den

Brug følgende PowerShell-kommandoen til at teste din ressourcestyring-skabelon med en parametre fil:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

#### <a name="deploy-it"></a>Installere det.

Hvis ressourcestyring skabelon test overfører, kan du bruge følgende PowerShell-kommando til at udrulle ressourcestyring skabelonen med en parametre fil:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

<a name="assign-roles"></a>
## <a name="assign-users-to-roles"></a>Tildele brugere roller

Når du har oprettet programmer til at repræsentere din klynge, du vil tildele brugerne til de roller, der understøttes af tjenesten strukturen: skrivebeskyttet og administrator. Du kan gøre dette ved hjælp af [Azure klassisk portal][azure-classic-portal].

1. Gå til din lejer, og vælg programmer.
2. Vælg webprogram, som har et navn som `myTestCluster_Cluster`.
3. Klik på fanen brugere.
4. Vælg en bruger til at tildele og klikke på knappen **tildele** nederst på skærmen.

    ![Tildele brugere til roller knap][assign-users-to-roles-button]

5. Vælg rollen til at tildele til brugeren.

    ![Tildele brugere roller][assign-users-to-roles-dialog]

>[AZURE.NOTE] Du kan finde flere oplysninger om roller i tjenesten strukturen, [Rollebaseret adgangskontrol for tjenesten strukturen klienter](service-fabric-cluster-security-roles.md).

 <a name="secure-linux-cluster"></a> 
##  <a name="create-secure-clusters-on-linux"></a>Oprette secure klynger på Linux

For at gøre processen nemmere, et hjælpeprogram script har fået [her](http://github.com/ChackDan/Service-Fabric/tree/master/Scripts/CertUpload4Linux). Til brug af dette hjælper script, forudsættes det, du allerede har Azure CLI installeret, og det er i din sti. Sørg for, at scriptet har tilladelse til at udføre ved at køre `chmod +x cert_helper.py` efter hente den. Det første trin er at logge på din Azure-konto ved hjælp af CLI med den `azure login` kommandoen. Når du har logget på din Azure-konto, du brug hjælperen sammen med din CA signeret certifikat, som følgende kommando viser:

```sh
./cert_helper.py [-h] CERT_TYPE [-ifile INPUT_CERT_FILE] [-sub SUBSCRIPTION_ID] [-rgname RESOURCE_GROUP_NAME] [-kv KEY_VAULT_NAME] [-sname CERTIFICATE_NAME] [-l LOCATION] [-p PASSWORD]

The -ifile parameter can take a .pfx or a .pem file as input, with the certificate type (pfx or pem, or ss if it is a self-signed cert).
The parameter -h prints out the help text.
```

Denne kommando returnerer følgende tre strenge som output: 

1. En SourceVaultID, som er ID for den nye KeyVault ResourceGroup det oprettet for dig. 

2. En CertificateUrl for at få adgang til certifikatet.

3. En CertificateThumbprint, som bruges til godkendelse.


I følgende eksempel viser, hvordan du kan bruge kommandoen:

```sh
./cert_helper.py pfx -sub "fffffff-ffff-ffff-ffff-ffffffffffff"  -rgname "mykvrg" -kv "mykevname" -ifile "/home/test/cert.pfx" -sname "mycert" -l "East US" -p "pfxtest"
```
Udførelse af foregående kommandoen giver dig de tre strenge på følgende måde:

```sh
SourceVault: /subscriptions/fffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/mykvrg/providers/Microsoft.KeyVault/vaults/mykvname
CertificateUrl: https://myvault.vault.azure.net/secrets/mycert/00000000000000000000000000000000
CertificateThumbprint: 0xfffffffffffffffffffffffffffffffffffffffff
```

 Certifikatets emnenavn skal svare til det domæne, der bruges til at få adgang til tjenesten strukturen klynge. Dette er påkrævet for at give SSL til klyngen HTTPS management slutpunkter og Service-strukturen Explorer. Du kan ikke hente et SSL-certifikat fra et nøglecenter (CA) for den `.cloudapp.azure.com` domæne. Du skal anskaffe et brugerdefineret domænenavn til din klynge. Når du anmoder om et certifikat fra et Nøglecenter skal certifikatets emnenavn til det brugerdefinerede domænenavn, der bruges til din klynge.

Dette er de poster, der er behov for at oprette en sikker tjeneste strukturen klynge (uden AAD), som beskrevet på [konfigurere ressourcestyring Skabelonparametre](#configure-arm). Du kan oprette forbindelse til sikker klynge via vejledningen på [godkendelse Klientadgang til en klynge](service-fabric-connect-to-secure-cluster.md). Linux preview klynger understøtter ikke AAD godkendelse. Du kan tildele admin og klienten roller som beskrevet i afsnittet [tildele roller til brugere](#assign-roles). Når du angiver admin og klienten roller for en Linux preview klynge, er det nødvendigt at levere certifikat thumbprints til godkendelse (i modsætning til emnenavn, da ingen kæde validering eller tilbagekaldelse udføres i denne preview-version).


Hvis du ønsker at bruge et selvsigneret certifikat til test, kan du bruge det samme script til at oprette et selvsigneret certifikat, og Overfør det til KeyVault, ved at indsende flaget `ss` i stedet for at give certifikatstien og certifikatnavnet. Se følgende kommando for at oprette og overføre et selvsigneret certifikat, f.eks.:

```sh
./cert_helper.py ss -rgname "mykvrg" -sub "fffffff-ffff-ffff-ffff-ffffffffffff" -kv "mykevname"   -sname "mycert" -l "East US" -p "selftest" -subj "mytest.eastus.cloudapp.net" 
```

Denne kommando returnerer de samme tre strenge, SourceVault, CertificateUrl og CertificateThumbprint, som bruges til at oprette en sikker Linux-klynge, sammen med den placering, hvor et selvsigneret certifikat er placeret. Du skal bruge et selvsigneret certifikat til at oprette forbindelse til klyngen.  Du kan oprette forbindelse til sikker klynge via vejledningen på [godkendelse Klientadgang til en klynge](service-fabric-connect-to-secure-cluster.md). Certifikatets emnenavn skal svare til det domæne, der bruges til at få adgang til tjenesten strukturen klynge. Dette er påkrævet for at give SSL til klyngen HTTPS management slutpunkter og Service-strukturen Explorer. Du kan ikke hente et SSL-certifikat fra et nøglecenter (CA) for den `.cloudapp.azure.com` domæne. Du skal anskaffe et brugerdefineret domænenavn til din klynge. Når du anmoder om et certifikat fra et Nøglecenter skal certifikatets emnenavn til det brugerdefinerede domænenavn, der bruges til din klynge.

Portalen kan udfyldes de parametre, der leveres af hjælper script, som det er beskrevet i afsnittet [oprette en klynge i portalen Azure](service-fabric-cluster-creation-via-portal.md#create-cluster-portal).

## <a name="next-steps"></a>Næste trin

På dette tidspunkt har du en sikker klynge med Azure Active Directory giver management godkendelse. [Oprette forbindelse til din klynge](service-fabric-connect-to-secure-cluster.md) næste, og Find ud af hvordan du [administrerer programmet hemmeligheder](service-fabric-application-secret-management.md).

## <a name="troubleshoot-setting-up-azure-active-directory-for-client-authentication"></a>Fejlfinding i forbindelse med konfiguration af Azure Active Directory til klientgodkendelse

Hvis der opstår et problem under konfigurationen af Azure Active Directory til klientgodkendelse af på computeren, kan du gennemgå følgende suggestings mulige løsninger.

### <a name="service-fabric-explorer-prompts-for-selecting-certificate"></a>Tjenesten strukturen Explorer beder om at vælge certifikat

#### <a name="problem"></a>Problem

Browseren vender tilbage til startsiden efter logon AAD logonside i tjenesten strukturen Explorer korrekt men beder om en dialogboks for at vælge et certifikat.

![Dialogboksen SFX Vælg certifikat][sfx-select-certificate-dialog]

#### <a name="reason"></a>Årsag

Brugeren blev ikke tildelt en rolle i AAD klynge programmet. AAD-godkendelse mislykkes, og som derfor på Service-strukturen klynge. Tjenesten strukturen Explorer går tilbage til certifikatgodkendelse.

#### <a name="solution"></a>Løsning

Følg vejledningen for at konfigurere AAD og tildele brugerroller. "Bruger tildeling PÅKRÆVET til ACCESS-APP" anbefales også, at være slået til som `SetupApplications.ps1` fungerer.

### <a name="connect-with-powershell-fails-with-error-the-specified-credentials-are-invalid"></a>Oprette forbindelse til PowerShell mislykkes med fejl: de angivne legitimationsoplysninger er ugyldige

#### <a name="problem"></a>Problem

Når bruge PowerShell til at oprette forbindelse til klynge ved hjælp af "AzureActiveDirectory" sikkerheds-tilstand, efter login korrekt på AAD logonside-forbindelse afbrydes med fejl: de angivne legitimationsoplysninger er ugyldige vises.

#### <a name="solution"></a>Løsning

Det samme som ovenfor.

### <a name="service-fabric-explorer-signing-in-return-failure-aadsts50011"></a>Tjenesten strukturen Explorer logge som svar fejl: AADSTS50011

#### <a name="problem"></a>Problem

Efter logon på AAD logonside i tjenesten strukturen Explorer, returnerer siden Log på fejl – AADSTS50011: svar adressen &lt;URL-adressen&gt; ikke svarer til de svar adresser, der er konfigureret for programmet: &lt;guid&gt;. 

![Ikke svarer til SFX svaradresse][sfx-reply-address-not-match]

#### <a name="reason"></a>Årsag

Det cluster(web)-program, der repræsenterer Service-strukturen Explorer forsøg på at godkende mod AAD, som en del af anmodningen kan den URL-adresse, der er vende tilbage til omdirigering. Men det er anført ikke på listen AAD programmet 'Svar URL'.

#### <a name="solution"></a>Løsning

Tilføj URL-adressen til tjenesten strukturen Explorer til 'Svar URL' under fanen Konfigurer i cluster(web) programmet eller erstatte en af elementerne på listen. Gem.

![Programmet svar URL-adresse][web-application-reply-url]

### <a name="can-i-reuse-the-same-aad-tenant-for-multiple-clusters"></a>Kan jeg genbruge den samme AAD lejer til flere klynger?

#### <a name="answer"></a>Svar

Ja. Men husk at føje URL-adressen i tjenesten strukturen Explorer til cluster(web) programmet ellers Service-strukturen Explorer ikke virker.

### <a name="why-do-i-still-need-server-certificate-while-aad-enabled"></a>Hvorfor skal jeg stadig bruge servercertifikat mens AAD aktiveret?

#### <a name="answer"></a>Svar

FabricClient og FabricGateway udfører fælles godkendelse. I tilfælde af AAD godkendelse indeholder AAD integration klient identitet server og servercertifikat bruges til at kontrollere server-id. Du kan finde flere oplysninger om, hvordan certifikat fungerer på Service-strukturen kontrollere [x.509-certifikater og Service-strukturen][x509-certificates-and-service-fabric]

<!-- Links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype-wad]: https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype-wad/
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[assign-users-to-roles-button]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png