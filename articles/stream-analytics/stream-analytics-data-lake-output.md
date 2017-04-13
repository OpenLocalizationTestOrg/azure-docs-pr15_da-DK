<properties
    pageTitle="Stream Analytics sø datalager Output | Microsoft Azure"
    description="Konfiguration af godkendelse og godkendelse af et Azure datalager sø i et Stream Analytics-job"
    keywords=""
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>

# <a name="stream-analytics-data-lake-store-output"></a>Stream Analytics sø datalager output

Stream Analytics job understøtter flere forskellige outputmetoder, en blive en [Azure sø datalager](https://azure.microsoft.com/services/data-lake-store/). Azure datalager sø er en hele virksomheden hyper-skala lager for stor data analytisk arbejdsmængder. Sø datalager gør det muligt at gemme data fra en hvilken som helst størrelse, type og indtagelse hastigheden for drift og undersøgende analyser.

## <a name="authorize-a-data-lake-store-account"></a>Godkend en sø datalager-konto

1.  Når sø datalager er markeret som output i portalen Azure Management, du vil blive bedt om at tillade brugen af dit eksisterende datalager for sø eller anmode om adgang til Data sø Store udskrift via portalen Azure klassisk.

    ![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-authorization.png)  

2.  Hvis du allerede har adgang til sø datalager, klik på "Godkend nu", og i kort tid en side kommer frem, der angiver "Redirecting til godkendelse …". Siden lukkes automatisk, og du vil få vist den side, der giver dig tilladelse til at konfigurere sø datalager output.

Hvis du ikke har tilmeldt sig til Store datavisning sø, kan du følge linket "Tilmeld dig nu" for at starte anmodningen, eller Følg [få en introduktion vejledning](../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="configure-the-data-lake-store-output-properties"></a>Konfigurere egenskaberne sø datalager output

Når du har kontoen sø datalager godkendt, kan du konfigurere egenskaberne for din sø datalager output. Tabellen nedenfor er listen over navne og deres beskrivelse for at konfigurere din sø datalager output.

<table>
<tbody>
<tr>
<td><B>EGENSKABSNAVN</B></td>
<td><B>BESKRIVELSE</B></td>
</tr>
<tr>
<td>Output Alias</td>
<td>Dette er et brugervenligt navn, der bruges i forespørgsler til at dirigere forespørgslens output til denne sø datalager.</td>
</tr>
<tr>
<td>Sø datalager konto</td>
<td>Navnet på kontoen lagerplads, hvor du sender din output. Du der præsenteres med en rulleliste over sø datalager konti, som den bruger, der er logget på portalen har adgang til.</td>
</tr>
<tr>
<td>Sti præfiks mønster [<I>valgfrit</I>]</td>
<td>Filstien bruges til at skrive dine filer i den angivne Data sø Store konto. <BR>{date}, {time}<BR>Eksempel 1: folder1/logfiler / {dato} / {tid}<BR>Eksempel 2: folder1/logfiler / {dato}</td>
</tr>
<tr>
<td>Datoformat [<I>valgfrit</I>]</td>
<td>Hvis dato tokenet bruges i præfiks stien, kan du vælge datoformatet, hvor dine filer er organiseret. Eksempel: Åååå/MM/DD</td>
</tr>
<tr>
<td>Klokkeslætsformat [<I>valgfrit</I>]</td>
<td>Hvis tokenet tid der bruges i præfiks stien, kan du angive det klokkeslætsformat, hvor dine filer er organiseret. Den eneste understøttede værdi er i øjeblikket HH.</td>
</tr>
<tr>
<td>Begivenhed serialiseringsformatet</td>
<td>Serialiseringsformatet til outputdata. JSON, csv- og Avro understøttes.</td>
</tr>
<tr>
<td>Kode</td>
<td>Hvis csv- eller JSON formaterer, skal en kodning angives. UTF-8 er kun understøttet kodningsformatet på nuværende tidspunkt.</td>
</tr>
<tr>
<td>Afgrænser</td>
<td>Gælder kun for csv-serialisering. Stream Analytics understøtter et antal almindelige afgrænsere til serialisering csv-data. Understøttede værdier er komma, semikolon, mellemrum, tabulatorstop og lodret streg.</td>
</tr>
<tr>
<td>Formatér</td>
<td>Gælder kun for JSON serialisering. Linje adskilt angiver, at der skal formateres output ved at få de enkelte JSON-objekter, der er adskilt af en ny linje. Matrix angiver, at der skal formateres output som en matrix med JSON objekter.</td>
</tr>
</tbody>
</table>

## <a name="renew-data-lake-store-authorization"></a>Forny sø datalager godkendelse

I øjeblikket, er der en begrænsning hvor godkendelse tokenet skal opdateres manuelt hver 90 dage for alle job med sø datalager output. Du skal også til at godkende kontoen sø datalager igen, hvis du har ændret din adgangskode, da tingene blev oprettet eller senest godkendt. Et symptom på dette problem er ingen job output og fejl i loggene handling, der angiver behovet for fornyet godkendelse.

Stoppe med at køre tingene for at løse dette problem, og gå til din sø datalager output. Klik på linket "Fornyelse godkendelse", og i kort tid en side kommer frem, der angiver "Redirecting til godkendelse …". Siden lukkes automatisk, og hvis det lykkes, vil angive "Godkendelse er blevet fornyet". Du og derefter skal du klikke på "Gem" nederst på siden, og kan fortsætte ved at genstarte tingene fra sidste ikke længere tid til at undgå tab af data.

![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-renew-authorization.png)
