<properties
    pageTitle="Introduktion til Azure stak vigtige samling | Microsoft Azure"
    description="Introduktion til brug af Azure stak nøgle samling"
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
    ms.date="10/18/2016"
    ms.author="ricardom"/>


# <a name="getting-started-with-key-vault"></a>Introduktion til tasten samling

I dette afsnit beskrives disse trin for at oprette en samling af legitimationsoplysninger, administrere nøgler og hemmeligheder samt Godkend brugere eller programmer til at aktivere handlinger i samling af legitimationsoplysninger i Azure stak. Følgende trin forudsætter en lejer abonnement findes, og KeyVault tjenesten er registreret i dette abonnement. Alle kommandoerne eksempel er baseret på den KeyVault cmdletter, der er tilgængelige som en del af Azure PowerShell SDK.

## <a name="enabling-the-tenant-subscription-for-vault-operations"></a>Aktivere lejer abonnementet for samling handlinger 

Før du kan udsteder handlinger mod en samling af legitimationsoplysninger, skal du sikre dig, at abonnementet er aktiveret for samling handlinger. Du kan bekræfte, ved at udstede følgende PowerShell-kommando:

    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -AutoSize

Output fra ovenstående kommando skal rapportere "Registreret" til "Registrering" tilstanden for hver række.

    ProviderNamespace RegistrationState ResourceTypes Locations
    Microsoft.KeyVault Registered {operations} {local}
    Microsoft.KeyVault Registered {vaults} {local}
    Microsoft.KeyVault Registered {vaults/secrets} {local}
    

 Hvis, der ikke er tilfældet, skal du aktivere følgende kommando for at registrere KeyVault service inden for dit abonnement:

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault

Og følgende er output fra kommandoen:
    
    ProviderNamespace : Microsoft.KeyVault
    RegistrationState : Registered
    ResourceTypes : {operations, vaults, vaults/secrets}
    Locations : {local}


>[AZURE.NOTE] Hvis du får fejl: "*abonnementet ikke er registreret med Azure nøgle samling*" under aktivering af KeyVault-cmdletter, skal du bekræfte, du har aktiveret provideren KeyVault ressource per vejledningen ovenfor.


## <a name="creating-a-hardened-container-a-vault-in-azure-stack-to-store-and-manage-cryptographic-keys-and-secrets"></a>Oprette en hærdet objektbeholder (en samling) Azure stablede at lagre og administrere cryptographic nøgler og hemmeligheder

Hvis du vil oprette en samling af legitimationsoplysninger, oprette en lejer først en ressourcegruppe. Følgende PowerShell-kommandoer til at oprette en ressourcegruppe og derefter en samling af legitimationsoplysninger i denne ressourcegruppe. Eksemplet indeholder også typisk output fra denne cmdlet.

### <a name="creating-a-resource-group"></a>Oprette en ressourcegruppe:

    New-AzureRmResourceGroup -Name vaultrg010 -Location local -Verbose -Force

Output:

    VERBOSE: Performing the operation "Replacing resource group ..." on target "".
    VERBOSE: 12:52:51 PM - Created resource group 'vaultrg010' in location 'local'
    ResourceGroupName : vaultrg010
    Location : local
    ProvisioningState : Succeeded
    Tags :
    ResourceId : /subscriptions/fa881715-3802-42cc-a54e-a06adf61584d/resourceGroups/vaultrg010
    

### <a name="creating-a-vault"></a>Oprette en samling af legitimationsoplysninger:


    New-AzureRmKeyVault -VaultName vault010 -ResourceGroupName vaultrg010 -Location local -Verbose

