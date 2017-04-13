<properties
    pageTitle="Administrere FOS i et virtuelt skala sæt | Microsoft Azure"
    description="Administrere virtuelle maskiner i et virtuelt skala sæt med Azure PowerShell."
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="manage-virtual-machines-in-a-virtual-machine-scale-set"></a>Administrere virtuelle maskiner i et virtuelt skala sæt

Brug opgaver i denne artikel til at administrere virtuelle maskiner i dit virtuelt skala sæt.

De fleste af de opgaver, der involverer administration af en virtuel maskine i et sæt skala kræver, at du kender forekomst-ID på den computer, du vil administrere. Du kan bruge [Azure ressource Stifinder](https://resources.azure.com) til at finde en virtuel maskine forekomst ID i et sæt skala. Du kan også bruge ressource Explorer til at kontrollere status for de opgaver, som du er færdig.

Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) for at få oplysninger om at installere den nyeste version af Azure PowerShell, vælge dit abonnement og logge på din konto.

## <a name="display-information-about-a-scale-set"></a>Få vist oplysninger om nogle skala

Du kan finde generelle oplysninger om et sæt af skalering, som kaldes også visningen forekomst. Eller du kan få mere specifikke oplysninger som oplysninger om ressourcerne i dialogboksen Angiv skala.

Erstatte de tilbudte værdier med navnet eller ressourcegruppe og skala angive og derefter køre kommandoen:

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

Den returnerer nogenlunde sådan ud:

    Id                                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                                        : myvmss1
    Type                                        : Microsoft.Compute/virtualMachineScaleSets
    Location                                    : centralus
    Sku                                         :
      Name                                      : Standard_A0
      Tier                                      : Standard
      Capacity                                  : 3
    UpgradePolicy                               :
      Mode                                      : Manual
    VirtualMachineProfile                       :
      OsProfile                                 :
        ComputerNamePrefix                      : vmss1
        AdminUsername                           : admin1
        WindowsConfiguration                    :
          ProvisionVMAgent                      : True
          EnableAutomaticUpdates                : True
    StorageProfile                              :
      ImageReference                            :
        Publisher                               : MicrosoftWindowsServer
        Offer                                   : WindowsServer
        Sku                                     : 2012-R2-Datacenter
        Version                                 : latest
      OsDisk                                    :
        Name                                    : vmssosdisk
        Caching                                 : ReadOnly
        CreateOption                            : FromImage
        VhdContainers[0]                        : https://astore.blob.core.windows.net/vmss
        VhdContainers[1]                        : https://gstore.blob.core.windows.net/vmss
        VhdContainers[2]                        : https://mstore.blob.core.windows.net/vmss
        VhdContainers[3]                        : https://sstore.blob.core.windows.net/vmss
        VhdContainers[4]                        : https://ystore.blob.core.windows.net/vmss
    NetworkProfile                              :
      NetworkInterfaceConfigurations[0]         :
        Name                                    : mync1
        Primary                                 : True
        IpConfigurations[0]                     :
          Name                                  : ip1
          Subnet                                :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/virtualNetworks/myvn1/subnets/mysn1
          LoadBalancerBackendAddressPools[0]    :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/backendAddressPools/bepool1
        LoadBalancerInboundNatPools[0]          :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/inboundNatPools/natpool1
    ExtensionProfile                            :
      Extensions[0]                             :
        Name                                    : Microsoft.Insights.VMDiagnosticsSettings
        Publisher                               : Microsoft.Azure.Diagnostics
        Type                                    : IaaSDiagnostics
        TypeHandlerVersion                      : 1.5
        AutoUpgradeMinorVersion                 : True
        Settings                                : {"xmlCfg":"...","storageAccount":"astore"}
    ProvisioningState                           : Succeeded
    
Erstatte de tilbudte værdier med navnet på dit ressource gruppe og skalering sæt. Erstatte *#* med forekomst-id på den virtuelle maskine, du vil have oplysninger om, og kør den:

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
        
