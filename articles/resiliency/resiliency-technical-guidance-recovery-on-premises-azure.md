<properties
   pageTitle="Teknisk vejledning: gendannelse fra det lokale til Azure | Microsoft Azure"
   description="Artikel om forståelse og design gendannelse systemer fra lokale infrastruktur til Azure"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="azure-resiliency-technical-guidance-recovery-from-on-premises-to-azure"></a>Azure fleksibilitet teknisk vejledning: gendannelse fra det lokale til Azure

Azure indeholder et omfattende sæt af tjenester for at aktivere udvidelse af en lokal datacenter til Azure til høj tilgængelighed og genoprettelse efter genoprettelse formål:

* __Netværk__: med et virtuelt privat netværk, du sikkert udvide dit lokale netværk til skyen.
* __Beregne__: kunder, der bruger lokale Hyper-V kan "løft, og Skift" eksisterende virtuelle maskiner (VM'er) til Azure.
* __Lager__: StorSimple udvider filsystemet til Azure-lager. Tjenesten Azure sikkerhedskopiering indeholder sikkerhedskopi til filer og SQL-databaser til Azure-lager.
* __Databasereplikering__: med SQL Server 2014 (eller nyere) tilgængelighed grupper, du kan implementere høj tilgængelighed og genoprettelse efter nedbrud til dine lokale data.

##<a name="networking"></a>Netværk

Du kan bruge Azure virtuelt netværk til at oprette en logisk isolerede sektion i Azure og sikkert forbinde den til dit lokale datacenter eller en enkelt klientcomputer ved hjælp af en IPSec-forbindelse. Med virtuelt netværk, kan du drage fordel af SVG, efter behov-infrastrukturen i Azure samtidig med at forbindelse til data og programmer lokalt, herunder systemer, der kører på Windows Server, mainframes og UNIX. Yderligere oplysninger finder du i [Azure netværk dokumentation](../virtual-network/virtual-networks-overview.md) .

##<a name="compute"></a>Beregne

Hvis du bruger Hyper-V i det lokale miljø, du kan "løft og Skift" eksisterende virtuelle maskiner til Azure og -udbyder, der kører Windows Server 2012 (eller nyere), uden at foretage ændringer til VM eller ved at konvertere VM formater. Se Få mere at vide [om og virtuelle harddiske til Azure virtuelle computere](../virtual-machines/virtual-machines-linux-about-disks-vhds.md).

##<a name="azure-site-recovery"></a>Gendannelse af Azure websteder

Hvis du vil nedbrud som en tjeneste (DRaaS), leverer Azure [Azure gendannelse af websteder](https://azure.microsoft.com/services/site-recovery/). Azure gendannelse af websteder indeholder omfattende beskyttelse for VMware, Hyper-V og fysiske servere. Med Azure gendannelse af websteder, kan du bruge en anden lokal server eller Azure som webstedet gendannelse. Du kan finde flere oplysninger om gendannelse af Azure websteder, i [dokumentationen til Azure gendannelse af websteder](https://azure.microsoft.com/documentation/services/site-recovery/).

##<a name="storage"></a>Lagerplads

Der er flere muligheder for brug af Azure som en sikkerhedskopi websted for lokale data.

###<a name="storsimple"></a>StorSimple

StorSimple integrerer sikkert og transparent skylagring for lokale programmer. Den indeholder også en enkelt enhed, der leverer høj ydeevne lagdelte lokale og skylagring, direkte arkivering, skybaseret databeskyttelse og nedbrud. Se [StorSimple produktsiden](https://azure.microsoft.com/services/storsimple/)kan finde flere oplysninger.

###<a name="azure-backup"></a>Azure sikkerhedskopi

Azure sikkerhedskopi aktiverer skyen sikkerhedskopier ved hjælp af velkendte værktøjer til sikkerhedskopiering i Windows Server 2012 (eller nyere), Windows Essentials Server 2012 (eller nyere) og System Center 2012 Data Protection Manager (eller nyere). Disse værktøjer giver en arbejdsproces for sikkerhedskopiering administration, som er uafhængig af lagerplads placeringen af sikkerhedskopier, om en lokal disk eller Azure-lager. Når dataene er sikkerhedskopieret i skyen, kan autoriserede brugere nemt gendanne sikkerhedskopier til en hvilken som helst server.

Med trinvise sikkerhedskopier overføres kun ændringerne i filer i skyen. Dette kan du effektivt bruger lagerplads, reducere båndbredde forbrug og understøtter punkt-in-time gendannelse af flere versioner af dataene. Du kan også vælge at bruge flere funktioner, som data opbevaringspolitikker, datakomprimering og dataoverførsel (throttling). Brug af Azure som den sikkerhedskopiplacering har indlysende fordelen, sikkerhedskopier af er automatisk "andet sted". Dette fjerner ekstra krav til at sikre og beskytte på stedet medier med sikkerhedskopier.

Du kan finde flere oplysninger, se [Hvad er Azure sikkerhedskopi?](../backup/backup-introduction-to-azure-backup.md) og [Konfigurere Azure sikkerhedskopi til DPM data](https://technet.microsoft.com/library/jj728752.aspx).

##<a name="database"></a>Database

Du kan har en løsning til genoprettelse efter genoprettelse til dine SQL Server-databaser i et hybridt IT-miljø ved hjælp af AlwaysOn tilgængelighed grupper, databasespejling, log forsendelses og sikkerhedskopiering og gendannelse med Azure Blob-lager. Alle disse løsninger bruger SQL Server, der kører på virtuelle Azure-computere.

AlwaysOn tilgængelighed grupper kan bruges i en hybrid-IT-miljø, hvor replikaer findes både lokalt og i skyen. Dette er vist i følgende diagram.

![SQL Server AlwaysOn tilgængelighed grupper i en hybrid skyen arkitektur](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-3.png)

Databasespejling af kan også dække lokale servere og skyen i et certifikat-baserede konfiguration. I følgende diagram vises anvendes.

![SQL Server databasespejling i en hybrid skyen arkitektur](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-4.png)

Log levering kan bruges til at synkronisere en lokal database med en SQL Server-database i en Azure virtuelt.

![SQL Server log forsendelses i en hybrid skyen arkitektur](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-5.png)

Du kan til sidst skal sikkerhedskopiere en lokal database direkte til Azure Blob-lager.

![Sikkerhedskopiere SQL Server til Azure Blob-lager i en hybrid skyen arkitektur](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-6.png)

Se [høj tilgængelighed og genoprettelse efter nedbrud for SQL Server på Azure virtuelle computere](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md) og [Sikkerhedskopiering og gendannelse til SQL Server på Azure virtuelle computere](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md)kan finde flere oplysninger.

##<a name="checklists-for-on-premises-recovery-in-microsoft-azure"></a>Kontrollister til lokale genoprettelse i Microsoft Azure

###<a name="networking"></a>Netværk

  1. Gennemse afsnittet netværk af dette dokument.
  2. Brug virtuelt netværk til at oprette sikker forbindelse mellem lokale til skyen.

###<a name="compute"></a>Beregne

  1. Gennemgå sektionen Beregn af dette dokument.
  2. Flytte FOS mellem Hyper-V og Azure.

###<a name="storage"></a>Lagerplads

  1. Gennemse afsnittet lager i dette dokument.
  2. Udnyt StorSimple services til brug af skylagring.
  3. Bruge tjenesten Azure sikkerhedskopi.

###<a name="database"></a>Database

  1. Gennemgå sektionen Database af dette dokument.
  2. Overvej at bruge SQL Server på Azure FOS som sikkerhedskopien.
  3. Konfigurere AlwaysOn tilgængelighed grupper.
  4. Konfigurere certifikat-baseret databasespejling.
  5. Brug log levering.
  6. Sikkerhedskopiere lokale databaser til Azure Blob-lager.

##<a name="next-steps"></a>Næste trin

I denne artikel er en del af en serie, fokuseret på [Azure fleksibilitet teknisk vejledning](./resiliency-technical-guidance.md). Næste artikel i denne serie er [genoprettelse fra databeskadigelse eller utilsigtet sletning](./resiliency-technical-guidance-recovery-data-corruption.md).