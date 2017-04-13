<properties
    pageTitle="Få mere at vide om funktioner i BizTalk Services-versioner | Microsoft Azure"
    description="Sammenligne funktionerne i BizTalk Services-versioner: ledig, udvikler, Basic, Standard og Premium. MABS, WABS."
    services="biztalk-services"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="mandia"/>


# <a name="biztalk-services-editions-chart"></a>BizTalk-tjenester: Udgaver diagram

Azure BizTalk Services tilbyder flere versioner. Brug denne artikel til at bestemme, hvilken version der passer bedst til dine behov scenarie og business.


## <a name="compare-the-editions"></a>Sammenligne udgaverne

**Ledig (Preview)**

Kan oprette og administrere Hybrid forbindelser. En Hybrid forbindelse er en nem måde at forbinde en Azure websted til et lokalt system, som SQL Server.

**Udvikler**

Omfatter behandling af Hybrid forbindelser, EAI- og eller-Brugergrupper meddelelse med en nemt bruge handel partner administration portal og understøttelse af almindelige eller-Brugergrupper skemaer og RTF eller-Brugergrupper behandling over X12 og AS2. Kan oprette almindelige EAI scenarier, der forbinder tjenester i skyen med en hvilken som helst HTTP/S, RESTEN, FTP, WCF og SFTP protokoller at læse og skrive meddelelser.  Anvende forbindelse til lokale LOB-systemer med klar til brug SAP, Oracle eBusiness, Oracle DB, Siebel og SQL Server kort. Bruge en udvikler centreret miljø med Visual Studio-værktøjer til nem udvikling og installation. Begrænset til udvikling og test formål kun med ingen serviceaftale (SLA).

**Grundlæggende**

Indeholder de fleste udvikler funktioner med øges i hybride forbindelser, EAI broer, eller-Brugergrupper aftaler og Pack til BizTalk-Adapter forbindelser. Tilbyder også høj tilgængelighed og muligheden for at skalere med en serviceaftale (SLA).

**Standard**

Omfatter alle de grundlæggende funktioner med øges i hybride forbindelser, EAI broer, eller-Brugergrupper aftaler og Pack til BizTalk Adapter forbindelser. Tilbyder også høj tilgængelighed og muligheden for at skalere med en serviceaftale (SLA).

**Premium**

Indeholder alle Standard funktioner med øges i hybride forbindelser, EAI broer, eller-Brugergrupper aftaler og Pack til BizTalk Adapter forbindelser. Indeholder også arkivering, høj tilgængelighed og muligheden for at skalere med en serviceaftale (SLA).


## <a name="editions-chart"></a>Udgaver diagram
Den følgende tabel viser forskellene.

<table border="1">
<tr bgcolor="FAF9F9">
        <th></th>
        <th>Ledig (Preview)</th>
        <th>Udvikler</th>
        <th>Grundlæggende</th>
        <th>Standard</th>
        <th>Premium</th>
</tr>

