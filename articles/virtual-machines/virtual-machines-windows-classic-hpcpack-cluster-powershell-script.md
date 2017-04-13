<properties
   pageTitle="PowerShell-script til at udrulle Windows HPC-klynge | Microsoft Azure"
   description="Køre en PowerShell-script for at installere en Windows HPC Pack klynge i Azure virtuelle maskiner"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="big-compute"
   ms.date="07/07/2016"
   ms.author="danlep"/>

# <a name="create-a-windows-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Oprette en Windows (HPC) high-performance computing klynge med HPC Pack IaaS installation script

Kør installationen HPC Pack IaaS PowerShell-script til at implementere en komplet HPC-klynge for Windows arbejdsbelastninger på Azure virtuelle computere. Klyngen bestående af en Active Directory, der er joinforbundne hoved node, som kører Windows Server og Microsoft HPC Pack, og flere vinduer beregne ressourcer, du angiver. Hvis du vil installere en HPC Pack klynge i Azure for Linux arbejdsbelastninger, skal du se [oprette en Linux HPC klynge med scriptet HPC Pack IaaS installation](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md). Du kan også bruge en skabelon til Azure ressourcestyring til at installere en HPC Pack klynge. Eksempler, under [oprette en HPC-klynge](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/) og [oprette en HPC-klynge med en brugerdefineret beregne node billede](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-files"></a>Eksempel på konfigurationsfiler

Erstatte dine egne værdier for dit abonnement Id eller og navn på de konto og -tjenesten i eksemplerne nedenfor.

### <a name="example-1"></a>Eksempel 1

Følgende konfigurationsfil installerer en HPC Pack klynge, der indeholder en hoved node med lokale databaser og fem beregne noder, der kører Windows Server 2012 R2-operativsystemet. Alle skytjenester oprettes direkte på sted, vest USA. Noden hoved fungerer som domænenavn fra domænecontrolleren af domæner.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%1000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>5</NodeCount>
    <OSVersion>WindowsServer2012R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### <a name="example-2"></a>Eksempel 2

Følgende konfigurationsfil installerer en HPC Pack klynge i en eksisterende domæner. Klyngen har 1 hoved node med lokale databaser og 12 beregne noder med filtypenavnet BGInfo VM anvendt.
Automatisk installation af Windows-opdateringer er deaktiveret for alle FOS i domæner. Alle skytjenester oprettes direkte i Østasien placering. Beregn knuderne er oprettet i tre skytjenester og tre lagerplads konti: _MyHPCCN-0001_ til _MyHPCCN-0005_ i _MyHPCCNService01_ og _mycnstorage01_; _MyHPCCN-0006_ til _MyHPCCN0010_ i _MyHPCCNService02_ og _mycnstorage02_; og _MyHPCCN 0011_ til _MyHPCCN 0012_ i _MyHPCCNService03_ og _mycnstorage03_). Beregn knuderne er oprettet ud fra et eksisterende privat billede, der er hentet fra en beregningsnode. Automatisk forøge og formindske tjenesten er aktiveret med standard forøge og formindske intervaller.

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
     <NoWindowsAutoUpdate />
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
  </Certificates>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0001%</VMNamePattern>
<ServiceNamePattern>MyHPCCNService%01%</ServiceNamePattern>
<MaxNodeCountPerService>5</MaxNodeCountPerService>
<StorageAccountNamePattern>mycnstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>12</NodeCount>
    <ImageName HPCPackInstalled=”true”>MyHPCComputeNodeImage</ImageName>
    <VMExtensions>
       <VMExtension>
          <ExtensionName>BGInfo</ExtensionName>
          <Publisher>Microsoft.Compute</Publisher>
          <Version>1.*</Version>
       </VMExtension>
    </VMExtensions>
  </ComputeNodes>
  <AutoGrowShrink>
    <CertificateId>1</CertificateId>
  </AutoGrowShrink>
</IaaSClusterConfig>

```

### <a name="example-3"></a>Eksempel 3

Følgende konfigurationsfil installerer en HPC Pack klynge i en eksisterende domæner. Klyngen indeholder en hoved node, én databaseserver med en 500 GB datadisk, 2 broker noder, der kører Windows Server 2012 R2-operativsystemet og fem Beregn noder, der kører Windows Server 2012 R2-operativsystemet. Skytjenesten MyHPCCNService oprettes i gruppen forbindelse *MyIBAffinityGroup*, og de andre skytjenester er oprettet i gruppen forbindelse *MyAffinityGroup*. HPC Job Scheduler REST-API og HPC webportal er aktiveret på noden hoved.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>    
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>NewRemoteDB</DBOption>
    <DBVersion>SQLServer2014_Enterprise</DBVersion>
    <DBServer>
      <VMName>MyDBServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>ExtraLarge</VMSize>
      <DataDiskSizeInGB>500</DataDiskSizeInGB>
    </DBServer>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>A8</VMSize>
<NodeCount>5</NodeCount>
<AffinityGroup>MyIBAffinityGroup</AffinityGroup>
  </ComputeNodes>
  <BrokerNodes>
    <VMNamePattern>MyHPCBN-%0000%</VMNamePattern>
    <ServiceName>MyHPCBNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>2</NodeCount>
  </BrokerNodes>
</IaaSClusterConfig>
```



