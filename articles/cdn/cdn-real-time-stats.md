<properties
    pageTitle="Real-gang-statistik i Azure CDN | Microsoft Azure"
    description="Realtid statistik indeholder data om ydeevne på Azure CDN i realtid, når du fremlægger indhold til dine kunder."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>

# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Realtid statistik i Microsoft Azure CDN

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Oversigt

I dette dokument forklares realtid statistik i Microsoft Azure CDN.  Denne funktion giver realtidsdata som båndbredde, cachen statusser og samtidige forbindelser til din CDN profil ved levering af indhold til dine kunder. Dette gør det muligt for konstant overvågning af tilstanden for din tjeneste når som helst, herunder opstart begivenheder.

De følgende grafer er tilgængelige:

* [Båndbredde](#bandwidth)
* [Status-koder](#status-codes)
* [Statusser, som cache](#cache-statuses)
* [Forbindelser](#connections)


## <a name="accessing-real-time-stats"></a>Få adgang til realtid statistik

1. Gå til din CDN profil i [Azure-portalen](https://portal.azure.com).

    ![LEVERANDØR(er) profil blade](./media/cdn-real-time-stats/cdn-profile-blade.png)

2. Klik på knappen **Administrer** fra bladet CDN profil.

    ![Knappen Administrer CDN profil blade](./media/cdn-real-time-stats/cdn-manage-btn.png)

    Portalen CDN management åbnes.

3. Peg på fanen **analyser** og derefter holde markøren over **Realtid statistik** pop op.  Klik på **http-stort objekt**.

    ![LEVERANDØR(er) management portal](./media/cdn-real-time-stats/cdn-premium-portal.png)

    Realtid statistik grafer vises.
    
Hver af graferne viser realtid statistik for det valgte tidsinterval, starter, når siden indlæses.  Graferne opdatere automatisk hvert par.  Knappen **Opdater Graph** , vil Hvis de findes, nulstille diagrammet, hvorefter den vil kun vise de markerede data.

## <a name="bandwidth"></a>Båndbredde

![Båndbredde graph](./media/cdn-real-time-stats/cdn-bandwidth.png)

**Båndbredde** grafen viser mængden båndbredde, der bruges til den aktuelle platform over det valgte tidsinterval. Den skraverede del af grafen angiver båndbredden. Den nøjagtige mængde båndbredde nuværende vises lige under kurvediagrammet.

## <a name="status-codes"></a>Statuskoder

![Status kode graph](./media/cdn-real-time-stats/cdn-status-codes.png)

Grafen **Statuskoder** angiver, hvor ofte visse HTTP Svarkoder virkelighed over det valgte tidsinterval.

> [AZURE.TIP]  Du kan finde en beskrivelse af hver indstilling for HTTP status kode, [Azure CDN HTTP statuskoder](https://msdn.microsoft.com/library/mt759238.aspx).

Der vises en liste over HTTP statuskoder umiddelbart oven over diagrammet. Denne liste angiver hver statuskode, der skal indgå i kurvediagrammet og det aktuelle antal forekomster sekundet for pågældende statuskode. Som standard, der vises en linje for hver af disse statuskoder i diagrammet. Dog kan du kun overvåge statuskoderne, der har særlig betydning for din CDN konfiguration. Kontrollere ønskede statuskoderne for at gøre dette, og Fjern alle andre indstillinger og derefter skal du klikke på **Opdater Graph**. 

Du kan skjule registrerede data til en bestemt statuskode midlertidigt.  Klik på den statuskode, du vil skjule forklaringen direkte under diagrammet. Statuskoden skjules med det samme fra diagrammet. Klikke på statuskoden igen medfører denne indstilling skal vises igen.

## <a name="cache-statuses"></a>Statusser, som cache

![Cache statusser graph](./media/cdn-real-time-stats/cdn-cache-status.png)

**Statusser, som cachen** grafen angiver, hvor ofte visse typer cache statusser virkelighed over det valgte tidsinterval. 

> [AZURE.TIP]  Du kan finde en beskrivelse af hver indstilling for cache status kode [Azure CDN Cache statuskoder](https://msdn.microsoft.com/library/mt759237.aspx).

Der vises en liste over cache statuskoder umiddelbart oven over diagrammet. Denne liste angiver hver statuskode, der skal indgå i kurvediagrammet og det aktuelle antal forekomster sekundet for pågældende statuskode. Som standard, der vises en linje for hver af disse statuskoder i diagrammet. Dog kan du kun overvåge statuskoderne, der har særlig betydning for din CDN konfiguration. Kontrollere ønskede statuskoderne for at gøre dette, og Fjern alle andre indstillinger og derefter skal du klikke på **Opdater Graph**. 

Du kan skjule registrerede data til en bestemt statuskode midlertidigt.  Klik på den statuskode, du vil skjule forklaringen direkte under diagrammet. Statuskoden skjules med det samme fra diagrammet. Klikke på statuskoden igen medfører denne indstilling skal vises igen.

## <a name="connections"></a>Forbindelser

![Forbindelser graph](./media/cdn-real-time-stats/cdn-connections.png)

Denne graph angiver, hvor mange forbindelser, der er fastsat til edge-servere. Hver anmodning for et aktiv, der passerer gennem vores CDN resultaterne i en forbindelse.

## <a name="next-steps"></a>Næste trin

- Få besked med [alarmer i realtid i Azure CDN](cdn-real-time-alerts.md)
- Gå dybere med [Avancerede HTTP-rapporter](cdn-advanced-http-reports.md)
- Analysere [brugsmønstre](cdn-analyze-usage-patterns.md)

