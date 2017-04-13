<properties
   pageTitle="VM at genstarte eller ændre størrelsen på problemer med | Microsoft Azure"
   description="Foretage fejlfinding af Ressourcestyring installation problemer med at genstarte eller ændre størrelsen på en eksisterende Linux virtuel maskine i Azure"
   services="virtual-machines-linux, azure-resource-manager"
   documentationCenter=""
   authors="Deland-Han"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
   ms.service="virtual-machines-linux"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-linux"
   ms.devlang="na"
   ms.workload="required"
   ms.date="09/09/2016"
   ms.author="delhan"/>

# <a name="troubleshoot-resource-manager-deployment-issues-with-restarting-or-resizing-an-existing-linux-virtual-machine-in-azure"></a>Foretage fejlfinding af Ressourcestyring installation problemer med at genstarte eller ændre størrelsen på en eksisterende Linux virtuel maskine i Azure

Når du forsøger at starte en holdt op med at Azure Virtual Machine (VM) eller ændre størrelsen på en eksisterende Azure VM, er den almindelige fejl, du støder på en fejl under allokering. Denne fejl søgeresultaterne ved klynge eller område ikke har ressourcer eller understøtter ikke den anmodede VM størrelse.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Indsamle overvågningslog logfiler

For at starte fejlfinding skal du indsamle overvågningslogge for at identificere den fejl, der er knyttet til problemet. Følgende links indeholder detaljerede oplysninger om processen:

[Fejlfinding i forbindelse med ressource gruppe-installationer med Azure-portalen](../resource-manager-troubleshoot-deployments-portal.md)

[Overvåge forskellige handlinger med ressourcestyring](../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problem: Fejl, når du starter en holdt op med at VM

Du forsøger at starte en holdt op med at VM men får en fejl under allokering.

### <a name="cause"></a>Årsag

Anmodning om at starte holdt op med at VM skal forsøges ved den oprindelige klynge, der er vært skytjenesten. Klyngen har dog ikke ledig plads til at udføre anmodningen.

### <a name="resolution"></a>Opløsning

*   Stoppe alle FOS i sættet tilgængelighed, og genstart derefter hver VM.

  1. Klik på **ressourcegrupper** > _din ressourcegruppe_ > **ressourcer** > _angive din tilgængelighed_ > **virtuelle maskiner** > _din virtuelle maskine_ > **Stop**.

  2. Når alle FOS stopper, markere hvert af de holdt op med at FOS, og klik på Start.

*   Forsøg genstart anmodningen igen på et senere tidspunkt.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problem: Fejl, når du ændre størrelsen på en eksisterende VM

Du forsøger at ændre størrelsen på en eksisterende VM men får en fejl under allokering.

### <a name="cause"></a>Årsag

Anmodningen om at ændre størrelsen på VM skal forsøges ved den oprindelige klynge, der er vært skytjenesten. Klyngen understøtter dog ikke den anmodede VM størrelse.

### <a name="resolution"></a>Opløsning

* Prøv igen ved hjælp af en mindre VM anmodningen.

* Hvis størrelsen på den ønskede VM ikke kan ændres:

  1. Stop alle FOS i sættet tilgængelighed.

    * Klik på **ressourcegrupper** > _din ressourcegruppe_ > **ressourcer** > _angive din tilgængelighed_ > **virtuelle maskiner** > _din virtuelle maskine_ > **Stop**.

  2. Når alle FOS stopper, ændre størrelsen på den ønskede VM til en større størrelse.
  3. Vælg den ændrede VM og klik på **Start**, og derefter i gang med hver af de holdt op med at FOS.

## <a name="next-steps"></a>Næste trin

Hvis du støder på problemer, når du opretter en ny Linux VM i Azure, se [fejlfinding på installation problemer med at oprette en ny Linux virtuel maskine i Azure](../virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md).
