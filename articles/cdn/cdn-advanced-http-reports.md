<properties
    pageTitle="Azure CDN avancerede HTTP rapporter | Microsoft Azure"
    description="Avancerede HTTP-rapporter i Microsoft Azure CDN. Disse rapporter indeholder detaljerede oplysninger om CDN aktivitet."
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

# <a name="advanced-http-reports-in-microsoft-azure-cdn"></a>Avancerede HTTP-rapporter i Microsoft Azure CDN

## <a name="overview"></a>Oversigt

I dette dokument forklares avancerede HTTP rapportering i Microsoft Azure CDN. Disse rapporter indeholder detaljerede oplysninger om CDN aktivitet.

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>Få adgang til avancerede HTTP-rapporter

1. Klik på knappen **Administrer** fra bladet CDN profil.

    ![Knappen Administrer CDN profil blade](./media/cdn-advanced-http-reports/cdn-manage-btn.png)

    Portalen CDN management åbnes.

2. Peg på fanen **analyser** og derefter holde markøren over den **Avancerede HTTP rapporter** pop op.  Klik på **http-store Platform**.

    ![LEVERANDØR(er) management portal - menuen avancerede rapporter](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)

    Rapportindstillinger vises.

## <a name="geography-reports-map-based"></a>Geografi rapporter (Kortbaserede)

Der er fem rapporter, der benytter en kortet for at angive de områder, hvor der anmodes dit indhold. Disse rapporter er verdenskort, kort USA, Canada kort, Europe kort og Asien/Stillehavsområdet kort.

Hver kortbaserede rapport rangerer geografiske enheder (det vil sige lande, tilstande og provins) efter procentdelen af søgeresultater, der stammer fra den pågældende region. Desuden er et kort lettere at visualisere de placeringer, hvor der anmodes dit indhold. Det er kunne gøre farvekodning hvert område efter mængden demand erfarne i det pågældende område. Lettere områder med skygge angiver nederste krav om dit indhold, mens mørkere områder angiver højere niveauer af krav om dit indhold.

Detaljerede oplysninger om trafik og båndbredde for hvert område leveres direkte under kortet. Dette kan du få vist det samlede antal søgeresultater, procentdelen af søgeresultater, den samlede mængde af data overføres (i gigabyte), og procentdelen af data, der er overført for hvert område. Få vist en beskrivelse for hvert af disse målepunkter. Til sidst, når du peger på et område (dvs., land, stat eller provins), navn og procentdelen af søgeresultater, der er opstået i området vises som et værktøjstip.

En kort beskrivelse er angivet under for hver kortbaserede Geografi rapporttype.

Rapportnavn | Beskrivelse
------------|------------
Verdenskort | Denne rapport kan du få vist den globale efterspørgsel til CDN indholdet. Hvert land er farvekodede på verdenskort til at angive procentdelen af søgeresultater, der stammer fra den pågældende region.
USA kort | Denne rapport kan du få vist krav om CDN indholdet i USA. Hver enkelt tilstand er farvekodet på denne kortet for at angive procentdelen af søgeresultater, der stammer fra den pågældende region.
Canada-kort | Denne rapport kan du få vist krav om CDN indholdet i Canada. Hver provins farvekodede på denne kortet for at angive procentdelen af søgeresultater, der stammer fra den pågældende region.
Europe kort | Denne rapport kan du få vist krav om CDN indholdet i Europa. Hvert land er farvekodet på denne kortet for at angive procentdelen af søgeresultater, der stammer fra den pågældende region.
Asien Pacific kort | Denne rapport kan du få vist krav om CDN indholdet i Asien. Hvert land er farvekodet på denne kortet for at angive procentdelen af søgeresultater, der stammer fra den pågældende region.

## <a name="geography-reports-bar-charts"></a>Geografi rapporter (liggende søjlediagrammer)

Der findes to yderligere rapporter, som giver statistiske oplysninger i henhold til Geografi, som er toppen byer og toppen lande. Disse rapporter rangere byer og lande, henholdsvis efter antallet af forekomster, der stammer fra disse områder. Ved oprettelse af denne type rapport, angiver et liggende søjlediagram på øverste 10 byer eller lande, der anmodes om indhold over en bestemt platform. Denne søjlediagram kan du hurtigt at vurdere de områder, der opretter det højeste antal anmodninger om dit indhold.

