<properties
    pageTitle="Installere en VM med et certifikat ved hjælp af Azure stak nøgle samling | Microsoft Azure"
    description="Få mere at vide hvordan implementere en VM og Indsæt et certifikat fra Azure stak nøgle samling"
    services="azure-stack"
    documentationCenter=""
    authors="rlfmendes"
    manager="natmack"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="ricardom"/>

# <a name="create-vms-and-include-certificates-retrieved-from-key-vault"></a>Oprette FOS og medtage certifikater, der er hentet fra nøgle samling

Azure stablede FOS installeres via Azure Ressourcestyring, og du kan nu gemmer certifikater i Azure stak nøgle samling. Derefter flytter Azure stak (Microsoft.Compute ressource provider skal være specifikke) dem til din FOS når FOS er installeret. Certifikater kan bruges i mange situationer, herunder SSL, kryptering og baseret certifikatgodkendelse.

Ved hjælp af denne metode, kan du beskytte certifikatet. Det er nu ikke i VM billedet eller i programmets konfigurationsfiler eller usikker placering. Ved at angive relevante access-politik for den vigtige samling af legitimationsoplysninger, kan du også styre, hvem der får adgang til dit certifikat. En anden fordel er, at du kan administrere dine egne certifikater på ét sted i Azure stak nøgle samling.

Her er en hurtig oversigt over processen:

-   Du skal bruge et certifikat i formatet .pfx.

-   Oprette en samling af vigtige legitimationsoplysninger (ved hjælp af en skabelon eller det følgende eksempelscript).

-   Kontrollér, at du har aktiveret parameteren EnabledForDeployment.

-   Upload certifikatet som et hemmeligt.

## <a name="deploying-vms"></a>Implementere FOS

Dette eksempelscript opretter en vigtige samling, og derefter gemmer et certifikat, der er gemt i .pfx-fil i en lokal mappe til den vigtige samling af legitimationsoplysninger som et hemmeligt.

    $vaultName = "contosovault"
    $resourceGroup = "contosovaultrg"
    $location = "local"
    $secretName = "servicecert"
    $fileName = "keyvault.pfx"
    $certPassword = "abcd1234"

    # =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

    $fileContentBytes = get-content \$fileName -Encoding Byte

    $fileContentEncoded =
    [System.Convert\]::ToBase64String(\$fileContentBytes)
    $jsonObject = @"
    {
    "data": "\$filecontentencoded",
    "dataType" :"pfx",
    "password": "\$certPassword"
    }
    @$jsonObjectBytes = [System.Text.Encoding\]::UTF8.GetBytes(\$jsonObject)
    $jsonEncoded = \[System.Convert\]::ToBase64String(\$jsonObjectBytes)
    Switch-AzureMode -Name AzureResourceManager
    New-AzureResourceGroup -Name \$resourceGroup -Location \$location
    New-AzureKeyVault -VaultName \$vaultName -ResourceGroupName
    $resourceGroup -Location \$location -sku standard -EnabledForDeployment
    $secret = ConvertTo-SecureString -String \$jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName \$vaultName -Name \$secretName -SecretValue \$secret

Den første del af scriptet læser den .pfx-fil og derefter gemmer den som en JSON objekt med den fil indhold base64-kodet. Derefter er JSON objektet også base64-kodet.

Derefter opretter en ny ressourcegruppe og opretter derefter en vigtige samling af legitimationsoplysninger. Bemærk, at den sidste parameter til kommandoen ny AzureKeyVault '-EnabledForDeployment', som giver adgang til Azure (specielt til Microsoft.Compute ressource udbyderen) til at læse hemmeligheder fra nøglen samling til installationer.

Den sidste kommando gemmer blot base64-kodet JSON objekt i den vigtige samling af legitimationsoplysninger som et hemmeligt.

