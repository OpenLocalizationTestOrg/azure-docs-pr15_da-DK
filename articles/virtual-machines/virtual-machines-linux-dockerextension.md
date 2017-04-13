<properties
   pageTitle="Ved hjælp af filtypenavnet Azure Docker VM | Microsoft Azure"
   description="Lær, hvordan du bruger filtypenavnet Docker VM for hurtigt og sikkert at implementere et Docker miljø i Azure ved hjælp af Ressourcestyring skabeloner."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/25/2016"
   ms.author="iainfou"/>

# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension"></a>Oprette et Docker miljø i Azure, der bruger filtypenavnet Docker VM
Docker er en populære objektbeholder administration og afbildning platform, som gør det muligt at arbejde hurtigere med beholdere på Linux (og Windows samt). I Azure er der forskellige måder, du kan installere Docker efter dine behov. I denne artikel fokuserer på ved hjælp af filtypenavnet Docker VM og Azure ressourcestyring skabeloner. 

Se følgende artikler kan finde flere oplysninger om de forskellige installationsmetoder, herunder brug af Docker maskine og Azure objektbeholder Services:

- Til at hurtigt udvikle prototyper en app, kan du oprette en enkelt Docker vært ved hjælp af [Docker computer](./virtual-machines-linux-docker-machine.md).
- Du kan bruge Azure Docker VM udvidelse, som understøtter også [Docker Skriv](https://docs.docker.com/compose/overview/) for at generere ensartet objektbeholder installationer for større, mere stabil miljøer. I denne artikel oplysninger, der bruger filtypenavnet Azure Docker VM.
- For at opbygge fremstilling klar, SVG-miljøer, der indeholder flere værktøjer, planlægning og administration, kan du installere en [Docker Swarm klynge på Azure objektbeholder Services](../container-service/container-service-deployment.md).


## <a name="azure-docker-vm-extension-overview"></a>Azure Docker VM lokalnummer oversigt
Filtypenavnet Azure Docker VM installerer og konfigurerer Docker daemon, Docker klient, og skriv Docker i din Linux virtual machine (VM). Ved hjælp af filtypenavnet Azure Docker VM, har du mere kontrol og funktioner end blot ved hjælp af Docker maskine eller oprette værten Docker dig selv. Disse ekstra funktioner, som [Docker skriver](https://docs.docker.com/compose/overview/), skal du filtypenavnet Azure Docker VM er tilpasset til mere robust udvikler eller fremstilling miljøer.

Azure ressourcestyring skabeloner definere hele strukturen i dit miljø. Skabeloner gør det muligt at oprette og konfigurere ressourcer som værten Docker FOS, lagring, rollebaseret adgangskontrol (RBAC) og diagnosticering. Du kan genbruge disse skabeloner for at oprette flere installationer på en ensartet måde. Du kan finde flere oplysninger om Azure ressourcestyring og skabeloner, [ressourcestyring oversigt](../azure-resource-manager/resource-group-overview.md). 


## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Installere en skabelon med filtypenavnet Azure Docker VM
Lad os bruge en eksisterende Hurtig start-skabelon til at oprette en Ubuntu VM, der bruger filtypenavnet Azure Docker VM til at installere og konfigurere værten Docker. Du kan få vist skabelonen her: [enkel installation af en Ubuntu VM med Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

Du skal bruge den [Nyeste Azure CLI](../xplat-cli-install.md) installeret og logget på ved hjælp af Ressourcestyring tilstand på følgende måde:

```
azure config mode arm
```

Installer skabelonen ved hjælp af Azure CLI, der angiver skabelonen URI. I følgende eksempel oprettes en ressourcegruppe med navnet `myResourceGroup` i den `WestUS` placering. Bruge dit eget ressource gruppenavn og placering på følgende måde:

```
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Besvar instruktionerne til at navngive kontoen lagerplads, angive et brugernavn og adgangskode, og Angiv et navn til DNS. Output ligner i følgende eksempel:

```
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Updating resource group myResourceGroup
info:    Updated resource group myResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mystorageaccount
adminUsername: ops
adminPassword: P@ssword!
dnsNameForPublicIP: mypublicip
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK

```

Funktionen Azure CLI returnerer du bliver bedt om det når kun et par sekunder, men din Docker vært er stadig, der har oprettet og konfigureret ved filtypenavnet Azure Docker VM. Det tager et par minutter at afslutte installationen. Du kan få vist oplysninger om brug af status for Docker host den `azure vm show` kommandoen.

I følgende eksempel kontrolleres status for VM med navnet `myDockerVM` (standardnavnet fra skabelon - ikke ændre dette navn) i ressourcegruppen med navnet `myResourceGroup`. Skriv navnet på den ressourcegruppe, du oprettede i det foregående trin:

```bash
azure vm show -g myResourceGroup -n myDockerVM
```

Output fra den `azure vm show` kommandoen ligner i følgende eksempel:

```
info:    Executing command vm show
+ Looking up the VM "myDockerVM"
+ Looking up the NIC "myVMNicD"
+ Looking up the public ip "myPublicIPD"
data:    Id                              :/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Compute/virtualMachines/MyDockerVM
data:    ProvisioningState               :Succeeded
data:    Name                            :MyDockerVM
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
[...]
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-D3-95
data:          Provisioning State        :Succeeded
data:          Name                      :myVMNicD
data:          Location                  :westus
data:            Public IP address       :13.91.107.235
data:            FQDN                    :mypublicip.westus.cloudapp.azure.com]
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

Nær toppen af output skal du se de `ProvisioningState` VM. Når dette viser `Succeeded`, installationen er færdig, og du kan SSH til VM.

Mod slutningen af outputtet, `FQDN` viser det fulde domænenavn for din Docker vært. Denne fulde Domænenavn er, hvad du bruger til SSH din Docker vært i de resterende trin.


## <a name="deploy-your-first-nginx-container"></a>Installere din første nginx objektbeholder
Når installationen er færdig, SSH til din nye Docker vært fra din lokale computer. Indtast din egen brugernavn og din fulde Domænenavn på følgende måde:

```bash
ssh ops@mypublicip.westus.cloudapp.azure.com
```

Når logget på værten Docker, Lad os køre en nginx beholder:

```
sudo docker run -d -p 80:80 nginx
```

Output ligner i følgende eksempel, som overføres nginx billedet og en objektbeholder i gang:

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

Kontrollere status for de beholdere, der kører på din Docker vært på følgende måde:

```
sudo docker ps
```

Output ligner i følgende eksempel, der viser, nginx objektbeholderen kører, og TCP port 80 og 443 og bliver videresendt:

```
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Åbne en webbrowser for at få vist din beholder i handling, og Angiv det fulde Domænenavn navnet på din Docker vært:

![Igangværende ngnix objektbeholder](./media/virtual-machines-linux-dockerextension/nginxrunning.png)


## <a name="azure-docker-vm-extension-template-reference"></a>Azure Docker VM lokalnummer skabelon reference
I forrige eksempel bruges en eksisterende skabelon i Hurtig start. Du kan også distribuere filtypenavnet Azure Docker VM med dine egne ressourcestyring skabeloner. Hvis du vil gøre det, skal du tilføje følgende til Ressourcestyring skabelonerne, definere den `vmName` af din VM korrekt:

```
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Du kan finde mere detaljeret gennemgang på ved hjælp af Ressourcestyring skabeloner ved at læse [Azure ressourcestyring oversigt](../azure-resource-manager/resource-group-overview.md).


## <a name="next-steps"></a>Næste trin
Du muligvis vil [konfigurere Docker daemonen TCP-port](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket), forstå [Docker sikkerhed](https://docs.docker.com/engine/security/security/), eller du kan installere beholdere ved hjælp af [Docker Skriv](https://docs.docker.com/compose/overview/). Du kan finde flere oplysninger om filtypenavnet Azure Docker VM selve, [GitHub projekt](https://github.com/Azure/azure-docker-extension/).

Læs mere om de ekstra Docker installationsindstillinger i Azure:

- [Bruge Docker Machine med Azure-driver](./virtual-machines-linux-docker-machine.md)  
- [Introduktion til Docker og Opret til at definere og køre en med flere objektbeholder program på en Azure virtuel maskine](virtual-machines-linux-docker-compose-quickstart.md).
- [Installere en Azure objektbeholder tjenesten klynge](../container-service/container-service-deployment.md)
