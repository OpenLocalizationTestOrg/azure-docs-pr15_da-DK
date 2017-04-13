<properties
    pageTitle="Guiden data Factory kopi | Microsoft Azure"
    description="Få mere at vide om, hvordan du bruger guiden kopi Factory til at kopiere data fra understøttede datakilder til dræn."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="spelluru"/>

# <a name="data-factory-copy-wizard"></a>Guiden data Factory kopi
Guiden Azure Data Factory kopi er at gøre det nemmere at ingesting data, som er som regel et første trin i et scenarie med integration til slut data. Når du går gennem guiden Azure Data Factory kopi, behøver du ikke at forstå en hvilken som helst JSON definitioner for sammenkædede services, datasæt og rørledninger. Men, efter du fuldføre alle trin i guiden, guiden opretter automatisk en rørledning for at kopiere data fra den valgte datakilde til den valgte destination. Desuden guiden kopi hjælper dig med at validere dataene der optagelse på tidspunktet for redigering, som gemmer meget af din tid, især når du ingesting data for første gang fra datakilden. Klik på feltet **kopiere data** på startsiden for dine data factory for at starte guiden kopi.

![Kopiere guiden](./media/data-factory-copy-wizard/copy-data-wizard.png)


## <a name="an-intuitive-wizard-for-copying-data"></a>En intuitiv guiden for kopiering af data
Denne guide kan du nemt flytte data fra en lang række kilder til destinationer i minutter. Når du har åbnet i guiden, oprettes en rørledning med en kopi aktivitet automatisk for dig sammen med afhængige Data Factory-enheder (sammenkædede services og datasæt). Der kræves ingen yderligere trin til at oprette en pipeline.   

![Vælg datakilde](./media/data-factory-copy-wizard/select-data-source-page.png)

> [AZURE.NOTE] Se [kopi guiden selvstudium](data-factory-copy-data-wizard-tutorial.md) artikel for at få en trinvis vejledning til at oprette en stikprøve pipeline til at kopiere data fra en Azure blob til en tabel med Azure SQL-Database. 

Guiden er udviklet med stor data huske fra starten. Det er enkle og effektive til at redigere Data Factory rørledninger, som flytter hundredvis af mapper, filer eller tabeller ved hjælp af guiden Kopier Data. Guiden understøtter følgende tre funktioner: automatisk eksempelvisning, skema capture og tilknytning og filtrering af data. 

## <a name="automatic-data-preview"></a>Automatisk datavisning 
Kopiér guiden kan du gennemser en del af dataene fra den valgte datakilde for dig at validere, om dataene, det er de rigtige data, du vil kopiere. Hvis kildedataene er i en tekstfil, fortolker guiden kopi desuden tekstfil for at lære række og kolonneafgrænsere og skema automatisk. 

![Indstillinger for format](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Skema capture og tilknytning 
Skemaet for inputdataene muligvis ikke overens med skemaet output data i nogle tilfælde. I dette scenarie skal du knytte kolonner fra kilden skemaet til kolonner fra Destinationsskemaet. 

Guiden Kopier knytter automatisk kolonner i skemaet kilde til kolonner i Destinationsskemaet. Du kan tilsidesætte tilknytningerne ved hjælp af de rullelister (eller) angive, om en kolonne, skal være ignoreret under kopiering af dataene.   

![Skematilknytning](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrering af data  
Guiden kan du filtrere kildedataene for at vælge kun de data, der skal kopieres til destinationen/sink datalager. Filtrering reducerer lydstyrken for dataene, der skal kopieres til datalager sink og derfor forbedrer overførselshastigheden af kopieringen. Det er en fleksibel metode til at filtrere data i en relationel database ved hjælp af SQL query language (eller) filer i en Azure blob-mappe ved hjælp af [Data Factory-funktioner og variabler](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtrering af data i en database  
I eksemplet med SQL-forespørgslen bruger den `Text.Format` funktionen og `WindowStart` variabel. 

![Validere udtryk](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrering af data i en Azure blob-mappe
Du kan bruge variabler i stien til at kopiere data fra en mappe, der er fastsat på kørselstidspunktet, der er baseret på [Systemvariabler](data-factory-functions-variables.md#data-factory-system-variables). Understøttede variabler er: **{år}**, **{måned}**, **{dag}**, **{time}**, **{minut}**og **{brugerdefineret}**. Eksempel: inputfolder / {år} / {måned} / {dag}.

Antag, at du har indtastet mapper i følgende format:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Klik på knappen **Gennemse** til **fil eller mappe**, gå til en af disse mapper (for eksempel 2016 -> 03 -> 01 -> 02), og klik på **Vælg**. Skal du se afsnittet `2016/03/01/02` i tekstfeltet. Nu, Erstat **2016** med **{år}**, **03** med **{måneden}**, **01** med **{dag}**og **02** med **{time}**, og tryk på Tab. Du burde se rullelister til at vælge format for disse fire variabler:

![Ved hjælp af systemvariabler](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Som vist på følgende skærmbillede, kan du også bruge en **brugerdefineret** variabel og en hvilken som helst [understøttes formatstrenge](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Hvis du vil vælge en mappe med strukturen, skal du først bruge knappen **Gennemse** . Derefter Erstat værdien med **{brugerdefineret}**, og tryk på Tab for at se tekstfeltet, hvor du kan skrive Formatstrengen.     

![Ved hjælp af brugerdefinerede variabel](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)


## <a name="support-for-diverse-data-and-object-types"></a>Understøttelse af forskelligartet data og objekttyper
Du kan flytte hundredvis af mapper, filer eller tabeller effektivt ved hjælp af guiden kopi.

![Vælg tabeller, du vil kopiere data fra](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>Indstillinger for planlægning
Du kan køre kopieringen én gang eller på en tidsplan (hver time, dagligt, osv.). Begge disse indstillinger kan bruges til bredden af forbindelserne på tværs af lokale, skyen og lokal kopi af computeren.

En enkeltstående kopieringen aktiverer flytning af data fra en kilde til en destination kun én gang. Gælder for data fra en hvilken som helst størrelse og en hvilken som helst understøttet format. Den planlagte kopi giver dig mulighed at kopiere data på en bestemt gentagelse. Du kan bruge avancerede indstillinger (som forsøg igen, timeout og beskeder) til at konfigurere den planlagte kopi.

![Planlægge egenskaber](./media/data-factory-copy-wizard/scheduling-properties.png)


## <a name="next-steps"></a>Næste trin
En hurtig gennemgang af ved hjælp af guiden Data Factory kopi til at oprette en rørledning med kopi aktiviteter, se [Selvstudium: oprette en pipeline ved hjælp af guiden kopi](data-factory-copy-data-wizard-tutorial.md).
