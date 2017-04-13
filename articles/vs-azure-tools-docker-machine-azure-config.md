<properties
   pageTitle="Oprette Docker hosts i Azure med Docker maskine | Microsoft Azure"
   description="I denne artikel beskrives brugen af Docker computer til at oprette docker hosts i Azure."
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="mlearned" />

# <a name="create-docker-hosts-in-azure-with-docker-machine"></a>Oprette Docker Hosts i Azure med Docker maskine

Kører [Docker](https://www.docker.com/) beholdere kræver en vært VM kører docker daemon.
Dette emne beskrives, hvordan du kan bruge kommandoen [docker-computer](https://docs.docker.com/machine/) til at oprette nye Linux FOS konfigureret med Docker daemonen, kører i Azure. 

**Bemærk!** 
- *I denne artikel, afhænger af docker maskine version 0.7.0 eller nyere*
- *Windows beholdere vil være understøttet gennem docker maskine i nær fremtid*

## <a name="create-vms-with-docker-machine"></a>Oprette FOS med Docker maskine

Oprette docker host FOS i Azure med den `docker-machine create` kommandoen ved hjælp af den `azure` driver. 

Azure-driver skal dit abonnement-ID. Du kan bruge [Azure CLI](xplat-cli-install.md) eller [Azure Portal](https://portal.azure.com) til at hente abonnementet Azure. 

**Ved hjælp af portalen Azure**
- Vælg abonnementer fra siden venstre navigationsrude, og Kopiér til abonnement-id.

**Brug af Azure CLI**
- Skriv ```azure account list``` og kopiere abonnement-id.

Skriv `docker-machine create --driver azure` at se indstillingerne og deres standardværdier.
Du kan også se [Docker Azure Driver dokumentation](https://docs.docker.com/machine/drivers/azure/) for at få flere oplysninger. 

I følgende eksempel er afhængig af standardværdierne, men det du kan også åbne port 80 på VM adgang til internettet. 

```
docker-machine create -d azure --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> --azure-open-port 80 mydockerhost
```

## <a name="choose-a-docker-host-with-docker-machine"></a>Vælg en docker vært med docker maskine
Når du har en indtastning i docker maskine for din udbyder, kan du angive standardværten, når du kører docker kommandoer.
##<a name="using-powershell"></a>Ved hjælp af PowerShell

```powershell
docker-machine env MyDockerHost | Invoke-Expression 
```

##<a name="using-bash"></a>Brug af fest

```bash
eval $(docker-machine env MyDockerHost)
```

Du kan nu kører docker kommandoer i forhold til den angivne vært

```
docker ps
docker info
```

## <a name="run-a-container"></a>Køre en objektbeholder

Med en vært, der er konfigureret, kan du nu køre en simpel webserver at teste, om din vært blev konfigureret korrekt.
Her vi bruge et standard nginx billede, skal du angive, at den skal lytter på port 80 og, hvis værten VM genstarter, vil objektbeholderen genstart samt (`--restart=always`). 

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

Og Kontrollér objektbeholderen, der kører, Skriv `docker-machine ip <VM name>` til at finde IP-adressen til at angive i browseren:

```
PS C:\> docker-machine ip MyDockerHost
191.237.46.90
```

![Igangværende ngnix objektbeholder](./media/vs-azure-tools-docker-machine-azure-config/nginxsuccess.png)

##<a name="summary"></a>Oversigt
Med docker computer kan du nemt klargøre docker hosts i Azure for din individuelle docker host valideringer.
For fremstilling vært af objektbeholdere, skal du se [Azure objektbeholder tjenesten](http://aka.ms/AzureContainerService)

Hvis du vil udvikle .NET Core programmer med Visual Studio, se [Docker værktøjerne til Visual Studio](http://aka.ms/DockerToolsForVS)
