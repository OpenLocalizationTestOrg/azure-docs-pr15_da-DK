<properties
   pageTitle="Programmet afhængighed skærm (ADM) i handlinger Management-pakken (OMS) | Microsoft Azure"
   description="Programmet afhængighed skærm (ADM) er en handlinger Management pakke (OMS)-løsning, der automatisk opdager programkomponenter på Windows og Linux-systemer og kort kommunikationen mellem tjenester.  Denne artikel indeholder oplysninger om installation af ADM i dit miljø og bruge det i en række forskellige scenarier."
   services="operations-management-suite"
   documentationCenter=""
   authors="daseidma"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/28/2016"
   ms.author="daseidma;bwren" />

# <a name="using-application-dependency-monitor-solution-in-operations-management-suite-oms"></a>Brug af programmet afhængighed skærm løsning i handlinger Management pakke (OMS)
![Administration af advarselsikon](media/operations-management-suite-application-dependency-monitor/icon.png) Programmet afhængighed skærm (ADM) opdager programkomponenter på Windows og Linux-systemer og kort kommunikationen mellem tjenester automatisk. Det kan du få vist dine servere, mens du betragte – som forbundne systemer, der leverer kritiske tjenester.  Programmet afhængighed skærm viser forbindelser mellem servere, processer, og porte på tværs af forskellige TCP-forbindelse arkitekturer med ingen konfiguration kræves dog installation af en agent.

I denne artikel beskriver oplysningerne om brug af programmet afhængighed skærm.  Oplysninger om konfiguration af ADM og onboarding supportmedarbejdere, se [konfiguration af programmet afhængighed skærm løsning i handlinger Management pakke (OMS)](operations-management-suite-application-dependency-monitor-configure.md)

