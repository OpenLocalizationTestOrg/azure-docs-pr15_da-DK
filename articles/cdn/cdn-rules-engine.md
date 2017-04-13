<properties
    pageTitle="Tilsidesætte HTTP standardfunktionsmåde i Azure CDN ved hjælp af regler databaseprogram | Microsoft Azure"
    description="Regler program kan du tilpasse, hvordan HTTP-anmodninger skal håndteres ved Azure CDN som blokere leveringen af visse typer indhold, definere en politik for cachelagring og ændre HTTP-headere."
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

# <a name="override-default-http-behavior-using-the-rules-engine"></a>Tilsidesætte standardfunktionsmåde HTTP ved hjælp af regler databaseprogram

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Oversigt

Regler program kan du tilpasse, hvordan HTTP-anmodninger skal håndteres, såsom blokere leveringen af visse typer indhold, definere en politik for cachelagring og ændre HTTP-headere.  Dette selvstudium gennemgås, oprette en regel, der ændrer funktionen cachelagring af CDN aktiver.  Der findes også videoindhold i afsnittet "[Se også](#see-also)".

## <a name="tutorial"></a>Selvstudium

1. Klik på knappen **Administrer** fra bladet CDN profil.

    ![Knappen Administrer CDN profil blade](./media/cdn-rules-engine/cdn-manage-btn.png)

    Portalen CDN management åbnes.

2. Klik på fanen **HTTP store** , efterfulgt af **Regler program**.

    Indstillinger for en ny regel vises.

    ![LEVERANDØR(er) nye regel indstillinger](./media/cdn-rules-engine/cdn-new-rule.png)

    >[AZURE.IMPORTANT] Den rækkefølge, hvori flere regler vises påvirker, hvordan de skal håndteres. En regel for efterfølgende kan tilsidesætte de handlinger, der er angivet af en tidligere regel.
    
3. Angiv et navn i den **navn / beskrivelse** tekstfelt.

4. Identificer typen af anmodninger om reglen skal gælde for.  Betingelsen **altid** match er som standard markeret.  Du skal bruge **altid** til dette selvstudium, så lad, har valgt.

    ![LEVERANDØR(er) match betingelse](./media/cdn-rules-engine/cdn-request-type.png)

    >[AZURE.TIP] Der findes mange typer match betingelser, der er tilgængelige i rullemenuen.  At klikke på det blå informative ikon til venstre for betingelsen match forklares den aktuelt markerede betingelse i detaljer.
    >
    >Se [regler program svarer betingelse og funktion detaljer](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_0)for den komplette liste over match betingelser i detaljer.

5.  Klik på den **+** knappen ved siden af **Funktioner** til at tilføje en ny funktion.  Vælg **Kraft interne Maks-alder**i rullemenuen i venstre side.  I tekstfeltet, der vises, skal du angive **300**.  Lad de resterende standardværdier.

    ![LEVERANDØR(er) funktion](./media/cdn-rules-engine/cdn-new-feature.png)

    >[AZURE.NOTE] Som med Sammenlign betingelser vises klikke på det blå informative ikon til venstre for den nye funktion oplysninger om denne funktion.  Hvis det er **Gældende interne Maks-alder**, vi tilsidesætte aktivets **Cache-Control** og **udløber** overskrifter til at styre, når noden CDN kant opdaterer aktiv fra startpunkt.  Vores eksempel på 300 sekunder betyder noden CDN kant cachelagres aktivet for fem minutter, før opdatere aktiv fra sin oprindelige placering.
    >
    >Se [regler program Match betingelse og funktion detaljer](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_1)for den komplette liste over funktioner i detaljer.

6.  Klik på knappen **Tilføj** for at gemme den nye regel.  Den nye regel er nu afventer godkendelse. Når den er blevet godkendt, ændre status fra **Afventer XML** til **Aktive XML**.

    >[AZURE.IMPORTANT] Regler ændringer kan tage op til 90 minutter at overføre via CDN.

## <a name="see-also"></a>Se også
* [Azure fredage: Azure CDN kraftfulde nye Premium funktioner](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (video)
* [Regler program Match betingelse og funktion detaljer](https://msdn.microsoft.com/library/mt757336.aspx)
