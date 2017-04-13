<properties 
   pageTitle="Konfigurere DNS mellem to Azure virtuelle netværk | Microsoft Azure" 
   description="Lær, hvordan du konfigurerer VPN-forbindelser og domæne navneoversættelse mellem to virtuelle netværk, og hvordan du konfigurerer HBase geografisk-gentagelse." 
   services="hdinsight,virtual-network" 
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
   ms.date="06/28/2016"
   ms.author="jgao"/>

# <a name="configure-dns-between-two-azure-virtual-networks"></a>Konfigurere DNS mellem to Azure virtuelle netværk

> [AZURE.SELECTOR]
- [Konfigurere VPN-forbindelse](../hdinsight-hbase-geo-replication-configure-vnets.md)
- [Konfigurere DNS](hdinsight-hbase-geo-replication-configure-dns.md)
- [Konfigurere HBase gentagelse](hdinsight-hbase-geo-replication.md) 


Lær, hvordan du tilføjer og konfigurerer DNS-servere at Azure virtuelle netværk til at håndtere navneoversættelse i og på tværs af de virtuelle netværk.

Dette selvstudium er den anden del af en [serie] [ hdinsight-hbase-geo-replication] om oprettelse af HBase geografisk-gentagelse:

- [Konfigurere en VPN-forbindelse mellem to virtuelle netværk][hdinsight-hbase-geo-replication-vnet]
- Konfigurere DNS til de virtuelle netværk (dette selvstudium)
- [Konfigurere HBase geografisk gentagelse][hdinsight-hbase-geo-replication]


I følgende diagram vises de to virtuelle netværk, du oprettede i [konfigurere en VPN-forbindelse mellem to virtuelle netværk][hdinsight-hbase-geo-replication-vnet]:

![HDInsight HBase gentagelse virtuelle netværksdiagram][img-vnet-diagram]

##<a name="prerequisites"></a>Forudsætninger
Inden du starter selvstudiet, skal du have følgende:

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **En arbejdsstation med Azure PowerShell**.

    Før du kører PowerShell-scripts, du kontrollere, at du har forbindelse til abonnementet Azure ved hjælp af følgende cmdlet:

        Add-AzureAccount

    Hvis du har flere Azure abonnementer, kan du bruge følgende cmdlet til at angive det aktuelle abonnement:

        Select-AzureSubscription <AzureSubscriptionName>
        
    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **To Azure virtuelt netværk med VPN-forbindelse**.  Flere oplysninger under [konfigurere en VPN-forbindelse mellem to Azure virtuelle netværk][hdinsight-hbase-geo-replication-vnet].

>[AZURE.NOTE] Azure service navne og virtuelt skal være entydig. Det navn, der bruges i dette selvstudium er Contoso-[navn på Azure Service/VM]-[EU / USA]. Contoso-VNet-EU-er for eksempel Azure virtuelt netværk i datacenter North Europe; Contoso-DNS-US er DNS-server VM i datacenter af US. Du skal komme frem til dine egne navne.
 
 
##<a name="create-azure-virtual-machines-to-be-used-as-dns-servers"></a>Oprette Azure virtuelle maskiner som skal bruges som DNS-servere

**For at oprette en virtuel maskine i Contoso-VNet-EU, med navnet Contoso-DNS-EU-**

1.  Klik på **Ny**, **beregne**, **VIRTUELT**, **Fra GALLERIET**.
2.  Vælg **Windows Server 2012 R2 Datacenter**.
3.  Angiv:
    - **VIRTUELT navn**: Contoso-DNS-EU-
    - **Nye brugernavn**: 
    - **Ny adgangskode**: 
4.  Angiv:
    - **SKYBASERET tjeneste**: oprette en ny tjeneste i skyen
    - **Område/forbindelse gruppe/VIRTUELLE netværk**: (Vælg Contoso-VNet-EU)
    - **Virtuel NETVÆRKSUNDERNET**: undernet-1
    - **LAGERPLADS konto**: Brug en automatisk oprettede lagerplads konto
    
        Navnet på skyen tjenesten vil være den samme som navnet på den virtuelle computer. I dette tilfælde er, der Contoso-DNS-EU. Til efterfølgende virtuelle maskiner, kan jeg vælge at bruge den samme skybaseret tjeneste.  Alle virtuelle maskiner under samme skytjenesten dele de samme virtuelt netværk og domænesuffiks.

        Kontoen lagerplads bruges til at gemme filen virtuelt billede. 
    - **SLUTPUNKTER**: (Rul ned, og vælg **DNS**) 

