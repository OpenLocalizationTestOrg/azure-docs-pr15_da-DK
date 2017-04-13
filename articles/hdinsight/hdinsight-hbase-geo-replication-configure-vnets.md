<properties 
   pageTitle="Konfigurere VPN-forbindelse mellem to virtuelle netværk | Microsoft Azure" 
   description="Lær, hvordan du konfigurerer VPN-forbindelser og domæne navneoversættelse mellem to Azure virtuelle netværk, og hvordan du konfigurerer HBase geografisk-gentagelse." 
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

# <a name="configure-a-vpn-connection-between-two-azure-virtual-networks"></a>Konfigurere en VPN-forbindelse mellem to Azure virtuelle netværk  

> [AZURE.SELECTOR]
- [Konfigurere VPN-forbindelse](hdinsight-hbase-geo-replication-configure-vnets.md)
- [Konfigurere DNS](hdinsight-hbase-geo-replication-configure-dns.md)
- [Konfigurere HBase gentagelse](hdinsight-hbase-geo-replication.md) 

Azure virtuelle til websted netværksforbindelsen bruger en VPN-gateway til at give en sikker tunnel ved hjælp af Ipsec/IKE. VNets kan være i forskellige abonnementer og forskellige områder. Du kan også kombinere VNet til VNet kommunikation med flere websted konfigurationer. Der er flere årsager til VNet til VNet connectivity:

- Cross geografisk område-redundans og geografisk tilstedeværelse 
- Internationale med flere lag programmer med stærke isolationsniveauet rammen 
- Cross mellem organisation kommunikation i Azure-abonnement

Se [konfigurere en VNet til VNet forbindelse](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)kan finde flere oplysninger. 

Sådan vises den på video:

> [AZURE.VIDEO configure-the-vpn-connectivity-between-two-azure-virtual-networks]

Dette selvstudium er en del af en [serie] [ hdinsight-hbase-replication] om oprettelse af HBase geografisk-gentagelse. 

- Konfigurere en VPN-forbindelse mellem to virtuelle netværk (dette selvstudium)
- [Konfigurere DNS til de virtuelle netværk][hdinsight-hbase-geo-replication-dns]
- [Konfigurere HBase geografisk gentagelse][hdinsight-hbase-geo-replication]

I følgende diagram vises de to virtuelle netværk, du opretter i dette selvstudium:

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


>[AZURE.NOTE] Azure service navne og virtuelt skal være entydig. Det navn, der bruges i dette selvstudium er Contoso-[navn på Azure Service/VM]-[EU / USA]. Contoso-VNet-EU-er for eksempel Azure virtuelt netværk i datacenter North Europe; Contoso-DNS-US er DNS-server VM i af US datacenter. Du skal komme frem til dine egne navne.
 

##<a name="create-two-azure-vnets"></a>Oprette to Azure VNets



**Oprette et virtuelt netværk, der hedder Contoso-VNet-EU-i Nord Europa**

1.  Log på [Azure klassisk Portal][azure-portal].
2.  Klik på **Ny**, **netværk SERVICES**, **VIRTUELLE netværk**, **brugerdefinerede Opret**.
3.  Angiv:

    - **Navn**: Contoso-VNet-EU-
    - **Placering**: North Europe

        Dette selvstudium bruger North Europa og indtastning af østasiatiske USA datacentre. Du kan vælge din egen datacentre.
