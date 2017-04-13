<properties
    pageTitle="Tilknytte lageret i Azure gendannelse af websteder til Hyper-V virtuelt replikering mellem lokale datacentre | Microsoft Azure"
    description="Forberede lagerplads tilknytning af Hyper-V virtuelt replikering mellem to lokale datacentre med Azure gendannelse af websteder."
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
    ms.date="07/06/2016"
    ms.author="raynew"/>


# <a name="prepare-storage-mapping-for-hyper-v-virtual-machine-replication-between-two-on-premises-datacenters-with-azure-site-recovery"></a>Forberede lagerplads tilknytning af Hyper-V virtuelt replikering mellem to lokale datacentre med Azure gendannelse af websteder


Azure gendannelse af websteder bidrager til din business løbende og genoprettelse efter genoprettelse (BCDR) strategi ved orchestrating gentagelse, failover og gendannelse af virtuelle maskiner og fysiske servere. I denne artikel beskrives lagerplads tilknytning, som hjælper med at du har foretaget optimal brug af lagerplads, når du bruger gendannelse af websteder til at gentage Hyper-V virtuelle maskiner mellem to lokalt VMM datacentre.

Sende en hvilken som helst kommentarer eller spørgsmål nederst i denne artikel eller på [Azure gendannelse Services-forummet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Oversigt

Lagerplads tilknytning er kun relevant, når du replikering Hyper-V virtuelle maskiner, der findes i VMM skyer fra en primær datacenter til en sekundær datacenter ved hjælp af Hyper-V replika eller SAN gentagelse, på følgende måde:


- **Lokalt på lokale replikering med Hyper-V replika)** – Du konfigurere lagerplads tilknytning af tilknytning lagerplads klassificeringer på en kilde og målrette VMM servere for at gøre følgende:

    - **Identificer target lagerplads på replika virtuelle maskiner**– virtuelle maskiner kan replikeres til et lager mål (små og mellemstore virksomheder dele eller klynge delte enheder (CSVs)), som du vælger.
    - **Replika virtuelt placering**– lagerplads tilknytning bruges til at placere optimalt replika virtuelle maskiner på Hyper-V værtsservere. Replika virtuelle maskiner placeres på værter, der kan få adgang til den tilknyttede lagerplads klassificering.
    - **Ingen lagerplads tilknytning**– Hvis du ikke konfigurerer lagerplads tilknytning, virtuelle maskiner kan replikeres til standardplaceringen, der er angivet på Hyper-V host serveren er knyttet til den replika virtuelle maskine.

- **Lokalt på lokale replikering med SAN**– du konfigurere lagerplads tilknytning af tilknytning lagerplads matrixer grupper på en kilde og målrette VMM servere.
    - **Angiv puljen**– angiver, hvilke sekundær lagerpulje modtager gentagelse data fra den primære gruppe.
    - **Identificer target lagerplads grupper**– sikrer, at logiske enheder i en kilde gentagelse gruppe replikeres til tilknyttede target lagerpulje efter eget valg.

## <a name="set-up-storage-classifications-for-hyper-v-replication"></a>Konfigurere lagerplads klassificeringer af Hyper-V replikering

Når du bruger Hyper-V replika for at gentage med gendannelse af websteder, kan du knytte mellem lagerplads klassificeringer på kilde- og destinationswebsteder VMM-servere eller på en enkelt VMM server Hvis to steder administreres af den samme VMM-server. Bemærk, at:

- Når tilknytning er konfigureret korrekt, og replikering er aktiveret, replikeres et virtuelt virtuel harddisk på den primære placering til lagring i det tilknyttede destinationsplacering.
- Lagerplads klassificeringer skal være tilgængelige for grupperne host placeret i kilde- og destinationswebsteder skyer.
- Klassificeringer behøver ikke at have samme type lagerplads. For eksempel kan du knytte en kilde klassifikation, der indeholder små og mellemstore virksomheder aktier til en destination klassifikation, der indeholder CSVs.
- Få mere at vide i [hvordan du opretter lagerplads klassificeringer i VMM](https://technet.microsoft.com/library/gg610685.aspx).

## <a name="example"></a>Eksempel

Hvis klassificeringer konfigureret korrekt i VMM, når du vælger den kilde- og destinationswebsteder VMM server under lagerplads tilknytningen, vises de kilde- og destinationswebsteder klassificeringer. Her er et eksempel på lagerplads filshares og klassificeringer for en organisation med to forskellige steder i New York og Chicago.

**Placering** | **VMM server** | **Filshare (kilde)** | **Klassifikation (kilde)** | **Tilknyttet** | **Filshare (mål)**
---|---|--- |---|---|---
New York | VMM_Source| SourceShare1 | GULD | GOLD_TARGET | TargetShare1
 |  | SourceShare2 | SILVER | SILVER_TARGET | TargetShare2
 | | SourceShare3 | BRONZE | BRONZE_TARGET | TargetShare3
Chicago | VMM_Target |  | GOLD_TARGET | Ikke er tilknyttet |
| | | SILVER_TARGET | Ikke er tilknyttet |
 | | | BRONZE_TARGET | Ikke er tilknyttet

Du vil konfigurere disse under fanen **Server lagerplads** på siden **ressourcer** i portalen gendannelse af websteder.

![Konfigurer tilknytning af lager](./media/site-recovery-storage-mapping/storage-mapping1.png)

Med dette eksempel:
- Når en, der oprettes en replika virtuel maskine til en hvilken som helst virtual machine på GULD lagerplads (SourceShare1) kan replikeres til en GOLD_TARGET lager (TargetShare1).
- Når der oprettes en replika virtuel maskine til en hvilken som helst virtual machine på SILVER lagerplads (SourceShare2), kan replikeres til en SILVER_TARGET (TargetShare2)-lager, og så videre.

De faktiske filshares og deres tildelte klassificeringer i VMM vises i det næste skærmbillede.

![Lagerplads klassificeringer i VMM](./media/site-recovery-storage-mapping/storage-mapping2.png)

## <a name="multiple-storage-locations"></a>Flere lagerplads placeringer

Hvis destinationen klassifikation tildeles til flere små og mellemstore virksomheder aktier eller CSVs, markeres optimal lagerplaceringen for automatisk, når den virtuelle maskine er beskyttet. Hvis uden passende target lager er tilgængelige med den angivne klassificering, bruges standardplaceringen, der er angivet på værten Hyper-V til at placere replika virtuelle harddiske.

Tabellen nedenfor viser, hvordan lagerplads klassificering og klynge delt enheder er konfigureret i vores eksempel.

**Placering** | **Klassifikation** | **Tilknyttede lagerplads**
---|---|---
New York | GULD | <p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p>
 | SILVER | <p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p>
Chicago | GOLD_TARGET | <p>C:\ClusterStorage\TargetVolume1</p><p>\\FileServer\TargetShare1</p>
 | SILVER_TARGET| <p>C:\ClusterStorage\TargetVolume2</p><p>\\FileServer\TargetShare2</p>

I denne tabel opsummeres funktionsmåden, når du aktiverer beskyttelse af virtuelle maskiner (VM1 - VM5) i dette eksempel-miljø.

**Virtuelt** | **Kilde-lagerplads** | **Kilde klassifikation** | **Tilknyttede target lagerplads**
---|---|---|---
VM1 | C:\ClusterStorage\SourceVolume1 | GULD | <p>C:\ClusterStorage\SourceVolume1</p><p>\\\FileServer\SourceShare1</p><p>Begge GOLD_TARGET</p>
VM2 | \\FileServer\SourceShare1 | GULD | <p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p> <p>Begge GOLD_TARGET</p>
VM3 | C:\ClusterStorage\SourceVolume2 | SILVER | <p>C:\ClusterStorage\SourceVolume2</p><p>\FileServer\SourceShare2</p>
VM4 | \FileServer\SourceShare2 | SILVER |<p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p><p>Begge SILVER_TARGET</p>
VM5 | C:\ClusterStorage\SourceVolume3 | I/T. | Ingen tilknytning, så standardplaceringen af værten Hyper-V bruges

## <a name="next-steps"></a>Næste trin

Nu hvor du har en bedre forståelse af lagerplads tilknytning, [er du klar til at udrulle Azure gendannelse af websteder](site-recovery-best-practices.md).