Output:

    VaultUri : https://vault010.vault.azurestack.local
    TenantId : 5454420b-2e38-4b9e-8b56-1712d321cf33
    TenantName : 5454420b-2e38-4b9e-8b56-1712d321cf33
    Sku : Standard
    EnabledForDeployment : False
    EnabledForTemplateDeployment : False
    EnabledForDiskEncryption : False
    AccessPolicies : {5454420b-2e38-4b9e-8b56-1712d321cf33}
    AccessPoliciesText :
    Tenant ID : 5454420b-2e38-4b9e-8b56-1712d321cf33
    Object ID : ca342e90-f6aa-435b-a11c-dfe5ef0bfeeb
    Application ID :
    Display Name : Tenant Admin (tenantadmin1@msazurestack.onmicrosoft.com)
    Permissions to Keys : get, create, delete, list, update, import, backup, restore
    Permissions to Secrets : all
    OriginalVault : Microsoft.Azure.Management.KeyVault.Vault
    ResourceId : /subscriptions/fa881715-3802-42cc-a54e-a06adf61584d/resourceGroups/vaultrg010/providers/Microsoft.KeyVault/vaults/vault010
    VaultName : vault010
    ResourceGroupName : vaultrg010
    Location : local
    Tags : {}
    TagsTable :
    
Output fra denne cmdlet viser egenskaberne for den vigtige samling af legitimationsoplysninger, som du lige har oprettet. De to vigtigste egenskaber er:

-   **Samling af legitimationsoplysninger navn**: I eksemplet, er dette **vault010**. Du skal bruge dette navn til andre nøgle samling cmdletter.

-   **Samling af legitimationsoplysninger URI**: I eksemplet, er dette https://vault010.vault.azurestack.local. Programmer, der bruger din samling via dens REST-API skal bruge denne URI.

Azure-konto er nu tilladelse til at udføre handlinger på denne vigtige samling. Ingen andre er endnu.


## <a name="operating-on-keys-and-secrets"></a>Operativsystem på nøgler og hemmeligheder

Når du har oprettet en samling af legitimationsoplysninger, skal du følge den under trin til at oprette administrere nøgler og hemmeligheder:

### <a name="creating-a-key"></a>Oprette en nøgle

Bruge **Tilføj AzureKeyVaultKey** i eksemplet nedenfor for at oprette en nøgle. Efter vellykket oprettelse af nøgle, output cmdlet vil blive sendt de nyoprettede vigtige oplysninger.

    Add-AzureKeyVaultKey -VaultName \$vaultName -Name\$keyVaultKeyName -Verbose -Destination Software