I venstre side af diagrammet (y-aksen) angiver, hvor mange forekomster, der er opstået i det angivne område. Direkte under diagrammet (x-aksen), kan du finde en etiket for hver af de øverste 10 områder.

### <a name="using-the-bar-charts"></a>Ved hjælp af de liggende søjlediagrammer

* Hvis du placerer markøren over en linje, vises navn og det samlede antal søgeresultater, der er opstået i området som et værktøjstip.
* Værktøjstip til rapporten toppen byer identificerer en by ved navn, stat/provins og land forkortelse.
* Hvis ikke der blev fundet by eller område (dvs., stat/provins) som en anmodning om stammer fra, derefter indikerer det, at de er ukendt. Hvis landet er ukendt, og klik derefter to spørgsmålstegn (det vil sige??), vises.
* En rapport kan indeholde måleredskaber for "Europa" eller "Asien/Stillehavsområdet-Region". Disse elementer er ikke beregnet til at levere statistiske oplysninger på alle IP-adresser i disse områder. I stedet gælder de kun for anmodninger, der stammer fra IP-adresser, som er fordelt over Europa eller Asien/Stillehavsområdet i stedet for til en bestemt by eller land.

De data, der blev brugt til at generere det liggende søjlediagram kan ses under den. Der kan du finde det samlede antal søgeresultater, procentdelen af søgeresultater, mængden data overføres (i gigabyte), og procentdelen af data, der er overført for regionerne toppen 250. Få vist en beskrivelse for hvert af disse målepunkter.

En kort beskrivelse er beregnet til begge typer rapporter nedenfor.

Rapportnavn | Beskrivelse
------------|------------
Toppen byer | Denne rapport rangerer byer efter antallet af forekomster, der stammer fra den pågældende region.
Øverste lande | Denne rapport rangerer lande efter antallet af forekomster, der stammer fra den pågældende region.

## <a name="daily-summary"></a>Daglig oversigt

Rapporten daglig oversigt kan du få vist det samlede antal besøg og data, der overføres over en bestemt platform dagligt. Disse oplysninger kan bruges til at skelne hurtigt CDN aktivitet mønstre. Denne rapport kan for eksempel hjælpe dig med at registrere hvilke dage erfarne højere eller lavere end forventet trafik.

Ved oprettelse af denne type rapport, giver et liggende søjlediagram en visuel angivelse som mængden platform-specifikke behov erfarne dagligt over den tidsperiode, der er dækket af rapporten. Det vil gøre det ved at vise en søjle for hver dag i rapporten. For eksempel at vælge tidsperioden kaldet "Sidste uge" genererer et liggende søjlediagram med syv søjler. Hver søjle angiver det samlede antal fundne erfarne på den pågældende dag.

I venstre side af diagrammet (y-aksen) angiver, hvor mange forekomster, der er opstået på den angivne dato. Direkte under diagrammet (x-aksen), kan du finde en etiket, der angiver datoen (Format: åååå-MM-DD) for hver dag, der skal medtages i rapporten.

> [AZURE.TIP] Hvis du placerer markøren over en linje, vises det samlede antal søgeresultater, der er indtruffet på denne dato som et værktøjstip.

De data, der blev brugt til at generere det liggende søjlediagram kan ses under den. Der kan du finde det samlede antal fundne og mængden data, der overføres (i gigabyte) til hver dag, der er dækket af rapporten.

## <a name="by-hour"></a>Time

Rapporten ved time kan du få vist det samlede antal besøg og data, der overføres over en bestemt platform på grundlag af hver time. Disse oplysninger kan bruges til at skelne hurtigt CDN aktivitet mønstre. Denne rapport kan for eksempel hjælpe dig med at registrere de tidsperioder under dag, opleve højere eller lavere end forventet trafik.

Ved oprettelse af denne type rapport, giver et liggende søjlediagram en visuel angivelse som mængden platform-specifikke behov fandt på grundlag af hver time over den tidsperiode, der er dækket af rapporten. Det vil gøre det ved at vise en søjle for hver time, der er dækket af rapporten. For eksempel genererer at vælge en 24-timers tidsperiode et liggende søjlediagram med 20 fire søjler. Hver søjle angiver det samlede antal fundne erfarne under timen.

