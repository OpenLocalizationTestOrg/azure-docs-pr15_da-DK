<properties 
   pageTitle="Konfigurere en VPN-punkt-til-Site gateway-forbindelse til virtuelt netværk ved hjælp af Ressourcestyring implementeringsmodel | Microsoft Azure"
   description="Oprette sikker forbindelse til netværket Azure virtuelle ved at oprette en VPN-punkt-til-Site gateway-forbindelse."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="cherylmc" />

# <a name="configure-a-point-to-site-connection-to-a-vnet-using-powershell"></a>Konfigurere en punkt-til-Site forbindelse til en VNet ved hjælp af PowerShell

> [AZURE.SELECTOR]
- [Ressourcestyring - Azure-portalen](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- [Ressourcestyring - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Klassisk - Azure-portalen](vpn-gateway-howto-point-to-site-classic-azure-portal.md)

Konfiguration af et punkt-til-websted (P2S) kan du oprette en sikker forbindelse fra den enkelte klientcomputer til et virtuelt netværk. En P2S forbindelse er nyttig, når du vil oprette forbindelse til din VNet fra en fjernplacering, f.eks, fra home eller en konference, eller hvis du kun har nogle klienter, der har brug for at oprette forbindelse til et virtuelt netværk. 

Punkt-til-Site forbindelser kræver ikke en VPN-enhed eller en offentligt IP-adresse til at arbejde. Er oprettet en VPN-forbindelse ved at starte forbindelsen fra klientcomputeren. Se [Ofte stillede spørgsmål om VPN-Gateway](vpn-gateway-vpn-faq.md#point-to-site-connections) og [planlægning og Design](vpn-gateway-plan-design.md)kan finde flere oplysninger om punkt-til-Site forbindelser. 

I denne artikel vejledes du gennem oprettelse af en VNet med en punkt-til-Site forbindelse i implementeringsmodel Ressourcestyring, ved hjælp af PowerShell.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Installation-modeller og metoder til P2S forbindelser

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

I følgende tabel vises de to installation-modeller og tilgængelig installationsmetoder til P2S konfigurationer. Når en artikel med konfigurationstrinnene er tilgængelig, link vi direkte til den fra denne tabel.

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 

## <a name="basic-workflow"></a>Grundlæggende arbejdsproces 

![Punkt-til-websted-diagram] (./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "punkt-til-websted")

I dette scenarie skal oprette du et virtuelt netværk med en forbindelse til punkt-til-websted. Vejledningen også hjælper dig med at oprette certifikater, som kræves til denne konfiguration. En P2S forbindelse består af følgende elementer: A VNet med en VPN-gateway, en root certificate .cer-fil (offentlig nøgle), et klientcertifikat og VPN-konfiguration på klienten. 

Vi bruger følgende værdier for denne konfiguration. Vi angive variablerne i sektion [1](#declare) i denne artikel. Du kan enten bruge trinnene som en gennemgang og bruge værdier uden at ændre dem eller ændre dem for at afspejle dit miljø. 

### <a name="example"></a>Eksempler på værdier

- **Navn: VNet1**
- **Adresse mellemrum: 192.168.0.0/16** og **10.254.0.0/16**<br>I dette eksempel skal bruge vi mere end én adresseområde til at illustrere, denne konfiguration fungerer sammen med flere adresse mellemrum. Flere adresse mellemrum er ikke påkrævet til denne konfiguration.
- **Undernet navn: front end**
    - **Undernet adresseområde: 192.168.1.0/24**
- **Undernet navn: backend-version**
    - **Undernet adresseområde: 10.254.1.0/24**
- **Undernet navn: GatewaySubnet**<br>Navnet på undernet *GatewaySubnet* er obligatorisk for gatewayen VPN til at arbejde.
    - **Undernet adresseområde: 192.168.200.0/24** 
- **VPN-klient adresse puljen: 172.16.201.0/24**<br>VPN-klienter, der har forbindelse til VNet ved hjælp af denne punkt-til-Site forbindelse modtage en IP-adresse fra VPN-klient adresse puljen.
- **Abonnement:** Hvis du har mere end ét abonnement, skal du bekræfte, at du bruger det rigtige arbejdsområde.
- **Ressourcegruppe: TestRG**
- **Placering: Af USA**
- **DNS-Server: IP-adresse** på den DNS-server, du vil bruge til navneoversættelse.
- **GW navn: Vnet1GW**
- **Offentlige IP-navn: VNet1GWPIP**
- **VpnType: RouteBased**

## <a name="before-beginning"></a>Før du begynder

- Kontrollér, at du har et Azure-abonnement. Hvis du ikke allerede har et Azure-abonnement, kan du aktivere din [MSDN abonnement fordele](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller Log op for at få [gratis konto](https://azure.microsoft.com/pricing/free-trial/).
    
- Installere den nyeste version af Azure ressourcestyring PowerShell-cmdlet'er. Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) kan finde flere oplysninger om installation af PowerShell-cmdlet'er. Når du arbejder med PowerShell til denne konfiguration, kontrollere, at du kører som administrator. 

## <a name="declare"></a>Del 1 – Log på og angive variabler

I dette afsnit, skal du logge på og erklære værdierne, der bruges til denne konfiguration. De angivne værdier, der bruges i eksempel scriptene. Ændre værdierne afspejler dit eget miljø. Eller du kan bruge de angivne værdier og gå gennem trinnene, som en opgave.

1. Log på din Azure-konto i PowerShell-konsollen. Denne cmdlet beder dig om logonoplysninger for din Azure-konto. Når du logger på, henter den dine kontoindstillinger, så de er tilgængelige til Azure PowerShell.

        Login-AzureRmAccount 

2. Få en liste over dine Azure abonnementer.

        Get-AzureRmSubscription

3. Angiv det abonnement, du vil bruge. 

        Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. Erklære variablerne, du vil bruge. Brug det følgende eksempel erstatning med værdierne for din egen, når det er nødvendigt.

        $VNetName  = "VNet1"
        $FESubName = "FrontEnd"
        $BESubName = "Backend"
        $GWSubName = "GatewaySubnet"
        $VNetPrefix1 = "192.168.0.0/16"
        $VNetPrefix2 = "10.254.0.0/16"
        $FESubPrefix = "192.168.1.0/24"
        $BESubPrefix = "10.254.1.0/24"
        $GWSubPrefix = "192.168.200.0/26"
        $VPNClientAddressPool = "172.16.201.0/24"
        $RG = "TestRG"
        $Location = "East US"
        $DNS = "8.8.8.8"
        $GWName = "VNet1GW"
        $GWIPName = "VNet1GWPIP"
        $GWIPconfName = "gwipconf"
        
## <a name="ConfigureVNet"></a>Del 2 - konfigurere en VNet 

1. Oprette en ressourcegruppe.

        New-AzureRmResourceGroup -Name $RG -Location $Location

2. Oprette undernet konfigurationer for det virtuelle netværk og navngive dem. *FrontEnd*, *backend-version*og *GatewaySubnet*. Disse præfikser skal være en del af den VNet adresseområde, som du har defineret.

        $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
        $besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
        $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix

3. Oprette det virtuelle netværk. Den angivne DNS-server skal være en DNS-server, der kan oversætte navne for de ressourcer, du opretter forbindelse til. I dette eksempel brugte vi en offentlig IP-adresse. Sørg for at bruge dit eget værdier.
    
        New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS

4. Angive variabler for det virtuelle netværk, du har oprettet.

        $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

5. Anmode om en dynamisk tildelt offentlige IP-adresse. Denne IP-adresse er nødvendig for gatewayen skal fungere korrekt. Du knytter senere gatewayen til gateway IP-konfigurationen.
        
        $pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

## <a name="Certificates"></a>Del 3 – certifikater

Certifikater bruges af Azure til at godkende VPN-klienter til punkt-til-Site VPN. Du kan eksportere offentlige certifikat data (ikke privat nøgle), som en Base-64-kodet x.509-.cer-fil fra et certifikat fra genereres af en virksomhedsløsning certifikat eller et selvsigneret certifikat. Du derefter importere offentlige certifikatets data fra certifikatet til Azure. Desuden skal du oprette et klientcertifikat fra certifikatet for klienter. Hver klient, som ønsker at oprette forbindelse til det virtuelle netværk ved hjælp af en P2S forbindelse skal have et klientcertifikat installeret, der er oprettet ud fra certifikatet.

### <a name="cer"></a>1. få .cer-filen til certifikatet

Du skal hente offentlige certifikatets data til det certifikat, du vil bruge.

- Hvis du bruger et enterprise certifikat system, kan du få .cer-filen til det certifikat, du vil bruge. 

- Hvis du ikke bruger en virksomhedsløsning certifikatet, skal du oprette et selvsigneret certifikat. Til Windows 10 trin, kan du referere til at [arbejde med selvsignerede rodcertifikater punkt-til-Site konfigurationer](vpn-gateway-certificates-point-to-site.md).


1. Åbn **certmgr.msc** for at få en .cer-fil fra et certifikat, og Find certifikatet. Højreklik på det selvsignerede certifikat, skal du klikke på **alle opgaver**, og klik derefter på **Eksporter**. Dette åbner **Certifikat guiden Eksporter**.

2. Klik på **Næste**i guiden, Vælg **Nej, Eksporter ikke den private nøgle**, og klik derefter på **Næste**.

3. På siden **Filformat til eksport** Vælg **Base-64-kodet X.509 (. Virksomheden).** Klik derefter på **Næste**. 

4. På den **fil skal eksporteres**, **Gå** til den placering, hvor du vil eksportere certifikat. Navngiv certifikatfilen for **filnavn**. Klik derefter på **Næste**.

5. Klik på **Udfør** for at eksportere certifikat.

### <a name="generate"></a>2. generere klientcertifikat

Dernæst skal oprette klientcertifikater. Du kan enten oprette et entydigt certifikat for hver klient, som opretter forbindelse, eller du kan bruge det samme certifikat på flere forskellige kunder. Fordelen ved oprettelse af entydige klientcertifikater er muligheden for at ophæve et enkelt certifikat, hvis det er nødvendigt. Ellers hvis alle bruger den samme klientcertifikat, og du kan finde, du vil ophæve certifikat for en kunde, skal du oprette og installere nye certifikater for alle de klienter, der bruger certifikatet til at godkende. Klientcertifikater er installeret på hver klientcomputer senere i denne opgave.

- Hvis du bruger en virksomhedsløsning certifikat skal generere et klientcertifikat med almindelige værdi navneformatet 'name@yourdomain.com', i stedet for NetBIOS 'Domæne\brugernavn' formatere. 

- Hvis du bruger et selvsigneret certifikat skal du se [arbejde med selvsignerede rodcertifikater punkt-til-Site konfigurationer](vpn-gateway-certificates-point-to-site.md) til at generere et klientcertifikat.

### <a name="exportclientcert"></a>3. eksportere klientcertifikat

Et klientcertifikat er påkrævet til godkendelse. Eksportere den efter generering klientcertifikat. Klientcertifikat du eksportere installeres senere på hver klientcomputer.

1. Hvis du vil eksportere et klientcertifikat, kan du bruge *certmgr.msc*. Højreklik på det klientcertifikat, du vil eksportere, klik på **alle opgaver**, og klik derefter på **Eksporter**.
2. Eksportere klientcertifikat med den private nøgle. Dette er en *.pfx* -fil. Sørg for at optage eller huske adgangskoden (nøgle), som du angiver for dette certifikat.

### <a name="upload"></a>4. overføre root certificate .cer-fil

Erklære variablen for dit certifikatnavn, erstatter værdien med dine egne:

        $P2SRootCertName = "Mycertificatename.cer"

Føje et certifikat offentlig data for certifikatet til Azure. Du kan overføre filer til op til 20 rodcertifikater. Du ikke overføre den private nøgle for certifikatet til Azure. Når .cer-filen er overført, bruges Azure til at godkende klienter, der har forbindelse til det virtuelle netværk. 

Erstat stien til filen med dine egne, og derefter køre cmdletter.
    
        $filePathForCert = "C:\cert\Mycertificatename.cer"
        $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
        $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
        $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64

## <a name="creategateway"></a>Del 4 – Opret en VPN-gateway

Konfigurere og oprette gatewayen virtuelt netværk for din VNet. *-GatewayType* skal være **Vpn** og *-VpnType* skal være **RouteBased**. Det kan tage op til 45 minutter at gennemføre.

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
        -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
        -VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
        -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## <a name="clientconfig"></a>Del 5 – Download pakken VPN-klient konfiguration

Klienter opretter forbindelse til Azure ved hjælp af P2S skal have både et klientcertifikat og en VPN-klient konfiguration-pakke, der er installeret. VPN-klient konfigurationspakker fås til Windows-klienter. VPN-klientpakken indeholder oplysninger for at konfigurere VPN-klient-software, der er indbygget i Windows og er specifik for VPN-Forbindelsen, du vil oprette forbindelse til. Pakken installerer ikke yderligere software. Se [Ofte stillede spørgsmål om VPN-Gateway](vpn-gateway-vpn-faq.md#point-to-site-connections) kan finde flere oplysninger.

1. Når gatewayen er blevet oprettet, kan du hente pakken klient konfiguration. I dette eksempel henter installationspakken til 64-bit-klienter. Hvis du vil hente 32-bit-klienten, skal du erstatte 'Amd64' med 'x86'. Du kan også hente VPN-klienten ved hjælp af portalen Azure.

        Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
        -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64

2. PowerShell-cmdlet returnerer en URL-hyperlink. Dette er et eksempel på, hvad de returnerede URL-adressen ser sådan ud:

        "https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"

3. Kopiere og indsætte det link, der returneres til en webbrowser for at hente pakker. Derefter installere pakken på klientcomputeren. Hvis du får en SmartScreen-pop op-vindue, klik på **mere**, derefter **køre alligevel** for at installere pakken.

4. Gå til **Netværksindstillinger** på klientcomputeren, og klik på **VPN**. Du får vist den forbindelse, der er angivet. Det viser navnet på den virtuelle netværk, der skal oprettes forbindelse til og ser ligner dette eksempel: 

    ![VPN-klient] (./media/vpn-gateway-howto-point-to-site-rm-ps/vpn.png "VPN-klient")


## <a name="clientcertificate"></a>Del 6 – Installer klientcertifikat

Hver klientcomputer skal have et klientcertifikat for at godkende. Når du installerer klientcertifikat, skal du den adgangskode, der blev oprettet, da klientcertifikatet blev eksporteret.

1. Kopiere .pfx-fil til klientcomputeren.
2. Dobbeltklik på .pfx-fil for at installere den. Installationsplaceringen må ikke ændres.

## <a name="connect"></a>Del 7 - oprette forbindelse til Azure

1. Gå til VPN-forbindelser for at oprette forbindelse til din VNet på klientcomputeren, og Find den VPN-forbindelse, du har oprettet. Den hedder det samme navn som netværket virtuel. Klik på **Opret forbindelse**. Pop op-vises muligvis en meddelelse, som refererer til at bruge certifikatet. Hvis dette sker, skal du klikke på **Fortsæt** for at bruge administratorrettigheder. 

2. Klik på **Opret forbindelse** for at starte forbindelsen på siden **forbindelsen** status. Hvis du ser en **Vælg certifikat** skærmbillede, skal du kontrollere, klientcertifikat, der viser er den effekt, du vil bruge til at oprette forbindelse. Brug pil ned til at vælge det rigtige certifikat, hvis det ikke er, og klik derefter på **OK**.

    ![VPN klientforbindelse] (./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "VPN klientforbindelse")

3. Din forbindelse bør nu oprettes.

    ![Oprettet forbindelse] (./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "Oprettet forbindelse")

## <a name="verify"></a>Del 8 - Kontrollér din forbindelse

1. Åbn en kommandoprompt for at bekræfte, at din VPN-forbindelse er aktiv, og kør *ipconfig/alle*.

2. Få vist resultaterne. Bemærk, at du har modtaget IP-adressen er en af adresserne i punkt-til-websted VPN-klient adressegruppe, som du har specificeret i konfigurationen. Resultaterne skal være noget nogenlunde sådan ud:
    
        PPP adapter VNet1:
            Connection-specific DNS Suffix .:
            Description.....................: VNet1
            Physical Address................:
            DHCP Enabled....................: No
            Autoconfiguration Enabled.......: Yes
            IPv4 Address....................: 172.16.201.3(Preferred)
            Subnet Mask.....................: 255.255.255.255
            Default Gateway.................:
            NetBIOS over Tcpip..............: Enabled

## <a name="addremovecert"></a>Tilføje eller fjerne et certifikat fra der er tillid til

Certifikater bruges til at godkende VPN-klienter til punkt-til-Site VPN. Følgende trin hjælper dig med at tilføje og fjerne rodcertifikater. Når du føjer en Base64-kodet X.509 (.cer)-fil til Azure, får du besked om Azure at have tillid til det certifikat, der repræsenterer filen. 

Du kan tilføje eller fjerne rodcertifikater ved hjælp af PowerShell eller i portalen Azure. Hvis du vil gøre dette ved hjælp af portalen Azure, skal du gå til din **virtuelt netværksgateway > Indstillinger > konfiguration af punkt-til-websted > Root certifikater**. Følgende trin fører dig igennem disse opgaver ved hjælp af PowerShell. 

### <a name="add-a-trusted-root-certificate"></a>Tilføje et certifikat fra der er tillid til

Du kan føje op til 20 der er tillid til certifikatet .cer-filer til Azure. Følg nedenstående trin for at tilføje et certifikat fra.

1. Oprette og forberede den nye certifikat, du vil tilføje til Azure. Eksportere den offentlige nøgle, som en Base-64-kodet X.509 (. Virksomheden) og åbne den i et tekstredigeringsprogram. Kopiér den sektion, der er vist nedenfor. 
 
    Kopiér værdierne, som vist i følgende eksempel:

    ![certifikat] (./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png "certifikat")
    
2. Angiv navn på certifikat og vigtige oplysninger som en variabel. Erstat oplysningerne med dine egne, som vist i følgende eksempel:

        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"


3. Tilføje den nye certifikat. Du kan kun tilføje ét certifikat ad gangen.

        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2


4. Du kan kontrollere, at det nye certifikat blev tilføjet korrekt ved hjælp af følgende cmdlet.

        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

### <a name="to-remove-a-trusted-root-certificate"></a>Fjerne et certifikat fra der er tillid til

Du kan fjerne der er tillid til certifikat fra Azure. Når du fjerner et certifikat, der er tillid til, vil klientcertifikater, der er dannet ud fra certifikatet ikke længere kunne oprette forbindelse til Azure via punkt-til-websted. Hvis du vil klienter mulighed for at oprette forbindelse, skal de installere en ny klientcertifikat, der er dannet ud fra et certifikat, der er tillid til i Azure.

1. Hvis du vil fjerne et certifikat fra der er tillid til, skal du ændre i følgende eksempel:

    Erklære variablerne.

        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

2. Fjerne certifikatet.

        Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
 
3. Brug følgende cmdlet til at bekræfte, at certifikatet, der er blevet fjernet. 

        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

## <a name="revoke"></a>Sådan administreres listen over på computeren tilbagekaldte klientcertifikater

Du kan ophæve klientcertifikater. Listen over tilbagekaldte certifikater kan du selektivt nægte punkt-til-Site connectivity baseret på individuelle klientcertifikater. Hvis du fjerner en root certificate .cer fra Azure, tilbagekalder adgangen til alle klientcertifikater oprettet/logget af rodcertifikatet, tilbagekaldt. Hvis du vil ophæve et bestemt klientcertifikat, ikke i roden, kan du gøre det. På denne måde, andre certifikater, der er dannet ud fra certifikatet, der stadig er gyldige.

Almindelig praksis er at bruge certifikatet til at administrere adgang på team eller organisation niveauer, mens du bruger på computeren tilbagekaldte klientcertifikater til detaljerede adgangskontrol på de enkelte brugere.

### <a name="revoke-a-client-certificate"></a>Ophæve et klientcertifikat

1. Få miniature klientcertifikat ophæve.

        $RevokedClientCert1 = "ClientCert1"
        $RevokedThumbprint1 = "‎ef2af033d0686820f5a3c74804d167b88b69982f"

2. Tilføj miniature til listen over tilbagekaldte miniature.

        Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

3. Kontrollere, at miniature blev føjet til listen over tilbagekaldte certifikater. Du skal tilføje en miniature ad gangen.

        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### <a name="reinstate-a-client-certificate"></a>Genaktivere et klientcertifikat

Du kan genaktivere et klientcertifikat ved at fjerne miniature på listen over tilbagekaldte klientcertifikater.

1.  Fjern miniature fra listen over tilbagekaldte klient certifikat miniature.

        Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

2. Markér, hvis miniature er fjernet fra listen over tilbagekaldte.

        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## <a name="next-steps"></a>Næste trin

Du kan føje en virtuel maskine til netværket virtuel. Se [oprette en virtuel maskine](../virtual-machines/virtual-machines-windows-hero-tutorial.md) for trin.