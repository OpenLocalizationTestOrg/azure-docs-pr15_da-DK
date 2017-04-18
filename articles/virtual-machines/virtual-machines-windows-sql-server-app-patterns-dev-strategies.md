<properties
    pageTitle="SQL Server-program mønstre på FOS | Microsoft Azure"
    description="I denne artikel dækker programmet mønstre for SQL Server på Azure FOS. Den indeholder løsningsarkitekter og udviklere en foundation til god programmet arkitektur og design."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="luisherring"
    manager="jhubbard"
    editor=""
    tags="azure-service-management,azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="lvargas" />

# <a name="application-patterns-and-development-strategies-for-sql-server-in-azure-virtual-machines"></a>Programmet mønstre og udviklingsstrategier til SQL Server på Azure virtuelle computere

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="summary"></a>Oversigt:
Afgøre, hvilket program mønster eller mønstre, der skal bruge til dine SQL-Server-baserede programmer i Azure miljø er et vigtigt design beslutning og kræver en sikker forståelse af Sådan SQL Server og hver infrastrukturkomponent i Azure sammen. Med SQL Server på Azure infrastrukturtjenester, kan du nemt overføre, vedligeholde og overvåge dine eksisterende SQL Server-programmer bygget på Windows Server virtuelle maskiner i Azure.

Formålet med denne artikel er at tilvejebringe et fundament løsningsarkitekter og udviklere til god programmet arkitektur og design, som de kan følge, når du overfører eksisterende programmer til Azure, samt nye udviklingsprogrammer i Azure.

For hvert program mønster finder du en lokal scenarie, dens respektive skyen aktiverede løsning og de relaterede tekniske anbefalinger. I artiklen beskrives desuden Azure-specifikke udviklingsstrategier, så du kan designe dine programmer korrekt. Det anbefales, at arkitekter og udviklere skal vælge de mest relevante mønster for deres programmer og brugere på grund af de mange mulige programmet mønstre.

**Tekniske bidragydere:** Mikkel Carlos Vargas sild, Madhan Arumugam Ramakrishnan

**Tekniske korrekturlæsere:** Corey sandrør Drew McDaniel Narayan Annamalai, Nir Mashkowski, Sanjay Mishra, og Silvano Coriani, Stefans Schackow Tim Hickey, Tim Wieman, Xin Jin

## <a name="introduction"></a>Introduktion

Du kan udvikle mange typer n-delt programmer ved at adskille komponenterne i de forskellige programlag på forskellige computere samt som separate komponenter. For eksempel du kan placere klientprogrammet og business regler komponenter i én maskine, front end-niveau og data access niveau komponenter i en anden computer og en back end-databaseniveau i en anden computer. Denne type ved at strukturere hjælper med at isolere hvert niveau fra hinanden. Hvis du ændrer, hvor data kommer fra, skal du ikke ændre programmet klient på computeren eller internettet, men kun data access niveau komponenterne.

Et typisk *n-delt* program indeholder præsentation niveau, business niveau og data niveau:


| Niveau              | Beskrivelse                                                                                                                                                                     |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Præsentation** | *Præsentation niveau* (web niveau, front end-niveauet) er det lag, hvor brugere kommunikere med et program.                                                                      |
| **Business**     | Den *business klasse* (midterste niveau) er det lag, præsentationen klasse og data niveau bruger til at kommunikere med hinanden og omfatter kernefunktioner til systemet. |
| **Data**         | *Data niveau* er som den server, der gemmer et programs data (for eksempel en server, der kører SQL Server).                                                             |

