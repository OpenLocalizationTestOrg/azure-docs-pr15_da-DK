 <properties
   pageTitle="Azure og Linux | Microsoft Azure"
   description="I denne artikel beskrives Azure beregne, Storage og netværk services med Linux virtuelle maskiner."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines-linux"
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/14/2016"
   ms.author="v-livech"/>

# <a name="azure-and-linux"></a>Azure og Linux

Microsoft Azure er en voksende samling af integreret offentlige cloud services, herunder analytics, virtuelle maskiner, databaser, mobil, netværk, lagring, og web – ideel som vært for dine løsninger.  Microsoft Azure leverer en SVG databehandling platform, hvor du kan kun betale for du bruger, når du ønsker det. - uden at investere i lokalt hardware.  Azure er klar, når du er skalere dine løsninger op og ud med uanset skalering du har brug for at imødekomme behovene hos dine kunder.

Hvis du allerede kender, de forskellige funktioner i Amazon's AWS, kan du undersøge Azure vs AWS [definition tilknytning dokument](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/).


## <a name="regions"></a>Områder
Microsoft Azure ressourcerne er fordelt på tværs af flere geografiske områder i hele verden.  En "region" repræsenterer flere datacentre i et enkelt geografisk område.  Og 1 januar 2016 dette omfatter: 8 i USA, 2 i Europa, 6 i Asien/Stillehavsområdet, 2 i Kina og 3 i Indien.  Hvis du vil have en komplet liste over alle Azure områder, vedligeholde vi en liste over eksisterende og nye offentliggjort områder.

