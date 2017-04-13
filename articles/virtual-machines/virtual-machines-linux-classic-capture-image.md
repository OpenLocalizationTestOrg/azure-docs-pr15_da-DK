<properties
    pageTitle="Hent et billede af en Linux VM | Microsoft Azure"
    description="Lær at tage et skærmbillede af en Linux-baserede Azure VM (virtual machine) oprettet med den klassiske implementeringsmodel."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="iainfou"/>


# <a name="how-to-capture-a-classic-linux-virtual-machine-as-an-image"></a>Hvordan du kan registrere en klassisk Linux virtuel maskine som et billede

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Lær, hvordan du kan [udføre disse trin, ved hjælp af Ressourcestyring modellen](virtual-machines-linux-capture-image.md).

I denne artikel beskrives, hvordan til at registrere en klassisk Azure virtuelt kører Linux som et billede til at oprette andre virtuelle maskiner. Dette billede indeholder OS disken og datadisce, der er knyttet til den virtuelle maskine. Det omfatter ikke netværk konfiguration, så du skal konfigurere, når du opretter på andre virtuelle maskiner fra billedet.

Azure gemmer billedet under **billeder**, sammen med nogen billeder, du har overført. Du kan finde flere oplysninger om billeder, [Om virtuelt billeder i Azure] [].

## <a name="before-you-begin"></a>Inden du går i gang

Disse trin antager, at du allerede har oprettet en Azure virtuelt ved hjælp af implementeringsmodel klassisk og konfigureret det operativsystem, herunder vedhæfte en hvilken som helst datadisce. Hvis du vil oprette en VM, skal du læse [hvordan du opretter en virtuel maskine Linux] [].


## <a name="capture-the-virtual-machine"></a>Registrere den virtuelle maskine

1. [Opret forbindelse til den virtuelle maskine](virtual-machines-linux-mac-create-ssh-keys.md) ved hjælp af en SSH klient efter eget valg.

2. Skriv følgende kommando i vinduet SSH. Output fra `waagent` kan variere en smule, afhængigt af versionen af denne funktion:

    `sudo waagent -deprovision+user`

    Denne kommando forsøger at rydde op i systemet, så det passer til reprovisioning. Denne handling udfører følgende opgaver:

    - Fjerner SSH host nøgler (hvis Provisioning.RegenerateSshHostKeyPair er 'y' i konfigurationsfilen)
    - Rydder konfiguration af nameserver i /etc/resolv.conf
    - Fjerner den `root` brugeradgangskode fra/osv/skygge (hvis Provisioning.DeleteRootPassword er 'y' i konfigurationsfilen)
    - Fjerner cachelagret DHCP-klient rettigheder
    - Nulstiller værtsnavn til localhost.localdomain
    - Sletter den sidste klargjort bruger kontoen (der fås fra /var/lib/waagent) **og tilknyttede data**.

    >[AZURE.NOTE] Ophævelse af klargøring sletter filer og data til "generalize" billedet. Kun Kør denne kommando på en virtuel maskine, som du vil registrere som en ny skabelon til billede. Det garanterer ikke, at billedet ikke er markeret med alle følsomme oplysninger eller er egnet til videredistribution til tredjeparter.


3. Skriv **y** for at fortsætte. Du kan tilføje de `-force` parameter for at undgå dette bekræftelsestrin.

4. Skriv **Afslut** for at lukke SSH klienten.

    >[AZURE.NOTE] De resterende trin forudsætter, at du allerede har [installeret Azure CLI](../xplat-cli-install.md) på klientcomputeren. Alle de følgende trin kan også udføres på [Azure klassisk portal] [].

5. Åbn Azure CLI og logge på dit Azure abonnement fra din computer. Læs [Opret forbindelse til et Azure-abonnement fra Azure CLI](../xplat-cli-connect.md)få mere at vide.

6. Sørg for, at du er i Service Management tilstand:

    `azure config mode asm`

7. Lukke den virtuelle maskine, der allerede er fjernet i forrige trin med:

    `azure vm shutdown <your-virtual-machine-name>`

    >[AZURE.NOTE] Du kan finde ud af alle de virtuelle-computere, der er oprettet i dit abonnement ved hjælp af`azure vm list`

8. Når den virtuelle maskine stoppes, tage et skærmbillede med kommandoen:

    `azure vm capture -t <your-virtual-machine-name> <new-image-name>`

    Skriv det ønskede i stedet for _nye-billede-navn_billede navn. Denne kommando opretter en generalized OS billede. Den `-t` underkommando sletter den oprindelige virtuelle maskine.

9.  Det nye billede er nu tilgængelig på listen over billeder, der kan bruges til at konfigurere en hvilken som helst nye virtuelle maskiner. Du kan se den med kommandoen:

    `azure vm image list`

    [Azure klassisk portal] []vises det på listen **billeder** .

    ![Billede af capture vellykket](./media/virtual-machines-linux-classic-capture-image/VMCapturedImageAvailable.png)


## <a name="next-steps"></a>Næste trin
Billedet er klar til at bruges til at oprette virtuelle computere. Du kan bruge kommandoen Azure CLI `azure vm create` og Tilføj navnet på den billede, du har oprettet. Under [Brug af Azure CLI med klassisk implementeringsmodel](../virtual-machines-command-line-tools.md) få mere at vide om kommandoen. Du kan også bruge [Azure klassisk portal] [] til at oprette en brugerdefineret virtuel maskine ved hjælp af metoden **Fra galleriet** og vælge det billede, du har oprettet. Se, [hvordan du opretter en brugerdefineret virtuel maskine] [] få mere at vide.

**Se også:** [Brugervejledning til Azure Linux Agent](virtual-machines-linux-agent-user-guide.md)

[Azure klassisk portal]: http://manage.windowsazure.com
[Om virtuelt billeder i Azure]: virtual-machines-linux-classic-about-images.md
[Sådan oprettes en brugerdefineret virtuel maskine]: virtual-machines-linux-classic-create-custom.md
[How to Attach a Data Disk to a Virtual Machine]: virtual-machines-windows-classic-attach-disk.md
[Sådan oprettes en Linux virtuel maskine]: virtual-machines-linux-classic-create-custom.md
