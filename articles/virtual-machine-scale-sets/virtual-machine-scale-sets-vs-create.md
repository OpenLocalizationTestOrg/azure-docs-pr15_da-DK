<properties
    pageTitle="Installere virtuelt skala sæt ved hjælp af Visual Studio | Microsoft Azure"
    description="Installere virtuelt skala sæt ved hjælp af Visual Studio og en skabelon for ressourcestyring"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/13/2016"
    ms.author="guybo"/>

# <a name="deploy-virtual-machine-scale-set-using-visual-studio"></a>Installere virtuelt skala sæt ved hjælp af Visual Studio

I denne artikel beskrives, hvordan du installerer en Azure virtuelt skala sæt ved hjælp af en Visual Studio ressource gruppe-installation.


[Azure virtuelt skala sæt](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) er en Azure beregne ressource til at installere og administrere en samling af samme virtuelle maskiner med nemt integreret indstillinger for automatisk skalering og justering af belastning. Du kan klargøre og installere VM skala sæt ved hjælp af [Azure ressource Manager (ARM) skabeloner](https://github.com/Azure/azure-quickstart-templates). ARM skabeloner kan installeres ved hjælp af Azure CLI, PowerShell, REST samt direkte fra Visual Studio. Visual Studio indeholder et sæt af eksempel skabeloner, som kan installeres som en del af et projekt til Azure ressource gruppe installation.

Azure ressourcegruppe installationer er en måde at gruppere og publicere et sæt relaterede Azure ressourcer i en enkelt Installationshandling. Du kan få mere at vide om dem her: [oprette og implementere Azure ressourcegrupper via Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="pre-requisites"></a>Forudsætninger

Du skal bruge følgende for at komme i gang implementerer VM skala sæt i Visual Studio:

- Visual Studio 2013 eller 2015
- Azure SDK 2.7, 2,8 eller 2.9

Bemærk: Disse instruktioner forudsætter, du bruger Visual Studio 2015 med [Azure SDK 2,8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="creating-a-project"></a>Oprette et projekt

1. Oprette et nyt projekt i Visual Studio 2015 ved at vælge **fil | Ny | Project**.

    ![Ny i menuen Filer][file_new]

2. Under **Visual C# | Skyen**, Vælg **Azure ressourcestyring** til at oprette et projekt til implementering af en skabelon til ARM.

    ![Oprette et projekt][create_project]

3.  Vælg enten Linux eller Windows virtuelt skala angive skabelon på listen over skabeloner.

    ![Vælg skabelon][select_Template]

4. Når projektet er oprettet får du vist PowerShell-scripts, installation, en skabelon til Azure ressourcestyring og en parameterfil for virtuelt skala angive.

    ![Solution Explorer][solution_explorer]

## <a name="customize-your-project"></a>Tilpasse dit projekt

Du kan nu redigere skabelonen for at tilpasse det til dit program behov, som tilføje VM lokalnummer egenskaber eller redigere regler justering af belastning. Som standard konfigureret VM skala angive skabeloner for at installere filtypenavnet AzureDiagnostics som gør det nemt at tilføje Autoskalering regler. Det også installerer belastningsjustering med en offentlig IP-adresse, konfigureret med indgående NAT regler, som du opretter forbindelse til VM forekomster med SSH (Linux) eller RDP (Windows) – Lad front-end portområdet starter 50000, hvilket betyder, at tale om Linux, hvis du SSH til port 50000 af den offentlige IP-adresse (eller domænenavn) bliver du dirigeret til port 22 første VM i det skala sæt. Oprette forbindelse til port 50001 bliver dirigeret til port 22 af anden VM og så videre.

 En god metode til at redigere dine skabeloner med Visual Studio er at bruge JSON disposition til at organisere parametre, variabler og ressourcer. Med en forståelse af skemaet Peg Visual Studio ud fejl i skabelonen, inden du installerer det.

![JSON Explorer][json_explorer]

## <a name="deploy-the-project"></a>Anvende projektet

6. Installere skabelonen ARM i Azure oprette VM skala angive ressourcen. Højreklik på projektnoden skal du vælge **Implementer | Ny installation**.

    ![Installere skabelon][5deploy_Template]

7. Vælg dit abonnement i dialogboksen "Installere til ressourcegruppe".

    ![Installere skabelon][6deploy_Template]

8. Her kan du også oprette en ny Azure ressourcegruppe for at installere din skabelon til.

    ![Ny ressourcegruppe][new_resource]

9. Næste Vælg knappen **Rediger parametre** til at angive parametre, der vil blive overført til skabelonen, bestemte værdier som brugernavnet og adgangskoden til OS er nødvendige for at oprette installationen. Hvis du ikke har PowerShell værktøjer til Visual Studio, der er installeret, anbefales det at kontrollere "Gem adgangskoder" for at undgå en skjulte PowerShell-kommandolinjen prompt, eller brug [keyvault understøtter](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/).

    ![Rediger parametre][edit_parameters]

10. Klik nu på **Implementer**. Vinduet **Output** vises installationsstatus. Bemærk, at den handlingen udføres **Implementer AzureResourceGroup.ps1** scriptet.

    ![Outputvinduet][output_window]

## <a name="exploring-your-vm-scale-set"></a>Udforske dit VM skala sæt

Når installationen er fuldført, kan du se det nye VM skala sæt i Visual Studio **Skyen Explorer** (opdatere listen). Skyen Explorer kan du administrere Azure ressourcer i Visual Studio mens udviklingsprogrammer. Du kan også få vist dine VM skala angive i [Azure portalen](https://portal.azure.com) og [Azure ressource Explorer](https://resources.azure.com/).

![Skyen Explorer][cloud_explorer]

 Den bedste måde at visuelt at administrere din Azure infrastruktur med en webbrowser, mens Azure ressource Explorer er en nem måde at Stifinder og foretage fejlfinding i Azure ressourcer, giver et vindue til "forekomst visningen" og viser også PowerShell-kommandoer for de ressourcer, du kigger på portalen. Mens VM skala sæt er i preview, viser ressource Explorer de fleste detaljerne for din VM skala sæt.

## <a name="next-steps"></a>Næste trin

Når du har installeret VM skala sæt gennem Visual Studio kan du yderligere tilpasse dit projekt, så den passer til dine krav til programmet. Konfigurere f.eks Autoskalering ved at tilføje en indsigt ressource, tilføje infrastruktur til din skabelon som enkeltstående FOS eller implementerer programmer, der bruger filtypenavnet brugerdefineret script. Et godt sted at eksempel skabeloner kan findes i [Azure Hurtig start skabeloner](https://github.com/Azure/azure-quickstart-templates) GitHub lager (Søg efter "vmss").

[file_new]: ./media/virtual-machine-scale-sets-vs-create/1-FileNew.png
[create_project]: ./media/virtual-machine-scale-sets-vs-create/2-CreateProject.png
[select_Template]: ./media/virtual-machine-scale-sets-vs-create/3b-SelectTemplateLin.png
[solution_explorer]: ./media/virtual-machine-scale-sets-vs-create/4-SolutionExplorer.png
[json_explorer]: ./media/virtual-machine-scale-sets-vs-create/10-JsonExplorer.png
[5deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/5-DeployTemplate.png
[6deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/6-DeployTemplate.png
[new_resource]: ./media/virtual-machine-scale-sets-vs-create/7-NewResourceGroup.png
[edit_parameters]: ./media/virtual-machine-scale-sets-vs-create/8-EditParameter.png
[output_window]: ./media/virtual-machine-scale-sets-vs-create/9-Output.png
[cloud_explorer]: ./media/virtual-machine-scale-sets-vs-create/12-CloudExplorer.png