>[AZURE.NOTE]Programmet afhængighed skærm er i øjeblikket i private preview.  Du kan anmode om adgang til ADM privat eksemplet på [https://aka.ms/getadm](https://aka.ms/getadm).
>
>Under privat eksempelvisning alle OMS-konti har ubegrænset adgang til ADM.  ADM noder er gratis, men Log Analytics-data for typerne AdmComputer_CL og AdmProcess_CL vil forbrugsafregnede som enhver anden løsning.
>
>Når ADM indtaster offentlige preview, bliver den kun tilgængelig for gratis og betalte kunder af indsigt og Analytics i kørslen OMS priser Plan.  Vil være begrænset til 5 ADM noder gratis niveau konti.  Hvis du deltager i private preview og er ikke registreret i kørslen OMS priser Plan, når ADM placeres i prøveversionen, deaktiveres ADM på det pågældende tidspunkt. 


## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Use Cases: Gøre den til din behandler afhængighed opmærksom på

### <a name="discovery"></a>Registrering
ADM opretter automatisk et almindelige reference kort over afhængigheder på tværs af dine servere, processer og 3 part tjenester.  Den opdager og kort alle TCP-afhængigheder, identificere overraskelse forbindelser, remote 3 part systemer du afhænger og afhængigheder til traditionel mørke områder i dit netværk som DNS og AD.  ADM opdager mislykkedes netværksforbindelser, din administrerede systemer forsøger at sikre, at hjælpe dig med at identificere potentielle server forkert konfiguration, serviceafbrydelser og netværksproblemer.

### <a name="incident-management"></a>Hændelsen administration
ADM hjælper med at undgå gætteriet problem isolationsniveauet ved at vise, hvordan systemer er tilsluttet og påvirker hinanden.  Ud over mislykkedes forbindelser at oplysninger om forbundne klienter identificere konfigureret forkert Indlæs balancere, overraskende eller unødvendig belastning på kritiske tjenester og uautoriserede klienter som udvikler maskiner taler med fremstilling systemer.  Integreret arbejdsprocesser med OMS ændre registrering også giver dig mulighed at se, om en ændring begivenhed på en back end-computer eller tjeneste beskriver den egentlige årsag en hændelse.

### <a name="migration-assurance"></a>Overførsel Assurance
ADM giver dig til effektivt at planlægge, fremskynde og validere Azure overførsler, mulighed for at sikre, at noget er efterladt, og der er ingen overraskelse udfald.  Du kan finde alle indbyrdes afhængige systemer, der har brug for at overføre sammen, vurdere systemkonfiguration og kapacitet og identificere, om et kørende system stadig fungerer brugere eller er en kandidat til udfase i stedet for overførsel.  Når Flyt er færdig, kan du se på klienten Indlæs og identitet for at bekræfte, at testsystemer og kunder opretter forbindelse.  Hvis dit undernet planlægnings- og firewall definitioner har problemer, peg mislykkedes forbindelser i ADM kort du på de systemer, der har brug for forbindelse.

### <a name="business-continuity"></a>Forretningskontinuitet
Hvis du bruger Azure gendannelse af websteder og har brug for hjælp, der definerer gendannelse rækkefølgen for dit programmiljø, ADM automatisk viser dig hvordan systemer, der er afhængige af hinanden for at sikre, at er din gendannelse plan pålidelig.  Ved at vælge en kritiske server og få vist dets klienter, kan du identificere de front end-systemer, der skal gendannes, når den kritiske server er gendannet og tilgængelig.  Du kan derimod ved at se på en kritiske server back end-afhængigheder, identificere disse systemer, der skal gendannes, før systemet fokus er gendannet.

### <a name="patch-management"></a>Håndtering af programrettelse
ADM forbedrer din brug af OMS System opdatering vurdering ved viser dig, som andre teams og servere, der er afhængige af din tjeneste, så du kan kontakte dem på forhånd, før du tage dine systemer til rettelse.  ADM forbedrer også programrettelse management i OMS ved at vise, om dine tjenester er korrekt forbundet når de er installeret for at udbedre og genstartes. 


## <a name="mapping-overview"></a>Oversigt over tilknytning
ADM supportmedarbejdere indsamle oplysninger om alle TCP-forbindelse processer på den server, hvor de er installeret, samt oplysninger om indgående og udgående forbindelser for hver proces.  Brug af listen over computer i venstre side af løsningen ADM, kan maskiner med ADM supportmedarbejdere vælges til visualisering af deres afhængigheder over en valgte tidsinterval.  Maskine afhængighed knytter fokus på en bestemt maskine, og få vist alle de computere, der er direkte TCP-klienter eller servere på denne computer.

![Oversigt over ADM](media/operations-management-suite-application-dependency-monitor/adm-overview.png)

Computere kan udvides på kortet for at vise de kørende processer med aktive netværksforbindelser under det valgte tidsinterval.  Når en fjerncomputer med en ADM agent er udvidet til at vise oplysninger om processen, vises kun disse processer, kommunikere med fokus maskinen.  Antallet af uafhængig front end-computere at oprette forbindelse til fokus maskine angives i venstre side af de processer, de opretter forbindelse til.  Hvis fokus maskinen laver en forbindelse til en back end-maskine uden en agent, back end-repræsenteres med en node i kortet, der viser dets IPv4-adresse, og noden kan udvides for at få vist individuelle porte og -tjenester, der fokus maskinen kommunikerer med.

ADM kort viser de sidste 10 minutter af oplysninger om objektafhængigheder som standard.  Ved hjælp af tid kontrolelementerne øverst til venstre, kan kort forespørges for historiske tidsintervaller, op til en time hele, til at vise, hvordan afhængigheder set tidligere, f.eks. under en hændelse eller før en blev ændret.    ADM data er gemt i 30 dage i betalt arbejdsområder, og til 7 dage i gratis arbejdsområder.

![Maskine kort med valgte maskine egenskaber](media/operations-management-suite-application-dependency-monitor/machine-map.png)

## <a name="failed-connections"></a>Mislykkede forbindelser
Forbindelser med fejl vises i ADM-kort for processer og computere, med en stiplet rød linje viser Hvis et klientsystem går ned, når en proces eller port.  Mislykkede forbindelser rapporteres fra en hvilken som helst systemet med en udløst ADM agent, hvis systemet er den person, der forsøger at oprette mislykkedes forbindelse.  ADM måler dette ved at overvåge TCP sockets, der ikke kunne oprette en forbindelse.  Det kan skyldes en firewall, en forkert konfiguration i klienten eller server eller en remote tjeneste, der ikke tilgængelig. 

![Mislykkede forbindelser](media/operations-management-suite-application-dependency-monitor/failed-connections.png)

Forstå mislykkedes forbindelser kan hjælpe dig med fejlfinding af overførsel validering, sikkerhedsanalyse og totale arkitektonisk forstå.  Nogle gange mislykkedes forbindelser er skadelige, men de ofte henviser direkte til et problem, som et failover miljø pludselig blive er utilgængelig, … eller to program niveauer at kigge efter en skyen overførsel.  IIS og WebSphere begge kører i billedet ovenfor, men de kan ikke oprette forbindelse. 

## <a name="computer-and-process-properties"></a>Computer og egenskaber for proces
Når du navigerer en ADM tilknytning, kan du vælge-computere og processer til at få yderligere kontekst om deres egenskaber.  Maskiner indeholder oplysninger om DNS-navn, IPv4-adresser, CPU og hukommelse kapacitet, VM Type, operativsystemversion, sidste genstart tid og id'erne for deres OMS og ADM supportmedarbejdere.

Proces oplysninger der indsamles fra operativsystemet metadata om at køre processer, herunder procesnavn, Procesbeskrivelse, brugernavn og domæne (på Windows), firmanavn, produktnavn, produktversion, arbejdsmappe, kommandolinjen og proces starttidspunkt.

![Egenskaber for proces](media/operations-management-suite-application-dependency-monitor/process-properties.png)

Panelet Oversigt over processen giver ekstra oplysninger om denne proces connectivity, herunder dets bundne porte, indgående og udgående forbindelser, og mislykkedes forbindelser. 

![Oversigt over processen](media/operations-management-suite-application-dependency-monitor/process-summary.png)

## <a name="oms-change-tracking-integration"></a>OMS registrering Integration
ADM'S integration med registrering af ændringer er automatisk, når begge løsninger er aktiveret og konfigureret i arbejdsområdet OMS.

Panelet maskine oversigt angiver, om ændringsregistrering hændelser er indtruffet på den markerede maskine under det valgte tidsinterval.

![Maskine oversigt Panel](media/operations-management-suite-application-dependency-monitor/machine-summary.png)

Panelet maskine Skift sporing viser en liste over alle ændringer med den seneste første sammen med et link til at analysere i Log søge efter yderligere oplysninger.
![Maskine Skift sporing Panel](media/operations-management-suite-application-dependency-monitor/machine-change-tracking.png)

Følgende er en detailudledning visning af hændelsen konfigurationsændring efter valg af **Vis i Log analyser**.
![Ændre konfigurationen](media/operations-management-suite-application-dependency-monitor/configuration-change-event.png)


## <a name="log-analytics-records"></a>Log Analytics poster
ADM'S computer og proces lagerdata er tilgængelig for [søgning](../log-analytics/log-analytics-log-searches.md) i Log analyser.  Dette kan anvendes på scenarier, herunder overførsel planlægning, kapacitetsanalyse, registrering og foretage fejlfinding af ad hoc-ydeevne. 

Én post er oprettet i timen for hver entydige computer, og processen ud over poster oprettes når, proces eller computer, der starter eller er på-ske til ADM.  Disse poster har egenskaber i tabellerne nedenfor. 

Der er internt genererede egenskaber, der kan bruges til at identificere entydige processer og computere:

- PersistentKey_s er sikret defineret af proces konfiguration, fx kommandolinjen og bruger-id.  Det er entydige for en given computer, men du kan gentage på tværs af computere.
- ProcessId_s og ComputerId_s er globalt entydige i ADM modellen.



### <a name="admcomputercl-records"></a>AdmComputer_CL poster
Poster med en type af **AdmComputer_CL** har lagerdata for servere med ADM supportmedarbejdere.  Disse poster har egenskaber i den følgende tabel.  

| Egenskaben | Beskrivelse |
|:--|:--|
| Type | *AdmComputer_CL* |
| SourceSystem | *OpsManager* |
| ComputerName_s | Windows eller Linux computernavn |
| CPUSpeed_d | CPU-hastighed i MHz |
| DnsNames_s | Liste over alle DNS-navne for denne computer |
| IPv4s_s | Liste over alle IPv4-adresser i brug af denne computer |
| IPv6s_s | Liste over alle IPv6-adresser i brug af denne computer.  (ADM identificerer IPv6-adresser, men opdage ikke IPv6 afhængigheder). |
| Is64Bit_b | SAND eller FALSK, der er baseret på OS type |
| MachineId_s | En intern GUID, der er entydige på tværs af et OMS arbejdsområde  |
| OperatingSystemFamily_s | Windows eller Linux |
| OperatingSystemVersion_s | Lang OS versionsstreng |
| TimeGenerated | Dato og klokkeslæt, der er oprettet i en post. |
| TotalCPUs_d | Antallet af CPU kerner |
| TotalPhysicalMemory_d | Hukommelseskapacitet i MB |
| VirtualMachine_b | SAND eller FALSK, der er baseret på om OS er gæst VM |
| VirtualMachineID_g | Hyper-V VM-ID |
| VirtualMachineName_g | Hyper-V VM navn |
| VirtualMachineType_s | Hyperv, Vmware, Xen, Kvm, Ldom, Lpar, Virtualpc |


### <a name="admprocesscl-type-records"></a>AdmProcess_CL Type poster 
Poster med en type af **AdmProcess_CL** har lagerdata for TCP-forbindelse processer på servere med ADM supportmedarbejdere.  Disse poster har egenskaber i den følgende tabel.

| Egenskaben | Beskrivelse |
|:--|:--|
| Type | *AdmProcess_CL* |
| SourceSystem | *OpsManager* |
| CommandLine_s | Fuld kommandolinjen over processen |
| CompanyName_s | Virksomhedens navn (fra Windows PE eller Linux RPM) |
| Description_s | Lang Procesbeskrivelse (fra Windows PE eller Linux RPM) |
| FileVersion_s | Eksekverbar filversion (fra Windows PE, kun Windows) |
| FirstPid_d | OS proces-ID |
| InternalName_s | Eksekverbar fil interne navn (fra Windows PE, kun Windows) |
| MachineId_s | Interne GUID entydige på tværs af et OMS arbejdsområde  |
| Name_s | Det eksekverbare procesnavn |
| Path_s | Stien i filsystemet på den eksekverbare fil proces |
| PersistentKey_s | Interne GUID entydige inden for denne computer |
| PoolId_d | Interne ID for sammenlægning af processer, der er baseret på lignende kommandoen linjer. |
| ProcessId_s | Interne GUID entydige på tværs af et OMS arbejdsområde  |
| ProductName_s | Produkt navn streng (fra Windows PE eller Linux RPM) |
| ProductVersion_s | Produkt versionsstreng (fra Windows PE eller Linux RPM) |
| StartTime_t | Processen starttidspunktet på lokale computerens ur |
| TimeGenerated | Dato og klokkeslæt, der er oprettet i en post. |
| UserDomain_s | Domænet for processen ejer (kun Windows) |
| UserName_s | Navnet på processen ejer (kun Windows) |
| WorkingDirectory_s | Processen arbejdsmappe |


## <a name="sample-log-searches"></a>Eksempel log søgninger

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Liste over fysiske hukommelseskapaciteten af alle administrerede computere. 
Skriv = AdmComputer_CL | Vælg TotalPhysicalMemory_d, ComputerName_s | Dedup ComputerName_s

![Eksempel med ADM en opdateringsforespørgsel](media/operations-management-suite-application-dependency-monitor/adm-example-01.png)

### <a name="list-computer-name-dns-ip-and-os-version"></a>Listen computernavn, DNS, IP- og OS version.
Skriv = AdmComputer_CL | Vælg ComputerName_s, OperatingSystemVersion_s, DnsNames_s, IPv4s_s | dedup ComputerName_s

![Eksempel med ADM en opdateringsforespørgsel](media/operations-management-suite-application-dependency-monitor/adm-example-02.png)

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Finde alle processer med "sql" i kommandolinjen
Skriv = AdmProcess_CL CommandLine_s = \*sql\* | dedup ProcessId_s

![Eksempel med ADM en opdateringsforespørgsel](media/operations-management-suite-application-dependency-monitor/adm-example-03.png)

### <a name="after-viewing-event-data-for-given-process-use-its-machine-id-to-retrieve-the-computers-name"></a>Når du har set begivenhed data for den pågældende, bruge sin computer-ID til at hente computerens navn
Skriv = AdmComputer_CL "m!m-9bb187fa-e522-5f73-66d2-211164dc4e2b" | Forskellige ComputerName_s

![Eksempel med ADM en opdateringsforespørgsel](media/operations-management-suite-application-dependency-monitor/adm-example-04.png)

### <a name="list-all-computers-running-sql"></a>Liste over alle computere, der kører SQL
Skriv = AdmComputer_CL MachineId_s i {Type = AdmProcess_CL \*sql\* | Entydige MachineId_s} | Forskellige ComputerName_s

![Eksempel med ADM en opdateringsforespørgsel](media/operations-management-suite-application-dependency-monitor/adm-example-05.png)

### <a name="list-of-all-unique-product-versions-of-curl-in-my-datacenter"></a>Liste over alle entydigt produkt-versioner af krøllet i min datacenter
Skriv = AdmProcess_CL Name_s = krøllet | Forskellige ProductVersion_s

![Eksempel med ADM en opdateringsforespørgsel](media/operations-management-suite-application-dependency-monitor/adm-example-06.png)

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Opret en Computer gruppe af alle computere, der kører CentOS

![Eksempel med ADM en opdateringsforespørgsel](media/operations-management-suite-application-dependency-monitor/adm-example-07.png)



## <a name="diagnostic-and-usage-data"></a>Diagnosticerings- og brugen af data
Microsoft indsamler automatisk brugen og ydeevne data via din brug af tjenesten programmet afhængighed skærm. Microsoft bruger disse Data til at give og forbedre kvaliteten, sikkerhed og integriteten af tjenesten programmet afhængighed skærm. Data indeholder oplysninger om konfiguration af din software som operativsystem og version og indeholder også IP-adresse, DNS-navn og arbejdsstation navn for at give præcise og effektive fejlfinding funktioner. Vi indsamler ikke navne, adresser eller andre kontaktoplysninger.

Flere oplysninger om dataindsamling og -format, skal du se [Microsoft Online Services-erklæring](hhttps://go.microsoft.com/fwlink/?LinkId=512132).



## <a name="next-steps"></a>Næste trin
- Få flere oplysninger om [logge søgninger](../log-analytics/log-analytics-log-searches.md] in Log Analytics to retrieve data collected by Application Dependency Monitor.)
