<properties
    pageTitle="Ændre størrelsen på en Windows VM | Microsoft Azure"
    description="Ændre størrelsen på en Windows virtuel maskine, der er oprettet i implementeringsmodel Ressourcestyring, ved hjælp af Azure Powershell."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="Drewm3"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="drewm"/>

    
# <a name="resize-a-windows-vm"></a>Ændre størrelsen på en Windows VM

I denne artikel beskrives, hvordan ændre størrelsen på en Windows-VM, der er oprettet i ressourcestyring installation modellen med Azure Powershell.

Når du opretter en virtuel maskine (VM), kan du skalere VM op eller ned ved at ændre VM størrelse. I nogle tilfælde skal du deallokere VM først. Dette kan ske, hvis den nye størrelse ikke er tilgængelig under hardware-klynge, der er i øjeblikket vært VM.

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Ændre størrelsen på en Windows-VM ikke er i en tilgængelighed

1. Liste over de VM størrelser, der er tilgængelige på den hardware klynge, hvor VM er hostet. 

    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName> 
    ```

2. Hvis den ønskede størrelse er angivet, skal du køre følgende kommandoer for at ændre størrelsen på VM. Hvis den ønskede størrelse ikke vises, skal du gå til trin 3.

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -VMName <vmName>
    $vm.HardwareProfile.VmSize = "<newVMsize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```

3. Hvis den ønskede størrelse ikke vises, skal du køre følgende kommandoer til deallokere VM, tilpasse størrelsen på den, og genstart VM.

    ```powershell
    $rgname = "<resourceGroupName>"
    $vmname = "<vmName>"
    Stop-AzureRmVM -ResourceGroupName $rgname -VMName $vmname -Force
    $vm = Get-AzureRmVM -ResourceGroupName $rgname -VMName $vmname
    $vm.HardwareProfile.VmSize = "<newVMSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName $rgname
    Start-AzureRmVM -ResourceGroupName $rgname -Name $vmname
    ```

> [AZURE.WARNING] En hvilken som helst dynamiske IP-adresser, der er tildelt til VM versioner har deallokeret VM. Diskene OS og data påvirkes ikke. 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Ændre størrelsen på en Windows-VM i et sæt tilgængelighed

Hvis den nye størrelse for en VM i et tilgængelighed sæt ikke er tilgængelig på den hardware klynge i øjeblikket er vært VM, skal alle FOS i sættet tilgængelighed kan deallokeres for at ændre størrelsen på VM. Du muligvis også nødt til at opdatere størrelsen på andre FOS i tilgængeligheden angive efter én VM er tilpasset. For at tilpasse en VM i et sæt tilgængelighed skal du udføre følgende trin.

1. Liste over de VM størrelser, der er tilgængelige på den hardware klynge, hvor VM er hostet.

    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName>
    ```

2. Hvis den ønskede størrelse er angivet, skal du køre følgende kommandoer for at ændre størrelsen på VM. Hvis den ikke vises, skal du gå til trin 3.

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -VMName <vmName>
    $vm.HardwareProfile.VmSize = "<newVmSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```

3. Hvis den ønskede størrelse ikke vises, skal du fortsætte med følgende trin for at deallokere alle FOS i sættet tilgængelighed, ændre størrelsen på FOS, og genstart dem.

4.  Stop alle FOS i sættet tilgængelighed.

    ```powershell
    $rg = "<resourceGroupName>"
    $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
    $vmIds = $as.VirtualMachinesReferences
    foreach ($vmId in $vmIDs){
        $string = $vmID.Id.Split("/")
        $vmName = $string[8]
        Stop-AzureRmVM -ResourceGroupName $rg -Name $vmName -Force
    } 
    ```
              
5.  Tilpas og genstarte FOS i sættet tilgængelighed.

    ```powershell
    $rg = "<resourceGroupName>"
    $newSize = "<newVmSize>"
    $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
    $vmIds = $as.VirtualMachinesReferences
    foreach ($vmId in $vmIDs){
        $string = $vmID.Id.Split("/")
        $vmName = $string[8]
        $vm = Get-AzureRmVM -ResourceGroupName $rg -Name $vmName
        $vm.HardwareProfile.VmSize = $newSize
        Update-AzureRmVM -ResourceGroupName $rg -VM $vm
        Start-AzureRmVM -ResourceGroupName $rg -Name $vmName
    }
    ```

## <a name="next-steps"></a>Næste trin

- Køre flere forekomster af VM yderligere skalerbarhed og skalere ud. Se [skalere Windows-computere i et virtuelt skala angive automatisk](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)kan finde flere oplysninger.



