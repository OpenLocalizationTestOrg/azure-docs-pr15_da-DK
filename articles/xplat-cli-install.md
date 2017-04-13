<properties
    pageTitle="Installer Azure kommandolinjen | Microsoft Azure"
    description="Installere på Azure kommandolinjen (CLI) til Mac, Linux og Windows at begynde at bruge Azure tjenester"
    editor=""
    manager="timlt"
    documentationCenter=""
    authors="squillace"
    services="virtual-machines-linux,virtual-network,storage,azure-resource-manager"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="command-line-interface"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="rasquill"/>
    
# <a name="install-the-azure-cli"></a>Installer Azure CLI

> [AZURE.SELECTOR]
- [PowerShell](powershell-install-configure.md)
- [Azure CLI](xplat-cli-install.md)

Hurtigt installere Azure kommandolinjen (Azure CLI) for at bruge et sæt open source-shell-baserede kommandoer til oprettelse og administration af ressourcer i Microsoft Azure. Du har flere muligheder for at installere værktøjerne på tværs af platforme på din computer: 

* **npm-pakke** – Kør npm (pakke manager JavaScript) for at installere den nyeste Azure CLI pakke på din Linux fordeling eller OS. Kræver node.js og npm på din computer.
* **Installer** - Hent en installer til nem installation på Mac eller Windows.
* **Docker objektbeholder** - begynde at bruge den nyeste CLI i en klar og kør Docker beholder. Kræver Docker host på din computer.
    
Se project-lager på [GitHub](https://github.com/azure/azure-xplat-cli)til flere indstillinger og baggrund. 

Når Azure CLI er installeret, [Opret forbindelse mellem softwaren med abonnementet Azure](xplat-cli-connect.md) og køre kommandoerne **azure** fra din kommandolinjen (fest, Terminal, kommandoprompt og osv.) for at arbejde med dine Azure ressourcer.



## <a name="option-1-install-an-npm-package"></a>Mulighed 1: Installere en npm-pakke

Kontrollér, at du har downloadet og installeret de [seneste Node.js og npm](https://nodejs.org/en/download/package-manager/)for at installere CLI fra en npm-pakke. Derefter skal du køre **npm installere** for at installere pakken azure-cli: 

    npm install -g azure-cli

På Linux salgsdistributioner, kan det være nødvendigt at bruge **sudo** til at køre kommandoen __npm__ på følgende måde:

    sudo npm install -g azure-cli

> [AZURE.NOTE]Hvis du har brug at installere eller opdatere Node.js og npm på din Linux fordeling eller OS, anbefaler vi, at du installerer den nyeste version af Node.js LTS (4.x). Hvis du bruger en ældre version, kan du få-installationsfejl. 

Hvis du foretrækker, kan du hente den seneste Linux [tjære fil] [ linux-installer] til npm pakken lokalt. Installer derefter pakken hentede npm på følgende måde (på Linux fordelingerne det være nødvendigt at bruge **sudo**):

    npm install -g <path to downloaded tar file>

## <a name="option-2-use-an-installer"></a>Mulighed 2: Brug et installationsprogram

Hvis du bruger en Mac eller Windows-computer, kan de følgende CLI Installer hentes:

* [Mac OS X installer][mac-installer]

* [Windows MSI][windows-installer] 

>[AZURE.TIP]I Windows, kan du også hente [Web Platform Installer](https://go.microsoft.com/?linkid=9828653) for at installere CLI. Installationsprogrammet giver dig mulighed for at installere yderligere Azure SDK og kommandolinjen værktøjer, når du har installeret CLI. 


## <a name="option-3-use-a-docker-container"></a>Mulighed 3: Brug en Docker objektbeholder

Hvis du har konfigureret computeren som en [Docker](https://docs.docker.com/engine/understanding-docker/) vært, kan du køre den seneste Azure CLI i en Docker beholder. Kør følgende kommando (på Linux fordelingerne det være nødvendigt at bruge **sudo**):

```
docker run -it microsoft/azure-cli
```


## <a name="run-azure-cli-commands"></a>Køre Azure CLI-kommandoer
Når Azure CLI er installeret, skal du køre kommandoen **azure** fra kommandolinjen brugergrænsefladen (fest, Terminal, kommandoprompt og osv.). For eksempel for at køre kommandoen Hjælp, Skriv følgende:

```
azure help
```
> [AZURE.NOTE]På nogle Linux salgsdistributioner, kan du få en fejl, der ligner `/usr/bin/env: ‘node’: No such file or directory`. Denne fejl kommer fra seneste installationer af Node.js installeres på /usr/bin/nodejs. For at løse problemet ved at oprette et symbolsk link til /usr/bin/node ved at køre denne kommando:

```
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

Hvis du vil se versionen af det Azure CLI, du har installeret, skal du skrive følgende:

```
azure --version
```

Nu er du klar! Adgang til alle CLI-kommandoer til at arbejde med dine egne ressourcer, [oprette forbindelse til dit Azure abonnement fra Azure CLI](xplat-cli-connect.md).

>[AZURE.NOTE] Når du først bruge Azure CLI, får du vist en meddelelse, hvor du bliver spurgt, om du vil tillade Microsoft at indsamle oplysninger om brug. Deltagelse er frivillige. Hvis du vælger at deltage, kan du forhindre når som helst ved at køre `azure telemetry --disable`. Kør for at aktivere deltagelse når som helst, `azure telemetry --enable`.


## <a name="update-the-cli"></a>Opdatere CLI

Microsoft udgiver ofte opdaterede versioner af Azure CLI. Geninstallere CLI ved hjælp af installationsprogrammet til dit operativsystem, eller Kør seneste Docker objektbeholderen. Eller hvis du har de seneste Node.js og npm installeret, kan du opdatere ved at skrive følgende (på Linux fordelingerne det være nødvendigt at bruge **sudo**).

```
npm update -g azure-cli
```

## <a name="enable-tab-completion"></a>Aktivere fanen fuldførelse

Fanen fuldførelsen af CLI-kommandoer understøttes til Mac og Linux.

For at aktivere det i zsh skal du køre:

```
echo '. <(azure --completion)' >> .zshrc
```

For at aktivere det i fest skal du køre:

```
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.bash_profile
```


## <a name="next-steps"></a>Næste trin 

* [Opret forbindelse fra CLI til dit Azure abonnement](xplat-cli-connect.md) til at oprette og administrere Azure ressourcer.

* Hvis du vil vide mere om Azure CLI, hente kildekode, rapportere problemer eller bidrage til projektet ved at besøge [GitHub lager til Azure CLI](https://github.com/azure/azure-xplat-cli).

* Hvis du har spørgsmål om brug af Azure CLI eller Azure, skal du besøge [Azure-fora](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting).

* Hvis du vil, kan du også prøve Python-baserede [Azure CLI 2.0 Preview](https://github.com/azure/azure-cli).

[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: http://aka.ms/webpi-azure-cli
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md
