<properties
   pageTitle="Data warehouse arbejdsbelastning"
   description="SQL Data Warehouse Elasticitet gør det muligt at vokse, formindske eller peg Beregn power ved hjælp af en glidende skala af data warehouse enheder (DWUs). Denne artikel forklares data warehouse målene, og hvordan de relaterer til DWUs. "
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/25/2016"
   ms.author="barbkess;mausher;jrj;sonyama"/>


# <a name="data-warehouse-workload"></a>Data warehouse arbejdsbelastning
En data warehouse arbejdsbyrde refererer til alle de funktioner, der gå mod et datawarehouse. Data warehouse arbejdsbelastningen omfatter hele processen med at indlæse data i lageret, udføre analyser og rapportering om et datalager, administrere data i et datalager og eksportere data fra et datalager. Dybdeakse og bredden af disse komponenter er ofte svarer til udløbsdato niveauet i et datalager.


## <a name="new-to-data-warehousing"></a>Ny til opbevaring af data?
Et datawarehouse er en samling af data, der er indlæst fra en eller flere datakilder og bruges til at udføre business intelligence som rapportering og analyse.

Data lagre genkendes ved forespørgsler, der scan større antal rækker, store dataområder og kan returnere relativt store resultater med henblik på analyse og rapportering. Data lagre genkendes også ved relativt store data indlæse kontra small transaktion niveau indsætter/opdateringer/sletter.

- Et datawarehouse udfører bedst, når dataene er gemt på en måde, som optimerer forespørgsler, der har brug for at scanne stort antal rækker eller store dataområder. Denne type scanning fungerer bedst, når dataene er gemt og søger efter kolonner, i stedet for efter rækker.

>[AZURE.NOTE] Indekset i hukommelsen columnstore, som bruger kolonne lagerplads, indeholder op til 10 x komprimering gevinst og 100 x forespørgsel ydeevne over traditionelle binære træer til rapportering og analyse forespørgsler. Vi bør du overveje columnstore indeks som standard for at gemme og scanning store data i et datawarehouse.

- Et datawarehouse har forskellige krav end et system, som optimerer for online transaktion processing (OLTP). OLTP systemet har mange indsætte, opdatere og slette handlinger. Disse handlinger forsøge at bestemte rækker i tabellen. Tabel søger udføre bedst, når dataene er gemt i en række ad gangen måde. Dataene kan sorteres og hurtigt søger med en divider og indtage tilgang kaldet en binær træ eller btree søgning.


## <a name="data-loading"></a>Indlæsning af data
Indlæsning af data er en stor en del af data warehouse arbejdsbelastningen. Virksomheder, der har normalt et optaget OLTP system, der registrerer ændringer i løbet af arbejdsdagen, når kunder der genererer business transaktioner. Med jævne mellemrum, ofte på NAT. under et vedligeholdelsesvindue, transaktionerne flyttes eller kopieres til et datalager. Når dataene er i et datalager, kan analytikere udføre analyser og gøre forretningsbeslutninger på dataene.

- Traditionelt hedder processen med at indlæsning ETL til Udpak, transformation og Indlæs. Data, som regel skal være transformeret, så det er ensartet med andre data i et datalager. Virksomheder, der bruges tidligere dedikerede ETL servere til at udføre transformeringer. Nu skal du med sådanne hurtigt stort omfang parallel behandling indlæse data til SQL Data Warehouse først og udfør derefter transformeringer. Denne proces kaldes udtrække, indlæse og transformere (ELT), og bliver til en ny standard for data warehouse arbejdsbyrde.

> [AZURE.NOTE] Med SQL Server 2016, kan du nu udføre analyser i realtid på en OLTP-tabel. Dette erstatter ikke brug for et datawarehouse til at gemme og analysere data, men det ikke en metode til at udføre analysen i realtid.

### <a name="reporting-and-analysis-queries"></a>Rapportering og analyse forespørgsler
Rapportering og analyse forespørgsler ofte er inddelt i lille, medium og store baseret på en række kriterier, men det er som regel baseret på tid. I de fleste data lagre er der en blandet arbejdsbyrde af fast kører kontra længerevarende forespørgsler. I hver sag, er det vigtigt at finde ud af denne blanding og til at bestemme hyppigheden (time, hver dag, måned-end, kvartal end- og osv.). Det er vigtigt at forstå, arbejdsbelastningen blandede forespørgsel, der er forbundet til på dokumentsammenfald, kundeemne til stort kapacitet, planlægning af et datawarehouse.

- Kapacitetsplanlægning af, kan det være en kompleks opgave for en blandet forespørgsel arbejdsbyrde, især hvis du har brug for en lang overlappende tid til at føje kapacitet til et datalager. SQL Data Warehouse fjerner hastende planlægning af kapacitet, da du kan forøge og formindske databehandling, når som helst og siden lager og databehandling er bekræftet af uafhængig størrelse.

### <a name="data-management"></a>Datastyring
Det er vigtigt, at administrere data, især hvis du ved, at du muligvis løbe tør for plads på harddisken inden for kort tid. Data lagre inddele normalt dataene i beskrivende områder, der er gemt som partitioner i en tabel. Alle SQL Server-baserede programmer kan du flytte partitioner ind og ud af tabellen. Denne partition skifte kan du flytte ældre data til billigere lager og bevare de nyeste data, der er tilgængelige på online-lagerplads.

- Columnstore indeks understøtter partitioneret tabeller. For columnstore indeks der partitioneret tabeller bruges til datastyring og arkivering. For tabeller, der er gemt række-for-række har partitioner en større rolle i forespørgslens ydeevne.  

- PolyBase spiller en vigtig rolle i administration af data. Brug af PolyBase, har du mulighed for at arkivere ældre data til Hadoop eller Azure blob-lager.  Dette giver mange muligheder, da data, er stadig er online.  Det kan tage længere tid at hente data fra Hadoop, men fordeling af hentning tid kan opveje lagerplads omkostninger.

### <a name="exporting-data"></a>Eksport af data
En metode til at gøre data tilgængelige til rapporter og analyse er at sende data fra et datalager til servere dedikeret til kørsel af rapporter og analyse. Disse servere kaldes data marts. Du kan for eksempel allerede behandle rapportdata og derefter eksportere den fra et datalager til mange servere over hele verden, så det alment tilgængelig for kunder og analytikere.

- Til at oprette rapporter, hver NAT. kan du udfylde skrivebeskyttet reporting servere med et øjebliksbillede af de daglige data. Det giver større båndbredde til kunder og reducerer ressourcebehov Beregn på et datalager. Fra et sikkerhed aspekt data marts giver dig mulighed at reducere antallet af brugere, der har adgang til datawarehouse.
- For analyser, kan du enten opbygge en analysis-kube på et datalager og kører analysis i forhold til et datalager, eller før behandle data og eksportere den til analysis-serveren for yderligere analyser.

## <a name="next-steps"></a>Næste trin
Nu hvor du kender lidt om SQL Data Warehouse, lære, hvordan du kan hurtigt [oprette et SQL Data Warehouse][] og [indlæse eksempeldata][].

<!--Image references-->

<!--Article references-->
[indlæse eksempeldata]: ./sql-data-warehouse-load-sample-databases.md
[oprette et SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other web references-->
