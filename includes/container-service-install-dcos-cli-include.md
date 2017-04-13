<properties
   pageTitle="Installere DC/OS CLI | Microsoft Azure"
   description="Installere DC/OS CLI."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Beholdere, Micro-tjenesterne, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/10/2016"
   ms.author="rogardle"/>

>[AZURE.NOTE] Dette er til at arbejde med DC/OS-baserede ACS klynger. Der er ikke nødvendigt at gøre dette for sværme-baserede ACS klynger.

Først skal [oprette forbindelse til din DC/OS-baserede ACS klynge](../articles/container-service/container-service-connect.md). Når du har gjort det, kan du installere DC/OS CLI på klientcomputeren med kommandoerne nedenfor:

```bash
sudo pip install virtualenv
mkdir dcos && cd dcos
wget https://raw.githubusercontent.com/mesosphere/dcos-cli/master/bin/install/install-optout-dcos-cli.sh
chmod +x install-optout-dcos-cli.sh
./install-optout-dcos-cli.sh . http://localhost --add-path yes
```

Hvis du bruger en ældre version af Python, kan du se nogle "InsecurePlatformWarnings". Du kan ignorere disse.

For at komme i gang uden at genstarte din shell skal du køre:

```bash
source ~/.bashrc
```

Dette trin kan ikke det er nødvendigt, når du starter nye er kommet.

Du kan bekræfte, at CLI er installeret:

```bash
dcos --help
```