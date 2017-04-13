<properties
    pageTitle="Gøre D: drevet af et VM en datadisk | Microsoft Azure"
    description="Beskriver, hvordan du kan ændre drevbogstaver for en Windows-VM, så du kan bruge D: drev som et datadrev."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Bruge D: drev som et datadrev på en Windows-VM 

Hvis dit program skal bruge drev D til at gemme data, skal du følge disse instruktioner for at bruge et andet bogstav til den midlertidige disk. Brug aldrig midlertidige disken til at gemme data, du vil beholde.

Hvis du tilpasser størrelsen eller **Stop (Deallocate)** en virtuel maskine, kan det udløse placeringen af den virtuelle maskine til en ny hypervisor. En planlagt eller ikke-planlagt vedligeholdelse kan også udløse denne placering. I dette scenarie skal tildeles den midlertidige disk til det første drevbogstav i tilgængelige. Hvis du har et program, der specifikt kræver D: drev, skal du følge disse trin for at midlertidigt flytte pagefile.sys, vedhæfte en ny datadisk og tildele den bogstavet D og derefter flytte pagefile.sys tilbage til det midlertidige drev. Når dette er færdigt, træder Azure ikke tilbage på D: Hvis VM flyttes til en anden hypervisor.

Du kan finde flere oplysninger om, hvordan Azure anvender den midlertidige disk, kan du se [om midlertidigt drev på Microsoft Azure virtuelle maskiner](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="attach-the-data-disk"></a>Tilslut disken data

Først skal du vedhæfte disken data til den virtuelle maskine. 

- Hvis du vil bruge på portalen, se, [hvordan du vedhæfte en datadisk i portalen Azure](virtual-machines-windows-attach-disk-portal.md)
- Hvis du vil bruge portalen klassisk, se, [hvordan du vedhæfte en datadisk til en Windows virtuel maskine](virtual-machines-windows-classic-attach-disk.md). 


## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Flytte midlertidigt pagefile.sys til C-drevet

1. Oprette forbindelse til den virtuelle maskine. 

2. Højreklik på menuen **Start** , og vælg **System**.

3. Vælg **Avancerede systemindstillinger**i den venstre menu.

4. Vælg **Indstillinger**i sektionen **ydeevne** .

5. Vælg fanen **Avanceret** .

5. Vælg **Skift**i afsnittet **virtuel hukommelse** .

6. Vælg **C** -drevet, og klik derefter på **System administrerede størrelse** , og klik derefter på **Angiv**.

7. Vælg drevet, **D** , og klik derefter på **ingen sideopdeling filer** , og klik derefter på **angive**.

8. Klik på Anvend. Du modtager en advarsel om, at computeren skal genstartes for at ændringerne kan træde i kraft.

9. Genstart den virtuelle maskine.




## <a name="change-the-drive-letters"></a>Ændre drevbogstaver 

1. Når VM genstarter, skal du logge på VM.

2. Klik på menuen **Start** , og skriv **diskmgmt.msc** , og tryk på Enter. Disk Management starter.

3. Højreklik på **D**, midlertidige drevet, og vælg **Skift drevbogstavet og stier**.

4. Vælg drev **G** under et bestemt bogstav, og klik derefter på **OK**. 

5. Højreklik på disken data, og vælg **Skift Drive bogstavet og stier**.

6. Vælg drev **D** under et bestemt bogstav, og klik derefter på **OK**. 

7. Højreklik på **G**, midlertidige drevet, og vælg **Skift drevbogstavet og stier**.

8. Vælg drev **E** under et bestemt bogstav, og klik derefter på **OK**. 

> [AZURE.NOTE] Hvis din VM har andre diske eller drev, skal du bruge den samme metode til at tildele drevbogstaver i de andre diske og drev. Vil du konfigurationen af den disk skal være:  
>- C: OS diskplads  
>- D: data Disk  
>- E midlertidige disk



## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Flytte pagefile.sys tilbage til det midlertidige drev 

1. Højreklik på menuen **Start** , og vælg **System**

2. Vælg **Avancerede systemindstillinger**i den venstre menu.

3. Vælg **Indstillinger**i sektionen **ydeevne** .

4. Vælg fanen **Avanceret** .

5. Vælg **Skift**i afsnittet **virtuel hukommelse** .

6. Vælg OS drev **C** , og klik på **ingen sideopdeling filer** , og klik derefter på **Angiv**.

7. Vælg midlertidige drev **E** , og klik derefter på **System administrerede størrelse** , og klik derefter på **Angiv**.

8. Klik på **Anvend**. Du modtager en advarsel om, at computeren skal genstartes for at ændringerne kan træde i kraft.

9. Genstart den virtuelle maskine.




## <a name="next-steps"></a>Næste trin
- Du kan øge den tilgængelige lagerplads til din computer og virtuelle ved at [knytte en yderligere datadisk](virtual-machines-windows-attach-disk-portal.md).



