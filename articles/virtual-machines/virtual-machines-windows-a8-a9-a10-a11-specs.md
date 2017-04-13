<properties
 pageTitle="Om Beregn-intensivt FOS med Windows | Microsoft Azure"
 description="Få baggrundsoplysninger og overvejelser ved brug af Azure H-serien og A8, A9, A10 og A11 Beregn-intensivt størrelsen for Windows FOS og skyen"
 services="virtual-machines-windows, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>

# <a name="about-h-series-and-compute-intensive-a-series-vms"></a>Om H-serien og Beregn-intensivt A-serien FOS

Her er baggrundsoplysninger og nogle overvejelser ved brug af nyere Azure H-serien og de tidligere A8, A9, A10 og A11 forekomster, også kaldet *Beregn intensivt* forekomster. I denne artikel fokuserer på ved hjælp af disse forekomster til Windows FOS. I denne artikel findes også for [Linux FOS](virtual-machines-linux-a8-a9-a10-a11-specs.md).


[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Adgang til netværket RDMA

Du kan oprette klynger af forekomster af RDMA kan Windows Server og installere en af de understøttede MPI installationer for at kunne udnytte Azure RDMA netværket. Dette lav ventetid, høj overførselshastighed netværk er reserveret til MPI trafik kun.

* **Operativsystem**
    * **Virtuelle maskiner** – Windows Server 2012 R2, Windows Server 2012
    * **Cloud services** - Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 gæst OS familie

* **MPI** – Microsoft MPI (MS-MPI) 2012 R2 eller nyere, Intel MPI bibliotek 5.x

Understøttede MPI installationer bruge grænsefladen Microsoft netværk direkte til at kommunikere mellem forekomster. Se [konfigurere en Windows RDMA klynge med HPC Pack til at køre MPI programmer](virtual-machines-windows-classic-hpcpack-rdma-cluster.md) og [bruge flere forekomster opgaver til at køre der passerer MPI (Message Interface) programmer i Azure Batch](../batch/batch-mpi.md) til installationsindstillinger og trinnene til konfiguration af eksempel.


>[AZURE.NOTE]På RDMA-kompatible Beregn-intensivt FOS, skal filtypenavnet HpcVmDrivers føjes til FOS at installere Windows netværk-enhedsdrivere, der skal bruges til RDMA connectivity. I de fleste installationer føjes filtypenavnet HpcVmDrivers automatisk. Hvis du vil føje filtypenavnet dig selv, skal du se [administrere VM filtypenavne](virtual-machines-windows-classic-manage-extensions.md).

## <a name="considerations-for-hpc-pack-and-windows"></a>Overvejelser i forbindelse med HPC Pack og Windows

[Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), Microsofts gratis HPC-klynge og job management-løsning, er ikke påkrævet for du kan bruge Beregn-intensivt forekomster med Windows Server. Det er dog en af mulighederne at oprette en computerklynge i Azure til at køre Windows-baseret MPI programmer og andre HPC arbejdsmængder. Indeholder en runtime-miljø til MS-MPI, der kan bruge Azure RDMA netværket når installeret på RDMA-kompatible FOS, HPC Pack 2012 R2 og nyere versioner.

Flere oplysninger og kontrollister til at bruge Beregn-intensivt forekomster med HPC Pack på Windows Server, kan du se [konfigurere en Windows RDMA klynge med HPC Pack til at køre MPI programmer](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).




## <a name="next-steps"></a>Næste trin

* Se Få mere at vide om tilgængeligheden af og priser af Beregn-intensivt størrelser [virtuelle maskiner priser](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) og [Cloud Services priser](https://azure.microsoft.com/pricing/details/cloud-services/).

* Se [størrelser for virtuelle maskiner](virtual-machines-linux-sizes.md)til lagerkapacitet og disk detaljer.

* For at komme i gang installerer og bruger Beregn-intensivt forekomster med HPC Pack på Windows skal du se [konfigurere en Windows RDMA klynge med HPC Pack til at køre MPI programmer](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

* Oplysninger om brug af A8 og A9 forekomster til at køre MPI programmer med Azure batchen, finder du i [Brug med flere forekomster opgaver til at køre der passerer MPI (Message Interface) programmer i Azure Batch](../batch/batch-mpi.md).
