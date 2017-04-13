<properties
    pageTitle="Styre Azure CDN cachelagring funktionsmåden for anmodninger om med forespørgselsstrenge | Microsoft Azure"
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

#<a name="controlling-caching-behavior-of-cdn-requests-with-query-strings"></a>Styre cachelagring funktionsmåden for CDN anmodninger med forespørgselsstrenge

> [AZURE.SELECTOR]
- [Standard](cdn-query-string.md)
- [Azure CDN Premium fra Verizon](cdn-query-string-premium.md)

##<a name="overview"></a>Oversigt

Forespørgselsstreng cachelagring af kontrolelementer, hvordan filer er cachelagres, når de indeholder forespørgselsstrenge.

> [AZURE.IMPORTANT] Standard- og Premium CDN produkterne har de samme forespørgselsstreng cachelagring funktionalitet, men brugergrænsefladen varierer.  I dette dokument beskrives grænsefladen til **Azure CDN Standard fra Akamai** og **Azure CDN Standard fra Verizon**.  Til forespørgsel streng cachelagring med **Azure CDN Premium fra Verizon**, oplevede [Controlling cachelagring af CDN anmodninger med forespørgselsstrenge - Premium](cdn-query-string-premium.md).

Der findes tre tilstande:

- **Ignorer forespørgselsstrenge**: Dette er standardtilstanden.  Noden CDN kant overfører forespørgselsstrengen fra person til origin på cache og første anmodningen aktivet.  Alle efterfølgende anmodninger om aktivet, der leveres fra noden kant vil ignorere forespørgselsstrengen, indtil den cachelagrede aktiv udløber.
- **Spring cachelagring af URL-adresse med forespørgselsstrenge**: I denne tilstand anmodninger med forespørgselsstrenge ikke er cachelagret på noden CDN kant.  Noden kant henter aktivet direkte fra startpunkt og sender den til anmoder med hver enkelt anmodning.
- **Cache hver entydige URL-adresse**: denne tilstand behandler hver anmodning med en forespørgsel som et entydigt aktiv med sin egen cache.  For eksempel er svaret fra kilde til en anmodning om *foo.ashx?q=bar* cachelagret på noden kant og returneres til efterfølgende cache med den samme forespørgselsstreng.  En anmodning om *foo.ashx?q=somethingelse* ville være cachelagrede som en separat aktiv med sin egen tid til live.

##<a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Ændre forespørgselsstreng cachelagring af indstillinger for standard CDN profiler

1. Klik på det CDN slutpunkt, du vil administrere bladet CDN profil.

    ![LEVERANDØR(er) profil blade slutpunkter](./media/cdn-query-string/cdn-endpoints.png)

    Bladet CDN slutpunkt åbnes.

2. Klik på knappen **Konfigurer** .

    ![Knappen Administrer CDN profil blade](./media/cdn-query-string/cdn-config-btn.png)

    Bladet CDN konfiguration åbnes.

3. Vælg en indstilling på rullelisten **forespørgselsstreng cachelagring funktionsmåde** .

    ![LEVERANDØR(er) forespørgselsstreng indstillinger for cachelagring](./media/cdn-query-string/cdn-query-string.png)

4. Når du har foretaget dine valg, skal du klikke på knappen **Gem** .

> [AZURE.IMPORTANT] Indstillinger for ændringer er muligvis ikke umiddelbart er synlige, som det tager tid om at overføre via CDN registrering.  For <b>Azure CDN fra Akamai</b> profiler fuldfører overførsel normalt inden for et minut.  For <b>Azure CDN fra Verizon</b> profiler overførsel fuldfører normalt inden for 90 minutter, men i nogle tilfælde kan tage længere tid.
