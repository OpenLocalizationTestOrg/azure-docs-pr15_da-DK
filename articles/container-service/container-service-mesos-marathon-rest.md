<properties
   pageTitle="Azure objektbeholder tjenesten objektbeholder administration ved hjælp af REST-API | Microsoft Azure"
   description="Installere beholdere til en Azure objektbeholder tjenesten Mesos klynge ved hjælp af Marathon REST-API."
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

# <a name="container-management-through-the-rest-api"></a>Objektbeholder administration ved hjælp af REST-API

DC/OS indeholder et miljø til implementering og skalering grupperet arbejdsbelastninger, mens du abstracting den underliggende hardware. Oven på DC/OS er der en struktur, der administrerer planlægge og afholde Beregn arbejdsmængder.

Selvom strukturer er tilgængelige for mange populære arbejdsbelastninger, beskrives dette dokument, hvordan du kan oprette og tilpasse objektbeholder installationer ved hjælp af Marathon. Før du gennemgår eksemplerne skal du en DC/OS klynge, der er konfigureret i Azure objektbeholder tjenesten. Du skal også have remote connectivity til denne klynge. Du kan finde flere oplysninger om disse elementer, i følgende artikler:

- [Implementere en Azure objektbeholder tjenesten klynge](container-service-deployment.md)
- [Oprette forbindelse til en Azure objektbeholder tjenesten klynge](container-service-connect.md)

Når du har forbindelse til Azure objektbeholder tjenesten klynge, kan du få adgang til DC/OS og relaterede REST API'er via http://localhost:local-port. Eksemplerne i dette dokument forudsætter, at du tunnelføring på port 80. For eksempel Marathon slutpunktet kan ringes til `http://localhost/marathon/v2/`. Se dokumentationen til Mesosphere til [Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html) og [Chronos API](https://mesos.github.io/chronos/docs/api.html)og Apache dokumentationen til [Mesos Scheduler API](http://mesos.apache.org/documentation/latest/scheduler-http-api/)kan finde flere oplysninger om de forskellige API'er.

## <a name="gather-information-from-dcos-and-marathon"></a>Indsamle oplysninger fra DC/OS og Marathon

Før du installerer beholdere til DC/OS klynge, indsamle data om DC/OS klynge, som navne og aktuelle status for DC/OS supportmedarbejdere. Gør du ved at forespørge på `master/slaves` slutpunkt DC/OS REST-API. Hvis alt går godt, vises en liste over DC/OS supportmedarbejdere og flere egenskaber for hver.

```bash
curl http://localhost/mesos/master/slaves
```

Nu skal bruge Marathon `/apps` slutpunkt at kontrollere, om aktuelle program-installationer til DC/OS klynge. Hvis dette er en ny klynge, får du vist en tom matrix til apps.

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Installere en formateret som Docker objektbeholder

Du kan installere formateret som Docker beholdere gennem Marathon ved hjælp af en JSON-fil, der beskriver den ønskede installation. I følgende eksempel implementerer objektbeholderen Nginx binding port 80 af DC/OS agent til port 80 på objektbeholderen. Bemærk også, at egenskaben 'acceptedResourceRoles' er indstillet til 'slave_public'. Dette vil installere beholderen til en agent i sættet offentligt agent skala.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
    "acceptedResourceRoles": [
    "slave_public"
  ],
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Oprette din egen JSON-fil for at installere en objektbeholder, der er formateret som Docker, eller brug det eksempel, der er angivet i [Azure objektbeholder tjenesten demo](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json). Gemme den i en tilgængelig placering. Derefter skal du køre følgende kommando for at installere objektbeholderen. Angiv navnet på filen JSON.

```
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

Output bliver ligner følgende:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Nu, hvis du forespørger Marathon for programmer, vises dette nye program i outputtet.

```
curl localhost/marathon/v2/apps
```

## <a name="scale-your-containers"></a>Tilpasse dine objektbeholdere

Du kan også bruge Marathon API til at skalere ud eller skalere i programmet installationer. Du har installeret én forekomst af et program i det forrige eksempel. Lad os tilpasse denne op til tre forekomster af et program. Oprette en JSON-fil ved hjælp af følgende JSON tekst for at gøre det, og Gem den på en tilgængelig placering.

```json
{ "instances": 3 }
```

Kør følgende kommando for at skalere ud af programmet.

>[AZURE.NOTE] URI bliver http://localhost/marathon/v2/apps/, og klik derefter på program tilladelse til at skalere ID. Hvis du bruger eksemplet Nginx, der er angivet her, er URI http://localhost/marathon/v2/apps/nginx.

```json
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Til sidst skal forespørge Marathon slutpunkt for programmer. Du vil se, at der er nu tre af Nginx beholdere.

```
curl localhost/marathon/v2/apps
```

## <a name="use-powershell-for-this-exercise-marathon-rest-api-interaction-with-powershell"></a>Bruge PowerShell til denne opgave: Marathon REST-API interaktion med PowerShell

Du kan udføre disse samme handlinger ved hjælp af PowerShell-kommandoer på en Windows-system.

Kør følgende kommando for at indsamle oplysninger om DC/OS klynge, som agent navne og agent status.

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Du kan installere formateret som Docker beholdere gennem Marathon ved hjælp af en JSON-fil, der beskriver den ønskede installation. I følgende eksempel implementerer objektbeholderen Nginx binding port 80 af DC/OS agent til port 80 på objektbeholderen.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Oprette din egen JSON-fil, eller brug det eksempel, der er angivet i [Azure objektbeholder tjenesten demo](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json). Gemme den i en tilgængelig placering. Derefter skal du køre følgende kommando for at installere objektbeholderen. Angiv navnet på filen JSON.

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Du kan også bruge Marathon API til at skalere ud eller skalere i programmet installationer. Du har installeret én forekomst af et program i det forrige eksempel. Lad os tilpasse denne op til tre forekomster af et program. Oprette en JSON-fil ved hjælp af følgende JSON tekst for at gøre det, og Gem den på en tilgængelig placering.

```json
{ "instances": 3 }
```

Kør følgende kommando for at skalere ud af programmet.

> [AZURE.NOTE] URI bliver http://localhost/marathon/v2/apps/, og klik derefter på program tilladelse til at skalere ID. Hvis du bruger den Nginx eksempel, der er angivet her, er URI http://localhost/marathon/v2/apps/nginx.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Næste trin

- [Læs mere om Mesos HTTP slutpunkterne]( http://mesos.apache.org/documentation/latest/endpoints/).
- [Læs mere om Marathon REST-API]( https://mesosphere.github.io/marathon/docs/rest-api.html).
