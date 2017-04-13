<properties
    pageTitle="Oprette en Azure VM ved hjælp af PowerShell | Microsoft Azure"
    description="Brug Azure PowerShell og Azure ressourcestyring til nemt for at oprette en ny VM, der kører Windows Server."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/21/2016"
    ms.author="davidmu"/>

# <a name="create-a-windows-vm-using-resource-manager-and-powershell"></a>Oprette en Windows-VM ved hjælp af Ressourcestyring og PowerShell

I denne artikel beskrives, hvordan til hurtigt at oprette en Azure virtuel maskine, der kører Windows Server og de ressourcer, der skal bruges ved hjælp af [Ressourcestyring](../azure-resource-manager/resource-group-overview.md) og PowerShell. 

Alle trinnene i denne artikel er nødvendige for at oprette en virtuel maskine og tager om 30 minutter for at udføre trinnene. Erstat eksempel parameterværdier kommandoer med navne, der giver mening for dit miljø.

## <a name="step-1-install-azure-powershell"></a>Trin 1: Installere Azure PowerShell

Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) for at få oplysninger om at installere den nyeste version af Azure PowerShell, vælge dit abonnement og logge på din konto.
        
## <a name="step-2-create-a-resource-group"></a>Trin 2: Oprette en ressourcegruppe

Alle ressourcer, der skal indgå i en ressourcegruppe, så giver mulighed for at oprette det først.  

1. Få en liste over tilgængelige placeringer, hvor ressourcer, der kan oprettes.

    ```powershell
    Get-AzureRmLocation | sort Location | Select Location
    ```

2. Angive placering for ressourcerne. Denne kommando angiver placeringen til **centralus**.

    ```powershell
    $location = "centralus"
    ```
    
3. Oprette en ressourcegruppe. Denne kommando opretter ressourcegruppen med navnet **myResourceGroup** på den placering, som du fastsætter.

    ```powershell
    $myResourceGroup = "myResourceGroup"
    New-AzureRmResourceGroup -Name $myResourceGroup -Location $location
    ```
    
## <a name="step-3-create-a-storage-account"></a>Trin 3: Opret en konto til lager

En [lagerplads konto](../storage/storage-introduction.md) skal bruges til at gemme den virtuelle harddisk, der bruges af den virtuelle maskine, som du opretter. Lagerplads kontonavne skal være mellem 3 og 24 tegn og kan indeholde tal og små bogstaver.

1. Test kontonavn lager til at er entydige. Denne kommando afprøver navn **myStorageAccount**.

    ```powershell
    $myStorageAccountName = "mystorageaccount"
    Get-AzureRmStorageAccountNameAvailability $myStorageAccountName
    ```
    
    Hvis denne kommando returnerer **Sand**, er navnet på din foreslåede entydigt i Azure. 
    
2. Opret nu kontoen lagerplads.
    
    ```powershell    
    $myStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $myResourceGroup -Name $myStorageAccountName -SkuName "Standard_LRS" -Kind "Storage" -Location $location
    ```
    
## <a name="step-4-create-a-virtual-network"></a>Trin 4: Oprette et virtuelt netværk

Alle virtuelle maskiner er en del af et [virtuelt netværk](../virtual-network/virtual-networks-overview.md).

1. Oprette et undernet for det virtuelle netværk. Denne kommando opretter et undernet med navnet **mySubnet** med en adressepræfiks af 10.0.0.0/24.
        
    ```powershell
    $mySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 10.0.0.0/24
    ```
    
2. Opret nu det virtuelle netværk. Denne kommando opretter et virtuelt netværk med navnet **myVnet** ved hjælp af det undernet, som du har oprettet og en adressepræfikset for **10.0.0.0/16**.

    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $mySubnet
    ```
        
## <a name="step-5-create-a-public-ip-address-and-network-interface"></a>Trin 5: Oprette en offentlig IP-adresse og netværk brugergrænseflade

For at aktivere kommunikation med den virtuelle maskine i det virtuelle netværk, skal have du en [offentlig IP-adresse](../virtual-network/virtual-network-ip-addresses-overview-arm.md) og en netværksgrænseflade.

1. Oprette den offentlige IP-adresse. Denne kommando opretter en offentlig IP-adresse med navnet **myPublicIp** med metoden en fordeling af **dynamisk**.
 
    ```powershell
    $myPublicIp = New-AzureRmPublicIpAddress -Name "myPublicIp" -ResourceGroupName $myResourceGroup -Location $location -AllocationMethod Dynamic
    ```
        
2. Oprette netværksgrænsefladen. Denne kommando opretter en netværksgrænseflade med navnet **myNIC**.

    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup -Location $location -SubnetId $myVnet.Subnets[0].Id -PublicIpAddressId $myPublicIp.Id
    ```
       
