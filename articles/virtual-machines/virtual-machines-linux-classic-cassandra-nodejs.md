<properties
    pageTitle="Køre Cassandra med Linux på Azure | Microsoft Azure"
    description="Sådan køres en Cassandra klynge på Linux i virtuelle Azure-computere fra en Node.js-app"
    services="virtual-machines-linux"
    documentationCenter="nodejs"
    authors="hanuk"
    manager="wpickett"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="hanuk;robmcm"/>

# <a name="running-cassandra-with-linux-on-azure-and-accessing-it-from-nodejs"></a>Køre Cassandra med Linux på Azure og få adgang til den fra Node.js

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Lær, hvordan du kan [udføre disse trin, ved hjælp af Ressourcestyring modellen](https://azure.microsoft.com/documentation/templates/datastax-on-ubuntu/).

## <a name="overview"></a>Oversigt
Microsoft Azure er en åben skyen platform, der kører både Microsoft som godt som ikke stammer fra Microsoft-software, som omfatter operativsystemer, application servere, SMS programmer samt SQL og NoSQL databaser fra begge kommercielle og open source-modeller. Opbygning af tolerant tjenester på offentlige skyer, herunder Azure kræver nøje planlægning og bevidst arkitektur for begge programmer servere som godt lagerplads lag. Cassandras fordelt lagerplads arkitektur naturligt hjælper med at opbygge meget tilgængelige systemer, der er fejlsikrede for klynge mislykkede forsøg. Cassandra er en skybaseret skala NoSQL database vedligeholdes af Apache Software Foundation på cassandra.apache.org; Cassandra er skrevet i Java og dermed kører på begge i Windows samt Linux platforme.

Fokus i denne artikel er at vise Cassandra installation på Ubuntu som en enkelt og flere data center klynge udnytte virtuelle Microsoft Azure-computere og virtuelle netværk. Klynge til fremstilling optimeret arbejdsbelastninger er uden for omfanget af denne artikel, som kræver flere diskplads node konfiguration, relevante ring topologi design og datamodeller for at understøtte nødvendige gentagelse, data konsistens, overførselshastighed og høj tilgængelighedskrav.

I denne artikel tager en grundlæggende fremgangsmåde til at vise Hvad er involveret i opbygning af Cassandra klynge sammenlignes Docker, kok eller Puppet, som kan gøre infrastruktur installationen meget nemmere.  

## <a name="the-deployment-models"></a>Installation-modeller
Microsoft Azure netværk giver mulighed for installation af isolerede privat klynger, access, kan være begrænset til at nå at kornet netværkssikkerhed.  Da denne artikel handler om viser Cassandra installationen på et grundlæggende niveau, vil vi ikke fokusere på niveauet for konsistens og optimal lagerplads design for overførsel. Her er listen over netværk krav til vores hypotetiske klynge:

- Eksterne systemer kan ikke få adgang til Cassandra database fra i eller uden for Azure
- Cassandra klynge skal være bag en belastningsjustering til thrift trafik
- Installere Cassandra noder i to grupper i hver datacenter for en udvidet klynge tilgængelighed
- Låse klyngen så, der kun programmet serverfarm har direkte adgang til databasen
- Ingen offentlige netværk slutpunkter end SSH
- Hver Cassandra node skal have en fast interne IP-adresse

Cassandra kan installeres, til et enkelt Azure område eller til flere områder, der er baseret på arbejdsbelastningen fordelt art. Flere områder implementeringsmodel kan bruges til at fungere slutbrugere tættere til en bestemt Geografi gennem den samme Cassandra infrastruktur. Cassandras indbyggede node gentagelse træder omtanke med synkronisering af flere master skriver kommer fra flere datacentre og præsenterer en ensartet visning af data til programmer. Flere områder installation kan også hjælpe med risikoen afhjælpning af de bredere Azure serviceafbrydelser. Cassandra's tunable konsistens og topologi for replikering hjælper med at opfylde forskellige frigivne Produktionsordre behov over programmer.

### <a name="single-region-deployment"></a>Enkelt område installation
Vi starter med et enkelt område installation og høste learnings i at oprette en model til flere områder. Azure virtuelt netværk bruges til at oprette isolerede undernet, så de netværk sikkerhedskrav, der er nævnt ovenfor kan opfyldes.  Processen beskrevet i oprette enkelt område installationen bruger Ubuntu 14.04 LTS og Cassandra 2.08; processen kan dog nemt indført til de andre Linux-varianter. Følgende er nogle af de systematiske karakteristika for enkelt område installationen.  

**Høj tilgængelighed:** Noderne Cassandra vist i figur 1 er installeret på to tilgængelighed sæt, så noderne fordeles mellem flere fejl domæner til høj tilgængelighed. FOS kommenterede med hvert sæt tilgængelighed er knyttet til 2 fejl domæner.  Microsoft Azure bruger, som repræsenterer et domæne til at administrere ikke-planlagt ned tid (fx hardware og software fejl), mens begrebet opgradering domæne (fx vært eller gæst OS rettelse/opgraderinger, programmet opgraderinger) bruges til at administrere planlagt nedetid. Se [nedbrud og høj tilgængelighed til Azure programmer](http://msdn.microsoft.com/library/dn251004.aspx) for rollen for fejl, og opgradere domæner i nå høj tilgængelighed.

![Enkelt område installation](./media/virtual-machines-linux-classic-cassandra-nodejs/cassandra-linux1.png)

Figur 1: Enkelt område installation

Bemærk, samtidig med denne skriver Azure ikke tillader eksplicitte tilknytning af en gruppe af FOS til en bestemt fejl domæne; selv med den implementeringsmodel, der er vist i figur 1, er det derfor statistisk sandsynligt, at alle virtuelle maskiner kan knyttes til to fejl domæner i stedet for fire.

**Thrift trafik justering af belastning:** Thrift klientbiblioteker i web-serveren oprette forbindelse til klynge gennem en intern justering af belastning. Dette kræver processen med at tilføje den interne justering af belastning til "data" undernet (se figur 1) i forbindelse med skytjenesten vært Cassandra klynge. Når den interne justering af belastning er defineret, kræver hver node Indlæs afstemmes slutpunktet skal tilføjes med anmærkninger af nogle Indlæs afstemmes med tidligere defineret Indlæs belastningsjusteringstjenesten navn. Se [Azure interne belastning ](../load-balancer/load-balancer-internal-overview.md)kan finde flere oplysninger.

**Klynge frø:** Det er vigtigt at vælge de fleste meget tilgængelige knuderne for frø, som de nye noder vil kommunikere med frø noder til at finde topologien for klyngen. En node fra hvert sæt tilgængelighed er angivet som frø noder for at undgå ét afsnit.

**Gentagelse faktor og konsistens niveau:** Cassandras indbygget høj tilgængelighed og data holdbarhed er er betegnet ved gentagelse faktor (RF - antallet af kopier af hver række, der er gemt på klyngen) og konsistens niveau (antal replikaer som læst/skrevet før der returneres resultatet kalderen). Replikering faktor er angivet under KEYSPACE (svarende til en relationel database) oprettelse, mens niveauet konsistens er angivet under udstedelse CRUD forespørgslen. Se dokumentationen til Cassandra på [konfiguration for konsistens](http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) for konsistens detaljer og formel til beregning af quorum.

Cassandra understøtter to typer integritet datamodeller – konsistens og eventuel konsistens; Replikering faktor og konsistens niveau bestemmer sammen Hvis dataene vil være ensartet, så snart en handling af skrivning er fuldført eller det bliver til sidst ensartet. For eksempel angive QUORUM som niveauet konsistens skal altid sikrer data konsistens, mens en hvilken som helst konsistens niveau, under antallet af replikaer skal skrives efter behov for at nå QUORUM (fx én) søgeresultater i data, der til sidst ensartet.

Den ovenstående med en gentagelse faktor på 3 og QUORUM klynge med 8 noder (2 noder læses eller skrives til konsistens) læse-og skriveadgang konsistens niveau, kan klare teoretiske tab af højst 1 node per gentagelse gruppe før programmet start bemærke fejlen. Dette forudsætter, at alle de vigtigste mellemrum har også afstemmes læse-og skriveadgang anmodninger.  Følgende er de parametre, vi vil bruge til den udløst klynge:

Enkelt område Cassandra klynge konfiguration:

| Klynge Parameter | Værdi | Bemærkninger |
| ----------------- | ----- | ------- |
| Antallet af knuder (N) | 8   | Antallet af knuder på klyngen |
| Replikering faktor (RF) | 3 | Antallet af kopier af en bestemt række |
| Konsistens niveau (Skriv) | QUORUM[(RF/2) +1) = 2] resultatet af formlen afrundes | Skriver højst 2 replikaer, inden svaret sendes til kalderen; 3 replika er skrevet på et senere ensartet måde. |
| Konsistens niveau (læsning) | QUORUM [(RF/2) + 1 = 2] resultatet af formlen afrundes | Læser 2 replikaer før du sender svar til kalderen. |
| Strategi for gentagelse | NetworkTopologyStrategy se [Replikering af Data](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) i Cassandra dokumentation kan finde flere oplysninger | Forstår installation topologien og placerer replikaer på noder, så alle kopier ikke ender på samme rack |
| Snitch | GossipingPropertyFileSnitch se [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) i Cassandra dokumentation kan finde flere oplysninger | NetworkTopologyStrategy bruger en begrebet snitch til at forstå topologien. GossipingPropertyFileSnitch giver bedre styring i tilknytte hver node til datacenter og rack. Computerklynge bruger derefter sladder for at sprede disse oplysninger. Dette er meget nemmere dynamiske IP-indstillingen i forhold til PropertyFileSnitch |


**Azure overvejelser i forbindelse med Cassandra klynge:** Muligheden for virtuelle Microsoft Azure-computere bruger Azure Blob-lager til disk brugerdata; Azure-lager gemmer 3 kopier af hver enkelt disk for stor holdbarhed. Det betyder, at hver række med data, der er indsat i en Cassandra tabel er allerede gemt i 3 replikaer og dermed data konsistens er allerede taget sig af selvom gentagelse faktor (RF) er 1. Det primære problemet med gentagelse faktor der 1 er, at programmet oplever nedetid, selv hvis en enkelt Cassandra node mislykkes. Hvis en node ned til de problemer (fx hardware, systemfejl software), der genkendes af Azure-strukturen Controller, vil det klargøre en ny node i stedet bruge de samme lagerplads drev. Klargøring af en ny node for at erstatte den gamle liste kan tage et par minutter.  Cassandra opgraderer på samme måde for planlagt vedligeholdelsesaktiviteter som gæst OS ændringer, og programmet ændringer Azure-strukturen Controller udfører rullende opgraderinger for knuderne i klyngen.  Rullende opgraderinger også kan tage ned et par noder ad gangen, og derfor klyngen kan opleve kort nedetid for nogle partitioner. Dataene, der er dog går ikke tabt på grund af den indbyggede redundans Azure-lager.  

Til systemer, der er installeret på Azure, der ikke kræver høj tilgængelighed (fx omkring 99,9, som svarer til 8.76 timer/år, du kan finde oplysninger om [Høj tilgængelighed](http://en.wikipedia.org/wiki/High_availability) ) kan du ikke kunne køre med RF = 1 og konsistens niveau = en.  For programmer med høj tilgængelighedskrav, RF = 3 og konsistens niveau = QUORUM vil tillade ned klokkeslættet for en af noderne en af replikaerne. RF = 1 i traditionelle installationer (fx lokalt) kan ikke bruges på grund af skyldes problemer som mislykkede forsøg på disk tab af data.   

## <a name="multi-region-deployment"></a>Flere områder installation
Det hjælper med at med flere områder installation af feltet uden brug af en hvilken som helst eksterne værktøj Cassandra's data-center-aware gentagelse og konsistens model er beskrevet ovenfor. Dette er helt forskellige fra de traditionelle relationsdatabaser, hvor opsætningen af databasespejling af flere master skriver kan være meget indviklet. Cassandra i et med flere område konfigurere kan hjælpe med brugsscenarier, herunder følgende:

**Afstand baseret installation:** Flere lejer-programmer med Fjern tilknytningen af lejer brugere-til-område, kan havde nytte af flere områder-klynge lav latenstider. For eksempel en learning management systemer til uddannelsesinstitutioner kan installere en fordelt klynge i af USA og Vest USA områder at kunne levere de respektive læreanstalter for transaktions samt analyser. Dataene kan være lokalt ensartet tid læser og skriver og kan være til sidst ensartede på tværs af begge områderne. Der er andre eksempler som medier fordeling, e-handel og noget og alt, der fungerer geografisk koncentreret bruger base er en god use case for denne implementeringsmodel.

**Høj tilgængelighed:** Redundans er en vigtig faktor i nå høj tilgængelighed af software og hardware; Se dokumentkomponent pålidelig skyen systemer på Microsoft Azure få mere at vide. På Microsoft Azure er den kun pålidelig metode til at opnå SAND redundans ved at installere en klynge med flere områder. Programmer kan installeres i en aktiv-aktiv eller aktiv / passiv tilstand, og hvis en af områderne er ned, Azure trafik Manager kan omdirigere trafik til det aktive område.  Med enkelt område installationen, hvis tilgængeligheden er 99,9, en to-område-installation kan nå en tilgængeligheden af 99.9999 beregnes ved formlen: (1-(1-0.999) *(1-0,999))*100); Se ovenstående papiret få mere at vide.

**Nedbrud:** Flere områder Cassandra klynge, kan Hvis korrekt designet, klare katastrofal data center afbrydelser. Hvis et område er nede, kan det program, der er installeret til andre områder starte fungerer slutbrugerne. Som alle andre business løbende-installationer skal programmet være tolerant for nogle resultatet af dataene i den asynkrone rørledning tab af data. Cassandra gør imidlertid at genoprettelsen meget swifter end den tid, det tager ved traditionelle database gendannelsesprocesser. Figur 2 viser typisk flere områder implementeringsmodel med otte noder i hvert område. Begge områder er spejlbilleder af hinanden for samme af vedkommende virkelige design afhænger af arbejdsbelastningen type (fx transaktions eller analytical), frigivne Produktionsordre, RTO, dataenes konsistens og tilgængelighedskrav.

![Flere område installation](./media/virtual-machines-linux-classic-cassandra-nodejs/cassandra-linux2.png)

Figur 2: Flere områder Cassandra installation

### <a name="network-integration"></a>Integration af netværk
Sæt af virtuelle maskiner, der er installeret til private netværk, der er placeret på to områder kommunikerer med hinanden ved hjælp af en VPN-tunnel. VPN-tunnel forbinder to software gateways klargjort i forbindelse med installationsprocessen netværk. Begge områder har samme netværksarkitektur med hensyn til "web" og "data" undernet; Azure netværk tillader oprettelse af så mange undernet efter behov og anvende ACLs efter behov ved netværkssikkerhed. Når du designer klynge topologien blandt data center kommunikation ventetid og økonomiske påvirkningen af netværk trafik skal betragtes som.

### <a name="data-consistency-for-multi-data-center-deployment"></a>Data konsistens til flere Data Center installation
Distribueret installationer skal være opmærksom på klynge topologi påvirkning af overførselshastighed og høj tilgængelighed. RF og konsistens niveau skal udvælges således, at quorum ikke afhænger af tilgængeligheden af alle datacentre.
For et system, der kræver høj konsistens, vil en LOCAL_QUORUM for konsistens niveau (for læser og skriver) Sørg for, at lokale læsning og skrivning er opfyldt fra lokale knuderne, mens data replikeres asynkront til de eksterne datacentre.  Tabel 2 opsummerer konfigurationsoplysninger for den flere områder klynge senere i Skriv op.

**To område Cassandra klynge konfiguration**


| Klynge Parameter | Værdi | Bemærkninger |
| ----------------- | ----- | ------- |
| Antallet af knuder (N) | 8 + 8 | Antallet af knuder på klyngen |
| Replikering faktor (RF) | 3 | Antallet af kopier af en bestemt række |
| Konsistens niveau (Skriv) | LOCAL_QUORUM [(sum(RF)/2) +1) = 4] resultatet af formlen afrundes | 2 noder skrives til det første datacenter synkront; Yderligere 2 noder, der kræves for quorum skrives asynkront til 2. datacentret. |
| Konsistens niveau (læsning) | LOCAL_QUORUM ((RF/2) + 1) = 2, der er resultatet af formlen afrundes ned | Læs anmodninger er opfyldt fra kun ét område; 2 noder læses, inden svaret sendes tilbage til klienten. |
| Strategi for gentagelse | NetworkTopologyStrategy se [Replikering af Data](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) i Cassandra dokumentation kan finde flere oplysninger | Forstår installation topologien og placerer replikaer på noder, så alle kopier ikke ender på samme rack |
| Snitch | GossipingPropertyFileSnitch se [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) i Cassandra dokumentation kan finde flere oplysninger | NetworkTopologyStrategy bruger en begrebet snitch til at forstå topologien. GossipingPropertyFileSnitch giver bedre styring i tilknytte hver node til datacenter og rack. Computerklynge bruger derefter sladder for at sprede disse oplysninger. Dette er meget nemmere dynamiske IP-indstillingen i forhold til PropertyFileSnitch |


##<a name="the-software-configuration"></a>SOFTWAREKONFIGURATION
Følgende softwareversioner bruges under installationen:

<table>
<tr><th>Software</th><th>Kilde</th><th>Version</th></tr>
<tr><td>JRE </td><td>[JRE 8](http://www.oracle.com/technetwork/java/javase/downloads/server-jre8-downloads-2133154.html) </td><td>8U5</td></tr>
<tr><td>JNA </td><td>[JNA](https://github.com/twall/jna) </td><td> 3.2.7</td></tr>
<tr><td>Cassandra</td><td>[Apache Cassandra 2.0.8](http://www.apache.org/dist/cassandra/2.0.8/apache-cassandra-2.0.8-bin.tar.gz)</td><td> 2.0.8</td></tr>
<tr><td>Ubuntu  </td><td>[Microsoft Azure](https://azure.microsoft.com/) </td><td>14.04 LTS</td></tr>
</table>

Da hentning af JRE kræver manuel accept af Oracle licens, du for at forenkle installationen, hente den nødvendige software til skrivebordet til senere overførsel til Ubuntu skabelon billedet vi opretter som en forud for den klynge installation.

Hent den ovenstående software til en velkendt download mappe (fx %TEMP%/downloads i Windows eller ~/Downloads på de fleste Linux fordelingerne eller Mac) på den lokale computer.

### <a name="create-ubuntu-vm"></a>OPRETTE UBUNTU VM
I dette trin over processen opretter vi Ubuntu billede med allerede påkrævede softwaren så billedet kan genbruges til klargøring af flere Cassandra noder.  
####<a name="step-1-generate-ssh-key-pair"></a>TRIN 1: Oprette SSH vigtige par
Azure skal have en offentlig nøgle, der er PEM eller DER-kodet klargøring samtidig X509. Oprette et offentlige/private nøgler par ved hjælp af vejledningen, der er placeret på, hvordan man Brug SSH med Linux på Azure. Hvis du planlægger at bruge putty.exe som SSH klient enten på Windows eller Linux, har du konvertere PEM kodet RSA privat nøgle PPK formatet ved hjælp af puttygen.exe; instruktioner til dette kan findes i den ovenstående webside.

####<a name="step-2-create-ubuntu-template-vm"></a>TRIN 2: Oprette Ubuntu skabelon VM
Hvis du vil oprette skabelonen VM, log på portalen Azure klassisk og bruge følgende rækkefølge: Klik på ny, BEREGN, VIRTUELT, fra GALLERIET, UBUNTU, Ubuntu Server 14.04 LTS, og klik derefter på den højre pil. Se Oprette et virtuelt kører Linux et selvstudium, der beskriver, hvordan du opretter en Linux VM.

Angiv følgende oplysninger på skærmbilledet "virtuelt konfiguration" #1:

<table>
<tr><th>FELTNAVN              </td><td>       FELTVÆRDIEN               </td><td>         BEMÆRKNINGER                </td><tr>
<tr><td>UDGIVELSESDATO VERSION    </td><td> Vælg en dato fra drow ned</td><td></td><tr>
<tr><td>VIRTUELT NAVN    </td><td> cass-skabelon                </td><td> Dette er hostname VM </td><tr>
<tr><td>NIVEAU                     </td><td> STANDARD                        </td><td> Lad være standard              </td><tr>
<tr><td>STØRRELSE                     </td><td> A1                              </td><td>Vælg VM baseret på behovene EY; Lad standardegenskaberne for hertil </td><tr>
<tr><td> NYE BRUGERNAVN           </td><td> localadmin                      </td><td> "admin" er et reserveret brugernavn i Ubuntu 12. xx og efter</td><tr>
<tr><td> GODKENDELSE      </td><td> Klik på afkrydsningsfeltet                 </td><td>Kontrollere, om du vil sikre med en SSH nøgle </td><tr>
<tr><td> CERTIFIKAT             </td><td> filnavnet på det certifikat til offentlig nøgle </td><td> Brug den offentlige nøgle, der er oprettet tidligere</td><tr>
<tr><td> Ny adgangskode   </td><td> stærk adgangskode </td><td> </td><tr>
<tr><td> Bekræft adgangskode   </td><td> stærk adgangskode </td><td></td><tr>
</table>

Angiv følgende oplysninger på skærmbilledet "virtuelt konfiguration" #2:

<table>
<tr><th>FELTNAVN             </th><th> FELTVÆRDIEN                       </th><th> BEMÆRKNINGER                                 </th></tr>
<tr><td> SKYBASERET TJENESTE  </td><td> Oprette en ny tjeneste i skyen    </td><td>Skybaseret tjeneste er en objektbeholder Beregn ressourcer som virtuelle maskiner</td></tr>
<tr><td> SKYEN TJENESTEN DNS-NAVN </td><td>Ubuntu template.cloudapp.net   </td><td>Navngiv maskine agnostic Indlæs belastningsjusteringstjenesten</td></tr>
<tr><td> OMRÅDE/FORBINDELSE GRUPPE/VIRTUELT NETVÆRK </td><td>    Vest USA </td><td> Vælg et område, hvor dine webprogrammer få adgang til Cassandra klynge</td></tr>
<tr><td>LAGERPLADS KONTO </td><td>   Brug standard </td><td>Bruge lagerplads standardkontoen eller en allerede oprettet lagerplads konto i et bestemt område</td></tr>
<tr><td>TILGÆNGELIGHED SÆT </td><td>  Ingen </td><td>  Lade feltet være tomt</td></tr>
<tr><td>SLUTPUNKTER   </td><td>Brug standard </td><td>  Brug af standard SSH konfiguration </td></tr>
</table>

Klik på højre pil, beholde standardindstillingerne på skærmbilledet #3, og klik på knappen "Kontrollér" for at fuldføre VM klargøring af processen. VM med navnet "ubuntu-skabelonen" skal være i statussen "kører" efter et par minutter.

###<a name="install-the-necessary-software"></a>INSTALLERE DEN NØDVENDIGE SOFTWARE
####<a name="step-1-upload-tarballs"></a>TRIN 1: Overfør tarballs
Bruger scp eller pscp, kopiere tidligere hentede softwaren til ~/downloads directory ved hjælp af formatet følgende kommando:

#####<a name="pscp-server-jre-8u5-linux-x64targz-localadminhk-cas-templatecloudappnethomelocaladmindownloadsserver-jre-8u5-linux-x64targz"></a>pscp server-jre-8u5-linux-x64.tar.gzlocaladmin@hk-cas-template.cloudapp.net:/home/localadmin/downloads/server-jre-8u5-linux-x64.tar.gz

Gentag ovenstående kommando for JRE samt som for Cassandra bit.

####<a name="step-2-prepare-the-directory-structure-and-extract-the-archives"></a>TRIN 2: Forberede mappestrukturen og udtrække Arkiver
Log på VM og oprette mappestrukturen og udtrække software som superbruger ved hjælp af nedenstående fest script:

    #!/bin/bash
    CASS_INSTALL_DIR="/opt/cassandra"
    JRE_INSTALL_DIR="/opt/java"
    CASS_DATA_DIR="/var/lib/cassandra"
    CASS_LOG_DIR="/var/log/cassandra"
    DOWNLOADS_DIR="~/downloads"
    JRE_TARBALL="server-jre-8u5-linux-x64.tar.gz"
    CASS_TARBALL="apache-cassandra-2.0.8-bin.tar.gz"
    SVC_USER="localadmin"

    RESET_ERROR=1
    MKDIR_ERROR=2

    reset_installation ()
    {
       rm -rf $CASS_INSTALL_DIR 2> /dev/null
       rm -rf $JRE_INSTALL_DIR 2> /dev/null
       rm -rf $CASS_DATA_DIR 2> /dev/null
       rm -rf $CASS_LOG_DIR 2> /dev/null
    }
    make_dir ()
    {
       if [ -z "$1" ]
       then
          echo "make_dir: invalid directory name"
          exit $MKDIR_ERROR
       fi

       if [ -d "$1" ]
       then
          echo "make_dir: directory already exists"
          exit $MKDIR_ERROR
       fi

       mkdir $1 2>/dev/null
       if [ $? != 0 ]
       then
          echo "directory creation failed"
          exit $MKDIR_ERROR
       fi
    }

    unzip()
    {
       if [ $# == 2 ]
       then
          tar xzf $1 -C $2
       else
          echo "archive error"
       fi

    }

    if [ -n "$1" ]
    then
       SVC_USER=$1
    fi

    reset_installation
    make_dir $CASS_INSTALL_DIR
    make_dir $JRE_INSTALL_DIR
    make_dir $CASS_DATA_DIR
    make_dir $CASS_LOG_DIR

    #unzip JRE and Cassandra
    unzip $HOME/downloads/$JRE_TARBALL $JRE_INSTALL_DIR
    unzip $HOME/downloads/$CASS_TARBALL $CASS_INSTALL_DIR

    #Change the ownership to the service credentials

    chown -R $SVC_USER:$GROUP $CASS_DATA_DIR
    chown -R $SVC_USER:$GROUP $CASS_LOG_DIR
    echo "edit /etc/profile to add JRE to the PATH"
    echo "installation is complete"


Hvis du indsætter dette script i vim vindue, skal du sørge for at fjerne den vognretur ('\r ") ved hjælp af følgende kommando:

    tr -d '\r' <infile.sh >outfile.sh

####<a name="step-3-edit-etcprofile"></a>Trin 3: Rediger osv/profil
Tilføj følgende sidst:

    JAVA_HOME=/opt/java/jdk1.8.0_05
    CASS_HOME= /opt/cassandra/apache-cassandra-2.0.8
    PATH=$PATH:$HOME/bin:$JAVA_HOME/bin:$CASS_HOME/bin
    export JAVA_HOME
    export CASS_HOME
    export PATH

####<a name="step-4-install-jna-for-production-systems"></a>Trin 4: Installer JNA til fremstilling systemer
Brug følgende kommandosekvens: følgende kommando skal installeres jna 3.2.7.jar og jna-platform-3.2.7.jar til /usr/share.java directory sudo Hovedgaden-få installere libjna java

Oprette symbolic links i $CASS_HOME/bibliotek directory, så Cassandra startscript kan finde disse krukker:

    ln -s /usr/share/java/jna-3.2.7.jar $CASS_HOME/lib/jna.jar

    ln -s /usr/share/java/jna-platform-3.2.7.jar $CASS_HOME/lib/jna-platform.jar

####<a name="step-5-configure-cassandrayaml"></a>Trin 5: Konfigurere cassandra.yaml
Redigere cassandra.yaml på hver VM til at afspejle konfiguration, der bruges til alle de virtuelle-computere [vi vil tilpasse dette under den faktiske klargøringen]:

<table>
<tr><th>Feltnavn   </th><th> Værdi  </th><th> Bemærkninger </th></tr>
<tr><td>cluster_name </td><td>  "CustomerService"   </td><td> Brug det navn, afspejler din installation</td></tr>
<tr><td>listen_address  </td><td>[lade feltet være tomt]   </td><td> Slette "localhost" </td></tr>
<tr><td>rpc_addres   </td><td>[lade feltet være tomt]  </td><td> Slette "localhost" </td></tr>
<tr><td>frø   </td><td>"10.1.2.4, 10.1.2.6, 10.1.2.8" </td><td>Liste over alle de IP-adresser der er angivet som frø.</td></tr>
<tr><td>endpoint_snitch </td><td> org.apache.cassandra.locator.GossipingPropertyFileSnitch </td><td> Det bruges af NetworkTopologyStrateg til udlede datacenteret og rack VM</td></tr>
</table>

####<a name="step-6-capture-the-vm-image"></a>Trin 6: Registrere VM billedet
Log på den virtuelle maskine ved hjælp af hostname (hk-nøglecentre-template.cloudapp.net) og SSH privat nøgle tidligere har oprettet. Se, hvordan du kan bruge SSH med Linux på Azure få mere at vide om, hvordan du logger på ved hjælp af kommandoen ssh eller putty.exe.

Udfør følgende sekvens af handlinger til at tage et skærmbillede:
#####<a name="1-deprovision"></a>1. deprovision
Brug kommandoen "sudo waagent – deprovision + bruger" til at fjerne Virtual Machine forekomst bestemte oplysninger. Se til, [hvordan du kan registrere en Linux virtuel maskine](virtual-machines-linux-classic-capture-image.md) til brug som en skabelon flere oplysninger om image capture-processen.

#####<a name="2-shutdown-the-vm"></a>2: lukning VM
Sørg for, at den virtuelle maskine er fremhævet, og klik på linket lukning fra kommandolinjen nederst.

#####<a name="3-capture-the-image"></a>3: tage et skærmbillede
Sørg for, at den virtuelle maskine fremhæves, og klikke på linket CAPTURE fra kommandolinjen nederst. Navngive en billede (fx hk-cas-2-08-ub-14-04-2014071) det næste skærmbillede, relevante billede beskrivelse, og klik på "markeringen" for at afslutte processen CAPTURE.

Det kan tage et par sekunder og billedet skal være tilgængelig i sektionen mine billeder i billedgalleriet. Kilden VM, bliver automatisk delated når billedet er blevet hentet.

##<a name="single-region-deployment-process"></a>Enkelt område installationsprocessen
**Trin 1: oprette det virtuelle netværk** Log på portalen Azure klassisk og oprette et virtuelt netværk med Vis attributter i tabellen. Se [konfigurere et virtuelt netværk Cloud-Only i portalen Azure klassisk](../virtual-network/virtual-networks-create-vnet-classic-portal.md) til detaljerede trin i processen.      

<table>
<tr><th>VM attributnavn</th><th>Værdi</th><th>Bemærkninger</th></tr>
<tr><td>Navn</td><td>vnet-cass-Vest-os</td><td></td></tr>
<tr><td>Område</td><td>Vest USA</td><td></td></tr>
<tr><td>DNS-servere </td><td>Ingen</td><td>Ignorere dette, som vi ikke bruger en DNS-Server</td></tr>
<tr><td>Konfigurere en VPN-forbindelse til punkt-til-websted</td><td>Ingen</td><td> Ignorere dette</td></tr>
<tr><td>Konfigurere en VPN til-websted</td><td>Nnone</td><td> Ignorere dette</td></tr>
<tr><td>Adresseområde</td><td>10.1.0.0/16</td><td></td></tr>
<tr><td>Start-IP-</td><td>10.1.0.0</td><td></td></tr>
<tr><td>CIDR </td><td>/ 16 (65531)</td><td></td></tr>
</table>

Tilføj de følgende undernet:

<table>
<tr><th>Navn</th><th>Start-IP-</th><th>CIDR</th><th>Bemærkninger</th></tr>
<tr><td>Web</td><td>10.1.1.0</td><td>/ 24 (251)</td><td>Undernet til webfarmen</td></tr>
<tr><td>data</td><td>10.1.2.0</td><td>/ 24 (251)</td><td>Undernet til noderne database</td></tr>
</table>

Du kan beskytte data og Web undernet via netværk sikkerhedsgrupper beskrivelser af som er uden for området for denne artikel.  

**Trin 2: Klargør virtuelle maskiner** Ved hjælp af det billede, der tidligere har oprettet, vi opretter følgende virtuelle maskiner i skyen server "hk-c-svc-Vest" og binder dem til de respektive undernet, som vist nedenfor:

<table>
<tr><th>Computernavn    </th><th>Undernet </th><th>IP-adresse </th><th>Tilgængelighed sæt</th><th>DC/Rack</th><th>Frø?</th></tr>
<tr><td>HK-c1-Vest-os   </td><td>data   </td><td>10.1.2.4   </td><td>HK-c-aset-1    </td><td>DC = WESTUS rack = rack1 </td><td>Ja</td></tr>
<tr><td>HK-c2-Vest-os   </td><td>data   </td><td>10.1.2.5   </td><td>HK-c-aset-1    </td><td>DC = WESTUS rack = rack1 </td><td>Nej </td></tr>
<tr><td>HK-c3-Vest-os   </td><td>data   </td><td>10.1.2.6   </td><td>HK-c-aset-1    </td><td>DC = WESTUS rack = rack2 </td><td>Ja</td></tr>
<tr><td>HK-c4-Vest-os   </td><td>data   </td><td>10.1.2.7   </td><td>HK-c-aset-1    </td><td>DC = WESTUS rack = rack2 </td><td>Nej </td></tr>
<tr><td>HK-c5-Vest-os   </td><td>data   </td><td>10.1.2.8   </td><td>HK-c-aset-2    </td><td>DC = WESTUS rack = rack3 </td><td>Ja</td></tr>
<tr><td>HK-c6-Vest-os   </td><td>data   </td><td>10.1.2.9   </td><td>HK-c-aset-2    </td><td>DC = WESTUS rack = rack3 </td><td>Nej </td></tr>
<tr><td>HK-c7-Vest-os   </td><td>data   </td><td>10.1.2.10  </td><td>HK-c-aset-2    </td><td>DC = WESTUS rack = rack4 </td><td>Ja</td></tr>
<tr><td>HK-c8-Vest-os   </td><td>data   </td><td>10.1.2.11  </td><td>HK-c-aset-2    </td><td>DC = WESTUS rack = rack4 </td><td>Nej </td></tr>
<tr><td>HK-w1-Vest-os   </td><td>Web    </td><td>10.1.1.4   </td><td>HK-w-aset-1    </td><td>                       </td><td>I/T.</td></tr>
<tr><td>HK-w2-Vest-os   </td><td>Web    </td><td>10.1.1.5   </td><td>HK-w-aset-1    </td><td>                       </td><td>I/T.</td></tr>
</table>

Oprettelse af ovenstående liste af FOS kræver følgende fremgangsmåde:

1.  Oprette en tom skybaseret tjeneste i et bestemt område
2.  Oprette en VM fra tidligere skærmklippet, og vedhæft den til det virtuelle netværk, der er oprettet tidligere, eller Gentag dette for alle FOS
3.  Tilføje en intern justering af belastning til skybaseret tjeneste, og vedhæft den til "-data" undernettet
4.  Hver VM tidligere har oprettet, kan du tilføje et Indlæs afstemmes slutpunkt for thrift trafik via et Indlæs afstemmes sæt, der er knyttet til den tidligere oprettet interne justering af belastning

Ovenstående proces kan udføres ved hjælp af Azure klassisk portalen; Brug en Windows-computer (Brug en VM på Azure, hvis du ikke har adgang til en Windows-computer), Brug følgende PowerShell-script til at klargøre alle 8 FOS automatisk.

**Listen 1: PowerShell-script til klargøring af virtuelle maskiner**

        #Tested with Azure Powershell - November 2014
        #This powershell script deployes a number of VMs from an existing image inside an Azure region
        #Import your Azure subscription into the current Powershell session before proceeding
        #The process: 1. create Azure Storage account, 2. create virtual network, 3.create the VM template, 2. crate a list of VMs from the template

        #fundamental variables - change these to reflect your subscription
        $country="us"; $region="west"; $vnetName = "your_vnet_name";$storageAccount="your_storage_account"
        $numVMs=8;$prefix = "hk-cass";$ilbIP="your_ilb_ip"
        $subscriptionName = "Azure_subscription_name";
        $vmSize="ExtraSmall"; $imageName="your_linux_image_name"
        $ilbName="ThriftInternalLB"; $thriftEndPoint="ThriftEndPoint"

        #generated variables
        $serviceName = "$prefix-svc-$region-$country"; $azureRegion = "$region $country"

        $vmNames = @()
        for ($i=0; $i -lt $numVMs; $i++)
        {
           $vmNames+=("$prefix-vm"+($i+1) + "-$region-$country" );
        }

        #select an Azure subscription already imported into Powershell session
        Select-AzureSubscription -SubscriptionName $subscriptionName -Current
        Set-AzureSubscription -SubscriptionName $subscriptionName -CurrentStorageAccountName $storageAccount

        #create an empty cloud service
        New-AzureService -ServiceName $serviceName -Label "hkcass$region" -Location $azureRegion
        Write-Host "Created $serviceName"

        $VMList= @()   # stores the list of azure vm configuration objects
        #create the list of VMs
        foreach($vmName in $vmNames)
        {
           $VMList += New-AzureVMConfig -Name $vmName -InstanceSize ExtraSmall -ImageName $imageName |
           Add-AzureProvisioningConfig -Linux -LinuxUser "localadmin" -Password "Local123" |
           Set-AzureSubnet "data"
        }

        New-AzureVM -ServiceName $serviceName -VNetName $vnetName -VMs $VMList

        #Create internal load balancer
        Add-AzureInternalLoadBalancer -ServiceName $serviceName -InternalLoadBalancerName $ilbName -SubnetName "data" -StaticVNetIPAddress "$ilbIP"
        Write-Host "Created $ilbName"
        #Add add the thrift endpoint to the internal load balancer for all the VMs
        foreach($vmName in $vmNames)
        {
            Get-AzureVM -ServiceName $serviceName -Name $vmName |
                Add-AzureEndpoint -Name $thriftEndPoint -LBSetName "ThriftLBSet" -Protocol tcp -LocalPort 9160 -PublicPort 9160 -ProbePort 9160 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ilbName |
                Update-AzureVM

            Write-Host "created $vmName"     
        }

**Trin 3: Konfigurere Cassandra på hver VM**

Log på VM og udføre følgende:

* Redigere $CASS_HOME/conf/cassandra-rackdc.properties for at angive data center og rack egenskaber:

       dc =EASTUS, rack =rack1

* Redigere cassandra.yaml for at konfigurere frø noder som nedenfor:

       Seeds: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10"

**Trin 4: Starte FOS og teste klyngen**

Log på en af noderne (fx hk-c1-Vest-OS) og køre følgende kommando for at se status for klyngen:

       nodetool –h 10.1.2.4 –p 7199 status

Du burde se visningen ligner, under for en 8-node klynge:

<table>
<tr><th>Status</th><th>Adresse  </th><th>Indlæsning   </th><th>Tokens </th><th>Ejer </th><th>Host-ID  </th><th>Rack</th></tr>
<tr><th>FJERN  </td><td>10.1.2.4   </td><td>87.81 KB   </td><td>256    </td><td>38.0%  </td><td>GUID (fjernet)</td><td>rack1</td></tr>
<tr><th>FJERN  </td><td>10.1.2.5   </td><td>41.08 KB   </td><td>256    </td><td>68.9%  </td><td>GUID (fjernet)</td><td>rack1</td></tr>
<tr><th>FJERN  </td><td>10.1.2.6   </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>GUID (fjernet)</td><td>rack2</td></tr>
<tr><th>FJERN  </td><td>10.1.2.7   </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>GUID (fjernet)</td><td>rack2</td></tr>
<tr><th>FJERN  </td><td>10.1.2.8   </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>GUID (fjernet)</td><td>rack3</td></tr>
<tr><th>FJERN  </td><td>10.1.2.9   </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>GUID (fjernet)</td><td>rack3</td></tr>
<tr><th>FJERN  </td><td>10.1.2.10  </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>GUID (fjernet)</td><td>rack4</td></tr>
<tr><th>FJERN  </td><td>10.1.2.11  </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>GUID (fjernet)</td><td>rack4</td></tr>
</table>

## <a name="test-the-single-region-cluster"></a>Teste enkelt område klynge
Brug følgende trin til at teste klyngen:

1.    Ved hjælp af Powershell kommandoen Get-AzureInternalLoadbalancer cmdlet accepterer hente IP-adressen på den interne justering af belastning (f.eks.  10.1.2.101). Syntaksen for kommandoen er vist nedenfor: Get-AzureLoadbalancer – ServiceName "hk-c-svc-Vest-os" [viser oplysninger om den interne justering af belastning sammen med dens IP-adresse]
2.  Log på webfarmen VM (fx hk-w1-Vest-OS) ved hjælp af trykfarver eller ssh
3.  Udføre $CASS_HOME/placering/cqlsh 10.1.2.101 9160
4.  Brug følgende CQL kommandoer til at bekræfte, om klyngen virker:

        CREATE KEYSPACE customers_ks WITH REPLICATION = { 'class' : 'SimpleStrategy', 'replication_factor' : 3 };
        USE customers_ks;
        CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');

        SELECT * FROM Customers;

Du burde se en skærm som den nedenfor:

<table>
  <tr><th> customer_id </th><th> Fornavn </th><th> Efternavn </th></tr>
  <tr><td> 1 </td><td> John </td><td> Larsen </td></tr>
  <tr><td> 2 </td><td> Jane </td><td> Larsen </td></tr>
</table>

Vær opmærksom på, at keyspace oprettede i trin 4 anvender SimpleStrategy med en replication_factor af 3. SimpleStrategy anbefales til enkelt data center installationer mens NetworkTopologyStrategy til flere data center installationer. En replication_factor af 3 giver tolerance for node mislykkede forsøg.

##<a id="tworegion"> </a>Med flere område installationsprocessen
Derved udnytte den enkelt område installationen er fuldført, og Gentag den samme fremgangsmåde for at installere det andet område. Vigtige forskellen mellem en og flere område installation er opsætningen af VPN-tunnel for kommunikation mellem område; Vi starter med netværksinstallationen af, klargøre FOS og konfigurere Cassandra.

###<a name="step-1-create-the-virtual-network-at-the-2nd-region"></a>Trin 1: Oprette det virtuelle netværk på området 2.
Log på portalen Azure klassisk og oprette et virtuelt netværk med Vis attributter i tabellen. Se [konfigurere et virtuelt netværk Cloud-Only i portalen Azure klassisk](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) til detaljerede trin i processen.      

<table>
<tr><th>Attributnavn    </th><th>Værdi    </th><th>Bemærkninger</th></tr>
<tr><td>Navn    </td><td>vnet-cass-Øst-os</td><td></td></tr>
<tr><td>Område  </td><td>Indtastning af østasiatiske USA</td><td></td></tr>
<tr><td>DNS-servere     </td><td></td><td>Ignorere dette, som vi ikke bruger en DNS-Server</td></tr>
<tr><td>Konfigurere en VPN-forbindelse til punkt-til-websted</td><td></td><td>     Ignorere dette</td></tr>
<tr><td>Konfigurere en VPN til-websted</td><td></td><td>      Ignorere dette</td></tr>
<tr><td>Adresseområde   </td><td>10.2.0.0/16</td><td></td></tr>
<tr><td>Start-IP- </td><td>10.2.0.0   </td><td></td></tr>
<tr><td>CIDR    </td><td>/ 16 (65531)</td><td></td></tr>
</table>

Tilføj de følgende undernet:
<table>
<tr><th>Navn    </th><th>Start-IP-    </th><th>CIDR   </th><th>Bemærkninger</th></tr>
<tr><td>Web </td><td>10.2.1.0   </td><td>/ 24 (251)  </td><td>Undernet til webfarmen</td></tr>
<tr><td>data    </td><td>10.2.2.0   </td><td>/ 24 (251)  </td><td>Undernet til noderne database</td></tr>
</table>


###<a name="step-2-create-local-networks"></a>Trin 2: Oprette lokale netværk
Et lokalnetværk i Azure virtuelt netværk er en proxy-adresseområde, der er tilknyttet en fjernplacering, herunder en privat sky eller et andet Azure område. Denne proxy-adresseområde er bundet til en ekstern gateway for routing-netværk til de rigtige netværk destinationer. Se [konfigurere en VNet til VNet forbindelse](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) yderligere oplysninger om oprettelse af VNET-VNET forbindelse.

Oprette to lokale netværk per følgende oplysninger:

| Netværksnavn | VPN-Gatewayadresse | Adresseområde | Bemærkninger |
| ------------ | ------------------- | ------------- | ------- |
| HK-lnet-Map-to-East-US | 23.1.1.1  | 10.2.0.0/16   | Giv en pladsholder Gatewayadresse under oprettelse af det lokalnetværk. Reelle gateway-adressen er udfyldt, når gatewayen er oprettet. Sørg for, at adresseområdet svarer nøjagtigt til de respektive remote VNET; i dette tilfælde oprettet VNET i området til indtastning af østasiatiske dk. |
| HK-lnet-Map-to-West-US | 23.2.2.2  | 10.1.0.0/16   | Giv en pladsholder Gatewayadresse under oprettelse af det lokalnetværk. Reelle gateway-adressen er udfyldt, når gatewayen er oprettet. Sørg for, at adresseområdet svarer nøjagtigt til de respektive remote VNET; i dette tilfælde oprettet VNET i området Vest USA. |


###<a name="step-3-map-local-network-to-the-respective-vnets"></a>Trin 3: Opret "Lokale" forbindelse til de respektive VNETs
Markér hver vnet Azure klassisk portalen, klik på "Konfigurer", Markér "Opret forbindelse til det lokale netværk", og vælg de lokalnetværk per følgende oplysninger:


| Virtuelt netværk | Lokale netværk |
| --------------- | ------------- |
| HK-vnet-Vest-os | HK-lnet-Map-to-East-US |
| HK-vnet-Øst-os | HK-lnet-Map-to-West-US |


###<a name="step-4-create-gateways-on-vnet1-and-vnet2"></a>Trin 4: Oprette Gateways på VNET1 og VNET2
Klik på Opret GATEWAY, der udløser gatewayen VPN klargøring proces dashboard over begge de virtuelle netværk. Efter et par minutter skal dashboardet på hvert enkelt virtuelle netværk vise faktisk gateway-adressen.

###<a name="step-5-update-local-networks-with-the-respective-gateway-addresses"></a>Trin 5: Opdater "Lokale" netværk med de respektive "Gateway" adresser###
Redigere begge de lokale netværk for at erstatte pladsholder gateway IP-adressen med blot klargjort gateways reelle IP-adresse. Brug følgende tilknytning:

<table>
<tr><th>Lokale netværk    </th><th>Virtuelt netværksgateway</th></tr>
<tr><td>HK-lnet-Map-to-East-US </td><td>Gatewayen af hk-vnet-Vest-os</td></tr>
<tr><td>HK-lnet-Map-to-West-US </td><td>Gatewayen af hk-vnet-Øst-os</td></tr>
</table>

###<a name="step-6-update-the-shared-key"></a>Trin 6: Opdatere den delte nøgle
Brug følgende Powershell-script til at opdatere IPSec-nøglen for hver VPN gateway [Brug tasten risvin for begge gateways]: Set-AzureVNetGatewayKey - VNetName hk-vnet-Øst-os - LocalNetworkSiteName hk-lnet-map-to-west-us - SharedKey D9E76BKK sæt-AzureVNetGatewayKey - VNetName hk-vnet-Vest-os - LocalNetworkSiteName hk-lnet-map-to-east-us - SharedKey D9E76BKK

###<a name="step-7-establish-the-vnet-to-vnet-connection"></a>Trin 7: Oprette VNET-VNET
Brug menuen "DASHBOARD" af de virtuelle netværk fra Azure klassisk portal, til at oprette forbindelse til gateway-til-gateway. Bruge menupunkter "Opret forbindelse" i den nederste værktøjslinje. Når du har et par minutter skal dashboardet vise forbindelsesoplysningerne grafisk.

###<a name="step-8-create-the-virtual-machines-in-region-2"></a>Trin 8: Oprette virtuelle maskiner i område #2
Oprette Ubuntu billedet, som det er beskrevet i område #1 installation ved at følge de samme trin eller Kopiér Virtuelle billedfilen til kontoen Azure-lager, der er placeret i område #2 og billedet. Brug dette billede, og Opret følgende liste over virtuelle maskiner til en ny tjeneste i skyen hk-c-svc-Øst-os:


| Computernavn | Undernet | IP-adresse | Tilgængelighed sæt | DC/Rack | Frø? |
| ------------ | ------ | ---------- | ---------------- | ------- | ----- |
| HK-c1-Øst-os | data  | 10.2.2.4   | HK-c-aset-1      | DC = EASTUS rack = rack1 | Ja |
| HK-c2-Øst-os | data  | 10.2.2.5   | HK-c-aset-1      | DC = EASTUS rack = rack1 | Nej  |
| HK-c3-Øst-os | data  | 10.2.2.6   | HK-c-aset-1      | DC = EASTUS rack = rack2 | Ja |
| HK-c5-Øst-os | data  | 10.2.2.8   | HK-c-aset-2      | DC = EASTUS rack = rack3 | Ja |
| HK-c6-Øst-os | data  | 10.2.2.9   | HK-c-aset-2      | DC = EASTUS rack = rack3 | Nej  |
| HK-c7-Øst-os | data  | 10.2.2.10  | HK-c-aset-2      | DC = EASTUS rack = rack4 | Ja |
| HK-c8-Øst-os | data  | 10.2.2.11  | HK-c-aset-2      | DC = EASTUS rack = rack4 | Nej  |
| HK-w1-Øst-os | Web   | 10.2.1.4   | HK-w-aset-1      | I/T.                    | I/T. |
| HK-w2-Øst-os | Web   | 10.2.1.5   | HK-w-aset-1      | I/T.                    | I/T. |


Følg samme fremgangsmåde som område #1, men bruge 10.2.xxx.xxx-adresseområde.

###<a name="step-9-configure-cassandra-on-each-vm"></a>Trin 9: Konfigurere Cassandra på hver VM
Log på VM og udføre følgende:

1. Redigere $CASS_HOME/conf/cassandra-rackdc.properties for at angive egenskaberne data center og rack i formatet: dc = EASTUS rack = rack1
2. Redigere cassandra.yaml for at konfigurere frø noder: frø: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10,10.2.2.4,10.2.2.6,10.2.2.8,10.2.2.10"

###<a name="step-10-start-cassandra"></a>Trin 10: Start Cassandra
Log på hver VM og starte Cassandra i baggrunden ved at køre følgende kommando: $CASS_HOME/placering/cassandra

## <a name="test-the-multi-region-cluster"></a>Teste flere områder klynge
På nuværende tidspunkt er blevet installeret Cassandra til 16 noder med 8 noder i hvert område for Azure. Disse noder er i den samme klynge i henhold til den almindelige klyngenavn og frø node konfigurationen. Brug følgende fremgangsmåde til at teste klyngen:

###<a name="step-1-get-the-internal-load-balancer-ip-for-both-the-regions-using-powershell"></a>Trin 1: Hente IP-adresse med interne Indlæs belastningsjusteringstjenesten for begge de områder, ved hjælp af PowerShell
- Get-AzureInternalLoadbalancer - ServiceName "hk-c-svc-Vest-os"
- Get-AzureInternalLoadbalancer - ServiceName "hk-c-svc-Øst-os"  

    Bemærk IP-adresserne (fx Vest - 10.1.2.101, Øst - 10.2.2.101) vises.

###<a name="step-2-execute-the-following-in-the-west-region-after-logging-into-hk-w1-west-us"></a>Trin 2: Udføre følgende i region Vest når du har logget ind i hk-w1-Vest-os
1.    Udføre $CASS_HOME/placering/cqlsh 10.1.2.101 9160
2.  Udfør følgende CQL kommandoer:

        CREATE KEYSPACE customers_ks
        WITH REPLICATION = { 'class' : 'NetworkToplogyStrategy', 'WESTUS' : 3, 'EASTUS' : 3};
        USE customers_ks;
        CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
        SELECT * FROM Customers;

Du burde se en skærm som den nedenfor:

| customer_id | Fornavn | Efternavn |
| ----------- | --------- | -------- |
| 1           | John      | Larsen      |
| 2           | Jane      | Larsen      |


###<a name="step-3-execute-the-following-in-the-east-region-after-logging-into-hk-w1-east-us"></a>Trin 3: Udføre følgende i region Øst når du har logget ind i hk-w1-Øst-os:
1.    Udføre $CASS_HOME/placering/cqlsh 10.2.2.101 9160
2.  Udfør følgende CQL kommandoer:

        USE customers_ks;
        CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
        SELECT * FROM Customers;

Du burde se samme skærm, som set for region Vest:


| customer_id | Fornavn | Efternavn   |
|------------ | --------- | ---------- |
| 1           | John      | Larsen        |
| 2           | Jane      | Larsen        |


Udføre et par flere indsættes, og se, at de får replikeres til vest-os en del af klyngen.

## <a name="test-cassandra-cluster-from-nodejs"></a>Test Cassandra klynge fra Node.js
Ved hjælp af en af de Linux FOS crated i "Internettet" klasse tidligere, vi vil udføre en simpel Node.js script for at læse de tidligere indsatte data

**Trin 1: Installere Node.js og Cassandra klient**

1. Installere Node.js og npm
2. Installere node pakke "cassandra-klient" ved hjælp af npm
3. Udfør følgende script kommandoprompten shell som viser de hentede data json tekststrengen:

        var pooledCon = require('cassandra-client').PooledConnection;
        var ksName = "custsupport_ks";
        var cfName = "customers_cf";
        var hostList = ['internal_loadbalancer_ip:9160'];
        var ksConOptions = { hosts: hostList,
                             keyspace: ksName, use_bigints: false };

        function createKeyspace(callback){
           var cql = 'CREATE KEYSPACE ' + ksName + ' WITH strategy_class=SimpleStrategy AND strategy_options:replication_factor=1';
           var sysConOptions = { hosts: hostList,  
                                 keyspace: 'system', use_bigints: false };
           var con = new pooledCon(sysConOptions);
           con.execute(cql,[],function(err) {
           if (err) {
             console.log("Failed to create Keyspace: " + ksName);
             console.log(err);
           }
           else {
             console.log("Created Keyspace: " + ksName);
             callback(ksConOptions, populateCustomerData);
           }
           });
           con.shutdown();
        }

        function createColumnFamily(ksConOptions, callback){
          var params = ['customers_cf','custid','varint','custname',
                        'text','custaddress','text'];
          var cql = 'CREATE COLUMNFAMILY ? (? ? PRIMARY KEY,? ?, ? ?)';
        var con =  new pooledCon(ksConOptions);
          con.execute(cql,params,function(err) {
              if (err) {
                 console.log("Failed to create column family: " + params[0]);
                 console.log(err);
              }
              else {
                 console.log("Created column family: " + params[0]);
                 callback();
              }
          });
          con.shutdown();
        }

        //populate Data
        function populateCustomerData() {
           var params = ['John','Infinity Dr, TX', 1];
           updateCustomer(ksConOptions,params);

           params = ['Tom','Fermat Ln, WA', 2];
           updateCustomer(ksConOptions,params);
        }

        //update will also insert the record if none exists
        function updateCustomer(ksConOptions,params)
        {
          var cql = 'UPDATE customers_cf SET custname=?,custaddress=? where custid=?';
          var con = new pooledCon(ksConOptions);
          con.execute(cql,params,function(err) {
              if (err) console.log(err);
              else console.log("Inserted customer : " + params[0]);
          });
          con.shutdown();
        }

        //read the two rows inserted above
        function readCustomer(ksConOptions)
        {
          var cql = 'SELECT * FROM customers_cf WHERE custid IN (1,2)';
          var con = new pooledCon(ksConOptions);
          con.execute(cql,[],function(err,rows) {
              if (err)
                 console.log(err);
              else
                 for (var i=0; i<rows.length; i++)
                    console.log(JSON.stringify(rows[i]));
            });
           con.shutdown();
        }

        //exectue the code
        createKeyspace(createColumnFamily);
        readCustomer(ksConOptions)


## <a name="conclusion"></a>Konklusion
Microsoft Azure er en fleksibel platform, som muliggør kørslen af både Microsoft samt open source-software, som vist ved denne opgave. Meget tilgængelige Cassandra klynger kan installeres på et enkelt datacenter gennem sprede for knuderne klynge på tværs af flere fejl domæner. Cassandra klynger kan også installeres på tværs af flere geografisk afstand Azure områder til nedbrud bevis systemer. Azure og Cassandra aktiverer sammen konstruktion af meget SVG, høj tilgængelighed og nedbrud genoprettelige skytjenester det er nødvendigt ved dags internet skalere tjenester.  

##<a name="references"></a>Referencer##
- [http://cassandra.Apache.org](http://cassandra.apache.org)
- [http://www.datastax.com](http://www.datastax.com)
- [http://www.nodejs.org](http://www.nodejs.org)
