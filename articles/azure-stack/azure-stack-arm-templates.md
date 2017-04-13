<properties
    pageTitle="Bruge Azure ressourcestyring skabeloner i Azure stak (lejer udviklere) | Microsoft Azure"
    description="Lær at bruge Azure ressourcestyring skabeloner Azure stablede at implementere og klargøre alle ressourcerne for dit program i en enkelt, koordineret handling."
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
    ms.date="10/25/2016"
    ms.author="helaw"/>

# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Bruge Azure ressourcestyring skabeloner i Azure stak

Azure ressourcestyring skabeloner implementere og klargøre alle ressourcerne for dit program i en enkelt, koordineret handling. Du definerer ressourcer til programmet, og hvordan den installeres.  Du kan også Geninstaller skabeloner for at foretage ændringer til ressourcer i ressourcegruppen.

Skabelonerne kan også anvendes sammen med Microsoft Azure stak portalen, PowerShell, kommandolinjen og Visual Studio.

[AZURE.VIDEO microsoft-azure-stack-tp1--foundational-skills-1-deploying-json-templates]

Følgende skabeloner er tilgængelige på [GitHub](http://aka.ms/azurestackgithub):

## <a name="deploy-sharepoint-non-high-availability"></a>Installere SharePoint (ikke høj tilgængelighed)

Bruge PowerShell DTK udvidelse til at oprette en SharePoint 2013-farm, der omfatter følgende:

-   Et virtuelt netværk

-   Tre lagerplads konti

-   To eksterne Indlæs balancere

-   Én VM konfigureret som et domænenavn fra domænecontrolleren til en ny skov med et enkelt domæne

-   Én VM konfigureret som en separat SQL Server-2014-server

-   Én VM konfigureret som en én maskine SharePoint 2013-farm

## <a name="deploy-ad-non-high-availability"></a>Installere AD (ikke høj tilgængelighed)

Bruge PowerShell DTK udvidelse til at oprette en AD domain controllerserver, der omfatter følgende:

-   Et virtuelt netværk

-   Én lagerplads konto

-   En ekstern justering af belastning

-   Én VM konfigureret som et domænenavn fra domænecontrolleren til en ny skov med et enkelt domæne

## <a name="deploy-adsql-non-high-availability"></a>Installere AD/SQL (ikke høj tilgængelighed)

Bruge PowerShell DTK udvidelse til at oprette en SQL Server 2014 enkeltstående server, der omfatter følgende:

-   Et virtuelt netværk

-   To lagerplads konti

-   En ekstern justering af belastning

-   Én VM konfigureret som et domænenavn fra domænecontrolleren til en ny skov med et enkelt domæne

-   Én VM konfigureret som en separat SQL Server-2014-server

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

Bruge PowerShell DTK udvidelse til at konfigurere en eksisterende virtuelt lokale Configuration Manager (mindste.fælles.MULTIPLUM) og registrere den til en Azure Automation konto DTK adskille-serveren.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Oprette en virtuel maskine fra en bruger billede

Oprette en virtuel maskine fra et brugerdefinerede billede. Denne skabelon installerer også et virtuelt netværk (med DNS), offentlige IP-adresse og en netværksgrænseflade.

## <a name="simple-vm"></a>Enkel VM

Installere en enkelt Windows VM, der indeholder et virtuelt netværk (med DNS), offentlige IP-adresse og en netværksgrænseflade.

## <a name="cancel-a-running-template-deployment"></a>Annullere en igangværende installation af skabelon

Hvis du vil annullere en igangværende installation af skabelon, skal du bruge den `Stop-AzureRmResourceGroupDeployment` PowerShell-cmdlet.


## <a name="next-steps"></a>Næste trin

[Installere skabeloner ved hjælp af portal](azure-stack-deploy-template-portal.md)

[Oversigt over Azure ressourcestyring](../azure-resource-manager/resource-group-overview.md)

