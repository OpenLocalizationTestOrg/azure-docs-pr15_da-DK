<properties
    pageTitle="Hvilke arbejdsbelastninger, som kan du beskytte med Azure gendannelse af websteder?"
    description="Azure gendannelse af websteder beskytter dine arbejdsmængder og programmer ved at koordinere gentagelse, failover og gendannelse af lokale virtuelle maskiner og fysiske servere til Azure eller til en sekundær lokalt websted"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/10/2016"
    ms.author="raynew"/>

# <a name="what-workloads-can-you-protect-with-azure-site-recovery"></a>Hvilke arbejdsbelastninger, som kan du beskytte med Azure gendannelse af websteder?


I denne artikel beskrives arbejdsmængder og programmer, du kan gentage med tjenesten Azure gendannelse af websteder.

Sende en hvilken som helst kommentarer eller spørgsmål nederst i denne artikel eller på [Azure gendannelse Services-forummet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Oversigt

Virksomheder har brug for en business løbende nedbrud gendannelse (BCDR) strategi og beholde arbejdsmængder og data sikre og tilgængelige under planlagte og ikke-planlagt nedetid, og gendanne til normalt arbejdsbetingelser så tidligt som muligt.

Gendannelse af websteder er en Azure-tjeneste, der bidrager til din strategi for BCDR. Ved hjælp af gendannelse af websteder, kan du installere programmet-aware gentagelse, i skyen eller til en sekundær websted. Uanset om dine apps er Windows eller Linux-baserede, der kører på fysiske servere, VMware eller Hyper-V, kan du bruge gendannelse af websteder til dirigerer gentagelse, udføre nedbrud gendannelse test, og kør failover og failback.


Gendannelse af websteder integrerer med Microsoft-programmer, herunder SharePoint, Exchange, Dynamics, SQL Server og Active Directory. Microsoft arbejder også tæt sammen med foranstillet leverandører, herunder Oracle, SAP, IBM og Red Hat. Du kan tilpasse gentagelse løsninger på grundlag af app efter app.

## <a name="why-use-site-recovery-for-application-replication"></a>Hvorfor bruge gendannelse af websteder til programmet gentagelse?

Gendannelse af websteder bidrager til programmet beskyttelse og gendannelse på følgende måde:

- App-agnostic, give gentagelse for alle arbejdsbelastninger, som kører på en understøttede maskine.
- I nærheden af synkron replikering med frigivne produktionsordrer så lave som 30 sekunder, der imødekommer behovet hos de vigtigste virksomheder-apps.
- App-ensartet øjebliksbilleder for enkelte eller flere lag programmer.
- Integration med SQL Server AlwaysOn og samarbejde med andre programmer niveau gentagelse teknologier, herunder AD gentagelse SQL AlwaysOn, Exchange Database tilgængelighed grupper (DAGs) og Oracle Data vagt.
- Fleksible gendannelse planer, der gør det muligt at gendanne en hele programmet stak med et enkelt klik, og Medtag for at medtage eksterne scripts og manuel handlinger i planen.
- Netværksadministration af Avanceret i gendannelse af websteder og Azure for at forenkle app netværkskrav, herunder også muligheden for at reservere IP-adresser, konfigurere justering af belastning og integration med Azure trafik Manager til lav RTO netværk switchovers.
-  En omfattende automatiske bibliotek, der indeholder fremstilling klar, programmet-specifikke scripts, som kan hentes og integreres med gendannelse planer.



## <a name="workload-summary"></a>Arbejdsbelastning oversigt

Gendannelse af websteder kan gentage en app, der kører på en understøttede maskine. Desuden har vi gået sammen med produkt for teams at udføre yderligere app-specifikke undersøgelser.

**Arbejdsbelastning** | **Gentage Hyper-V FOS til en sekundær websted** | **Gentage Hyper-V FOS til Azure** | **Gentage VMware FOS til en sekundær websted** | **Gentage VMware FOS til Azure**
---|---|---|---|---
Active Directory, DNS | Y | Y | Y | Y
Webapps (IIS, SQL) | Y | Y | Y | Y
System Center Operations Manager | Y | Y | Y | Y
SharePoint | Y | Y | Y | Y
SAP<br/><br/>Gentage websted for SAP til Azure for ikke-klynge | Y (testet af Microsoft) | Y (testet af Microsoft) | Y (testet af Microsoft) | Y (testet af Microsoft)
Exchange (ikke-DAG) | Y | Kommer snart | Y | Y
Remote Desktop/VDI | Y | Y | Y | I/T.
Linux (operativsystem og apps) | Y (testet af Microsoft) | Y (testet af Microsoft) | Y (testet af Microsoft) | Y (testet af Microsoft)
Dynamics AX | Y | Y | Y | Y
Dynamics CRM | Y | Kommer snart | Y | Kommer snart
Oracle | Y (testet af Microsoft) | Y (testet af Microsoft) | Y (testet af Microsoft) | Y (testet af Microsoft)
Windows-filserver | Y | Y | Y | Y


## <a name="replicate-active-directory-and-dns"></a>Gentage Active Directory og DNS

En Active Directory og DNS-infrastrukturen er vigtige for de fleste virksomhedsapps. Under nedbrud skal du beskytte og gendanne komponenterne infrastruktur før gendanne din arbejdsmængder og apps.

Du kan bruge gendannelse af websteder til at oprette en komplet automatiseret nedbrud gendannelse plan til Active Directory og DNS. Hvis du vil mislykkes eksempelvis over SharePoint og SAP fra en primær til en sekundær websted, kan du konfigurere en gendannelse plan, der skifter Active Directory først, og klik derefter en yderligere app-specifikke betroet plan mislykkes over de apps, der er afhængige af Active Directory.

Du kan [Få mere at vide](site-recovery-active-directory.md) om beskyttelse af Active Directory og DNS.

## <a name="protect-sql-server"></a>Beskytte SQL Server

SQL Server indeholder et data services fundament for datatjenester til mange virksomheder-apps i lokale datacenter.  Gendannelse af websteder kan bruges sammen med SQL Server HA/DR teknologier til at beskytte flere niveauer virksomhedsapps, der bruger SQL Server. Gendannelse af websteder indeholder:

- En nem og økonomisk nedbrud gendannelsesløsning til SQL Server. Gentage flere versioner og udgaver af SQL Server enkeltstående servere og klynger, til Azure eller til et sekundært websted.  
- Integration med SQL AlwaysOn tilgængelighed grupper til at administrere failover og failback med Azure gendannelse af websteder gendannelse planer.
- Til slut gendannelse planer for alle niveauer i et program, herunder SQL Server-databaserne.
- Skalering af SQL Server for spidsbelastning indlæser med gendannelse af websteder, ved at "bursting" dem i IaaS virtuelt større i Azure.
- Nemt test af SQL Server nedbrud. Du kan køre test failover for at analysere data og køre overholdelse kontrol, uden at påvirke dit produktionsmiljø.

Du kan [Få mere at vide](site-recovery-sql.md) om beskyttelse af SQL server.

##<a name="protect-sharepoint"></a>Beskytte SharePoint

Azure gendannelse af websteder hjælper med at beskytte SharePoint-installationer, som følger:

- Fjerner skal og tilhørende infrastruktur omkostninger for en standby farm til nedbrud. Brug gendannelse af websteder til at kopiere en hel farm (Web app og database niveauer) til Azure eller til et sekundært websted.
- Forenkler programmet installation og administration. Opdateringer, der implementeres til webstedet for primær replikeres automatisk, og dermed er tilgængeligt efter failover og gendannelse af en farm på en sekundær websted. Sænker også management kompleksitet og omkostninger, der er knyttet til at holde en standby farm opdateret.
- Forenkler udvikling af SharePoint-programmer og test af ved at oprette en kopi af synes godt om fremstilling efter behov replika miljø til test og fejlfinding.
- Forenkler overgang til skyen ved hjælp af gendannelse af websteder til at overføre SharePoint-installationer til Azure.

Du kan [Få mere at vide](https://gallery.technet.microsoft.com/SharePoint-DR-Solution-f6b4aeae) om at beskytte SharePoint.


## <a name="protect-dynamics-ax"></a>Beskytte Dynamics AX

Azure gendannelse af websteder hjælper med at beskytte din Dynamics AX ERP-løsning med:

- Orchestrating replikering af hele Dynamics AX miljøet (internettet og AOS niveauer, database lag, SharePoint) til Azure eller til et sekundært websted.
- At forenkle migrering af Dynamics AX installationer til skyen (Azure).
- At forenkle Dynamics AX udvikling og test ved at oprette en kopi af synes godt om fremstilling efter behov, til test og fejlfinding.

Du kan [Få mere at vide](https://gallery.technet.microsoft.com/Dynamics-AX-DR-Solution-b2a76281) om at beskytte Dynamic AX.

## <a name="protect-rds"></a>Beskytte RDS

Remote Desktop Services (RDS) aktiverer virtual desktop-infrastruktur (VDI), session-baserede stationære computere og programmer, så brugerne kan arbejde alle steder. Med Azure gendannelse af websteder kan du:

- Gentage administreret eller ikke-administreret puljebaseret virtuelle stationære computere til et sekundært websted og remote-programmer og sessioner til et sekundært websted eller Azure.
- Her er, hvad du kan gentage:

**RDS** | **Gentage Hyper-V FOS til en sekundær websted** | **Gentage Hyper-V FOS til Azure** | **Gentage VMware FOS til en sekundær websted** | **Gentage VMware FOS til Azure** | **Gentage fysiske servere til en sekundær websted** | **Gentage fysiske servere til Azure**
---|---|---|---|---|---|---
**Grupperede virtuelle skrivebord (ikke-administreret)** | Ja | Nej | Ja | Nej | Ja | Nej
**Samlet virtuelle skrivebord (administrerede og uden hvorved UPD)** | Ja | Nej | Ja | Nej | Ja | Nej
**Remote-programmer og computeren sessioner (uden hvorved UPD)** | Ja | Ja | Ja | Ja | Ja | Ja


[Få mere at vide](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) om beskyttelse af RDS.


## <a name="protect-exchange"></a>Beskytte Exchange

Gendannelse af websteder hjælper med at beskytte Exchange, som følger:

- Lille Exchange-installationer, som en enkelt eller enkeltstående servere, kan gendannelse af websteder gentage og skifte over til Azure eller til et sekundært websted.
- For større installationer integrerer gendannelse af websteder med Exchange DAGS.
- Exchange DAGs er anbefalet løsning for Exchange nedbrud i en virksomhed.  Websted gendannelse gendannelse planer kan indeholde DAGs, hvis du vil dirigerer DAG failover på tværs af websteder.


Du kan [Få mere at vide](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) om beskyttelse af Exchange.

## <a name="protect-sap"></a>Beskytte SAP

Bruge gendannelse af websteder til at beskytte din SAP-installation på følgende måde:

- Aktivere beskyttelse af hele SAP-installationen, ved at replikering forskellige installation lag til Azure eller til en sekundær websted.
- Forenkle skyen overførslen ved hjælp af gendannelse af websteder til at overføre din SAP-installation til Azure.
- Forenkle SAP udvikling og test, ved at oprette en fremstilling-synes godt om Kopiér efter behov for at teste og fejlfinding af programmer.

Du kan [Få mere at vide](http://aka.ms/asr-sap) om beskyttelse af SAP.

## <a name="next-steps"></a>Næste trin

[Forberede installationen af gendannelse af websteder](site-recovery-best-practices.md) 