Programlag beskriver de logiske grupperinger af de funktioner og komponenter i et program mens niveauer beskriver fysisk fordelingen af de funktioner og komponenter på separate fysiske servere, computere, netværk eller fjerne steder. Lag af et program kan være placeret i den samme fysiske computer (det samme niveau) eller kan være fordelt over en separat computere (n-delt), og komponenterne i hvert lag, kommunikere med komponenter i andre lag via klart defineret grænseflader. Du kan tænke på ord niveauet som henvisninger til fysisk distributionen mønstre som to niveauer, tredelt og n-niveau. Et **2-niveau programmet mønster** indeholder to niveauer af programmet: application server og database-server. Der sker direkte kommunikationen mellem application server og databaseserveren. Application server indeholder både web lag og business lag komponenter. I **programmet på computeren 3 lag mønster**, der er tre programmer niveauer:-server, programserver, som indeholder business logik niveau og/eller virksomheder niveau data access-komponenter, og databaseserveren. Kommunikationen mellem webserveren og databaseserveren sker over application server. Du kan finde detaljerede oplysninger om programlag og niveauer, [Microsoft Application arkitektur Guide](https://msdn.microsoft.com/library/ff650706.aspx).

Før du går i gang med at læse denne artikel, bør du have viden om de grundlæggende begreber i SQL Server og Azure. For at vide, skal du se [SQL Server bøger Online](https://msdn.microsoft.com/library/bb545450.aspx), [SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-server-iaas-overview.md) og [Azure.com](https://azure.microsoft.com/).

I denne artikel beskrives flere programmet mønstre, der kan være passer til dine enkle programmer samt meget komplekse enterprise-programmer. Før der beskriver hver mønster, anbefaler vi, at du skal lære at kende lagerplads tilgængelig datatjenester i Azure, som [Azure-lager](../storage/storage-introduction.md), [Azure SQL-Database](../sql-database/sql-database-technical-overview.md)og [SQL Server i en Azure Virtual Machine](virtual-machines-windows-sql-server-iaas-overview.md). For at få de bedste designbeslutninger til dine programmer, at forstå, hvornår du skal bruge hvilke lagerplads datatjeneste tydeligt.

### <a name="choose-sql-server-in-an-azure-virtual-machine-when"></a>Vælg SQL Server i en Azure virtuelt, når:

- Du skal have kontrol i SQL Server og Windows. Dette kan for eksempel indeholde SQL Server-version, speciel hotfixes, ydeevne konfiguration, osv.

- Du har brug for en fuld kompatibilitet med SQL Server lokalt og vil flytte eksisterende programmer til Azure som-er.

- Du kan udnytte mulighederne i Azure-miljø, men Azure SQL-Database understøtter ikke alle de funktioner, der kræver, at dit program. Dette kan omfatte følgende områder:

    - **Databasestørrelse**: denne artikel er opdateret samtidig SQL-Database understøtter en database med op til 1 TB data. Hvis programmet kræver mere end 1 TB data, og du ikke vil implementere brugerdefinerede sharding løsninger, anbefales det, at du bruger SQL Server i en Azure Virtual Machine. Se [Skalering af Azure SQL-databaser](https://msdn.microsoft.com/library/azure/dn495641.aspx) og [Azure SQL Database Service niveauer og ydeevne](../sql-database/sql-database-service-tiers.md)til de seneste oplysninger.
    - **Overholdelse af HIPAA**: sundhedssektoren kunder og uafhængige softwareleverandører (softwareudviklere) kan vælge [SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-server-iaas-overview.md) i stedet for [Azure SQL-Database](../sql-database/sql-database-technical-overview.md) , fordi SQL Server i en Azure Virtual Machine er dækket af HIPAA Business knytte aftale (BAA). Finde oplysninger om overholdelse af angivne standarder i [Microsoft Azure Trust Center: overholdelse](https://azure.microsoft.com/support/trust-center/compliance/).
    - **Forekomst niveau funktioner**: SQL-Database ikke på nuværende tidspunkt understøtter funktioner, der live uden for databasen (som sammenkædede servere Agent job, FileStream, Service Broker osv.). Få mere at vide under [Azure SQL Database retningslinjer og begrænsninger](https://msdn.microsoft.com/library/azure/ff394102.aspx).

## <a name="1-tier-simple-single-virtual-machine"></a>1-niveau (enkle): enkelt virtuelt

I dette program mønster installerer du den SQL Server-applikation og database til en enkeltstående virtuel maskine i Azure. Den samme virtuelle maskine indeholder din klient/webprogram, business-komponenter, data access lag og databaseserveren. Den præsentation, business og data pinkode adskilles logisk, men fysisk er placeret i en enkelt server maskine. De fleste kunder starter med dette program mønster og derefter de skalere ud ved at tilføje flere web roller eller virtuelle maskiner til deres system.

Dette program mønster er nyttig, når:

- Du vil udføre en simpel overførsel til Azure-platformen at evaluere, om platformen finder du svar på dit program krav eller ej.

- Du vil beholde alle niveauer for programmet, der findes i den samme virtuelle maskine i den samme Azure datacenter at reducere ventetid mellem niveauer.

- Du vil hurtigt klargøre udvikling og teste miljøer i korte perioder.

- Du vil udføre tests for arbejdsbelastningen varierende belastning, men samtidig du vil ikke at eje og vedligeholde mange fysiske maskiner hele tiden.

I det følgende diagram viser en enkel lokale scenarie, og hvordan du kan installere dens skyen aktiveret løsning i en enkelt virtuel maskine i Azure.

![1 lag programmet mønster](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728008.png)

Implementere business laget (forretningslogik og få adgang til komponenter data) på samme fysiske niveau som præsentation lag kan maksimere programmets ydeevne, medmindre du skal bruge en separat niveau på grund af skalerbarhed eller sikkerhed problemstillinger.

Da dette er en meget almindelig mønster skal starte med, kan være nyttige i følgende artikel på overførsel til at flytte dine data til din SQL Server VM: [overføre en Database til SQL Server på en Azure VM](virtual-machines-windows-migrate-sql.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>3-niveau (enkle): flere virtuelle maskiner

I dette program mønster, kan du installere en 3-niveau-programmet i Azure ved at placere hvert program niveau i en anden virtuel computer. Dette giver en fleksibel miljø til en nemt skalere op eller udvide scenarier. Når et virtuelt indeholder din klient/webprogram, den anden computer vært for business-komponenter, og den anden computer vært databaseserveren.

Dette program mønster er nyttig, når:

- Du vil udføre en overførsel af komplekse databaseprogrammer til virtuelle Azure-computere.

- Du vil have programmet på forskellige niveauer til at være placeret i forskellige områder. Du har muligvis delt databaser, der er installeret til flere områder til rapportering.

- Du vil flytte virksomhedens programmer fra lokale virtualiseret platforme til virtuelle Azure-computere. En detaljeret beskrivelse på virksomhedens programmer, kan du se [Hvad er et Enterprise-program](https://msdn.microsoft.com/library/aa267045.aspx).

- Du vil hurtigt klargøre udvikling og teste miljøer i korte perioder.

- Du vil udføre tests for arbejdsbelastningen varierende belastning, men samtidig du vil ikke at eje og vedligeholde mange fysiske maskiner hele tiden.

I det følgende diagram viser, hvordan du kan placere et enkelt lag 3-program i Azure ved at placere hvert program niveau i en anden virtuel computer.

![3-niveau programmet mønster](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728009.png)

I dette program mønster er der kun én virtuel maskine (VM) i hvert lag. Hvis du har flere FOS i Azure, anbefaler vi, at du konfigurerer et virtuelt netværk. [Azure virtuelt netværk](../virtual-network/virtual-networks-overview.md) opretter der er tillid til rammen og kan også FOS til at kommunikere indbyrdes via privat IP-adresse. Desuden altid Sørg for, at alle internetforbindelser kun gå til præsentation niveauet. Når følger dette program mønster, kan du administrere netværk sikkerhed gruppe regler for at styre adgangen. Du kan finde yderligere oplysninger finder [Tillad ekstern adgang til din VM ved hjælp af portalen Azure](virtual-machines-windows-nsg-quickstart-portal.md).

Internet-protokoller kan være TCP, UDP, HTTP eller HTTPS i diagrammet.

>[AZURE.NOTE] Konfiguration af et virtuelt netværk i Azure er gratis. Men du betaler for VPN-gateway, der opretter forbindelse til en lokal installation. Dette gebyr er baseret på mængden tid, der forbindelsen er klargjort og tilgængelig.

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>trin 2 og 3 lag med ud med præsentation niveau skala

I dette program mønster, kan du installere 2-niveau eller 3-niveau databaseprogram til Azure virtuelle maskiner ved at placere hvert program niveau i en anden virtuel computer. Desuden kan du skalere præsentation niveauet på grund af øget lydstyrken for indgående klient-anmodninger.

Dette program mønster er nyttig, når:

- Du vil flytte virksomhedens programmer fra lokale virtualiseret platforme til virtuelle Azure-computere.

- Du vil skalere ud af præsentation niveauet på grund af øget lydstyrken for indgående klient-anmodninger.

- Du vil hurtigt klargøre udvikling og teste miljøer i korte perioder.

- Du vil udføre tests for arbejdsbelastningen varierende belastning, men samtidig du vil ikke at eje og vedligeholde mange fysiske maskiner hele tiden.

- Vil du ejer et infrastruktur-miljø, der kan skalere op og ned efter behov.

I det følgende diagram viser, hvordan du kan placere programmet niveauer i flere virtuelle maskiner i Azure ved skalering af præsentation niveau på grund af øget lydstyrken for indgående klient-anmodninger. Som det ses i diagrammet, er Azure justering af belastning ansvarlig for distribuere trafik på tværs af flere virtuelle maskiner og også afgøre, hvilke webserver for at oprette forbindelse til. Har du flere forekomster af webserverne bag en belastningsjustering sikrer høj tilgængeligheden af præsentation niveauet.

![Programmet mønster - præsentation niveau Skaler ud](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Bedste fremgangsmåder til trin 2, 3-niveau eller n-niveau mønstre, der har flere FOS i ét niveau

Det anbefales, at du placerer de virtuelle maskiner, der hører til det samme niveau i den samme skytjeneste og i den samme tilgængeligheden angive. For eksempel placere et sæt-webserverne i **CloudService1** og **AvailabilitySet1** og et sæt databaseservere i **CloudService2** og **AvailabilitySet2**. En tilgængelighed, der er angivet i Azure gør det muligt at placere noderne høj tilgængelighed i separate fejl domæner og opgradere domæner.

For at udnytte flere VM forekomster af et trin, skal du konfigurere Azure justering af belastning mellem programmet på computeren. For at konfigurere belastning i hvert lag skal du oprette et netværksbelastningen slutpunkt på hvert niveau FOS separat. For et bestemt niveau, du først oprette FOS i samme skytjenesten. Dette sikrer, at de har den samme offentlige virtuelle IP-adresse. Dernæst skal du oprette et slutpunkt på en af virtuelle maskiner på dette niveau. Derefter kan du tildele det samme slutpunkt til de andre virtuelle maskiner på dette niveau til justering af belastning. Ved at oprette et netværksbelastningen sæt kan du distribuere trafik på tværs af flere virtuelle maskiner og giver mulighed for belastning til at bestemme, hvilke node for at oprette forbindelse, når en back end-VM node mislykkes. Eksempelvis sikrer har flere forekomster af webserverne bag en belastningsjustering høj tilgængeligheden af præsentation niveauet.

Som en bedste fremgangsmåde du altid Sørg for, at alle internetforbindelser først gå til præsentation niveauet. Laget præsentation har adgang til business-niveau, og derefter business niveau har adgang til data niveau. Du kan finde flere oplysninger om, hvordan du tillader adgang til præsentationen lag, [Tillad ekstern adgang til din VM ved hjælp af portalen Azure](virtual-machines-windows-nsg-quickstart-portal.md).

Bemærk, at belastning i Azure fungerer på samme måde at indlæse balancere i et lokalt miljø. Se [belastning til Azure infrastrukturtjenester](virtual-machines-windows-load-balance.md)kan finde flere oplysninger.

Desuden anbefaler vi, at du konfigurerer et privat netværk for virtuelle maskiner ved hjælp af Azure virtuelt netværk. Dette gør det muligt at kommunikere indbyrdes via privat IP-adresse. Du kan finde yderligere oplysninger finder [Azure virtuelt netværk](../virtual-network/virtual-networks-overview.md).

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>trin 2 og 3 lag med business klasse skala ud

I dette program mønster, kan du installere 2-niveau eller 3-niveau databaseprogram til Azure virtuelle maskiner ved at placere hvert program niveau i en anden virtuel computer. Desuden kan du distribuere server programkomponenter flere virtuelle maskiner på grund af kompleksiteten af dit program.

Dette program mønster er nyttig, når:

- Du vil flytte virksomhedens programmer fra lokale virtualiseret platforme til virtuelle Azure-computere.

- Du vil distribuere server programkomponenter flere virtuelle maskiner på grund af kompleksiteten af dit program.

- Du vil flytte business logik tungt lokale LOB (line of business) programmer til virtuelle Azure-computere. LOB programmer er et sæt af kritiske computerprogrammer, der er vigtig for at køre en virksomheds-, som accounting, hr-afdeling (t), løn, styring af forsyningskæde og ressourceplanlægning programmer.

- Du vil hurtigt klargøre udvikling og teste miljøer i korte perioder.

- Du vil udføre tests for arbejdsbelastningen varierende belastning, men samtidig du vil ikke at eje og vedligeholde mange fysiske maskiner hele tiden.

- Vil du ejer et infrastruktur-miljø, der kan skalere op og ned efter behov.

I det følgende diagram viser en lokal scenarie og dens skyen aktiveret løsning. I dette scenarie skal markere du programmet niveauer i flere virtuelle maskiner i Azure ved skalering af business lag, som indeholder business logik niveau og data access-komponenter. Som det ses i diagrammet, er Azure justering af belastning ansvarlig for distribuere trafik på tværs af flere virtuelle maskiner og også afgøre, hvilke webserver for at oprette forbindelse til. Har du flere forekomster af application servere bag en belastningsjustering sikrer høj tilgængeligheden af business-niveauet. Få mere at vide i [bedste fremgangsmåder til 2-niveau, 3-niveau, eller n-delt programmet mønstre, der har flere virtuelle maskiner i ét niveau](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Programmet mønster med business niveau Skaler ud](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>trin 2 og 3 lag med præsentation og business niveauer skala op- og HADR

I dette program mønster, kan du installere 2-niveau eller 3-niveau databaseprogram til Azure virtuelle maskiner ved at distribuere præsentationen niveau (webserver) og business niveau (programserver) komponenter flere virtuelle maskiner. Desuden kan implementere du løsninger til høj tilgængelighed og genoprettelse efter genoprettelse for dine databaser på Azure virtuelle computere.

Dette program mønster er nyttig, når:

- Du vil flytte virksomhedens programmer fra lokale virtuelt platforme til Azure ved at implementere SQL Server høj tilgængelighed og muligheder for genoprettelse efter genoprettelse.

- Du vil skalere præsentation niveauet og business niveau på grund af øget lydstyrken for indgående klient-anmodninger og kompleksiteten af dit program.

- Du vil hurtigt klargøre udvikling og teste miljøer i korte perioder.

- Du vil udføre tests for arbejdsbelastningen varierende belastning, men samtidig du vil ikke at eje og vedligeholde mange fysiske maskiner hele tiden.

- Vil du ejer et infrastruktur-miljø, der kan skalere op og ned efter behov.

I det følgende diagram viser en lokal scenarie og dens skyen aktiveret løsning. I dette scenarie skal skalere du præsentation niveauet og komponenterne i flere virtuelle maskiner i Azure niveau. Desuden kan implementere du høj tilgængelighed og genoprettelse efter genoprettelse (HADR) teknikker, så SQL Server-databaser i Azure.

Køre flere kopier af et program i forskellige Sørg FOS for, at du er anmodninger af belastning på tværs af dem. Når du har flere virtuelle maskiner, skal du sikre, at alle dine FOS er tilgængelige og, der kører på ét sted i gang. Hvis du konfigurerer justering af belastning, Azure belastning sporer tilstanden for FOS og omdirigerer indgående opkald til noderne sund fungerer VM korrekt. Oplysninger om, hvordan du konfigurerer belastning af virtuelle maskiner, se [til Azure infrastrukturtjenester justering af belastning](virtual-machines-windows-load-balance.md). Har du flere forekomster af web og programmet servere bag en belastningsjustering sikrer høj tilgængeligheden af præsentation og business niveauer.

![Tilgængelighed af skala ud og høj](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Bedste fremgangsmåder for programmet mønstre, der kræver SQL HADR

Når du konfigurerer SQL Server høj tilgængelighed og løsninger til genoprettelse efter genoprettelse i Azure virtuelle maskiner, er konfiguration af et virtuelt netværk for din virtuelle maskiner ved hjælp af [Azure virtuelt netværk](../virtual-network/virtual-networks-overview.md) obligatorisk.  Virtuelle maskiner med et virtuelt netværk har en stabil privat IP-adresse også efter en nedetid, og som derfor kan du undgå den opdateringstid, til DNS-navneoversættelse. Desuden kan du udvide dit lokale netværk til Azure virtuelle Netværks- og opretter en der er tillid til rammen. Eksempelvis hvis dit program har virksomhedens domæne begrænsninger (såsom, Windows-godkendelse, Active Directory), er konfigurerer [Azure virtuelt netværk](../virtual-network/virtual-networks-overview.md) nødvendigt.

De fleste af kunder, der kører fremstilling kode på Azure, er at holde både primære og sekundære replikaer i Azure.

Omfattende oplysninger og selvstudier på høj tilgængelighed og genoprettelse efter genoprettelsesteknikker, under [høj tilgængelighed og nedbrud til SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="2-tier-and-3-tier-using-azure-vms-and-cloud-services"></a>trin 2 og 3 lag ved hjælp af Azure FOS og Cloud Services

I dette program mønster, du installerer programmet på 2-niveau eller 3-niveau til Azure ved hjælp af begge [Azure Cloud Services](../cloud-services/cloud-services-choose-me.md#tellmecs) (internettet og arbejder roller - Platform som en tjeneste (PaaS)) og [virtuelle Azure-computere](virtual-machines-windows-about.md) (infrastruktur som en tjeneste (IaaS)). Bruge [Azure Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) for præsentation niveau/business niveau og SQL Server på [virtuelle Azure-computere](virtual-machines-windows-about.md) til niveauet af data er nyttige for de fleste programmer, der kører på Azure. Det skyldes, har du en Beregn forekomst, der kører på Cloud Services leverer en nem administration, installation, overvågning og skala ud.

Med Cloud Services Azure fører infrastrukturen for dig, udfører rutinemæssige vedligeholdelse, programrettelser operativsystemerne og forsøger at gendanne fra tjenesten og hardware mislykkede forsøg. Når dit program skal skala ud, vil automatiske og manuelle skala op-indstillinger, er tilgængelige for projektet skyen service ved at øge eller reducere antallet af forekomster eller virtuelle maskiner, der bruges af programmet. Desuden kan du bruge lokale Visual Studio til at installere programmet til en skybaseret tjeneste projekt i Azure.

I oversigt over, hvis du ikke vil ejer omfattende administrative opgaver til præsentation/virksomheder klasse og dit program ikke kræver en kompleks konfiguration af softwaren eller operativsystemet, kan du bruge Azure Cloud Services. Hvis Azure SQL-Database ikke understøtter alle de funktioner, du leder efter, du bruge SQL Server i en Azure Virtual Machine til data niveau. Køre et program på Azure Cloud Services og lagring af data i Azure virtuelle maskiner kombinerer fordelene ved at begge tjenester. I afsnittet i dette emne på [sammenligne udviklingsstrategier i Azure](#comparing-web-development-strategies-in-azure)en detaljeret sammenligning.

I dette program mønster omfatter præsentation niveau en web rolle, som er en komponent Cloud Services kører i Azure udførelse af miljøet og den er tilpasset til web application programmering, som understøttes af IIS og ASP.NET. Business eller back end-niveauet omfatter en kollega rolle, som er en komponent Cloud Services kører i Azure miljøet, og det er nyttigt for generel udvikling, og kan udføre behandling i baggrunden for en web rolle. Niveauet database er placeret i en SQL Server virtuel maskine i Azure. Kommunikationen mellem præsentation niveau og databaseniveau sker direkte eller over business niveauet – arbejder rolle komponenter.

Dette program mønster er nyttig, når:

- Du vil flytte virksomhedens programmer fra lokale virtuelt platforme til Azure ved at implementere SQL Server høj tilgængelighed og muligheder for genoprettelse efter genoprettelse.

- Vil du ejer et infrastruktur-miljø, der kan skalere op og ned efter behov.

- Azure SQL-Database understøtter ikke alle de funktioner, som dit program eller en database skal.

- Du vil udføre tests for arbejdsbelastningen varierende belastning, men samtidig du vil ikke at eje og vedligeholde mange fysiske maskiner hele tiden.

I det følgende diagram viser en lokal scenarie og dens skyen aktiveret løsning. I dette scenarie skal markere du præsentation niveauet i web roller, business niveau i arbejder roller, men data niveauet i virtuelle maskiner i Azure. Køre flere kopier af præsentation niveauet i forskellige web roller sikrer for at indlæse balance anmodninger på tværs af dem. Når du kombinerer Azure Cloud Services med Azure virtuelle maskiner, anbefaler vi, at du også konfigurere [Azure virtuelt netværk](../virtual-network/virtual-networks-overview.md) . Med [Azure virtuelt netværk](../virtual-network/virtual-networks-overview.md), kan du få stabil og fast private IP-adresser i den samme skybaseret tjeneste i skyen. Når du definerer et virtuelt netværk for din virtuelle computere og tjenester i skyen, kan de begynde kommunikerer indbyrdes via privat IP-adresse. Desuden indeholder har virtuelle maskiner og Azure web/arbejder roller i det samme [Azure virtuelt netværk](../virtual-network/virtual-networks-overview.md) lav ventetid og mere sikker forbindelse. Du kan finde flere oplysninger, kan du se [Hvad er en skybaseret tjeneste](../cloud-services/cloud-services-choose-me.md).

Som det ses i diagrammet, Azure justering af belastning distribuerer trafik på tværs af flere virtuelle maskiner og også bestemmer, hvilke webserver eller programserver i at oprette forbindelse til. Har du flere forekomster af web og programmet servere bag en belastningsjustering sikrer høj tilgængeligheden af præsentation niveau og business niveau. Få mere at vide i [bedste fremgangsmåder for programmet mønstre, der kræver SQL HADR](#best-practices-for-application-patterns-requiring-sql-hadr).

![Programmet mønstre med Cloud Services](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728013.png)

En anden metode til at implementere dette program mønster er at bruge en konsoliderede web rolle, der indeholder både præsentation niveau og business niveau komponenter som vist i følgende diagram. Dette program mønster er nyttigt til programmer, der kræver med høj sikkerhed design. Da Azure leverer uden status Beregn noder på internettet og arbejder roller, anbefales det, at du implementerer et logik for at gemme session stat ved hjælp af en af følgende teknologier: [Azure cachelagring](https://azure.microsoft.com/documentation/services/redis-cache/), [Azure Table Storage](../storage/storage-dotnet-how-to-use-tables.md) eller [Azure SQL-Database](../sql-database/sql-database-technical-overview.md).

![Programmet mønstre med Cloud Services](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728014.png)

## <a name="pattern-with-azure-vms-azure-sql-database-and-azure-app-service-web-apps"></a>Et mønster med Azure FOS, Azure SQL-Database og Azure App Service (online)

Det primære mål med dette program mønster er at viser, hvordan du kombinerer Azure infrastruktur som en tjeneste (IaaS) komponenter med Azure platform som en tjeneste komponenter (PaaS) i din løsning. Dette mønster fokuserer på Azure SQL-Database til lagring af relationelle data. Det omfatter ikke SQL Server på en Azure virtuel computer, som er en del af Azure-infrastrukturen som en servicetilbud.

I dette program mønster installerer du et databaseprogram til Azure ved at placere præsentation og business niveauer i den samme virtuelle maskine og få adgang til en database i Azure SQL-Database (SQL-Database)-servere. Du kan implementere præsentation niveau ved hjælp af traditionelle IIS-baserede webløsninger. Eller du kan implementere en kombinerede præsentation og business niveau ved hjælp af [Azure Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/).

Dette program mønster er nyttig, når:

- Du har allerede en eksisterende SQL-databaseserver konfigureret i Azure, og du vil teste dit program hurtigt.

- Du vil teste funktionerne i Azure-miljø.

- Du vil hurtigt klargøre udvikling og teste miljøer i korte perioder.

- Din business logik og data access-komponenter kan være selvstændig inden for et webprogram.

I det følgende diagram viser en lokal scenarie og dens skyen aktiveret løsning. I dette scenarie skal markere du programmet niveauer i en enkelt virtuel maskine i Azure og adgang til data i Azure SQL-Database.

![Blandede programmet mønster](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728015.png)

Hvis du vælger at implementere en kombinerede internettet og programmet niveau ved hjælp af Azure Web Apps, anbefaler vi, at du beholder det midterste niveau eller program niveau som DLL-biblioteker (DLL-filer) i forbindelse med et webprogram.

Desuden Gennemse de anbefalinger, som findes i afsnittet [sammenligne web development strategier i Azure](#comparing-web-development-strategies-in-azure) i slutningen af denne artikel for at lære mere om programmering teknikker.

## <a name="n-tier-hybrid-application-pattern"></a>N-delt hybrid programmet mønster

Du kan implementere dit program i flere niveauer, der er distribueret mellem lokale og Azure i n-delt hybrid programmet mønster. Derfor kan du oprette en fleksibel og der kan genbruges hybrid system, som du kan ændre eller tilføje et bestemt niveau uden at ændre de øvrige lag. Hvis du vil udvide virksomhedens netværk til skyen, kan du bruge [Azure virtuelle](../virtual-network/virtual-networks-overview.md) Netværkstjeneste.

Hybrid programmet mønsteret er nyttig, når:

- Du kan bygge programmer, der kører delvist i skyen og delvist on-premises.

- Du vil overføre nogle eller alle elementer på et eksisterende lokalt program til skyen.

- Du vil flytte virksomhedens programmer fra lokale virtualiseret platforme til Azure.

- Vil du ejer et infrastruktur-miljø, der kan skalere op og ned efter behov.

- Du vil hurtigt klargøre udvikling og teste miljøer i korte perioder.

- Du vil have en rentable måde at tage sikkerhedskopier til enterprise databaseprogrammer.

I det følgende diagram viser en n-delt hybrid programmet mønster, der strækker sig over flere lokale og Azure. Som vist i diagrammet, omfatter infrastruktur til lokale [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) domænenavn fra domænecontrolleren for at understøtte brugergodkendelse og godkendelse. Bemærk, at diagrammet viser et scenarie, hvor nogle dele af niveauet data direkte i et lokalt datacenter, mens nogle dele af niveauet data direkte i Azure. Du kan implementere flere andre hybrid scenarier, afhængigt af dit program behov. For eksempel kan du holde præsentationen niveau og business niveau i et lokalt miljø, men data niveauet i Azure.

![N-delt programmet mønster](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728016.png)

Du kan bruge Active Directory som en enkeltstående skyen mappe til din organisation i Azure, eller du kan også integrere eksisterende lokale Active Directory med [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Som det ses i diagrammet, business niveau komponenter kan få adgang til flere datakilder, f.eks til [SQL Server på Azure](virtual-machines-windows-sql-server-iaas-overview.md) via en privat interne IP-adresse, til lokal SQL Server via [Azure virtuelt netværk](../virtual-network/virtual-networks-overview.md)eller til [SQL-Database](../sql-database/sql-database-technical-overview.md) ved hjælp af .NET Framework data-provider teknologier. I dette diagram er Azure SQL-Database en valgfri data lagerplads.

Du kan implementere følgende arbejdsprocessen i den rækkefølge, der er angivet i n-delt hybrid programmet mønster:

1. Identificere enterprise databaseprogrammer, der skal flyttes til skyen ved hjælp af [Microsoft vurdering og planlægning (kort) værktøjskassen](http://microsoft.com/map). KORT værktøjerne samler lager og ydeevne data fra computere, du overvejer for virtualization og giver anbefalinger til kapacitet og vurdering planlægning.

1. Planlægge de ressourcer og konfiguration, der skal bruges i Azure platformen, som lager konti og virtuelle computere.

1. Konfigurere netværksforbindelser mellem virksomhedens netværk lokalt og [Azure virtuelt netværk](../virtual-network/virtual-networks-overview.md). For at konfigurere forbindelsen mellem virksomhedens netværk i det lokale miljø og en virtuel maskine i Azure skal du benytte en af følgende to metoder:

    1. Oprette en forbindelse mellem lokale og Azure via offentlige slutpunkter på en virtuel maskine i Azure. Denne metode en nem at konfigurere og gør det muligt at bruge SQL Server-godkendelse i din virtuelle maskine. Konfigurere desuden netværk sikkerhed gruppe regler til at styre offentlige trafik til VM. Du kan finde yderligere oplysninger finder [Tillad ekstern adgang til din VM ved hjælp af portalen Azure](virtual-machines-windows-nsg-quickstart-portal.md).

    1. Oprette en forbindelse mellem lokale og Azure via Azure virtuelt privat netværk (VPN) tunnel. Denne metode kan du udvide domæne politikker til en virtuel maskine i Azure. Desuden kan du konfigurere firewallregler og bruger Windows-godkendelse i din virtuelle maskine. På nuværende tidspunkt understøtter Azure sikker VPN-websted til websted og punkt-til-site VPN-forbindelser:

        - Med sikker forbindelse til websted, kan du oprette netværksforbindelse mellem dit lokale netværk og netværket virtuel i Azure. Det anbefales til at oprette forbindelse til dit lokale data center-miljø til Azure.

        - Med sikker forbindelse til punkt-til-websted, kan du oprette netværksforbindelse mellem netværket virtuel i Azure og din individuelle computere, der kører et vilkårligt sted. Det anbefales hovedsageligt til udvikling og test formål.

    Du kan finde oplysninger om, hvordan du opretter forbindelse til SQL Server på Azure, [Opret forbindelse til en SQL Server virtuel maskine på Azure](virtual-machines-windows-classic-sql-connect.md).

1. Konfigurere planlagte opgaver og påmindelser, sikkerhedskopiere lokale data i et virtuelt disk i Azure. Se [SQL serversikkerhedskopiering og gendannelse med Azure Blob Storage Service](https://msdn.microsoft.com/library/jj919148.aspx) og [Sikkerhedskopiering og gendannelse til SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-backup-recovery.md)kan finde flere oplysninger.

1. Du kan implementere et af følgende tre almindelige scenarier afhængigt af dit program behov:

    1. Du kan holde din webserver, programserver og følsomt data i en databaseserver i Azure, mens du beholder de følsomme data i det lokale miljø.

    1. Du kan holde din webserver og programserver on-premises mens databaseserveren i en virtuel maskine i Azure.

    1. Du kan holde din-databaseserver, webserver, og programserver lokale bør du beholde replikaerne virtuelle maskiner i Azure. Denne indstilling giver mulighed for den lokale-servere eller rapporteringsprogrammer at få adgang til replikaer i Azure. Derfor kan du opnå for at reducere arbejdsbelastningen i en lokal database. Vi anbefaler, at du implementerer dette scenarie for tungt læse arbejdsmængder og udvikling formål. Du kan finde oplysninger om at oprette replikaer i Azure AlwaysOn tilgængelighed grupper på [høj tilgængelighed og nedbrud til SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="comparing-web-development-strategies-in-azure"></a>Sammenligne web development strategier i Azure

For at implementere og installere en med flere lag SQL Server-baseret program i Azure, kan du bruge en af følgende to programming metoder:

- Konfigurere en traditionel webserver (IIS - Internet Information Services) i Azure og access-databaser i SQL Server på virtuelle Azure-computere.

- Implementere og installere en skybaseret tjeneste til Azure. Kontrollere, at denne skytjeneste har adgang til databaser i SQL Server på virtuelle Azure-computere. En skybaseret tjeneste kan indeholde flere internettet og arbejder roller.

Den følgende tabel indeholder en sammenligning af traditionelle webudvikling med Azure Cloud Services og Azure Web Apps i forhold til SQL Server på virtuelle Azure-computere. Tabellen indeholder Azure Web Apps, som det er muligt at bruge SQL Server i Azure VM som datakilde til Azure online via dens offentlige virtuelle IP-adresse eller DNS-navn.

||Traditionelle webudvikling i virtuelle Azure-computere|Cloud Services i Azure|Web, der er vært for Azure Webapps|
|---|---|---|---|
|**Programmet overførsel fra det lokale**|Eksisterende programmer som-er.|Programmer skal internettet og arbejder roller.|Eksisterende programmer som-men egner sig til selvstændig webprogrammer og webtjenester, der kræver hurtig skalerbarhed.|
|**Udvikling og installation**|Visual Studio WebMatrix Visual Web Developer, WebDeploy, FTP, TFS, og IIS Manager, PowerShell.|Visual Studio, Azure SDK, TFS, PowerShell. Hver skytjeneste har to miljøer, som du kan installere din servicepakke og konfiguration: test- og. Du kan installere en skybaseret tjeneste til det midlertidige miljø til at teste den, før du opgraderer til fremstilling.|Visual Studio WebMatrix Visual Web Developer, FTP, CIFFER, BitBucket, CodePlex, DropBox, GitHub, betyder, TFS, og Web installerer, PowerShell.|
|**Administration og konfiguration**|Du er ansvarlig for administrative opgaver på det program, data, firewallregler, virtuelt netværk og operativsystem.|Du er ansvarlig for administrative opgaver på det program, data, firewallregler og virtuelt netværk.|Du er ansvarlig for administrative opgaver på programmet og kun dataene.|
|**Høj tilgængelighed og nedbrud (HADR)**|Vi anbefaler, at du placerer virtuelle maskiner, i det samme sæt tilgængelighed og i den samme skybaseret tjeneste. Holde styr på dine FOS i den samme kan tilgængelighed sæt Azure til at placere noderne høj tilgængelighed i separate fejl domæner og opgradere domæner. På samme måde, holde styr på dine FOS i samme skytjenesten aktiverer justering af belastning og FOS kan kommunikere direkte med hinanden over det lokale netværk i en Azure datacenter.<br/><br/>Du er ansvarlig for at implementere en høj tilgængelighed og genoprettelse efter genoprettelsesløsning til SQL Server på virtuelle Azure-computere for at undgå eventuelle nedetid. Understøttede HADR teknologier i [høj tilgængelighed og nedbrud til SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-high-availability-dr.md).<br/><br/>Du er ansvarlig for sikkerhedskopiering af dine egne data og programmet.<br/><br/>Azure kan flytte din virtuelle maskiner, hvis værtsmaskinen i datacenteret mislykkes på grund af problemer med hardware. Desuden, der kan være planlagt nedetid af din VM når værtsmaskinen opdateres for sikkerhed og software opdateringer. Vi anbefaler derfor, at du har mindst to FOS i hvert program niveau for at sikre kontinuerlig tilgængeligheden. Azure giver ikke SERVICENIVEAUAFTALE for en enkelt virtuel maskine. Du kan finde yderligere oplysninger finder [Azure fleksibilitet teknisk vejledning](../resiliency/resiliency-technical-guidance.md).|Azure håndterer fejlene i resultatet af den underliggende hardware eller operativsystem software. Vi anbefaler, at du implementerer flere forekomster af en web eller arbejder rolle at sikre høj tilgængeligheden af dit program. Finde oplysninger i [Cloud Services, virtuelle maskiner, og virtuelle netværk serviceaftale](http://www.microsoft.com/download/details.aspx?id=38427) og [nedbrud og høj tilgængelighed til Azure-programmer](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)<br/><br/>Du er ansvarlig for sikkerhedskopiering af dine egne data og programmet.<br/><br/>Du er ansvarlig for at implementere en høj tilgængelighed og genoprettelse efter løsning til genoprettelse for at undgå eventuelle nedetid i forbindelse med databaser fra en SQL Server-database i en Azure VM. Understøttede HDAR teknologier finde høj tilgængelighed og nedbrud til SQL Server på virtuelle Azure-computere.<br/><br/>**Spejling af SQL Server Database**: Brug med Azure Cloud Services (web/arbejder roller). SQL Server FOS og en skybaseret tjeneste project kan være i samme Azure virtuelle netværk. Hvis SQL Server VM ikke er i det samme virtuelle netværk, skal du oprette en SQL Server-Alias for at omdirigere kommunikation til forekomsten af SQL Server. Desuden aliasnavnet skal svare til navnet på SQL Server.|Høj tilgængelighed nedarves fra Azure arbejder roller, Azure blob-lager og Azure SQL-Database. For eksempel fører Azure-lager tre kopier af alle blob, tabel og kø data. Azure SQL-Database hele tiden tre kopier af data, der kører på én gang, – én primære replika og to sekundær replikaer. Yderligere oplysninger finder du se [Azure-lager](https://azure.microsoft.com/documentation/services/storage/) og [Azure SQL-Database](../sql-database/sql-database-technical-overview.md).<br/><br/>Når du bruger SQL Server i Azure VM som datakilde til Azure Web Apps, Husk på, at Azure Web Apps ikke understøtter Azure virtuelt netværk. Det vil sige, skal alle forbindelser fra Azure Web Apps til SQL Server FOS i Azure gennemgå offentlige slutpunkter af virtuelle maskiner. Dette kan medføre visse begrænsninger for høj tilgængelighed og genoprettelse efter nedbrud scenarier. For eksempel vil klientprogrammet på Azure Web Apps, oprette forbindelse til SQL Server VM med databasespejling ikke opretter forbindelse til den nye primære server som databasespejling kræver, at du konfigurerer Azure virtuelt netværk mellem SQL Server host FOS i Azure. Derfor understøttes bruger **SQL Server databasespejling** med Azure Web Apps ikke i øjeblikket.<br/><br/>**SQL Server AlwaysOn tilgængelighed grupper**: Du kan konfigurere AlwaysOn tilgængelighed grupper, når du bruger Azure Web Apps med SQL Server FOS i Azure. Men du skal konfigurere AlwaysOn tilgængelighed gruppe lytteren for at omdirigere kommunikationen til den primære replika via offentlige belastningsfordeling slutpunkter.|
|**Tværs lokale-forbindelse**|Du kan bruge Azure virtuelt netværk til at oprette forbindelse til en lokal installation.|Du kan bruge Azure virtuelt netværk til at oprette forbindelse til en lokal installation.|Azure virtuelt netværk understøttes. Du kan finde yderligere oplysninger finder [Web Apps virtuelle Network Integration](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/).|
|**Skalerbarhed**|Skalere op findes ved at øge størrelsen virtuelt eller tilføje flere diske. Du kan finde flere oplysninger om virtuelt størrelser, [Virtuelt størrelser til Azure](virtual-machines-windows-sizes.md).<br/><br/>**For databaseserver**: skala fra er tilgængelig via database leverandør teknikker og tilgængeligheden af SQL Server AlwaysOn grupper.<br/><br/>Du kan bruge [AlwaysOn tilgængelighed grupper](https://msdn.microsoft.com/library/hh510230.aspx) for tunge arbejdsbelastninger, som læst, på flere sekundære noder samt SQL Server-replikering.<br/><br/>Du kan implementere vandret leverandør data på tværs af flere fysiske servere til at levere programmet skala ud for tunge Skriv arbejdsmængder.<br/><br/>Du kan desuden implementere en skala ud ved hjælp af [SQL Server med afhængige distribution af Data](https://technet.microsoft.com/library/cc966448.aspx). Med Data afhængige Routing (DDR), skal du implementere leverandør ordning i klientprogrammet typisk i business niveau lag til at dirigere database anmodninger til flere SQL Server-noder. Business niveauet indeholder tilknytninger til hvordan dataene er opdelt og hvilke node indeholder dataene.<br/><br/>Du kan skalere programmer, der kører virtuelle computere. Du kan finde flere oplysninger, se, [hvordan skalere et program](../cloud-services/cloud-services-how-to-scale.md).<br/><br/>**Vigtig bemærkning**: funktionen **Autoskalering** i Azure gør det muligt at øge eller mindske de virtuelle maskiner, der bruges af dit program automatisk. Denne funktion garanterer, at slutbrugeroplevelsen påvirkes ikke negativt i perioder med spidsbelastning, og FOS er lukket, når behovet er lav. Det anbefales, at du ikke angive indstillingen Autoskalering for skybaseret tjeneste, hvis det indeholder SQL Server FOS. Det skyldes, at funktionen Autoskalering kan Azure at slå en virtuel maskine, når CPU-brug i pågældende VM er højere end nogle grænseværdi, og til at deaktivere en virtuel maskine, når CPU-brug er lavere end den. Funktionen Autoskalering er nyttige til uden status programmer, som Web-servere, hvor en hvilken som helst VM kan administrere arbejdsbelastningen uden eventuelle referencer til en hvilken som helst tidligere tilstand. Funktionen Autoskalering er dog ikke nyttige til med høj sikkerhed programmer, såsom SQL Server, hvor kun én forekomst giver mulighed for skrivning til databasen.|Skalere op findes ved hjælp af flere internettet og arbejder roller. Du kan finde flere oplysninger om virtuelt størrelser for web- og arbejder roller, [Konfigurere størrelser for Cloud Services](../cloud-services/cloud-services-sizes-specs.md).<br/><br/>Når du bruger **Cloud Services**, kan du angive flere roller for at distribuere behandling og også opnå fleksible skalering af dit program. Hver skytjeneste indeholder en eller flere web roller og/eller arbejder roller, hver med sin egen programfiler og konfiguration. Du kan skalere op en skybaseret tjeneste ved at øge antallet af forekomster af rolle (virtuelle maskiner) installeret for en rolle og skala ned en skybaseret tjeneste ved at reducere antallet af forekomster af rollen. Du kan finde detaljerede oplysninger [Azure udførelse af modeller](../cloud-services/cloud-services-choose-me.md).<br/><br/>Skala fra er tilgængelig via understøttelse af indbyggede Azure høj tilgængelighed via [Cloud Services, virtuelle maskiner, og virtuelle netværk serviceaftale](http://www.microsoft.com/download/details.aspx?id=38427) og justering af belastning.<br/><br/>Programmer med flere lag anbefaler vi, at du opretter forbindelse web/arbejder roller programmet til databasen server FOS via Azure virtuelt netværk. Desuden leverer Azure belastningsjustering for FOS i den samme skybaseret tjeneste, sprede anmodninger på tværs af dem. Virtuelle maskiner forbindelse på denne måde kan kommunikere direkte med hinanden over det lokale netværk i en Azure datacenter.<br/><br/>Du kan konfigurere **Autoskalering** på portalen Azure klassisk samt tidspunkterne i tidsplanen. Du kan finde flere oplysninger, se, [hvordan skalere et program](../cloud-services/cloud-services-how-to-scale.md).|**Skalere op og ned**: Du kan øge/reducere størrelsen på den forekomst (VM) reserveret til dit websted.<br/><br/>Skaler ud: Du kan tilføje mere reserveret forekomster (FOS) til dit websted.<br/><br/>Du kan konfigurere **Autoskalering** på portalen samt tidspunkterne i tidsplanen. Du kan finde flere oplysninger, se, [hvordan du skala Web Apps](../app-service-web/web-sites-scale.md).|

Finde flere oplysninger om at vælge mellem disse programming metoder, [Azure Web Apps, Skytjenester og FOS: hvornår du skal bruge som](../app-service-web/choose-web-site-cloud-service-vm.md).

## <a name="next-steps"></a>Næste trin

Du kan finde flere oplysninger om kører SQL Server på virtuelle Azure-computere, [SQL Server på Azure virtuelle maskiner oversigt](virtual-machines-windows-sql-server-iaas-overview.md).
