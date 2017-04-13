<properties
   pageTitle="Fejlfinding i forbindelse med Linux VM installation-Ressourcestyring | Microsoft Azure"
   description="Foretage fejlfinding af problemer med installation af Ressourcestyring, når du opretter en ny Linux virtuel maskine i Azure"
   services="virtual-machines-linux, azure-resource-manager"
   documentationCenter=""
   authors="JiangChen79"
   manager="felixwu"
   editor=""
   tags="top-support-issue, azure-resource-manager"/>

<tags
  ms.service="virtual-machines-linux"
  ms.workload="na"
  ms.tgt_pltfrm="vm-linux"
  ms.devlang="na"
  ms.topic="article"
  ms.date="09/09/2016"
  ms.author="cjiang"/>

# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Foretage fejlfinding af problemer med installation af Ressourcestyring til at oprette en ny Linux virtuel maskine i Azure

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Indsamle overvågningslog logfiler

For at starte fejlfinding skal du indsamle overvågningslogge for at identificere den fejl, der er knyttet til problemet. Følgende links indeholder detaljerede oplysninger om at følge processen.

[Fejlfinding i forbindelse med ressource gruppe-installationer med Azure-portalen](../resource-manager-troubleshoot-deployments-portal.md)

[Overvåge forskellige handlinger med ressourcestyring](../resource-group-audit.md)

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[AZURE.INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** Hvis Operativsystemet er Linux generalized, og det er overført og/eller hentes med indstillingen generalized, være der ikke eventuelle fejl. På samme måde, hvis Operativsystemet er Linux specialiserede, og det er overført og/eller hentes med indstillingen specialiserede derefter der er ikke en fejl.

**Overfør fejl:**

**N<sup>1</sup>:** Hvis Operativsystemet er Linux generalized, og det er overført som specialiserede, får du en klargøring timeoutfejl fordi VM er fastlåst ved klargøring fasen.

**N<sup>2</sup>:** Hvis Operativsystemet er Linux specialiserede, og det er overført som generalized, får du fejlen klargøring mislykkedes, fordi den nye VM kører med det oprindelige computernavn, brugernavn og adgangskode.

**Løsning:**

Du kan løse begge disse fejl ved at overføre den oprindelige virtuelle harddisk, tilgængelig lokalt, med de samme indstillinger som for OS (generalized/specialiserede). Hvis du vil overføre generalized, skal du huske at køre - deprovision først.

**Registrere fejl:**

**N<sup>3</sup>:** Hvis Operativsystemet er Linux generalized, og det registreres som specialiserede, får du en klargøring timeoutfejl fordi den oprindelige VM ikke kan bruges, når den er markeret som generalized.

**N<sup>4</sup>:** Hvis Operativsystemet er Linux specialiserede, og det registreres som generalized, får du fejlen klargøring mislykkedes, fordi den nye VM kører med det oprindelige computernavn, brugernavn og adgangskode. Desuden originalen VM kan ikke bruges fordi det er markeret som specialiserede.

**Løsning:**

Du kan løse begge disse fejl ved at slette det aktuelle billede fra portalen og [finde dem fra de aktuelle virtuelle harddiske](virtual-machines-linux-capture-image.md) med de samme indstillinger som for OS (generalized/specialiserede).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problem: Brugerdefineret / galleri / marketplace billede. fejl under allokering
Denne fejl opstår i situationer, når den nye VM anmodning fastgøres til en klynge, enten ikke understøtter VM størrelsen anmodes, eller har ikke ledige plads til anmodningen.

**Årsag 1:** Klyngen understøtter ikke den anmodede VM størrelse.

**Løsning 1:**

- Prøv igen ved hjælp af en mindre VM anmodningen.
- Hvis størrelsen på den ønskede VM ikke kan ændres:
  - Stop alle FOS i sættet tilgængelighed.
  Klik på **ressourcegrupper** > *din ressourcegruppe* > **ressourcer** > *angive din tilgængelighed* > **virtuelle maskiner** > *din virtuelle maskine* > **Stop**.
  - Når alle FOS stoppe, skal du oprette den nye VM i den ønskede størrelse.
  - Først skal starte den nye VM og markere hvert af de holdt op med at FOS, og klik på **Start**.

**Årsag 2:** Klyngen har ikke ledige ressourcer.

**Løsning 2:**

- Forsøg anmodningen igen på et senere tidspunkt.
- Hvis den nye VM kan være en del af en anden tilgængelighed sæt
  - Oprette en ny VM i en anden tilgængelighed, angive (i det samme område).
  - Føje den nye VM til det samme virtuelle netværk.

## <a name="next-steps"></a>Næste trin
Hvis du støder på problemer, når du starter en holdt op med at Linux VM eller ændre størrelsen på en eksisterende Linux VM i Azure, se [fejlfinding af Ressourcestyring installation problemer med at genstarte eller ændre størrelsen på en eksisterende Linux virtuel maskine i Azure](virtual-machines-linux-restart-resize-error-troubleshooting.md).
