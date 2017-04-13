<properties
    pageTitle="Installere skabeloner med kommandolinjen Azure stablede | Microsoft Azure"
    description="Lær, hvordan du bruger i tværs af platforme linje kommandogrænseflade (CLI) til at installere skabeloner fra i ClientVM eller når du har brugt VPN-Forbindelsen til at oprette forbindelse til Azure stak."
    services="azure-stack"
    documentationCenter=""
    authors="heathl17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="helaw"/>

# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Installere skabeloner Azure stablede ved hjælp af kommandolinjen

Bruge kommandolinjen skal installeres Azure ressourcestyring skabeloner til Azure stak Konceptet. Azure ressourcestyring skabeloner implementere og klargøre alle ressourcerne for dit program i en enkelt, koordineret handling.

## <a name="download-template"></a>Hent skabelon        
Hent filer azuredeploy.json og azuredeploy.parameters.json for at teste en installation med CLI, fra den [oprette lagerplads konto eksempelskabelon](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).

## <a name="deploy-template"></a>Installere skabelon
Naviger til den mappe, hvor filerne er blevet hentet og køre følgende kommando for at installere skabelonen:

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

Denne kommando installerer skabelonen på ressource gruppe **cliRG** på Azure stak Konceptet standardplacering.

## <a name="validate-template-deployment"></a>Valider installation af skabelon
For at se denne ressource gruppe og lager konto skal du bruge følgende kommandoer:

    azure group list

    azure storage account list

## <a name="next-steps"></a>Næste trin

[Administrere brugertilladelser](azure-stack-manage-permissions.md)
