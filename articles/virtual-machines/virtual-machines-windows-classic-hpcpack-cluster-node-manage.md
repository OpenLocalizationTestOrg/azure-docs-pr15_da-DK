<properties
 pageTitle="Administrere HPC Pack klynge Beregn noder | Microsoft Azure"
 description="Få mere at vide om PowerShell-script-værktøjer til at tilføje, fjerne, starte og stoppe HPC Pack klynge Beregn noder i Azure"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="07/22/2016"
 ms.author="danlep"/>

# <a name="manage-the-number-and-availability-of-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Administrere tal og tilgængeligheden af Beregn noder i en HPC Pack klynge i Azure

Hvis du har oprettet en HPC Pack klynge i Azure FOS, kan du kan nemt tilføje, fjerne, starte (klargøre) eller stoppe (deprovision) et antal Beregn node FOS i klyngen måder. For at udføre disse opgaver skal du køre Azure PowerShell-scripts, som er installeret på noden hoved VM. Disse scripts hjælpe dig med at styre det antal og tilgængeligheden af ressourcerne HPC Pack klynge, så du kan styre omkostninger.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


## <a name="prerequisites"></a>Forudsætninger

* **HPC Pack klynge i Azure FOS** - oprette en HPC Pack klynge i modellen Klassisk installation ved hjælp af mindst HPC Pack 2012 R2 Update 1. For eksempel kan du automatisere installationen ved hjælp af det aktuelle HPC Pack VM billede i Azure Marketplace og et Azure PowerShell-script. Se [oprette en HPC-klynge med HPC Pack IaaS installation script](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)oplysninger og forudsætninger.

    Efter installation, finder noden management scripts i mappen % Forarbejdningshastigheden\_HOME % placering mappe på noden hoved. Du skal køre hver af scriptene som administrator.

* **Azure publicere indstillinger fil eller management certifikat** - skal du gøre et af følgende fremgangsmåder på noden hoved:

    * **Importér Azure publicere indstillingsfil**. For at gøre dette, skal du køre følgende Azure PowerShell-cmdletter på noden hoved:

    ```
    Get-AzurePublishSettingsFile

    Import-AzurePublishSettingsFile –PublishSettingsFile <publish settings file>
    ```

    * **Konfigurer Azure management certifikatet på noden hoved**. Hvis du har .cer-filen, importere den i CurrentUser\My certifikat store, og derefter køre følgende Azure PowerShell-cmdlet til dit Azure-miljø (AzureCloud eller AzureChinaCloud):

    ```
    Set-AzureSubscription -SubscriptionName <Sub Name> -SubscriptionId <Sub ID> -Certificate (Get-Item Cert:\CurrentUser\My\<Cert Thrumbprint>) -Environment <AzureCloud | AzureChinaCloud>
    ```

## <a name="add-compute-node-vms"></a>Tilføje beregningsnode FOS

Tilføj beregne noder med scriptet **Tilføj HpcIaaSNode.ps1** .

### <a name="syntax"></a>Syntaksen for
```
Add-HPCIaaSNode.ps1 [-ServiceName] <String> [-ImageName] <String>
 [-Quantity] <Int32> [-InstanceSize] <String> [-DomainUserName] <String> [[-DomainUserPassword] <String>]
 [[-NodeNameSeries] <String>] [<CommonParameters>]

```
### <a name="parameters"></a>Parametre

* **ServiceName** - navnet på skyen service nye Beregn noden FOS, føjes til.

* **Procesnavn** - Azure VM billede navn, som kan fås via Azure klassisk administrationsportalen eller Azure PowerShell-cmdlet **Get-AzureVMImage**. Billedet skal opfylde følgende krav:

    1. En Windows-operativsystemet skal være installeret.

    2. HPC Pack skal være installeret i rollen Beregn node.

    3. Billedet skal være et privat billede i kategorien bruger ikke en offentlig Azure VM billede.

* **Antal** - antal beregningsnode FOS skal tilføjes.

* **InstanceSize** - størrelsen på noden Beregn FOS.

* **DomainUserName** - domænebrugernavn, som bruges til at deltage i de nye FOS til domænet.

* **DomainUserPassword** - adgangskode for domænebrugeren.

* **NodeNameSeries** (valgfrit) - navngive mønster for noderne Beregn. Formatet skal være &lt; *rod\_navn*&gt;&lt;*starte\_tal*&gt;%. For eksempel MyCN % 10%: en serie af Beregn node navnene begyndende fra MyCN11. Hvis du ikke er angivet, bruges scriptet noden konfigurerede navngive serier i HPC-klynge.

### <a name="example"></a>Eksempel

