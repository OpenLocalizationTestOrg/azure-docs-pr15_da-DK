<properties
    pageTitle="Styre Azure CDN Premium fra Verizon cachelagring funktionsmåden for anmodninger om med forespørgselsstrenge | Microsoft Azure"
    description="Azure CDN-forespørgselsstreng cachelagring af kontrolelementer, hvordan filer er cachelagres, når de indeholder forespørgselsstrenge."
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

#<a name="controlling-caching-behavior-of-cdn-requests-with-query-strings---premium"></a>Styre cachelagring funktionsmåden for CDN anmodninger med forespørgselsstrenge - Premium

> [AZURE.SELECTOR]
- [Standard](cdn-query-string.md)
- [Azure CDN Premium fra Verizon](cdn-query-string-premium.md)

##<a name="overview"></a>Oversigt

Forespørgselsstreng cachelagring af kontrolelementer, hvordan filer er cachelagres, når de indeholder forespørgselsstrenge.

> [AZURE.IMPORTANT] Standard- og Premium CDN produkterne har de samme forespørgselsstreng cachelagring funktionalitet, men brugergrænsefladen varierer.  I dette dokument beskrives grænsefladen til **Azure CDN Premium fra Verizon**.  Til forespørgsel streng cachelagring med **Azure CDN Standard fra Akamai** og **Azure CDN Standard fra Verizon**, oplevede [Controlling cachelagring af CDN anmodninger med forespørgselsstrenge](cdn-query-string.md).

Der findes tre tilstande:

- **standard-cache**: Dette er standardtilstanden.  Noden CDN kant overfører forespørgselsstrengen fra person til origin på cache og første anmodningen aktivet.  Alle efterfølgende anmodninger om aktivet, der leveres fra noden kant vil ignorere forespørgselsstrengen, indtil den cachelagrede aktiv udløber.
- **ikke-cache**: I denne tilstand anmodninger med forespørgselsstrenge ikke er cachelagret på noden CDN kant.  Noden kant henter aktivet direkte fra startpunkt og sender den til anmoder med hver enkelt anmodning.
- **entydige-cache**: denne tilstand behandler hver anmodning med en forespørgsel som et entydigt aktiv med sin egen cache.  For eksempel er svaret fra kilde til en anmodning om *foo.ashx?q=bar* cachelagret på noden kant og returneres til efterfølgende cache med den samme forespørgselsstreng.  En anmodning om *foo.ashx?q=somethingelse* ville være cachelagrede som en separat aktiv med sin egen tid til live.

##<a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Ændre forespørgselsstreng cachelagring af indstillinger for premium CDN profiler

1. Klik på knappen **Administrer** fra bladet CDN profil.

    ![Knappen Administrer CDN profil blade](./media/cdn-query-string-premium/cdn-manage-btn.png)

    Portalen CDN management åbnes.

2. Holde markøren over fanen **HTTP store** , og derefter holde markøren over **Cacheindstillinger** pop op.  Klik på **forespørgselsstreng cachelagring**.

    Indstillinger for forespørgsel streng cachelagring vises.

    ![LEVERANDØR(er) forespørgselsstreng indstillinger for cachelagring](./media/cdn-query-string-premium/cdn-query-string.png)

3. Når du har foretaget dine valg, skal du klikke på knappen **Opdater** .


> [AZURE.IMPORTANT] Indstillinger for ændringer er muligvis ikke umiddelbart er synlige, som det tager tid om at overføre via CDN registrering.  For <b>Azure CDN fra Verizon</b> profiler overførsel fuldfører normalt inden for 90 minutter, men i nogle tilfælde kan tage længere tid.
