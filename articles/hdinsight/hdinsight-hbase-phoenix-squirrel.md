<properties 
   pageTitle="Bruge Apache København og SQuirreL i HDInsight | Microsoft Azure" 
   description="Lær, hvordan du bruger Apache København i HDInsight, og hvordan du installerer og konfigurerer SQuirreL på computeren til at oprette forbindelse til en HBase klynge i HDInsight." 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/02/2016"
   ms.author="jgao"/>

# <a name="use-apache-phoenix-and-squirrel-with-windows-based-hbase-clusters-in-hdinsight"></a>Brug Apache København og SQuirreL med Windows-baseret HBase klynger i HDinsight  

Lær, hvordan du bruger [Apache København](http://phoenix.apache.org/) i HDInsight, og hvordan du installerer og konfigurerer SQuirreL på computeren til at oprette forbindelse til en HBase klynge i HDInsight. Du kan finde flere oplysninger om København, [København på 15 minutter eller mindre](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Du kan finde grammatik København [København grammatik](http://phoenix.apache.org/language/index.html).

>[AZURE.NOTE] Finde oplysninger om København i HDInsight, [Nyheder i de Hadoop klynge versioner, der leveres af HDInsight?] [hdinsight-versions].
>
> Oplysningerne i dette dokument er specifikke for Windows-baseret HDInsight klynger. Du kan finde oplysninger om brug af København på Linux-baserede HDInsight [Brug Apache København med Linux-baserede HBase klynger i HDinsight](hdinsight-hbase-phoenix-squirrel-linux.md).

##<a name="use-sqlline"></a>Brug SQLLine
[SQLLine](http://sqlline.sourceforge.net/) er en kommandolinjen til at udføre SQL. 

###<a name="prerequisites"></a>Forudsætninger
Før du kan bruge SQLLine, skal du have følgende:

- **A HBase klynge i HDInsight**. Oplysninger om klargøring HBase klynge, skal du se [komme i gang med Apache HBase i HDInsight][hdinsight-hbase-get-started].
- **Opret forbindelse til HBase klynge via den protokol til Fjernskrivebord**. Flere oplysninger under [administrere Hadoop klynger i HDInsight ved hjælp af portalen Azure klassisk][hdinsight-manage-portal].

**At finde ud af værtsnavnet**

1. Åbn **Hadoop kommandolinjen** fra skrivebordet.
2. Kør følgende kommando for at få DNS-suffiks:

        ipconfig

    Notér **Forbindelse-specifikke DNS-suffiks**. For eksempel, *myhbasecluster.f5.internal.cloudapp.net*. Når du opretter forbindelse til en HBase klynge, skal du oprette forbindelse til en af Dyrepasserne ved hjælp af fulde Domænenavn. Hver HDInsight klynge har 3 Dyrepasserne. De er *zookeeper0*, *zookeeper1*og *zookeeper2*. FULDE bliver noget i retning af *zookeeper2.myhbasecluster.f5.internal.cloudapp.net*.

**Bruge SQLLine**

1. Åbn **Hadoop kommandolinjen** fra skrivebordet.
2. Kør følgende kommandoer for at åbne SQLLine:

        cd %phoenix_home%\bin
        sqlline.py [The FQDN of one of the Zookeepers]

    ![hdinsight hbase København sqlline][hdinsight-hbase-phoenix-sqlline]

    De kommandoer, som bruges i stikprøven:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));
        
        !tables
        
        UPSERT INTO Company VALUES(1, 'Microsoft');
        
        SELECT * FROM Company;