Den returnerer noget i retning af dette eksempel:

    Id                            : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/
                                    virtualMachineScaleSets/myvmss1/virtualMachines/0
    Name                          : myvmss1_0
    Type                          : Microsoft.Compute/virtualMachineScaleSets/virtualMachines
    Location                      : centralus
    InstanceId                    : 0
    Sku                           :
      Name                        : Standard_A0
      Tier                        : Standard
    LatestModelApplied            : True
    StorageProfile                :
      ImageReference              :
        Publisher                 : MicrosoftWindowsServer
        Offer                     : WindowsServer
        Sku                       : 2012-R2-Datacenter
        Version                   : 4.0.20160617
      OsDisk                      :
        OsType                    : Windows
        Name                      : vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8
        Vhd                       :
          Uri                     : https://astore.blob.core.windows.net/vmss/vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8.vhd
        Caching                   : ReadOnly
        CreateOption              : FromImage
    OsProfile                     :
      ComputerName                : myvmss1-0
      AdminUsername               : admin1
      WindowsConfiguration        :
        ProvisionVMAgent          : True
        EnableAutomaticUpdates    : True
    NetworkProfile                :
      NetworkInterfaces[0]        :
        Id                        : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/
                                    myvmss1/virtualMachines/0/networkInterfaces/mync1
    ProvisioningState             : Succeeded
    Resources[0]                  :
      Id                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachines/
                                    myvmss1_0/extensions/Microsoft.Insights.VMDiagnosticsSettings
      Name                        : Microsoft.Insights.VMDiagnosticsSettings
      Type                        : Microsoft.Compute/virtualMachines/extensions
      Location                    : centralus
      Publisher                   : Microsoft.Azure.Diagnostics
      VirtualMachineExtensionType : IaaSDiagnostics
      TypeHandlerVersion          : 1.5
      AutoUpgradeMinorVersion     : True
      Settings                    : {"xmlCfg":"...","storageAccount":"astore"}
      ProvisioningState           : Succeeded
        
## <a name="start-a-virtual-machine-in-a-scale-set"></a>Starte en virtuel maskine i et sæt skala

Erstatte de tilbudte værdier med navnet på dit ressource gruppe og skalering sæt. Erstatte *#* med id'et for den virtuelle maskine, du vil starte og køre den:

    Start-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

Ressource Stifinder, kan vi se **, at status for forekomsten kører**:

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T02:10:08.0730839+00:00"
      },
      {
        "code": "PowerState/running",
        "level": "Info",
        "displayStatus": "VM running"
      }
    ]

Du kan starte virtuelle maskiner i skalaen angive undlader at bruge parameteren - InstanceId.
    
## <a name="stop-a-virtual-machine-in-a-scale-set"></a>Stoppe en virtuel maskine i et sæt skala

Erstatte de tilbudte værdier med navnet på dit ressource gruppe og skalering sæt. Erstatte *#* med id'et for den virtuelle maskine, du vil stoppe og derefter køre den:

    Stop-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

Ressource Stifinder, kan vi se, at status for forekomsten er **deallokeres**:

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T01:25:17.8792929+00:00"
      },
      {
        "code": "PowerState/deallocated",
        "level": "Info",
        "displayStatus": "VM deallocated"
      }
    ]
    
Hvis du vil standse en virtuel maskine deallokere ikke det, kan du bruge parameteren - StayProvisioned. Du kan stoppe alle virtuelle maskiner i dialogboksen Angiv undlader at bruge parameteren - InstanceId.
    
## <a name="restart-a-virtual-machine-in-a-scale-set"></a>Genstarte en virtuel maskine i et sæt skala

Erstatte de tilbudte værdier med navnet på din ressourcegruppe og sættet skala. Erstatte *#* med id'et for den virtuelle maskine, som du vil genstarte og derefter køre den:

    Restart-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
    
Du kan genstarte alle virtuelle maskiner i dialogboksen Angiv undlader at bruge parameteren - InstanceId.

## <a name="remove-a-virtual-machine-from-a-scale-set"></a>Fjerne en virtuel maskine fra et sæt skala

Erstatte de tilbudte værdier med navnet på din ressourcegruppe og sættet skala. Erstatte *#* med id'et for den virtuelle maskine, du vil fjerne, og kør den:  

    Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId #

Du kan fjerne virtuelt skala sættet på én gang undlader at bruge parameteren - InstanceId.

## <a name="change-the-capacity-of-a-scale-set"></a>Ændre kapaciteten af et sæt skala

Du kan tilføje eller fjerne virtuelle maskiner ved at ændre kapaciteten af sættet. Få sættet skala, du vil ændre, angive kapacitet til det, du vil være og derefter opdatere skalaen konfigureret med den nye kapacitet. Erstat tilbudte værdierne i disse kommandoer med navnet på din ressourcegruppe og sættet skala.

  $vmss = get-AzureRmVmss - ResourceGroupName "ressource gruppenavn" - VMScaleSetName "skala set name" $vmss.sku.capacity = 5 opdatering AzureRmVmss - ResourceGroupName "gruppe ressourcenavn"-navn "skalere sætnavn" - VirtualMachineScaleSet $vmss 

Hvis du vil fjerne virtuelle maskiner fra sættet skala, fjernes de virtuelle maskiner med de højeste id'er først.
