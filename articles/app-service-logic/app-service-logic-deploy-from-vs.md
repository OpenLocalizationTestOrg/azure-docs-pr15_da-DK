<properties 
    pageTitle="Opbygge logik Apps i Visual Studio | Microsoft Azure" 
    description="Oprette et projekt i Visual Studio til at oprette og installere din logik app." 
    authors="jeffhollan" 
    manager="erikre" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jehollan"/> 
    
# <a name="build-and-deploy-logic-apps-in-visual-studio"></a>Opbygge og anvende logik Apps i Visual Studio

Selvom [Azure Portal](https://portal.azure.com/) giver dig en god måde at designe og administrere dine logik apps, kan du også vil til at designe og implementere logik appen fra Visual Studio i stedet.  Logik Apps leveres med en omfattende Visual Studio-værktøjer, så du kan oprette en logik app ved hjælp af designer, konfigurere nogen installation og automatisering skabeloner og installere i en hvilken som helst miljø.  

## <a name="installation-steps"></a>Installationstrin

Herunder finder du disse trin for at installere og konfigurere Visual Studio-værktøjerne til logik Apps.

### <a name="prerequisites"></a>Forudsætninger

- [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- [Seneste Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 eller nyere)
- [Azure PowerShell] (https://github.com/Azure/azure-powershell#installation)
- Adgang til internettet ved hjælp af integreret designer

### <a name="install-visual-studio-tools-for-logic-apps"></a>Installere Visual Studio-værktøjer til logik Apps

Når du har fået de forudsætninger, der er installeret, 

1. Åbn Visual Studio-2015 i menuen **Funktioner** , og vælg **Extensions og opdateringer**
1. Vælg kategorien **Online** til at søge online
1. Søge efter **Logik Apps** at få vist **Azure logik Apps Tools til Visual Studio**
1. Klik på knappen **Hent** for at hente og installere filtypenavnet
1. Genstart Visual Studio efter installationen

> [AZURE.NOTE] Du kan også hente filtypenavnet direkte fra [dette link](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9)

Når de er installeret du vil kunne bruge Azure ressourcegruppe projektet med logik App Designer.

## <a name="create-a-project"></a>Oprette et projekt

1. Gå til menuen **filer** , og vælg **Ny** >  **Project** (eller du kan gå til **Tilføj** og derefter vælge **nyt projekt** for at føje den til en eksisterende løsning):  ![menuen Filer](./media/app-service-logic-deploy-from-vs/filemenu.png)

1. Find **skyen**i dialogboksen, og vælg derefter **Azure ressourcegruppe**. Skriv et **navn** , og klik derefter på **OK**.
    ![Tilføje nyt projekt](./media/app-service-logic-deploy-from-vs/addnewproject.png)

1. Vælg **logik app** -skabelonen. Dette vil oprette en tom logik app installation skabelon skal starte med.
    ![Vælg Azure skabelon](./media/app-service-logic-deploy-from-vs/selectazuretemplate.png)

1. Når du har markeret din **skabelon**, klik på **OK**.

    Nu er projektet logik app føjet til din løsning. Du burde se installationsfilen i Solution Explorer:  

    ![Installation](./media/app-service-logic-deploy-from-vs/deployment.png)

## <a name="using-the-logic-app-designer"></a>Brug af logik App Designer

Når du har en Azure ressourcegruppe projekt, der indeholder en logik app, kan du åbne designer i Visual Studio til at hjælpe dig med at oprette arbejdsprocessen.  Designer kræver forbindelse til internettet for at forespørge forbindelser til tilgængelige egenskaber og -data (for eksempel hvis ved hjælp af Dynamics CRM Online connector designer beder din CRM forekomst for at få vist tilgængelige brugerdefineret og standardegenskaber).

1. Højreklik på den `<template>.json` filen og vælge **Åbn med logik App Designer** (eller `Ctrl+L`)
1. Vælg det abonnement, ressourcegruppe og placering for skabelonen installation
    - Det er vigtigt at være opmærksom på, at designe en logik app opretter **API forbindelse** ressourcer til at forespørge om egenskaber under design.  Ressourcegruppen, der er markeret, vil være ressourcegruppen bruges til at oprette disse forbindelser i design-periode.  Du kan få vist eller ændre en hvilken som helst API forbindelser ved at gå til portalen Azure og søge efter **API forbindelser**.
    ![Abonnement datovælger](./media/app-service-logic-deploy-from-vs/designer_picker.png)
1. Designer skal gengive baseret på definitionen i den `<template>.json` fil.
1. Du kan nu oprette og designe din logik app, og ændringerne bliver opdateret i skabelonen installation.
    ![Designer i Visual Studio](./media/app-service-logic-deploy-from-vs/designer_in_vs.png)

Du kan også få vist `Microsoft.Web/connections` ressourcer, der føjes til ressourcefilen for alle forbindelser, der kræves for appen logik til funktionen.  Disse egenskaber for forbindelse kan angive, når du installerer og administreres når du installerer i **API forbindelser** i portalen Azure.

### <a name="switching-to-the-json-code-view"></a>Skifte til JSON-kodevisning

Du kan vælge fanen **Kodevisning** i bunden af designer til at skifte til JSON repræsentationen af appen logik.  Hvis du vil skifte tilbage til fuld ressourcen JSON, skal du højreklikke på den `<template>.json` filen og vælge **Åbn**.

### <a name="saving-the-logic-app"></a>Gemme appen logik

Du kan gemme logik app på når som helst via knappen **Gem** eller `Ctrl+S`.  Hvis der er fejl med din logik app på det tidspunkt du gemmer, vises de i vinduet **output** i Visual Studio.

## <a name="deploying-your-logic-app"></a>Implementering af din app logik

Til sidst, når du har konfigureret din app, kan du installere direkte fra Visual Studio i et par trin. 

1. Højreklik på projektet i Solution Explorer, og gå til **Implementer** > **Ny installation...** 
     ![Ny installation](./media/app-service-logic-deploy-from-vs/newdeployment.png)

2. Du bliver bedt om at logge på dine Azure abonnementer. 

3. Nu skal du vælge detaljerne for den ressourcegruppe, du vil installere appen logik til. 
    ![Installere på ressourcegruppe](./media/app-service-logic-deploy-from-vs/deploytoresourcegroup.png)

     > [AZURE.NOTE]    Sørg for at vælge de rigtige skabelon og parametre filer til ressourcegruppen (for eksempel hvis du installerer til et produktionsmiljø skal du vælge filen fremstilling parametre). 
4. Vælg knappen Implementer
 
    
6. Status for installationen vises i vinduet **Output** (du skal muligvis vælge **Azure klargøring**. 
    ![Output](./media/app-service-logic-deploy-from-vs/output.png)

Du kan i fremtiden, ændrer din logik app i versionsstyring og bruge Visual Studio til at installere nye versioner. 

> [AZURE.NOTE] Hvis du ændrer definitionen på portalen Azure direkte, overskrives næste gang du installerer fra Visual Studio disse ændringer.

## <a name="next-steps"></a>Næste trin

- For at komme i gang med logik Apps skal du følge selvstudiet [oprette en logik App](app-service-logic-create-a-logic-app.md) .  
- [Eksempler på almindelige og -scenarier](app-service-logic-examples-and-scenarios.md)
- [Du kan automatisere forretningsprocesser med logik Apps](http://channel9.msdn.com/Events/Build/2016/T694) 
- [Lær at integrere dine systemer med logik Apps](http://channel9.msdn.com/Events/Build/2016/P462)