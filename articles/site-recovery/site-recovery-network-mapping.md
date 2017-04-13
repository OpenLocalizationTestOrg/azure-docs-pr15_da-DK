<properties
    pageTitle="Forberede netværk tilknytning til Hyper-V virtuelt beskyttelse med VMM i Azure gendannelse af websteder | Microsoft Azure"
    description="Konfigurere netværk tilknytning af Hyper-V virtuelt replikering fra en lokal datacenter til Azure eller til et sekundært websted."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/05/2016"
    ms.author="raynew"/>


# <a name="prepare-network-mapping-for-hyper-v-virtual-machine-protection-with-vmm-in-azure-site-recovery"></a>Forberede netværk tilknytning til Hyper-V virtuelt beskyttelse med VMM i Azure gendannelse af websteder

Azure gendannelse af websteder bidrager til din business løbende og genoprettelse efter genoprettelse (BCDR) strategi ved orchestrating gentagelse, failover og gendannelse af virtuelle maskiner og fysiske servere.

I denne artikel beskrives kort over netværk, der hjælper med at du optimalt konfigurere netværksindstillinger, når du bruger gendannelse af websteder til at gentage Hyper-V virtuelle maskiner, der er placeret i VMM skyer mellem to lokale datacentre eller mellem en lokal datacenter og Azure. Bemærk, at hvis du replikering Hyper-V FOS uden en VMM sky, eller replikering VMware FOS eller fysiske servere, i denne artikel ikke relevant.

