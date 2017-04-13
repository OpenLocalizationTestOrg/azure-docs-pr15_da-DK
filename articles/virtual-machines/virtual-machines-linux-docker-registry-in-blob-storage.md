<properties 
  pageTitle="Implementering af din egen privat Docker registreringsdatabasen på Azure | Microsoft Azure"
  description="I denne artikel beskrives, hvordan du kan bruge Docker registreringsdatabasen til at hoste billederne objektbeholder tjenesten Azure Blob-lager."
  services="virtual-machines-linux"
  documentationCenter="virtual-machines"
  authors="ahmetalpbalkan"
  editor="squillace"
  manager="timlt"
  tags="azure-service-management,azure-resource-manager" />

<tags
  ms.service="virtual-machines-linux"
  ms.devlang="multiple"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure-services"
  ms.date="09/27/2016" 
  ms.author="ahmetb" />

# <a name="deploying-your-own-private-docker-registry-on-azure"></a>Implementering af din egen privat Docker registreringsdatabasen på Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



I dette dokument beskrives hvilke Docker privat registreringsdatabasen og viser, hvordan du kan implementere et Docker registreringsdatabasen 2.0 objektbeholder billede til en privat registreringsdatabase Docker på Microsoft Azure ved hjælp af Azure Blob-lager.

Dette dokument forudsætter:

1. Du ved, hvordan du bruger Docker og har Docker billeder til at gemme. (Du ikke? [Få mere at vide om Docker](https://www.docker.com))
2. Du har en server, der indeholder Docker-program, der er installeret. (Du ikke? [Gør det hurtigt på Azure.](https://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/))


## <a name="what-is-a-private-docker-registry"></a>Hvad er en privat Docker registreringsdatabasen?

For at kunne levere programmerne bulkvareenhed til skyen, kan du opbygge et Docker objektbeholder billede og gemme den et andet sted, så den kan bruges alene og af andre. 

Mens er nemt at oprette en objektbeholder billede og levering til skyen, er det en udfordring at gemme det oprettede billede sikkert. Derfor Docker giver et centralt service kaldet [Docker Hub] [ docker-hub] til at gemme objektbeholder billeder i skyen, og gør det muligt at oprette beholdere når som helst ved hjælp af disse billeder.

Selvom [Docker Hub] [ docker-hub] er en betalt tjeneste til lagring af din private programmet objektbeholder billeder, Docker respekterer udviklere behov og indeholder en open source-værktøjer for at gemme alle billeder i dine egne private Docker registreringsdatabasen bag en firewall eller en lokal uden at nå det offentlige Internet.
Da Azure Blob-lager er nemt at sikre, kan du hurtigt bruge det til at oprette og bruge en privat Docker registreringsdatabasen i Azure, der du bestemmer dig selv.

## <a name="why-should-you-host-a-docker-registry-on-azure"></a>Hvorfor skal du være vært for en Docker registreringsdatabasen på Azure?

Vært for din Docker registreringsdatabasen forekomst på Microsoft Azure og gemme dine billeder på Azure Blob-lager, kan du få flere fordele:

**Sikkerhed:** Billederne Docker må ikke være Azure datacentre, så de ikke cross offentlige internettet, som de ville gøre, hvis du brugte Docker Hub.
  
**Ydeevne:** Billederne Docker er gemt i det samme datacenter eller område, som dine programmer. Det betyder, at billederne, der trækkes hurtigere og mere pålideligt sammenlignet med Docker Hub.

**Pålidelighed:** Ved hjælp af Microsoft Azure Blob-lager, kan du gøre brug af mange lagerplads egenskaber som høj tilgængelighed, redundans, premium lagerplads (SSDs), og så videre.

## <a name="configuring-docker-registry-to-use-azure-blob-storage"></a>Konfigurere Docker registreringsdatabasen for at anvende Azure Blob-lager

(Det anbefales for at læse [Docker registreringsdatabasen 2.0 dokumentation][registreringsdatabasen-dokumenter] , før du fortsætter.)

Du kan [konfigurere] [ registry-config] Docker registreringsdatabasen på to forskellige måder.
Du kan:

1. Brug en `config.yml` fil. I dette tilfælde skal du oprette en separat Docker billede oven på `registry` billede.
2. Tilsidesætte standard konfigurationsfil gennem miljøvariabler: får tingene gjort uden at oprette og vedligeholde et separat Docker billede.

Af hensyn til enkel følger dette emne mulighed 2, ved hjælp af miljøvariabler.

For at køre forekomst en Docker registreringsdatabase som:

* bruger kontoen Azure-lager til lagring af billederne
* lytter på den virtuelle maskine port 5000
* har ingen godkendelse, der er konfigureret til (anbefales ikke, se nedenstående bemærkning)

skal du køre følgende kommando for Docker i din fest terminal (erstatte `<storage-account>` og `<storage-key>` med dine legitimationsoplysninger):

```sh
$ docker run -d -p 5000:5000 \
     -e REGISTRY_STORAGE=azure \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTNAME="<storage-account>" \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTKEY="<storage-key>" \
     -e REGISTRY_STORAGE_AZURE_CONTAINER="registry" \
     --name=registry \
     registry:2
```

Når kommandoen afsluttet, kan du se objektbeholderen vært for din private Docker registreringsdatabasen forekomst ved at køre den `docker ps` kommando på din udbyder:

```sh
$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                    NAMES
3698ddfebc6f        registry:2          "registry cmd/regist   2 seconds ago       Up 1 seconds        0.0.0.0:5000->5000/tcp   registry
```

> [AZURE.IMPORTANT] Konfiguration af sikkerhed for Docker registreringsdatabasen er ikke omfattet i dette dokument, og din registreringsdatabase vil være tilgængelig for alle uden godkendelse som standard, hvis du åbner op port til registreringsdatabasen port på virtuelt slutpunktet eller justering af belastning, hvis du bruger kommandoen installation.
>
> Læs [Konfigurere Docker registreringsdatabasen] [ registry-config] dokumentation for at se, hvordan du sikrer forekomsten registreringsdatabasen og dine billeder.

## <a name="next-steps"></a>Næste trin

Når du har konfigurere registreringsdatabasen, er det tid til at blive sendt bruge den nogle flere. Start med docker [registreringsdatabasen-dokumenter]. 

[docker-hub]: https://hub.docker.com/
[registry]: https://github.com/docker/distribution
[registreringsdatabasen-dokumenter]: http://docs.docker.com/registry/
[registry-config]: http://docs.docker.com/registry/configuration/
 