Når den virtuelle maskine er oprettet, find ud af det interne IP- og ekstern IP.

1.  Klik på navnet på virtuelt **Contoso-DNS-EU**.
2.  Klik på **DashBoard**.
3.  Notér:
    - OFFENTLIGE VIRTUELLE IP-ADRESSE
    - INTERNE IP-ADRESSE


**Du opretter en virtuel maskine i Contoso-VNet-DK, kaldet Contoso-DNS-dk** 

- Gentag den samme procedure for at oprette en virtuel maskine med følgende værdier:
    - VIRTUELT navn: Contoso-DNS-dk
    - OMRÅDE/forbindelse gruppe/VIRTUELLE netværk: vælge Contoso-VNET-US
    - VIRTUELT NETVÆRKSUNDERNET: Undernet-1
    - LAGERPLADS konto: Bruge en konto automatisk oprettede lagerplads
    - SLUTPUNKTER: (Vælg DNS)

##<a name="set-static-ip-addresses-for-the-two-virtual-machines"></a>Angive statiske IP-adresser for de to virtuelle maskiner

DNS-servere kræver statiske IP-adresser.  Dette trin kan ikke udføres fra portalen Azure klassisk. Du skal bruge Azure PowerShell.

**Konfigurere statiske IP-adresse til to virtuelle maskiner**

1. Åbn Windows PowerShell ISE.
2. Kør følgende cmdletter.  

        Add-AzureAccount
        Select-AzureSubscription [YourAzureSubscriptionName]
        
        Get-AzureVM -ServiceName Contoso-DNS-EU -Name Contoso-DNS-EU | Set-AzureStaticVNetIP -IPAddress 10.1.0.4 | Update-AzureVM
        Get-AzureVM -ServiceName Contoso-DNS-US -Name Contoso-DNS-US | Set-AzureStaticVNetIP -IPAddress 10.2.0.4 | Update-AzureVM 

    ServiceName er navnet på tjenesten skyen. Da DNS-serveren er den første virtuelle maskine af skytjenesten, er navnet på tjenesten skyen den samme som navnet på den virtuelle computer.

    Du skal muligvis opdatere ServiceName og navn, så det svarer til de navne, som du har.


##<a name="add-the-dns-server-role-the-two-virtual-machines"></a>Tilføj DNS-Server rolle to virtuelle maskiner

**Tilføje rollen DNS-Server for Contoso-DNS-EU-**

1.  Fra portalen Azure klassisk skal du klikke på **virtuelle maskiner** i venstre side. 
2.  Klik på **Contoso-DNS-EU**.
3.  Klik på **DASHBOARD** fra toppen.
4.  Klik på **Opret forbindelse** fra bunden, og følg vejledningen for at oprette forbindelse til den virtuelle maskine via RDP.
2.  Klik på knappen Windows i det nederste venstre hjørne til at åbne startskærmen i RDP-session.
3.  Klik på feltet **Server Manager** .
4.  Klik på **Tilføj roller og funktioner**.
5.  Klik på **Næste**
6.  Vælg **rollebaseret eller funktion-baserede installation**, og klik derefter på **Næste**.
7.  Vælg din DNS-virtuelt (den skal være fremhævet allerede), og klik derefter på **Næste**.
8.  Se **DNS-Server**.
9.  Klik på **Tilføj funktioner**, og klik derefter på **Fortsæt**.
10. Klik på **Næste** tre gange, og klik derefter på **Installer**. 

**Tilføje rollen DNS-Server for Contoso-DNS-dk**

- Gentag trinnene for at tilføje DNS-rolle til **Contoso-DNS-US**.

##<a name="assign-dns-servers-to-the-virtual-networks"></a>Tildele DNS-servere til de virtuelle netværk

**Til at registrere to DNS-servere**

1.  Klik på **Ny** **Netværk SERVICES** **VIRTUELT netværk**, og **REGISTRERE DNS-SERVER**fra portalen Azure klassisk.
2.  Angiv:
    - **Navn**: Contoso-DNS-EU-
    - **DNS-SERVER IP-adresse**: 10.1.0.4 – IP-adresse skal tilsvarende DNS server virtuelt IP-adresse.
     
3.  Gentag processen for at registrere Contoso-DNS-US med følgende indstillinger:
    - **Navn**: Contoso-DNS-dk
    - **DNS-SERVER IP-adresse**: 10.2.0.4

