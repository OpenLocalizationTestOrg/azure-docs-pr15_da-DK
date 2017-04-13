<properties
   pageTitle="Docker og Opret på en virtuel maskine | Microsoft Azure"
   description="Hurtig introduktion til arbejde med Opret og Docker på Linux virtuelle maskiner i Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="iainfou"/>

# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-on-an-azure-virtual-machine"></a>Introduktion til Docker og Opret for at definere og køre et program med flere objektbeholder på en Azure virtuelt

Introduktion til brug af Docker og [Opret](http://github.com/docker/compose) til at definere og køre en kompleks program på en Linux virtuel maskine i Azure. Med Opret, kan du bruge en simpel tekstfil til at definere et program består af flere Docker beholdere. Du dreje op dit program i en enkelt kommando, som indeholder alt for at installere dit defineret miljø. 

Som et eksempel beskrives i denne artikel, hvordan til hurtigt at oprette en WordPress blog med en back-end MariaDB SQL-database på en Ubuntu VM. Du kan også bruge Opret til at oprette mere komplekse programmer.


## <a name="step-1-set-up-a-linux-vm-as-a-docker-host"></a>Trin 1: Konfigurere en Linux VM som Docker vært

Du kan bruge forskellige Azure procedurer og tilgængelige billeder eller Ressourcestyring skabeloner i Azure Marketplace til at oprette en Linux VM og konfigurere det som en Docker vært. Se for eksempel [bruge Docker VM udvidelse for at installere dit miljø](virtual-machines-linux-dockerextension.md) til hurtigt at oprette en Ubuntu VM med filtypenavnet Azure Docker VM ved hjælp af en [skabelon i Hurtig start](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

Når du bruger filtypenavnet Docker VM, din VM er automatisk konfigureret som Docker vært, og Opret allerede er installeret. Eksemplet i denne artikel viser, hvordan du bruger den [Azure kommandolinjen til Mac, Linux, og Windows](../xplat-cli-install.md) (Azure-CLI) i ressourcestyring tilstand for at oprette VM.

Kommandoen grundlæggende fra det foregående dokument opretter en ressourcegruppe med navnet `myResourceGroup` i den `West US` placering og installerer en VM med filtypenavnet Azure Docker VM installeret:

```bash
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

## <a name="step-2-verify-that-compose-is-installed"></a>Trin 2: Bekræfte, at Opret er installeret

Når installationen er fuldført, Navngiv SSH til din nye Docker vært ved hjælp af DNS du angav under installationen. Du kan bruge `azure vm show -g myDockerResourceGroup -n myDockerVM` til at få vist oplysninger om din VM, herunder DNS-navnet.

Hvis du vil kontrollere, at Opret er installeret på VM, skal du køre følgende kommando:

```bash
docker-compose --version
```

Du kan se output, der svarer til `docker-compose 1.6.2, build 4d72027`.

>[AZURE.TIP] Hvis du brugte en anden metode til at oprette en Docker vært og skal installere Opret dig selv, kan du se [Opret dokumentation](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md).


## <a name="step-3-create-a-docker-composeyml-configuration-file"></a>Trin 3: Opret en docker compose.yml konfigurationsfil

Næste du opretter en `docker-compose.yml` -fil, der er bare en konfiguration tekstfil, du definerer Docker beholdere til at køre på VM. Filen angiver billedet for at køre på hver beholder (eller det kan være et build fra en Dockerfile), det er nødvendigt miljøvariabler og afhængigheder, porte og links mellem beholdere. Du kan finde oplysninger om syntaksen yml filen, [Opret filreference](http://docs.docker.com/compose/yml/).

Oprette den `docker-compose.yml` fil på følgende måde:

```bash
touch docker-compose.yml
```

Brug din foretrukne teksteditor til at tilføje nogle data i filen. I følgende eksempel bruges den `vi` editor:

```bash
vi docker-compose.yml
```

Indsæt i følgende eksempel i din tekstfil. Denne konfiguration bruger billeder fra [DockerHub registreringsdatabasen](https://registry.hub.docker.com/_/wordpress/) til at installere WordPress (Åbn kilde blogge og indhold management system) og en sammenkædet back-end MariaDB SQL-database. Angive dine egne `MYSQL_ROOT_PASSWORD` på følgende måde:

```bash
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="step-4-start-the-containers-with-compose"></a>Trin 4: Begynd beholdere med Opret

I den samme mappe som din `docker-compose.yml` fil skal du køre følgende kommando (afhængigt af dit miljø, skal du køre `docker-compose` ved hjælp af `sudo`.):

```bash
docker-compose up -d

```

Denne kommando starter de Docker beholdere, der er angivet i `docker-compose.yml`. Det kræver et minut eller to for dette trin for at fuldføre. Du får vist output, der svarer til i følgende eksempel:

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

>[AZURE.NOTE] Sørg for at bruge indstillingen **-d** på opstart, så beholderne løbende køre i baggrunden.

For at bekræfte, at objektbeholdere er op, Skriv `docker-compose ps`. Du bør se nogenlunde således:

```bash
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:80->80
ess_1              apache2-for ...                       /tcp
```

Du kan nu oprette forbindelse til WordPress direkte på VM på port 80. Åbn en webbrowser, og indtast din VM DNS-navn (såsom `http://myresourcegroup.westus.cloudapp.azure.com`). Du bør nu se WordPress startskærmen, hvor du kan gennemføre installationen og komme i gang med programmet.

![WordPress startskærmbilledet][wordpress_start]


## <a name="next-steps"></a>Næste trin

* Gå til [Docker VM lokalnummer brugervejledning](https://github.com/Azure/azure-docker-extension/blob/master/README.md) til flere muligheder for at konfigurere Docker og Opret i din Docker VM. For eksempel er en af mulighederne at sætte Opret yml filen (konverteret til JSON) direkte i konfigurationen af filtypenavnet Docker VM.
* Se [Opret kommandolinjereference](http://docs.docker.com/compose/reference/) og [Brugervejledning](http://docs.docker.com/compose/) til flere eksempler på opbygning og implementering af flere objektbeholder apps.
* Bruge en Azure ressourcestyring skabelon, enten din egen eller en bidraget fra [community](https://azure.microsoft.com/documentation/templates/), til at installere en Azure VM med Docker og et program, der er konfigureret med Opret. For eksempel bruger skabelonen [Implementer en WordPress blog med Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) Docker og Opret hurtigt skal installere WordPress med en MySQL back-end på en Ubuntu VM.
* Prøv at integrere Docker skriver med en [Docker Swarm](virtual-machines-linux-docker-swarm.md) klynge. Se [Ved hjælp af Opret med sværme](https://docs.docker.com/compose/swarm/) for scenarier.

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-linux-docker-compose-quickstart/WordPress.png
