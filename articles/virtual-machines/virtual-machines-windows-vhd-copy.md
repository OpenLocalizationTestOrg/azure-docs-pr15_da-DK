<properties
    pageTitle="Oprette en kopi af en særlig VM i Azure | Microsoft Azure"
    description="Lær at oprette en kopi af en særlig Windows VM kører i Azure, i implementeringsmodel ressourcestyring."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="cynthn"/>
    
    
    
# <a name="create-a-copy-of-a-specialized-windows-vm-running-in-azure"></a>Oprette en kopi af en særlig Windows VM kører i Azure 

I denne artikel viser, hvordan du bruger værktøjet AZCopy til at oprette en kopi af den virtuelle harddisk fra en særlig Windows VM, der kører på Azure. Du kan derefter bruge kopien af den virtuelle harddisk til at oprette en ny VM. 

- Hvis vil kopiere en generalized VM, hvordan [du opretter et VM billede fra en eksisterende generalized Azure VM](virtual-machines-windows-capture-image.md).

- Hvis du vil overføre en virtuel harddisk fra en lokal VM, som oprettet ved hjælp af Hyper-V, se [overføre en Windows-Virtuelle fra en lokal VM til Azure](virtual-machines-windows-upload-image.md).


## <a name="before-you-begin"></a>Inden du går i gang

Sørg for, at du:

- Indeholde oplysninger om **kilde- og destinationstabellerne lagerplads konti**. For kilden VM skal du lagerplads konto og beholder navne. Normalt kan objektbeholder navn **virtuelle harddiske**. Du skal også have en destination lagerplads konto. Hvis du ikke allerede har en, kan du oprette én ved hjælp af enten portalen (**Flere tjenester** > lagerplads konti > Tilføj) eller ved hjælp af [Ny AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) cmdlet. 

- Har Azure [PowerShell 1.0](../powershell-install-configure.md) (eller nyere) installeret.

- Har downloadet og installeret [AzCopy værktøj](../storage/storage-use-azcopy.md). 


## <a name="deallocate-the-vm"></a>Dealloker VM

Dealloker VM, som frigør den virtuelle harddisk skal kopieres. 

- **Portalen**: Klik på **virtuelle maskiner** > **myVM** > Stop
- **PowerShell**: `Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM` deallocates VM med navnet **myVM** i ressource gruppe **myResourceGroup**.

**Status** for VM i portalen Azure ændres fra **showet afbrydes** til **showet afbrydes (deallokeres)**.


## <a name="get-the-storage-account-urls"></a>Hente URL-adresser med lagerplads konto

Du har brug for URL-adresserne på de kilde- og destinationstabellerne lagerplads konti. Ud for URL-adresser: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Hvis du allerede kender navnet på lagerplads konto og objektbeholder, kan du bare erstatte oplysninger mellem de kantede parenteser til at oprette din URL-adresse. 

Du kan bruge Azure portal eller Azure Powershell til at hente URL-adressen:

- **Portalen**: Klik på **flere tjenester** > **lagerplads konti**  >  <storage account> **BLOB** og Virtuelle kildefilen sandsynligvis i objektbeholderen **virtuelle harddiske** . Klik på **Egenskaber** for objektbeholderen, og Kopiér den tekst, der er mærket **URL-adresse**. Du skal have URL-adresserne på både kilde- og beholdere. 

- **PowerShell**: `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` bliver oplysningerne til VM med navnet **myVM** i ressource gruppe **myResourceGroup**. Se afsnittet **lagerplads profil** for den **Virtuelle Uri**i resultaterne. Den første del af Uri er URL-adressen til objektbeholderen, og den sidste del er OS Virtuelle navnet på VM.

## <a name="get-the-storage-access-keys"></a>Få adgangstaster lagerplads

Finde hurtigtasterne til kilde- og lagerplads konti. Se [om Azure lagerplads konti](../storage/storage-create-storage-account.md)kan finde flere oplysninger om access-taster.

- **Portalen**: Klik på **flere tjenester** > **lagerplads konti**  >  <storage account> **Alle indstillinger** > **Access-taster**. Kopiere den nøgle, der er mærket **nøgle1**.
- **PowerShell**: `Get-AzureRmStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup` får tasten lagerplads for lagerplads konto **mystorageaccount** i ressource gruppe **myResourceGroup**. Kopiere den nøgle, der er mærket **nøgle1**.


## <a name="copy-the-vhd"></a>Kopiere den virtuelle harddisk 

Du kan kopiere filer mellem lagerplads firmaer ved hjælp af AzCopy. For objektbeholderen destination, hvis den angivne beholder ikke findes, oprettes den for dig. 

Åbn en kommandoprompt på din lokale computer for at bruge AzCopy, og Naviger til den mappe, hvor AzCopy er installeret. Det bliver ligner *C:\Program Files (x86) \Microsoft SDKs\Azure\AzCopy*. 

Hvis du vil kopiere alle filer i en objektbeholder, skal bruge du parameteren **/S** . Dette kan bruges til at kopiere den virtuelle harddisk OS og alle data diskene, hvis de er i samme beholder. I dette eksempel vises, hvordan du kopierer alle filerne i beholderen **mysourcecontainer** i lagerplads konto **mysourcestorageaccount** til objektbeholder **mydestinationcontainer** i kontoen **mydestinationstorageaccount** lagerplads. Erstatte navnene på de lagerplads konti og beholdere med dine egne. Erstatte `<sourceStorageAccountKey1>` og `<destinationStorageAccountKey1>` med dine egne taster.

```
    AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
        /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
        /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Hvis du kun vil kopiere en bestemt virtuel harddisk i en objektbeholder med flere filer, kan du også angive navnet på den fil, ved hjælp af parameteren /Pattern. I dette eksempel skal kun den fil, der hedder **myFileName.vhd** kopieres.

```
    AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
        /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
        /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
        /Pattern:myFileName.vhd
```


Når overførslen er færdig, får du vist en meddelelse, der ser nogenlunde således:

```
  Finished 2 of total 2 file(s).
  [2016/10/07 17:37:41] Transfer summary:
  -----------------
  Total files transferred: 2
  Transfer successfully:   2
  Transfer skipped:        0
  Transfer failed:         0
  Elapsed time:            00.00:13:07
```

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

- Når du bruger AZCopy, hvis du får vist fejlen "Server kunne ikke godkende anmodningen. Sørg for, at værdien af Authorization-header dannes korrekt herunder signaturen." og du bruger nøgle 2 eller sekundær lagerplads-tast, skal du prøve at bruge tasten primær eller 1st lagerplads.


## <a name="next-steps"></a>Næste trin

- Du kan oprette en ny VM ved at [vedhæfte kopien af den virtuelle harddisk til en VM som en OS disk](virtual-machines-windows-create-vm-specialized.md).












