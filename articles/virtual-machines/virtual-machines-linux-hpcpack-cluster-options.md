<properties
 pageTitle="Linux HPC Pack klynge indstillinger i skyen | Microsoft Azure"
 description="Få mere at vide om indstillinger med Microsoft HPC Pack til at oprette og administrere en Linux høj ydeevne computing (HPC) klynge i Azure skyen"
 services="virtual-machines-linux,cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="09/26/2016"
 ms.author="danlep"/>

# <a name="options-with-hpc-pack-to-create-and-manage-an-hpc-cluster-in-azure-for-linux-workloads"></a>Indstillinger med HPC Pack til at oprette og administrere en HPC-klynge i Azure for Linux arbejdsmængder

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

I denne artikel fokuserer på Indstillinger for at bruge HPC Pack til at køre Linux arbejdsmængder. Der er også indstillinger til at køre [Windows HPC arbejdsmængder med HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md).

## <a name="run-an-hpc-pack-cluster-in-azure-vms"></a>Køre en HPC Pack klynge i Azure FOS

### <a name="azure-templates"></a>Azure-skabeloner


* (Marketplace) [HPC Pack klynge for Linux arbejdsmængder](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)

* (Hurtig start) [Opret en HPC-klynge med Linux beregne noder](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)


### <a name="powershell-deployment-script"></a>Scriptet til implementering af PowerShell

* [Oprette en Linux HPC-klynge med HPC Pack IaaS installation script](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md)

### <a name="tutorials"></a>Selvstudier

* [Selvstudium: Introduktion til Linux Beregn noder i en HPC Pack klynge i Azure](virtual-machines-linux-classic-hpcpack-cluster.md)

* [Selvstudium: Kør NAMD med Microsoft HPC Pack på Linux beregne noder i Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)

* [Selvstudium: Kør OpenFOAM med Microsoft HPC Pack på en Linux RDMA klynge i Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [Selvstudium: Kør STJERNE-CCM + med Microsoft HPC Pack på en Linux RDMA klynge i Azure](virtual-machines-linux-classic-hpcpack-cluster-starccm.md)

### <a name="cluster-management"></a>Klyngeadministration

* [Sende job til en HPC Pack klynge i Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)

* [Administration af udskriftsjob i HPC Pack](https://technet.microsoft.com/library/jj899585.aspx)


## <a name="create-rdma-clusters-for-mpi-workloads"></a>Oprette RDMA klynger for MPI arbejdsmængder

* [Selvstudium: Kør OpenFOAM med Microsoft HPC Pack på en Linux RDMA klynge i Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [Konfigurere en Linux RDMA klynge til at køre MPI programmer](virtual-machines-linux-classic-rdma-cluster.md)

