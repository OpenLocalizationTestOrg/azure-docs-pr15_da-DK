<properties
    pageTitle="Analytics platforme: Apache Storm sammenligning med Stream Analytics | Microsoft Azure"
    description="Få vejledning, vælge en skyen analytics platform ved hjælp af en sammenligning af Apache Storm med Stream analyser. Forstå funktioner og forskelle."
    keywords="Analytics platform, analytics platforme, skyen analytics platform, storm sammenligning"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="help-choosing-a-streaming-analytics-platform-apache-storm-comparison-to-azure-stream-analytics"></a>Hjælp til at vælge en streaming analytics-platform: Apache Storm sammenligning med Azure Stream Analytics

Få vejledning, vælge en skyen analytics platform ved hjælp af denne Apache Storm sammenligning til Azure Stream analyser. Forstå tilbud af Stream Analytics kontra Apache Storm som en administreret tjeneste på Azure HDInsight, så du kan vælge den rigtige løsning til din virksomhed bruger sager.

Både analytics platforme leverer fordelene ved en PaaS løsning, der er et par overordnede kendingsnummer egenskaber, som skelner dem. Funktioner samt begrænsningerne af disse tjenester er nedenfor for at du lander på den løsning, du har brug for at nå dine mål.

## <a name="storm-comparison-to-stream-analytics-general-features"></a>Storm sammenligning med Stream Analytics: generelle funktioner ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm på HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Åbn kilde</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Nej, Azure Stream Analytics er en Microsoft-farmaceutiske tilbyder.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Ja, Apache Storm er en Apache licens teknologi.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Microsoft understøttes</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Ja </p>
            </td>
            <td width="246" valign="top">
                <p>
Ja </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Hardwarekrav</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Der findes ingen hardwarekrav. Azure Stream Analytics er en Azure Service.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Der findes ingen hardwarekrav. Apache Storm er en Azure Service.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Øverste niveau enhed</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Installere med Azure Stream Analytics kunder og Overvåg streaming job.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Installere med Apache Storm på HDInsight kunder og overvåge en hel klynge, som kan være vært for flere Storm job samt andre arbejdsbelastninger (herunder batchen).
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Pris</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Stream Analytics pris lydstyrke behandlet og antallet af streaming enheder (i timen kørslen) påkrævet.
                </p>
                <p>
                    <a href="http://azure.microsoft.com/en-us/pricing/details/stream-analytics/">Yderligere prisoplysninger kan findes her.</a>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
For Apache Storm på HDInsight, måleenheden for køb er klynge-baserede og debiteres baseret på det tidspunkt, klyngen kører, uafhængigt af sager, der er installeret.
                </p>
                <p>
                    <a href="http://azure.microsoft.com/en-us/pricing/details/hdinsight/">Yderligere prisoplysninger kan findes her.</a>
                </p>
            </td>
        </tr>
    </tbody>
</table>
##Redigering på hver analytics-platform##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm på HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Funktioner: SQL DSL</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Ja, en brugervenlig understøttelse af sprog SQL er tilgængelig.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Nej, brugerne skal skrive programkode i Java C# eller bruge Trident API'er.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Funktioner: Tidsmæssig operatorer</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Slideshowtype samlinger og tidsmæssig joinforbindelser understøttes af feltet.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Tidsmæssig operatorer skal være implementeret af brugeren.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Udvikling oplevelse</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Interaktive redigering og fejlfinding oplevelse via Azure Portal på eksempeldata.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Udvikling, fejlfinding og overvågning oplevelse der leveres via Visual Studio oplevelse for .NET brugere, mens til Java og andre sprog udviklere kan bruge IDE efter eget valg.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Understøttelse af fejlfinding.</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Stream Analytics tilbyder grundlæggende jobstatus og handlinger logfiler som en metode for fejlfinding, men i øjeblikket ikke tilbyder fleksibilitet i hvad/hvor meget ie er inkluderet i loggene: detaljeret tilstand.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Detaljerede logfiler er tilgængelige for i forbindelse med fejlfinding. Der er to måder at surface logfiler til bruger, via visual Studio eller bruger kan RDP til klynge for at få adgang til logfiler.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Understøttelse af UDF (brugerdefinerede funktioner)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Der er i øjeblikket ingen understøttelse af brugerdefinerede funktioner.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Brugerdefinerede funktioner kan skrives i C#, Java eller et andet sprog efter eget valg.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Den udvidede - brugerdefineret kode</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Der er ingen understøttelse for extensible kode i Stream analyser.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Ja, der er tilgængelig til at skrive brugerdefineret kode i C#, Java eller andre understøttede sprog på Storm.
                </p>
            </td>
        </tr>
    </tbody>
