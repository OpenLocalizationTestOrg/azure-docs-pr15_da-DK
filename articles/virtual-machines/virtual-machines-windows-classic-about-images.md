<properties
    pageTitle="Om billeder til Windows virtuelle maskiner | Microsoft Azure"
    description="Få mere at vide om, hvordan billeder bruges med Windows virtuelle maskiner i Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/21/2016"
    ms.author="cynthn"/>

# <a name="about-images-for-windows-virtual-machines"></a>Om billeder til Windows virtuelle maskiner

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-about-images](../../includes/virtual-machines-common-classic-about-images.md)]



## <a name="working-with-images"></a>Arbejde med billeder

Du kan bruge Azure PowerShell-modulet til at administrere de tilgængelige billeder til dit Azure-abonnement. Du kan også bruge Azure klassisk portalen for nogle opgaver til billedet, men kommandolinjen får du flere muligheder.


-   **Få alle billeder**:`Get-AzureVMImage`returnerer en liste over alle de billeder, der er tilgængelige i dit aktuelle abonnement: billederne, såvel som dem, der leveres af Azure eller partnere. Det betyder, at du får måske en stor liste. De næste eksempler viser, hvordan du får en kortere liste.
-   **Få billede familier**:`Get-AzureVMImage | select ImageFamily` får liste over billedet findes ved at vise strenge **ImageFamily** egenskab.
-   **Få alle billeder i en bestemt familie**:`Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
-   **Finde VM billeder**: `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` dette virker ved at filtrere egenskaben DataDiskConfiguration, som gælder kun for VM billeder. I dette eksempel filtrerer også output kun etiketten og billedet til navnet på.
-   **Gemme et generalized billede**:`Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
-   **Gemme en særlig billede**:`Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`
>[Azure.Tip] Parameteren OSState er påkrævet, hvis du vil oprette et VM billede, som omfatter datadisce samt operativsystem disken. Hvis du ikke bruge parameteren, opretter cmdlet et OS billede. Værdien af parameteren angiver, om billedet er generalized eller specialiserede, baseret på om operativsystem disken er blevet forberedt til senere brug.
-   **Slette et billede**:`Remove-AzureVMImage –ImageName "MyOldVmImage"`


## <a name="next-steps"></a>Næste trin

Du kan også [oprette en Windows-computer ved hjælp af portalen klassisk](virtual-machines-windows-classic-tutorial.md)

