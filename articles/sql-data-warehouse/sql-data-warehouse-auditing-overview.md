<properties
   pageTitle="Overvågning i Azure SQL datawarehouse | Microsoft Azure"
   description="Introduktion til overvågning i Azure SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/24/2016" 
   ms.author="rortloff;barbkess;sonyama"/>

# <a name="auditing-in-azure-sql-data-warehouse"></a>Overvågning i Azure SQL datawarehouse

> [AZURE.SELECTOR]
- [Overvågning](sql-data-warehouse-auditing-overview.md)
- [Truslen registrering](sql-data-warehouse-security-threat-detection.md)

SQL Data Warehouse overvågning, kan du post begivenheder i din database til en revision log på kontoen Azure-lager. Overvågning kan hjælpe dig med at bevare overholdelse af lovgivningen, forstå databaseaktivitet og få indsigt i uoverensstemmelser og afvigelser, der kan angive business problemstillinger eller mulig overtrædelse af sikkerhed. Overvågning af SQL Data Warehouse også integreres med Microsoft Power BI til analysere ned rapportering og analyse.

Overvågning værktøjer aktivere og lette overholdelse overholdelse standarder men ikke garantere overholdelse af angivne standarder. Du kan finde flere oplysninger om Azure programmer, der understøtter overholdelse af standarder, <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Azure Center for sikkerhed og rettighedsadministration</a>.

+ [Grundlæggende om databaser overvågning]
+ [Konfigurere overvågning for din database]
+ [Analysere overvågningslogge og rapporter]

##<a id="subheading-1"></a>Grundlæggende om Azure SQL Data Warehouse Database overvågning


SQL Data Warehouse database overvågning, kan du:

- **Behold** et revisionsspor for markerede hændelser. Du kan definere kategorier af databasehandlinger til at blive overvåget.
- **Rapport** over databaseaktivitet. Du kan bruge forudkonfigurerede rapporter og et dashboard til at komme hurtigt i gang med aktivitet og begivenhed rapportering.
- **Analysér** rapporter. Du kan finde mistænkelige begivenheder, usædvanlige aktivitet og tendenser.

Du kan konfigurere overvågning for begivenhed følgende kategorier:

**Almindelig SQL** og **Parameteriseret SQL** , der indsamles overvågningslog logger er klassificeret som  

- **Adgang til data**
- **Skemaændringer (DDL)**
- **Dataændringer (DML)**
- **Konti, roller og tilladelser (listen)**
- **Gemt Procedure**, **logon** og **håndtering af**.

Overvågning af **succes** og **manglende** handlinger er konfigureret separat for hver begivenhed kategori.

Du kan finde yderligere oplysninger om de aktiviteter og arrangementer overvåget <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">Overvågningslog Log Reference-Format (dokument filoverførsel)</a>.

Overvågningslogge er gemt på kontoen Azure-lager. Du kan angive en overvågningslog log opbevaringsperiode.

En politik til overvågning kan defineres for en bestemt database eller som en server standardpolitik. En overvå serverpolitikken til standard gælder for alle databaser på en server, som ikke har en bestemt tilsidesætte database overvå politik defineret.

Før indstillingen Overvåg op overvågning afkrydsningsfelt, hvis du bruger en ["bagudkompatible klienter"](sql-data-warehouse-auditing-downlevel-clients.md).


##<a id="subheading-2"></a>Konfigurere overvågning for din database

1. Start <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>.

2. Gå til bladet konfiguration af SQL Data Warehouse databasen / SQL Server, du vil overvåge. Klik på knappen **Indstillinger** øverst og derefter i bladet indstilling, og vælg **overvågning**.

    ![][1]

3. I bladet overvå konfiguration først fjerne markeringen af afkrydsningsfeltet **Nedarver overvågning indstillinger fra Server** . Dette kan du angive indstillinger for en bestemt database.

    ![][2]

4. Derefter skal du aktivere overvågning ved at klikke på knappen **på** .

    ![][3]

5. Vælg **LAGERPLADS detaljer** for at åbne bladet overvågningslog logfiler lagerplads i bladet overvå konfiguration. Vælg kontoen Azure-lager, hvor logfiler skal gemmes, og opbevaringsperioden. **Tip:** Brug den samme konto lagerplads for alle reviderede databaser til at få mest muligt ud af skabelonerne forudkonfigurerede rapporter.

    ![][4]

