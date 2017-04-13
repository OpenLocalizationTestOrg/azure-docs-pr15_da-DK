<properties
    pageTitle="Godkendt fordeling af Linux | Microsoft Azure"
    description="Få mere at vide om Linux på Azure-godkendt salgsdistributioner, herunder retningslinjer for Ubuntu, OpenLogic, Oracle og SUSE."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management,azure-resource-manager"
    />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="szark"/>



#<a name="linux-on-azure-endorsed-distributions"></a>Linux på Azure-godkendt fordeling

> [AZURE.NOTE] Hvis du har et øjeblik, Hjælp os med at forbedre Azure Linux VM dokumentationen ved at tage dette [Hurtig undersøgelse](https://aka.ms/linuxdocsurvey) oplevelser. Alle svar hjælper os Hjælp du udføre dit arbejde.

Linux billederne i Azure galleriet eller Marketplace der leveres med et antal partnere, og vi arbejder med forskellige Linux communities til at føje endnu flere typer til godkendt distributionslisten. I mellemtiden for fordelingerne er ikke tilgængelige i galleriet kan du altid Flyt-dine-ejer-Linux ved at følge vejledningen på [denne side](virtual-machines-linux-classic-create-upload-vhd.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="supported-distributions--versions"></a>Understøttede Salgsdistributioner & versioner ##

I følgende tabel vises de Linux salgsdistributioner og versioner, der understøttes på Azure. Skal du også henvise til [support til Linux billeder i Microsoft Azure](https://support.microsoft.com/en-us/kb/2941892) finde mere detaljerede oplysninger.

Linux Integration Services (LIS) driverne til Hyper-V og Azure er kernen moduler, som Microsoft bidrager direkte til før Linux kernen.  LIS driverne enten er indbygget i en fordeling kernen som standard, eller for ældre RHEL/CentOS-baserede fordelingerne er tilgængelige som en separat overførsel [her](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409).  Se [denne artikel](virtual-machines-linux-create-upload-generic.md#linux-kernel-requirements) for at få flere oplysninger om LIS driverne.

Azure Linux-Agent er allerede er forudinstalleret på Azure galleriet billederne og er tilgængelig fra en fordeling pakke lager.  Kildekode kan findes på [GitHub](https://github.com/azure/walinuxagent).

Fordeling|Version|Drivere|Agent
---|---|---|---
CentOS ved OpenLogic | CentOS 6.3 +, 7.0 + | CentOS 6.3: [LIS hente](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)<p>CentOS 6.4 +: I kernen | Pakke: I [OpenLogic repo](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) under "WALinuxAgent" <br/>Kildekode: [GitHub](https://github.com/Azure/WALinuxAgent)
[CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) | 494.4.0+ | I kernen | Kildekode: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent)
Debian | Debian 7,9 +, 8.2 + | I kernen | Pakke: I repo under "waagent" <br/>Kildekode: [GitHub](https://github.com/Azure/WALinuxAgent)
Oracle Linux | 6.4 +, 7.0 + | I kernen | Pakke: I repo under "WALinuxAgent" <br/>Kildekode: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
Red Hat Enterprise Linux | RHEL 6,7 +, 7.1 + | I kernen|Pakke: I repo under "WALinuxAgent" <br/>Kildekode: [GitHub](https://github.com/Azure/WALinuxAgent)
SUSE Linux Enterprise | SLES 11 SP4, SLES 12 SP1 + og <p> SLES for SAP 11 SP3 + | I kernen | Pakke: I [Skyen: værktøjer](https://build.opensuse.org/project/show/Cloud:Tools) repo under "python-azure-agent" <br/>Kildekode: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
openSUSE | openSUSE 13,2 + | I kernen | Pakke: I [Skyen: værktøjer](https://build.opensuse.org/project/show/Cloud:Tools) repo under "python-azure-agent" <br/>Kildekode: [GitHub](https://github.com/Azure/WALinuxAgent)
Ubuntu|Ubuntu 12.04, 14.04, 16.04, 16.10 | I kernen | Pakke: I repo under "walinuxagent" <br/>Kildekode: [GitHub](https://github.com/Azure/WALinuxAgent)


## <a name="partners"></a>Partnere

### <a name="openlogic"></a>OpenLogic
[http://www.openlogic.com/Azure](http://www.openlogic.com/azure)

OpenLogic er en foranstillet udbyder af Åbn kilde virksomhedsløsninger til skyen og datacenter. OpenLogic hjælper med at hundredvis af foranstillede enterprise på tværs af en lang række virksomheder til på en sikker måde at få fat på, understøtter og holde styr på open source-software. OpenLogic tilbyder kommercielle klasse teknisk support og refusion for 600 Åbn kilde-pakker, der understøttes af OpenLogic ekspert Community, herunder enterprise niveau understøttelse af CentOS samt der Start partner for giver CentOS-baserede billeder på Azure.

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/Running-coreos/cloud-Providers/Azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

Fra webstedet CoreOS:

*CoreOS er udviklet til sikkerhed, konsistens og pålidelighed. I stedet for installation af pakker via yum eller Hovedgaden, bruger CoreOS Linux beholdere til at administrere dine tjenester på et højere niveau af fremstilling. En enkelt tjenestekode og alle afhængigheder er pakket i en objektbeholder, der kan køres på et eller flere CoreOS computere.*


### <a name="credativ"></a>Credativ
[http://www.credativ.co.uk/credativ-blog/debian-images-Microsoft-Azure](http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ er en uafhængig konsulenttjenester og virksomheden, specialiserede i udvikling og implementeringen af professionel ved hjælp af gratis software. Som foranstillet Open Source specialister har Credative internationale anerkendelse med mange IT-afdelinger ved hjælp af deres support. Sammen med Microsoft Credativ i øjeblikket forbereder tilsvarende Debian billeder til Debian 8 (Jessie) og Debian før 7 (Wheezy), som er særligt designet til at køre på Azure og nemt kan administreres via platformen. Credativ understøtter også langtidsaftale vedligeholdelse og opdatering af Debian billederne til Azure gennem dens Åbn kilde Support Centers.

### <a name="oracle"></a>Oracle
[http://www.Oracle.com/technetwork/topics/cloud/FAQ-1963009.HTML](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracle-strategi er indeholder et bredt udvalg af løsninger til offentlige og private skyer, mens du giver kunderne valg og større fleksibilitet, hvordan de implementere Oracle software i Oracle skyer samt andre skyer.  Oracle-partnerskab med Microsoft gør det muligt for kunder til at implementere Oracle softwaren i Microsoft offentlige og private skyer uden bekymringer af dokumentation og support fra Oracle.  Oracle-anvendelsen og investeringer i Oracle offentlige og private skyen løsninger er uændret.

### <a name="red-hat"></a>Red Hat
[http://www.RedHat.com/en/partners/Strategic-Alliance/Microsoft](http://www.redhat.com/en/partners/strategic-alliance/microsoft)

Verdens foranstillet udbyder af open source-løsninger, Red Hat hjælper med mere end 90% af Fortune 500 firmaer løse business udfordringer, justere deres IT og business strategier og forberede fremtiden for teknologi. Red Hat gør dette ved at indsende sikre løsninger via en åben business model og en økonomisk, forudsigelige abonnement-model.

### <a name="suse"></a>SUSE
[http://www.SUSE.com/SUSE-Linux-Enterprise-Server-on-Azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server på Azure er en dokumenterede platform, som indeholder et overordnet pålidelighed og sikkerhed for cloud computing. SUSES alsidige Linux platform integrerer problemfrit med Azure skytjenester til at levere en nem at håndtere cloud-miljø. Og med mere end 9,200 certificeret programmer fra mere end 1,800 uafhængige softwareleverandører til SUSE Linux Enterprise Server, SUSE sikrer, at arbejdsbelastninger, som kører understøttede i datacenteret sikkert kan installeres på Azure.

### <a name="canonical"></a>Vedtaget
[http://www.Ubuntu.com/cloud/Azure](http://www.ubuntu.com/cloud/azure)

Vedtaget teknisk og åbent community styring drev Ubuntu succes i klienten, server og cloud computing, herunder personlige skytjenester til forbrugere. Vedtaget syn af en samlet gratis platform i Ubuntu, fra telefonen til skyen, med en serie af sammenhængende grænseflader for telefon, tablet, TV og computeren, gør Ubuntu det første valg til forskelligartet institutioner fra udbydere af offentlige skyen producenten af consumer electronics, og en foretrukken mellem individuelle teknologer.

Med udviklere og tekniske centrum over hele verden Canonical entydigt er placeret for at partner med hardware-producenten, indholdsudbydere og softwareudviklere til at hente Ubuntu løsninger på markedet, fra pc'er til servere og håndholdte enheder.

