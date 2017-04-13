<properties
    pageTitle="Slip anmærkninger for programmet indsigt | Microsoft Azure"
    description="Tilføje installation, eller Opret datamærker til dine målepunkter explorer diagrammer i programmet indsigt."
    services="application-insights"
    documentationCenter=".net"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/28/2016"
    ms.author="awills"/>

# <a name="release-annotations-in-application-insights"></a>Slip anmærkninger i programmet indsigt

Slip anmærkninger i [Målepunkter Explorer](app-insights-metrics-explorer.md) søjlediagram viser, hvor du har installeret et nyt build. De gør det nemt at se, om dine ændringer havde indflydelse på programmets ydeevne. De kan oprettes automatisk af [Visual Studio Team Services byg system](https://www.visualstudio.com/en-us/get-started/build/build-your-app-vs), og du kan også [oprette dem fra PowerShell](#create-annotations-from-powershell).

![Eksempel på anmærkninger med synlige korrelations med serverens svartid](./media/app-insights-annotations/00.png)

Release anmærkninger er en funktion i Opret skybaseret og slip-tjenesten for Visual Studio Team Services. 

## <a name="install-the-annotations-extension-one-time"></a>Installere filtypenavnet anmærkninger (én gang)

Hvis du vil kunne oprette release anmærkninger, skal du installere et af mange Team Service filtypenavne tilgængelig i Visual Studio-Marketplace.

1. Log på dit [Visual Studio Team Services](https://www.visualstudio.com/en-us/get-started/setup/sign-up-for-visual-studio-online) -projekt.
2. I Visual Studio Marketplace, [får filtypenavnet Release anmærkninger](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations), og tildele den til kontoen Team Services.

![I øverste højre af Team Services webside, Åbn Marketplace. Vælg visuelle Team Services, og derefter vælge se mere under Build og version.](./media/app-insights-annotations/10.png)

Du skal kun gøre det én gang for kontoen Visual Studio Team Services. Release anmærkninger kan nu være konfigureret til et projekt på din konto. 

## <a name="get-an-api-key-from-application-insights"></a>Få en API-nøgle fra programmet indsigt

Du skal gøre dette for hver version skabelon, du vil oprette release anmærkninger.


1. Logge på [Microsoft Azure Portal](https://portal.azure.com) , og Åbn den ressource, programmet viden, overvåger dit program. (Eller [oprette en nu](app-insights-overview.md), hvis du endnu ikke har gjort det).
2. Åbn **API-adgang**, og tage en kopi af **Programmet indsigt Id**.

    ![Åbn din ressource, som programmet indsigt i portal.azure.com, og vælg indstillinger. Åbn API-adgang. Kopiere program-ID](./media/app-insights-annotations/20.png)

2. I et separat browservindue, Åbn (eller Opret) skabelonen udgave, der administrerer din installationer fra Visual Studio Team Services. 

    Føje en opgave, og vælg opgaven, programmet indsigt Release anmærkning i menuen.

    Indsætte **Program-Id** , som du kopierede fra bladet API-adgang.

    ![Åbn version, Vælg en overgang definition, og vælg Rediger i Visual Studio Team Services. Klik på Tilføj opgave, og vælg programmet indsigt Release anmærkning. Indsætte program indsigt-id.](./media/app-insights-annotations/30.png)

3. Angiv **APIKey** felt til en variabel `$(ApiKey)`.

4. Opret en ny API-nøgle tilbage i bladet API-adgang og tage en kopi af filen.

    ![Klik på Opret API-nøgle i bladet API-adgang i vinduet Azure. Giver en kommentar, Markér skrive anmærkninger, og klik på Generer nøgle. Kopiere den nye nøgle.](./media/app-insights-annotations/40.png)

4. Åbn fanen Konfiguration af skabelonen version.

    Oprette en variabel definition for `ApiKey`.

    Indsæt din API-nøgle til ApiKey variable definitionen.

    ![Vælg fanen konfiguration i vinduet Team Services og klikke på Tilføj variabel. Angiv navnet til ApiKey og til værdien skal du indsætte den nøgle, du lige har oprettet.](./media/app-insights-annotations/50.png)


5. Til sidst skal du **gemme** release definitionen.

## <a name="create-annotations-from-powershell"></a>Oprette anmærkninger fra PowerShell

Du kan også oprette anmærkninger fra en hvilken som helst proces, du kan lide (uden at bruge eller-Team System). 

Få [Powershell-script fra GitHub](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

Bruge det sådan ud:

    .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }

Få den `applicationId` og en `apiKey` fra dit program indsigt ressource: Åbn indstillinger, API-adgang og Kopiér program-ID. Klik på Opret API-nøgle og kopiere nøglen. 

## <a name="release-annotations"></a>Slip anmærkninger

Nu, når du bruger skabelonen release til at installere en ny version, sendes en anmærkning til programmet indsigt. Anmærkningerne vises på diagrammer i målepunkter Explorer.

Klik på en hvilken som helst anmærkning markør til at åbne oplysninger om den version, herunder anmoder, kilde kontrolelement gren, slip definition, miljø og meget mere.


![Klik på en hvilken som helst release anmærkning mærke.](./media/app-insights-annotations/60.png)
