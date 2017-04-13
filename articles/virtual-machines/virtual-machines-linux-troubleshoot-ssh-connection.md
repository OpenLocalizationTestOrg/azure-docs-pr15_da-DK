<properties
    pageTitle="Foretage fejlfinding af forbindelsesproblemer SSH til en VM | Microsoft Azure"
    description="Sådan foretages fejlfinding af problemer som 'SSH forbindelse mislykkedes' eller 'SSH forbindelsen blev afvist' for en Azure VM kører Linux."
    keywords="SSH forbindelsen afvist, ssh fejl, azure ssh, SSH forbindelsen mislykkedes"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="iainfou"/>

# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Foretage fejlfinding af SSH forbindelser til en Azure Linux VM, mislykkes, fejl, eller er nægtet
Der er forskellige årsager, at der opstår fejl Secure Shell (SSH), SSH forbindelse fejl, eller SSH er afvist, når du forsøger at oprette forbindelse til en Linux virtuel maskine (VM). I denne artikel kan du finde og løse problemerne. Du kan bruge til Azure-portalen, Azure CLI eller VM Access-udvidelse til Linux til at fejlfinde og løse problemer med forbindelsen.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Hvis du har brug for mere hjælp på en hvilken som helst sted i denne artikel, kan du kontakte de Azure eksperter på [MSDN Azure og stakoverløb fora](http://azure.microsoft.com/support/forums/). Du kan også sende en Azure support-hændelse. Gå til den [Azure supportwebsted](http://azure.microsoft.com/support/options/) , og vælg **få support**. Oplysninger om brug af Azure understøtter, læse, [Microsoft Azure understøtter ofte stillede spørgsmål](http://azure.microsoft.com/support/faq/).


## <a name="quick-troubleshooting-steps"></a>Hurtige trin til fejlfinding
Efter hvert trin i fejlfindingen, kan du prøve at oprette forbindelse til VM.

1. Nulstil SSH konfigurationen.
2. Nulstil legitimationsoplysninger for brugeren.
3. Kontrollere reglerne, der [Netværk sikkerhedsgruppe](../virtual-network/virtual-networks-nsg.md) tillade SSH trafik.
    - Sørg for, at der findes en netværk sikkerhedsgruppe regel for at tillade SSH trafik (som standard TCP-port 22).
    - Du kan ikke bruge port omdirigering / tilknytning uden at bruge en Azure justering af belastning.
4. Tjekke [VM ressource tilstand](../resource-health/resource-health-overview.md). 
    - Sørg for, at VM rapporter som værende sund.
    - Hvis du har Start diagnosticering aktiveret, Bekræft VM ikke rapporterer Start fejl i loggene.
5. Genstart VM.
6. Geninstaller VM.

Fortsætte med at læse for mere detaljerede fejlfindingstrin og beskrivelser.


## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Tilgængelige metoder til at foretage fejlfinding af forbindelsesproblemer SSH

Du kan nulstille legitimationsoplysninger eller SSH konfiguration ved hjælp af en af følgende fremgangsmåder:

- [Azure portal](#using-the-azure-portal) - godt, hvis du har brug at nulstille hurtigt SSH konfigurationen eller SSH nøgle, og du har ikke installeret Azure værktøjer.
- [Azure CLI-kommandoer](#using-the-azure-cli) - nulstille Hvis du allerede er på kommandolinjen, hurtigt SSH konfiguration eller legitimationsoplysninger.
- [Azure VMAccessForLinux lokalnummer](#using-the-vmaccess-extension) - Opret og genbruge json definitionsfiler for at nulstille SSH konfiguration eller bruger legitimationsoplysninger.

Efter hvert trin i fejlfindingen, kan du prøve at oprette forbindelse til din VM igen. Hvis du stadig ikke oprette forbindelse, kan du prøve næste trin.


## <a name="using-the-azure-portal"></a>Ved hjælp af portalen Azure
Portalen Azure giver en hurtig måde at nulstille SSH konfiguration eller bruger legitimationsoplysninger uden at installere en hvilken som helst værktøjer på din lokale computer.

Vælg din VM i portalen Azure. Rul ned til sektionen **Support + fejlfinding** , og vælg **Nulstil adgangskode** som i følgende eksempel:

![Nulstil SSH konfiguration eller legitimationsoplysninger i portalen Azure](./media/virtual-machines-linux-troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a>Nulstil SSH konfiguration
Som et første skridt, vælge `Reset SSH configuration only` i menuen **tilstand** rullelisten som det forrige skærmbillede, klik derefter på knappen **Nulstil** . Når denne handling er afsluttet, kan du prøve at få adgang til din VM igen.

### <a name="reset-ssh-credentials-for-a-user"></a>Nulstille SSH legitimationsoplysninger for en bruger
Hvis du vil nulstille legitimationsoplysninger for en eksisterende bruger, skal du enten vælge `Reset SSH public key` eller `Reset password` fra rullemenuen **tilstand** som i det forrige skærmbillede. Angiv brugernavnet og en SSH nøgle eller ny adgangskode, og derefter klikke på knappen **Nulstil** .

Du kan også oprette en bruger med sudo rettigheder på VM fra denne menu. Angiv et nyt brugernavn og den tilknyttede adgangskode eller SSH nøgle, og klik derefter på knappen **Nulstil** .


## <a name="using-the-azure-cli"></a>Brug af Azure CLI
Hvis du ikke allerede har gjort, [installere Azure CLI og oprette forbindelse til dit Azure-abonnement](../xplat-cli-install.md). Sørg for, at ved hjælp af Ressourcestyring tilstand på følgende måde:

```
azure config mode arm
```

Hvis du har oprettet og har overført et brugerdefineret billede af Linux disken, Sørg for, at [Microsoft Azure Linux Agent](virtual-machines-linux-agent-user-guide.md) version 2.0.5 eller nyere er installeret. For FOS oprettet ved hjælp af galleriet billeder, er dette access lokalnummer allerede installeret og konfigureret for dig.

### <a name="reset-ssh-configuration"></a>Nulstil SSH konfiguration
SSHD konfigurationen selve er forkert konfigureret eller tjenesten fandt en fejl. Du kan nulstille SSHD at sikre, at konfigurationen SSH selve er gyldig. Nulstilling af SSHD skal være det første fejlfinding trin, du tager.

I følgende eksempel nulstiller SSHD på en VM med navnet `myVM` i ressourcegruppen med navnet `myResourceGroup`. Bruge dit eget VM og ressource gruppenavne på følgende måde:

```bash
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Nulstille SSH legitimationsoplysninger for en bruger
Hvis SSHD ser ud til at fungere korrekt, kan du nulstille adgangskoden for en bruger, giver. I følgende eksempel nulstiller legitimationsoplysninger for `myUsername` til den værdi, der er angivet i `myPassword`, på VM med navnet `myVM` i `myResourceGroup`. Bruge dit eget værdier på følgende måde:

```bash
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Hvis bruger SSH vigtige godkendelse, kan du nulstille SSH nøglen for en given bruger. I følgende eksempel opdateres den SSH nøgle, der er gemt i `~/.ssh/azure_id_rsa.pub` for den bruger, der hedder `myUsername`, på VM med navnet `myVM` i `myResourceGroup`. Bruge dit eget værdier på følgende måde:

```bash
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-file ~/.ssh/azure_id_rsa.pub
```


## <a name="using-the-vmaccess-extension"></a>Ved hjælp af filtypenavnet VMAccess
Filtypenavnet VM adgang til Linux læser i en json-fil, der definerer handlinger til at udføre. Disse handlinger omfatter nulstilling af SSHD, hvis du nulstiller en SSH nøgle eller at tilføje en bruger. Du kan stadig bruge Azure CLI til at ringe til filtypenavnet VMAccess, men du kan genbruge json-filer på tværs af flere FOS, hvis du ønsker. Denne metode kan du oprette et lager over json-filer, der kan kaldes for den angivne scenarier.

### <a name="reset-sshd"></a>Nulstil SSHD
Oprette en fil med navnet `PrivateConf.json` med følgende indhold:

```bash
{  
    "reset_ssh":"True"
}
```

Bruger Azure CLI, skal du derefter ringe på `VMAccessForLinux` lokalnummer at nulstille din SSHD forbindelse ved at angive din json-fil. I følgende eksempel nulstiller SSHD på VM med navnet `myVM` i `myResourceGroup`. Bruge dit eget værdier på følgende måde:

```bash
azure vm extension set myResourceGroup myVM \
    VMAccessForLinux Microsoft.OSTCExtensions "1.2" \
    --private-config-path PrivateConf.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Nulstille SSH legitimationsoplysninger for en bruger
Hvis SSHD ser ud til at fungere korrekt, kan du nulstille legitimationsoplysninger for en bruger, giver. For at nulstille adgangskoden for en bruger, skal du oprette en fil med navnet `PrivateConf.json`. I følgende eksempel nulstiller legitimationsoplysninger for `myUsername` til den værdi, der er angivet i `myPassword`. Skriv følgende linjer til din `PrivateConf.json` fil, ved hjælp af din egen værdier:

```bash
{
    "username":"myUsername", "password":"myPassword"
}
```

Eller hvis du vil nulstille tasten SSH for en bruger, skal du først oprette en fil med navnet `PrivateConf.json`. I følgende eksempel nulstiller legitimationsoplysninger for `myUsername` til den værdi, der er angivet i `myPassword`, på VM med navnet `myVM` i `myResourceGroup`. Skriv følgende linjer til din `PrivateConf.json` fil, ved hjælp af din egen værdier:

```bash
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

Når du har oprettet din json-fil, kan du bruge Azure CLI til at ringe på `VMAccessForLinux` lokalnummer at nulstille din SSH brugerlegitimationsoplysninger ved at angive din json-fil. I følgende eksempel nulstiller legitimationsoplysninger på VM med navnet `myVM` i `myResourceGroup`. Bruge dit eget værdier på følgende måde:

```
azure vm extension set myResourceGroup myVM \
    VMAccessForLinux Microsoft.OSTCExtensions "1.2" \
    --private-config-path PrivateConf.json
```


## <a name="restart-a-vm"></a>Genstarte en VM
Hvis du har nulstille SSH konfiguration og bruger legitimationsoplysninger, eller der opstod en fejl i at gøre dette, kan du prøve at genstarte VM til adresse underliggende Beregn problemer.

### <a name="azure-portal"></a>Azure-portalen
Vælg din VM for at genstarte en VM ved hjælp af portalen Azure, og klik på den ***Genstart** knappen som i følgende eksempel:

![Genstarte en VM i portalen Azure](./media/virtual-machines-linux-troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
I følgende eksempel genstarter VM med navnet `myVM` i ressourcegruppen med navnet `myResourceGroup`. Bruge dit eget værdier på følgende måde:

```bash
azure vm restart --resource-group myResourceGroup --name myVM
```


## <a name="redeploy-a-vm"></a>Geninstaller en VM
Du kan geninstallere en VM til en anden node i Azure, som kan rette problemer med underliggende netværk. Finde oplysninger om geninstallation af en VM, [Geninstaller virtuelt til nye Azure node](virtual-machines-windows-redeploy-to-new-node.md).

> [AZURE.NOTE] Når handlingen er fuldført, mistes kortvarige diskdata og dynamiske IP-adresser, der er knyttet til den virtuelle maskine opdateres.

### <a name="azure-portal"></a>Azure-portalen
Hvis du vil geninstallere en VM ved hjælp af portalen Azure, Vælg din VM, og Rul ned til sektionen **Support + fejlfinding** . Klik på knappen **Geninstaller** som i følgende eksempel:

![Geninstaller en VM i portalen Azure](./media/virtual-machines-linux-troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
I følgende eksempel redeploys VM med navnet `myVM` i ressourcegruppen med navnet `myResourceGroup`. Bruge dit eget værdier på følgende måde:

```bash
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>VM'er, der er oprettet ved hjælp af implementeringsmodel klassisk

Prøv disse trin for at løse de mest almindelige SSH internetforbindelse, fejl for VM'er, der blev oprettet ved hjælp af den klassiske implementeringsmodel. Efter hvert trin skal du prøve at genoprette forbindelse til VM.

- Nulstil fjernadgang fra [Azure-portalen](https://portal.azure.com). Vælg din VM på Azure-portalen, og klik på knappen **Nulstil Remote …** .

- Genstart VM. Vælg din VM [Azure-portalen](https://portal.azure.com), og klik på knappen **Genstart** .

    -ELLER-

    Vælg **virtuelle maskiner**i [Azure klassisk portal](https://manage.windowsazure.com), > **forekomster** > **Genstart**.

- Geninstaller VM til en ny Azure node. Finde oplysninger om hvordan du kan geninstallere en VM, [Geninstaller virtuelt til nye Azure node](virtual-machines-windows-redeploy-to-new-node.md).

    Når handlingen er fuldført, mistes kortvarige diskdata og dynamiske IP-adresser, der er knyttet til den virtuelle maskine opdateres.

- Følg vejledningen i, [hvordan du nulstiller en adgangskode eller SSH for Linux-baserede virtuelle maskiner](virtual-machines-linux-classic-reset-access.md) til:
    - Nulstil adgangskode eller SSH nede.
    - Oprette en _sudo_ brugerkonto.
    - Nulstil SSH konfigurationen.

- Markér den VM ressource sundhed vedrørende spørgsmål om platform.<br>
  Vælg din VM, og Rul ned **Indstillinger** > **Kontrollere tilstand**.


## <a name="additional-resources"></a>Yderligere ressourcer

- Hvis du stadig ikke kan SSH til din VM når du har fulgt trinene efter, kan du se [mere detaljerede fejlfindingstrin](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md) Gennemse yderligere trin for at løse problemet.

- Du kan finde flere oplysninger om fejlfinding af adgang til programmer, se [fejlfinding af adgang til et program, der kører på en Azure virtuel maskine](virtual-machines-linux-troubleshoot-app-connection.md)

- Se, [hvordan du nulstiller en adgangskode eller SSH for Linux-baserede virtuelle maskiner](virtual-machines-linux-classic-reset-access.md)kan finde flere oplysninger om fejlfinding i forbindelse med virtuelle maskiner, der er oprettet ved hjælp af den klassiske implementeringsmodel.
