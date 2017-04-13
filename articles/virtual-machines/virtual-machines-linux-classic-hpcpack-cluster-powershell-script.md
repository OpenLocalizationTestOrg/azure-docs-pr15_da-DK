<properties
   pageTitle="PowerShell-script til at udrulle Linux HPC-klynge | Microsoft Azure"
   description="Køre en PowerShell-script for at installere en Linux HPC Pack klynge i Azure virtuelle maskiner"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="big-compute"
   ms.date="07/07/2016"
   ms.author="danlep"/>

# <a name="create-a-linux-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Oprette en Linux høj ydeevne computing (HPC) klynge med HPC Pack IaaS installation script

Kør installationen HPC Pack IaaS PowerShell-script til at implementere en komplet HPC-klynge for Linux arbejdsbelastninger på Azure virtuelle computere. Klyngen består af en Active Directory, der er joinforbundne hoved node, som kører Windows Server og Microsoft HPC Pack, og Beregn noder, der kører en af Linux fordelingerne understøttes af HPC Pack. Hvis du vil installere en HPC Pack klynge i arbejdsbelastninger, som Azure til Windows, skal du se [oprette en Windows HPC klynge med scriptet HPC Pack IaaS installation](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md). Du kan også bruge en skabelon til Azure ressourcestyring til at installere en HPC Pack klynge. Et eksempel, skal du se [oprette en HPC-klynge med Linux beregne noder](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-file"></a>Eksempel konfigurationsfil

Følgende konfigurationsfil opretter en ny domænecontrolleren og domæner og installerer en HPC Pack klynge som har 1 hoved node med lokale databaser og 10 Linux Beregn noder. Alle skytjenester oprettes direkte i Østasien placering. Noderne Linux Beregn er oprettet i 2-skytjenester og 2 lagerplads konti (det vil sige _MyLnxCN-0001_ til _MyLnxCN-0005_ i _MyLnxCNService01_ og _mylnxstorage01_) og _MyLnxCN-0006_ til _MyLnxCN 0010_ i _MyLnxCNService02_ og _mylnxstorage02_. Beregn knuderne er oprettet ud fra et OpenLogic CentOS version 7.0 Linux billede. 

Erstatte dine egne værdier for dit abonnementsnavn og navnene på konto og service.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
    </DomainController>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>10</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```
## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

* **"VNet findes ikke" fejl** – Hvis du kører scriptet HPC Pack IaaS installation for at installere flere klynger i Azure samtidigt under ét abonnement, mislykkes én eller flere-installationer med fejlen "VNet *VNet\_navn* findes ikke".
Hvis denne fejl opstår, igen køre scriptet til den mislykkede installation.

* **Problemet adgang til internettet fra Azure virtuelle netværket** – Hvis du opretter en HPC Pack klynge med et nyt domænenavn fra domænecontrolleren ved hjælp af scriptet installation, eller du manuelt hæve en hoved knude VM til domænecontrolleren, du oplever problemer med at forbinde FOS i det Azure virtuelle netværk til internettet. Dette kan ske, hvis en domænes DNS-server er automatisk konfigureret på domænecontrolleren, og denne domænes DNS-server ikke løser korrekt.

    Du kan løse dette problem, log på domænecontrolleren og enten Fjern indstillingen domænes konfiguration eller konfigurere en gyldig domænes DNS-server. For at gøre dette, i Server Manager skal du klikke på **værktøjer** >
    **DNS** for at åbne DNS Manager, og dobbeltklik derefter på **videresendelse**.
    
## <a name="next-steps"></a>Næste trin

* Se [komme i gang med Linux Beregn noder i en HPC Pack klynge i Azure](virtual-machines-linux-classic-hpcpack-cluster.md) for oplysninger om understøttede Linux fordelingerne, flytning af data og sende job til en HPC Pack klynge med Linux beregne noder.
* Du kan finde selvstudier, bruge scriptet til at oprette en klynge og køre en Linux HPC arbejdsbyrde, i:
    * [Køre NAMD med Microsoft HPC Pack på Linux Beregn noder i Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
    * [Køre OpenFOAM med Microsoft HPC Pack på Linux Beregn noder i Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)
    * [Køre STJERNE-CCM + med Microsoft HPC Pack på Linux beregne noder i Azure](virtual-machines-linux-classic-hpcpack-cluster-starccm.md)
