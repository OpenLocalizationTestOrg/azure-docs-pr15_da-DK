<properties
    pageTitle="Oprette Docker hosts i Azure med Docker maskine | Microsoft Azure"
    description="I denne artikel beskrives brugen af Docker computer til at oprette docker hosts i Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="07/22/2016"
    ms.author="rasquill"/>

# <a name="use-docker-machine-with-the-azure-driver"></a>Bruge Docker Machine med Azure-driver

[Docker](https://www.docker.com/) er et af de mest populære virtualization metoder, der bruger Linux beholdere i stedet for virtuelle maskiner som en metode til at isolere programmet data og databehandling med på delte ressourcer. I dette emne beskrives hvornår og hvordan du bruger [Docker maskine](https://docs.docker.com/machine/) (den `docker-machine` kommandoen) til at oprette nye Linux FOS i Azure aktiveret som docker vært for din Linux beholdere.


## <a name="create-vms-with-docker-machine"></a>Oprette FOS med Docker maskine

Oprette docker host FOS i Azure med den `docker-machine create` kommandoen ved hjælp af den `azure` driver-argument for indstillingen driver (`-d`) og alle andre argumenter. 

I følgende eksempel er afhængig af standardværdierne, men det åbnes port 80 på VM til internettet for at teste med en nginx beholder, gør `ops` logon-brugeren til SSH, og kald den nye VM `machine`. 

Skriv `docker-machine create --driver azure` at se indstillingerne og deres standardværdier. Du kan også læse [Docker Azure Driver dokumentation](https://docs.docker.com/machine/drivers/azure/). (Bemærk, at hvis du har to-faktor godkendelse er aktiveret, du vil blive bedt om at godkende ved hjælp af den anden faktor).

```bash
docker-machine create -d azure \
  --azure-ssh-user ops \
  --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> \
  --azure-open-port 80 \
  machine
```

Output bør se nogenlunde sådan ud, afhængigt af om du har to-faktor-godkendelse, der er konfigureret på din konto.

```
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(machine) Microsoft Azure: To sign in, use a web browser to open the page https://aka.ms/devicelogin. Enter the code <code> to authenticate.
(machine) Completed machine pre-create checks.
Creating machine...
(machine) Querying existing resource group.  name="machine"
(machine) Creating resource group.  name="machine" location="eastus"
(machine) Configuring availability set.  name="docker-machine"
(machine) Configuring network security group.  name="machine-firewall" location="eastus"
(machine) Querying if virtual network already exists.  name="docker-machine-vnet" location="eastus"
(machine) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(machine) Creating public IP address.  name="machine-ip" static=false
(machine) Creating network interface.  name="machine-nic"
(machine) Creating storage account.  name="vhdsolksdjalkjlmgyg6" location="eastus"
(machine) Creating virtual machine.  name="machine" location="eastus" size="Standard_A2" username="ops" osImage="canonical:UbuntuServer:15.10:latest"
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env machine
```

## <a name="configure-your-docker-shell"></a>Konfigurere din docker shell

Du kan nu skrive `docker-machine env <VM name>` at se, hvad du skal gøre for at konfigurere shell. 

```bash
docker-machine env machine
```

Som udskriver oplysninger om miljø, som ser nogenlunde således ud. Bemærk, at IP-adressen er blevet tildelt, som skal du tester VM.

```
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://191.237.46.90:2376"
export DOCKER_CERT_PATH="/Users/rasquill/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env machine)
```

Du kan enten køre kommandoen foreslåede konfiguration, eller du kan konfigurere miljøvariabler dig selv. 

## <a name="run-a-container"></a>Køre en objektbeholder

Nu kan du køre en simpel webserver til at afprøve om alle fungerer korrekt. Her vi bruge et standard nginx billede, skal du angive, at den skal lytter på port 80 og, hvis VM genstarter skal objektbeholderen genstart samt (`--restart=always`). 

```bash
docker run -d -p 80:80 --restart=always nginx
```

Output skal se ud som følger:

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## <a name="test-the-container"></a>Teste objektbeholderen

Undersøge, der kører beholdere ved hjælp af `docker ps`:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

Og Kontrollér objektbeholderen, der kører, Skriv `docker-machine ip <VM name>` til at finde IP-adressen (Hvis du har glemt fra den `env` kommandoen):

![Igangværende ngnix objektbeholder](./media/virtual-machines-linux-docker-machine/nginxsuccess.png)

## <a name="next-steps"></a>Næste trin

Hvis du er interesseret, kan du prøve Azure [Docker VM udvidelse](virtual-machines-linux-dockerextension.md) til at gøre den samme handling med Azure CLI eller Azure ressource manager skabeloner. 

Flere eksempler på arbejde med Docker, under [arbejde med Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) fra [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 forbinde [demo](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Du kan finde flere Hurtig start-guider fra HealthClinic.biz videoen, [Azure udvikler værktøjer Hurtig start-guider](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).

