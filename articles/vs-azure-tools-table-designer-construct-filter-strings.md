<properties
   pageTitle="Bygning af filter strenge for tabeldesign | Microsoft Azure"
   description="Bygning af filter strenge for tabeldesign"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="storage"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="constructing-filter-strings-for-the-table-designer"></a>Bygning af Filter strenge for tabeldesign

## <a name="overview"></a>Oversigt

Hvis du vil filtrere data i en Azure-tabel, der vises i **Tabeldesign**i Visual Studio, skal du oprette en filterstreng og angive den i filterfeltet. Filter Strengsyntaksen er defineret af WCF Data Services og svarer til en SQL WHERE-delsætning, men der sendes til tjenesten tabel via en HTTP-anmodning. **Tabeldesigner** håndterer den korrekte kodning for dig, så til at filtrere på en ønskede egenskabsværdi, du skal kun angive egenskabsnavn, sammenligningsoperator, kriterieværdi, og du kan også boolesk operator i filterfeltet. Du behøver ikke at medtage indstillingen $filter forespørgsel, som du ville gøre, hvis du er blevet bygning af en URL-adresse for at forespørge tabellen via [Lagerplads Services RESTEN API Reference](http://go.microsoft.com/fwlink/p/?LinkId=400447).

WCF Data Services er baseret på [Open Data Protocol](http://go.microsoft.com/fwlink/p/?LinkId=214805) (OData). Du kan finde oplysninger om indstillingen filter system forespørgsel (**$filter**), [OData URI konventioner specifikation](http://go.microsoft.com/fwlink/p/?LinkId=214806).

## <a name="comparison-operators"></a>Sammenligningsoperatorer

Følgende logiske operatorer understøttes for alle egenskabstyper af:

|Logisk operator|Beskrivelse|Eksempel på filter en streng|
|---|---|---|
|EQ|Der er lig med|By eq 'Redmond'|
|BT|Større end|Pris BT 20|
|Flet|Større end eller lig med|Pris Flet 10|
|lt|Mindre end|Pris lt 20|
|Le|Mindre end eller lig med|Pris le 100|
|ne|Forskellig fra|By ne 'London'|
|og|Og|Pris le 200 og pris BT 3.5|
|eller|Eller|Pris le 3.5 eller pris BT 200|
|ikke|Ikke|ikke isAvailable|

Når bygning af et filterstreng, der er vigtige følgende regler:

- Brug logiske operatorer til at sammenligne en egenskab til en værdi. Bemærk, at det ikke er muligt at sammenligne en egenskab til en dynamisk værdi én side af udtrykket skal være en konstant.

- Der skelnes mellem alle dele af Filterstrengen.

- Konstantværdien skal være den samme datatype som egenskaben i rækkefølge for filteret til at returnere gyldige resultater. Du kan finde flere oplysninger om understøttede egenskabstyper, kan du se [om tabel Service datamodellen](http://go.microsoft.com/fwlink/p/?LinkId=400448).

## <a name="filtering-on-string-properties"></a>Filtrering på strengegenskaber

Når du filtrerer streng egenskaber, skal du sætte strengkonstant i enkelte anførselstegn.

Følgende eksempel filtre på egenskaberne **PartitionKey** og **RowKey** ; Yderligere egenskaber, der ikke er en nøgle kan også tilføjes Filterstrengen:

    PartitionKey eq 'Partition1' and RowKey eq '00001'

Du kan skrive hver filterudtrykket i parenteser, selvom det ikke er nødvendigt:

    (PartitionKey eq 'Partition1') and (RowKey eq '00001')

Bemærk, at tjenesten tabel understøtter ikke forespørgsler med jokertegn, og de understøttes ikke i tabeldesigner enten. Du kan dog udføre præfiks tilsvarende ved hjælp af sammenligningsoperatorer på det ønskede præfiks. I følgende eksempel returneres enheder med en efternavn egenskaben begynder med bogstavet "A":

    LastName ge 'A' and LastName lt 'B'

## <a name="filtering-on-numeric-properties"></a>Filtrering på numeriske egenskaber

For at filtrere på et heltal eller et tal med flydende, skal du angive antallet uden anførselstegn.

I dette eksempel returnerer alle enheder med en alder egenskab, hvis værdi er større end 30:

    Age gt 30

I dette eksempel returnerer alle enheder med en AmountDue egenskab, hvis værdi er mindre end eller lig med 100.25:

    AmountDue le 100.25

## <a name="filtering-on-boolean-properties"></a>Filtrering på booleske egenskaber

Hvis du vil filtrere på en boolesk værdi, skal du angive **Sand** eller **Falsk** uden anførselstegn.

I følgende eksempel returneres alle objekter, hvor egenskaben ErAktiv er indstillet til **Sand**:

    IsActive eq true

Du kan også skrive denne filterudtryk uden den logiske operator. I følgende eksempel returnerer tjenesten tabel også alle objekter hvor ErAktiv er **sande**:

    IsActive

For at returnere alle objekter, hvor ErAktiv er falsk, kan du bruge feltet ikke operator:

    not IsActive

## <a name="filtering-on-datetime-properties"></a>Filtrering på Egenskaber for dato/klokkeslæt

For at filtrere på en dato / klokkeslætsværdi, skal du angive nøgleordet **datetime** , efterfulgt af dato/klokkeslæt konstanten i enkelte anførselstegn. Konstanten dato/klokkeslæt være i kombinerede UTC-format, som beskrevet i [Formatering DateTime egenskabsværdier](http://go.microsoft.com/fwlink/p/?LinkId=400449).

I følgende eksempel returneres enheder, hvor egenskaben CustomerSince er lig med 10 juli 2008:

    CustomerSince eq datetime'2008-07-10T00:00:00Z'