</table>
##Datakilder og output##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm på HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                 <strong>Input-datakilder</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>Understøttede inputkilder er Azure begivenhed Hubs og Azure BLOB.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Der findes forbindelser til begivenhed Hubs, Service Bus, Kafka osv. Ikke-understøttede forbindelser kan gennemføres via brugerdefineret kode.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Skriv dataformater</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Understøttede formater til input er Avro, JSON, CSV.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Et vilkårligt format kan gennemføres via brugerdefineret kode.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Output</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Et streaming job kan have flere output. Understøttede output: Azure begivenhed hubber, Azure Blob-lager, Azure tabeller, SQL Azure DB og PowerBI.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Understøttelse af for mange output i en topologi muligvis hver output brugerdefineret logik for efterfølgende behandling. Af feltet indeholder Storm forbindelser til PowerBI, Azure begivenhed Hubs, Azure Blob-lager, Azure DocumentDB, SQL og HBase. Ikke-understøttede forbindelser kan gennemføres via brugerdefineret kode.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Kodning dataformater</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Stream Analytics kræver UTF-8 dataformat til anvendes.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Data kodningsformatet kan gennemføres via brugerdefineret kode.
                </p>
            </td>
        </tr>
    </tbody>
</table>
##Administration og handlinger##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm på HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Jobbet implementeringsmodel</strong>
                </p>
                <p>
                    - <strong>Azure-portalen</strong>
                </p>
                <p>
                    - <strong>Visual Studio</strong>
                </p>
                <p>
                    - <strong>PowerShell</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Installation er implementeret via Azure-portalen, PowerShell og REST API'er.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Depolyment er implementeret via Azure-portalen, PowerShell, Visual Studio og REST API'er.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Overvågning (statistik, tællere osv.)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Overvågning er implementeret via Azure Portal og REST API'er.
                </p>
                <p>
Brugeren kan også konfigurere Azure beskeder.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Overvågning er implementeret via Storm Brugergrænsefladen og REST API'er.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Skalerbarhed</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Antallet af Streaming enheder for hver sag. Hver Streaming enhed behandler op til 1 MB/s. Maksimalt antal 50 enheder som standard. Opkald for at øge grænse.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Antallet af knuder på HDI Storm klynge. Ingen grænse for antallet af knuder (øverste grænsen defineret af den Azure lagerkvote). Opkald for at øge grænse.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Begrænsninger for databehandling</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Brugere kan skalere op eller ned antal Streaming enheder for at øge databehandling eller optimere omkostninger.
                </p>
                <p>
Skalere op til 1 GB/s </p>
            </td>
            <td width="246" valign="top">
                <p>
Brugeren kan skalere op eller ned klyngestørrelse til behov.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Stop/CV</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Stoppe og fortsætte fra sidste sted ikke længere.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Stoppe og fortsætte fra sidste placere holdt op med at baseret på vandmærket.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Opdatering til tjenesten og framework</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Automatisk rettelse uden nedetid.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Automatisk rettelse uden nedetid.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Forretningskontinuitet via en meget tilgængelig tjeneste med garanterede SERVICENIVEAUAFTALE</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
SLA af 99,9% oppetid </p>
                <p>
Automatisk gendannelse af fejl </p>
                <p>
Gendannelse af med høj sikkerhed tidsmæssig operatorer er indbygget.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
SLA af 99,9% oppetid af Storm klynge. Apache Storm er en fejl tolerant platform til streaming, men det er kundernes ansvaret for at sikre deres streaming job kører uden afbrydelser.
                </p>
            </td>
        </tr>
    </tbody>
</table>
##Avancerede funktioner##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm på HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Sen ankomst og håndtering af defekt begivenhed</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Indbyggede konfigurerbar politikker til at ændre rækkefølgen, slip begivenheder eller Juster tidspunkt for begivenheden.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Brugeren skal implementere logik for at håndtere dette scenario.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Referencedata</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Tilgængelige referencedata fra Azure BLOB med maksimal størrelse på 100 MB i hukommelsen opslag cache. Opdatering af referencedata administreres af tjenesten.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Ingen begrænsninger på datastørrelse. Forbindelser, der er tilgængelige for HBase, DocumentDB, SQL Server og Azure. Ikke-understøttede forbindelser kan gennemføres via brugerdefineret kode.
                </p>
                <p>
Opdatering af referencedata skal håndteres af brugerdefineret kode.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Integration med Machine Learning</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Publicerede Azure Machine Learning modeller som funktioner under ASA job oprettelse af <a href="http://blogs.msdn.com/b/streamanalytics/archive/2015/05/24/real-time-scoring-of-streaming-data-using-machine-learning-models.aspx">(privat preview)</a>ved at konfigurere.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Tilgængelige via Storm bolte.
                </p>
            </td>
        </tr>
    </tbody>
</table>
