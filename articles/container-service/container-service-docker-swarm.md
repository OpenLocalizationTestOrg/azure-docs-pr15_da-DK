<properties
   pageTitle="Azure objektbeholder tjenesten objektbeholder management med Docker Swarm | Microsoft Azure"
   description="Installere beholdere til en Docker Swarm i Azure objektbeholder tjenesten"
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, objektbeholdere, Micro-tjenester, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="timlt"/>

# <a name="container-management-with-docker-swarm"></a>Administration af objektbeholder med Docker Swarm

Docker sværme indeholder et miljø til implementering af bulkvareenhed arbejdsbelastninger på tværs af et samlet sæt Docker værter. Docker sværme bruger oprindelige Docker API. Arbejdsproces til styring af objektbeholdere på en Docker Swarm er næsten identisk med det ville være på en enkelt objektbeholder vært. Dette dokument indeholder enkle eksempler med at udrulle bulkvareenhed arbejdsmængder i en Azure objektbeholder tjenesten forekomst af Docker Swarm. Finde mere i dybden dokumentation på Docker Swarm i [Docker Swarm på Docker.com](https://docs.docker.com/swarm/).

Forudsætninger for at øvelserne i dette dokument:

[Oprette en sværme klynge i Azure objektbeholder tjenesten](container-service-deployment.md)

[Oprette forbindelse med sværme klynge i Azure objektbeholder tjenesten](container-service-connect.md)

## <a name="deploy-a-new-container"></a>Installere en ny objektbeholder

Hvis du vil oprette en ny objektbeholder i Docker Swarm, skal du bruge den `docker run` kommandoen (at sikre, at du har åbnet en SSH tunnel til mastere ud fra de forudsætninger ovenfor). I dette eksempel oprettes en objektbeholder fra den `yeasy/simple-web` billede:


```bash
user@ubuntu:~$ docker run -d -p 80:80 yeasy/simple-web

4298d397b9ab6f37e2d1978ef3c8c1537c938e98a8bf096ff00def2eab04bf72
```

Når beholderen er blevet oprettet, kan du bruge `docker ps` til at returnere oplysninger om objektbeholderen. Bemærk her, den sværme agent, der er vært for objektbeholderen står på listen:


```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   31 seconds ago      Up 9 seconds        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

Du kan nu få adgang til det program, der kører i denne beholder gennem offentlige DNS-navnet på den sværme agent justering af belastning. Du kan finde disse oplysninger i portalen Azure:  


![Reelle besøg resultater](media/real-visit.jpg)  

Som standard er den justering af belastning portene 80, 8080 og 443 Åbn. Hvis du vil oprette forbindelse på en anden port skal du åbne porten på den Azure justering af belastning til Agent puljen.

## <a name="deploy-multiple-containers"></a>Installere flere beholdere

Flere objektbeholdere er startet, ved at udføre 'docker Kør' flere gange, du kan bruge den `docker ps` kommando for at se som vært for beholderne, der kører. I eksemplet nedenfor er tre beholdere fordeles jævnt på tværs af de tre sværme supportmedarbejdere:  


```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
11be062ff602        yeasy/simple-web    "/bin/sh -c 'python i"   11 seconds ago      Up 10 seconds       10.0.0.6:83->80/tcp   swarm-agent-34A73819-2/clever_banach
1ff421554c50        yeasy/simple-web    "/bin/sh -c 'python i"   49 seconds ago      Up 48 seconds       10.0.0.4:82->80/tcp   swarm-agent-34A73819-0/stupefied_ride
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   2 minutes ago       Up 2 minutes        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

## <a name="deploy-containers-by-using-docker-compose"></a>Installere beholdere ved hjælp af Docker Skriv

Du kan bruge Docker Opret til at automatisere til installation og konfiguration af flere beholdere. Gør du ved at sikre dig, en Secure Shell (SSH) tunnel er blevet oprettet og, at variablen DOCKER_HOST er indstillet (se forudsætninger, der er ovenfor).

Oprette en docker compose.yml fil på det lokale system. Brug denne [eksempel](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/docker-compose.yml)til at udføre dette.

```bash
web:
  image: adtd/web:0.1
  ports:
    - "80:80"
  links:
    - rest:rest-demo-azure.marathon.mesos
rest:
  image: adtd/rest:0.1
  ports:
    - "8080:8080"

```

Køre `docker-compose up -d` til at starte objektbeholder installationer:


```bash
user@ubuntu:~/compose$ docker-compose up -d
Pulling rest (adtd/rest:0.1)...
swarm-agent-3B7093B8-0: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-3: Pulling adtd/rest:0.1... : downloaded
Creating compose_rest_1
Pulling web (adtd/web:0.1)...
swarm-agent-3B7093B8-3: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-0: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/web:0.1... : downloaded
Creating compose_web_1
```

Til sidst returneres på listen over kører beholdere. Denne liste afspejler de beholdere, der blev installeret ved hjælp af Docker Opret:


```bash
user@ubuntu:~/compose$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                     NAMES
caf185d221b7        adtd/web:0.1        "apache2-foreground"   2 minutes ago       Up About a minute   10.0.0.4:80->80/tcp       swarm-agent-3B7093B8-0/compose_web_1
040efc0ea937        adtd/rest:0.1       "catalina.sh run"      3 minutes ago       Up 2 minutes        10.0.0.4:8080->8080/tcp   swarm-agent-3B7093B8-0/compose_rest_1
```

Naturligt, kan du bruge `docker-compose ps` at undersøge de beholdere, der er defineret i din `compose.yml` fil.

## <a name="next-steps"></a>Næste trin

[Få flere oplysninger om Docker Swarm](https://docs.docker.com/swarm/)
