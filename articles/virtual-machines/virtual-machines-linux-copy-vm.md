<properties
    pageTitle="Oprette en kopi af din Azure Linux VM | Microsoft Azure"
    description="Lær at oprette en kopi af din Azure Linux virtuelle maskine i implementeringsmodel ressourcestyring"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="cynthn"/>

# <a name="create-a-copy-of-a-linux-virtual-machine-running-on-azure"></a>Oprette en kopi af en Linux virtuel maskine, der kører på Azure


I denne artikel beskrives, hvordan til at oprette en kopi af din Azure virtuelt (VM), der kører Linux ved hjælp af Ressourcestyring implementeringsmodel. Først du kopiere over operativsystem og datadisce til en ny objektbeholder til og derefter angive netværksressourcerne, der og oprette nye virtuelle maskine.

Du kan også [overføre og oprette en VM fra brugerdefinerede disk billede](virtual-machines-linux-upload-vhd.md).


## <a name="before-you-begin"></a>Inden du går i gang

Sikre, at du opfylder følgende forudsætninger, før du starter trinnene:

- Du har [Azure CLI] (… / xplat-cli-install.md) har downloadet og installeret på computeren. 

- Du kan også bruge oplysninger om din eksisterende Azure Linux VM:

| Oplysninger om datakilden VM | Hvor du kan få den |
|------------|-----------------|
| VM navn | `azure vm list` |
| Ressourcegruppe navn | `azure vm list` |
| Placering | `azure vm list` |
| Kontonavn til lager | `azure storage account list -g <resourceGroup>` |
| Navnet på objektbeholderen | `azure storage container list -a <sourcestorageaccountname>` |
| Navnet kildefilen VM VHD | `azure storage blob list --container <containerName>` |



- Du skal foretage nogle valg for din nye VM:   <br> -Objektbeholder navn   <br> -VM navn   <br> -VM størrelse   <br> -vNet navn   <br> -Undernet navn   <br> / IP-navn   <br> NIC - navn
    

## <a name="login-and-set-your-subscription"></a>Logon og angive dit abonnement

1. Log på CLI.
        
        azure login

2. Kontrollér, at du er i ressourcestyring tilstand.
    
        azure config mode arm

3. Angiv det korrekte abonnement. Du kan bruge 'azure-konto liste' til at se alle dine abonnementer.

        azure account set <SubscriptionId>



## <a name="stop-the-vm"></a>Stoppe VM 

Stoppe og deallokere kilden VM. Du kan bruge 'azure vm liste' til at få en liste over alle FOS i dit abonnement og deres ressource gruppenavne.
    
        azure vm stop <ResourceGroup> <VmName>
        azure vm deallocate <ResourceGroup> <VmName>




## <a name="copy-the-vhd"></a>Kopiere den virtuelle harddisk


Du kan kopiere den virtuelle harddisk fra den kilde lagerplads til den destination ved hjælp af den `azure storage blob copy start`. I dette eksempel skal vi at kopiere den virtuelle harddisk til den samme konto lagerplads, men en anden objektbeholder.

Hvis du vil kopiere den virtuelle harddisk til en anden beholder i den samme konto lagerplads, skal du skrive:

        azure storage blob copy start https://<sourceStorageAccountName>.blob.core.windows.net:8080/<sourceContainerName>/<SourceVHDFileName.vhd> <newcontainerName>
        

## <a name="set-up-the-virtual-network-for-your-new-vm"></a>Konfigurere det virtuelle netværk til din nye VM

Konfigurere et virtuelt netværk og NIC til din nye VM. 

    azure network vnet create <ResourceGroupName> <VnetName> -l <Location>

    azure network vnet subnet create -a <address.prefix.in.CIDR/format> <ResourceGroupName> <VnetName> <SubnetName>

    azure network public-ip create <ResourceGroupName> <IpName> -l <yourLocation>

    azure network nic create <ResourceGroupName> <NicName> -k <SubnetName> -m <VnetName> -p <IpName> -l <Location>


## <a name="create-the-new-vm"></a>Oprette den nye VM 

Nu kan du oprette en VM fra dine overførte Virtuel disk [ved hjælp af en ressource manager skabelon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) eller via CLI ved at angive URI til kopierede disken ved at skrive:

```bash
azure vm create -n <newVMName> -l "<location>" -g <resourceGroup> -f <newNicName> -z "<vmSize>" -d https://<storageAccountName>.blob.core.windows.net/<containerName/<fileName.vhd> -y Linux
```



## <a name="next-steps"></a>Næste trin

For at lære at bruge Azure CLI til at administrere din nye virtuelle maskine, skal du se [Azure CLI-kommandoer til Azure ressourcestyring](azure-cli-arm-commands.md).
