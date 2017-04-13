<properties
   pageTitle="VM at genstarte eller ændre størrelsen på problemer med | Microsoft Azure"
   description="Fejlfinding i forbindelse med klassisk installation problemer med at genstarte eller ændre størrelsen på en eksisterende Linux virtuel maskine i Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="Deland-Han"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
   ms.service="virtual-machines-linux"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="required"
   ms.date="09/20/2016"
   ms.devlang="na"
   ms.author="delhan"/>

# <a name="troubleshoot-classic-deployment-issues-with-restarting-or-resizing-an-existing-linux-virtual-machine-in-azure"></a>Fejlfinding i forbindelse med klassisk installation problemer med at genstarte eller ændre størrelsen på en eksisterende Linux virtuel maskine i Azure

> [AZURE.SELECTOR]
- [Klassisk](../articles/virtual-machines/virtual-machines-linux-classic-restart-resize-error-troubleshooting.md)
- [Ressourcestyring](../articles/virtual-machines/virtual-machines-linux-restart-resize-error-troubleshooting.md)

Når du forsøger at starte en holdt op med at Azure Virtual Machine (VM) eller ændre størrelsen på en eksisterende Azure VM, er den almindelige fejl, du støder på en fejl under allokering. Denne fejl søgeresultaterne ved klynge eller område ikke har ressourcer eller understøtter ikke den anmodede VM størrelse.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Indsamle overvågningslog logfiler

For at starte fejlfinding skal du indsamle overvågningslogge for at identificere den fejl, der er knyttet til problemet.

Klik på **Gennemse**i portalen Azure > **virtuelle maskiner** > _din Linux virtuelle maskine_ > **Indstillinger** > **overvågningslogge**.

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problem: Fejl, når du starter en holdt op med at VM

Du forsøger at starte en holdt op med at VM men får en fejl under allokering.

### <a name="cause"></a>Årsag

Anmodning om at starte holdt op med at VM skal forsøges ved den oprindelige klynge, der er vært skytjenesten. Klyngen har dog ikke ledig plads til at udføre anmodningen.

### <a name="resolution"></a>Opløsning

* Oprette en ny tjeneste i skyen og knytte den til enten et område eller et område-baserede virtuelt netværk, men ikke en forbindelse gruppe.

* Slet den holdt op med at VM.

* Genopret VM i den nye tjeneste i skyen ved hjælp af disketterne.

* Starte den nyoprettede VM.

Hvis du får en fejl, når du forsøger at oprette en ny tjeneste i skyen, kan du enten prøv igen på et senere tidspunkt eller ændre området for skytjenesten.

> [AZURE.IMPORTANT] Ny skytjenesten har et nyt navn og VIP, så du skal ændre oplysningerne for alle de afhængigheder, bruger disse oplysninger til den eksisterende skybaseret tjeneste.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problem: Fejl, når du ændre størrelsen på en eksisterende VM

Du forsøger at ændre størrelsen på en eksisterende VM men får en fejl under allokering.

### <a name="cause"></a>Årsag

Anmodningen om at ændre størrelsen på VM skal forsøges ved den oprindelige klynge, der er vært skytjenesten. Klyngen understøtter dog ikke den anmodede VM størrelse.

### <a name="resolution"></a>Opløsning

Reducere størrelsen på den ønskede VM, og prøv igen ændre størrelsen på anmodningen.

* Klik på **Gennemse alle** > **virtuelle maskiner (klassisk)** > _din virtuelle maskine_ > **Indstillinger** > **størrelse**. Detaljeret vejledning, skal du se [ændre størrelsen på den virtuelle maskine](https://msdn.microsoft.com/library/dn168976.aspx).

Hvis det ikke er muligt at reducere størrelsen VM, skal du følge disse trin:

  * Oprette en ny skybaseret tjeneste, sikre, at det er ikke er knyttet til en forbindelse gruppe og ikke er knyttet til et virtuelt netværk, der er knyttet til en forbindelse gruppe.

  * Oprette en ny, større størrelse VM i den.

Du kan konsolidere alle FOS i den samme skybaseret tjeneste. Hvis din eksisterende skybaseret tjeneste er knyttet til et område-baserede virtuelt netværk, kan du forbinde nye skytjenesten til det eksisterende virtuelle netværk.

Hvis eksisterende skytjenesten ikke er knyttet til et område-baserede virtuelt netværk, har du at slette FOS i eksisterende skytjenesten og gendanne dem i den nye tjeneste i skyen fra deres diske. Men det er vigtigt at huske, at nye skytjenesten har et nyt navn og VIP, så du skal opdatere disse oplysninger til alle afhængigheder, der aktuelt bruger disse oplysninger til den eksisterende skybaseret tjeneste.

## <a name="next-steps"></a>Næste trin

Hvis du støder på problemer, når du opretter en ny Linux VM i Azure, se [fejlfinding på installation problemer med at oprette en ny Linux virtuel maskine i Azure](../virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md).
