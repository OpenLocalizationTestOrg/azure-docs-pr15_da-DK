<properties
    pageTitle="Ofte stillede spørgsmål om Azure stak | Microsoft Azure"
    description="Azure stak stillede ofte spørgsmål."
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="helaw"/>

# <a name="frequently-asked-questions-for-azure-stack"></a>Ofte stillede spørgsmål om Azure stak

## <a name="deployment"></a>Installation

### <a name="do-i-need-to-format-my-data-disks-before-starting-or-restarting-an-installation"></a>Skal jeg formatere min datadisce, før du starter eller ved at genstarte en installation?

Diske skal være i raw-format. Hvis du geninstallerer operativsystemet, skal du muligvis kontrollere Hvis den gamle lagerpulje er stadig er til stede og slette at benytte følgende fremgangsmåde:

1. Åbn Server Manager.
2. Vælg lagerplads grupper.
3. Se, om en lagerpulje står.
4. Højreklik på **lagerpulje** , hvis angivet og aktivere læse / skrive.
5. Højreklik på **virtuel harddisk** (nederste venstre hjørne) og vælge Slet.
6. Højreklik på **Lagerpulje** , og klik derefter på Slet.
7. Start Azure stak script igen, og Bekræft, at disken bekræftelsen passerer.

Du kan også kan følgende script bruges:

```PowerShell
$pools = Get-StoragePool -IsPrimordial $False -ErrorVariable Err -ErrorAction SilentlyContinue
if ($pools -ne $null) {
  $pools| Set-StoragePool -IsReadOnly $False -ErrorVariable Err -ErrorAction SilentlyContinue
  $pools = Get-StoragePool -IsPrimordial $False -ErrorVariable Err -ErrorAction SilentlyContinue
  $pools | Get-VirtualDisk | Remove-VirtualDisk -Confirm:$False
  $pools | Remove-StoragePool -Confirm:$False
}
```

### <a name="can-i-use-all-ssd-disks-for-the-storage-pool-in-the-poc-installation"></a>Kan jeg bruge alle SSD diske til lagerpulje i Konceptet installationen?

Denne konfiguration understøttes ikke i denne udgave.  Du kan finde yderligere oplysninger finder [krav vejledning](azure-stack-deploy.md) til flere oplysninger.

### <a name="can-i-use-nvme-data-disks-for-the-microsoft-azure-stack-poc"></a>Kan jeg bruge NVMe datadisce til Microsoft Azure stak Konceptet?

Mens lagerplads mellemrum direkte understøtter NVMe diske, understøtter Azure stak kun et undersæt af de mulige drevtyper og mulige kombinationer for lagerplads mellemrum direkte. 

### <a name="how-can-i-reinstall-azure-stack"></a>Hvordan kan jeg geninstallere Azure stak?
Du kan følge trinnene i [geninstallation vejledning](azure-stack-redeploy.md).  

## <a name="tenant"></a>Lejer

### <a name="can-i-deploy-my-own-images-as-a-tenant"></a>Kan jeg installere mine egne billeder som en lejer?

Ja, ligesom i Azure, en lejer kan overføre billeder i Azure stak, ud over at bruge billeder fra tjenesteadministratoren. Have et overblik, kan du se den [tilføjelse af en VM billede](azure-stack-add-vm-image.md). 

## <a name="testing"></a>Test

### <a name="can-i-use-nested-virtualization-to-test-the-microsoft-azure-stack-poc"></a>Kan jeg bruge indlejrede Virtualization teste Microsoft Azure stak Konceptet?

Indlejrede virtualization er ikke understøttet eller testet med Azure stak Technical Preview 2.

## <a name="virtual-machines"></a>Virtuelle maskiner

### <a name="does-azure-stack-support-dynamic-disks-for-virtual-machines"></a>Understøtter Azure stak dynamiske diske til virtuelle computere?

Azure stak understøtter ikke dynamiske diske.

## <a name="next-steps"></a>Næste trin

[Fejlfinding i forbindelse med](azure-stack-troubleshooting.md)
