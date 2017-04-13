<properties
   pageTitle="Oprette forbindelse til en Azure objektbeholder tjenesten klynge | Microsoft Azure"
   description="Oprette forbindelse til en Azure objektbeholder tjenesten klynge ved hjælp af en SSH tunnel."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, objektbeholdere, Micro-tjenester, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="rogardle"/>


# <a name="connect-to-an-azure-container-service-cluster"></a>Oprette forbindelse til en Azure objektbeholder tjenesten klynge

Få vist RESTEN slutpunkter DC/OS og Docker Swarm klynger, der er installeret med Azure objektbeholder tjenesten. Disse slutpunkter er dog ikke åbne til verden udenfor. For at administrere disse slutpunkter, skal du oprette en Secure Shell (SSH) tunnel. Når du har en SSH der tunnel er oprettet, kan du køre kommandoer mod klynge slutpunkterne og se klynge Brugergrænsefladen via en browser på din egen computer. Dette dokument vejleder dig gennem oprettelse af en SSH tunnel fra Linux, OS X og Windows.

>[AZURE.NOTE] Du kan oprette en SSH session med en klynge management system. Dog anbefales ikke dette. Arbejde direkte på et administrationssystem Fremviser risikoen for af utilsigtede konfigurationsændringer.   

## <a name="create-an-ssh-tunnel-on-linux-or-os-x"></a>Oprette en SSH tunnel på Linux eller OS X

Det første, når du opretter en SSH tunnel på Linux eller OS X er at finde netværksbelastningen mastere offentlige DNS-navn. Udvid ressourcegruppen for at gøre dette, så hver ressource vises. Find og vælg den offentlige IP-adresse på master. Dette åbner op en blade, der indeholder oplysninger om den offentlige IP-adresse, som indeholder DNS-navn. Gem dette navn til senere brug. <br />


![Offentlige DNS-navn](media/pubdns.png)

Nu åbner en shell og køre følgende kommando hvor:

**PORT** er porten på det slutpunkt, du vil vise. Dette er 2375 for sværme. Til DC/OS, skal du bruge port 80.  
**Brugernavn** er navnet på den bruger, der blev angivet, da du installerede klyngen.  
**DNSPREFIX** er præfikset DNS, som du angav, da du installerede klyngen.  
**Område** er det område, hvor din ressourcegruppen er placeret.  
**PATH_TO_PRIVATE_KEY** [VALGFRIT] er stien til den private nøgle, der svarer til den offentlige nøgle, du angav, da du oprettede objektbeholder tjenesten klynge. Brug denne indstilling med -i flag.

```bash
ssh -L PORT:localhost:PORT -f -N [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com -p 2200
```
> SSH forbindelse port er 2200 – ikke standardporten 22.

## <a name="dcos-tunnel"></a>DC/OS tunnel

For at åbne en tunnel til slutpunkterne DC/OS-relaterede skal du udføre en kommando, der ligner følgende:

```bash
sudo ssh -L 80:localhost:80 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Du kan nu få adgang til DC/OS-relaterede slutpunkterne på:

- DC/OS:`http://localhost/`
- Marathon:`http://localhost/marathon`
- Mesos:`http://localhost/mesos`

På samme måde, kan du når rest API'er for hvert program gennem denne tunnel.

## <a name="swarm-tunnel"></a>Sværme tunnel

For at åbne en tunnel til sværme slutpunktet skal du udføre en kommando, der ser ud som følger:

```bash
ssh -L 2375:localhost:2375 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Du kan nu angive miljøvariablen DOCKER_HOST på følgende måde. Du kan fortsætte med at bruge din Docker kommandolinjen (CLI) som normalt.

```bash
export DOCKER_HOST=:2375
```

## <a name="create-an-ssh-tunnel-on-windows"></a>Oprette en SSH tunnel i Windows

Der findes flere indstillinger for oprettelse af SSH tunneler i Windows. Dette dokument beskrives det, hvordan du bruger trykfarver til at gøre dette.

Download trykfarver til din Windows-pc og køre programmet.

Angiv et værtsnavn, der består af klynge admin-brugernavn og den første master i klyngen offentlige DNS-navn. **Host Name** ser sådan ud: `adminuser@PublicDNS`. Angiv 2200 for **Port**.

![Trykfarver konfiguration 1](media/putty1.png)

Vælg **SSH** og **godkendelse**. Tilføj privat nøgle filen til godkendelse.

![Trykfarver konfiguration 2](media/putty2.png)

Vælg **tunneler** og Konfigurer følgende videresendt porte:
- **Kildeport:** Din præference – Brug 80 til DC/OS eller 2375 for sværme.
- **Destination:** Bruge localhost:80 til DC/OS eller localhost:2375 til sværme.

I følgende eksempel er konfigureret til DC/OS, men de ser nogenlunde for Docker Swarm.

>[AZURE.NOTE] Port 80 må ikke være i brug, når du opretter denne tunnel.

![Trykfarver konfiguration 3](media/putty3.png)

Når du er færdig, gemme den forbindelseskonfiguration og forbinde den trykfarver session. Når du opretter forbindelse, kan du se konfigurationen af porten i hændelseslog trykfarver.

![Trykfarver hændelseslog](media/putty4.png)

Når du har konfigureret tunnelen til DC/OS, kan du få adgang til relaterede slutpunktet på:

- DC/OS:`http://localhost/`
- Marathon:`http://localhost/marathon`
- Mesos:`http://localhost/mesos`

Når du har konfigureret tunnelen for Docker Swarm, kan du få adgang til sværme klynge gennem Docker CLI. Skal du først konfigurere en Windows-miljøvariablen med navnet `DOCKER_HOST` med en værdi på ` :2375`.

## <a name="next-steps"></a>Næste trin

Installere og administrere beholdere med DC/OS eller sværme:

- [Arbejde med Azure objektbeholder tjenesten og DC/OS](container-service-mesos-marathon-rest.md)
- [Arbejde med Azure objektbeholder tjenesten og Docker sværme](container-service-docker-swarm.md)
