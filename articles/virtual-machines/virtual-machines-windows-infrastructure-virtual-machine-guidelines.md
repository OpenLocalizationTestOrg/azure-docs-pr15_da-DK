<properties
    pageTitle="Windows virtuelle maskiner retningslinjer | Microsoft Azure"
    description="Få mere at vide om de vigtigste design og implementering retningslinjer for installation af windows virtuelle maskiner til Azure"
    documentationCenter=""
    services="virtual-machines-windows"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="virtual-machines-guidelines"></a>Virtuelle maskiner retningslinjer

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)] 

I denne artikel fokuserer på forstå de nødvendige planlægning trin til oprettelse og administration af virtuelle maskiner (VM'er) i dit Azure-miljø.

## <a name="implementation-guidelines-for-vms"></a>Implementering retningslinjer for FOS
Beslutninger:

- Hvor mange FOS kræver til forskellige programmet niveauer og komponenter af infrastrukturen?
- Hvilke CPU og hukommelse ressourcer skal hver VM, og hvad er lagerplads?

Opgaver:

- Definere arbejdsmængder for dit program og i FOS kræver ressourcer.
- Justere ressource behov for hver VM med den relevante VM størrelse og lager type.
- Definere dine ressourcegrupper for de forskellige niveauer og komponenter af infrastrukturen.
- Definere dine VM navngivningskonvention.
- Oprette din FOS med Azure PowerShell web portalen, eller med ressourcestyring skabeloner.

## <a name="virtual-machines"></a>Virtuelle maskiner

En af de vigtigste komponenter i dit Azure miljø er sandsynligvis FOS. Dette er, hvor du kører din programmer, databaser, godkendelsestjenester osv.

Det er vigtigt at forstå de [forskellige VM størrelser](virtual-machines-windows-sizes.md) for at tilpasse korrekt størrelsen dit miljø mod en ydeevne og omkostninger perspektiv. Hvis din FOS ikke har nok CPU kerner eller hukommelse, langsommere ydeevnen for dit program uanset hvor godt det er designet og udviklet. Gennemgå de foreslåede arbejdsmængder for hver VM serie som udgangspunkt, efterhånden som du beslutte, hvilke størrelse VM skal bruges for hver komponent i infrastrukturen. Du kan [ændre størrelsen på en VM](https://azure.microsoft.com/blog/resize-virtual-machines/) efter installation.

Lagerplads afspilles en vigtig rolle i VM ydeevne. Du kan bruge Standard lagerplads, der bruger normalt rundt om diske, eller Premium lagerplads til høj i/o-arbejdsmængder og optimal ydeevne, der bruger SSD diske. Som med VM størrelse, der er omkostninger overvejelser i forbindelse med til at vælge lagerplads mediet. Du kan læse [lagerplads infrastruktur retningslinjer artikel](virtual-machines-windows-infrastructure-storage-solutions-guidelines.md) for at forstå, hvordan til at designe passende lager til optimal ydeevne af din FOS.


## <a name="resource-groups"></a>Ressourcegrupper
Komponenter som FOS grupperes logisk sammen for at lette administration og vedligeholdelse ved hjælp af [Azure ressourcegrupper](../azure-resource-manager/resource-group-overview.md). Ved hjælp af grupper, kan du oprette, administrere og overvåge alle de ressourcer, der udgør et bestemt program. Du kan også implementere [Rollebaseret adgangskontrol](../active-directory/role-based-access-control-what-is.md) for at give adgang til andre i dit team til kun de ressourcer, de skal bruge. Tage tid til at planlægge ud af dine grupper og rolletildelinger. Der er forskellige måder at faktisk designe og implementere ressourcegrupper, så sørg for at læse [ressource grupper retningslinjer artikel](virtual-machines-windows-infrastructure-resource-groups-guidelines.md) for at forstå, hvordan du bedst udvidelse din FOS.


## <a name="templates"></a>Skabeloner 
Du kan oprette skabeloner, der er defineret af deklarativ JSON-filer, du opretter din FOS. Skabeloner typisk oprette også den nødvendige lagerplads netværk netværksgrænseflader, IP-adresser, og sammen med FOS sig selv. Du bruger skabeloner til at oprette ensartede og genskabes miljøer for udvikling og test formål at gentage nemt fremstilling miljøer og omvendt. Du kan læse mere om [oprettelse og brug af skabeloner](../azure-resource-manager/resource-group-overview.md#template-deployment) at forstå, hvordan du kan bruge dem til oprettelse og implementere din FOS.


## <a name="next-steps"></a>Næste trin
[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)] 