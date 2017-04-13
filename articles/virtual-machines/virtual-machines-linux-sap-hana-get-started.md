<properties
   pageTitle="Hurtig start vejledning til manuel installation af SAP HANA på Azure FOS | Microsoft Azure"
   description="Hurtig start vejledning til manuel installation af SAP HANA på Azure FOS"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="hermanndms"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/15/2016"
   ms.author="hermannd"/>

# <a name="quickstart-guide-for-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>Hurtig start vejledning til manuel installation af enkelt forekomst SAP HANA på Azure FOS

## <a name="introduction"></a>Introduktion

Denne vejledning til hurtig start hjælper med at konfigurere en enkelt forekomst SAP HANA prototyper/demo system på Azure FOS ved en manuel installation af SAP NetWeaver 7.5 og SAP HANA SP12.
Guiden forudsætter, at læseren er fortrolig med Azure IaaS grundlæggende som hvordan du kan installere virtuelle maskiner eller virtuelle netværk, enten via Azure portal eller Powershell/CLI herunder mulighed for at bruge json skabeloner. Det forventes desuden, læseren er kendskab til SAP HANA, SAP NetWeaver og hvordan du installerer programmet i det lokale miljø.

Det forventes, at læseren er bekendt med den generelle SAP-Azure dokumentation, der er nævnt i sektionen generelle oplysninger i slutningen af artiklen.

Denne vejledning på grund af begrænsning til ikke-produktiv systemer ikke dækker emner som HA, Sikkerhedskopiér, DR, høj ydeevne eller speciel sikkerhedsovervejelser.

Eksempel konfigurationen blev foretaget ved hjælp af to virtuelle maskiner til at udføre en fordelt SAP NetWeaver installation via Azure ressourcestyring modellen, som SAP-Linux-Azure understøttes kun på på Azure ressourcestyring og ikke klassisk modellen. Links til yderligere oplysninger om Azure ressourcestyring kan findes i sektionen generelle oplysninger i slutningen af denne artikel.

Disse er de to test FOS bruges til installation af eksempeldata:

* Hana-appsrv (type DS3) til at hoste NW 7.5 ASC'ER forekomst + PAS
* Hana dbsrv (type GS4) vært HANA SP12
* begge FOS hørt til én Azure virtuelt netværk (azure-hana-test-vnet)
* Operativsystemet i begge tilfælde kunne SLES 12 SP1


Vær opmærksom på hensyn til via, og juli 2016 SAP HANA kun understøttes fuldt ud for OLAP (Båndbredden) fremstilling systemer på Azure VM type GS5. Det er fint at bruge noget mindre som f.eks. GS4 til testformål, hvor en ikke forventer officiel SAP-support.
Hvad altid skal bruges til SAP HANA på Azure, er Azure premium lagerplads for HANA data og logfiler - se "Disk konfigurationen" afsnit længere nede. Om yderligere oplysninger om, hvilke SAP produkter understøttes på Azure skal du se sektionen generelle oplysninger i slutningen af denne artikel.


Vejledningen i denne artikel beskrives to forskellige måder at kunne installere SAP HANA manuelt i Azure FOS:

* installere SAP HANA via SAP Software klargøring Manager (SWPM) som en del af en fordelt NetWeaver installation på trinnet "databaseforekomst"
* installere SAP HANA ved hjælp af HANA livscyklus Manager værktøjet hdblcm og derefter installere NetWeaver bagefter

