<properties
    pageTitle="Azure gendannelse af websteder support matrix | Microsoft Azure"
    description="Indeholder en oversigt over de understøttede operativsystemer og komponenter til Azure gendannelse af websteder"
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

# <a name="azure-site-recovery-support-matrix"></a>Azure gendannelse af websteder support matrix

I denne artikel indeholder en oversigt over understøttede operativsystemer og -komponenter til Azure gendannelse af websteder installationer. En liste over understøttede komponenter og forudsætninger, der er tilgængelige for hvert installationsscenario i hvert tilsvarende installation artiklen, og dette dokument indeholder en oversigt over dem.

## <a name="supported-operating-systems-for-virtualization-servers"></a>Understøttede operativsystemer for virtualization servere


**Kilde** | **Mål** | **Host OS**
---|---|---|--- 
**Hyper-V hosts (uden VMM)** | Azure | Windows Server 2012 R2 med seneste opdateringer
**Hyper-V hosts (med VMM)** | Azure | Windows Server 2012 R2 med seneste opdateringer
**Hyper-V hosts (med VMM)** | Sekundær VMM websted | Mindst Windows Server 2012 med seneste opdateringer
**VMware hosts/vCenter** | Azure | vCenter 5.5 eller 6.0 (understøttelse af 5,5 funktioner) <br/><br/> vSphere 6.0, 5.5 eller 5.1 med seneste opdateringer
**VMware hosts/vCenter** | Sekundær VMware websted | vCenter 5.5 eller 6.0 (understøttelse af 5,5 funktioner) <br/><br/> vSphere 6.0, 5.5 eller 5.1 med seneste opdateringer

## <a name="supported-requirements-for-replicated-machines"></a>Understøttede krav til replikerede computere

**Kilde** | **Hvad replikeres** | **Mål** | **Host OS**
---|---|---|--- 
**Hyper-V FOS** | En hvilken som helst arbejdsbelastning | Azure | En hvilken som helst gæst OS [understøttes af Azure](https://technet.microsoft.com/library/cc794868.aspx)<br/><br/> FOS skal opfylde [Azure krav](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**Hyper-V FOS (med VMM)** | En hvilken som helst arbejdsbelastning | Azure | En hvilken som helst gæst OS [understøttes af Azure](https://technet.microsoft.com/library/cc794868.aspx)<br/><br/> FOS skal opfylde [Azure krav](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**Hyper-V FOS (med VMM)** | En hvilken som helst arbejdsbelastning | Sekundær VMM websted | En hvilken som helst gæst OS [understøttes af Hyper-V](https://technet.microsoft.com/library/mt126277.aspx)
**VMware FOS** | En hvilken som helst arbejdsbyrde, der kører på Windows VM | Azure | 64-bit Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 med på minimum SP1<br/><br/> FOS skal opfylde [Azure krav](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**VMware FOS** | En hvilken som helst arbejdsbyrde, der kører på Linux VM | Azure | Red Hat Enterprise Linux 6,7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6,7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 kører Red Hat kompatible kernen eller Unbreakable Enterprise kernen version 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Nødvendige lagerplads: filsystem (EXT3, ETX4, ReiserFS, XFS) MultiPath software-enhed Mapper (multipath)). Lydstyrken manager: (LVM2). Fysiske servere med HP CCISS controller lagerplads understøttes ikke. ReiserFS filsystemet understøttes kun på SUSE Linux Enterprise Server 11 SP3.<br/><br/> FOS skal opfylde [Azure krav](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**VMware FOS** | En hvilken som helst arbejdsbyrde, der kører på Windows VM | Sekundær VMware websted | 64-bit Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 med på minimum SP1
**VMware FOS** | En hvilken som helst arbejdsbyrde, der kører på Linux VM | Sekundær VMware websted | Red Hat Enterprise Linux 6,7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6,7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 kører Red Hat kompatible kernen eller Unbreakable Enterprise kernen version 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Nødvendige lagerplads: filsystem (EXT3, ETX4, ReiserFS, XFS) MultiPath software-enhed Mapper (multipath)). Lydstyrken manager: (LVM2). Fysiske servere med HP CCISS controller lagerplads understøttes ikke. ReiserFS filsystemet understøttes kun på SUSE Linux Enterprise Server 11 SP3.
**Fysiske servere** | En hvilken som helst arbejdsbyrde, der kører på Windows | Azure | 64-bit Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 med på minimum SP1
**Fysiske servere** | En hvilken som helst arbejdsbyrde, der kører på Linux | Azure | Red Hat Enterprise Linux 6.7,7.1,7.2 <br/><br/> Centos 6.5, 6.6,6.7,7.0,7.1,7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 kører Red Hat kompatible kernen eller Unbreakable Enterprise kernen version 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Nødvendige lagerplads: filsystem (EXT3, ETX4, ReiserFS, XFS) MultiPath software-enhed Mapper (multipath)). Lydstyrken manager: (LVM2). Fysiske servere med HP CCISS controller lagerplads understøttes ikke. ReiserFS filsystemet understøttes kun på SUSE Linux Enterprise Server 11 SP3.
**Fysiske servere** | En hvilken som helst arbejdsbyrde, der kører på Windows | Sekundær websted | 64-bit Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 med på minimum SP1
**Fysiske servere** | En hvilken som helst arbejdsbyrde, der kører på Linux | Sekundær websted | Red Hat Enterprise Linux 6.7,7.1,7.2 <br/><br/> Centos 6.5, 6.6,6.7,7.0,7.1,7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 kører Red Hat kompatible kernen eller Unbreakable Enterprise kernen version 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Nødvendige lagerplads: filsystem (EXT3, ETX4, ReiserFS, XFS) MultiPath software-enhed Mapper (multipath)). Lydstyrken manager: (LVM2). Fysiske servere med HP CCISS controller lagerplads understøttes ikke. ReiserFS filsystemet understøttes kun på SUSE Linux Enterprise Server 11 SP3.


## <a name="provider-versions"></a>Udbyder versioner

**Navn** | **Beskrivelse** | **Seneste version** | **Support** | **Detaljer**
---|---|---|---| ---
**Azure websted gendannelse udbyder** | Koordinaterne kommunikationen mellem lokale servere og Azure/sekundær webstedet <br/><br/> Installeret på lokale VMM servere eller Hyper-V servere, hvis der er ingen VMM server | 5.1.1700 (tilgængelig fra portalen) | [Nyeste funktioner og løsninger](https://support.microsoft.com/kb/3155002)
**Gendannelse af Azure websteder Unified konfiguration (VMware til Azure)** | Koordinaterne kommunikation mellem lokale VMware servere og Azure <br/><br/> Installeret på lokale VMware servere | 9.3.4246.1 (tilgængelig fra portalen) | [Nyeste funktioner og løsninger](https://support.microsoft.com/kb/3155002)
**Mobilitet service** | Koordinaterne replikering mellem lokale VMware servere/fysiske servere og Azure/sekundær websted | IKKE.tilgængelig (tilgængelig fra portalen) | Installeret på hver VMware VM eller fysisk server, du vil gentage. **Microsoft Azure gendannelse tjenester (MARS) agent** | Koordinaterne replikering mellem Hyper-V FOS og Azure<br/><br/> Installeret på lokale Hyper-V servere (med eller uden en VMM server) | 2.0.8689.0 (tilgængelig fra portalen) | Denne agent bruges af Azure sikkerhedskopiering og gendannelse af Azure websteder). [Få mere at vide] (https://support.microsoft.com/en-us/kb/2997692)

## <a name="next-steps"></a>Næste trin

[Forberede til installation](site-recovery-best-practices.md)

