<properties
   pageTitle="Bruge eller-kode med ressourcestyring skabeloner | Microsoft Azure"
   description="Viser, hvordan du konfigurerer Visual Studio-kode til at oprette Azure ressourcestyring skabeloner."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="cmatskas"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/26/2016"
   ms.author="chmatsk;tomfitz"/>

# <a name="working-with-azure-resource-manager-templates-in-visual-studio-code"></a>Arbejde med Azure ressourcestyring skabeloner i Visual Studio-kode

Azure ressourcestyring skabeloner er JSON-filer, der beskriver en ressource og relaterede afhængigheder. Disse filer kan sommetider være stor og kompliceret, så det er vigtigt at værktøjer support. Visual Studio-kode er en ny lette open source-, og på tværs af platforme Kodeeditor. Understøtter oprettelse og redigering af Ressourcestyring skabeloner gennem et [nyt filtypenavn](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). Eller-kode kører overalt og kræver ikke adgang til internettet, medmindre du også vil installere dine ressourcestyring skabeloner.

Hvis du ikke allerede har eller-kode, kan du installere det på [https://code.visualstudio.com/](https://code.visualstudio.com/).

## <a name="install-the-resource-manager-extension"></a>Installere filtypenavnet ressourcestyring

Hvis du vil arbejde med JSON-skabeloner i eller-kode, skal du installere et filtypenavn. Følgende trin downloade og installere understøttelsen af sprog for ressourcestyring JSON skabeloner:

1. Start eller-kode 
2. Åbne hurtig Åbn (Ctrl + P) 
3. Kør følgende kommando: 

        ext install azurerm-vscode-tools

4. Genstart eller-kode, når du bliver bedt om at aktivere filtypenavnet. 

 Jobstatus færdig!

## <a name="set-up-resource-manager-snippets"></a>Konfigurere ressourcestyring kodestykker

De forrige trin installeret understøttelse af værktøj, men vi skal nu konfigurere eller-kode for at bruge JSON skabelon kodestykker.

1. Kopiere indholdet af filerne fra [azure-xplat-arm-værktøj](https://raw.githubusercontent.com/Azure/azure-xplat-arm-tooling/master/VSCode/armsnippets.json) lager til din Udklipsholder.
2. Start eller-kode 
3. Eller-kode, du kan åbne filen JSON kodestykker ved enten navigere til **filen** -> **Indstillinger** -> **Bruger kodestykker** -> **JSON**, eller ved at vælge **F1** og skrive **Indstillinger** , indtil du kan vælge **indstillinger: kodestykker**.

    ![foretrukket kodestykker](./media/resource-manager-vs-code/preferences-snippets.png)

    Indstillinger, Vælg **JSON**.

    ![Vælg json](./media/resource-manager-vs-code/select-json.png)

4. Indsætte indholdet af filen på trin 1 i filen bruger kodestykker før endeligt "}" 
5. Sørg for JSON ser OK, og der er ingen squiggles et vilkårligt sted. 
6. Gem og Luk filen bruger kodestykker.

Det er alt, der er nødvendige for at begynde at bruge ressourcestyring kodestykker. Dernæst skal sætte vi denne konfiguration testen.

## <a name="work-with-template-in-vs-code"></a>Arbejde med skabelon i eller-kode

Den nemmeste måde at begynde at arbejde med en skabelon er enten Tag en hurtig Start skabeloner, som findes på [Github](https://github.com/Azure/azure-quickstart-templates) eller bruge en af dine egne. Du kan nemt [eksportere en skabelon](resource-manager-export-template.md) på grund af dine grupper via portalen. 

1. Hvis du har eksporteret en skabelon fra en ressourcegruppe, kan du åbne de udtrukne filer i eller-kode.

    ![få vist filer](./media/resource-manager-vs-code/show-files.png)

2. Åbn filen template.json, så du kan redigere den og tilføje nogle yderligere ressourcer. Når du har den **"ressourcer": [** Tryk på enter for at starte en ny linje. Hvis du skriver **arm**, får du vist med en liste over indstillinger. Disse indstillinger er de skabelon kodestykker, du har installeret. Det ser sådan ud: 

    ![vise kodestykker](./media/resource-manager-vs-code/type-snippets.png)

3. Vælg den kodestykke, du ønsker. I denne artikel er vælger jeg **arm-IP-adresse** til at oprette en ny offentlige IP-adresse. Placere et komma efter den kantede slutparentes "}" for at sikre, at din skabelon nyoprettede ressourcen syntaks er gyldig.

     ![tilføje komma](./media/resource-manager-vs-code/add-comma.png)

4. Eller-kode har indbyggede IntelliSense. Når du redigerer dine skabeloner, foreslår eller-kode tilgængelige værdier. For eksempel for at tilføje en variabler sektion i skabelonen, tilføje **""** (to dobbelte anførselstegn), og vælg **Ctrl + mellemrum** mellem disse tilbud. Du der præsenteres med indstillinger, herunder **variabler**.

    ![tilføje variabler](./media/resource-manager-vs-code/add-variables.png)

5. IntelliSense kan også foreslå tilgængelige værdier eller funktioner. Oprette et udtryk med **"[]"** og **Ctrl + mellemrum**for at angive en egenskab til en parameterværdi. Du kan begynde at skrive navnet på en funktion. Vælg en **fane** , når du har fundet den ønskede funktion.

    ![tilføje parameter](./media/resource-manager-vs-code/select-parameters.png)

6. Vælg **Ctrl + mellemrum** igen i funktionen til at få vist en liste over de tilgængelige parametre i skabelonen.

    ![tilføje parameter](./media/resource-manager-vs-code/select-avail-parameters.png)

7. Hvis du har en hvilken som helst skema valideringsproblemer i skabelonen, får du vist de velkendte squiggles i editoren. Du kan få vist listen over fejl og advarsler ved at skrive **Ctrl + Skift + M** eller vælge glyfferne i nederste venstre statuslinjen.

    ![fejl](./media/resource-manager-vs-code/errors.png)

    Validering af din skabelon kan hjælpe dig med at registrere syntaksen problemer. Du kan også se fejl, som du kan ignorere. I nogle tilfælde er redigeringsprogrammet til sammenligning af din skabelon mod et skema, der ikke er opdateret, og derfor rapporterer en fejl, selvom du kender den er korrekt. Antag f.eks, at en funktion er senest blevet føjet til Ressourcestyring men skemaet ikke er opdateret. Editoren rapporterer en fejl, selv om funktionen virker korrekt under installationen.

    ![fejlmeddelelse](./media/resource-manager-vs-code/unrecognized-function.png)

## <a name="deploy-your-new-resources"></a>Installere dit nye ressourcer

Når din skabelon er klar, kan du installere de nye ressourcer, følge vejledningen nedenfor: 

### <a name="windows"></a>Windows

1. Åbn en kommandoprompt PowerShell 
2. Til logon type: 

        Login-AzureRmAccount 

3. Hvis du har flere abonnementer, få en liste over abonnementerne med:

        Get-AzureRmSubscription

    Og vælg abonnementet, du bruger.
   
        Select-AzureRmSubscription -SubscriptionId <Subscription Id>

4. Opdatere parametre i filen parameters.json
5. Køre Deploy.ps1 for at installere skabelonen i Azure

### <a name="osxlinux"></a>Alle OSX/Linux

1. Åbne et terminal vindue 
2. Til logon type:

        azure login 

3. Hvis du har flere abonnementer, Vælg det rigtige abonnement med:

        azure account set <subscriptionNameOrId> 

4. Opdater parametre i filen parameters.json.
5. Hvis du vil installere skabelonen, du Kør:

        azure group deployment create -f <PathToTemplate> 

## <a name="next-steps"></a>Næste trin

- Hvis du vil vide mere om skabeloner, se [redigering Azure ressourcestyring skabeloner](resource-group-authoring-templates.md).
- For at få mere for at vide om skabelon funktioner, under [Azure ressourcestyring skabelon funktioner](resource-group-template-functions.md).
- Flere eksempler på arbejde med Visual Studio-kode, kan du se [Build skyen apps med Visual Studio-kode](https://github.com/Microsoft/HealthClinic.biz/wiki/Build-cloud-apps-with-Visual-Studio-Code) fra [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 forbinde [demo](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Du kan finde flere Hurtig start-guider fra HealthClinic.biz demo, [Azure udvikler værktøjer Hurtig start-guider](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).