## <a name="step-6-create-a-virtual-machine"></a>Trin 6: Oprette en virtuel maskine

Nu hvor du har alle elementerne på plads, er det tid til at oprette den virtuelle maskine.

1. Kør denne kommando til at angive navn på administratorkonto og adgangskoden til den virtuelle maskine.

        $cred = Get-Credential -Message "Type the name and password of the local administrator account."
        
    Adgangskoden skal være på 12-123 tegn og have mindst én lille bogstav, ét stort bogstav, et tal og ét specialtegn. 
        
2. Oprette konfigurationsobjektet til den virtuelle maskine. Denne kommando opretter et konfigurationsobjekt med navnet **myVmConfig** , der definerer navnet på VM og størrelsen af VM.

    ```powershell
    $myVm = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2"
    ```
     
    Du kan finde en liste over tilgængelige størrelser for en virtuel maskine i [størrelser for virtuelle maskiner i Azure](virtual-machines-windows-sizes.md) .
    
3. Konfigurere operativsystemindstillinger for VM. Denne kommando indstiller computernavn, operativsystemtype og legitimationsoplysninger for VM.

    ```powershell
    $myVM = Set-AzureRmVMOperatingSystem -VM $myVM -Windows -ComputerName "myVM" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    ```
    
4. Definere billedet for at bruge til at klargøre VM. Denne kommando definerer Windows Server billedet skal bruges til VM. 

    ```powershell
    $myVM = Set-AzureRmVMSourceImage -VM $myVM -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
    ```
        
    Se [Naviger og vælge Windows virtuelt billeder i Azure med PowerShell eller CLI](virtual-machines-windows-cli-ps-findimage.md)kan finde flere oplysninger om at vælge billeder der skal bruges.
        
5. Føje grænsefladen for det netværk, du har oprettet til konfigurationen.

    ```powershell
    $myVM = Add-AzureRmVMNetworkInterface -VM $myVM -Id $myNIC.Id
    ```
        
6. Definer navn og placering på harddisken VM. Virtuel harddisk filen er gemt i en beholder. Denne kommando opretter disken i en objektbeholder med navnet **vhds/WindowsVMosDisk.vhd** i kontoen lagerplads, du har oprettet.

    ```powershell
    $blobPath = "vhds/myOsDisk1.vhd"
    $osDiskUri = $myStorageAccount.PrimaryEndpoints.Blob.ToString() + $blobPath
    ```
        
7. Tilføj operativsystem diskoplysninger til VM konfigurationen. Erstat værdien af **$diskName** med et navn til operativsystem disken. Oprette variablen, og Tilføj diskoplysninger, af konfigurationen.
    
    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $myVM -Name "myOsDisk1" -VhdUri $osDiskUri -CreateOption fromImage
    ```
        
8. Til sidst skal oprette den virtuelle maskine.

    ```
    New-AzureRmVM -ResourceGroupName $myResourceGroup -Location $location -VM $myVM
    ```
                                  
## <a name="next-steps"></a>Næste trin

- Hvis der var problemer med installationen, der ville være et næste trin at kigge på [fejlfinding ressource gruppe-installationer med Azure-portalen](../resource-manager-troubleshoot-deployments-portal.md)
- Få mere at vide, hvordan du administrerer den virtuelle maskine, du har oprettet ved at gennemgå [Administrer virtuelle maskiner ved hjælp af Azure ressourcestyring og PowerShell](virtual-machines-windows-ps-manage.md).
- Udnytte ved hjælp af en skabelon til at oprette en virtuel maskine ved hjælp af oplysningerne i [oprette en Windows virtuel maskine med en skabelon for ressourcestyring](virtual-machines-windows-ps-template.md)
