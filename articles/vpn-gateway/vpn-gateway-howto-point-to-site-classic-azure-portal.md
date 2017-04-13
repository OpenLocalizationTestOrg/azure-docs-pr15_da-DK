<properties
   pageTitle="Konfigurere en VPN-punkt-til-Site gateway-forbindelse til en Azure virtuelt netværk ved hjælp af portalen Azure | Microsoft Azure"
   description="Oprette sikker forbindelse til netværket Azure virtuelle ved at oprette en VPN-punkt-til-Site gateway-forbindelse ved hjælp af portalen Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="cherylmc"/>

# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal"></a>Konfigurere en punkt-til-Site forbindelse til en VNet ved hjælp af portalen Azure

> [AZURE.SELECTOR]
- [Ressourcestyring - Azure-portalen](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- [Ressourcestyring - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Klassisk - Azure-portalen](vpn-gateway-howto-point-to-site-classic-azure-portal.md)

I denne artikel vejledes du gennem oprettelse af en VNet med en punkt-til-Site forbindelse i modellen Klassisk installation ved hjælp af portalen Azure. Konfiguration af et punkt-til-websted (P2S) kan du oprette en sikker forbindelse fra den enkelte klientcomputer til et virtuelt netværk. En P2S forbindelse er nyttig, når du vil oprette forbindelse til din VNet fra en fjernplacering, f.eks, fra home eller en konference. Eller når du kun har nogle klienter, der har brug for at oprette forbindelse til et virtuelt netværk.

Punkt-til-Site forbindelser kræver ikke en VPN-enhed eller en offentligt IP-adresse til at arbejde. Er oprettet en VPN-forbindelse ved at starte forbindelsen fra klientcomputeren. Se [Ofte stillede spørgsmål om VPN-Gateway](vpn-gateway-vpn-faq.md#point-to-site-connections) og [Om VPN-Gateway](vpn-gateway-about-vpngateways.md#point-to-site)kan finde flere oplysninger om punkt-til-Site forbindelser.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Installation-modeller og metoder til P2S forbindelser

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

I følgende tabel vises de to installation-modeller og tilgængelig installationsmetoder til P2S konfigurationer. Når en artikel med konfigurationstrinnene er tilgængelig, link vi direkte til den fra denne tabel.

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 

## <a name="basic-workflow"></a>Grundlæggende arbejdsproces 

![Punkt-til-websted-diagram] (./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "punkt-til-websted")


De følgende afsnit fører dig gennem trinnene til at oprette en sikker forbindelse punkt-til-websted til et virtuelt netværk. 

1. Oprette et virtuelt netværk og VPN-gateway
2. Oprette certifikater
3. Overføre .cer-fil
4. Generere pakken VPN-klient konfiguration
5. Konfigurere klientcomputeren
6. Oprette forbindelse til Azure

### <a name="example-settings"></a>Eksempelindstillinger for

Du kan bruge indstillingerne for følgende eksempel:

- **Navn: VNet1**
- **Adresse mellemrum: 192.168.0.0/16**
- **Undernet navn: front end**
- **Undernet adresseområde: 192.168.1.0/24**
- **Abonnement:** Hvis du har mere end ét abonnement, skal du bekræfte, at du bruger det rigtige arbejdsområde.
- **Ressourcegruppe: TestRG**
- **Placering: Af USA**
- **Forbindelsestype: punkt-til-websted**
- **Klient-adresseområde: 172.16.201.0/24**. VPN-klienter, der har forbindelse til VNet ved hjælp af denne punkt-til-Site forbindelse modtager en IP-adresse fra den angivne gruppe.
- **GatewaySubnet: 192.168.200.0/24**. Gateway-undernet skal bruge navnet "GatewaySubnet".
- **Størrelse:** Vælg gatewayen SKU, du vil bruge.
- **Routing Type: dynamisk**

## <a name="vnetvpn"></a>Afsnit 1 - Opret et virtuelt netværk og en VPN-gateway

### <a name="createvnet"></a>Del 1: Oprette et virtuelt netværk

Hvis du ikke allerede har et virtuelt netværk, oprette en. Skærmbilleder, der er angivet som eksempler. Sørg for at erstatte værdierne med dine egne. Hvis du vil oprette en VNet ved hjælp af portalen Azure, skal du følge nedenstående trin: 

1. I en browser, gå til [Azure-portalen](http://portal.azure.com) og, hvis det er nødvendigt, logge på med din Azure-konto.

2. Klik på **Ny**. Skriv "Virtuelt netværk" i feltet **Søg på marketplace** . Find **Virtuelt netværk** fra listen returnerede, og klik for at åbne bladet **Virtuelt netværk** .

    ![Søge efter virtuelt netværk blade] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvnetportal700.png "Søge efter virtuelt netværk blade")

3. Vælg **Klassisk**i bunden af bladet virtuelt netværk på listen **Vælg en implementeringsmodel** , og klik derefter på **Opret**.

    ![Vælg implementeringsmodel] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png "Vælg implementeringsmodel")

4. Konfigurere indstillingerne for VNet på bladet **Opret virtuelt netværk** . I denne blade, skal du tilføje din første adresseområde og et enkelt undernet adresseområde. Når du er færdig med at oprette VNet, kan du gå tilbage og tilføjer yderligere undernet og adresse mellemrum.

    ![Oprette virtuelle netværk blade] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png "Oprette virtuelle netværk blade")

