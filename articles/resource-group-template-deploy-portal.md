<properties 
    pageTitle="Bruge Azure portal til at implementere Azure ressourcer | Microsoft Azure" 
    description="Bruge Azure portal og Azure ressource administrere til at installere dine ressourcer." 
    services="azure-resource-manager,azure-portal" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016" 
    ms.author="tomfitz"/>

# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Implementere ressourcer til Ressourcestyring skabeloner og Azure-portalen

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Azure CLI](resource-group-template-deploy-cli.md)
- [Portal](resource-group-template-deploy-portal.md)
- [REST-API](resource-group-template-deploy-rest.md)

Dette emne viser, hvordan du bruger [Azure-portalen](https://portal.azure.com) med [Azure ressourcestyring](azure-resource-manager/resource-group-overview.md) til at installere ressourcerne Azure. Se [administrere Azure ressourcer via portal](./azure-portal/resource-group-portal.md)for at få mere for at vide om administration af dine ressourcer.

På nuværende tidspunkt understøtter ikke hver tjeneste portal eller Ressourcestyring. I disse tjenester skal du bruge [Klassisk portal](https://manage.windowsazure.com). Du kan finde status for hver tjeneste, [Azure portalen tilgængelighed diagram](https://azure.microsoft.com/features/azure-portal/availability/).

## <a name="create-resource-group"></a>Oprette ressourcegruppe

1. Hvis du vil oprette en tom ressourcegruppe, skal du vælge **Ny** > **Management** > **Ressourcegruppe**.

    ![oprette tomme ressourcegruppe](./media/resource-group-template-deploy-portal/create-empty-group.png)

2. Give den et navn og placering, og vælg et abonnement, hvis det er nødvendigt. Du har brug at angive en placering for ressourcegruppen, fordi ressourcegruppen indeholder metadata om ressourcerne. Overholdelse årsager, kan du vil angive, hvor pågældende metadata er gemt. Vi anbefaler Generelt, at du angiver en placering, hvor de fleste af dine ressourcer er placeret. Med den samme placering kan forenkle din skabelon.

    ![Angiv gruppe værdier](./media/resource-group-template-deploy-portal/set-group-properties.png)

## <a name="deploy-resources-from-marketplace"></a>Implementere ressourcer fra Marketplace

Når du opretter en ressourcegruppe, kan du installere ressourcer til den fra Marketplace. På Marketplace indeholder foruddefinerede løsninger til almindelige scenarier.

1. Vælg **Ny** og typen ressourcer, du vil gerne installere for at starte en installation. Derefter skal du søge efter den pågældende version af den ressource, du vil installere.

    ![installere ressource](./media/resource-group-template-deploy-portal/deploy-resource.png)

2. Hvis du ikke kan se den bestemte løsning, du vil installere, kan du søge på Marketplace efter den.

    ![Søg på marketplace](./media/resource-group-template-deploy-portal/search-resource.png)

3. Afhængigt af typen valgte ressource har du en samling af relevante egenskaber for at angive før installation. Disse indstillinger vises ikke her, når de varierer afhængigt af ressourcetype. For alle de typer, skal du vælge en destination ressourcegruppe. Følgende billede viser, hvordan du opretter en WebApp og installerer det til den ressourcegruppe, du har oprettet.

    ![oprette ressourcegruppe](./media/resource-group-template-deploy-portal/select-existing-group.png)

    Alternativt kan du beslutter at oprette en ressourcegruppe, når du installerer dine ressourcer. Vælg **Opret ny** , og navngiv ressourcegruppen.

    ![oprette ny ressourcegruppe](./media/resource-group-template-deploy-portal/select-new-group.png)

4. Begynder med din installation. Installationen kan tage et par minutter. Når installationen er færdig, vises der en meddelelse.

    ![Vis meddelelse](./media/resource-group-template-deploy-portal/view-notification.png)

5. Efter installation af dine ressourcer, kan du føje flere ressourcer til ressourcegruppen ved hjælp af kommandoen **Tilføj** på bladet ressource gruppe.

    ![føje ressource](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Implementere ressourcer fra brugerdefineret skabelon

Hvis du vil udføre en installation, men ikke bruger en af skabelonerne på markedet, kan du oprette en brugerdefineret skabelon, der definerer infrastruktur til din løsning. Se [redigering Azure ressourcestyring skabeloner](resource-group-authoring-templates.md)for at få mere for at vide om at oprette skabeloner.

1. Du skal installere en brugerdefineret skabelon via portalen, skal du vælge **Ny**og begynde at søge efter **Installation af skabelon** , indtil du kan vælge mellem indstillinger.

    ![installation af Søg skabelon](./media/resource-group-template-deploy-portal/search-template.png)

2. Vælg **Skabelonen installation** fra de ressourcer, der er tilgængelige.

    ![Vælg skabelonen installation](./media/resource-group-template-deploy-portal/select-template.png)

3. Efter at åbne skabelon installationen, kan du åbne den tomme skabelon, der er tilgængelige for tilpasning af.

    ![oprette skabelon](./media/resource-group-template-deploy-portal/show-custom-template.png)

    I editoren skal du tilføje JSON syntaks, der definerer de ressourcer, du vil installere. Vælg **Gem** , når du er færdig. Du kan finde vejledning til at skrive JSON syntaksen [ressourcestyring skabelon gennemgang](resource-manager-template-walkthrough.md).

    ![Rediger skabelon](./media/resource-group-template-deploy-portal/edit-template.png)

4. Eller du kan vælge en eksisterende skabelon fra [Azure Hurtig start skabeloner](https://azure.microsoft.com/documentation/templates/). Disse skabeloner er bidraget ved community'et. De dækker mange almindelige scenarier, og nogen har tilføjet en skabelon, der svarer til det, du forsøger at installere. Du kan søge skabeloner for at finde noget, der svarer til det pågældende scenarie.

    ![Vælg Hurtig start skabelon](./media/resource-group-template-deploy-portal/select-quickstart-template.png)

    Du kan få vist den valgte skabelon i editoren.

5. Vælg **Opret** for at installere skabelonen, når du har indtastet alle de andre værdier. 

    ![installere skabelon](./media/resource-group-template-deploy-portal/create-custom-deploy.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>Implementere ressourcer fra en skabelon, der er gemt til din konto

Portalen gør det muligt at gemme en skabelon til kontoen Azure og geninstallere den senere. Du kan finde flere oplysninger om at arbejde med disse gemte skabeloner, [komme i gang med private skabeloner på Azure-portalen](./marketplace-consumer/mytemplates-getstarted.md).

1. Vælg **Gennemse**for at finde dine gemte skabeloner, > **skabeloner**.

    ![Gennemse skabeloner](./media/resource-group-template-deploy-portal/browse-templates.png)

2. Vælg et, du vil arbejde på listen over skabeloner, der er gemt til din konto.

    ![gemte skabeloner](./media/resource-group-template-deploy-portal/saved-templates.png)

3. Vælg **Installer** geninstallere denne gemte skabelon.

    ![installere gemt skabelon](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>Næste trin

- For at få vist overvågningslogge, se [overvåge forskellige handlinger med ressourcestyring](resource-group-audit.md).
- Fejlfinde installation fejl, skal du se [fejlfinding ressource gruppe-installationer med Azure-portalen](resource-manager-troubleshoot-deployments-portal.md).
- For at hente en skabelon fra en installation eller ressourcegruppe skal du se [eksportere Azure ressourcestyring skabelon ud fra eksisterende ressourcer](resource-manager-export-template.md).





