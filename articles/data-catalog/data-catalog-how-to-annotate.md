<properties
   pageTitle="Sådan anmærke datakilder | Microsoft Azure"
   description="Vejledning til artikel fremhævning Sådan anmærke dataaktiver i Azure datakatalog, herunder kaldenavne, mærker, beskrivelser og eksperter."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/21/2016"
   ms.author="maroche"/>


# <a name="how-to-annotate-data-sources"></a>Sådan anmærke datakilder

## <a name="introduction"></a>Introduktion
**Microsoft Azure datakatalog** er en fuldt administreret skybaseret tjeneste, der fungerer som et system af registrering og system af registrering til enterprise-datakilder. Med andre ord handler datakatalog om hjælpe personer opdage, forstå og bruge datakilder, og hjælp organisationer til at få flere værdi fra deres eksisterende data. Når en datakilde er registreret med datakatalog, dens metadata er kopieret og indekseret af tjenesten, men tekstenheden afslutte ikke der. Datakatalog kan brugerne angive deres egne beskrivende metadata – som beskrivelser og mærker – som supplement til de metadata, der er hentet fra datakilden og for at få datakilden mere forstå til flere personer.

## <a name="annotation-and-crowdsourcing"></a>Anmærkninger og crowdsourcing
Alle har en udtalelse. Og det er godt.
Datakatalog genkender, forskellige brugere har forskellige perspektiver på virksomhedens datakilder, og at hver af disse perspektiver kan være værdifulde. Forestil dig følgende:

* Systemadministratoren kender serviceniveauaftale for servere eller tjenester, der er vært datakilden.
* Databaseadministratoren kender tidsplanen for sikkerhedskopiering for hver database og vinduerne tilladte ETL behandling.
* Ejeren af system ved processen for brugerne at anmode om adgang til datakilden.
* Databehandler ved, hvordan aktiver og attributter i datakilden knyttes til enterprise-datamodel.
* Analytikeren ved, hvordan dataene skal bruges i forbindelse med forretningsprocesserne han understøtter.

Hver af disse perspektiver er værdifulde og datakatalog bruger en crowdsourcing tilgang til metadata, der gør det muligt for hver enkelt til registreres og bruges til at give et komplet billede over registrerede datakilder. Ved hjælp af portalen datakatalog kan hver bruger tilføje og redigere eget anmærkninger, samtidig med at få vist anmærkninger, der leveres af andre brugere.

## <a name="different-types-of-annotations"></a>Forskellige typer anmærkninger
Datakatalog understøtter følgende typer af anmærkninger:

| Anmærkning     | Noter                                                                                                                                                                                                                                                                                                                                                           |
|----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Fulde navn  | Kaldenavne kan leveres på det dataniveau aktiv at gøre de lettere at forstå over dataaktiver. Fulde navn er mest nyttige, når den underliggende objektnavn er kryptisk, forkortet eller ellers ikke giver mening for brugere.                                                                                                                            |
| Beskrivelse    | Beskrivelser kan levere data aktiv og attribut / kolonne niveauer. Beskrivelser er ledig formular kort tekst anmærkninger, der beskriver brugerens perspektiv på data aktiv eller dens brug.                                                                                                                                                              |
| Mærker (bruger mærker)          | Mærker kan levere data aktiv og attribut / kolonne niveauer. Bruger mærker er brugerdefinerede etiketter, der kan bruges til at kategorisere dataaktiver eller attributter.                                                                                                                                                                                                    |
| Mærker (ordliste mærker)          | Mærker kan levere data aktiv og attribut / kolonne niveauer. Ordliste mærker er centralt defineret ordliste ord, der kan bruges til at kategorisere dataaktiver eller attributter ved hjælp af en fælles business taksonomi. Se, [hvordan du konfigurerer Business Ordliste til omfattet mærkning](data-catalog-how-to-business-glossary.md) kan finde flere oplysninger                                                                                                                                                                                                    |
| Eksperter        | Eksperter kan leveres på niveauet for aktiv. Eksperter kan identificere brugere eller grupper med expert perspektiver på dataene og fungere som peger på Kontakt for brugere, der registrere de registrerede datakilder og har spørgsmål, som ikke besvares ved de eksisterende anmærkninger.  |
| Anmode om adgang | Anmodning om adgang til oplysninger kan leveres på niveauet for aktiv. Disse oplysninger er for brugere, der opleve en datakilde, som de endnu ikke har tilladelse til at få adgang til. Brugerne kan angive mailadressen på den bruger eller gruppe, der giver adgang, URL-adressen til den proces eller det værktøj, som brugerne skal have adgang, eller kan angive processen selve som tekst. |
| Dokumentation | Dokumentation kan leveres på niveauet for aktiv. Aktiver dokumentation er RTF oplysninger, der kan indeholde links og billeder, og som kan give oplysninger, der ikke overføres via beskrivelser og mærker. |


## <a name="annotating-multiple-assets"></a>Anmærke flere aktiver
Når du vælger flere dataaktiver i portalen datakatalog, kan brugere anmærke alle de valgte anlægsaktiver i en enkelt handling. Anmærkninger skal gælde for alle markerede anlægsaktiver, hvilket gør det nemt at vælge og giver dig en ensartet beskrivelse og sæt af mærker og eksperter til relaterede dataaktiver.

> [AZURE.NOTE] Mærker og eksperter kan også angives, når registrere dataaktiver ved hjælp af datakatalog dataene fra kilden værktøjet til registrering af.

Når valg af flere tabeller og visninger, kun kolonner, alle markeret Aktiver har til fælles data vises i portalen datakatalog. Dette kan brugerne angive mærker og beskrivelser for alle kolonner med samme navn for alle valgte aktiver.

## <a name="annotations-and-discovery"></a>Anmærkninger og registrering
Ligesom de metadata, der er hentet fra datakilden under registrering er føjet til søgeindekset datakatalog, indekseres brugerangivet metadata. Det betyder, ikke kun anmærkninger gør det nemmere for brugerne at forstå de data, de har fået kendskab, anmærkninger gør det også nemmere for brugerne at finde de kommenterede dataaktiver ved at søge ved hjælp af de betingelser, der giver mening for dem.

## <a name="summary"></a>Oversigt
Registrering af en datakilde med datakatalog gør dataene så det bliver nemmere ved at kopiere strukturelle og beskrivende metadata fra datakilden til tjenesten katalog. Når der er registreret en datakilde, kan brugerne angive anmærkninger til nemmere at finde og forstå fra i portalen datakatalog.

## <a name="see-also"></a>Se også
- [Introduktion til Azure datakatalog](data-catalog-get-started.md) selvstudium trinvise få mere at vide om, hvordan du anmærke datakilder.