6. Klik på knappen **OK** for at gemme lagerplads oplysninger om konfiguration.


7. Under **Logføring af BEGIVENHED**, skal du klikke på **VELLYKKEDE** og **MISLYKKEDE** for at logge alle begivenheder eller vælge enkelt begivenhed kategorier.


8. Hvis du vil konfigurere overvågning til en database, skal du muligvis ændre forbindelsesstrengen for din klient til sikre data overvågning registreres korrekt. Se emnet med den [Ændre Server FDQN i forbindelsesstrengen](sql-data-warehouse-auditing-downlevel-clients.md) for bagudkompatible klientforbindelser.

9. Klik på **OK**.


##<a id="subheading-3">Analysere overvågningslogge og rapporter</a>

Overvågningslogge samles i en samling af Store tabeller med præfikset **SQLDBAuditLogs** i kontoen Azure lagerplads du har valgt under installationen. Du kan få vist logfilerne ved hjælp af et værktøj som <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Azure Storage Explorer</a>.

En forudkonfigureret dashboard rapportskabelon er tilgængelig som et <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">Excel-regneark, der kan downloades</a> kan hjælpe dig med hurtig analyse af logdata. Hvis du vil bruge skabelonen på din overvågningslogge, du skal bruge Excel 2013 eller nyere og Power-forespørgsel, som du kan hente <a href="http://www.microsoft.com/download/details.aspx?id=39379">her</a>.

Skabelonen er fiktivt eksempeldata, og du kan konfigurere Power-forespørgsel til at importere din overvågningsloggen direkte fra kontoen Azure-lager.

Læs finde mere detaljerede oplysninger om at arbejde med rapportskabelonen, <a href="http://go.microsoft.com/fwlink/?LinkId=506731">Sådan (dokument download)</a>.

![][5]


##<a id="subheading-4">Fremgangsmåder for at få hjælp i fremstilling</a>
Beskrivelsen i dette afsnit refererer til skærmbillederne ovenfor. <a href="https://portal.azure.com" target="_blank">Azure-portalen</a> eller <a href= "https://manage.windowsazure.com/" target="_bank">Klassisk Azure klassisk Portal</a> kan bruges.


##<a id="subheading-5"></a>Lagerplads nøgler

Fremstilling har du sandsynligvis at opdatere dine lagerplads nøgler med jævne mellemrum. Når opdatere dine nøgler skal du gemme politikken igen. Processen er som følger:.


1. Skift **Lagerplads adgangsnøgle** fra *primære* *sekundær* og **gemme**i overvå konfiguration bladet (beskrevet ovenfor i gruppen Konfigurer overvågning afsnit).
![][4]
2. Gå til den lagerplads konfiguration blade og for at **gendanne** den *Primærnøgle i Access*.

3. Gå tilbage til bladet overvågning konfiguration skifte **Lagerplads adgangsnøgle** fra *sekundær* til *primære* og trykke på **Gem**.

4. Gå tilbage til den lagerplads brugergrænseflade og for at **gendanne** den *Sekundære hurtigtast* (som forberedelse til den næste taster opdateringscyklus.

##<a id="subheading-6"></a>Automatisering
Der findes flere PowerShell-cmdlet'er du kan bruge til at konfigurere overvågning i Azure SQL-Database. For at få adgang til de cmdletter til overvågning skal du køre PowerShell i Azure ressourcestyring tilstand.

> [AZURE.NOTE] Modulet [Azure ressourcestyring](https://msdn.microsoft.com/library/dn654592.aspx) er i øjeblikket i Vis udskrift. Det kan ikke angive de samme funktioner som modulet Azure.

Når du er i Azure ressourcestyring tilstand, skal du køre `Get-Command *AzureSql*` med listen over tilgængelige cmdletter.


<!--Anchors-->
[Grundlæggende om databaser overvågning]: #subheading-1
[Konfigurere overvågning for din database]: #subheading-2
[Analysere overvågningslogge og rapporter]: #subheading-3


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png


<!--Link references-->
