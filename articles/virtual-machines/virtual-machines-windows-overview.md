<properties
    pageTitle="Oversigt over Windows virtuelle maskiner | Microsoft Azure"
    description="Få mere at vide om oprettelse og administration af Windows virtuelle maskiner i Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/20/2016"
    ms.author="davidmu"/>

# <a name="overview-of-windows-virtual-machines-in-azure"></a>Oversigt over Windows virtuelle maskiner i Azure

Azure virtuelle maskiner (VM) er et af flere typer [efter behov, SVG dataressourcer](../app-service-web/choose-web-site-cloud-service-vm.md) som Azure tilbyder. Du vælger typisk en VM, når du har brug for mere kontrol over det miljø, end de andre valg tilbyder. I denne artikel indeholder oplysninger om, hvad du bør overveje, før du opretter en VM, hvordan du opretter den, og hvordan du administrerer.

En Azure VM giver dig mulighed for virtualization uden at købe og vedligeholde den fysiske hardware, der kører den. Men du stadig nødt til at vedligeholde VM ved at udføre opgaver såsom at konfigurere, rettelse og installation af software, der kører på den.

Azure virtuelle maskiner kan bruges på forskellige måder. Nogle eksempler er:

- **Udvikling og test** – Azure FOS giver en hurtig og nem måde at oprette en computer med specifikke konfigurationer skal kode og teste et program.
- **Programmer i skyen** – fordi behov for dit program kan vise udsving, kan det gøre økonomiske mening at køre den på en VM i Azure. Du betaler for ekstra FOS, når du har brug for dem, og luk dem, når du ikke.
- **Udvidet datacenter** – virtuelle maskiner i en Azure virtuelt netværk kan nemt forbindelse til din organisations netværk.

Antallet af FOS, der bruger dit program kan skalere op og ud af, at det er påkrævet for at opfylde dine behov.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Hvad har jeg brug at overveje, før du opretter en VM?

Der er altid en lang række [Designovervejelser](virtual-machines-windows-infrastructure-virtual-machine-guidelines.md) , når du opretter ud af en programmet infrastruktur i Azure. Disse aspekter af en VM er vigtigt at overveje, før du går i gang:

- Navnene på dine programmet ressourcer
- Den placering, hvor ressourcerne, der er gemt
- Størrelsen af VM
- Det maksimale antal VM'er, der kan oprettes
- Det operativsystem, der kører, hvor VM
- Konfigurationen af VM, når det startes
- Relaterede ressourcer, der hvor VM skal

### <a name="naming"></a>Navngive

En virtuel maskine har et [navn](virtual-machines-windows-infrastructure-naming-guidelines.md) , der er tildelt, og den har et computernavn, der er konfigureret som en del af operativsystemet. Navnet på en VM kan være op til 15 tegn.

Hvis du bruger Azure til at oprette operativsystem disken, er computernavn og navnet på den virtuelle computer de samme. Hvis du [overføre og bruge dit eget billede](virtual-machines-windows-upload-image.md) , der indeholder et konfigureret tidligere operativsystem og bruge det til at oprette en virtuel maskine, navnene kan være forskellige. Vi anbefaler, at når du overfører din egen billedfil, du foretager computernavnet i operativsystemet og den virtuelle maskine navn på samme måde.

### <a name="locations"></a>Placeringer