I venstre side af diagrammet (y-aksen) angiver, hvor mange forekomster, der er opstået på den angivne time. Direkte under diagrammet (x-aksen), kan du finde en etiket, der angiver datatypen Dato/klokkeslæt (Format: åååå-MM-DD TT) for hver time, der skal medtages i rapporten. Tid rapporteres ved hjælp af 24-timers format, og den angives ved hjælp af UTC/GMT tidszone.

> [AZURE.TIP] Hvis du placerer markøren over en linje, vises det samlede antal søgeresultater, der er indtruffet i denne time som et værktøjstip.

De data, der blev brugt til at generere det liggende søjlediagram kan ses under den. Der kan du finde det samlede antal fundne og mængden data, der overføres (i gigabyte) til hver time, der er dækket af rapporten.

## <a name="by-file"></a>Af fil

Rapporten for fil kan du se mængden af behov og trafikken påløbet over en bestemt platform for de mest brugte aktiver. Ved oprettelse af denne type rapport, oprettes et liggende søjlediagram på de øverste 10 mest brugte Aktiver over det angivne tidsrum.

> [AZURE.NOTE] I forbindelse med denne rapport konverteres kant CNAME URL-adresser til deres tilsvarende CDN URL-adresser. Dette giver mulighed for en nøjagtig overensstemmelse mellem til det samlede antal søgeresultater, der er knyttet til et aktiv uanset CDN eller kant CNAME URL-adresse bruges til at anmode om den.

I venstre side af diagrammet (y-aksen) angiver antallet af anmodninger om hver enkelt aktiv over en bestemt tidsperiode. Direkte under diagrammet (x-aksen), kan du finde en etiket, der angiver filnavnet for hver af de øverste 10 anmodede aktiver.

De data, der blev brugt til at generere det liggende søjlediagram kan ses under den. Her finder du følgende oplysninger for hver af de øverste 250 anmodede Aktiver: relativ sti, det samlede antal søgeresultater, procentdelen af søgeresultater, mængden data overføres (i gigabyte), og procentdelen af data overføres.

## <a name="by-file-detail"></a>Ved fil detaljer

Rapporten ved fil detalje kan du se mængden af behov og trafikken påløbet over en bestemt platform for en bestemt aktiv. Er indstillingen fil detaljer For meget øverst i denne rapport. Denne indstilling giver en liste over dine mest brugte Aktiver på den valgte platform. Hvis du vil oprette en rapport ved fil detaljer, skal du vælge den ønskede aktiv fra indstillingen fil detaljer For. Efter hvilket, et liggende søjlediagram vil angive mængden af daglige behov, som den er oprettet i den angivne periode.

I venstre side af diagrammet (y-aksen) angiver det samlede antal anmodninger, støder på et aktiv på en bestemt dato. Direkte under diagrammet (x-aksen), kan du finde en etiket, der angiver datoen (Format: åååå-MM-DD) for hvilke CDN behov for aktivet blev rapporteret.

De data, der blev brugt til at generere det liggende søjlediagram kan ses under den. Der kan du finde det samlede antal fundne og mængden data, der overføres (i gigabyte) til hver dag, der er dækket af rapporten.

## <a name="by-file-type"></a>Efter filtype

Rapporten efter filtype kan du se mængden af behov og trafikken påløbet efter filtype. Ved oprettelse af denne type rapport, angiver et krans diagram procentdelen af søgeresultater, der genereres af de øverste 10 filtyper.

> [AZURE.TIP] Hvis du placerer markøren over et udsnit i kranse diagrammet, medietypen Internet af, at filtype vises som et værktøjstip.

De data, der blev brugt til at oprette diagrammet krans kan ses under den. Her finder du navnet lokalnummer/Internet medier filtypen, det samlede antal søgeresultater, procentdelen af søgeresultater, mængden data overføres (i gigabyte), og procentdelen af data, der er overført for hver af de øverste 250 filtyper.

## <a name="by-directory"></a>Efter mappe