5. Bekræft, at **abonnement** er korrekt. Du kan ændre abonnementer ved hjælp af på rullelisten.

6. Klik på **ressourcegruppe** og enten vælge et eksisterende ressourcegruppe eller oprette en ny ved at skrive et navn til din nye ressourcegruppe. Hvis du opretter en ny gruppe, du navngive ressourcegruppe ifølge din planlagte konfiguration af værdier. Du kan finde flere oplysninger om ressourcegrupper ved at besøge [Azure ressourcestyring oversigt](azure-resource-manager/resource-group-overview.md#resource-groups).

7. Derefter skal du vælge **placering** indstillingerne for din VNet. Placeringen, bestemmer, hvor de ressourcer, du anvender denne VNet er placeret.

8. Vælg **Fastgør til dashboard** , hvis du vil kunne nemt finde din VNet på dashboardet til, og klik derefter på **Opret**.
    
    ![Fastgør til dashboard] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png "Fastgør til dashboard")


9. Når du klikker på Opret, vil du se et felt på dit dashboard, der vil afspejle status for din VNet. Feltet ændres, når VNet oprettes.

    ![Oprette virtuelle netværk side om side] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png "Oprette virtuelle netværk side om side")

10. Når du opretter dit virtuelle netværk, kan du tilføje en DNS-server IP-adresse for at kunne håndtere navneoversættelse. Åbn indstillingerne for dit virtuelle netværk, skal du klikke på DNS-servere, og Tilføj IP-adressen på den DNS-server, du vil bruge. Denne indstilling oprette ikke en ny DNS-server. Sørg for at tilføje en DNS-server, som dine ressourcer kan kommunikere med.

Når netværket virtuel er blevet oprettet, vises der **oprettet** vises under **Status** på siden netværk i portalen Azure klassisk.

### <a name="gateway"></a>Del 2: Oprette gatewayen undernet og en dynamisk routing gateway

I dette trin skal oprette du en gateway-undernet og en dynamisk routing gateway. På portalen Azure for den klassiske implementeringsmodel kan oprettelse af gateway-undernettet og gatewayen udføres gennem de samme konfiguration blade.

1. Gå til det virtuelle netværk, du vil oprette en gateway på portalen.

2. Klik på **Gateway**bladet for netværket virtuel på bladet **Oversigt** i sektionen VPN-forbindelser.

    ![Klik her for at oprette en gateway] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png "Klik her for at oprette en gateway")


3. Vælg **punkt-til-websted på bladet **Ny VPN-forbindelse** **.

    ![P2S forbindelsestype] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png "P2S forbindelsestype")

4. Tilføj IP-adresseområder for **Klient-adresseområde**. Dette er det område, hvor VPN-klienter, modtager en IP-adresse, når der oprettes forbindelse. Slette det automatisk udfyldt område og tilføje dine egne.

    ![Klient-adresseområde] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png "Klient-adresseområde")

5. Markér afkrydsningsfeltet **Opret gateway med det samme** .

    ![Opret gateway med det samme] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/creategwimm.png "Opret gateway med det samme")

6. Klik på **valgfrit gateway konfiguration** for at åbne bladet **Gateway konfiguration** .

    ![Klik på konfiguration af valgfrit gateway] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png "Klik på konfiguration af valgfrit gateway")

