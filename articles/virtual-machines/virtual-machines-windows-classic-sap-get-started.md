<properties
   pageTitle="Brug af SAP på Windows virtuelle maskiner | Microsoft Azure"
   description="Ryd om brug af SAP på Windows virtuelle maskiner (VM'er) i Microsoft Azure"
   services="virtual-machines-windows,virtual-network,storage"
   documentationCenter="saponazure"
   authors="MSSedusch"
   manager="timlt"
   editor=""
   tags="azure-service-management"
   keywords=""/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="10/04/2016"
   ms.author="sedusch"/>

# <a name="using-sap-on-windows-virtual-machines-in-azure"></a>Brug af SAP på Windows virtuelle maskiner i Azure

Cloud Computing er et udbredte udtryk, er at få flere og flere prioritet i branche IT fra små virksomheder op til store og multinationale virksomheder. Microsoft Azure er Cloud Services Platform fra Microsoft, som har en lang række nye muligheder. Kunder er nu kan du hurtigt klargøre og deaktivere Klargør programmer som Skytjenester, så de ikke er begrænset til tekniske eller budgettering begrænsninger. I stedet for at investere og budgettet i hardwareinfrastruktur, kan firmaer fokusere på programmet, forretningsprocesser og dets fordele for kunder og brugere.

Microsoft tilbyder med virtuelle Microsoft Azure-computere, en omfattende infrastruktur som en tjeneste (IaaS) platform. SAP NetWeaver baseret programmer understøttes på virtuelle Azure-computere (IaaS). De nedenstående hvidbøger beskriver, hvordan du planlægge og implementere SAP NetWeaver baseret programmer på Windows virtuelle maskiner i Azure. Du kan også implementere SAP NetWeaver baseret programmer på [Linux virtuelle computere](virtual-machines-linux-classic-sap-get-started.md).

[AZURE.INCLUDE [virtual-machines-common-classic-sap-get-started](../../includes/virtual-machines-common-classic-sap-get-started.md)]

## <a name="sap-netweaver-on-azure---ha"></a>SAP NetWeaver på Azure - HA

Titel: SAP NetWeaver på Azure - klynge SAP ASC'ER/SCS forekomster ved hjælp af Windows Server sekundær klynge på Azure med SIOS DataKeeper

Oversigt: ' dette dokument beskrives det, hvordan du bruger SIOS DataKeeper til at konfigurere en meget tilgængelige SAP ASC'ER/SCS konfiguration på Azure. SAP beskytter deres for manglende komponenter som SAP ASC'ER/SCS eller Enqueue gentagelse tjenester med Windows Server sekundær klynge konfigurationer, der kræver delte diske fra ét sted. Disse SAP-komponenter er afgørende for funktionaliteten i et SAP-system. Derfor skal høj tilgængelighed funktionalitet anbringes i stedet for at sikre dig, at disse komponenter kan opretholde fejl på en server eller en VM som udført med Windows-klynge konfigurationer for helt fra bunden og Hyper-V-miljøer. Fra og med August 2015 levere ikke Azure på selve delte diske, der er nødvendige for Windows baseret meget tilgængelige konfigurationer, der kræves for disse kritiske SAP-komponenter. Men ved hjælp af produktet DataKeeper ved SIOS, Windows Server sekundær klynge konfigurationer efter behov for SAP ASC'ER/SCS kan konfigureres på en Azure IaaS platform. Dette dokument beskrives i en tilgang med trin-trin, hvordan du installerer en Windows Server sekundær klynge konfiguration med delte disk, der leveres af SIOS Datakeeper i Azure. Papiret beskriver detaljer i konfigurationer i Azure, Windows og SAP side, som gør høj tilgængelighed konfigurationen arbejde i en optimal måde. Papiret supplerer SAP Installation dokumentation og SAP-noter, som repræsenterer de primære ressourcer til installationer og installationer af SAP-software på bestemte platforme.

Opdateret: August 2015

[Hent denne vejledning nu](http://go.microsoft.com/fwlink/?LinkId=613056)