Rapporten ved mappe kan du se mængden af behov og trafikken påløbet over en bestemt platform for indhold fra en bestemt mappe. Ved oprettelse af denne type rapport, angiver et liggende søjlediagram det samlede antal søgeresultater, der genereres af indhold i de øverste 10 kataloger.

### <a name="using-the-bar-chart"></a>Ved hjælp af det liggende søjlediagram

* Hold markøren over en linje til at få vist den relative sti til det tilsvarende bibliotek.
* Indhold, der er gemt i en undermappe til et katalog tæller ikke, når den beregner demand af directory. Denne beregning er afhængig udelukkende af antallet af anmodninger om genereres til indhold, der er gemt i den aktuelle mappe.
* I forbindelse med denne rapport konverteres kant CNAME URL-adresser til deres tilsvarende CDN URL-adresser. Dette giver mulighed for en nøjagtig overensstemmelse mellem for alle statistiske data, der er knyttet til et aktiv uanset CDN eller kant CNAME URL-adresse bruges til at anmode om den.

I venstre side af diagrammet (y-aksen) angiver det samlede antal anmodninger om det indhold, der er gemt i din top 10-mapper. Hver søjle i diagrammet repræsenterer en mappe. Hvis du brug skemaet med farvekodning matche en linje til en mappe, der er angivet i sektionen toppen 250 hele mapper.

De data, der blev brugt til at generere det liggende søjlediagram kan ses under den. Her finder du følgende oplysninger for hver af de øverste 250 mapper: relativ sti, det samlede antal søgeresultater, procentdelen af søgeresultater, mængden data overføres (i gigabyte), og procentdelen af data overføres.

## <a name="by-browser"></a>Om i browseren

Rapporten ved at browseren kan du få vist, hvilke browsere blev brugt til at anmode om indhold. Ved oprettelse af denne type rapport, vil et cirkeldiagram angive procentdelen af anmodninger om håndteret af de øverste 10 browsere.

### <a name="using-the-pie-chart"></a>Brug af cirkeldiagrammet

* Hold markøren over et udsnit i cirkeldiagrammet til at få vist en browser navn og version.
* I forbindelse med denne rapport betragtes hver entydige browserversion kombination som en anden browser.
* Det udsnit, kaldet "Andet" Angiver procentdelen af anmodninger om håndteret af alle andre browsere og versioner.

De data, der blev brugt til at generere cirkeldiagrammet kan ses under den. Der kan du finde browser type/versionsnummeret, det samlede antal fundne og procentdelen af søgeresultaterne for hver af de øverste 250 browsere.

## <a name="by-referrer"></a>Ved Referrer

Rapporten ved Referrer kan du få vist de bedste henvisninger til indhold på den valgte platform. En referrer angiver den hostname, hvorfra en anmodning om blev oprettet. Et liggende søjlediagram vil angive mængden demand (det vil sige besøg) genereres af de øverste 10 henvisninger ved oprettelse af denne type rapport.

I venstre side af diagrammet (y-aksen) angiver det samlede antal anmodninger, støder på et aktiv for hver referrer. Hver søjle i diagrammet repræsenterer en referrer. Hvis du brug skemaet med farvekodning matche en linje til en referrer, der er angivet i sektionen toppen 250 Referrer.

De data, der blev brugt til at generere det liggende søjlediagram kan ses under den. Her finder du URL-adressen, det samlede antal fundne og procentdelen af søgeresultater, der er dannet ud fra hver af de mest populære henvisninger 250.

## <a name="by-download"></a>Som overførsel

Rapporten ved at hente giver dig mulighed at analysere download mønstre til mest brugte indholdet. Toppen af rapporten indeholder et liggende søjlediagram, sammenligner forsøgte overførsler med færdige overførsler for de øverste 10 anmodede aktiver. Hver søjle er farvekodet efter om det er en forsøgte overførsel (blå) eller en færdige overførsel (grøn).

> [AZURE.NOTE] I forbindelse med denne rapport konverteres kant CNAME URL-adresser til deres tilsvarende CDN URL-adresser. Dette giver mulighed for en nøjagtig overensstemmelse mellem for alle statistiske data, der er knyttet til et aktiv uanset CDN eller kant CNAME URL-adresse bruges til at anmode om den.