Se afsnittet [SQLLine manuel](http://sqlline.sourceforge.net/#manual) og [København grammatik](http://phoenix.apache.org/language/index.html)kan finde flere oplysninger.


















##<a name="use-squirrel"></a>Brug SQuirreL

[SQuirreL SQL Client](http://squirrel-sql.sourceforge.net/) er et grafisk Java-program, der gør det muligt at få vist strukturen i en JDBC kompatibel database, Søg efter data i tabeller, udstede SQL-kommandoer osv. Det kan bruges til at oprette forbindelse til Apache København på HDInsight.

Dette afsnit beskrives, hvordan du installerer og konfigurerer SQuirreL på computeren til at oprette forbindelse til en HBase klynge i HDInsight via VPN. 

###<a name="prerequisites"></a>Forudsætninger

Før du følger procedurerne, skal du have følgende:

- En HBase klynge installeret på en Azure virtuelt netværk med en DNS-virtuel maskine.  Flere oplysninger under [klargøring HBase klynger på Azure virtuelt netværk][hdinsight-hbase-provision-vnet]. 

    >[AZURE.IMPORTANT] Du skal installere en DNS-server på virtuelle netværket. Flere oplysninger under [Konfigurere DNS mellem to Azure virtuelt netværk](hdinsight-hbase-geo-replication-configure-dns.md)

- Få HBase klynge klynge forbindelse-specifikke DNS-suffiks. Få det RDP i klynge, og derefter køre IPConfig.  DNS-suffikset minder om:

        myhbase.b7.internal.cloudapp.net
- Hent og Installer [Microsoft Visual Studio Express 2013 til Windows-skrivebord](https://www.visualstudio.com/products/visual-studio-express-vs.aspx) på computeren. Du skal makecert i pakken til at oprette dit certifikat.  
- Hent og Installer [Java Runtime-miljø](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html) på computeren.  SQuirreL SQL client version 3.0 og nyere kræver JRE version 1,6 eller nyere.  


###<a name="configure-a-point-to-site-vpn-connection-to-the-azure-virtual-network"></a>Konfigurere et punkt-til-Site VPN-forbindelse til Azure virtuelle netværket

Der er 3 trin til konfiguration af en VPN-forbindelse til punkt-til-websted:

1. [Konfigurere et virtuelt netværk og en dynamisk routing gateway](#Configure-a-virtual-network-and-a-dynamic-routing-gateway)
2. [Oprette dine egne certifikater](#Create-your-certificates)
3. [Konfigurere din VPN-klient](#Configure-your-VPN-client)

Du kan få flere oplysninger i [konfigurere en punkt-til-Site VPN-forbindelse til et virtuelt Azure-netværk](../vpn-gateway/vpn-gateway-point-to-site-create.md) .

#### <a name="configure-a-virtual-network-and-a-dynamic-routing-gateway"></a>Konfigurere et virtuelt netværk og en dynamisk routing gateway

Sikre, at du har klargjort en HBase klynge i en Azure virtuelt netværk (se forudsætninger for dette afsnit). Næste trin er at konfigurere en forbindelse til punkt-til-websted.

**Til at konfigurere forbindelsen punkt-til-websted**

1. Log på [Azure klassisk Portal][azure-portal].
2. Klik på **netværk**i venstre side.
3. Klik på det virtuelle netværk, du har oprettet (se [klargøring HBase klynger på Azure virtuelt netværk][hdinsight-hbase-provision-vnet]).
4. Klik på **KONFIGURER** fra toppen.
5. Vælg **Konfigurer punkt-til-site connectivity**i sektionen **punkt-til-site connectivity** . 
6. Konfigurere **Første IP-** og **CIDR** for at angive den IP-adresseområder, hvorfra VPN-klienter, modtager en IP-adresse, når du har forbindelse. Området kan ikke overlapper med en af de områder, der er placeret på dit lokale netværk og det Azure virtuelle netværk, skal du oprette forbindelse til. For eksempel. Hvis du har valgt 10.0.0.0/20 for det virtuelle netværk, kan du vælge 10.1.0.0/24 for klient-adresseområde. Se [Punkt-til-Site Connectivity] [ vnet-point-to-site-connectivity] siden kan finde flere oplysninger.
7. Klik på **Tilføj gateway undernet**i sektionen virtuelt netværk mellemrum.
7. Klik på **Gem** nederst på siden.
8. Klik på **Ja** for at bekræfte ændringen. Vent, indtil systemet er færdig med at foretage ændringen, før du fortsætter til næste procedure.


**Oprette en dynamisk dirigere gateway**

1. Klik på **DASHBOARD** fra toppen af siden fra portalen Azure klassisk.
2. Klik på **Opret GATEWAY** fra bunden af siden.
3. Klik på **Ja** for at bekræfte. Vent, indtil gatewayen er blevet oprettet.
4. Klik på **DASHBOARD** fra toppen.  Der vises et visuelt diagram over det virtuelle netværk:

    ![Azure virtuelle punkt-til-site virtuelle netværksdiagram][img-vnet-diagram] 

    Diagrammet viser 0 klientforbindelser. Når du laver en forbindelse til det virtuelle netværk, opdateres antallet til en. 

#### <a name="create-your-certificates"></a>Oprette dine egne certifikater

Der er en metode til at oprette et x.509-certifikat ved hjælp af certifikat oprettelse af værktøjet (makecert.exe), der følger med [Microsoft Visual Studio Express 2013 til Windows-skrivebord](https://www.visualstudio.com/products/visual-studio-express-vs.aspx). 


**Oprette et selvsigneret certifikat**

1. Åbn et kommandopromptvindue fra din arbejdsstation.
2. Gå til mappen Visual Studio-værktøjer. 
3. Følgende kommando i eksemplet nedenfor oprettes og installere et certifikat i personligt certifikat store på computeren, og også oprette en tilsvarende .cer-fil, som du senere overføre til portalen Azure klassisk. 

        makecert -sky exchange -r -n "CN=HBaseVnetVPNRootCertificate" -pe -a sha1 -len 2048 -ss My "C:\Users\JohnDole\Desktop\HBaseVNetVPNRootCertificate.cer"

    Skifte til den mappe, du vil .cer-filen skal være placeret i, hvor HBaseVnetVPNRootCertificate er det navn, du vil bruge til certifikatet. 

    Luk ikke kommandoprompten.  Du skal bruge den i den næste procedure.

    >[AZURE.NOTE] Fordi du har oprettet et certifikat, der skal oprettes klientcertifikater, kan du vil eksportere dette certifikat sammen med tilhørende privat nøgle og gemme den på et sikkert sted, hvor den kan gendannes. 

**Oprette et klientcertifikat**

- Fra samme kommandoprompt (den skal være på den samme computer, hvor du har oprettet certifikatet. Klientcertifikat skal genereres fra certifikatet), du køre følgende kommando:

        makecert.exe -n "CN=HBaseVnetVPNClientCertificate" -pe -sky exchange -m 96 -ss My -in "HBaseVnetVPNRootCertificate" -is my -a sha1

    HBaseVnetVPNRootCertificate er navnet på rod certifikatet.  Der er så det svarer til navnet på rod certifikatet.  

    Både certifikatet og klientcertifikat er gemt i dit personlige lager på computeren. Brug certmgr.msc til at bekræfte.

    ![Azure virtuelt netværk VPN-punkt-til-site certifikat][img-certificate]

    Et klientcertifikat skal være installeret på alle computere, du vil oprette forbindelse til det virtuelle netværk. Vi anbefaler, at du opretter entydigt klient certifikater til alle computere, du vil oprette forbindelse til det virtuelle netværk. Hvis du vil eksportere klientcertifikaterne, Brug certmgr.msc. 

**For at overføre certifikatet til portalen Azure klassisk**

1. Fra portalen Azure klassisk skal du klikke på **netværk** i venstre side.
2. Klik på det virtuelle netværk, hvor din HBase klynge er installeret på.
3. Klik på **certifikater** fra toppen.
4. Klik på **OVERFØR** fra bunden, og Angiv den rod-certifikatfil, du har oprettet i fremgangsmåden, før det foregående. Vent, indtil certifikatet, der har importeret.
5. Klik på **DASHBOARD** øverst.  Virtuel diagrammet viser status.


#### <a name="configure-your-vpn-client"></a>Konfigurere din VPN-klient



**Hente og installere klient VPN-pakken**

1. Side med det virtuelle netværk, i sektionen Oversigt over, skal du klikke på enten **Hent pakken med 64-bit klient VPN** fra DASHBOARDET eller **Hent pakken 32-bit klient VPN** baseret på din arbejdsstation OS-version.
2. Klik på **Kør** for at installere pakken.
3. Klik på **mere**sikkerhed for kommandoprompten, og klik derefter på **Kør alligevel**.
4. Klik på **Ja** to gange.

**Oprette forbindelse til VPN**

1. Klik på ikonet netværk på proceslinjen på skrivebordet på computeren. Du skal du se en VPN-forbindelse med navnet på din virtuelt netværk.
2. Klik på VPN-forbindelsesnavnet.
3. Klik på **Opret forbindelse**.

**Teste VPN-forbindelse og domæne navneoversættelse**

- Åbn en kommandoprompt fra din computer, og ping en af følgende navne ud fra den HBase klynge DNS-suffiks er myhbase.b7.internal.cloudapp.net:

        zookeeper0.myhbase.b7.internal.cloudapp.net
        zookeeper0.myhbase.b7.internal.cloudapp.net
        zookeeper0.myhbase.b7.internal.cloudapp.net
        headnode0.myhbase.b7.internal.cloudapp.net
        headnode1.myhbase.b7.internal.cloudapp.net
        workernode0.myhbase.b7.internal.cloudapp.net

###<a name="install-and-configure-squirrel-on-your-workstation"></a>Installere og konfigurere SQuirreL på computeren

**Installere SQuirreL**

1. Du kan hente filen SQuirreL SQL client glas fra [http://squirrel-sql.sourceforge.net/#installation](http://squirrel-sql.sourceforge.net/#installation).
2. Åbne/køre filen glas. Det kræver [Java Runtime-miljø](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html).
3. Klik på **Næste** to gange.
4. Angiv en sti, hvor du har tilladelsen Skrivning, og klik derefter på **Næste**.
    >[AZURE.NOTE] Mappen standard installation er i mappen C:\Program Files\squirrel-sql-3.6.  For at skrive til denne sti, tildelt installationsprogrammet administratorrettigheder. Du kan åbne en kommandoprompt som administrator, Naviger til Javas placering mappe og derefter køre 
    >
    >     java.exe -jar [the path of the SQuirreL jar file] 
5. Klik på **OK** for at bekræfte oprette destinationsmappen.
6. Standardindstillingen er at installere de Base og Standard pakker.  Klik på **Næste**.
7. Klik på **Næste** to gange, og klik derefter på **udført**.


**Installere København-driver**

København driver glas filen er placeret på HBase klynge. Stien ligner følgende baseret på versionerne:

    C:\apps\dist\phoenix-4.0.0.2.1.11.0-2316\phoenix-4.0.0.2.1.11.0-2316-client.jar
Skal du kopiere den til din arbejdsstation under [SQuirreL installationsmappen] / lib sti.  Den nemmeste måde er at RDP i klyngen og derefter bruge fil kopiere/indsætte (CTRL + C og CTRL + V) for at kopiere den til computeren.

**Føje en København driver til SQuirreL**

1. Åbn SQuirreL SQL Client fra computeren.
2. Klik på fanen **Driver** i venstre side.
2. Klik på **Ny Driver**fra menuen **drivere** .
3. Angiv følgende oplysninger:

    - **Navn**: København
    - **Eksempel på URL-adresse**: jdbc:phoenix:zookeeper2.contoso-hbase-eu.f5.internal.cloudapp.net
    - **Klassenavnet**: org.apache.phoenix.jdbc.PhoenixDriver

    >[AZURE.WARNING] Bruger alle små bogstaver i eksempel URL-adressen. Du kan bruge de fulde zookeeper quorum i tilfælde af, at de er nede.  Værtsnavne er zookeeper0, zookeeper1 og zookeeper2.

    ![HDInsight HBase København SQuirreL driver][img-squirrel-driver]
4. Klik på **OK**.

**Oprette et alias til HBase klynge**

1. SQuirreL, klik på fanen **aliasser** i venstre side.
2. Klik på **Nyt Alias**fra menuen **aliasser** .
3. Angiv følgende oplysninger:

    - **Navn**: navnet på HBase klyngen eller et navn, du foretrækker.
    - **Driver**: København.  Dette skal svare til navnet på driveren, som du oprettede i den seneste procedure.
    - **URL-adressen**: URL-adressen er kopieret fra konfigurationen af driver. Sørg for at bruger alle små bogstaver.
    - **Brugernavn**: det kan være noget tekst.  Da du bruger VPN-forbindelse her, bruges brugernavnet ikke overhovedet.
    - **Adgangskode**: det kan være noget tekst.

    ![HDInsight HBase København SQuirreL driver][img-squirrel-alias]
4. Klik på **Test**. 
5. Klik på **Opret forbindelse**. Når der foretages forbindelsen, SQuirreL ser sådan ud:

    ![HBase København SQuirreL][img-squirrel]

**Køre en test**

1. Klik på fanen **SQL** lige ved siden af fanen **objekter** .
2. Kopiér og Indsæt følgende kode:

        CREATE TABLE IF NOT EXISTS us_population (state CHAR(2) NOT NULL, city VARCHAR NOT NULL, population BIGINT  CONSTRAINT my_pk PRIMARY KEY (state, city))
3. Klik på knappen Kør.

    ![HBase København SQuirreL][img-squirrel-sql]
4. Skift tilbage til fanen **objekter** .
5. Udvid aliasnavnet, og udvid derefter **tabel**.  Du skal du se den nye tabel, der er angivet under.
 
##<a name="next-steps"></a>Næste trin
I denne artikel, har du lært at bruge Apache København i HDInsight.  Du kan få flere

- [Oversigt over HDInsight HBase][hdinsight-hbase-overview]: HBase er en Apache, Åbn-kilde, NoSQL-database, der er bygget på Hadoop, der indeholder RAM og stærke konsistens til store mængder ustrukturerede og semistructured data.
- [Klargør HBase klynger på Azure virtuelt netværk][hdinsight-hbase-provision-vnet]: med virtuelt netværksintegration HBase klynger kan installeres på det samme virtuelle netværk som dine programmer, så programmer kan kommunikere med HBase direkte.
- [Konfigurere HBase gentagelse i HDInsight](hdinsight-hbase-geo-replication.md): Lær, hvordan du konfigurerer HBase gentagelse på tværs af to Azure datacentre. 
- [Analysere Twitter synspunkt med HBase i HDInsight][hbase-twitter-sentiment]: Lær, hvordan du gør realtid [synspunkt analyse](http://en.wikipedia.org/wiki/Sentiment_analysis) af stor data ved hjælp af HBase i en Hadoop klynge i HDInsight.

[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png


 
