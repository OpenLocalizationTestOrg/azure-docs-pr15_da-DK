<properties
   pageTitle="Hvad er Azure datakatalog? | Microsoft Azure"
   description="I denne artikel indeholder en oversigt over Microsoft Azure-datakatalog, herunder dets funktioner og den er designet til at håndtere problemerne. Datakatalog indeholder funktioner, der aktiverer en bruger – fra analytikere data forskere til udviklere – til at registrere, opdage, forstå og bruge datakilder."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/21/2016"
   ms.author="maroche"/>

# <a name="what-is-azure-data-catalog"></a>Hvad er Azure datakatalog?

Azure datakatalog er en fuldt administreret skybaseret tjeneste, der gør det muligt for brugerne at finde de datakilder, de skal bruge, og at forstå de finder, samtidig med organisationer få flere værdi fra deres eksisterende investeringer datakilderne. Datakatalog indeholder funktioner, der aktiverer en bruger – fra analytikere data forskere til udviklere – til at finde, forstå og bruge datakilder. Datakatalog indeholder en crowdsourcing model af metadata og anmærkninger, og gør det muligt for alle brugere kan bidrage deres viden for at opbygge et community og kultur af data.

## <a name="discovery-challenges-for-data-consumers"></a>Registrering udfordringer for dataforbrugere af

Traditionelt opdager enterprise-datakilder, der har været en organiske proces, der er baseret på tribal viden. Dette viser flere udfordringer for virksomheder, som ønsker at få den største værdi fra deres oplysninger aktiver.

-   Brugere kan ikke opmærksom på, at der findes datakilder, medmindre de komme i kontakt med den som en del af en anden proces der er ikke central placering, hvor datakilder er registreret.
-   Medmindre en bruger kender placeringen af en datakilde, han kan ikke oprette forbindelse til data ved hjælp af en klientprogrammet; data forbrug oplevelser brugerne skal vide forbindelsesstreng eller sti.
-   Medmindre en bruger kender placeringen af en datakilde dokumentation, kan han forstå formål for dataene. datakilder og dokumentation live på forskellige steder og er consumed gennem forskellige oplevelser.
-   Hvis en bruger har spørgsmål om et oplysninger aktiv, skal han Find ekspert eller team, der er ansvarlig for dataene, og deltage disse eksperter offline. der er ingen eksplicitte forbindelse mellem data, og dem med expert perspektiver i dens anvendelse.
-  Medmindre en bruger forstår processen for anmoder om adgang til datakilden, aktiverer opdager datakilden og den tilhørende dokumentation stadig ikke til at få adgang til dataene, han kræver.

## <a name="discovery-challenges-for-data-producers"></a>Registrering udfordringer for producenter af data

Mens dataforbrugere vises disse udfordringer, ud for brugere, der er ansvarlig for at frembringe og opretholde oplysninger Aktiver udfordringer i forbindelse med deres egen.

-   Anmærke datakilder med beskrivende metadata er ofte et tabt der klientprogrammer Ignorer typisk beskrivelser, der er gemt i datakilden.
-   Oprette dokumentation til datakilder er ofte et tabt der holder dokumentation synkroniserede med datakilden er en igangværende ansvar, og brugere opnår sikkerhed og rettighedsadministration i dokumentation, som den anses ofte som værende forældet.
- Begrænse adgang til datakilden, og at sikre, at dataene forbrugere vide, hvordan du anmode om adgang er en igangværende udfordring.

Oprette og vedligeholde dokumentation for en datakilde er kompleks og mere tidskrævende. Udfordringen ved at gøre dokumentationen let tilgængelige for alle, som bruger datakilden er ofte endda endnu bedre.

Når kombineres, præsentere disse udfordringer en betydeligt barriere for virksomheder, der vil støtte og fremme brug og forståelse af virksomhedsdata.

## <a name="azure-data-catalog-can-help"></a>Azure datakatalog kan hjælpe

Datakatalog er designet til at løse disse problemer og virksomheder til at få den største værdi fra deres eksisterende oplysninger aktiver. Data katalog gør ved at gøre datakilder nemmere og forstå ved de brugere, der har brug for de data, de styrer.

Datakatalog indeholder en skybaseret tjeneste, i hvilken data kan være registreret kilde. Dataene forbliver i på den eksisterende placering, men en kopi af metadata, der er føjet til datakatalog, sammen med en reference til datakildeplacering. Disse metadata er også indekseret for at gøre hver datakilde nemmere via søgning, og forstå til brugere, som finde det.

Når der er registreret en datakilde, kan dens metadata derefter forbedre, enten ved den bruger, der udføres registreringen eller ved at andre brugere i virksomheden. Alle brugere kan oprette anmærkninger i en datakilde ved at indsende beskrivelser, mærker eller andre metadata, som dokumentation og processer til at anmode om adgang til datakilden. Disse beskrivende metadata supplerer de strukturelle metadata (som kolonnenavne og datatyper) registreret fra datakilden.

Finde og forstå datakilder og deres brug er Hovedformålet med at registrere kilderne. De kan bruge datakatalog registrering oplevelse til hurtigt at finde data, der svarer til deres behov, forstå dataene til at evaluere bekræfter til formål, og bruge disse data ved at åbne datakilden i deres værktøjet efter valg, når enterprise-brugere skal data til deres indsats (hvilket kunne business intelligence, udvikling af programmer, data videnskabelige eller andre opgaver hvor de rigtige data, der kræves). Datakatalog kan brugere til at bidrage til kataloget, mærkning, dokumentere og anmærkninger datakilder, der allerede er registreret og ved at registrere nye datakilder, som kan derefter har opdaget, forstået, og consumed ved community katalog brugere på samme tid.

![Data Datakataloget](./media/data-catalog-what-is-data-catalog/data-catalog-capabilities.png)

## <a name="get-started-with-data-catalog"></a>Introduktion til datakatalog

For at komme i gang med datakatalog i dag skal du besøge [www.azuredatacatalog.com](https://www.azuredatacatalog.com).

En introduktion få vejledning findes [her](data-catalog-get-started.md).

## <a name="learn-more-about-data-catalog"></a>Lær mere om datakatalog

Hvis du vil vide mere om funktionerne i datakatalog skal du se:

* [Sådan registreres datakilder](data-catalog-how-to-register.md)
* [Hvordan du kan registrere datakilder](data-catalog-how-to-discover.md)
* [Sådan anmærke datakilder](data-catalog-how-to-annotate.md)
* [Sådan dokumentdatakilder](data-catalog-how-to-documentation.md)
* [Sådan oprettes forbindelse til datakilder](data-catalog-how-to-connect.md)
* [Sådan arbejder du med stor data](data-catalog-how-to-big-data.md)
* [Sådan administreres over dataaktiver](data-catalog-how-to-manage.md)
* [Hvordan du konfigurerer Business-ordliste](data-catalog-how-to-business-glossary.md)
* [Ofte stillede spørgsmål](data-catalog-frequently-asked-questions.md)
