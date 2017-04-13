<properties
    pageTitle="Administrere FOS ved hjælp af Ressourcestyring og PowerShell | Microsoft Azure"
    description="Administrere virtuelle maskiner ved hjælp af Azure ressourcestyring og PowerShell."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="manage-azure-virtual-machines-using-resource-manager-and-powershell"></a>Administrere virtuelle Azure-computere ved hjælp af Ressourcestyring og PowerShell

## <a name="install-azure-powershell"></a>Installere Azure PowerShell
 
Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) for at få oplysninger om at installere den nyeste version af Azure PowerShell, vælge dit abonnement og logge på din konto.

## <a name="set-variables"></a>Angive variabler

Alle kommandoer i artiklen kræver navnet på den ressourcegruppe, hvor den virtuelle maskine er placeret og navnet på den virtuelle maskine til at administrere. Erstat værdien af **$rgName** med navnet på den ressourcegruppe, der indeholder den virtuelle maskine. Erstat værdien af **$vmName** med navnet på VM. Oprette variablerne.

    $rgName = "resource-group-name"
    $vmName = "VM-name"

## <a name="display-information-about-a-virtual-machine"></a>Få vist oplysninger om en virtuel maskine

Få oplysninger, virtuelt.
  
    Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Den returnerer noget i retning af dette eksempel:

    ResourceGroupName        : rg1
    Id                       : /subscriptions/{subscription-id}/resourceGroups/
                               rg1/providers/Microsoft.Compute/virtualMachines/vm1
    Name                     : vm1
    Type                     : Microsoft.Compute/virtualMachines
    Location                 : centralus
    Tags                     : {}
    AvailabilitySetReference : {
                                  "id": "/subscriptions/{subscription-id}/resourceGroups/
                                  rg1/providers/Microsoft.Compute/availabilitySets/av1"
                               }
    Extensions               : []
    HardwareProfile          : {
                                  "vmSize": "Standard_A0"
                               }
    InstanceView             : null
    NetworkProfile           : {
                                  "networkInterfaces": [
                                    {
                                      "properties.primary": null,
                                      "id": "/subscriptions/{subscription-id}/resourceGroups/
                                      rg1/providers/Microsoft.Network/networkInterfaces/nc1"
                                    }
                                  ]
                               }
    OSProfile                : {
                                  "computerName": "vm1",
                                  "adminUsername": "myaccount1",
                                  "adminPassword": null,
                                  "customData": null,
                                  "windowsConfiguration": {
                                    "provisionVMAgent": true,
                                    "enableAutomaticUpdates": true,
                                    "timeZone": null,
                                    "additionalUnattendContents": [],
                                    "winRM": null
                                  },
                                  "linuxConfiguration": null,
                                  "secrets": []
                               }
    Plan                     : null
    ProvisioningState        : Succeeded
    StorageProfile           : {
                                  "imageReference": {
                                    "publisher": "MicrosoftWindowsServer",
                                    "offer": "WindowsServer",
                                    "sku": "2012-R2-Datacenter",
                                    "version": "latest"
                                  },
                                  "osDisk": {
                                    "osType": "Windows",
                                    "encryptionSettings": null,
                                    "name": "osdisk",
                                    "vhd": {
                                      "Uri": "http://sa1.blob.core.windows.net/vhds/osdisk1.vhd"
                                    }
                                    "image": null,
                                    "caching": "ReadWrite",
                                    "createOption": "FromImage"
                                  }
                                  "dataDisks": [],
                               }
    DataDiskNames            : {}
    NetworkInterfaceIDs      : {/subscriptions/{subscription-id}/resourceGroups/
                                rg1/providers/Microsoft.Network/networkInterfaces/nc1}

## <a name="stop-a-virtual-machine"></a>Stoppe en virtuel maskine

Stoppe den, der kørende virtuelle maskine.

    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Du bliver spurgt, om en bekræftelse:

    Virtual machine stopping operation
    This cmdlet will stop the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
        
Angiv **Y** for at stoppe den virtuelle maskine.

Når du har et par minutter returnerer den noget i retning af dette eksempel:

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:11:57 PM
    EndTime    : 9/13/2016 12:14:40 PM

## <a name="start-a-virtual-machine"></a>Starte en virtuel maskine

Starte den virtuelle maskine, hvis det ikke er længere.

    Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Når du har et par minutter returnerer den noget i retning af dette eksempel:

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:32:55 PM
    EndTime    : 9/13/2016 12:35:09 PM

Hvis du vil genstarte en virtuel maskine, der allerede kører, beskrives brug **Genstart AzureRmVM** herefter.

## <a name="restart-a-virtual-machine"></a>Genstarte en virtuel maskine

Genstarte den, der kørende virtuelle maskine.

    Restart-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Den returnerer noget i retning af dette eksempel:

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:54:40 PM
    EndTime    : 9/13/2016 12:55:54 PM

## <a name="delete-a-virtual-machine"></a>Slette en virtuel maskine

Slet den virtuelle maskine.  

    Remove-AzureRmVM -ResourceGroupName $rgName –Name $vmName

> [AZURE.NOTE] Du kan bruge den **-kraft** parameter for at springe bliver bedt om bekræftelse.

Hvis du ikke bruge parameteren - kraft for du bliver spurgt, om en bekræftelse:

    Virtual machine removal operation
    This cmdlet will remove the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Den returnerer noget i retning af dette eksempel:

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

## <a name="update-a-virtual-machine"></a>Opdatere en virtuel maskine

I dette eksempel vises, hvordan du opdaterer størrelsen på den virtuelle maskine.
        
    $vmSize = "Standard_A1"
    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    $vm.HardwareProfile.vmSize = $vmSize
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
    
Den returnerer noget i retning af dette eksempel:

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK
                              
Du kan finde en liste over tilgængelige størrelser for en virtuel maskine i [størrelser for virtuelle maskiner i Azure](virtual-machines-windows-sizes.md) .

## <a name="add-a-data-disk-to-a-virtual-machine"></a>Føje disken data til en virtuel maskine

I dette eksempel vises, hvordan du føjer en datadisk til en eksisterende virtuelt.

    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    Add-AzureRmVMDataDisk -VM $vm -Name "disk-name" -VhdUri "https://mystore1.blob.core.windows.net/vhds/datadisk1.vhd" -LUN 0 -Caching ReadWrite -DiskSizeinGB 1 -CreateOption Empty
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm

Disken, du tilføjer er ikke initialiseret. Hvis du vil initialiseret disken, skal du logge på til den og bruge disk management. Hvis du installeret WinRM og et certifikat på den, når du har oprettet, kan du bruge remote PowerShell initialiseret disken. Du kan også bruge filtypenavnet brugerdefineret script: 

    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"

Script-filen kan indeholde noget i retning af denne kode initialiseret diskene:

    $disks = Get-Disk |   Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { ([char]$_) }
    $count = 0
    $labels = @("data1","data2")

    foreach($d in $disks) {
        $driveLetter = $letters[$count].ToString()
        $d | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] `
            -Confirm:$false -Force 
        $count++
    }

## <a name="next-steps"></a>Næste trin

Hvis der var problemer med en installation, kan du se [fejlfinding ressource gruppe-installationer med Azure-portalen](../resource-manager-troubleshoot-deployments-portal.md)
