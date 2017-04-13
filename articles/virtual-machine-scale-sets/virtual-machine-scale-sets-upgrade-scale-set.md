<properties
    pageTitle="Installere en app på virtuelt skala sæt | Microsoft Azure"
    description="Installere en app på virtuelt skala sæt"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="guybo"/>


# <a name="upgrade-a-virtual-machine-scale-set"></a>Opgradere et virtuelt skala sæt

I denne artikel beskrives, hvordan du kan rulle ud af en OS opdatering til en Azure virtuelt skala indstilles uden en hvilken som helst nedetid. I denne kontekst omfatter en opdatering til OS ændre version eller SKU af Operativsystemet eller ændre URI på et brugerdefineret billede. Ved at opdatere uden nedetid betyder opdaterer virtuelle maskiner én ad gangen eller i grupper (som en fejl domæne ad gangen) i stedet for på én gang. Gør det, kan holde kører en hvilken som helst virtuelle maskiner, der ikke opgraderes.

For at undgå tvetydighed, Lad os skelne tre typer OS opdatering kan du vil udføre:

- Ændre version eller SKU på et platform billede. Eksempelvis ændring Ubuntu 14.04.2-LTS version fra 14.04.201506100 til 14.04.201507060, eller ændre Ubuntu 15.10/sidste SKU til 16.04.0-LTS/latest. Dette scenarie dækkes i denne artikel.

- Ændre den URI, der peger på en ny version af et brugerdefineret billede indbygget (**Egenskaber** > **virtualMachineProfile** > **storageProfile** > **osDisk** > **billede** > **uri**). Dette scenarie dækkes i denne artikel.

- Retter OS fra i et virtuelt (eksempler på dette omfatter installere en sikkerhed programrettelse og køre Windows Update). Dette scenario er understøttet, men ikke er omfattet i denne artikel.

De første to indstillinger er understøttede krav, der er dækket af denne artikel. Du skal oprette en ny skala, der er angivet til at udføre den tredje mulighed.

Virtuelt skala sæt, der er implementeret som en del af en [Azure Service strukturen](https://azure.microsoft.com/services/service-fabric/) klynge er ikke omfattet her.

Den grundlæggende rækkefølge til at ændre OS version/SKU på et platform billede eller URI på et brugerdefineret billede ser ud som følger:

1. Få virtuelt skala sæt modellen.

2. Ændre den version, SKU eller URI værdi i modellen.

3. Opdater modellen.

4. Gør et *manualUpgrade* opkald på virtuelle maskiner i sættet skala. Dette trin er kun relevant, hvis *upgradePolicy* er indstillet til **Manuel** i din skala sæt. Hvis den er indstillet til **automatisk**, opgraderes virtuelle maskiner på én gang, og som derfor forårsager nedetid.


Med denne baggrundsoplysninger huske, Lad os se, hvordan du kan opdatere versionen af et målestoksforhold, der er angivet i PowerShell og ved hjælp af REST-API. Disse eksempler omfatte tilfældet med et billede af platform, men i denne artikel indeholder nok oplysninger til at tilpasse denne proces til et brugerdefineret billede.

## <a name="powershell"></a>PowerShell##

I dette eksempel opdaterer en Windows virtuelt skala, der er angivet til den nye version 4.0.20160229. Når du opdaterer modellen, gør den en opdatering et virtuelt forekomst ad gangen.

```powershell
$rgname = "myrg"
$vmssname = "myvmss"
$newversion = "4.0.20160229"
$instanceid = "1"

# get the VMSS model
$vmss = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname

# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.version = $newversion

# update the virtual machine scale set model
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $vmss

# now start updating instances
Update-AzureRmVmssInstance -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $instanceId
```

Hvis du opdaterer URI-adressen for et brugerdefineret billede i stedet for at ændre et billede-platformversion, kan du erstatte linjen "Angiv den nye version" med noget i retning af følgende:

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.osDisk.image.uri= $newURI
```


## <a name="the-rest-api"></a>REST-API

Her er nogle af Python eksempler, der bruger Azure REST-API til at implementere en OS Versionsopdatering. Begge bruge lette [azurerm](https://pypi.python.org/pypi/azurerm) bibliotek med Azure REST-API til slikpapir funktioner til at gøre få på skala sæt modellen, efterfulgt af et læg med en opdateret model. De ser også på virtuelt forekomster visninger til at identificere de virtuelle maskiner ved opdatering domæne.

### <a name="vmssupgrade"></a>Vmssupgrade

 [Vmssupgrade](https://github.com/gbowerman/vmsstools) er et Python script, som bruges til at udrulle en OS opgradering til en igangværende virtuelt skala angive en opdatering domæne ad gangen.

![Vmssupgrade script til valg af virtuelle maskiner eller et domæne, der opdatering](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssupgrade-screenshot.png)

Dette script kan du vælge bestemte virtuelle maskiner til at opdatere eller angive en opdatering domæne. Ændre et billede-platformversion eller ændring af et brugerdefineret billede URI understøtter.

### <a name="vmsseditor"></a>Vmsseditor

[Vmsseditor](https://github.com/gbowerman/vmssdashboard) er en generel editor for virtuelt skala sæt, der vises virtuel maskine status som en heatmap, hvor én række repræsenterer ét opdatering domæne. Blandt andet kan du opdatere modellen for et skala med en ny version, SKU eller brugerdefineret billede URI og derefter vælge et domæner til at opgradere. Når du gør dette, opgraderes alle virtuelle maskiner i det pågældende domæne opdatering til den nye model. Alternativt kan du gøre en rullende opgradering, der er baseret på batchstørrelsen på dine valg.  

Følgende skærmbillede viser en model af et målestoksforhold, der er angivet for Ubuntu 14.04-2LTS version 14.04.201507060. Mange flere indstillinger er blevet føjet til dette værktøj, efter dette skærmbillede blev gjort tilgængelig.

![Vmsseditor model af et målestoksforhold, der er angivet for Ubuntu 14.04-2LTS](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor1.png)

Når du klikker på **Opgrader** og derefter **Få mere at vide**, start virtuelle maskiner i UD 0 for at opdatere.

![Vmsseditor viser opdatering i gang](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor2.png)