7. Klik på **undernet konfigurere påkrævet indstillinger** for at tilføje **gateway undernet**. Selvom det er muligt at oprette en gateway-undernet så lille som /29, anbefaler vi, at du opretter et større undernet, der indeholder flere adresser ved at vælge mindst /28 eller /27. Dette giver mulighed for nok adresser til at medtage yderligere konfigurationer, der kan du i fremtiden.

    >[AZURE.IMPORTANT] Når du arbejder med gateway undernet, kan du undgå at tilknytte en netværk sikkerhedsgruppe (NSG) til gateway-undernettet. At tilknytte en netværk sikkerhedsgruppe til denne undernet, kan det medføre, at din VPN-gateway stoppe fungerer som forventet. Finde flere oplysninger om netværk sikkerhedsgrupper [Hvad er en sikkerhedsgruppe netværk?](../articles/virtual-network/virtual-networks-nsg.md)

    ![Tilføje GatewaySubnet] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png "Tilføje GatewaySubnet")

8. Vælg gatewayen **størrelse**. Dette er gatewayen SKU, du vil bruge til at oprette din gateway, virtuelt netværk. I portalen er den standard SKU **grundlæggende**. Se [Om VPN-Gateway indstillinger](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku)kan finde flere oplysninger om gateway lagerenheder.

    ![gatewayen størrelse](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)

9. Vælg den **Type Routing** til din gateway. P2S konfigurationer kræver en **dynamisk** routing type. Klik på **OK** , når du er færdig med at konfigurere denne blade.

    ![Konfigurer routing type] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png "Konfigurer routing type")

10. Klik på **OK** i nederst del af bladet for at begynde at oprette din gateway, virtuelt netværk bladet **Ny VPN-forbindelse** . Det kan tage op til 45 minutter at gennemføre. 


## <a name="generatecerts"></a>Afsnit 2 – oprette certifikater

Certifikater bruges af Azure til at godkende VPN-klienter til punkt-til-Site VPN. Du kan eksportere offentlige certifikat data (ikke privat nøgle), som en Base-64-kodet x.509-.cer-fil fra et certifikat fra genereres af en virksomhedsløsning certifikat eller et selvsigneret certifikat. Du derefter importere offentlige certifikatets data fra certifikatet til Azure. Desuden skal du oprette et klientcertifikat fra certifikatet for klienter. Hver klient, som ønsker at oprette forbindelse til det virtuelle netværk ved hjælp af en P2S forbindelse skal have et klientcertifikat installeret, der er oprettet ud fra certifikatet.

### <a name="cer"></a>Del 1: Hente .cer-filen til certifikatet


Hvis du bruger en virksomhedsløsning, kan du bruge din eksisterende certifikatkæde. Hvis du ikke bruger en enterprise CA løsning, kan du oprette et selvsigneret rod cert. En metode til at oprette et selvsigneret certifikat er makecert.

- Hvis du bruger et enterprise certifikat system, kan du få .cer-filen til det certifikat, du vil bruge. 

- Hvis du ikke bruger en virksomhedsløsning certifikatet, skal du oprette et selvsigneret certifikat. Til Windows 10 trin, kan du referere til at [arbejde med selvsignerede rodcertifikater punkt-til-Site konfigurationer](vpn-gateway-certificates-point-to-site.md).

1. Åbn **certmgr.msc** for at få en .cer-fil fra et certifikat, og Find certifikatet. Højreklik på det selvsignerede certifikat, skal du klikke på **alle opgaver**, og klik derefter på **Eksporter**. Dette åbner **Certifikat guiden Eksporter**.

2. Klik på **Næste**i guiden, Vælg **Nej, Eksporter ikke den private nøgle**, og klik derefter på **Næste**.

3. På siden **Filformat til eksport** Vælg **Base-64-kodet X.509 (. Virksomheden).** Klik derefter på **Næste**. 

4. På den **fil skal eksporteres**, **Gå** til den placering, hvor du vil eksportere certifikat. Navngiv certifikatfilen for **filnavn**. Klik derefter på **Næste**.

5. Klik på **Udfør** for at eksportere certifikat.


### <a name="genclientcert"></a>Del 2: Oprette et klientcertifikat

