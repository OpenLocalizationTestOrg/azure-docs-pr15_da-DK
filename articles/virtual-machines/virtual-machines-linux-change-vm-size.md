<properties
   pageTitle="Hvordan du kan ændre størrelsen på en Linux VM | Microsoft Azure"
   description="Sådan skalere op eller indskrænke en Linux virtuel maskine, ved at ændre VM størrelse."
   services="virtual-machines-linux"
   documentationCenter="na"
   authors="mikewasson"
   manager="timlt"
   editor=""
   tags=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="mikewasson"/>


# <a name="how-to-resize-a-linux-vm"></a>Hvordan du kan ændre størrelsen på en Linux VM

## <a name="overview"></a>Oversigt 

Når du klargør et virtuelt (VM), du kan tilpasse VM op eller ned ved at ændre [VM størrelse][vm-sizes]. I nogle tilfælde skal du deallokere VM først. Dette kan ske, hvis den nye størrelse ikke er tilgængelig under hardware-klynge, der er vært VM.

Denne artikel beskrives, hvordan du kan ændre størrelsen på en Linux VM ved hjælp af [Azure CLI][azure-cli].

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisk implementeringsmodel.


## <a name="resize-a-linux-vm"></a>Ændre størrelsen på en Linux VM 

Hvis du vil ændre størrelsen på en VM skal du udføre følgende trin.

1. Køre følgende kommando for CLI. Denne kommando viser VM størrelsen, der er tilgængelige på den hardware klynge, hvor VM er hostet.

    ```
    azure vm sizes -g <resource-group> --vm-name <vm-name>
    ```

2. Hvis den ønskede størrelse er angivet, skal du køre følgende kommando for at ændre størrelsen på VM.

    ```
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    ```

    VM genstartes under denne proces. Efter genstarten kan din eksisterende OS og datadisce ændres. Noget på midlertidige disken, går tabt.

    Brug den `--enable-boot-diagnostics` indstilling aktiverer [Start diagnosticering][boot-diagnostics], til at logge eventuelle fejl, der er relateret til start.

3. Hvis den ønskede størrelse ikke vises, skal du køre følgende kommandoer til deallokere VM, tilpasse størrelsen på den, og genstart derefter VM.

    ```
    azure vm deallocate -g <resource-group> <vm-name>
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    azure vm start -g <resource-group> <vm-name>
    ```

   > [AZURE.WARNING] En hvilken som helst dynamiske IP-adresser, der er tildelt til VM versioner har deallokeret VM også. Diskene OS og data påvirkes ikke.
   
## <a name="next-steps"></a>Næste trin

Køre flere forekomster af VM yderligere skalerbarhed og skalere ud. Du kan finde flere oplysninger, se [automatisk skalere Linux-computere i et virtuelt skala sæt][scale-set]. 

<!-- links -->
   
[azure-cli]: ../xplat-cli-install.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]: virtual-machines-linux-sizes.md