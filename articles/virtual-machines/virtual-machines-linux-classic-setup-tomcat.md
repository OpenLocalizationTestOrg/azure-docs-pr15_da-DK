<properties
    pageTitle="Konfigurere Apache Tomcat på en Linux VM | Microsoft Azure"
    description="Lær, hvordan du konfigurerer Apache Tomcat7 ved hjælp af en Azure virtuelt (VM), der kører Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="NingKuang"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="ningk"/>

#<a name="how-to-set-up-tomcat7-on-a-linux-virtual-machine-with-microsoft-azure"></a>Hvordan du konfigurerer Tomcat7 på en Linux virtuel maskine med Microsoft Azure

Apache Tomcat (eller blot Tomcat, tidligere også Jakarta Tomcat) er en webserver Åbn kilde og servlet objektbeholder udviklet af Apache Software Foundation (afrikansk). Tomcat implementerer Java Servlet og JavaServer sider (JSP) specifikationer fra Sun Microsystems og giver en ren Java HTTP web server-miljø, til at køre Java-kode. Den nemmeste konfiguration kører Tomcat i en enkelt operativsystem proces. Denne proces kører en Java virtual machine (JVM). Alle HTTP-anmodninger fra en browser til Tomcat behandles som en separat tråd i processen Tomcat.  

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


I denne vejledning, skal du installere tomcat7 på et billede af Linux og installerer det i Microsoft Azure.  

Du kan få mere at vide:  

-   Hvordan du opretter en virtuel maskine i Azure.
-   Sådan forberedes tomcat7 den virtuelle maskine.
-   Sådan installeres tomcat7.