Du kan enten oprette et entydigt certifikat for hver klient, som opretter forbindelse, eller du kan bruge det samme certifikat på flere forskellige kunder. Fordelen ved oprettelse af entydige klientcertifikater er muligheden for at ophæve et enkelt certifikat, hvis det er nødvendigt. Ellers hvis alle bruger den samme klientcertifikat, og du kan finde, du vil ophæve certifikat for en kunde, skal du oprette og installere nye certifikater for alle de klienter, der bruger dette certifikat til at godkende.

- Hvis du bruger en virksomhedsløsning certifikat skal generere et klientcertifikat med almindelige værdi navneformatet 'name@yourdomain.com', i stedet for at formatet 'domænenavn\brugernavn'. 

- Hvis du bruger et selvsigneret certifikat skal du se [arbejde med selvsignerede rodcertifikater punkt-til-Site konfigurationer](vpn-gateway-certificates-point-to-site.md) til at generere et klientcertifikat.

### <a name="exportclientcert"></a>Del 3: Eksportere klientcertifikat

Installere et klientcertifikat på alle computere, du vil oprette forbindelse til det virtuelle netværk. Et klientcertifikat er påkrævet til godkendelse. Du kan automatisere installerer klientcertifikat, eller du kan installere den manuelt. Følgende trin hjælper dig med eksport og installation af klientcertifikat manuelt.

1. Hvis du vil eksportere et klientcertifikat, kan du bruge *certmgr.msc*. Højreklik på det klientcertifikat, du vil eksportere, klik på **alle opgaver**, og klik derefter på **Eksporter**.
2. Eksportere klientcertifikat med den private nøgle. Dette er en *.pfx* -fil. Sørg for at optage eller huske adgangskoden (nøgle), som du angiver for dette certifikat.

## <a name="upload"></a>Afsnit 3 – Overfør root certificate .cer-fil

Når gatewayen er blevet oprettet, kan du overføre .cer-filen til et der er tillid til certifikat til Azure. Du kan overføre filer til op til 20 rodcertifikater. Du ikke overføre den private nøgle for certifikatet til Azure. Når .cer-filen er overført, bruges Azure til at godkende klienter, der har forbindelse til det virtuelle netværk.

1. Klik på **klienter** grafikken til Åbn **punkt-til-webstedet VPN-forbindelse i afsnittet **VPN-forbindelser** i bladet for din VNet** blade.

    ![Klienter] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png "Klienter")

2. På **punkt-til-site forbindelsen** blade, klik på **Administrer certifikater** for at åbne bladet **certifikater** .<br>

    ![Certifikater blade](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png "Certificates blade")<br><br>


3. Klik på **Overfør** for at åbne bladet **Upload certifikat** bladet **certifikater** .<br>

    ![Overføre certifikater blade](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png "Upload certificates blade")<br>


4. Klik på den mappe grafik til at søge efter .cer-filen. Vælg fil, og klik derefter på **OK**. Opdater siden for at få vist de overførte certifikater på bladet **certifikater** .

    ![Overføre certifikat](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png "Upload certificate")<br>
    

## <a name="vpnclientconfig"></a>Del 4 - generere pakken VPN-klient konfiguration

Hvis du vil oprette forbindelse til det virtuelle netværk, skal du også konfigurere en VPN-klient. Klientcomputeren kræver både et klientcertifikat og pakken med stort VPN-klient konfiguration for at oprette forbindelse.