<tr>
<td><strong>Start pris</strong></td>
<td colspan="5"><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Azure BizTalk Services priser</a> <br/><br/> <a HREF="http://azure.microsoft.com/pricing/calculator/?scenario=full">Azure priser Lommeregner</a></td>
</tr>
<tr>
<td><strong>Standard mindste konfiguration</strong></td>
<td>1 gratis enhed</td>
<td>1 udvikler enhed</td>
<td>1 grundlæggende enhed</td>
<td>1 almindelige enhed</td>
<td>1 premium enhed</td>
</tr>
<tr>
<td><strong>Skala</strong></td>
<td>Ingen skalering</td>
<td>Ingen skalering</td>
<td>Ja, i tidsintervaller 1 grundlæggende enhed</td>
<td>Ja, i tidsintervaller 1 Standard enhed</td>
<td>Ja, i tidsintervaller 1 Premium-enhed</td>
</tr>
<tr>
<td><strong>Maksimalt tilladte Skaler ud</strong></td>
<td>Ingen skalering</td>
<td>Ingen skalering</td>
<td>Op til 8 enheder</td>
<td>Op til 8 enheder</td>
<td>Op til 8 enheder</td>
</tr>
<tr>
<td><strong>EAI broer per enhed</strong></td>
<td>Ikke inkluderet</td>
<td>25</td>
<td>25</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>ELLER-BRUGERGRUPPER, AS2</strong>
<br/><br/>
Omfatter TPM aftaler</td>
<td>Ikke inkluderet</td>
<td>Inkluderet. 10 aftaler per enhed.</td>
<td>Inkluderet. 50 aftaler per enhed.</td>
<td>Inkluderet. 250 aftaler per enhed.</td>
<td>Inkluderet. 1000 aftaler per enhed.</td>
</tr>
<tr>
<td><strong>Hybrid forbindelser per enhed</strong></td>
<td>5</td>
<td>5</td>
<td>10</td>
<td>50</td>
<td>100</td>
</tr>
<tr>
<td><strong>Hybrid forbindelse dataoverførsel (GB) per enhed</strong></td>
<td>5</td>
<td>5</td>
<td>50</td>
<td>250</td>
<td>500</td>
</tr>
<tr>
<td><strong>BizTalk Adapter Service forbindelser til lokale LOB-systemer</strong></td>
<td>Ikke inkluderet</td>
<td>1 forbindelse</td>
<td>2 forbindelser</td>
<td>5 forbindelser</td>
<td>25 forbindelser</td>
</tr>
<tr>
<td align="left"><strong>Understøttede protokoller/systemer:</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>Service Bus (SB)</li>
<li>Azure Blob</li>
<li>REST API'er</li>
</ul>
</td>
<td>Ikke inkluderet</td>
<td>Inkluderet</td>
<td>Inkluderet</td>
<td>Inkluderet</td>
<td>Inkluderet</td>
</tr>
<tr>
<td><strong>Høj tilgængelighed</strong>
<br/><br/>
For serviceaftale (SLA), se <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk Services priser</a>.
</td>
<td>Ikke inkluderet</td>
<td>Ikke inkluderet</td>
<td>Inkluderet</td>
<td>Inkluderet</td>
<td>Inkluderet</td>
</tr>
<tr>
<td><strong>Sikkerhedskopiering og gendannelse</strong></td>
<td>Ikke inkluderet</td>
<td>Inkluderet</td>
<td>Inkluderet</td>
<td>Inkluderet</td>
<td>Inkluderet</td>
</tr>
<tr>
<td><strong>Sporing</strong></td>
<td>Ikke inkluderet</td>
<td>Inkluderet</td>
<td>Inkluderet</td>
<td>Inkluderet</td>
<td>Inkluderet</td>
</tr>
<tr>
<td><strong>Arkivering</strong><br/><br/>
Indeholder ikke-afvisning af kvittering (NRR) og hente registrerede meddelelser</td>
<td>Ikke inkluderet</td>
<td>Inkluderet</td>
<td>Ikke inkluderet</td>
<td>Ikke inkluderet</td>
<td>Inkluderet</td>
</tr>
<tr>
<td><strong>Brug af brugerdefineret kode</strong></td>
<td>Ikke inkluderet</td>
<td>Inkluderet</td>
<td>Inkluderet</td>
<td>Inkluderet</td>
<td>Inkluderet</td>
</tr>
<tr>
<td><strong>Brug af transformeringer, herunder brugerdefinerede XSLT</strong></td>
<td>Ikke inkluderet</td>
<td>Inkluderet</td>
<td>Inkluderet</td>
<td>Inkluderet</td>
<td>Inkluderet</td>
</tr>
</table>

> [AZURE.NOTE] Af mod hardwarefejl, tolerance indebærer høj tilgængelighed har flere FOS i en enkelt BizTalk-enhed.


## <a name="faqs"></a>Ofte stillede spørgsmål

#### <a name="what-is-a-biztalk-unit"></a>Hvad er en BizTalk-enhed?
En "enhed" er det atomiske niveau af en Azure BizTalk Services-installation. Hver edition leveres med en enhed, der indeholder forskellige databehandling og hukommelse. For eksempel en grundlæggende enhed har flere Beregn end udvikler, Standard har flere Beregn end grundlæggende osv. Når du skalere et BizTalk Service, skal skalere du med hensyn til enheder.

#### <a name="what-is-the-difference-between-biztalk-services-and-azure-biztalk-vm"></a>Hvad er forskellen mellem BizTalk-tjenester og Azure BizTalk VM?
BizTalk Services indeholder en SAND Platform som-en-tjenesten (PaaS) arkitektur opbygning af løsninger til integrering af i skyen. Med modellen PaaS helt fokusere på programmet logik og lade alle infrastruktur management til Microsoft, herunder:

- Du behøver ikke at håndtere eller programrettelse virtuelle computere.
- Microsoft sikrer tilgængelighed.
- Du kan kontrollere skala efter behov ved blot anmoder om mere eller mindre kapacitet via Azure-portalen.

BizTalk Server på virtuelle Azure-computere indeholder en infrastruktur som-en-tjenesten (IaaS) arkitektur. Du opretter virtuelle maskiner og konfigurere dem, præcis som det lokale miljø, gør det nemmere at køre eksisterende programmer i skyen, uden kodeændringer. Med IaaS er du stadig ansvarlig for konfiguration af virtuelle maskiner, administration af de virtuelle maskiner (for eksempel installere software og OS rettelser) og planlagde arkitektur programmet for høj tilgængelighed.

Hvis du kigger på opbygning af nye integration-løsninger, der minimerer din infrastruktur management indsats, kan du bruge BizTalk-tjenester. Hvis du vil du hurtigt overføre dine eksisterende BizTalk løsninger eller leder du efter et efter behov miljø til at udvikle og teste F.eks programmer, kan du bruge F.eks på en Azure Virtual Machine.

