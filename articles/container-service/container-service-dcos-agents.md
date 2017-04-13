<properties
   pageTitle="Offentlige og Private DC/OS Agent grupper ACS | Microsoft Azure"
   description="Sådan fungerer agent for offentlige og private grupper med en Azure objektbeholder tjenesten klynge."
   services="container-service"
   documentationCenter=""
   authors="Thraka"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, objektbeholdere, Micro-tjenester, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="timlt"/>

# <a name="dcos-agent-pools-for-azure-container-service"></a>DC/OS Agent grupper til Azure objektbeholder tjenesten

DC/OS Azure objektbeholder tjenesten opdeler supportmedarbejdere i offentlige og private grupper. En installation, der er foretaget til enten puljen, som påvirker hjælp til handicappede mellem maskiner i objektbeholder tjenesten. Maskiner kan være tilgængelig på internettet (offentlig) eller holdes interne (privat). I denne artikel giver en kort oversigt over, hvorfor der er en offentlige og private gruppe.

### <a name="private-agents"></a>Privat supportmedarbejdere

Privat agent noder køre via et netværk, der ikke understøtter routing. Dette netværk er kun tilgængelig, fra zonen administrator eller via offentlige zone kant router. Som standard åbner DC/OS apps på private agent noder. Se [DC/OS dokumentation](https://dcos.io/docs/1.7/administration/securing-your-cluster/) kan finde flere oplysninger om netværkssikkerhed.

### <a name="public-agents"></a>Offentlige supportmedarbejdere

Offentlige agent noder køre DC/OS apps og tjenester via et offentligt tilgængelige netværk. Se [DC/OS dokumentation](https://dcos.io/docs/1.7/administration/securing-your-cluster/) kan finde flere oplysninger om netværkssikkerhed.

## <a name="using-agent-pools"></a>Ved hjælp af en agent for grupper

Som standard installerer **Marathon** et nyt program til noderne *privat* agent. Du skal eksplicit installere programmet til den *offentlige* node under oprettelse af programmet. Vælg fanen **valgfrit** , og angiv **slave_public** for værdien **Accepteret ressource roller** . Denne proces er beskrevet [her](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) og i dokumentationen til [DC\OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/) .

## <a name="next-steps"></a>Næste trin

Læs flere oplysninger om [Administration af din DC/OS beholdere](container-service-mesos-marathon-ui.md).

Få mere at vide om at [åbne firewall](container-service-enable-public-access.md) fra Azure tillade offentlig adgang til din DC/OS objektbeholder.