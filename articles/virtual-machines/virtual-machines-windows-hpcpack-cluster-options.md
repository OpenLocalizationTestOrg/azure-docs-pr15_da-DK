<properties
 pageTitle="Indstillinger for Windows HPC Pack klynge i skyen | Microsoft Azure"
 description="Få mere at vide om indstillinger med Microsoft HPC Pack til at oprette og administrere en Windows høj ydeevne computing (HPC) klynge i Azure skyen"
 services="virtual-machines-windows,cloud-services,batch"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="09/26/2016"
 ms.author="danlep"/>

# <a name="options-with-hpc-pack-to-create-and-manage-a-windows-hpc-cluster-in-azure"></a>Indstillinger med HPC Pack til at oprette og administrere en Windows HPC klynge i Azure

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

I denne artikel fokuserer på Indstillinger for at oprette HPC Pack klynger for at køre Windows arbejdsmængder. Der er også indstillinger for oprettelse af klynger til at køre [Linux HPC arbejdsmængder med HPC Pack](virtual-machines-linux-hpcpack-cluster-options.md).


## <a name="run-an-hpc-pack-cluster-in-azure-vms"></a>Køre en HPC Pack klynge i Azure FOS

### <a name="azure-templates"></a>Azure-skabeloner

* (Marketplace) [HPC Pack klynge i Windows arbejdsmængder](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)

* (Marketplace) [HPC Pack klynge i arbejdsbelastninger, som Excel](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)

* (Hurtig start) [Opret en HPC-klynge](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)

* (Hurtig start) [Opret en HPC-klynge med brugerdefinerede Beregn node billede](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Azure VM billeder

* [HPC Pack på Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)

* [HPC Pack beregningsnode på Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)

* [HPC Pack beregne node med Excel på Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)



### <a name="powershell-deployment-script"></a>Scriptet til implementering af PowerShell

* [Oprette en HPC-klynge med HPC Pack IaaS installation script](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)

### <a name="tutorials"></a>Selvstudier

* [Selvstudium: Kom i gang med en HPC Pack klynge i Azure til at køre Excel og SOA arbejdsmængder](virtual-machines-windows-excel-cluster-hpcpack.md)



### <a name="manual-deployment-with-the-azure-portal"></a>Manuel installation ved hjælp af Azure portal

* [Konfigurere noden hoved i en HPC Pack klynge i en Azure VM](virtual-machines-windows-hpcpack-cluster-headnode.md)

### <a name="cluster-management"></a>Klyngeadministration

* [Administrere Beregn noder i en HPC Pack klynge i Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)

* [Forøge og formindske Azure Beregn ressourcer i en HPC Pack klynge](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md)

* [Sende job til en HPC Pack klynge i Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)

* [Administration af udskriftsjob i HPC Pack](https://technet.microsoft.com/library/jj899585.aspx)


## <a name="add-worker-role-nodes-to-an-hpc-pack-cluster"></a>Føje arbejder rolle noder til en HPC Pack klynge


* [Burst på forekomster af Azure arbejder med HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)

* [Selvstudium: Oprette en hybrid klynge med HPC Pack i Azure](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Føje Azure "burst" noder til en HPC Pack hoved node i Azure](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md)


## <a name="integrate-with-azure-batch"></a>Integrere med Azure batchen 

* [Burst til Azure batchen med HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)

## <a name="create-rdma-clusters-for-mpi-workloads"></a>Oprette RDMA klynger for MPI arbejdsmængder

* [Konfigurere en Windows RDMA klynge med HPC Pack til at køre MPI programmer](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)