I venstre side af diagrammet (y-aksen) angiver filnavnet for hver af de øverste 10 anmodede aktiver. Direkte under diagrammet (x-aksen), kan du finde etiketter, der angiver det samlede antal forsøgte/fuldført overførsler.

Direkte under det liggende søjlediagram, følgende oplysninger vises for de øverste 250 anmodede Aktiver: relativ sti (inklusive filnavnet), antallet gange, den blev hentet til afslutning, antallet gange, den blev skrevet og procentdelen af anmodninger, der udløste en komplet overførsel.

> [AZURE.TIP] Vores CDN får ikke besked om ved en HTTP-klient (det vil sige browser) når et aktiv er downloadet helt. Som et resultat, vi har til at beregne om et aktiv er blevet hentet helt afhængigt af status-koder og byte-området anmodninger. Det første, du vi se efter, når du foretager denne beregning er om anmodningen resulterer i en 200 OK statuskode. Hvis det er tilfældet, derefter vi se på byte-området anmodninger om at sikre, at de dækker hele aktivet. Til sidst skal sammenligne vi mængden data, der overføres til størrelsen på den ønskede aktiv. Hvis de data, der er overført er lig med eller større end filstørrelsen og byte-range-anmodninger er relevante for aktivet, der forekomst af tælles som en komplet overførsel.
>
>På grund af af interpretive funktionsmåden i denne rapport, skal du huske på følgende punkter, der kan ændre af konsistens og nøjagtigheden af denne rapport.
>
>* Trafikmønstre kan ikke forudsiges præcist, når brugeragenter, der fungerer anderledes. Dette kan medføre færdige Hent resultater, der er større end 100%.
>* Aktiver, der udnytte HTTP gradvist hente kan ikke være nøjagtigt repræsenteret af denne rapport. Dette er på grund af brugere, der ønsker til forskellige placeringer i en video.

## <a name="by-404-errors"></a>Ved 404-fejl

Rapporten ved 404-fejl, kan du identificere typen indhold, som genererer flest 404 ikke fundet status-koder. Toppen af rapporten indeholder et liggende søjlediagram for de 10 vigtigste aktiver, der blev returneret en 404 ikke fundet statuskode. Denne søjlediagram sammenligner det samlede antal anmodninger med anmodninger, der udløste en 404 ikke fundet statuskode for disse aktiver. Hver søjle er farvekodede. En gul bjælke, der bruges til at angive, at din anmodning udløste en 404 ikke fundet statuskode. En rød bjælke bruges til at angive det samlede antal anmodninger om aktivet.

> [AZURE.NOTE] I forbindelse med denne rapport, du bemærke følgende:
>
>* En forekomst af repræsenterer en anmodning for et aktiv uanset statuskode.
>* Kant CNAME URL-adresser, konverteres til deres tilsvarende CDN URL-adresser. Dette giver mulighed for en nøjagtig overensstemmelse mellem for alle statistiske data, der er knyttet til et aktiv uanset CDN eller kant CNAME URL-adresse bruges til at anmode om den.

I venstre side af diagrammet (y-aksen) angiver filnavnet for hver af de øverste 10 anmodede aktiver, der udløste en 404 ikke fundet statuskode. Direkte under diagrammet (x-aksen), kan du finde etiketter, der angiver det samlede antal anmodninger og antallet af anmodninger, der udløste en 404 ikke fundet statuskode.

Direkte under det liggende søjlediagram, følgende oplysninger vises for de øverste 250 anmodede Aktiver: relativ sti (inklusive filnavnet), antallet af anmodninger, der udløste en 404 ikke fundet statuskode, det samlede antal gange, der blev anmodet om aktivet og procentdelen af anmodninger, der udløste en 404 ikke fundet statuskode.

## <a name="see-also"></a>Se også
* [Azure CDN oversigt](cdn-overview.md)
* [Realtid statistik i Microsoft Azure CDN](cdn-real-time-stats.md)
* [Tilsidesætte standardfunktionsmåde HTTP ved hjælp af regler databaseprogram](cdn-rules-engine.md)
* [Analysere ydeevne](cdn-edge-performance.md)