Her kan du se eksempel på output fra det foregående script:

    VERBOSE:  – Created resource group 'contosovaultrg' in
    location
    'eastus'
    ResourceGroupName : contosovaultrg
    Location          : eastus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                        Actions NotActions
                        ======= ==========
                        \*
    ResourceId        :
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-dd149b4aeb56/re
    sourceGroups/contosovaultrg
    VaultUri             : https://contosovault.vault.azure.net
    TenantId             : xxxxxxxx-xxxx-xxxx-xxxx-2d7cd011db47
    TenantName           : xxxxxxxx
    Sku                  : standard
    EnabledForDeployment : True
    AccessPolicies       : {xxxxxxxx-xxxx-xxxx-xxxx-2d7cd011db47}
    AccessPoliciesText   :
                           Tenant ID              :
                           xxxxxxxx-xxxx-xxxx-xxxx-2d7cd011db47
                           Object ID              :
                           xxxxxxxx-xxxx-xxxx-xxxx-b092cebf0c80
                           Application ID         :
                           Display Name           : Derick Developer  (derick@contoso.com)
                           Permissions to Keys    : get, create, delete,
                           list, update, import, backup, restore
                           Permissions to Secrets : all
    OriginalVault        : Microsoft.Azure.Management.KeyVault.Vault
    ResourceId           :
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-dd149b4aeb56                 
    /resourceGroups/contosovaultrg/providers/Microsoft.KeyV
                           ault/vaults/contosovault
    VaultName            : contosovault
    ResourceGroupName    : contosovaultrg
    Location             : eastus
    Tags                 : {}
    TagsTable            :
    SecretValue     : System.Security.SecureString
    SecretValueText :
    ew0KImRhdGEiOiAiTUlJSkN3SUJBekNDQ01jR0NTcUdTSWIzRFFFSEFh
    Q0NDTGdFZ2dpME1JSUlzRENDQmdnR0NTcUdTSWIzRFFFSEFhQ0NCZmtF           
    Z2dYMU1JSUY4VENDQmUwR0N5cUdTSWIzRFFFTUNnRUNvSUlFL2pDQ0JQ
    &lt;&lt;&lt; Output truncated… &gt;&gt;&gt;
    Attributes      :
    Microsoft.Azure.Commands.KeyVault.Models.SecretAttributes
    VaultName       : contosovault
    Name            : servicecert
    Version         : e3391a126b65414f93f6f9806743a1f7
    Id              :
    https://contosovault.vault.azure.net:443/secrets/servicecert
                      /e3391a126b65414f93f6f9806743a1f7

Vi er nu klar til at installere en VM skabelon. Bemærk URI hemmeligt fra output (som fremhævet i den foregående output med grøn).

Du skal bruge en skabelon, der er placeret her. Parametrene for særlig interesse (ud over de sædvanlige VM parametre) er navnet på samling, ressourcegruppe samling af legitimationsoplysninger og hemmeligt URI. Du kan naturligvis også hente det fra GitHub og ændre efter behov.

Når denne VM er installeret, Azure certifikatet fra serveren i VM.
I Windows føjes certifikater i .pfx-fil med den private nøgle ikke eksporteres. Certifikatet, der er føjet til den lokale computer certifikat placering, til den store-certifikat, der bruges af brugeren. På Linux, certifikatfilen er placeret under mappen /var/lib/waagent med filnavnet &lt;UppercaseThumbprint&gt;.crt for X509 certifikatfil, og &lt;UppercaseThumbprint&gt;.prv for den private nøgle.
Begge disse filer er .pem formateret.

Programmet normalt finder certifikatet ved hjælp af miniature og kræver ikke nogen ændring.

## <a name="retiring-certificates"></a>Certifikater på pension


I det forrige afsnit viste vi dig, hvordan til at overføre et nyt certifikat til din eksisterende FOS. Men dit gamle certifikat er stadig i VM og ikke kan fjernes. Du kan ændre attributten for gamle hemmeligt til 'Deaktiveret' sikkerhedsmæssige så selvom en gamle skabelon forsøger at oprette en VM med denne gamle version af certifikat, der. Her er, hvordan du konfigurerer en bestemt hemmeligt version deaktiveres:

    Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0

## <a name="conclusion"></a>Konklusion


Med denne nye metode kan certifikatet holdes adskilt fra VM billede eller et program data. Så vi har fjernet et punkt i visning.

Certifikatet, der kan også fornyet og overføres til tasten samling uden at skulle igen opbygge VM billede eller et program-installationspakken. Programmet skal stadig tilføres med den nye URI til denne nye certifikat version via.

Ved at adskille certifikatet fra VM eller program data, har vi nu reduceret antallet af medarbejdere, der har direkte adgang til certifikatet.

Som en ekstra fordel har du nu ét sted i nøgle samling af legitimationsoplysninger til at administrere alle dine certifikater, herunder de alle de versioner, der blev installeret over tid.

## <a name="next-steps"></a>Næste trin

[Installere en VM med en nøgle samling adgangskode](azure-stack-kv-deploy-vm-with-secret.md)

[Tillad et program tilladelse til at få adgang til tasten samling](azure-stack-kv-sample-app.md)