<properties
    pageTitle="Reference til at navigere i portalen Azure"
    description="Lære om de forskellige oplevelser for App-tjenesten Web mellem administrationsportalen og Azure-Portal"
    services="app-service"
    documentationCenter=""
    authors="jaime-espinosa"
    manager="wpickett"
    editor="jimbe"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/26/2016"
    ms.author="jaime-espinosa"/>

# <a name="reference-for-navigating-the-azure-portal"></a>Reference til at navigere i portalen Azure

Azure websteder kaldes nu [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714). Vi opdaterer alle vores dokumentation til at afspejle ændringen navn og for at angive instruktioner til portalen Azure. Indtil processen er færdig, kan du bruge dette dokument som en vejledning til at arbejde med Web Apps i portalen Azure.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 
 
## <a name="the-future-of-the-azure-classic-portal"></a>Fremtiden for portalen Azure klassisk

Mens vil du bemærke mærkning ændringerne på portalen Azure klassisk, er portalen ved at blive erstattet af Azure Portal. Som portalen klassisk er ved at blive afviklet, forskyde fokus til udvikling af nye til Azure-portalen. Alle kommende nye funktioner til Web Apps kommer Azure-portalen. Begynde at bruge portalen Azure for at drage fordel af det seneste og bedste, Web Apps har til at tilbyde.

## <a name="layout-differences-between-the-azure-classic-portal-and-azure-portal"></a>Layoutet forskellene mellem Azure klassisk Portal og Azure-portalen

I portalen klassisk vises alle de Azure tjenester i venstre side. Navigation i portalen klassisk følger en træstruktur, hvor du starter fra tjenesten og navigere til hvert element. Denne struktur praktisk, når administration af uafhængige komponenter. Dog programmer, der er bygget på Azure er en samling af forbundne tjenester, og denne træstruktur er ikke velegnet til at arbejde med samlinger af tjenester. 

Portalen Azure gør det nemt at oprette programmer til en komplet med komponenter fra flere tjenester. Portalen er organiseret som *rejser*. En *rejse* er en række *blade*, som er beholdere for de forskellige komponenter. For eksempel konfiguration af automatisk-skalering til en WebApp, er en *rejse* som fører dig flere blade, som vist i følgende eksempel: bladet **websted** (, blade titel ikke er endnu er opdateret for at bruge den nye terminologi), bladet **Indstillinger** , og bladet **Skaler ud** . I eksemplet er automatisk skalering der konfigureret til at er afhængige af CPU'en, så der er også en **CPU procentdel** blade. Komponenterne i *blade* kaldes *dele*, som ligner felter. 

![](./media/app-service-web-app-azure-portal/AutoScaling.png)

## <a name="navigation-example-create-a-web-app"></a>Navigation eksempel: oprette en WebApp

Oprette nye webapps er stadig lige så nemt som 1-2-3. Følgende billede viser klassisk portalen og portalen side om side til at vise, ikke meget er ændret i feltet Antal trin behov for at få en WebApp op og kører. 

![](./media/app-service-web-app-azure-portal/CreateWebApp.png)

Du kan vælge mellem de mest almindelige typer webapps, herunder populære galleriet computerprogrammer som WordPress på portalen. En komplet liste over tilgængelige programmer, skal du besøge [Azure Marketplace].

Når du opretter en web-app, kan du angive URL-adresse, App-serviceaftale og placering i portalen kun som i portalen klassisk. 

![](./media/app-service-web-app-azure-portal/CreateWebAppSettings.png)

Desuden kan portalen du angive andre almindelige indstillinger. For eksempel gør [ressourcegrupper](../azure-resource-manager/resource-group-overview.md) det nemt at få vist og administrere relaterede Azure ressourcer. 

## <a name="navigation-example-settings-and-features"></a>Navigation eksempel: indstillinger og funktioner

Alle indstillinger og funktioner, der nu grupperes logisk i en enkelt blade, hvorfra du kan navigere.

![](./media/app-service-web-app-azure-portal/WebAppSettings.png)

For eksempel kan du oprette brugerdefinerede domæner ved at klikke på **brugerdefinerede domæner og SSL** bladet **Indstillinger** .

![](./media/app-service-web-app-azure-portal/ConfigureWebApp.png)

Hvis du vil konfigurere en overvågning besked, skal du klikke på **anmodninger og fejl** og klik derefter på **Tilføj påmindelse**.

![](./media/app-service-web-app-azure-portal/Monitoring.png)

For at aktivere diagnosticering skal du klikke på **diagnosticering logge** i bladet **Indstillinger** .

![](./media/app-service-web-app-azure-portal/Diagnostics.png)
 
Hvis du vil konfigurere indstillinger for et webprogram, skal du klikke på **Programindstillinger** i bladet **Indstillinger** . 

![](./media/app-service-web-app-azure-portal/AppSettingsPreview.png)

End navnet brand er et par ting i portalen blevet omdøbt eller grupperet anderledes for at gøre det nemmere at finde dem. For eksempel nedenfor vises et skærmbillede af den tilhørende side til app indstillinger (**Konfigurer**) i portalen klassisk.

![](./media/app-service-web-app-azure-portal/AppSettings.png)

## <a name="more-resources"></a>Flere ressourcer

[Azure Portal]: https://portal.azure.com
[Azure Marketplace]: /marketplace/

>[AZURE.NOTE] Hvis du vil komme i gang med Azure App Service før tilmelding til en Azure-konto, skal du gå til [Prøve App Service](http://go.microsoft.com/fwlink/?LinkId=523751), hvor du straks kan oprette en forbigående starter WebApp i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.

## <a name="whats-changed"></a>Hvad er ændret
* Finde en vejledning til ændring fra websteder til App-tjenesten: [Azure App Service og dets indvirkning på eksisterende Azure Services](http://go.microsoft.com/fwlink/?LinkId=529714)
 