- [Azure områder](https://azure.microsoft.com/regions/)

## <a name="availability"></a>Tilgængelighed
Du skal installere to eller flere VM'er, der kører arbejdsbelastningen inde i en tilgængelighed er angivet i rækkefølge for din installation til kvalificerer til vores 99.95 VM serviceaftale. Dette sikrer din FOS er distribueret på tværs af flere fejl domæner i vores datacentre samt installeret på hosts med forskellige vedligeholdelse windows. Den fulde [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) forklares garanterede tilgængeligheden af Azure som helhed.

## <a name="azure-virtual-machines--instances"></a>Azure virtuelle maskiner og forekomster
Microsoft Azure understøtter kører en række populære Linux fordelingerne leveres og vedligeholdes med et antal partnere.  Du kan finde salgsdistributioner som rød Hat Enterprise, CentOS, Debian, Ubuntu, CoreOS, RancherOS, FreeBSD og mere i Azure Marketplace. Vi arbejder aktivt med forskellige Linux communities til at tilføje endnu flere typer på listen [Azure godkendt Linux Distros](virtual-machines-linux-endorsed-distros.md) .

Hvis din foretrukne Linux distro efter valg ikke er til stede i øjeblikket i galleriet, kan du "tage dine egne Linux" VM ved at [oprette og overføre en Linux Virtuelle i Azure](virtual-machines-linux-create-upload-generic.md).

Azure virtuelle maskiner kan du anvende en lang række computing løsninger i en fleksible måde. Du kan installere næsten alle arbejdsbelastningen og et sprog på næsten alle operativsystem – Windows, Linux, eller en brugerdefineret har oprettet en fra en af vores voksende liste over partnere. Stadig ikke kan se det, du leder efter?  Bare rolig – du kan også tage dine egne billeder fra det lokale.

## <a name="vm-sizes"></a>VM størrelser
Når du installerer en VM i Azure, vil du vælge en VM størrelse i en af vores serie af størrelser, som egner sig til din arbejdsbyrde. Størrelsen påvirker også behandling power, hukommelse og lagerplads kapaciteten på den virtuelle maskine. Du er faktureret, baseret på tidsrum, hvor VM kører og forbrug dens tildelte ressourcer. En komplet liste over [størrelser af virtuelle maskiner](virtual-machines-linux-sizes.md).

Her er nogle grundlæggende retningslinjer for at vælge en VM størrelse på en af vores serie (A, D, DS, G og GS).

* A-serien FOS er vores værdi pris basis FOS for light arbejdsmængder og Udviklingscenter/afprøve scenarier. De kan er alment tilgængelig i alle områder og oprette forbindelse og bruge alle tilgængelige virtuelle maskiner standard ressourcer.
* A-serien størrelser (A8 - A11) er speciel Beregn intensivt konfigurationer passer til high-performance computing klynge programmer.
* D-serien FOS er designet til at køre programmer, der har behov for højere Beregn power og midlertidige disk ydeevne. D-serien FOS giver hurtigere processorer, en højere hukommelse-til-core-bredde-forhold og en solid state drev (SSD) for den midlertidige disk.
* Dv2-serien, er den nyeste version af vores D-serien, funktioner en mere effektiv CPU. Dv2 serie CPU'EN er om 35% hurtigere end CPU'EN D-serien. Det er baseret på den seneste generering af 2,4 GHz Intel Xeon® E5-2673 v3 (Haskell)-processor, og med Intel Turbo Boost teknologi 2.0, kan gå op til 3,2 GHz. Dv2-serien har de samme hukommelse og diskplads konfigurationer som D-serien.
* G-serien FOS tilbyder mest hukommelse og køre på hosts med Intel Xeon E5 V3 family processorer.

Bemærk: DS-serien og GS serie FOS har adgang til Premium-lager – vores SSD sikkerhedskopier høj ydeevne og lav ventetid lagerplads på i/o-intensivt arbejdsmængder. Premium Storage findes i visse områder. Yderligere oplysninger finder du:

- [Premium opbevaring: High-performance lagerplads til Azure virtuelt arbejdsmængder](../storage/storage-premium-storage.md)

## <a name="automation"></a>Automatisering
For at opnå en stort DevOps kultur skal være alle infrastruktur kode.  Når alle infrastrukturen bor i kode den nemt kan være genoprettet (København servere).  Azure fungerer med alle de vigtigste automatisering værktøjer som Ansible, kok, SaltStack og Puppet.  Azure har også et nyt værktøj til automatisering:

- [Azure-skabeloner](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

- [Azure VMAccess](virtual-machines-linux-using-vmaccess-extension.md)

Azure ruller ud af support til [skyen initialisering](http://cloud-init.io/) på tværs af de fleste Linux Distros, der understøtter den.  Canonical's Ubuntu FOS er i øjeblikket er installeret med skyen initialisering aktiveret som standard.  RedHats RHEL, CentOS og Fedora understøtter skyen initialisering, men Azure billederne vedligeholdes af RedHat ikke har installeret skyen initialisering.  Hvis du vil bruge skyen initialisering på en RedHat familie OS, skal du oprette et brugerdefineret billede med skyen initialisering installeret.

- [Brug af skyen initialisering på Azure Linux FOS](virtual-machines-linux-using-cloud-init.md)

## <a name="quotas"></a>Kvoter
Hver Azure abonnement har standard kvote begrænsninger på plads, der kan påvirke installationen af et stort antal FOS for projektet. Den nuværende grænse på grundlag af per abonnement er 20 FOS per område.  Kan hæves kvote begrænsninger ved at arkivere en supportbilletter anmoder om en grænse stigning.  Yderligere oplysninger om kvote begrænsninger:

- [Azure-abonnementstjeneste begrænser](../azure-subscription-service-limits.md)


## <a name="partners"></a>Partnere

Microsoft arbejder tæt sammen med vores partnere at sikre, at de tilgængelige billeder er opdateret, og optimeret til en Azure runtime.  Du kan finde flere oplysninger om vores partnere deres marketplace sider nedenfor.

- [Linux på Azure-godkendt fordeling](virtual-machines-linux-endorsed-distros.md)

RedHat - [Azure Marketplace - RedHat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)

Vedtaget - [Azure Marketplace - Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)

Debian - [Azure Marketplace - Debian 8 "Jessie"](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)

FreeBSD - [Azure Marketplace - FreeBSD 10.3](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)

CoreOS - [Azure Marketplace - CoreOS (stabil)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)

RancherOS - [Azure Marketplace - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)

Bitnami - [Bitnami bibliotek til Azure](https://azure.bitnami.com/)

Mesosphere - [Azure Marketplace - Mesosphere DC/OS om Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)

Docker - [Azure Marketplace - Azure objektbeholder tjenesten med Docker sværme](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)

Jenkins - [Azure Marketplace - CloudBees Jenkins Platform](https://azure.microsoft.com/marketplace/partners/cloudbees/jenkins-platformjenkins-platform/)


## <a name="getting-setup-on-azure"></a>Modtage konfiguration på Azure
Hvis du vil begynde at bruge Azure have du en Azure-konto, den Azure CLI installeret og et par SSH offentlige og private nøgler.

## <a name="sign-up-for-an-account"></a>Tilmelde sig en konto
Det første trin i brug af Azure skyen er at tilmelde dig en Azure-konto.  Gå til siden [Azure konto tilmelding](https://azure.microsoft.com/pricing/free-trial/) at komme i gang.

## <a name="install-the-cli"></a>Installere CLI
Med din nye Azure-konto, du kan komme i gang med det samme ved hjælp af portalen Azure, som er en webbaseret administrator panel.  For at administrere Azure skyen via kommandolinjen skal du installere den `azure-cli`.  Installer [Azure CLI ](../xplat-cli-install.md)på computeren Mac eller Linux.

## <a name="create-an-ssh-key-pair"></a>Oprette en SSH vigtige par
Nu har du en Azure-konto, webportalen Azure og Azure CLI.  Næste trin er at oprette et SSH-par, der bruges til SSH til Linux uden at bruge en adgangskode.  [Oprette SSH taster på Linux- og Mac](virtual-machines-linux-mac-create-ssh-keys.md) til at aktivere adgangskode mindre logon og bedre sikkerhed.

## <a name="getting-started-with-linux-on-microsoft-azure"></a>Introduktion til Linux på Microsoft Azure
Med opsætningen Azure-konto, Azure CLI installeret og SSH taster oprettet er du nu klar til at begynde at opbygge ud af en infrastruktur i Azure skyen.  Den første opgave er at oprette et par FOS.

## <a name="create-a-vm-using-the-cli"></a>Oprette en VM ved hjælp af CLI
Oprette en Linux VM ved hjælp af CLI er en hurtig måde at installere en VM uden at forlade terminalen, du arbejder i.  Alt, hvad du kan angive på webportalen er tilgængelige via en kommandolinjen flag eller Skift.  

- [Oprette en Linux VM ved hjælp af CLI](virtual-machines-linux-quick-create-cli.md)

## <a name="create-a-vm-in-the-portal"></a>Oprette en VM i portalen
Oprette en Linux VM i webportalen Azure er en måde at nemt pege og klikke på gennem de forskellige indstillinger for at få adgang til en installation.  I stedet for at bruge kommandolinjen flag eller parametre, er du kan se et godt Weblayout af forskellige muligheder og indstillinger.  Alt, der er tilgængelige via kommandolinjen findes også i portalen.

- [Oprette en Linux VM ved hjælp af portalen](virtual-machines-linux-quick-create-portal.md)

## <a name="login-using-ssh-without-a-password"></a>Logon ved hjælp af SSH uden en adgangskode
VM kører nu på Azure, og du er klar til at logge på.  Brug af adgangskoder til at logge på via SSH er usikre og tidskrævende.  Brug af SSH taster er den sikreste måde, og den hurtigste måde at logon.  Når du opretter du Linux VM via portalen eller CLI, har du to valgmuligheder for godkendelse.  Hvis du vælger en adgangskode til SSH, konfigurerer Azure VM for at tillade logon via adgangskoder.  Hvis du vælger at bruge en offentlig nøgle SSH, Azure konfigurerer VM kun så logon via SSH nøgler og deaktiverer adgangskode logon. Hvis du vil sikre din Linux VM ved kun at tillade SSH vigtige logon, bruge SSH offentlige vigtige indstillingen under VM oprettelse i portalen eller CLI.

- [Deaktivere SSH adgangskoder på din Linux VM ved at konfigurere SSHD](virtual-machines-linux-mac-disable-ssh-password-usage.md)

## <a name="related-azure-components"></a>Relaterede Azure komponenter

## <a name="storage"></a>Lagerplads

- [Introduktion til Microsoft Azure-lager](../storage/storage-introduction.md)

- [Tilføje en disk til en Linux VM ved hjælp af azure-cli](virtual-machines-linux-add-disk.md)

- [Hvordan du vedhæfter en datadisk til en Linux VM i portalen Azure](virtual-machines-linux-attach-disk-portal.md)

## <a name="networking"></a>Netværk

- [Oversigt over virtuelt netværk](../virtual-network/virtual-networks-overview.md)

- [IP-adresser i Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md)

- [Åbning af porte til en Linux VM i Azure](virtual-machines-linux-nsg-quickstart.md)

- [Oprette et fuldt domænenavn i portalen Azure](virtual-machines-linux-portal-create-fqdn.md)


## <a name="containers"></a>Objektbeholdere

- [Virtuelle maskiner og beholdere i Azure](virtual-machines-linux-containers.md)

- [Azure objektbeholder tjenesten Introduktion](../container-service/container-service-intro.md)

- [Installere en Azure objektbeholder tjenesten klynge](../container-service/container-service-deployment.md)

## <a name="next-steps"></a>Næste trin

Du har nu en oversigt over Linux på Azure.  Det næste trin er at gå i gang og oprette et par FOS!

- [Oprette en Linux VM på Azure ved hjælp af portalen](virtual-machines-linux-quick-create-portal.md)

- [Oprette en Linux VM på Azure ved hjælp af CLI](virtual-machines-linux-quick-create-cli.md)
