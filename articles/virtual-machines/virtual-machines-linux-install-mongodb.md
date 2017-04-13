<properties
   pageTitle="Installere MongoDB på en Linux VM | Microsoft Azure"
   description="Lær, hvordan du installerer og konfigurerer MongoDB på en Linux virtuel maskine i Azure ved hjælp af Ressourcestyring implementeringsmodel."
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
   ms.date="09/29/2016"
   ms.author="iainfou"/>

# <a name="install-and-configure-mongodb-on-a-linux-vm-in-azure"></a>Installere og konfigurere MongoDB på en Linux VM i Azure
[MongoDB](http://www.mongodb.org) er en populære open source-, høj ydeevne NoSQL database. I denne artikel beskrives, hvordan du installerer og konfigurerer MongoDB på en Linux VM i Azure ved hjælp af Ressourcestyring implementeringsmodel. Eksempler vises oplysninger om hvordan til:

- [Installere og konfigurere en grundlæggende MongoDB forekomst manuelt](#manually-install-and-configure-mongodb-on-a-vm)
- [Oprette en grundlæggende MongoDB forekomst ved hjælp af en ressourcestyring skabelon](#create-basic-mongodb-instance-on-centos-using-a-template)
- [Oprette en kompleks MongoDB delt klynge med replika angiver ved hjælp af en ressourcestyring skabelon](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="prerequisites"></a>Forudsætninger
I denne artikel kræver følgende:

- en Azure-konto ([hente en gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/)).
- [Azure CLI](../xplat-cli-install.md) logget på med`azure login`
- den Azure CLI *skal være* i Azure ressourcestyring tilstand ved hjælp af`azure config mode arm`


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Installere og konfigurere MongoDB på en VM manuelt
MongoDB [giver installationsvejledning](https://docs.mongodb.com/manual/administration/install-on-linux/) til Linux distros herunder Red Hat / CentOS, SUSE, Ubuntu og Debian. I følgende eksempel oprettes en `CoreOS` VM ved hjælp af en SSH nøgle, der er gemt på `.ssh/azure_id_rsa.pub`. Besvare instruktionerne til kontonavn lager, DNS-navn og legitimationsoplysninger:

```bash
azure vm quick-create --ssh-publickey-file .ssh/azure_id_rsa.pub --image-urn CentOS
```

Log på ved hjælp af den offentlige IP-adresse, der vises i slutningen af det foregående trin i oprettelsen af VM VM:

```bash
ssh ops@40.78.23.145
```

Hvis du vil tilføje installationsressourcer for MongoDB, skal du oprette en `yum` lager fil på følgende måde:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.2.repo
```

Åbn filen MongoDB repo til redigering. Tilføj følgende linjer:

```bash
[mongodb-org-3.2]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.2/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.2.asc
```

Installere MongoDB ved hjælp af `yum` på følgende måde:

```bash
sudo yum install -y mongodb-org
```

Som standard gennemtvinges SELinux på CentOS billeder, der forhindrer dig i at få adgang til MongoDB. Installere værktøjer til administration af politik og konfigurere SELinux for at tillade MongoDB til at betjene på dens standard TCP-port 27017 på følgende måde. 

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Starte tjenesten MongoDB på følgende måde:

```bash
sudo service mongod start
```

Kontrollere MongoDB installationen ved at oprette forbindelse ved hjælp af lokalt `mongo` klient:

```bash
mongo
```

Nu skal du teste forekomsten MongoDB ved at tilføje nogle data og derefter søge:

```
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

Hvis du vil, konfigurere MongoDB til at starte automatisk under genstarte systemet:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Opret en grundlæggende MongoDB forekomst på CentOS ved hjælp af en skabelon
Du kan oprette en grundlæggende MongoDB forekomst på en enkelt CentOS VM ved hjælp af skabelonen følgende Azure Hurtig start fra Github. Denne skabelon bruges i brugerdefineret Script-udvidelse til Linux til at tilføje en `yum` lager til nyoprettede CentOS VM og derefter installere MongoDB.

- [Grundlæggende MongoDB forekomst på CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

I følgende eksempel oprettes en ressourcegruppe med navnet `myResourceGroup` i den `WestUS` område. Skriv din egen værdier som følger:

```bash
azure group create --name myResourceGroup --location WestUS \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

> [AZURE.NOTE] Azure CLI vender tilbage til en meddelelse om inden for et par sekunder for at oprette installationen, men installation og konfiguration tager et par minutter at gennemføre. Kontrollere status for installation med `azure group deployment show myResourceGroup`, angive navnet på din ressourcegruppe i overensstemmelse hermed. Vent, indtil den `ProvisioningState` viser 'Lykkedes' før du forsøger at SSH til VM.

Når installationen er fuldført, SSH til VM. Hente IP-adressen på dit VM ved hjælp af den `azure vm show` kommando som i følgende eksempel:

```bash
azure vm show --resource-group myResourceGroup --name myVM
```

I slutningen af outputtet, den `Public IP address` vises. SSH til din VM med din VM IP-adresse:

```bash
ssh ops@138.91.149.74
```

Kontrollere MongoDB installationen ved at oprette forbindelse ved hjælp af lokalt `mongo` klient på følgende måde:

```bash
mongo
```

Nu skal du teste forekomsten ved at tilføje nogle data og søge på følgende måde:

```
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Oprette en kompleks MongoDB delt klynge på CentOS ved hjælp af en skabelon
Du kan oprette en kompleks delt MongoDB-klynge ved hjælp af skabelonen følgende Azure Hurtig start fra Github. Denne skabelon følger [MongoDB delt klynge bedste fremgangsmåder](https://docs.mongodb.com/manual/core/sharded-cluster-components/) for at give redundans og høj tilgængelighed. Skabelonen opretter to shards med tre noder i hvert replikasæt. Én config server replikasæt med tre noder oprettes også plus to `mongos` router servere for at muliggøre konsistens til programmer fra på tværs af shards.

- [MongoDB Sharding klynge på CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [AZURE.WARNING] Implementere denne komplekse MongoDB delt klynge kræver mere end 20 kerner, hvilket er typisk standard core antallet per område til et abonnement. Åbn en Azure supportanmodning at øge din core Tæl.

I følgende eksempel oprettes en ressourcegruppe med navnet `myResourceGroup` i den `WestUS` område. Skriv din egen værdier som følger:

```bash
azure group create --name myResourceGroup --location WestUS \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json
```

> [AZURE.NOTE] Azure CLI vender tilbage til en meddelelse om inden for et par sekunder for at oprette installationen, men installation og konfiguration kan tage mere end en time at fuldføre. Kontrollere status for installation med `azure group deployment show myResourceGroup`, justere navnet på din ressourcegruppe tilsvarende. Vent, indtil den `ProvisioningState` viser 'Lykkedes' før der oprettes forbindelse til FOS.


## <a name="next-steps"></a>Næste trin
I disse eksempler forbindelse du til MongoDB forekomsten lokalt fra VM. Hvis du vil oprette forbindelse til MongoDB forekomsten fra en anden VM eller netværk, kan du sikre dig den relevante [netværk sikkerhedsgruppe regler oprettes](virtual-machines-linux-nsg-quickstart.md).

Du kan finde flere oplysninger om oprettelse af brug af skabeloner, [Azure ressourcestyring oversigt](../azure-resource-manager/resource-group-overview.md).

Azure ressourcestyring skabeloner Brug filtypenavnet brugerdefineret Script til at overføre og køre scripts på dit FOS. Få mere at vide under [Brug af filtypenavnet Azure brugerdefineret Script med Linux virtuelle computere](virtual-machines-linux-extensions-customscript.md).