<properties
    pageTitle="Se begivenheder og overvågningsloggen"
    description="Få mere at vide, hvordan du kan se alle de hændelser, der udføres i abonnementet Azure."
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
    ms.date="04/28/2015"
    ms.author="robb"/>

# <a name="view-events-and-audit-logs"></a>Se begivenheder og overvågningsloggen

Alle handlinger, der udføres på Azure ressourcer overvåges fuldt ved Azure Ressourcestyring, fra oprettelse og sletninger til at give eller tilbagekalde adgang. Du kan gennemse disse logfiler i Azure-portalen, og du kan også bruge [REST-API](https://msdn.microsoft.com/library/azure/dn931927.aspx) eller [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) have programmeringsmæssig adgang det samlede sæt af begivenheder.

## <a name="browse-the-events-impacting-your-azure-subscription"></a>Gennemse de hændelser, som påvirker beskyttelsen abonnementet Azure

1. Log på [Azure-portalen](https://portal.azure.com/).
2. Klik på **Gennemse** , og vælg **overvågningslogge**.  
    ![Gennemse Hub](./media/insights-debugging-with-events/Insights_Browse.png)
3. Dette åbner op en blade, der viser alle de hændelser, der har påvirket et af dine abonnementer til de seneste 7 dage. Øverst er et diagram, der viser data efter niveau og nedenfor, som er den komplette liste over logfiler:  ![alle begivenheder](./media/insights-debugging-with-events/Insights_AllEvents.png)

>[AZURE.NOTE] Du kan kun se de seneste 500 hændelser til et bestemt abonnement på portalen Azure.

4. Du kan klikke på en hvilken som helst post i logfilen at se de hændelser, der gjorde det op. For eksempel, når du installerer noget til en ressourcegruppe, kan mange forskellige ressourcer oprettes eller redigeres. For hvert element kan du se:
    * **Niveau** for hændelsen - eksempelvis det kan være lige noget til at spore (**Information**), eller når noget er gået galt, at du har brug at vide om (**fejl**).
    * **Status** - den endelige status er normalt **lykkedes** eller **mislykkedes**, men det kan også være **accepterede** for længerevarende handlinger.
    * *Når* hændelsen opstod.
    * *Hvem der* har udført handlingen, hvis alle. Ikke alle handlinger er udført af brugere, nogle er udført af back end-tjenester, så de ikke ville have en **opkalds**.
    * **Korrelations-ID'ET** for hændelsen - dette er det entydige id for denne række handlinger.

5. Derfra kan du gå til bladet detaljer for at se detaljerne for begivenheden.

    ![Ressourcegrupper](./media/insights-debugging-with-events/Insights_EventDetails.png)

    For **mislykket** begivenheder indeholder denne side som regel **understatus** og **en egenskabssektion, som indeholder praktiske oplysninger om i forbindelse med fejlfinding** .

## <a name="filter-to-specific-logs"></a>Filtrere efter bestemt logfiler

For at kunne se hændelser, der gælder for et bestemt objekt eller af en bestemt type, kan du filtrere bladet overvågningslog logfiler ved at klikke på kommandoen **Filtrer** . Du kan også bruge bladet Filter til at ændre den **tid dække** af bladet overvågningslog logfiler.

Når du klikker på denne kommando, åbnes en ny blade:

![Filter](./media/insights-debugging-with-events/Insights_EventFilter.png)

Der er fire typer filtre:

1. Ved abonnement
2. Ved en **ressourcegruppe**
3. Ved en **ressourcetype**
4. Af en bestemt **ressource** – i dette skal du tidligere i den fulde *Ressource-ID* for den ressource, du er interesseret i

Desuden kan du også filtrere begivenheder, ved at der udføres begivenheden, eller ved niveauet for begivenheden.

Når du er færdig med at vælge, hvad du vil se, skal du klikke på knappen **Opdater** i bunden af bladet.

## <a name="monitor-events-impacting-specific-resources"></a>Overvåge hændelser, som påvirker beskyttelsen bestemte ressourcer

1. Klik på **Gennemse** for at finde den ressource, du er interesseret i. Du kan også se alle logfiler for en hel **ressourcegruppe**.
2. Rul ned, indtil du finder feltet **begivenheder** på ressourcens blade.  
    ![Hændelser felt](./media/insights-debugging-with-events/Insights_EventsTile.png)
3. Klik på dette felt for at få vist de hændelser, der er filtreret, så kun den ressource, du har valgt. Du kan bruge kommandoen **Filter** til at ændre tidsintervallet eller anvende mere specifikke filtre.

## <a name="next-steps"></a>Næste trin

* [Modtage beskeder om meddelelser,](insights-receive-alert-notifications.md) hver gang der sker en begivenhed.
* [Overvåge tjenesten målepunkter](insights-how-to-customize-monitoring.md) at sikre, at din tjeneste er tilgængelig og svarede.
* [Registrer tjenestetilstand](insights-service-health.md) til at finde ud af, når Azure fik ydeevne er forringet eller tjeneste afbrydelser.  
