<properties
   pageTitle="Sådan mærker du et VM | Microsoft Azure"
   description="Få mere at vide om mærkning af en Windows virtuel maskine, der er oprettet i Azure ved hjælp af implementeringsmodel ressourcestyring"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="07/05/2016"
   ms.author="memccror"/>

# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Sådan mærker du et Windows virtuelt i Azure


I denne artikel beskrives de forskellige måder at mærke en Windows virtuel maskine i Azure gennem implementeringsmodel ressourcestyring. Mærker er brugerdefinerede nøgle/værdi-par, som kan placeres direkte i en ressource eller en ressourcegruppe. Azure understøtter i øjeblikket op til 15 mærker hver ressource og ressourcegruppe. Mærker kan være placeret på en ressource på tidspunktet for oprettelse eller føjet til en eksisterende ressource. Vær opmærksom på, at mærker understøttes for de ressourcer, der er oprettet, via ressourcestyring implementeringsmodel kun. Hvis du vil mærke en Linux virtuel maskine, skal du se [Sådan mærke en Linux virtuel maskine i Azure](virtual-machines-linux-tag.md).

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Mærkning med PowerShell

Hvis du vil oprette, tilføje og slette mærker via PowerShell, skal du først konfigurere dit [PowerShell-miljø med Azure ressourcestyring][]. Du kan placere mærker på Beregn-, Netværks- og lagerplads ressourcer på oprettelse eller efter ressourcen, der er oprettet via PowerShell, når du har afsluttet konfigurationen. I denne artikel vil være koncentreret om vise/redigere mærker, som anbringes på virtuelle maskiner.

Gå først til en virtuel maskine gennem den `Get-AzureRmVM` cmdlet.

        PS C:\> Get-AzureRmVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Hvis din virtuelle maskine allerede indeholder mærker, vises der derefter alle koderne på din ressource:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Hvis du vil føje mærker via PowerShell, kan du bruge den `Set-AzureRmResource` kommandoen. Bemærk, når du opdaterer mærker via PowerShell, mærker opdateres som helhed. Så hvis du føjer en kode til en ressource, som allerede indeholder mærker, skal du medtage alle de mærker, du vil være placeret på ressourcen. Nedenfor er et eksempel på, hvordan du føjer flere mærker til en ressource ved hjælp af PowerShell Cmdlets.

Denne første cmdlet angiver alle de mærker, som anbringes på *MyTestVM* variabel *$tags* ved hjælp af den `Get-AzureRmResource` og `Tags` egenskab.

        PS C:\> $tags = (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

Den anden kommando viser koderne for den angivne variabel.

        PS C:\> $tags

        Name        Value
        ----                           -----
        Value       MyDepartment
        Name        Department
        Value       MyApp1
        Name        Application
        Value       MyName
        Name        Created By
        Value       Production
        Name        Environment

Kommandoen tredje føjer en ekstra mærke til variablen *$tags* . Bemærk brugen af den **+=** til at føje den nye nøgle/værdi-par til listen *$tags* .

        PS C:\> $tags += @{Name="Location";Value="MyLocation"}

Kommandoen fjerde indstiller alle de mærker, der er defineret i variablen *$tags* til den givne ressource. I dette tilfælde er det MyTestVM.

        PS C:\> Set-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

Kommandoen femte viser alle mærkerne for ressourcen. Som du kan se, er *placering* nu defineret som et mærke med *MyLocation* denne værdi.

        PS C:\> (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

        Name        Value
        ----                           -----
        Value       MyDepartment
        Name        Department
        Value       MyApp1
        Name        Application
        Value       MyName
        Name        Created By
        Value       Production
        Name        Environment
        Value       MyLocation
        Name        Location

Hvis du vil vide mere om mærkning via PowerShell, se [Azure ressource cmdletter][].

[AZURE.INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Næste trin

* Hvis du vil vide mere om mærkning af ressourcerne Azure, se [Azure ressourcestyring oversigt][] og [Bruge mærker til at organisere dine Azure ressourcer][].
* For at se, hvordan mærker kan hjælpe dig med at administrere din brug af Azure ressourcer skal du se [forstå din faktura Azure][] og [få indsigt i dine Microsoft Azure Ressourceforbrug][].

[PowerShell-miljø med Azure ressourcestyring]: ../powershell-azure-resource-manager.md
[Azure ressource cmdletter]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[Oversigt over Azure ressourcestyring]: ../azure-resource-manager/resource-group-overview.md
[Bruge mærker til at organisere dine Azure-ressourcer]: ../resource-group-using-tags.md
[Forstå din Azure faktura]: ../billing/billing-understand-your-bill.md
[Få indsigt i dine Microsoft Azure Ressourceforbrug]: ../billing-usage-rate-card-overview.md
