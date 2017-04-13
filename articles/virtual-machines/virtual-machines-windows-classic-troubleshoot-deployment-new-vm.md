<properties
   pageTitle="Fejlfinding i forbindelse med Windows VM installation-klassisk | Microsoft Azure"
   description="Foretage fejlfinding af problemer med klassisk implementeringen, når du opretter en ny Windows virtuel maskine i Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="JiangChen79"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
  ms.service="virtual-machines-windows"
  ms.workload="na"
  ms.tgt_pltfrm="vm-windows"
  ms.devlang="na"
  ms.topic="article"
  ms.date="09/06/2016"
  ms.author="cjiang"/>

# <a name="troubleshoot-classic-deployment-issues-with-creating-a-new-windows-virtual-machine-in-azure"></a>Foretage fejlfinding af problemer med klassisk implementeringen til at oprette en ny Windows virtuel maskine i Azure

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-selectors](../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-selectors-include.md)]

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Indsamle overvågningslog logfiler

For at starte fejlfinding skal du indsamle overvågningslogge for at identificere den fejl, der er knyttet til problemet.

Klik på **Gennemse**i portalen Azure > **virtuelle maskiner** > *din Windows virtuelle maskine* > **Indstillinger** > **overvågningslogge**.

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[AZURE.INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**Y:** Hvis Operativsystemet er Windows generalized, og det er overført og/eller hentes med indstillingen generalized, være der ikke eventuelle fejl. På samme måde, hvis Operativsystemet er Windows specialiserede, og det er overført og/eller hentes med indstillingen specialiserede derefter der er ikke en fejl.

**Overfør fejl:**

**N<sup>1</sup>:** Hvis Operativsystemet er Windows generalized, og det er overført som specialiserede, får du en klargøring timeoutfejl med VM hængende på skærmbilledet OOBE.

**N<sup>2</sup>:** Hvis Operativsystemet er Windows specialiserede, og det er overført som generalized, får du fejlen klargøring fejl med VM hængende på skærmbilledet OOBE, fordi den nye VM kører med det oprindelige computernavn, brugernavn og adgangskode.

**Løsning:**

Du kan løse begge disse fejl ved at overføre den oprindelige virtuelle harddisk, tilgængelig lokalt, med de samme indstillinger som for OS (generalized/specialiserede). Hvis du vil overføre generalized, skal du huske at køre sysprep først. Se [oprette og overføre en Windows Server virtuel harddisk til Azure](virtual-machines-windows-classic-createupload-vhd.md) kan finde flere oplysninger.

**Registrere fejl:**

**N<sup>3</sup>:** Hvis Operativsystemet er Windows generalized, og det registreres som specialiserede, får du en klargøring timeoutfejl fordi den oprindelige VM ikke kan bruges, når den er markeret som generalized.

**N<sup>4</sup>:** Hvis Operativsystemet er Windows specialiserede, og det registreres som generalized, får du fejlen klargøring mislykkedes, fordi den nye VM kører med det oprindelige computernavn, brugernavn og adgangskode. Desuden originalen VM kan ikke bruges fordi det er markeret som specialiserede.

**Løsning:**

Du kan løse begge disse fejl ved at slette det aktuelle billede fra portalen og [finde dem fra de aktuelle virtuelle harddiske](virtual-machines-windows-classic-capture-image.md) med de samme indstillinger som for OS (generalized/specialiserede).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problem: Brugerdefineret / galleri / marketplace billede. fejl under allokering
Denne fejl opstår i situationer, når den nye VM anmodning sendes til en klynge, enten har ikke ledige plads til anmodningen, eller understøtter ikke VM størrelsen der anmodes om. Det er ikke muligt at blande forskellige række FOS i den samme skybaseret tjeneste. Hvis du vil oprette en ny VM af en anden størrelse end hvad skybaseret tjeneste kan understøtte, mislykkes Beregn anmodningen så.

Afhængigt af begrænsninger af skytjenesten du kan bruge til at oprette den nye VM, kan du støde på fejlen skyldes et af to situationer.

**Årsag 1:** Skytjenesten er fastgjort til en bestemt klynge, eller det er knyttet til en forbindelse gruppe og dermed fastgjort til en bestemt klynge ved design. Så nye Beregn ressource, som i den valgte forbindelse gruppe er prøvet i den samme klynge, der er vært for de eksisterende ressourcer. Men samme klynge kan enten ikke understøtter den anmodede VM størrelse eller har nok ledig plads, hvilket resulterer i en fordeling fejl. Dette er sandt om de nye ressourcer, der er oprettet via en ny tjeneste i skyen eller via en eksisterende skybaseret tjeneste.

**Løsning 1:**

- Oprette en ny tjeneste i skyen og knytte den til et område eller et område-baserede virtuelt netværk.
- Oprette en ny VM i den nye tjeneste i skyen.
  Hvis du får en fejl, når du forsøger at oprette en ny tjeneste i skyen, kan du enten prøv igen på et senere tidspunkt eller ændre området for skytjenesten.

> [AZURE.IMPORTANT] Hvis du forsøgte at oprette en ny VM i en eksisterende skybaseret tjeneste, men ikke kunne og skulle oprette en ny skybaseret tjeneste til din nye VM, kan du vælge at konsolidere alle FOS i den samme skybaseret tjeneste. Slet FOS i eksisterende skytjenesten gør du ved, og frigøre dem fra deres diske i den nye tjeneste i skyen. Men det er vigtigt at huske, at nye skytjenesten har et nyt navn og VIP, så du skal opdatere disse oplysninger til alle afhængigheder, der aktuelt bruger disse oplysninger til den eksisterende skybaseret tjeneste.

**Årsag 2:** Skytjenesten er knyttet til et virtuelt netværk, der er knyttet til en forbindelse, så det er fastgjort til en bestemt klynge ved design. Alle nye Beregn ressource, som i den valgte forbindelse gruppe er derfor prøvet i den samme klynge, der er vært for de eksisterende ressourcer. Men samme klynge kan enten ikke understøtter den anmodede VM størrelse eller har nok ledig plads, hvilket resulterer i en fordeling fejl. Dette er sandt om de nye ressourcer, der er oprettet via en ny tjeneste i skyen eller via en eksisterende skybaseret tjeneste.

**Løsning 2:**

- Oprette et nyt internationale virtuelle netværk.
- Oprette den nye VM i det nye virtuelle netværk.
- [Tilslut din eksisterende virtuelt netværk](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) til det nye virtuelle netværk. Se mere om [internationale virtuelle netværk](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/). Alternativt kan du [overføre din forbindelse-gruppe-baserede virtuelt netværk til et internationale virtuelt netværk](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/), og derefter oprette den nye VM.

## <a name="next-steps"></a>Næste trin
Hvis du støder på problemer, når du starter en holdt op med Windows-VM eller ændre størrelsen på en eksisterende Windows VM i Azure, se [fejlfinding på klassisk installation problemer med at genstarte eller ændre størrelsen på en eksisterende Windows virtuel maskine i Azure](windows/classic/virtual-machines-windows-classic-restart-resize-error-troubleshooting.md).
