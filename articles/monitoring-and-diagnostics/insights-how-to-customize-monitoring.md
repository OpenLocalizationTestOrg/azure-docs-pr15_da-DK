<properties
    pageTitle="Oversigt over målepunkter i Microsoft Azure | Microsoft Azure"
    description="Lær, hvordan du tilpasser overvågning diagrammer i Azure."
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2015"
    ms.author="robb"/>

# <a name="overview-of-metrics-in-microsoft-azure"></a>Oversigt over målepunkter i Microsoft Azure

Alle Azure tjenester spore vigtige mål, som gør det muligt at overvåge systemtilstand, ydeevne, tilgængelighed og brugen af dine tjenester. Du kan få vist denne statistik i Azure-portalen, og du kan også bruge [REST-API](https://msdn.microsoft.com/library/azure/dn931930.aspx) eller [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) have programmeringsmæssig adgang det samlede sæt af målepunkter.

For nogle af tjenesterne skal du slå diagnosticering for at kunne se en hvilken som helst målepunkter. Får du et grundlæggende sæt målepunkter for andre, som virtuelle maskiner, men har brug for at aktivere fuldt angive hyppigt målepunkter. Se [aktivere overvågning og diagnosticering](insights-how-to-use-diagnostics.md) til at få mere at vide.

## <a name="using-monitoring-charts"></a>Ved hjælp af overvågning diagrammer

Du kan lave diagrammer nogen af målene dem over en tidsperiode, du vælger.

1. Klik på **Gennemse**, og klik derefter en ressource, du er interesseret i overvågning på [Azure-portalen](https://portal.azure.com/).

2. Afsnittet **overvågnings** indeholder de vigtigste målene for hver Azure ressource. For eksempel en WebApp har **anmodninger og fejl**, hvor som en virtuel maskine ville have **CPU procentdel** og **Disk læse og skrive**:  ![overvågnings lens](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)

3. At klikke på et diagram, vises du bladet **metrisk** . Blade, ud over grafen, er en tabel, der viser sammenlægninger målene (såsom gennemsnit, minimum og maksimum, i det tidsrum, du har valgt). Nedenfor, som er de regler for påmindelser for ressourcen.
    ![Metriske blade](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)

4. Hvis du vil tilpasse de linjer, der vises, skal du klikke på knappen **Rediger** i diagrammet, eller, kommandoen **Rediger diagram** på bladet metriske.

5. Du kan gøre tre ting på bladet Rediger forespørgsel:
    - Ændre tidsintervallet
    - Skifte udseende mellem streg og linje
    - Vælge forskellige metics ![Rediger forespørgsel](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)

6. Ændre tidsintervallet er lige så nemt som at vælge et andet område (såsom **Seneste time**) og klikke på **Gem** i bunden af bladet. Du kan også vælge **brugerdefineret**, som gør det muligt at vælge et tidsrum over de seneste to uger. For eksempel kan du se det hele to uger, eller, lige 1 time i dag. Skriv i tekstfeltet for at angive en anden time.
    ![Brugerdefineret tidsinterval](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)

7. Under tidsrum, du kanal, skal du vælge en hvilken som helst antal målepunkter skal vises i diagrammet.

8. Når du klikker på Gem gemmes dine ændringer for den bestemte ressource. Eksempelvis hvis du har to virtuelle maskiner, og du ændrer et diagram på en, påvirker ikke den anden.

## <a name="creating-side-by-side-charts"></a>Oprettelse af side om side diagrammer

Du kan tilføje lige så mange diagrammer, som du vil have med effektive tilpasningen på portalen.

1. Klik på **Tilføj felter**i menuen **…** øverst i bladet:  
    ![Menuen Tilføj](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. Derefter skal du kan vælge Vælg et diagram fra **galleriet** i højre side af skærmen:  ![galleri](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. Hvis du ikke kan se den metrikværdi, du vil, kan du altid tilføje en af de foruddefinerede målepunkter og **redigere** diagrammet for at få vist den metrikværdi, du skal bruge.

## <a name="monitoring-usage-quotas"></a>Overvåge kvoter for ressourceforbrug

De fleste målepunkter viser tendenser over tid, men visse data, som kvoter for ressourceforbrug, er punkt-in-time oplysninger med en grænseværdi.

Du kan også få vist kvoter for ressourceforbrug på bladet for ressourcer, der har kvoter:

![Brugen](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

Statistik, kan du bruge som [REST-API](https://msdn.microsoft.com/library/azure/dn931963.aspx) eller [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) have programmeringsmæssig adgang det samlede sæt af kvoter for ressourceforbrug.

## <a name="next-steps"></a>Næste trin

* [Modtage beskeder om meddelelser,](insights-receive-alert-notifications.md) hver gang en metrikværdi krydser en grænseværdi.
* [Aktivere overvågning og diagnosticering](insights-how-to-use-diagnostics.md) til at indsamle detaljerede hyppigt målepunkter på din tjeneste.
* [Skalere forekomst Tæl automatisk](insights-how-to-scale.md) at sikre, at din tjeneste er tilgængelig og svarede.
* [Overvåg programmet ydeevne](../application-insights/app-insights-azure-web-apps.md) , hvis du ønsker at forstå præcis hvordan din kode klarer sig i skyen.
* Bruge [Programmet indsigt til JavaScript-apps og websider](../application-insights/app-insights-web-track-usage.md) for at få klienten analytics om de browsere, der åbner en webside.
* [Overvåge tilgængelighed og tilgængeligheden af enhver webside](../application-insights/app-insights-monitor-web-app-availability.md) med programmet viden, så du kan finde ud af, om din side er nede.
