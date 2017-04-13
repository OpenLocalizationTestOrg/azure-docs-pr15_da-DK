<properties
 pageTitle="Om Beregn-intensivt FOS med Linux | Microsoft Azure"
 description="Få baggrundsoplysninger og overvejelser ved brug af H-serien og A8, A9, A10 og A11 Beregn-intensivt størrelsen for Linux FOS"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>

# <a name="about-h-series-and-compute-intensive-a-series-vms"></a>Om H-serien og Beregn-intensivt A-serien FOS 

Her er baggrundsoplysninger og nogle overvejelser ved brug af nyere Azure H-serien og de tidligere A8, A9, A10 og A11 størrelser, også kaldet *Beregn-intensivt* forekomster. I denne artikel fokuserer på ved hjælp af disse størrelser til Linux FOS. I denne artikel findes også for [Windows FOS](virtual-machines-windows-a8-a9-a10-a11-specs.md).




[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Adgang til netværket RDMA

Du kan oprette klynger af RDMA-kompatible Linux FOS, at køre en af følgende understøttede Linux HPC salgsdistributioner og en understøttede MPI implementering udnytte Azure RDMA netværket. Se [konfigurere en Linux RDMA klynge for at køre MPI programmer](virtual-machines-linux-classic-rdma-cluster.md) til installationsindstillinger og trinnene til konfiguration af eksempel.

* **Salgsdistributioner** - du skal installere FOS fra RDMA-kompatible SUSE Linux Enterprise Server (SLES) eller OpenLogic CentOS-baserede HPC billeder i Azure Marketplace. Kun følgende Marketplace billeder understøtter de nødvendige Linux RDMA drivere:

    * SLES 12 SP1 for HPC, SLES 12 SP1 til HPC (Premium)
    
    * SLES 12 for HPC, SLES 12 for HPC (Premium)
    
    * CentOS-baserede 7.1 HPC
    
    * CentOS-baserede 6.5 HPC
    
    >[AZURE.NOTE]For H-serien FOS anbefaler vi enten en SLES 12 SP1 til HPC billede eller CentOS-baseret 7.1 HPC afbildning.
    >
    >På billeder CentOS-baserede HPC er kerneopdateringer deaktiveret i **yum** konfigurationsfil. Dette skyldes, at Linux RDMA driverne fordeles som en RPM-pakke, og driveropdateringer fungerer muligvis ikke, hvis kernen opdateres.

* **MPI** - Intel MPI bibliotek 5.x

    Afhængigt af Marketplace billedet du vælger, separat licenser, installation eller konfiguration af Intel MPI det kan være nødvendigt, på følgende måde: 
    
    * **SLES 12 SP1 til HPC billede** - Installer Intel MPI pakker distribueres på VM ved at køre følgende kommando:
    
            sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm

    * **SLES 12 for HPC billede** - du skal separat register for at hente og installere Intel MPI. Se [installationsvejledningen til Intel MPI bibliotek](https://software.intel.com/sites/default/files/managed/7c/2c/intelmpi-2017-installguide-linux.pdf).
    
    * **CentOS-baserede HPC billeder** - Intel MPI 5.1 er allerede installeret.  

    Yderligere systemkonfiguration skal bruges til at køre MPI job på grupperet FOS. For eksempel på en klynge FOS skal du oprette tillid mellem noderne Beregn. Indstillinger for typisk under [konfigurere en Linux RDMA klynge for at køre MPI programmer](virtual-machines-linux-classic-rdma-cluster.md).


## <a name="considerations-for-hpc-pack-and-linux"></a>Overvejelser i forbindelse med HPC Pack og Linux

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), Microsofts gratis HPC-klynge og job management-løsning, indeholder en af mulighederne for dig at bruge Beregn-intensivt forekomster med Linux. Den seneste version af understøttelse af HPC Pack 2012 R2 flere Linux salgsdistributioner til at køre på beregne noder, der er implementeret i Azure FOS, administreres af en Windows Server hoved node. Med RDMA-kompatible Linux Beregn noder, der kører Intel MPI, HPC Pack planlægge og køre Linux MPI programmer, få adgang til RDMA netværket. For at komme i gang skal du se [komme i gang med Linux Beregn noder i en HPC Pack klynge i Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## <a name="network-topology-considerations"></a>Overvejelser i forbindelse med netværk topologi

* På RDMA aktiverede Linux FOS i Azure, er Eth1 reserveret til RDMA netværkstrafik. Undlad at ændre noget Eth1 eller andre oplysninger i den konfigurationsfil, der refererer til dette netværk. Eth0 er reserveret til almindelige Azure netværkstrafik.

* I Azure understøttes ikke IP over InfiniBand (b). Kun RDMA over b understøttes.

## <a name="rdma-driver-updates-for-sles-12"></a>RDMA driveropdateringer til SLES 12

Når du opretter en VM, der er baseret på en SLES 12 HPC billede, skal du muligvis opdatere RDMA driverne på FOS for RDMA netværksforbindelse. 

>[AZURE.IMPORTANT]Dette trin er **påkrævet** til SLES 12 til HPC VM installationer i alle Azure områder. 
>Dette trin ikke er påkrævet, hvis du installerer en SLES 12 SP1 for HPC, CentOS-baserede 7.1 HPC eller CentOS-baserede 6.5 HPC VM. 

Før du opdaterer driverne, stop alle **zypper** processer eller alle processer, Lås SUSE repo databaser i VM. Ellers opdateres driverne muligvis ikke korrekt.  

For at opdatere Linux RDMA drivere på hver VM, skal du køre en af følgende sæt af Azure CLI-kommandoer fra klientcomputeren.

**SLES 12 for HPC VM klargjort i modellen Klassisk installation**

```
azure config mode asm

azure vm extension set <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

**SLES 12 for HPC VM klargjort i implementeringsmodel ressourcestyring**

```
azure config mode arm

azure vm extension set <resource-group> <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

>[AZURE.NOTE]Det kan tage lidt tid at installere driverne, og kommandoen returnerer uden output. EfterOpdatering, din VM genstartes og skal være klar til brug i nogle minutter.

### <a name="sample-script-for-driver-updates"></a>Eksempel på et script til driveropdateringer

Hvis du har en klynge af SLES 12 for HPC FOS, kan du scripts opdateringen driver på tværs af alle noder i din klynge. For eksempel opdaterer følgende script drivere på en 8-node klynge.

```

#!/bin/bash -x

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Plug in appropriate numbers in the for loop below.

for (( i=11; i<19; i++ )); do

# For VMs created in the classic deployment model, use the following command in your script.

azure vm extension set $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

# For VMs created in the Resource Manager deployment model, use the following command in your script.

# azure vm extension set <resource-group> $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

done

```


## <a name="next-steps"></a>Næste trin

* Få mere at vide om tilgængeligheden af og priser af Beregn-intensivt størrelser se [virtuelle maskiner priser](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux).

* Se [størrelser for virtuelle maskiner](virtual-machines-linux-sizes.md)til lagerkapacitet og disk detaljer.

* For at komme i gang installerer og bruger Beregn-intensivt størrelser med RDMA på Linux skal du se [konfigurere en Linux RDMA klynge for at køre MPI programmer](virtual-machines-linux-classic-rdma-cluster.md).