Sende en hvilken som helst kommentarer eller spørgsmål nederst i denne artikel eller på [Azure gendannelse Services-forummet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Oversigt

Kort over netværk bruges, når Azure gendannelse af websteder er installeret til at kopiere Hyper-V virtuelle maskiner til Azure eller til en sekundær datacenter, ved hjælp af Hyper-V replika eller SAN gentagelse.

- **Replikering Hyper-V virtuelle maskiner i VMM skyer mellem to lokale datacentre**– hjemmenetværk tilknytning kort mellem VM netværk på en kilde VMM server og VM netværk på en målliste VMM server til at gøre følgende:

    - **Opret forbindelse virtuelle maskiner efter failover**– sikrer, at virtuelle maskiner forbindes med relevante netværk efter failover. Virtuelt replika forbindes til destinationen netværk, der er knyttet til netværket kilde.
    - **Sted replika virtuelle maskiner på værtsservere**– Placer optimalt replika virtuelle maskiner på Hyper-V værtsservere. Replika virtuelle maskiner placeres på værter, der kan få adgang til de tilknyttede VM netværk.
    - **Ingen netværk tilknytning**– Hvis du ikke konfigurerer kort over netværk, replikerede virtuelle maskiner ikke forbindelse til en hvilken som helst VM netværk efter failover.

- **Replikering Hyper-V virtuelle maskiner i et lokalt VMM skyen til Azure**– netværk tilknytning kort mellem VM netværk på VMM kildeserveren og målrette Azure netværk for at gøre følgende:
    - **Oprette forbindelse virtuelle maskiner efter failover**– alle de computere, hvilke failover på det samme netværk kan oprette forbindelse til hinanden, uanset hvilken gendannelse plan de er i.
    - **Netværksgateway**– Hvis en netværksgateway er konfigureret på destinationen Azure netværk, virtuelle computere kan oprette forbindelse til andre lokale virtuelle computere.
    - **Ingen netværk tilknytning**– Hvis du ikke konfigurerer kort over netværk, kun virtuelle maskiner, failover i den samme plan for gendannelse vil kunne oprette forbindelse til hinanden efter fejl til Azure.


## <a name="network-mapping-example"></a>Netværk tilknytning af eksempel

Kort over netværk kan konfigureres mellem VM netværk på to VMM servere eller på en enkelt VMM server, hvis to steder administreres af den samme server. Når tilknytning er konfigureret korrekt, og replikering er aktiveret, et virtuelt på den primære placering kan være tilsluttet et netværk, og dets replika i målplaceringen der skal knyttes til dens tilknyttede netværk.

Hvis du har konfigureret netværk korrekt i VMM, når du vælger et destinationsprogram VM netværk under netværk tilknytningen, vises den VMM kilde sky, der bruger kilde VM netværket, sammen med de tilgængelige mål VM netværk på den destinationsadresse-sky, der bruges til beskyttelse.

Her er et eksempel til at illustrere denne funktion. Lad os en organisation med to forskellige steder i New York og Chicago.

**Placering** | **VMM server** | **VM netværk** | **Tilknyttet**
---|---|---|---
New York | VMM NewYork| VMNetwork1 NewYork | Tilknyttet VMNetwork1 Chicago
 |  | VMNetwork2 NewYork | Ikke er tilknyttet
Chicago | VMM Chicago| VMNetwork1 Chicago | Tilknyttet VMNetwork1 NewYork
 | | VMNetwork2 Chicago | Ikke er tilknyttet

Med dette eksempel:

- Når der oprettes en replika virtuel maskine til en hvilken som helst virtuel maskine, der er forbundet med VMNetwork1 NewYork, der skal knyttes til VMNetwork1 Chicago.
- Når der oprettes en replika virtuel maskine til VMNetwork2 NewYork eller VMNetwork2 Chicago, vil det ikke forbindelse til en hvilken som helst netværk.

Her er, hvordan VMM skyer er konfigureret i vores eksempel organisation og de logiske netværk, der er knyttet til sky.

### <a name="cloud-protection-settings"></a>Indstillingerne for dokumentbeskyttelse skyen

**Beskyttet skyen** | **Beskytte skyen** | **Logisk netværk (New York)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>IKKE.TILGÆNGELIG</p><p></p> | <p>LogicalNetwork1 NewYork</p><p>LogicalNetwork1 Chicago</p>
SilverCloud2 | <p>IKKE.TILGÆNGELIG</p><p></p> | <p>LogicalNetwork1 NewYork</p><p>LogicalNetwork1 Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Logisk og VM netværksindstillinger

**Placering** | **Logisk netværk** | **Tilknyttede VM netværk**
---|---|---
New York | LogicalNetwork1 NewYork | VMNetwork1 NewYork
Chicago | LogicalNetwork1 Chicago | VMNetwork1 Chicago
 | LogicalNetwork2Chicago | VMNetwork2 Chicago

### <a name="target-networks"></a>Target netværk

Baseret på disse indstillinger, når du har valgt target VM netværk, vises i følgende tabel de valgmuligheder, der er tilgængelige.

**Vælg** | **Beskyttet skyen** | **Beskytte skyen** | **Target netværk tilgængelige**
---|---|---|---
VMNetwork1 Chicago | SilverCloud1 | SilverCloud2 | Tilgængelige
 | GoldCloud1 | GoldCloud2 | Tilgængelige
VMNetwork2 Chicago | SilverCloud1 | SilverCloud2 | Ikke tilgængelig
 | GoldCloud1 | GoldCloud2 | Tilgængelige



## <a name="multiple-subnets"></a>Flere undernet

Hvis target netværket har flere undernet og én af disse undernet har samme navn som det undernet, hvor virtuelt kilde er placeret, der derefter virtuelt replika skal knyttes til pågældende target undernet efter failover. Hvis der er ingen target undernet med et tilsvarende navn skal forbindes den virtuelle maskine til det første undernet i netværket.


### <a name="failback"></a>Failback

Hvis du vil se, hvad sker der, hvis det er failback (omvendt replikering), Lad os antage, at VMNetwork1 NewYork er knyttet til VMNetwork1-Chicago, med følgende indstillinger.


**Virtuel maskine** | **Forbindelse til VM netværk**
---|---
VM1 | VMNetwork1-netværk
VM2 (kopi af VM1) | VMNetwork1 Chicago

Med disse indstillinger, Lad os gennemgå Hvad sker der i et par mulige scenarier.

**Scenarie** | **Resultatet**
---|---
Ingen ændring i netværk egenskaberne for VM 2 efter failover. | VM 1 forbliver forbindelse til netværket kilde.
Netværksegenskaber for VM 2 er ændret efter en failover og er ikke tilsluttet. | VM-1 er afbrudt.
Netværksegenskaber for VM 2 er ændret efter en failover og er forbundet med VMNetwork2 Chicago. | Hvis VMNetwork2 Chicago ikke er tilknyttet, afbrydes VM-1.
Netværk tilknytningen af VMNetwork1 Chicago ændres. | VM 1 skal have forbindelse til netværket nu forbundet med VMNetwork1 Chicago.


## <a name="next-steps"></a>Næste trin

Nu hvor du har en bedre forståelse af kort over netværk, [komme i gang med installationen af gendannelse af websteder](site-recovery-best-practices.md).