4.  Angiv:

    - **DNS-SERVER**: (Lad feltet stå tomt) 
    
        Du skal bruge dine egne DNS-server til navneoversættelse i virtuelle netværk. Du kan finde flere oplysninger om hvornår Brug Azure-producenter navneoversættelse og hvornår du skal bruge dine egne DNS-server, [Name opløsning (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Se [Konfigurere DNS mellem to Azure virtuelle netværk]have hjælp til at konfigurere navneoversættelse mellem VNets[hdinsight-hbase-dns].
  
    - **Konfigurer en VPN-forbindelse til punkt-til-websted**: (ikke markeret)

        Punkt-til-site gælder ikke for dette scenario.

    - **Konfigurer en VPN til-websted**: (ikke markeret)
    
        Du skal konfigurere til websted VPN-forbindelse til det Azure virtuelle netværk i af US datacenter.
5.  Angiv:

    -   **Adresse mellemrum Start IP-adresse**: 10.1.0.0
    -   **Adresse mellemrum CIDR**: / 16
    -   **Undernet 1 Start IP-adresse**: 10.1.0.0
    -   **Undernet 1 CIDR**: / 24

    Adresseområdet kan ikke overlapper med det virtuelle netværk i USA.  

**At oprette et virtuelt netværk med navnet Contoso-VNet-EU-i Vest Europa**

- Gentag den seneste procedure med følgende værdier:

    - **Navn**: Contoso-VNet-dk
    - **Placering**: af USA
     
    - **DNS-SERVER**: (Lad feltet stå tomt)
    - **Konfigurer en VPN-forbindelse til punkt-til-websted**: (ikke markeret)
    - **Konfigurer en VPN til-websted**: (ikke markeret)
     
    - **Adresse mellemrum Start IP-adresse**: 10.2.0.0
    - **Adresse mellemrum CIDR**: / 16
    - **Undernet 1 Start IP-adresse**: 10.2.0.0
    - **Undernet 1 CIDR**: / 24

















##<a name="configure-a-vpn-connection-between-the-two-vnets"></a>Konfigurere en VPN-forbindelse mellem de to VNets

###<a name="create-local-networks"></a>Oprette lokale netværk

Når du opretter en VNet VNet konfigurationen, skal du konfigurere hver VNet for at identificere hinanden som et websted, lokale netværk. I dette afsnit, skal du konfigurere hver VNet som et lokalt netværk. De lokale netværk dele de samme IP-adresseområder med den tilsvarende VNet.

![Konfigurere Azure VPN-til-websted konfiguration – azure lokale netværk][img-vnet-lnet-diagram]


**For at oprette et lokalt netværk med navnet Contoso-LNet-EU tilsvarende Contoso-VNet-EU-netværk-adresseområde**

1. Fra portalen Azure klassisk skal du klikke på **Ny**, **NETVÆRKSTJENESTER**, **VIRTUELT netværk**, **Tilføje LOKALNETVÆRK**.
3. Angiv:

    - **Navn**: Contoso-LNet-EU-
    - **VPN-enhed IP-adresse**: 192.168.0.1 (denne adresse opdateres senere)

        Typisk, du vil bruge den faktiske eksterne IP-adresse til en VPN-enhed. For VNet til VNet konfigurationer, vil du bruge VPN-gateway IP-adresse. Da, at du ikke har oprettet VPN gateways for de to VNets endnu, kan du angive en arbitary IP-adresse og vender tilbage at løse problemet.
4.  Angiv:

    - **Adresse mellemrum Start IP-adresse:** 10.1.0.0
    - **Adresse mellemrum CIDR:** /16
    
    Dette skal svare nøjagtigt til det område, du tidligere har angivet for Contoso-VNet-EU.

**For at oprette et lokalt netværk med navnet Contoso-LNet-US tilsvarende Contoso-VNet-US netværk-adresseområde**

- Gentag den seneste procedure med følgende parametre:

    - **Navn**: Contoso-LNet-dk
    - **VPN-enhed IP-adresse**: 192.168.0.1 (denne adresse opdateres senere)
     
    - **Adresse mellemrum Start IP-adresse**: 10.2.0.0
    - **Adresse mellemrum CIDR**: / 16


###<a name="create-vpn-gateways"></a>Oprette VPN gateways

Der findes to dele i denne konfiguration. Først du konfigurere en VNet til websted forbindelse til et lokalt netværk, og derefter skal du oprette en dynamisk routing VPN-forbindelse. VNet til VNet kræver Azure VPN gateways med dynamisk routing VPN. Azure statisk routing VPN'er understøttes ikke.

**Sådan konfigureres til websted Contoso-VNet-EU-forbindelse til Contoso-LNet-dk**

1.  Fra portalen Azure klassisk skal du klikke på **netværk** i venstre rude
2.  Klik på **Contoso-VNet-EU**.
3.  Klik på fanen **CONFIGUE** .
4.  Se **oprette forbindelse til lokale netværk**.
5.  I **LOKALNETVÆRK**skal du vælge **Contoso-LNet-US**.
6.  Klik på **Tilføj gateway undernet** i sektionen virtuelt netværk mellemrum.
7.  Klik på **Gem**.
8.  Klik på **OK** for at bekræfte.


**Oprette en VPN-gateway for Contoso-VNet-EU-**

1.  Klik på fanen **DASHBOARD** fra portalen Azure klassisk.
4.  Klik på **Opret GATEWAY** nederst på siden, og klik derefter på **Dynamiske Routing**.
5.  Klik på **Ja** for at bekræfte. Bemærk gateway grafik på siden ændres til gul og siger, at oprette gatewayen. Det tager typisk 15 minutter til at oprette gatewayen.

    Når gatewaystatus ændres til tilslutning, vil IP-adressen for hver Gateway være synlig i dashboardet. Notér IP-adressen, der svarer til hver VNet, tager sig ikke for at blande dem. Dette er de IP-adresser, der skal bruges, når du redigerer dine pladsholder IP-adresser til VPN-enhed under lokalnetværk.

6.  Oprette en kopi af den **GATEWAY IP-adresse**. Du skal bruge det til at konfigurere VPN-gateway IP-adressen til Contoso-VNet-EU i næste afsnit.

**Oprette en VPN-gateway for Contoso-VNet-EU-**

- Gentag de to sidste procedure for at konfigurere forbindelsen til websted Contoso-VNet-US Contoso-LNet-EU, og Opret en VPN-gateway til Contoso-Vnet-US. Når du er færdig, har du VPN gateway IP-adressen for Contoso-VNet-US.


### <a name="set-the-vpn-device-ip-addresses-for-local-networks"></a>Angiv VPN-enheden IP-adresser for lokale netværk
I det sidste afsnit opretter du en VPN-gateway for hver af VNets. Du har har fået IP-adresserne for VPN-gateways. Du kan nu gå tilbage til konfigurere lokale netværk VPN-enhed IP-adresser.

**Konfigurere VPN-enhed IP-adressen for Contoso-LNet-EU-** 

1.  Klik på **netværk** i venstre rude fra portalen Azure klassisk.
2.  Klik på **LOKALNETVÆRK** fra toppen.
3.  Klik på **Contoso-LNet-EU**, og klik derefter på **Rediger** nederst.
4.  Opdater **VPN-enhed IP-adresse**.  Dette er den adresse, du får fra fanen DASHBOARD i Contoso-VNET-EU.
5.  Klik på knappen til højre.
6.  Klik på knappen Kontrollér.

**Konfigurere VPN-enhed IP-adressen for Contoso-LNet-dk** 

- Gentag den seneste procedure for at konfigurere VPN-enhed IP-adressen til Contoso-LNet-US.

###<a name="set-vnet-gateway-keys"></a>Angive VNet gateway taster

Vnet gateways bruger en delt nøgle til at godkende forbindelser mellem de virtuelle netværk. Tasten kan ikke konfigureres fra portalen Azure klassisk. Du skal bruge PowerShell eller .NET SDK.

**Angive de pågældende taster**

1. Åbn **Windows PowerShell ISE** eller Windows PowerShell console fra din arbejdsstation.
2. Opdatere parametre i dette opfølgning-script, og kør den:

        Add-AuzreAccount
        Select-AzureSubscription -[AzureSubscriptionName]
        Set-AzureVNetGatewayKey -VNetName ContosoVNet-EU -LocalNetworkSiteName Contoso-LNet-US -SharedKey A1b2C3D4
        Set-AzureVNetGatewayKey -VNetName ContosoVNet-US -LocalNetworkSiteName Contoso-LNet-EU -SharedKey A1b2C3D4 


##<a name="check-the-vpn-connection"></a>Kontrollere VPN-forbindelse 

Du kan bruge virtuelle visuelle netværksdiagrammet VNet Dashboard-siden på portalen Azure klassisk uden en hvilken som helst VM'er, der er installeret på VNets, til at kontrollere forbindelsesstatus:

![HDInsight HBase gentagelse virtuelt netværk VPN forbindelsesstatus][img-vpn-status]
  



##<a name="next-steps"></a>Næste trin

Du har lært at konfigurere en VPN-forbindelse mellem to Azure virtuelle netværk i dette selvstudium. De andre to artikler i serien omfatter:

- [Konfigurere DNS mellem to Azure virtuelle netværk][hdinsight-hbase-geo-replication-dns]
- [Konfigurere HBase geografisk gentagelse][hdinsight-hbase-geo-replication]



[hdinsight-hbase-geo-replication-dns]: hdinsight-hbase-geo-replication-configure-dns.md
[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com

[powershell-install]: ../install-configure-powershell.md



[hdinsight-hbase-replication]: hdinsight-hbase-geo-replication.md
[hdinsight-hbase-dns]: hdinsight-hbase-geo-replication-configure-dns.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-vnets/hdinsight-hbase-vpn-diagram.png
[img-vnet-lnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-vnets/hdinsight-hbase-vpn-lnet-diagram.png
[img-vpn-status]: ./media/hdinsight-hbase-geo-replication-configure-vnets/hdinsight-hbase-vpn-status.png 
