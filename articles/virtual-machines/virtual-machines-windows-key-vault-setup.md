<properties
    pageTitle="Konfigurere nøgle samling til virtuelle maskiner i Azure ressourcestyring | Microsoft Azure"
    description="Hvordan du konfigurerer nøgle samling til brug sammen med en Azure ressourcestyring virtuelt."
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
    ms.date="05/31/2016"
    ms.author="singhkay"/>

# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Konfigurere nøgle samling til virtuelle maskiner i Azure ressourcestyring.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisk implementeringsmodel

Azure ressourcestyring stablede er hemmeligheder/certifikater tilpasset som ressourcer, der leveres af ressource udbyderen af nøgle samling. Du kan få flere oplysninger om nøgle samling, [Hvad er Azure-tasten samling?](../key-vault/key-vault-whatis.md)

>[AZURE.NOTE] 
>
>1. Hvis nøgle samling skal bruges sammen med Azure ressourcestyring virtuelle maskiner, skal egenskaben **EnabledForDeployment** på tasten samling skal være angivet til sand. Du kan gøre dette i forskellige klienter.
>
>2. Den nøgle samling af legitimationsoplysninger skal oprettes i samme abonnement og placering, som den virtuelle maskine.

## <a name="use-powershell-to-set-up-key-vault"></a>Bruge PowerShell til at konfigurere nøgle samling
For at oprette en vigtige samling ved hjælp af PowerShell, skal du se [Introduktion til Azure nøgle samling](../key-vault/key-vault-get-started.md#vault).

Du kan bruge denne PowerShell-cmdlet til nye vigtige vaults:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Du kan bruge denne PowerShell-cmdlet til eksisterende vigtige vaults:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="us-cli-to-set-up-key-vault"></a>OS CLI til konfiguration af nøgle samling
For at oprette en vigtige samling ved hjælp af kommandolinjen (CLI), skal du se [administrere nøgle samling bruger CLI](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault).

CLI har du til at oprette den vigtige samling af legitimationsoplysninger, før du tildele politikken installation. Du kan gøre dette ved hjælp af følgende kommando:

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>Brug skabeloner til at konfigurere nøgle samling
Mens du bruger en skabelon, du vil angive den `enabledForDeployment` egenskab, der skal `true` for ressourcen nøgle samling.

    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

For andre indstillinger, som du kan konfigurere, når du opretter en vigtige samling ved brug af skabeloner, skal du se [oprette en vigtige samling af legitimationsoplysninger](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