Det antages, at læseren allerede har et Azure-abonnement.  Hvis du ikke du kan tilmelde dig en gratis prøveversion på [http://azure.microsoft.com](https://azure.microsoft.com/). Hvis du har et MSDN-abonnement, skal du se [Microsoft Azure særlige priser: MSDN, MPN og Bizspark fordele](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Du kan få mere at vide om Azure, [Hvad er Azure?](https://azure.microsoft.com/overview/what-is-azure/).

Dette emne antages det, at du har grundlæggende kendskab til tomcat og Linux.  

##<a name="phase-1-create-an-image"></a>Fase 1: Oprette et billede
I denne fase opretter du en virtuel maskine, ved hjælp af et billede af Linux i Azure.  

###<a name="step-1-generate-an-ssh-authentication-key"></a>Trin 1: Oprette en SSH godkendelsesnøgle
SSH er et vigtigt værktøj til administratorer. Konfiguration af access-sikkerhed, der er baseret på en hr-fastsat adgangskode er dog ikke den bedste fremgangsmåde. Hackere kan bryde ind i dit system, der er baseret på et brugernavn og en svage adgangskode.

Det positive er, at der er en måde at forlade fjernadgang åben og behøver ikke at bekymre dig om adgangskoder. Metoden består af godkendelse med asymmetrisk kryptering. Brugerens privat nøgle er det, der giver godkendelsen. Du kan endda låse brugerens konto for at forhindre godkendelse af adgangskode helt.

En anden fordelen ved denne metode er, at du ikke skal bruge forskellige adgangskoder til at logge på forskellige servere. Du kan godkende ved hjælp af den personlige privat nøgle på alle servere, der forhindrer dig i at skulle huske flere adgangskoder.

Det er også muligt at logge på med at kræve en adgangskode ved hjælp af denne metode.

Følg disse trin for at generere tasten SSH godkendelse.

1.  Hent og Installer puttygen fra følgende placering: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.  Køre PUTTYGEN. EXE.
3.  Klik på **Generer** for at generere de pågældende taster. Du kan øge tilfældigheden ved ved at flytte musen over det tomme område i vinduet i processen.  
![][1]
4.  Efter Generer processen vises Puttygen.exe genererede nøglen. Eksempel:  
![][2]
5.  Markere og kopiere den offentlige nøgle i **nøgle** og gemme den i en fil kaldet publicKey.pem. Ikke klikke på **Gem offentlig nøgle**, fordi den gemte offentlig nøgle-filformat er forskellig fra den offentlige nøgle, vi vil.
6.  Klik på **Gem privat nøgle** og gemme den i en fil kaldet privateKey.ppk.

###<a name="step-2-create-the-image-in-the-azure-portal"></a>Trin 2: Oprette billedet i portalen Azure.
[Azure-portalen](https://portal.azure.com/), klik på **Ny** på proceslinjen til at oprette et billede, når du vælger den Linux-billede, der er baseret på dine behov. I følgende eksempel bruges Ubuntu 14.04 billedet.
![][3]

For **Værtsnavn** Angiv navnet på den URL-adresse, som du og internetklienter vil bruge til at få adgang til denne virtuelt. Definere den sidste del af DNS-navn, for eksempel tomcatdemo og Azure genererer URL-adressen som tomcatdemo.cloudapp.net.  

For **SSH godkendelsesnøgle**, skal du kopiere den nøgleværdi fra filen **publicKey.pem** , som indeholder den offentlige nøgle, der genereres af puttygen.  
![][4]

Konfigurere andre indstillinger efter behov, og klik derefter på Opret.  

##<a name="phase-2-prepare-your-virtual-machine-for-tomcat7"></a>Fase 2: Forberede din virtuelle maskine til Tomcat7
I denne fase, skal du konfigurere et slutpunkt for tomcat trafik og derefter oprette forbindelse til din nye virtuelle maskine.
###<a name="step-1-open-the-http-port-to-allow-web-access"></a>Trin 1: Åbne HTTP-port for at tillade web access
Slutpunkter i Azure består af en protokol (TCP eller UDP) sammen med en offentlige og private port. Den private port er den port, tjenesten lytter på den virtuelle maskine. Den offentlige port er den port, Azure skytjenesten lytter på eksternt for indgående, internetbaserede trafik.  

TCP-port 8080 er standardportnummeret på hvilke tomcat lytter. Denne port åbnes med Azure ark, så du og andre klienter internetadgang tomcat sider.  

1.  Klik på **Gennemse**i portalen Azure -> **virtuel maskine**, og klik derefter på den virtuelle maskine, du har oprettet.  
![][5]
2.  Hvis du vil føje et slutpunkt til din virtuelle maskine, skal du klikke på feltet **slutpunkter** .
![][6]
3.  Klik på **Tilføj**.  
    1.  Skriv et navn til slutpunktet i slutpunkt til **slutpunkt**, og skriv derefter 80 i **Offentlige Port**.  

        Hvis du angiver den til 80, behøver ikke at medtage portnummeret i URL-adressen, hvor du kan få adgang til tomcat. For eksempel http://tomcatdemo.cloudapp.net.    

        Hvis du angive den til en anden værdi, som 81, skal du føje portnummeret til URL-adressen til tomcat. For eksempel http://tomcatdemo.cloudapp.net:81 /.
    2.  Skriv 8080 i privat Port. Som standard lytter tomcat på TCP-port 8080. Hvis du har ændret standard lytter port af tomcat, skal du opdatere privat Port for at være den samme som tomcat lytter port.  
    ![][7]

4.  Klik på **OK** for at føje slutpunktet til din virtuelle maskine.



###<a name="step-2-connect-to-the-image-you-created"></a>Trin 2: Oprette forbindelse til det billede, du har oprettet.
Du kan vælge en hvilken som helst SSH værktøj til at oprette forbindelse til din virtuelle maskine. I dette eksempel skal bruge vi trykfarver.  

Få først DNS-navnet på din virtuelle maskine fra Azure-portalen. **Klik på Gennemse** -> **virtuelle maskiner** -> navnet på din virtuelle maskine -> **Egenskaber**og derefter se i feltet **Domænenavn** i feltet **Egenskaber** .  

Få portnummeret for SSH forbindelser fra feltet **SSH** . Her er et eksempel.  
![][8]

Du kan hente trykfarver fra [her](http://www.putty.org/) .  

Når du overfører, skal du klikke på den eksekverbare fil trykfarver. EXE. Konfigurere de grundlæggende indstillinger med værtsnavnet og portnummeret, der fås fra egenskaberne for din virtuelle maskine. Her er et eksempel:  
![][9]

Klik på **forbindelse**i venstre rude, -> **SSH** -> **Auth** og derefter klikke på **Gennemse** for at angive placeringen af filen **privateKey.ppk** , som indeholder den private nøgle genereres af puttygen i fase 1: oprette et billede. Her er et eksempel:  
![][10]

Klik på **Åbn**. Du kan have besked via en meddelelsesboks. Hvis du har konfigureret DNS-navnet og portnummeret korrekt, skal du klikke på **Ja**.
![][11]  


Du burde se følgende:  
![][12]

Skriv det brugernavn, der er angivet, når du har oprettet den virtuelle maskine i fase 1: oprette et billede. Der vises noget i retning af følgende:  
![][13]





##<a name="phase-3-install-software"></a>Fase 3: Installér software
I denne fase skal installere du Java runtime-miljø, tomcat og andre tomcat komponenter.  

###<a name="java-runtime-environment"></a>Java runtime-miljø
Tomcat er skrevet i Java. Der findes to typer af Java Development Kit (JDKs) (OpenJDK og Oracle JDK), og du kan vælge den, du vil bruge.  

>AZURE. Bemærk: Begge JDKs har næsten samme koden for klasserne i Java API, men koden for den virtuelle maskine som faktisk er forskellig. Hvis du vil biblioteker, tendens OpenJDK til at bruge Åbn biblioteker, mens Oracle tendens til at bruge dem, der er lukket. Men Oracle JDK har flere klasser, og nogle fast fejl, og Oracle JDK er mere stabil end OpenJDK.

Følgende kommandoer kan du hente de anden JDKs.  

Åbn jdk   

    sudo apt-get update  
    sudo apt-get install openjdk-7-jre  

Oracle-jdk  

-   Sådan henter du JDK fra Oracle-websted:  

        wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u5-b13/jdk-8u5-linux-x64.tar.gz  

-   Sådan oprettes en mappe for at indeholde JDK filerne:  

        sudo mkdir /usr/lib/jvm  

-   Sådan udtrækkes JDK filerne til mappen/usr/bibliotek/jvm /:  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/  

-   Sådan angives Oracle JDK som standard JVM:  

        sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_05/bin/java 100  
        sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_05/bin/javac 100  

####<a name="test"></a>Test:
Du kan bruge en kommando, som følgende til at afprøve om Java runtime-miljø er installeret korrekt:  

    java -version  

Hvis du har installeret Åbn jdk, skal du se en meddelelse som følgende:![][14]

Hvis du har installeret oracle-jdk, skal du se en meddelelse som følgende:![][15]

###<a name="tomcat7"></a>Tomcat7
Ved hjælp af følgende kommando for at installere tomcat7:  

    sudo apt-get install tomcat7  

Hvis du ikke bruger tomcat7, kan du bruge den relevante variation af denne kommando.  

####<a name="test"></a>Test:

Sådan kontrollerer du, hvis tomcat7 er blevet installeret, gå til din tomcat servers DNS-navn (http://tomcatexample.cloudapp.net/ er eksempel URL-adressen i denne artikel). Hvis du får vist en side som følger, du har installeret tomcat7 korrekt.
![][16]


###<a name="install-other-tomcat-components"></a>Installere andre Tomcat komponenter
Der findes andre valgfri tomcat komponenter, som du kan også installere.  

Bruge kommandoen **sudo Hovedgaden-cache Søg tomcat7** til at se alle tilgængelige komponenter. Følgende kommandoer er eksempler for at installere nogle nyttige dele.  

    sudo apt-get install tomcat7-admin      #admin web applications
    sudo apt-get install tomcat7-user         #tools to create user instances  

##<a name="phase-4-configure-tomcat"></a>Fase 4: Konfigurere Tomcat
I denne fase, kan du administrere tomcat.
###<a name="start-and-stop-tomcat7"></a>Starte og stoppe tomcat7
Tomcat7 serveren startes automatisk, når du installerer den. Du kan også starte den dig selv med følgende kommando:   

    sudo /etc/init.d/tomcat7 start

Sådan stopper du tomcat7:  

    sudo /etc/init.d/tomcat7 stop

Sådan får du vist status for tomcat7:  

    sudo /etc/init.d/tomcat7 status

Sådan genstartes tomcat tjenester:  

    sudo /etc/init.d/tomcat7 restart

###<a name="tomcat-administration"></a>Administration af tomcat
Du kan redigere filen Tomcat bruger konfiguration for at konfigurere dine Administratoroplysninger med følgende kommando:  

    sudo vi  /etc/tomcat7/tomcat-users.xml   

Her er et eksempel:  
![][17]  

>AZURE. Bemærk: Oprette en stærk adgangskode for brugernavnet administrator.  

Når du redigerer denne fil, skal du genstarte tomcat7 tjenester med følgende kommando for at sikre, at ændringerne træder i kraft:  

    sudo /etc/init.d/tomcat7 restart  

Åbn browseren, og Skriv URL-adressen **http://<your tomcat server DNS name>/manager/html**. For eksempel i denne artikel er URL-adressen http://tomcatexample.cloudapp.net/manager/html.  

Når du har forbindelse, skal du se afsnittet noget, der ligner følgende:  
![][18]

##<a name="common-issues"></a>Almindelige problemer

###<a name="cant-access-the-virtual-machine-with-tomcat-and-moodle-from-the-internet"></a>Kan ikke få adgang til den virtuelle maskine med Tomcat og Moodle fra internettet

-   **Symptom**  
Tomcat der kører, men du kan ikke se standardsiden Tomcat med din browser.
-   **Mulige rod store og små bogstaver**   
    1.  Tomcat lytte port er ikke samme som privat Port på din virtuelle maskine slutpunkt for tomcat trafik.  

        Kontrollere indstillingerne Port offentlige og Private Port slutpunkt, og Sørg for, at den Private Port er det samme som tomcat lytter port. Se fase 1: Oprette et billede til instruktionerne om konfiguration af slutpunkter for din virtuelle maskine.  

        For at bestemme den tomcat lytte port skal du åbne /etc/httpd/conf/httpd.conf (Red Hat udgivet version) eller /etc/tomcat7/server.xml (Debian udgivet version). Som standard er tomcat lytte port 8080. Her er et eksempel:  

            <Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"  URIEncoding="UTF-8"            redirectPort="8443" />  

        Hvis du bruger en virtuel maskine såsom Debian eller Ubuntu, og du vil ændre den standard port af Tomcat lytte (for eksempel 8081), skal du også åbne port til Operativsystemet. Først skal du åbne profilen:  

            sudo vi /etc/default/tomcat7  

        Derefter Fjern kommentar fra den sidste linje og ændre "Nej" at "Ja".  

            AUTHBIND=yes

    2.  Firewallen har deaktiveret lytte port af tomcat.

        Hvis du kan kun se standardsiden Tomcat fra den lokale vært, derefter er problemet mest sandsynlige, som er lyttet ved tomcat port er blokeret af firewallen. Du kan bruge værktøjet w3m til at lede websiden. Følgende kommandoer installere w3m og gå til siden Tomcat standard:  

            sudo yum  install w3m w3m-img
            w3m http://localhost:8080  

-   **Løsning**
    1. Hvis tomcat lytte port er ikke samme som privat Port på slutpunktet for trafik til den virtuelle maskine, du skal ændre privat porten for at være den samme som tomcat lytte port.   

    2.  Hvis problemet skyldes firewall/iptables, kan du tilføje følgende linjer til /etc/sysconfig/iptables:  

            -A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
            -A INPUT -p tcp -m tcp --dport 443 -j ACCEPT  

        Bemærk, at den anden linje bruges kun til https-trafikken.  

        Sørg for, at dette er over de linjer, der vil globalt begrænse adgang, som følgende:  

            -A INPUT -j REJECT --reject-with icmp-host-prohibited  

        For at genindlæse iptables, skal du køre følgende kommando:  

            service iptables restart  

        Dette er testet på CentOS 6.3.

###<a name="permission-denied-when-upload-you-project-files-to-varlibtomcat7webapps"></a>Tilladelse nægtet hvornår Overfør du project-filer til /var/lib/tomcat7/webapps /  

-   **Symptom**  
Når du bruger en hvilken som helst SFTP klienten (såsom FileZilla) til at oprette forbindelse til din virtuelle maskine og navigere til /var/lib/tomcat7/webapps/publicere dit websted, får du en fejlmeddelelse i stil med følgende:  

        status: Listing directory /var/lib/tomcat7/webapps
        Command:    put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
        Error:  /var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
        Error:  File transfer failed

-   **Mulige rod store og små bogstaver** Du har ingen tilladelse til at få adgang til mappen /var/lib/tomcat7/webapps.  
-   **Løsning**  
Du har brug for få tilladelse fra kontoen rod. Du kan ændre ejerskabet af den pågældende mappe roden til det brugernavn, du brugte, da klargøring maskinen. Her er et eksempel med kontonavnet azureuser:  

        sudo chown azureuser -R /var/lib/tomcat7/webapps

    Brug indstillingen -R for at anvende tilladelser for alle filer inde i en mappe for.  

    Bemærk, at denne kommando kan også bruges for mapper. Indstillingen -R ændrer tilladelserne for alle filer og mapper i mappen. Her er et eksempel:  

        sudo chown -R username:group directory  

    Denne kommando ændres ejerskab (både bruger og grupper) for alle filer og mapper inde i mappe og directory sig selv.  

    Følgende kommando ændres tilladelse fra mappen mappe kun, men de bevares filer og mapper i mappen alene.  

        sudo chown username:group directory



[1]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-18.png