### <a name="example-4"></a>Eksempel 4

Følgende konfigurationsfil installerer en HPC Pack klynge i en eksisterende domæner. Klyngen har to hoved node med lokale databaser, der oprettes to Azure node skabeloner og tre størrelse mellem Azure noder oprettes til Azure node skabelon _AzureTemplate1_. En scriptfil kører på noden hoved, når noden hoved er konfigureret.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
<VMSize>ExtraLarge</VMSize>
    <PostConfigScript>c:\MyHNPostActions.ps1</PostConfigScript>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
    <Certificate>
      <Id>2</Id>
      <PfxFile>d:\mytestcert2.pfx</PfxFile>
    </Certificate>    
  </Certificates>
  <AzureBurst>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate1</TemplateName>
      <SubscriptionId>bb9252ba-831f-4c9d-ae14-9a38e6da8ee4</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc1</ServiceName>
      <StorageAccount>myteststorage1</StorageAccount>
      <NodeCount>3</NodeCount>
      <RoleSize>Medium</RoleSize>
    </AzureNodeTemplate>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate2</TemplateName>
      <SubscriptionId>ad4b9f9f-05f2-4c74-a83f-f2eb73000e0b</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc2</ServiceName>
      <StorageAccount>myteststorage2</StorageAccount>
      <Proxy>
        <UsesStaticProxyCount>false</UsesStaticProxyCount>     
        <ProxyRatio>100</ProxyRatio>
        <ProxyRatioBase>400</ProxyRatioBase>
      </Proxy>
      <OSVersion>WindowsServer2012</OSVersion>
    </AzureNodeTemplate>
  </AzureBurst>
</IaaSClusterConfig>
```

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med


* **"VNet findes ikke" fejl** – Hvis du kører scriptet for at installere flere klynger i Azure samtidigt under ét abonnement, mislykkes én eller flere-installationer med fejlen "VNet *VNet\_navn* findes ikke".
Hvis denne fejl opstår, kan du køre scriptet igen, før den mislykkede installation.

* **Problemet adgang til internettet fra Azure virtuelle netværket** – Hvis du opretter en klynge med et nyt domænenavn fra domænecontrolleren ved hjælp af scriptet installation, eller du manuelt hæve en hoved knude VM til domænecontrolleren, du oplever problemer med at forbinde FOS til internettet. Dette problem kan opstå, hvis en domænes DNS-server er automatisk konfigureret på domænecontrolleren, og denne domænes DNS-server ikke løser korrekt.

    Du kan løse dette problem, log på domænecontrolleren og enten Fjern indstillingen domænes konfiguration eller konfigurere en gyldig domænes DNS-server. Hvis du vil konfigurere denne indstilling, i Server Manager skal du klikke på **værktøjer** >
    **DNS** for at åbne DNS Manager, og dobbeltklik derefter på **videresendelse**.

* **Problemet adgang til RDMA netværk fra Beregn-intensivt FOS** - Hvis du tilføjer Windows Server Beregn eller broker node FOS ved hjælp af en RDMA-kompatible størrelse som A8 eller A9, du oplever problemer med at forbinde disse FOS RDMA programmet netværk. En af grundene til dette problem opstår, er Hvis filtypenavnet HpcVmDrivers ikke korrekt installeret, når FOS føjes til klyngen. Filtypenavnet kan for eksempel være fast i tilstanden installation.

    Du kan løse dette problem, skal du kontrollere tilstanden for udvidelsen i FOS. Hvis filtypenavnet ikke er installeret korrekt, du prøve at fjerne noderne fra HPC klynge og derefter tilføje knuderne igen. For eksempel kan du tilføje beregningsnode FOS ved at køre scriptet Tilføj HpcIaaSNode.ps1 på noden hoved.
    
## <a name="next-steps"></a>Næste trin

* Prøv at køre en test arbejdsbyrde på klyngen. Se et eksempel på HPC Pack i [Introduktion til](https://technet.microsoft.com/library/jj884144).

* Se [komme i gang med en HPC Pack klynge i Azure til at køre arbejdsbelastninger, som Excel og SOA](virtual-machines-windows-excel-cluster-hpcpack.md)et selvstudium scripts klynge installationen og køre en HPC arbejdsbyrde.

* Prøv HPC Pack værktøjer for at starte, stoppe, tilføje, og fjern Beregn noder fra en klynge, du opretter. Se [administrere beregne noder i en HPC Pack klynge i Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md).

* Hvis du vil er klar til at sende job til klyngen fra en lokal computer, skal du se [sende HPC job fra en lokal computer til en HPC Pack klynge i Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md).