VPN-klientpakken indeholder konfigurationsoplysninger for at konfigurere den VPN-klientsoftware, der er indbygget i Windows. Pakken installerer ikke yderligere software. Indstillingerne er specifikke for det virtuelle netværk, du vil oprette forbindelse til. Liste over klientoperativsystemer, der understøttes, se [punkt-til-Site forbindelserne](vpn-gateway-vpn-faq.md#point-to-site-connections) sektion af VPN-Gateway ofte stillede spørgsmål. 

### <a name="to-generate-the-vpn-client-configuration-package"></a>Til at generere pakken VPN-klient konfiguration

1. Klik på klient grafikken til Åbn **punkt-til-webstedet VPN-forbindelse i portalen Azure i bladet **Oversigt** for din VNet i **VPN-forbindelser**,** blade.
2. Øverst på **punkt-til-webstedet VPN forbindelse** blade, klik på download pakken, der svarer til klientens operativsystem, hvor det skal være installeret:

 - Vælg **VPN-klient (64-bit)**til 64-bit-klienter.
 - Vælg **VPN-klient (32-bit)**til 32-bit-klienter.

    ![Hent VPN-klient konfigurationspakke](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png "Download VPN client configuration package")<br>


3. Du får vist en meddelelse, Azure genererer VPN-klient konfigurationspakke til det virtuelle netværk. Pakken genereres efter et par minutter, og du får vist en meddelelse på din lokale computer, at pakken er hentet. Gemme pakke konfigurationsfil. Du kan installere det på hver klientcomputer, der opretter forbindelse til det virtuelle netværk ved hjælp af P2S.


## <a name="clientconfiguration"></a>Trin 5 - konfigurere klientcomputeren

### <a name="part-1-install-the-client-certificate"></a>Del 1: Installer klientcertifikat

Hver klientcomputer skal have et klientcertifikat for at godkende. Når du installerer klientcertifikat, skal du den adgangskode, der blev oprettet, da klientcertifikatet blev eksporteret.

1. Kopiere .pfx-fil til klientcomputeren.
2. Dobbeltklik på .pfx-fil for at installere den. Installationsplaceringen må ikke ændres.

### <a name="part-2-install-the-vpn-client-configuration-package"></a>Del 2: Installer pakken VPN-klient konfiguration

Du kan bruge den samme VPN-klient konfigurationspakke på hver klientcomputer, forudsat at versionen svarer til arkitekturen for klienten.

1. Kopiere konfigurationsfilen lokalt til den computer, du vil oprette forbindelse til netværket virtuel og dobbeltklikke på .exe-filen. 

2. Når pakken er installeret, kan du starte VPN-forbindelse. Konfigurationspakken er ikke logget af Microsoft. Du muligvis vil signere pakken ved hjælp af din organisations signerende tjenesten eller logge den dig selv med [SignTool]( http://go.microsoft.com/fwlink/p/?LinkId=699327). Er det OK at bruge funktionen pakke uden at logge. Men hvis pakken ikke er logget, vises en advarsel når du installerer pakken.

3. Gå til **Netværksindstillinger** på klientcomputeren, og klik på **VPN**. Du får vist den forbindelse, der er angivet. Det viser navnet på det virtuelle netværk, der opretter forbindelse til, og ser nogenlunde sådan ud: 

    ![VPN-klient] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vpn.png "VNet VPN-klient")

## <a name="connect"></a>Afsnit 6 - oprette forbindelse til Azure

### <a name="connect-to-your-vnet"></a>Oprette forbindelse til din VNet

1. Gå til VPN-forbindelser for at oprette forbindelse til din VNet på klientcomputeren, og Find den VPN-forbindelse, du har oprettet. Den hedder det samme navn som netværket virtuel. Klik på **Opret forbindelse**. Pop op-vises muligvis en meddelelse, som refererer til at bruge certifikatet. Hvis dette sker, skal du klikke på **Fortsæt** for at bruge administratorrettigheder. 

2. Klik på **Opret forbindelse** for at starte forbindelsen på siden **forbindelsen** status. Hvis du ser en **Vælg certifikat** skærmbillede, skal du kontrollere, klientcertifikat, der viser er den effekt, du vil bruge til at oprette forbindelse. Brug pil ned til at vælge det rigtige certifikat, hvis det ikke er, og klik derefter på **OK**.

    ![Oprette forbindelse] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png "VPN klientforbindelse")

3. Din forbindelse bør nu oprettes.

    ![ESTABLISHED forbindelse] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png "Oprettet forbindelse")

### <a name="verify-the-vpn-connection"></a>Bekræfte VPN-forbindelse

1. Åbn en kommandoprompt for at bekræfte, at din VPN-forbindelse er aktiv, og kør *ipconfig/alle*.
2. Få vist resultaterne. Bemærk, at du har modtaget IP-adressen er en af adresserne inden for området punkt-til-Site connectivity adresse, du angav, da du oprettede dit VNet. Resultaterne skal være noget nogenlunde sådan ud:

Eksempel:



    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled

## <a name="next-steps"></a>Næste trin

Du kan føje virtuelle maskiner til netværket virtuel. Se, [hvordan du opretter en brugerdefineret virtuel maskine](../virtual-machines/virtual-machines-windows-classic-createportal.md).