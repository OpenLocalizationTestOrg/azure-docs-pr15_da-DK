<properties
   pageTitle="Fejlfinding i forbindelse med Windows VM installation-Ressourcestyring | Microsoft Azure"
   description="Foretage fejlfinding af problemer med installation af Ressourcestyring, når du opretter en ny Windows virtuel maskine i Azure"
   services="virtual-machines-windows, azure-resource-manager"
   documentationCenter=""
   authors="JiangChen79"
   manager="felixwu"
   editor=""
   tags="top-support-issue, azure-resource-manager"/>

<tags
  ms.service="virtual-machines-windows"
  ms.workload="na"
  ms.tgt_pltfrm="vm-windows"
  ms.devlang="na"
  ms.topic="article"
  ms.date="09/09/2016"
  ms.author="cjiang"/>

# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-windows-virtual-machine-in-azure"></a>Foretage fejlfinding af problemer med installation af Ressourcestyring til at oprette en ny Windows virtuel maskine i Azure

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Indsamle overvågningslog logfiler

For at starte fejlfinding skal du indsamle overvågningslogge for at identificere den fejl, der er knyttet til problemet. Følgende links indeholder detaljerede oplysninger om at følge processen.

[Fejlfinding i forbindelse med ressource gruppe-installationer med Azure-portalen](../resource-manager-troubleshoot-deployments-portal.md)

[Overvåge forskellige handlinger med ressourcestyring](../resource-group-audit.md)

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[AZURE.INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**Y:** Hvis Operativsystemet er Windows generalized, og det er overført og/eller hentes med indstillingen generalized, være der ikke eventuelle fejl. På samme måde, hvis Operativsystemet er Windows specialiserede, og det er overført og/eller hentes med indstillingen specialiserede derefter der er ikke en fejl.

**Overfør fejl:**

**N<sup>1</sup>:** Hvis Operativsystemet er Windows generalized, og det er overført som specialiserede, får du en klargøring timeoutfejl med VM hængende på skærmbilledet OOBE.

**N<sup>2</sup>:** Hvis Operativsystemet er Windows specialiserede, og det er overført som generalized, får du fejlen klargøring fejl med VM hængende på skærmbilledet OOBE, fordi den nye VM kører med det oprindelige computernavn, brugernavn og adgangskode.

**Opløsning**

Du kan løse begge disse fejl ved at bruge [Tilføj-AzureRmVhd for at overføre den oprindelige virtuelle harddisk](https://msdn.microsoft.com/library/mt603554.aspx), tilgængelig lokalt, skal med de samme indstillinger som for OS (generalized/specialiserede). Hvis du vil overføre generalized, skal du huske at køre sysprep først.

**Registrere fejl:**

**N<sup>3</sup>:** Hvis Operativsystemet er Windows generalized, og det registreres som specialiserede, får du en klargøring timeoutfejl fordi den oprindelige VM ikke kan bruges, når den er markeret som generalized.

**N<sup>4</sup>:** Hvis Operativsystemet er Windows specialiserede, og det registreres som generalized, får du fejlen klargøring mislykkedes, fordi den nye VM kører med den oprindelige computernavn, brugernavn og adgangskode. Desuden originalen VM kan ikke bruges fordi det er markeret som specialiserede.

**Opløsning**

Du kan løse begge disse fejl ved at slette det aktuelle billede fra portalen og [finde dem fra de aktuelle virtuelle harddiske](virtual-machines-windows-vhd-copy.md) med de samme indstillinger som for OS (generalized/specialiserede).

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Problem: Brugerdefineret/galleri/marketplace billede. fejl under allokering
Denne fejl opstår i situationer, når den nye VM anmodning fastgøres til en klynge, enten ikke understøtter VM størrelsen anmodes, eller har ikke tilgængelig ledig plads til at tage højde for din anmodning.

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
Hvis du støder på problemer, når du starter en holdt op med Windows-VM eller ændre størrelsen på en eksisterende Windows VM i Azure, se [fejlfinding af Ressourcestyring installation problemer med at genstarte eller ændre størrelsen på en eksisterende Windows virtuel maskine i Azure](virtual-machines-windows-restart-resize-error-troubleshooting.md).