Følgende er output fra *Tilføj AzureKeyVaultKey* cmdlet:

    Attributes : Microsoft.Azure.Commands.KeyVault.Models.KeyAttributes
    Key : {"kid":"https://vault010.vault.azurestack.local/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff","kty":"RSA","key\_ops":\["encrypt"
    ,"decrypt","sign","verify","wrapKey","unwrapKey"\],"n":"nDkcBQCyyLnXtbwFMnXOWzPDqWKiyjf0G3QTxvuN\_MansEn9X-91q4\_WFmRBCd5zWBqz671iuZO\_D4r0P25
    Fe2lAq\_3T1gATVNGR7LTEU9W5h8AoY10bmt4e0y66Jn2vUV-UTCz4\_vtKSKoiuNXHFR\_tGZ-6YX-frqKIiC8pbE4Qvz1x-c7E-eM\_Cpu87koL95n-Hl3wQRQRPXEPRR6gcHR5E74D1
    gLEFCWKySTo4nXtLoeBMNK5QYEBZIAS61ACbR4czjHn6ty-tZeVTc7hyK\_UO2EbJovQIAhyayfq018uNtCBzjjkqJKnY34kviVCPoTQqOdpHa0FHrloe5FeIw","e":"AQAB"}
    VaultName : vault010
    Name : keyVaultKeyName001
    Version : 86062b02b10342688f3b0b3713e343ff
    Id : https://vault010.vault.azurestack.local:443/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff
    
Du kan nu henvise til denne tast, du har oprettet eller overført til Azure-tasten samling ved hjælp af dens URI. Bruge **taster-https://vault010.vault.azurestack.local:443/keyVaultKeyName001** for at få altid den aktuelle version og bruge **https://vault010.vault.azurestack.local:443/taster/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff** for at få denne bestemte version.

### <a name="retrieving-a-key"></a>Hente en nøgle

Bruge **Get-AzureKeyVaultKey** til at hente en nøgle og tilhørende detaljer i eksemplet nedenfor:

    Get-AzureKeyVaultKey -VaultName vault010 -Name keyVaultKeyName001

Følgende er et output fra Get-AzureKeyVaultKey

    Attributes : Microsoft.Azure.Commands.KeyVault.Models.KeyAttributes
    Key : {"kid":"https://vault010.vault.azurestack.local/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff","kty":"RSA","key\_ops":\["encrypt"
    ,"decrypt","sign","verify","wrapKey","unwrapKey"\],"n":"nDkcBQCyyLnXtbwFMnXOWzPDqWKiyjf0G3QTxvuN\_MansEn9X-91q4\_WFmRBCd5zWBqz671iuZO\_D4r0P25
    Fe2lAq\_3T1gATVNGR7LTEU9W5h8AoY10bmt4e0y66Jn2vUV-UTCz4\_vtKSKoiuNXHFR\_tGZ-6YX-frqKIiC8pbE4Qvz1x-c7E-eM\_Cpu87koL95n-Hl3wQRQRPXEPRR6gcHR5E74D1
    gLEFCWKySTo4nXtLoeBMNK5QYEBZIAS61ACbR4czjHn6ty-tZeVTc7hyK\_UO2EbJovQIAhyayfq018uNtCBzjjkqJKnY34kviVCPoTQqOdpHa0FHrloe5FeIw","e":"AQAB"}
    VaultName : vault010
    Name : keyVaultKeyName001
    Version : 86062b02b10342688f3b0b3713e343ff
    Id : https://vault010.vault.azurestack.local:443/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff

### <a name="setting-a-secret"></a>Angive et hemmeligt

    $secretvalue = ConvertTo-SecureString 'User@123' -AsPlainText -Force
    Set-AzureKeyVaultSecret -Name MySecret-VaultName vault010 -SecretValue $secretvalue
    
Output

    Vault Name : vault010
    Name : MySecret
    Version : 65a387f2ed4a416180e852b970846f5b
    Id : https://vault010.vault.azurestack.local:443/secrets/MySecret/65a387f2ed4a416180e852b970846f5b
    Enabled : True
    Expires :
    Not Before :
    Created : 8/17/2016 10:49:52 PM
    Updated : 8/17/2016 10:49:52 PM
    Content Type :
    Tags : 

### <a name="retrieving-a-secret"></a>Hente et hemmeligt

    Get-AzureKeyVaultSecret -VaultName vault010

Output

    Vault Name : vault010
    Name : MySecret
    Version :
    Id : https://vault010.vault.azurestack.local:443/secrets/MySecret
    Enabled : True
    Expires :
    Not Before :
    Created : 8/17/2016 10:49:52 PM
    Updated : 8/17/2016 10:49:52 PM
    Content Type :
    Tags :

Nu er din nøgle samling og nøgle eller hemmeligt er klar til programmer til brug.
Du skal Godkend programmer til at bruge dem.

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Tillade, at du skal bruge tasten eller hemmeligt 

For at godkende program tilladelse til at få adgang til den tast eller hemmeligt i samling af legitimationsoplysninger, skal du bruge cmdlet'en Set -**AzureRmKeyVaultAccessPolicy** til.

Eksempelvis hvis navnet på din samling af legitimationsoplysninger er *ContosoKeyVault* og det program, du vil give tilladelse har en *Klient-ID* for *8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed*, og du vil tillade, at du kan dekryptere og tilmelde taster i din samling, Kør følgende:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign

Hvis du vil tillade, at den samme program til at læse hemmeligheder i din samling, skal du køre følgende:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get


## <a name="next-steps"></a>Næste trin
[Installere en VM med en nøgle samling adgangskode](azure-stack-kv-deploy-vm-with-secret.md)

[Installere en VM med en nøgle samling certifikat](azure-stack-kv-push-secret-into-vm.md)