Det er også muligt at bruge SWPM og installerer alle komponenter (SAP HANA, SAP programserver, ASC'ER forekomst, SAP grafiske) i én enkelt VM. Denne indstilling ikke er beskrevet i denne artikel, men de elementer, der skal betragtes som er den samme.

Før du starter en installation læses i det næste afsnit kontrollister under om konfiguration af Azure test FOS for at undgå flere grundlæggende fejl, der sker, når du bruger kun en standard Azure VM konfiguration.


## <a name="checklist-sap-hana-installation-via-sap-swpm"></a>Tjekliste SAP HANA installation via SAP SWPM

Dette er en simpel tjekliste over de vigtigste elementer, der er relateret til en manuel enkelt forekomst SAP HANA installation til demo eller prototyper pursposes via SAP SWPM at gøre en fordelt SAP NW 7.5 installere. De enkelte elementer forklaring og vises i form af skærmbilleder mere detaljeret i hele artiklen:

* oprette en Azure virtuelt netværk, der er to test FOS 
* installere to Azure FOS med OS SLES 12 SP1 via Azure ressourcestyring model 
* vedhæfte to standard lagerplads diske til app-serveren VM (fx 75GB og 500GB)
* vedhæfte fire diske til HANA DB serveren VM - 2 standard lagerplads diske ud for app serveren VM + 2 premium lagerplads diske (fx 2x512GB)
* Afhængigt af størrelse og/eller overførselshastighed krav vedhæfte flere diske og oprette spredt enheder enten ved hjælp af lvm eller mdadm på OS niveau i VM
* oprette XFS filsystemer på den vedhæftede disk / logiske enheder
* oprette forbindelse til de nye XFS filsystemer på OS niveau. Brug én filsystemet til at holde alle SAP-software og det andet fx til sapmnt directory og måske sikkerhedskopier. På SAP HANA DB server Indlæs XFS fil systemer på premium lagerplads disk som /hana og /usr/sap dette er alle nødvendige for at undgå rod filsystemet som ikke er for stor på Linux Azure FOS fylder
* Angiv de lokale IP-adresser for test FOS i/osv/hosts
* Angiv parameteren nofail i /etc/fstab
* angive kernen parametre i overensstemmelse med HANA-SLES-12 SAP bemærkning (se oplysninger om længere nede i sektionen kernen paramters)
* Tilføj Udskift mellemrum
* Hvis ønskede - installere et grafisk skrivebord på test FOS. Ellers skal du bruge en ekstern sapinst installation
* hente SAP-software fra SAP service marketplace
* Sådan installeres SAP ASC'ER forekomsten på app-serveren VM
* dele mappen sapmnt via NFS mellem test FOS (app server VM er NFS-serveren)
* Sådan installeres den databaseforekomst, herunder HANA via SWPM på DB serveren VM
* installere på PAS på app-serveren VM
* Start SAP MC og Opret forbindelse fx via SAP grafiske / HANA Studio 



## <a name="checklist-sap-hana-installation-via-hdblcm"></a>Tjekliste SAP HANA installation via hdblcm

Dette er en simpel tjekliste over de vigtigste elementer, der er relateret til en manuel enkelt forekomst SAP HANA installation til demo eller prototyper pursposes via SAP SWPM at gøre en fordelt SAP NW 7.5 installere. De enkelte elementer forklaring og vises i form af skærmbilleder mere detaljeret i hele artiklen:

* oprette en Azure virtuelt netværk, der er to test FOS 
* installere to Azure FOS med OS SLES 12 SP1 via Azure ressourcestyring model 
* vedhæfte to standard lagerplads diske til app-serveren VM (fx 75GB og 500GB)
* vedhæfte fire diske til HANA DB serveren VM - 2 standard lagerplads som på app-serveren VM + 2 premium lagerplads diske (fx 2x512GB)
* Afhængigt af størrelse og/eller overførselshastighed krav vedhæfte flere diske og oprette spredt enheder enten ved hjælp af lvm eller mdadm på OS niveau i VM
* oprette XFS filsystemer på den vedhæftede disk / logiske enheder
* oprette forbindelse til de nye XFS filsystemer på OS niveau. Brug én filsystemet til at holde alle SAP-software og det andet fx til sapmnt directory og måske sikkerhedskopier. På SAP HANA DB server Indlæs XFS fil systemer på premium lagerplads disk som /hana og /usr/sap dette er alle nødvendige for at undgå rod filsystemet som ikke er for stor på Linux Azure FOS fylder
* Angiv de lokale IP-adresser for test FOS i/osv/hosts
* Angiv parameteren nofail i /etc/fstab
* angive kernen parametre i overensstemmelse med HANA-SLES-12 SAP bemærkning (se oplysninger om længere nede i sektionen kernen paramters)
* Tilføj Udskift mellemrum
* Hvis ønskede - installere et grafisk skrivebord på test FOS. Ellers skal du bruge en ekstern sapinst installation
* hente SAP-software fra SAP service marketplace
* Opret gruppe "sapsys" med gruppe-id 1001 på HANA DB Server VM
* installere SAP HANA på DB serveren VM ved hjælp af hdblcm
* Sådan installeres SAP ASC'ER forekomsten på app-serveren VM
* dele mappen sapmnt via NFS mellem test FOS (app server VM er NFS-serveren)
* Sådan installeres den databaseforekomst, herunder HANA via SWPM på DB serveren VM
* installere på PAS på app-serveren VM
* Start SAP MC og Opret forbindelse fx via SAP grafiske / HANA Studio 




## <a name="prepare-azure-vms-for-manual-installation-of-sap-hana"></a>Forberede manuel installation af SAP HANA Azure FOS

Kapitlet om at forberede Azure FOS til manuel installation af SAP HANA består af fem sektioner, som dækker de følgende emner:

* Konfiguration af disk
* Kernen parametre
* FILESYSTEMS
* / osv/hosts
* / osv/fstab


### <a name="disk-setup"></a>Konfiguration af disk

Rod filsystemet i en Linux VM på Azure, er begrænset størrelse. Det er derfor nødvendigt at knytte ekstra diskplads til en VM til kørsel af SAP. Det er fint at bruge Azure standard lagerplads diske tale om en SAP-app-server VM bruges i en ren prototyper/demo-miljø. Mens for de SAP HANA DB data og logfiler - Azure Premium-lagerplads der benyttes selv i en ikke-produktiv liggende.

Se nogle oplysninger om, hvordan du vedhæfter diske til en Linux VM [her](virtual-machines-linux-add-disk.md)

Hvis du vil Azure disk cachelagring - skal et brug "Ingen" om en disk, der skal bruges til at gemme transaktionslog HANA. Datafiler er det ok at bruge læse cachelagring HANA. Som HANA er forbedrer en i hukommelsen database det afhænger af det overordnede brugsmønster hvor meget få cachen på niveauet for Azure disk ydeevnen (fx starter HANA og læse data fra disken i hukommelsen).

Se detaljer om Azure Premium lager [her](../storage/storage-premium-storage.md)

[Her](https://github.com/Azure/azure-quickstart-templates) du finde json eksempelskabeloner til at oprette FOS.
Den "101-vm-enkle-linux" viser, hvordan en standardskabelon ligner herunder sektionen lagerplads der laves en 100GB datadisk.

[Denne artikel](virtual-machines-linux-sap-on-suse-quickstart.md) indeholder oplysninger om, hvordan du finder et SUSE billede via Powershell eller CLI og prioritet vedhæfte en disk via UUID.


Afhængigt af størrelsen på system og overførselshastighed krav kan det være nødvendigt at vedhæfte flere diske i stedet for ét og nyere på oprette en stribe, der er angivet på tværs af de pågældende diske på OS niveau. Dette er de to aspekter, hvorfor en vil oprette en stribe, der er angivet på tværs af flere Azure diske:

* øge overførselshastighed
* har du brug for en enkelt filsystemet > 1TB, som den aktuelle størrelsesbegrænsning Azure disken er 1TB (og juli 2016)


Flere oplysninger om de to primære værktøjer til at konfigurere striping kan findes her:

[Artikel om at bruge mdadm til at konfigurere Linux software raid på en Azure VM](virtual-machines-linux-configure-raid.md)

[Artikel om at konfigurere logiske lydstyrken Manager på en Linux Azure VM](virtual-machines-linux-configure-lvm.md)



![](./media/virtual-machines-linux-sap-hana-get-started/image003.jpg)

Miljø to Azure standard lagerplads diske blev knyttet til SAP app serveren VM i testen. En blev brugt til at gemme alle SAP-software til installation (fx NetWeaver 7.5, SAP grafiske SAP HANA... ) og andre den person, der har tilstrækkelig plads til det kan være nødvendige (fx Sikkerhedskopiér, testdata) samt mappen sapmnt (fx SAP profiles) skal fordeles på alle FOS der hører til den samme SAP liggende.

![](./media/virtual-machines-linux-sap-hana-get-started/image004.jpg)

I modsætning til app-serveren var VM fire diske tilsluttet SAP HANA serveren VM. Som før to diske blev brugt til at holde SAP softwaren (en kan også dele SAP software disken via NFS) og har tilstrækkelig plads fx til sikkerhedskopi. Yderligere to diskene blev Azure Premium lagerplads disk, der beholde SAP HANA data og logfiler filer samt mappen /usr/sap.


### <a name="kernel-parameters"></a>Kernen parametre


SAP HANA kræver specifikke Linux kernen indstillinger, der ikke er en del af de almindelige Azure galleri billeder og skal angives manuelt. Der er en bestemt SAP-note som beskrives indstillingerne. 


SAP Bemærk SAP HANA DB: Anbefalet OS indstillinger for SLES 12 / SLES til SAP-programmer 12: [SAP Note 2205917](https://launchpad.support.sap.com/#/notes/2205917)

En ekstra emne reagrding side-cache, der er relateret til kører SAP HANA på SLES kan findes [her](https://www.suse.com/documentation/sles_for_sap/singlehtml/sles_for_sap_guide/sles_for_sap_guide.html#sec.s4s.configure.page-cache) i kapitel 6.1 kernen: side-Cache grænse

Der er også en SAP-Note om side-cache grænsen [SAP Note 1557506](https://service.sap.com/sap/support/notes/1557506)

SLES 12 har et nyt værktøj, der erstatter værktøjet gamle sapconf. Det er "indstillet-adm", og der er en speciel SAP HANA profil. En kan finde flere oplysninger om dette værktøj, følge to linkene nedenfor.

SLES dokumentation om indstillet adm profil sap-hana kan findes [her](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html)

SLES dokumentation om indstillet adm profil sap-hana - kapitel 6.2 justering af systemer til SAP arbejdsmængder med indstillet adm - kan findes [her](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf)


![](./media/virtual-machines-linux-sap-hana-get-started/image005.jpg)

Her kan en se, hvordan "indstillet-adm" er ændret i transparent_hugepage samt numa_balancing værdierne overensstemmelse med de nødvendige SAP HANA indstillinger.


For at gøre indstillingerne SAP HANA kernen permanente har en Brug grub2 på SLES 12. Du kan finde yderligere oplysninger om grub2 [her](https://www.suse.com/documentation/sled-12/book_sle_admin/data/sec_grub2_file_structure.html)


![](./media/virtual-machines-linux-sap-hana-get-started/image006.jpg)

Dette skærmbillede viser, hvordan indstillingerne for kernen blev ændret i config-filen og derefter "kompileret" via grub2 mkconfig


![](./media/virtual-machines-linux-sap-hana-get-started/image007.jpg)

En anden mulighed kunne være at ændre indstillingerne for via Yast og indstillinger for Start Loader kernen parameter.


### <a name="filesystems"></a>FILESYSTEMS 

![](./media/virtual-machines-linux-sap-hana-get-started/image008.jpg)

Her kan se de to filsystemer, der er oprettet på SAP app serveren VM oven på de to vedhæftede Azure standard lagerplads diske. Begge filesystems er af typen XFS og fastgøres til /sapdata og /sapsoftware.

Det er ikke obligatorisk at gøre det på denne måde. Der er forskellige indstillinger Sådan sturcture så meget diskplads.
Det vigtigste aspekt er at undgå, at rod filsystemet løber tør for plads. 


![](./media/virtual-machines-linux-sap-hana-get-started/image009.jpg)

Om SAP HANA DB VM er det vigtigt at vide, at under installationen af en database via sapinst (swpm) og lige ved hjælp af indstillingen enkle "typisk" installation installeres den ting som standard under /hana og /usr/sap. Standardindstillingen for sikkerhedskopiering af SAP HANA er fx under /usr/sap.
Synes godt om før den er nøglen for at undgå, at rod filsystemet løber tør for plads. En skal derfor kontrollere, at der er nok ledig plads under /hana og /usr/sap før du installerer SAP HANA via swpm.

[I denne artikel](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm) fra SAP beskrives standard filsystemet layoutet af SAP HANA 


![](./media/virtual-machines-linux-sap-hana-get-started/image010.jpg)

Når du installerer SAP NetWeaver på et almindeligt SLES 12 Azure galleribillede vises der en meddelelse, der er ikke Udskift plads. For at slippe af med denne meddelelse kunne et fx manuelt føje en Byt om fil som beskrevet i dette dokument via dd, mkswap og swapon. Kun søge efter "Tilføjelse af et Ombyt fil manuelt" i [denne artikel](https://www.suse.com/documentation/sled-12/book_sle_deployment/data/sec_yast2_i_y2_part_expert.html)

En anden mulighed er at konfigurere Udskift mellemrum via Linux VM agent. Du kan finde flere oplysninger [her](virtual-machines-linux-agent-user-guide.md)


### <a name="etchosts"></a>/ osv/hosts

![](./media/virtual-machines-linux-sap-hana-get-started/image011.jpg)

En anden vigtigt aspekt før du begynder at installere SAP er at medtage host navne og IP-adresser for SAP-FOS i filen fjernsystemet. En skal installere alle SAP FOS i én Azure virtuelt netværk og derefter bruge de interne IP-adresser.

### <a name="etcfstab"></a>/ osv/fstab

![](./media/virtual-machines-linux-sap-hana-get-started/image000c.jpg)

I fasen test slået det til at være en god ide at føje parameteren nofail til fstab. Hvis noget går galt med diskene vil derefter VM stadig komme frem og ikke hænge i startprocessen. Men et skal se som i dette tilfælde ekstra diskpladsen ikke muligvis være tilgængelige og processer kan udfylde op i roden filsystemet. I tilfælde af /hana ville være manglende ville SAP HANA start selvom overhovedet.


## <a name="install-graphical-gnome-desktop-on-sles-12"></a>Installere grafiske Gnome skrivebord på SLES 12

Kapitlet består af to secitions som dækker de følgende emner:

* Installation af Gnome skrivebord og xrdp på SLES 12
* Køre Java-baserede SAP MC med Firefox på SLES 12

En kan også bruge alternativer som Xterminal, VNC, men i denne artikel beskrives kun xrdp og Sep 2016.

### <a name="installation-of-gnome-desktop-and-xrdp-on-sles-12"></a>Installation af Gnome skrivebord og xrdp på SLES 12

Der er en nem måde at arkivere dette især til de personer, der har Microsoft Windows baggrund og gerne vil bruge et grafisk skrivebord direkte i SAP Linux FOS til at køre Firefox, Sapinst, SAP grafiske brugergrænseflade, SAP MC eller HANA Studio og måske oprette forbindelse til VM via RDP fra en Microsoft Windows-computer. Det er ok for en ren prototyper/demo-miljø, mens det ikke er nødvendigvis relevante fx for en fremstilling databaseserver. Her er trinnene for at installere Gnome computeren på en Azure SLES 12 VM:

Du kan installere gnome skrivebordet ved følgende kommando (fx i et trykfarver vindue):

   zypper i -t mønster gnome basic

Installer derefter xrdp for at tillade forbindelsen til VM via RDP:

   zypper i xrdp

Rediger /etc/sysconfig/windowmanager, og Indstil windows standardprogrammet til Gnome:

   DEFAULT_WM = "gnome"

Kør chkconfig at sikre, at xrdp starter automatisk efter en genstart: 

  chkconfig-niveau 3 xrdp på

i tilfælde af, at der skal være et problem med RDP-forbindelse kan du prøve at genstarte (måske ikke et trykfarver vindue):

  /etc/xrdp/xrdp.sh genstart

i tilfælde af xrdp genstart som nævnt ovenfor, ikke fungerer afkrydsningsfelt, hvis der findes en .pid fil og fjerne den:

  kontrollere /var/run og se efter xrdp.pid   
  fjerne det og derefter prøve genstart igen



### <a name="sap-mc"></a>SAP MC


For at starte den grafiske Java-baserede SAP MC af Firefox kører i en Azure SLES 12 VM, når du har installeret Gnome får dekstop som beskrevet i forrige afsnit, hvis en en fejl på grund af manglende Java-browser-plug-in'en.

URL-adressen til at starte SAP MC <server>: 5 < instance_number > 13

Du kan finde flere oplysninger [her](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm)


![](./media/virtual-machines-linux-sap-hana-get-started/image013.jpg)

På skærmbilledet ovenfor se en, hvordan en fejlmeddelelse om kan se ud som når Java-Browser-Plug-in mangler. 

![](./media/virtual-machines-linux-sap-hana-get-started/image014.jpg)

En indstilling for at løse problemet er blot at installere manglende plug-in'et via Yast.

![](./media/virtual-machines-linux-sap-hana-get-started/image015.jpg)

Gentaget SAP MC URL-adressen kan få vist lidt tid på dialogboksen først bede om at aktivere plug-in'et.


En yderligere spørgsmål, som kan pop op-er en fejlmeddelelse om en manglende fil: Dette er højst sandsynligt relateret til installation af Java 1,8, som kræves til SAP grafiske 7.4 javafx.properties

Den IBM Java-version, ses via Yast omfatter ikke denne fil. Løsningen er en Java overførsel fra Oracle.
Du kan finde en artikel, der taler om problemet bestemte [her](https://scn.sap.com/thread/3908306)



## <a name="manual-sap-hana-installation-via-swpm-as-part-of-a-netweaver-75-installation"></a>Manuel SAP HANA installation via SWPM som en del af en NetWeaver 7.5 installation


Følgende liste over skærmbilleder viser de vigtigste trin til at installere SAP NetWeaver 7.5 og SAP HANA SP12 via SWPM (sapinst). Som en del af en NW 7.5 har installation SWPM funktioner for også at installere HANA databasen som en enkelt forekomst.


![](./media/virtual-machines-linux-sap-hana-get-started/image012.jpg)

For eksempel testen er miljø kun én ABAP app server installeret. Indstillingen "Distributed System" blev brugt til at installere ASC'ER forekomsten og den primære App server-forekomst i én Azure VM og SAP HANA som databasesystem i en en anden Azure VM.


![](./media/virtual-machines-linux-sap-hana-get-started/image016.jpg)

Når ASC'ER forekomsten er installeret på app-serveren VM og er indstillet til "grøn" i den SAP MC mappen sapmnt som omfatter fx SAP mappen profil der skal deles med SAP HANA DB serveren VM.
DB installationstrin skal have adgang til disse oplysninger. Der er den bedste måde at bruge NFS der kan konfigureres ved hjælp af Yast.


![](./media/virtual-machines-linux-sap-hana-get-started/image017b.jpg)

På appen bør server VM mappen sapmnt deles via NFS ved hjælp af indstillinger "rw" samt "no_root_squash". Standard er "ro" og "root_squash", hvilket kan medføre problemer med, når du installerer databaseforekomsten.


![](./media/virtual-machines-linux-sap-hana-get-started/image018b.jpg)

På SAP HANA DB serveren VM sapmnt dele fra app serveren VM der skal konfigureres via "NFS-klient" (fx ved hjælp af Yast)


![](./media/virtual-machines-linux-sap-hana-get-started/image019.jpg)

Derefter har én til at logge på serveren SAP HANA DB VM og starte SWPM for at opnå det næste trin i en fordelt NW 7.5 installation - "databaseforekomst".


![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

Relateret til SAP HANA installationen der ikke er faktisk for meget til at angive når du har valgt en "typisk" installation. Stien til installatiom medier en har ud over at angive et DB SID, værtsnavnet, forekomst antallet og DB for administratoradgangskode.

 
![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

Næste trin er at indtaste adgangskoden for DBACOCKPIT skemaet.



![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

Derefter kommer spørgsmål om SAPABAP1 skema adgangskoden.


![](./media/virtual-machines-linux-sap-hana-get-started/image023.jpg)

Til sidst skal være kun grønne markeringerne foran hver fase af installationsprocessen DB og feltet lille meddelelse, der vises skal der stå "udførelse af... Databaseforekomst er fuldført".

 
![](./media/virtual-machines-linux-sap-hana-get-started/image024.jpg)

Efter vellykket installation vise SAP MC også forekomsten af DB som "grøn" og den komplette liste over SAP HANA processer (fx hdbindexserver, hdbcompileserver)


![](./media/virtual-machines-linux-sap-hana-get-started/image025.jpg)

Dette skærmbillede viser dele af filstruktur under /hana/shared som SWPM oprettet under HANA installationen. Der ikke er en indstilling til at angive en anden sti. Det er derfor så vigtige at tilslutte ekstra diskplads under /hana før SAP HANA installationen via SWPM for at undgå, at rod filsystemet løber tør for plads.


![](./media/virtual-machines-linux-sap-hana-get-started/image026.jpg)

Her kan se det samme som det er beskrevet inden for mappen /usr/sap.


![](./media/virtual-machines-linux-sap-hana-get-started/image027b.jpg)

Det sidste trin i fordelt ABAP installationen er den "primære programmet Server-forekomst"


![](./media/virtual-machines-linux-sap-hana-get-started/image028b.jpg)

Når PAS samt SAP grafiske har installeret kan en se via posteringen "dbacockpit", der alt ser højre med SAP HANA installationen.

 
![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

Trin et kan installere SAP HANA Studio i SAP app server VM og oprette forbindelse til den SAP HANA forekomst, der kører på serveren DB VM er en senest.




## <a name="manual-sap-hana-installation-via-hana-life-cycle-manager-tool-hdblcm"></a>Manuel SAP HANA installation via HANA livscyklus manager værktøjet hdblcm


Ud over at installere SAP HANA som en del af en distribueret installation via SWPM er det også summen først installere HANA enkeltstående ved hjælp af hdblcm og derefter installere fx SAP NetWeaver 7.5 bagefter. Listen over skærmbilleder nedenfor viser, hvordan det fungerer.

Her er tre kilder til oplysninger om værktøjet HANA hdblcm:

[Vælge den korrekte SAP HANA HDBLCM til opgaven](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)

[Værktøjer til administration af SAP HANA livscyklus](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)

[SAP HANA serverinstallation og opdatering](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)



![](./media/virtual-machines-linux-sap-hana-get-started/image030.jpg)

For at undgå at løbe ind i problemer senere med en gruppe-id standardindstillingen for den \<HANA SID\>adm bruger (oprettet med værktøjet hdblcm) en skal definere en ny gruppe med navnet "sapsys" med gruppe-id 1001 før du installerer SAP HANA ved hjælp af hdblcm.




![](./media/virtual-machines-linux-sap-hana-get-started/image031.jpg)

Starte hdblcm først gang, der skal knyttes en simpel startmenu, hvor én har Markér element 1 "installere nye System"



![](./media/virtual-machines-linux-sap-hana-get-started/image032.jpg)

På dette skærmbillede kan se de vigtigste indstillinger, der er angivet før. Vigtigt – kataloger, er navnet for HANA log og data enheder samt installationssti (/ hana/delt i dette eksempel) og/usr/sap ikke skal være en del af rod filsystemet men tilhører Azure datadisce som blev knyttet til VM, som beskrevet i afsnittet Azure VM konfiguration. Dette vil undgå risikoen for, at rod filsystemet kan løbe tør for plads.
En kan også se, at HANA admin-brugeren har bruger-id 1005 og er en del af den sapsys gruppe (id 1001), som blev defineret før installationen.



![](./media/virtual-machines-linux-sap-hana-get-started/image033.jpg)

En kan se HANA \<HANA SID\>adm (azdadm i dette eksempel) brugeroplysninger i/osv/passwd



![](./media/virtual-machines-linux-sap-hana-get-started/image034.jpg)

Når du har installeret SAP HANA ved hjælp af hdblcm kan den ses i SAP HANA Studio. SAPABAP1 skemaet som omfatter fx alle SAP NetWeaver tabeller er ikke tilgængelig endnu.



![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

Når du har installeret SAP HANA kan en installere SAP NetWeaver ovenpå. Brug af en "distribueret installation" via SWPM som beskrevet i afsnittet tilsvarende ovenfor i dette eksempel, der blev foretaget.
Når installation databaseforekomsten via SWPM en lige har til at angive de samme data som før med hdblcm (fx hostname, HANA SID forekomst tal). SWPM kan derefter bruge eksisterende HANA installationen og tilføje yderligere skemaer.



![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

Dette er billedet af installationstrin SWPM, hvor én har angive data vedrørende DBACOCKPIT skemaet.


![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

Derefter forventer SWPM at indtaste data om SAPABAP1 skemaet.


![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

En kan se SAPABAP1 skemaet i HANA Studio, når SWPM database forekomst installationen er fuldført.



![](./media/virtual-machines-linux-sap-hana-get-started/image039b.jpg)

Og til sidst efter installationen af SAP app-serveren og SAP grafiske en bør kunne bekræfte forekomsten HANA DB med transaktion "dbacockpit".




## <a name="general-information-related-to-sap-azure-certifications-running-sap-hana-on-azure-and-sap-software-download"></a>Generelle oplysninger, der er relateret til SAP Azure-certificering, kører SAP HANA på Azure og SAP software download

* generelle SAP Azure doku om at køre SAP på Azure med Windows OS i tilstanden Klassisk: [Brug af SAP på Windows virtuelle maskiner i Azure](virtual-machines-windows-classic-sap-get-started.md)

* oplysninger om eksisterende SAP-skabeloner til brug af kunder: [Azure Hurtig start skabeloner til SAP](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/)

* generelle SAP Azure doku om at køre SAP på Azure med Linux OS i Azure ressourcestyring model: [Brug af SAP på Linux virtuelle maskiner (VM'er)](virtual-machines-linux-sap-get-started.md)

* certificeret SAP HANA hardware directory som viser, hvilke Azure VM datatyper understøttes til fremstilling: [Certificeret SAP HANA® Hardware Directory](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)

* oplysninger om virtuelt størrelser især for Linux arbejdsbelastninger: [størrelser for virtuelle maskiner i Azure](virtual-machines-linux-sizes.md)

* SAP-Note, som viser alle understøttes SAP-produkter på Azure og understøttede Azure VM datatyper for SAP: [SAP Note 1928533](https://launchpad.support.sap.com/#/notes/1928533/E)

* SAP bemærkning om SAP "udvidet overvågning" med Linux FOS på Azure: [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E)

* SAP HANA med tilbud om Azure "Store forekomster". Det er vigtigt at forstå, at dette er ikke om at køre SAP HANA på Azure FOS, men i et hybridt miljø, hvor SAP app serverne køre i Azure FOS men SAP HANA kører på servere helt fra bunden: [SAP Note 2316233](https://launchpad.support.sap.com/#/notes/2316233/E)

* SAP Note med oplysninger om SAPOSCOL på Linux: [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E)

* De primære overvågning målepunkter for SAP på Microsoft Azure: [SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E)

* Oplysninger om Azure ressourcestyring: [Azure ressourcestyring oversigt](../azure-resource-manager/resource-group-overview.md)

* Oplysninger om installation af Linux FOS via skabeloner: [Implementer og administrere virtuelle maskiner ved hjælp af Azure ressourcestyring skabeloner og Azure CLI](virtual-machines-linux-cli-deploy-templates.md)

* Sammenligning af installation modeller mellem Azure ressourcestyring og klassisk: [Azure ressourcestyring kontra klassisk installation: forstå installation-modeller og tilstanden for dine ressourcer](../resource-manager-deployment-model.md)

* Hente NetWeaver 7.5 til Linux/HANA fra SAP Service Marketplace:![](./media/virtual-machines-linux-sap-hana-get-started/image001.jpg)

* Hente HANA SP12 Platform Edition fra SAP Service Marketplace:![](./media/virtual-machines-linux-sap-hana-get-started/image002.jpg)