Alle de ressourcer, der er oprettet i Azure fordeles på tværs af flere [geografiske områder](https://azure.microsoft.com/regions/) i hele verden. Området er som regel, kaldet **placering** , når du opretter en VM. Placeringen angiver for en VM, hvor de virtuelle harddiske er gemt.

I denne tabel vises nogle af de måder, du kan få en liste over tilgængelige placeringer.

| Metode | Beskrivelse |
| ------ | ----------- |
| Azure-portalen | Vælg en placering på listen, når du opretter en VM. |
| Azure PowerShell | Bruge kommandoen [Get-AzureRmLocation](https://msdn.microsoft.com/library/mt619449.aspx) . |
| REST-API | Brug handlingen [listen placeringer](https://msdn.microsoft.com/library/dn790540.aspx) . |

### <a name="vm-size"></a>VM størrelse

[Størrelse](virtual-machines-windows-sizes.md) af VM, som du bruger bestemmes af arbejdsbelastningen, du vil køre. Den størrelse, som du vælger derefter bestemmer faktorer som behandling power, hukommelse og lagerkapacitet. Azure tilbyder et bredt udvalg af størrelser til at understøtte mange typer bruger.

Azure gebyrer en [hver time pris](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) baseret på VM størrelse og -operativsystemet. Azure gebyrer for delvis timer, kun for de minutter, der bruges. Lager priser og faktureres separat.

### <a name="vm-limits"></a>VM begrænsninger

Dit abonnement har standard [kvote begrænsninger](../azure-subscription-service-limits.md) på plads, der kan påvirke installationen af mange FOS for projektet. Den nuværende grænse på grundlag af per abonnement er 20 FOS per område. Kan hæves begrænsninger ved at arkivere en supportbilletter anmoder om et større.

### <a name="operating-system-disks-and-images"></a>Operativsystem diske og billeder

Virtuelle maskiner bruge [virtuelle harddiske (virtuelle harddiske)](virtual-machines-windows-about-disks-vhds.md) til at gemme deres operativsystemet (OS) og data. Virtuelle harddiske bruges også til de billeder, du kan vælge mellem at installere en OS. 

Azure indeholder mange [marketplace billeder](https://azure.microsoft.com/marketplace/virtual-machines/) der skal bruges med forskellige versioner og typer af Windows Server-operativsystemer. Marketplace billeder er identificeret med billede af publisher, tilbud, sku og version (typisk version er angivet som seneste). 

I denne tabel vises nogle måder, du kan finde oplysninger om et billede.

| Metode | Beskrivelse |
| ------ | ----------- |
| Azure-portalen | Værdierne, der angives automatisk for dig, når du vælger et billede for at bruge. |
| Azure PowerShell | [Get-AzureRMVMImagePublisher](https://msdn.microsoft.com/library/mt603484.aspx) -placering "placering"<BR>[Get-AzureRMVMImageOffer](https://msdn.microsoft.com/library/mt603824.aspx) -placering "placering"-Publisher "publisherName"<BR>[Get-AzureRMVMImageSku](https://msdn.microsoft.com/library/mt619458.aspx) -placering "placering"-Publisher "publisherName"-tilbyder "offerName" |
| REST API'er | [Listen billede udgivere](https://msdn.microsoft.com/library/mt743702.aspx)<BR>[Billede af listen indeholder](https://msdn.microsoft.com/library/mt743700.aspx)<BR>[Listen billede lagerenheder](https://msdn.microsoft.com/library/mt743701.aspx) |

Du kan vælge at [overføre og bruge dit eget billede](virtual-machines-windows-upload-image.md) , og når du gør, publisher navn, tilbud og sku bruges ikke.

### <a name="extensions"></a>Filtypenavne

VM [filtypenavne](virtual-machines-windows-extensions-features.md) Giv din VM yderligere funktioner til konfiguration af indlæg installation og automatiserede opgaver.

Disse almindelige opgaver kan gøres ved hjælp af filtypenavne:

- **Køre brugerdefinerede scripts** – i [Brugerdefineret Script filtypenavn](virtual-machines-windows-extensions-customscript.md) , kan du konfigurere arbejdsbelastninger på VM ved at køre scriptet, når VM er klargjort.
- **Implementer og administrere konfigurationer** – [PowerShell beskedteksten tilstand konfiguration (DTK) lokalnummer](virtual-machines-windows-extensions-dsc-overview.md) kan du konfigurere DTK på en VM til at administrere konfigurationer og miljøer.
- **Indsamle diagnosticering data** – [Azure diagnosticering filtypenavn](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) , kan du konfigurere VM for at indsamle diagnosticering data, der kan bruges til at overvåge tilstanden for dit program.

### <a name="related-resources"></a>Relaterede ressourcer

Ressourcerne i denne tabel anvendes af VM og skal findes eller oprettes, når VM oprettes.

| Ressource | Påkrævet | Beskrivelse |
| -------- | -------- | ----------- |
| [Ressourcegruppe](../azure-resource-manager/resource-group-overview.md) | Ja | VM skal være indeholdt i en ressourcegruppe. |
| [Lagerplads konto](../storage/storage-create-storage-account.md) | Ja | VM skal kontoen lagerplads til at gemme de virtuelle harddiske. |
| [Virtuelt netværk](../virtual-network/virtual-networks-overview.md) | Ja | VM skal være medlem af et virtuelt netværk. |
| [Offentlige IP-adresse](../virtual-network/virtual-network-ip-addresses-overview-arm.md) | Nej | VM kan have en offentlige IP-adresse, der er tildelt til at få fjernadgang til den. |
| [Netværksgrænsefladen](../virtual-network/virtual-network-network-interface-overview.md) | Ja | VM skal netværksgrænsefladen til at kommunikere i netværket. |
| [Datadisce](virtual-machines-windows-attach-disk-portal.md) | Nej | VM kan medtage datadisce for at udvide lagerplads funktioner. |

## <a name="how-do-i-create-my-first-vm"></a>Hvordan opretter jeg min første VM?

Du har flere valgmuligheder for oprettelse af din VM. Det valg, du foretager, afhænger af det miljø, du befinder dig i. 

Denne tabel indeholder oplysninger til at komme i gang med at oprette din VM.

| Metode | Artikel |
| ------ | ------- |
| Azure-portalen | [Oprette en virtuel maskine, der kører Windows ved hjælp af portalen](virtual-machines-windows-hero-tutorial.md) |
| Skabeloner | [Oprette en Windows virtuel maskine med en skabelon for ressourcestyring](virtual-machines-windows-ps-template.md) |
| Azure PowerShell | [Oprette en Windows-VM ved hjælp af PowerShell](virtual-machines-windows-ps-create.md) |
| Klient SDK'er | [Installere Azure ressourcer ved hjælp af C#](virtual-machines-windows-csharp.md) |
| REST API'er | [Oprette eller opdatere en Virtual Machine](https://msdn.microsoft.com/library/mt163591.aspx) |

Du håber aldrig sker det, men nogle gange noget går galt. Hvis problemet sker for dig, skal du se på oplysningerne i [foretage fejlfinding af Ressourcestyring installation problemer med at oprette en Windows virtuel maskine i Azure](virtual-machines-windows-troubleshoot-deployment-new-vm.md).

## <a name="how-do-i-manage-the-vm-that-i-created"></a>Hvordan administrerer jeg VM, som jeg har oprettet?

FOS kan administreres ved hjælp af en browser-baseret portal kommandolinjen værktøjer med support til scripting eller direkte via API'er. Nogle typiske administrationsopgaver, du kan udføre får oplysninger om en VM vedkommende logger på en VM, administrerer tilgængelighed og oprettelse af sikkerhedskopier.

### <a name="get-information-about-a-vm"></a>Få oplysninger om en VM

I denne tabel vises nogle af de måder, som du kan få oplysninger om en VM.

| Metode | Beskrivelse |
| ------ | ----------- |
| Azure-portalen | Klik på **virtuelle maskiner** , og vælg derefter VM på listen i menuen hub. På bladet af VM har du adgang til oversigtsoplysninger, indstille værdier og overvåge målepunkter. |
| Azure PowerShell | Finde oplysninger om brug af PowerShell til at administrere FOS, [administrere virtuelle Azure-computere ved hjælp af Ressourcestyring og PowerShell](virtual-machines-windows-ps-manage.md). |
| REST-API | Brug handlingen [få VM oplysninger](https://msdn.microsoft.com/library/mt163682.aspx) til at få oplysninger om en VM. |
| Klient SDK'er | Finde oplysninger om brug af C# til at administrere FOS, [administrere virtuelle Azure-computere ved hjælp af Azure ressourcestyring og C#](virtual-machines-windows-csharp-manage.md). |

### <a name="log-on-to-the-vm"></a>Log på VM

Du kan bruge knappen Opret forbindelse i Azure portalen for at [starte en session med RDP (Remote Desktop)](virtual-machines-windows-connect-logon.md). Ting, du kan også gå galt, når du forsøger at bruge en forbindelse til. Hvis denne situation sker for dig, kan du se Hjælp-oplysninger i [foretage fejlfinding af Fjernskrivebord forbindelser til en Azure virtuelt, der kører Windows](virtual-machines-windows-troubleshoot-rdp-connection.md).

### <a name="manage-availability"></a>Administrere tilgængelighed

Det er vigtigt for dig at forstå, hvordan du [sikrer høj tilgængelighed](virtual-machines-windows-manage-availability.md) for dit program. Denne konfiguration omfatter oprettelse af flere FOS for at sikre, at mindst én kører.

I rækkefølge for din installation til kvalificerer til vores 99.95 VM serviceaftale, skal du installere to eller flere VM'er, der kører arbejdsbelastningen inde i en [angivet tilgængelighed](virtual-machines-windows-infrastructure-availability-sets-guidelines.md). Denne konfiguration sikrer, at din FOS fordeles på tværs af flere fejl domæner og er installeret på hosts med forskellige vedligeholdelse windows. Den fulde [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) forklares garanterede tilgængeligheden af Azure som helhed.
 
### <a name="back-up-the-vm"></a>Sikkerhedskopiere VM
 
En [samling af legitimationsoplysninger gendannelse Services](../backup/backup-introduction-to-azure-backup.md) bruges til at beskytte data og aktiver i både Azure sikkerhedskopiering og gendannelse af websteder Azure services. Du kan bruge en samling af legitimationsoplysninger gendannelse Services til at [installere og administrere sikkerhedskopier til brug af Ressourcestyring FOS ved hjælp af PowerShell](../backup/backup-azure-vms-automation.md). 

## <a name="next-steps"></a>Næste trin

- Hvis din formålet er at arbejde med Linux FOS, skal du se på [Azure og Linux](virtual-machines-linux-azure-overview.md).
- Lær mere om retningslinjer rundt om konfiguration af din infrastruktur i [eksempel Azure infrastruktur gennemgang](virtual-machines-windows-infrastructure-example.md).
- Sørg for, at du følger de [Bedste fremgangsmåder til at køre en Windows-VM på Azure](virtual-machines-windows-guidance-compute-single-vm.md).