**Tildele to DNS-serverne til de to virtuelle netværk**

1.  Klik på **netværk** i venstre rude i portalen klassisk.
2.  Klik på **Contoso-VNet-EU**.
3.  Klik på **KONFIGURER**.
4.  Vælg **Contoso-DNS-EU-** i sektionen **DNS-servere** .
5.  Klik på **Gem** nederst på siden, og klik på **Ja** for at bekræfte.
6.  Gentag processen for at tildele **Contoso-DNS-US** DNS-server til det virtuelle netværk for **Contoso-VNet-US** .

Alle virtuelle maskiner, der er implementeret med de virtuelle netværk skal genstartes for at opdatere konfigurationen af DNS-server.

**At genstarte de virtuelle maskiner**

1. Fra portalen Azure klassisk skal du klikke på **virtuelle maskiner** i venstre side.
2. Klik på **Contoso-DNS-EU**.
3. Klik på **Dashboard** fra toppen.
4. Klik på **Genstart** nederst.
5. Gentag de samme trin for at genstarte computeren **Contoso-DNS-US**.


##<a name="configure-dns-conditional-forwarders"></a>Konfigurere DNS-betinget videresendelser

DNS-server i hver virtuelt netværk kan kun fortolke DNS-navne i den virtuelle netværk. Du skal konfigurere en betinget domænes til at pege på peer-DNS-server til navnet løsninger i det virtuelle peer-netværk.

For at konfigurere betinget domænes, skal du vide domænesuffikserne for to DNS-servere. DNS-suffikser kan være forskellige afhængigt af den Cloud Services konfiguration, du brugte, da du oprettede virtuelle computere. For hver DNS-suffiks, der bruges i det virtuelle netværk, skal du tilføje en betinget domænes. 

**Finde domænesuffikserne for to DNS-servere**

1. RDP til **Contoso-DNS-EU**.
2. Åbn Windows PowerShell console eller kommandoprompt.
3. Køre **ipconfig**, og notér **forbindelse-specifikke DNS-suffiks**.
4. Luk ikke RDP-session, du skal stadig bruge den. 
5. Gentag de samme trin til at finde ud af en **forbindelse-specifikke DNS-suffiks** **Contoso-DNS-US**.


**Konfigurere DNS-videresendelser**
 
1.  RDP-session til **Contoso-DNS-EU**, klik på Windows-tasten nederst til venstre.
2.  Klik på **Administration**.
3.  Klik på **DNS**.
4.  Udvid **DSN**, **Contoso-DNS-EU-**i venstre rude.
5.  Angiv følgende oplysninger:
    - **DNS-domæne**: Angiv DNS-suffikset i Contoso-DNS-US. For eksempel: Contoso-DNS-US.b5.internal.cloudapp.net.
    - **IP-adresserne på de overordnede servere**: Angiv 10.2.0.4, som er Contoso-DNS-USAS IP-adresse.
6.  Tryk på **ENTER**, og klik derefter på **OK**.  Nu vil du kunne løse Contoso-DNS-USAS IP-adresse fra Contoso-DNS-EU.
7.  Gentag trin for at tilføje en DNS-domænes DNS-tjenesten på Contoso-DNS-US virtuel maskine med følgende værdier:
    - **DNS-domæne**: Angiv DNS-suffikset i Contoso-DNS-EU. 
    - **IP-adresserne på de overordnede servere**: Angiv 10.2.0.4, som er Contoso-DNS-EUS IP-adresse.

##<a name="test-the-name-resolution-across-the-virtual-networks"></a>Teste navneoversættelse på tværs af de virtuelle netværk

Nu kan du teste host navneoversættelse på tværs af de virtuelle netværk. Ping er blokeret af firewall som standard.  Du kan bruge nslookup til at løse virtuelle maskiner på DNS-server (du skal bruge fulde Domænenavn) i peer-netværk.  


##<a name="next-steps"></a>Næste trin

I dette selvstudium, har du lært at konfigurere navneoversættelse på tværs af virtuelle netværk med VPN-forbindelser. De andre to artikler i serien omfatter:

- [Konfigurere en VPN-forbindelse mellem to Azure virtuelle netværk][hdinsight-hbase-geo-replication-vnet]
- [Konfigurere HBase geografisk gentagelse][hdinsight-hbase-geo-replication]



[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md
[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[powershell-install]: powershell-install-configure.md

[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-DNS/HDInsight.HBase.VPN.diagram.png 