#### <a name="what-is-the-difference-between-biztalk-adapter-service-and-hybrid-connections"></a>Hvad er forskellen mellem BizTalk Adapter Service og Hybrid forbindelser?
BizTalk Adapter Service bruges af en Azure BizTalk Service. Tjenesten BizTalk-Adapter bruger BizTalk-Adapter pakken til at oprette forbindelse til et lokalt linje for Business (LOB) system. En Hybrid forbindelse er en nem og praktisk måde at forbinde Azure-programmer, som funktionen Web Apps i Azure App Service og Azure Mobile-tjenester, til en lokal ressource.

#### <a name="what-does-hybrid-connection-data-transfer-gb-per-unit-mean-is-this-per-minutehourdayweekmonth-what-happens-when-the-limit-is-reached"></a>Hvad betyder "Hybrid forbindelse dataoverførsel (GB) per enhed"? Er dette per minutters/time/dag/uge/måned? Hvad sker der, når du er nået?

Hybrid forbindelse enhedsprisen afhænger af BizTalk Services edition. Bare omkostninger afhænger af, hvor meget data du overfører. For eksempel overføre 10 GB data dagligt du koster mindre end at overføre 100 GB dagligt. Bruge [Priser Lommeregner](https://azure.microsoft.com/pricing/calculator/?scenario=full) for BizTalk-tjenester til at bestemme specifikke omkostninger. Typisk håndhæves begrænsningerne dagligt. Hvis du overskrider grænsen, registreres alle overskud til rente $1 per GB.

#### <a name="when-i-create-an-agreement-in-biztalk-services-why-does-the-number-of-bridges-go-up-by-two-instead-of-just-one"></a>Når jeg opretter en aftale i BizTalk-tjenester, hvorfor antallet broer går med to i stedet for kun én?

Hver aftale består af to forskellige broer: en send side kommunikation bro og en Modtag side kommunikation bro.

####  <a name="what-happens-when-i-hit-the-quota-limit-on-the-number-of-bridges-or-agreements"></a>Hvad sker der, når jeg rammer kvote grænsen for antallet af broer eller aftaler?

Du kan ikke installere en ny broer eller oprette alle nye aftaler. Hvis du vil installere mere, vil du skalere op til flere enheder af tjenesten BizTalk eller opgradere til en nyere udgave.

#### <a name="how-do-i-migrate-from-one-tier-of-biztalk-services-to-another"></a>Hvordan kan jeg overføre fra den ene del af BizTalk tjenester til en anden?

Den gratis version kan ikke overføres eller 'skaleret ' til et andet niveau, og ikke kan sikkerhedskopieres og gendannes til et andet niveau. Hvis du har brug for et andet niveau, kan du oprette en ny BizTalk Service ved hjælp af det nye niveau. Alle elementer, der er oprettet ved hjælp af den gratis version, herunder hybrid forbindelser, skal genskabes i den nye BizTalk Service. 

Brug sikkerhedskopien for de resterende udgaver og gendanne for at overføre dine elementer fra den ene del til en anden. For eksempel sikkerhedskopieres din elementer i det Standard niveau og derefter gendanne dem til Premium-niveau. [BizTalk Services: sikkerhedskopiere og gendanne](biztalk-backup-restore.md) beskrives de understøttede overførsel stier og viser, hvilke elementer der skal sikkerhedskopieres. Bemærk, at Hybrid forbindelser ikke sikkerhedskopieres. Når du sikkerhedskopiere og gendanne til et nyt lag, Genopret du derefter hybrid-forbindelser.  


#### <a name="is-the-biztalk-adapter-service-included-in-the-service-how-do-i-receive-the-software"></a>Er BizTalk Adapter Service medtages i tjenesten? Hvordan får jeg vist softwaren?

Ja, BizTalk Adapter Service med BizTalk Adapter Pack er inkluderet i Azure BizTalk Services SDK [hente](http://www.microsoft.com/download/details.aspx?id=39087).

## <a name="next-steps"></a>Næste trin

Hvis du vil oprette Azure BizTalk Services i portalen Azure, gå til [BizTalk-tjenester: klargøring ved hjælp af portalen Azure](biztalk-provision-services.md). For at starte oprettelsen af programmer skal du gå til [Azure BizTalk-tjenester](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="additional-resources"></a>Yderligere ressourcer
- [BizTalk-tjenester: Klargøring ved hjælp af portalen Azure](biztalk-provision-services.md)<br/>
- [BizTalk tjenester: Klargøring Status diagram](biztalk-service-state-chart.md)<br/>
- [BizTalk-tjenester: Dashboard, overvåge og skala faner](biztalk-dashboard-monitor-scale-tabs.md)<br/>
- [BizTalk tjenester: Sikkerhedskopiering og gendannelse](biztalk-backup-restore.md)<br/>
- [BizTalk tjenester: (throttling)](biztalk-throttling-thresholds.md)<br/>
- [BizTalk-tjenester: Udstedernavn og udsteder nøgle](biztalk-issuer-name-issuer-key.md)<br/>
- [Hvordan får jeg Start med at bruge Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