I følgende eksempel adderes 20 størrelse store beregningsnode FOS i den skyen service *hpcservice1*, baseret på VM billede *hpccnimage1*.

```
Add-HPCIaaSNode.ps1 –ServiceName hpcservice1 –ImageName hpccniamge1
–Quantity 20 –InstanceSize Large –DomainUserName <username>
-DomainUserPassword <password>
```


## <a name="remove-compute-node-vms"></a>Fjern beregne node FOS

Fjern beregne noder med **Fjern HpcIaaSNode.ps1** scriptet.

### <a name="syntax"></a>Syntaksen for

```
Remove-HPCIaaSNode.ps1 -Name <String[]> [-DeleteVHD] [-Force] [-WhatIf] [-Confirm] [<CommonParameters>]

Remove-HPCIaaSNode.ps1 -Node <Object> [-DeleteVHD] [-Force] [-Confirm] [<CommonParameters>]
```

### <a name="parameters"></a>Parametre

* **Navn** - navnene på klyngenoder at blive fjernet. Jokertegn understøttes. Angiv parameternavn er navn. Du kan ikke angive **navn** og **Node** parametre.

* **Node** - feltet HpcNode objekt for noderne, der kan fjernes, som kan fås via HPC PowerShell-cmdlet [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Angiv parameternavn er Node. Du kan ikke angive **navn** og **Node** parametre.

* **DeleteVHD** (valgfrit) - konfiguration til at slette de tilknyttede diske for VM'er, der fjernes.

* **Kraft** (valgfrit) - konfiguration tvinge HPC noder offline før du fjerner dem.

* **Bekræfte** (valgfrit) – Prompt om en bekræftelse, før du udfører kommandoen.

* **WhatIf** - som indstilling til at beskrive, hvad der ville ske, hvis du har kørt kommandoen uden faktisk udfører kommandoen.

### <a name="example"></a>Eksempel

I følgende eksempel Fremtvinger offline noder med navne, der begynder *HPCNode-CN -* og dem fjerner noderne og deres tilknyttede diske.

```
Remove-HPCIaaSNode.ps1 –Name HPCNodeCN-* –DeleteVHD -Force
```

## <a name="start-compute-node-vms"></a>Start beregningsnode FOS

Start beregne noder med **Start HpcIaaSNode.ps1** scriptet.

### <a name="syntax"></a>Syntaksen for

```
Start-HPCIaaSNode.ps1 -Name <String[]> [<CommonParameters>]

Start-HPCIaaSNode.ps1 -Node <Object> [<CommonParameters>]
```
### <a name="parameters"></a>Parametre

* **Navn** - navnene på klyngenoderne skal startes. Jokertegn understøttes. Angiv parameternavn er navn. Du kan ikke angive **navn** og **Node** parametre.

* **Node**- feltet HpcNode objekt for noderne, der startes, som kan fås via HPC PowerShell-cmdlet [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Angiv parameternavn er Node. Du kan ikke angive **navn** og **Node** parametre.

### <a name="example"></a>Eksempel

I følgende eksempel starter noder med navne, der begynder *HPCNode-CN -*.

```
Start-HPCIaaSNode.ps1 –Name HPCNodeCN-*
```

## <a name="stop-compute-node-vms"></a>Stoppe beregningsnode FOS

Stoppe med at beregne noder med **Stop HpcIaaSNode.ps1** scriptet.

### <a name="syntax"></a>Syntaksen for

```
Stop-HPCIaaSNode.ps1 -Name <String[]> [-Force] [<CommonParameters>]

Stop-HPCIaaSNode.ps1 -Node <Object> [-Force] [<CommonParameters>]
```

### <a name="parameters"></a>Parametre


* **Navn**- navnene på de klyngenoder skal stoppes. Jokertegn understøttes. Angiv parameternavn er navn. Du kan ikke angive **navn** og **Node** parametre.

* **Node** - feltet HpcNode objekt for knuderne skal stoppes, som kan fås via HPC PowerShell-cmdlet [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Angiv parameternavn er Node. Du kan ikke angive **navn** og **Node** parametre.

* **Kraft** (valgfrit) - konfiguration tvinge HPC noder offline før du stopper dem.

### <a name="example"></a>Eksempel

I følgende eksempel Fremtvinger offline noder med navne, der begynder *HPCNode-CN -* og stopper derefter noderne.

Stop-HPCIaaSNode.ps1 – navn HPCNodeCN-*-kraft

## <a name="next-steps"></a>Næste trin

* Hvis du vil have en måde at automatisk Forøg eller Formindsk klyngenoderne efter aktuelle arbejdsbelastningen af job og opgaver på klyngen, se [automatisk forøge og formindske HPC Pack klyngeressourcer i Azure ifølge klynge arbejdsbelastningen](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md).
