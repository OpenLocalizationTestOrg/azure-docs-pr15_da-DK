<properties 
   pageTitle="Konfigurere en VPN-punkt-til-Site gateway-forbindelse til virtuelt netværk ved hjælp af implementeringsmodel ressourcestyring og portalen Azure | Microsoft Azure"
   description="Oprette sikker forbindelse til netværket Azure virtuelle ved at oprette en VPN-punkt-til-Site gateway-forbindelse ved hjælp af Ressourcestyring og Azure portalen."
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

# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal"></a>Konfigurere en punkt-til-Site forbindelse til en VNet ved hjælp af portalen Azure

> [AZURE.SELECTOR]
- [Ressourcestyring - Azure-portalen](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- [Ressourcestyring - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Klassisk - Azure-portalen](vpn-gateway-howto-point-to-site-classic-azure-portal.md)

Konfiguration af et punkt-til-websted (P2S) kan du oprette en sikker forbindelse fra den enkelte klientcomputer til et virtuelt netværk. En P2S forbindelse er nyttig, når du vil oprette forbindelse til din VNet fra en fjernplacering, f.eks, fra home eller en konference, eller hvis du kun har nogle klienter, der har brug for at oprette forbindelse til et virtuelt netværk. 

Punkt-til-Site forbindelser kræver ikke en VPN-enhed eller en offentligt IP-adresse til at arbejde. Er oprettet en VPN-forbindelse ved at starte forbindelsen fra klientcomputeren. Se [Ofte stillede spørgsmål om VPN-Gateway](vpn-gateway-vpn-faq.md#point-to-site-connections) og [planlægning og Design](vpn-gateway-plan-design.md)kan finde flere oplysninger om punkt-til-Site forbindelser.

I denne artikel vejledes du gennem oprettelse af en VNet med en punkt-til-Site forbindelse i implementeringsmodel Ressourcestyring, ved hjælp af portalen Azure.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Installation-modeller og metoder til P2S forbindelser

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

I følgende tabel vises de to installation-modeller og tilgængelig installationsmetoder til P2S konfigurationer. Når en artikel med konfigurationstrinnene er tilgængelig, link vi direkte til den fra denne tabel.

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 

## <a name="basic-workflow"></a>Grundlæggende arbejdsproces 

![Punkt-til-websted-diagram] (./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "punkt-til-websted")

### <a name="example"></a>Eksempler på værdier

- **Navn: VNet1**
- **Adresse mellemrum: 192.168.0.0/16**<br>I dette eksempel skal bruge vi kun én adresseområde. Du kan have mere end én adresseområde til din VNet.
- **Undernet navn: front end**
- **Undernet adresseområde: 192.168.1.0/24**
- **Abonnement:** Hvis du har mere end ét abonnement, skal du bekræfte, at du bruger det rigtige arbejdsområde.
- **Ressourcegruppe: TestRG**
- **Placering: Af USA**
- **GatewaySubnet: 192.168.200.0/24**
- **Virtuelt netværk gatewaynavn: VNet1GW**
- **Gatewayen type: VPN**
- **VPN-type: distribuere-baseret**
- **Offentlige IP-adresse: VNet1GWpip**
- **Forbindelsestype: punkt-til-websted**
- **Klient adresse puljen: 172.16.201.0/24**<br>VPN-klienter, der har forbindelse til VNet ved hjælp af denne punkt-til-Site forbindelse modtage en IP-adresse fra klient adresse puljen.

## <a name="before-beginning"></a>Før du begynder

- Kontrollér, at du har et Azure-abonnement. Hvis du ikke allerede har et Azure-abonnement, kan du aktivere din [MSDN abonnement fordele](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller Log op for at få [gratis konto](https://azure.microsoft.com/pricing/free-trial/).
    
## <a name="createvnet"></a>Del 1 – Opret et virtuelt netværk

Hvis du opretter denne konfiguration som en opgave, kan du se [eksempler på værdier](#example).

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]  

### <a name="2-add-additional-address-space-and-subnets"></a>2. tilføje yderligere adresseområde og undernet

Du kan tilføje yderligere adresseområde og undernet til din VNet, når den er blevet oprettet.

[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)] 

### <a name="3-create-a-gateway-subnet"></a>3. Opret en gateway-undernet

Før der oprettes forbindelse til netværket virtuel til en gateway, skal du først oprette gatewayen undernet for det virtuelle netværk, du vil oprette forbindelse. Hvis det er muligt, er det bedst at oprette en gateway-undernet, ved hjælp af en CIDR blok med /28 eller /27 for at give tilstrækkelig IP-adresser for at medtage yderligere fremtidige konfigurationskrav.

Skærmbilleder i dette afsnit er angivet som en reference eksempel. Sørg for at bruge det GatewaySubnet adresseområde, der svarer med de påkrævede værdier til din konfiguration.

#### <a name="to-create-a-gateway-subnet"></a>Oprette en gateway-undernet

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="dns"></a>4. Angiv en DNS-server (valgfrit)

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="creategw"></a>Del 2 – Opret en virtuelt netværksgateway

Punkt-til-site forbindelser kræver følgende indstillinger:

- Gatewayen type: VPN
- VPN-type: distribuere-baseret

### <a name="to-create-a-virtual-network-gateway"></a>Oprette et virtuelt netværksgateway

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="generatecert"></a>Del 3 – oprette certifikater

Certifikater bruges af Azure til at godkende VPN-klienter til punkt-til-Site VPN. Du kan eksportere offentlige certifikat data (ikke privat nøgle), som en Base-64-kodet x.509-.cer-fil fra et certifikat fra genereres af en virksomhedsløsning certifikat eller et selvsigneret certifikat. Du derefter importere offentlige certifikatets data fra certifikatet til Azure. Desuden skal du oprette et klientcertifikat fra certifikatet for klienter. Hver klient, som ønsker at oprette forbindelse til det virtuelle netværk ved hjælp af en P2S forbindelse skal have et klientcertifikat installeret, der er oprettet ud fra certifikatet.

### <a name="getcer"></a>1. få .cer-filen til certifikatet

Hvis du bruger en virksomhedsløsning, kan du bruge din eksisterende certifikatkæde. Hvis du ikke bruger en enterprise CA løsning, kan du oprette et selvsigneret rod cert. En metode til at oprette et selvsigneret certifikat er makecert.

- Hvis du bruger et enterprise certifikat system, kan du få .cer-filen til det certifikat, du vil bruge. 

- Hvis du ikke bruger en virksomhedsløsning certifikatet, skal du oprette et selvsigneret certifikat. Til Windows 10 trin, kan du referere til at [arbejde med selvsignerede rodcertifikater punkt-til-Site konfigurationer](vpn-gateway-certificates-point-to-site.md).

1. Åbn **certmgr.msc** for at få en .cer-fil fra et certifikat, og Find certifikatet. Højreklik på det selvsignerede certifikat, skal du klikke på **alle opgaver**, og klik derefter på **Eksporter**. Dette åbner **Certifikat guiden Eksporter**.

2. Klik på **Næste**i guiden, Vælg **Nej, Eksporter ikke den private nøgle**, og klik derefter på **Næste**.

3. På siden **Filformat til eksport** Vælg **Base-64-kodet X.509 (. Virksomheden).** Klik derefter på **Næste**. 

4. På den **fil skal eksporteres**, **Gå** til den placering, hvor du vil eksportere certifikat. Navngiv certifikatfilen for **filnavn**. Klik derefter på **Næste**.

5. Klik på **Udfør** for at eksportere certifikat.

### <a name="generateclientcert"></a>2. generere et klientcertifikat

Du kan enten oprette et entydigt certifikat for hver klient, som opretter forbindelse, eller du kan bruge det samme certifikat på flere forskellige kunder. Fordelen ved oprettelse af entydige klientcertifikater er muligheden for at ophæve et enkelt certifikat, hvis det er nødvendigt. Ellers hvis alle bruger den samme klientcertifikat, og du kan finde, du vil ophæve certifikat for en kunde, skal du oprette og installere nye certifikater for alle de klienter, der bruger dette certifikat til at godkende.

- Hvis du bruger en virksomhedsløsning certifikat skal generere et klientcertifikat med almindelige værdi navneformatet 'name@yourdomain.com', i stedet for at formatet 'domænenavn\brugernavn'. 

- Hvis du bruger et selvsigneret certifikat skal du se [arbejde med selvsignerede rodcertifikater punkt-til-Site konfigurationer](vpn-gateway-certificates-point-to-site.md) til at generere et klientcertifikat.

### <a name="exportclientcert"></a>3. eksportere klientcertifikat

Et klientcertifikat er påkrævet til godkendelse. Eksportere den efter generering klientcertifikat. Klientcertifikat du eksportere installeres senere på hver klientcomputer.

1. Hvis du vil eksportere et klientcertifikat, kan du bruge *certmgr.msc*. Højreklik på det klientcertifikat, du vil eksportere, klik på **alle opgaver**, og klik derefter på **Eksporter**.
2. Eksportere klientcertifikat med den private nøgle. Dette er en *.pfx* -fil. Sørg for at optage eller huske adgangskoden (nøgle), som du angiver for dette certifikat.

## <a name="addresspool"></a>Del 4 - Tilføj klient adresse puljen

1. Når virtuelt netværk gatewayen er blevet oprettet, kan du gå til sektionen **Indstillinger** af bladet virtuelt netværk gateway. Klik på konfiguration af **punkt-til-websted i sektionen **Indstillinger** ** til at åbne bladet **konfiguration** .

    ![Peg på webstedet blade] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configuration.png "Peg på webstedet blade")

2. **Adressegruppe** er gruppen af IP-adresser, som klienter, der har forbindelse, modtager en IP-adresse. Tilføj adresse puljen, og klik derefter på **Gem**.

    ![klient adresse puljen] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/addresspool.png "klient adresse puljen")

## <a name="uploadfile"></a>Del 5 – Overfør root certificate .cer-fil

Når gatewayen er blevet oprettet, kan du overføre .cer-filen til et der er tillid til certifikat til Azure. Du kan overføre filer til op til 20 rodcertifikater. Du ikke overføre den private nøgle for certifikatet til Azure. Når .cer-filen er overført, bruges Azure til at godkende klienter, der har forbindelse til det virtuelle netværk.

1. Gå til **punkt-til-site konfigurationen** blade. Du kan tilføje .cer-filer i afsnittet **certifikat** i denne blade.

    ![Peg på webstedet blade] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/rootcert.png "Peg på webstedet blade")

2. Sørg for, at du har eksporteret certifikatet fra som en Base-64-kodet x.509 (.cer)-fil. Skal du eksportere den i dette format, så du kan åbne certifikatet med tekstredigeringsprogram.
3. Åbn certifikatet med en teksteditor, såsom Notesblok. Kopiér kun følgende afsnit:

    ![certifikatets data] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/copycert.png "certifikatets data")

4. Indsæt certifikatets data i afsnittet **Offentlige certifikat Data** i portalen. Placere certifikatets navn i feltet **navn** , og klik derefter på **Gem**. Du kan føje op til 20 rodcertifikater.

    ![overføre certifikat] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploadcert.png "overføre certifikat")

## <a name="clientconfig"></a>Del 6 - Hent og Installer pakken VPN-klient konfiguration

Klienter opretter forbindelse til Azure ved hjælp af P2S skal have både et klientcertifikat og en VPN-klient konfiguration-pakke, der er installeret. VPN-klient konfigurationspakker fås til Windows-klienter. 

VPN-klientpakken indeholder oplysninger for at konfigurere VPN-klient-software, der er indbygget i Windows. Konfigurationen er specifikke for VPN-Forbindelsen, du vil oprette forbindelse til. Pakken installerer ikke yderligere software. Se [Ofte stillede spørgsmål om VPN-Gateway](vpn-gateway-vpn-faq.md#point-to-site-connections) kan finde flere oplysninger.

1. Under konfigurationen af **punkt-til-site** blade, klik på **Hent VPN-klient** for at åbne bladet **hente VPN-klient** .

    ![Hente VPN-klient] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/downloadclient.png "Hente VPN-klient")

2. Vælg den korrekte pakke til din kunde, og klik derefter på **Hent**. Vælg **AMD64**for 64-bit-klienter. Vælg **x86**til 32-bit-klienter.

3. Installere pakken på klientcomputeren. Hvis du får en SmartScreen-pop op-vindue, klik på **mere**, derefter **køre alligevel** for at installere pakken.

4. Gå til **Netværksindstillinger** på klientcomputeren, og klik på **VPN**. Du får vist den forbindelse, der er angivet. Det viser navnet på den virtuelle netværk, der skal oprettes forbindelse til og ser ligner dette eksempel: 

    ![VPN-klient] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpn.png "VPN-klient")

## <a name="installclientcert"></a>Del 7 - Installer klientcertifikat

Hver klientcomputer skal have et klientcertifikat for at godkende. Når du installerer klientcertifikat, skal du den adgangskode, der blev oprettet, da klientcertifikatet blev eksporteret.

1. Kopiere .pfx-fil til klientcomputeren.
2. Dobbeltklik på .pfx-fil for at installere den. Installationsplaceringen må ikke ændres.

## <a name="connect"></a>Del 8 - oprette forbindelse til Azure

1. Gå til VPN-forbindelser for at oprette forbindelse til din VNet på klientcomputeren, og Find den VPN-forbindelse, du har oprettet. Den hedder det samme navn som netværket virtuel. Klik på **Opret forbindelse**. Pop op-vises muligvis en meddelelse, som refererer til at bruge certifikatet. Hvis dette sker, skal du klikke på **Fortsæt** for at bruge administratorrettigheder. 

2. Klik på **Opret forbindelse** for at starte forbindelsen på siden **forbindelsen** status. Hvis du ser en **Vælg certifikat** skærmbillede, skal du kontrollere, klientcertifikat, der viser er den effekt, du vil bruge til at oprette forbindelse. Brug pil ned til at vælge det rigtige certifikat, hvis det ikke er, og klik derefter på **OK**.

    ![VPN-klient 2] (./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "VPN klientforbindelse")

3. Din forbindelse bør nu oprettes.

    ![VPN-klient 3] (./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "Klientforbindelse VPN-2")

## <a name="verify"></a>Del 9 - Kontrollér din forbindelse

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

## <a name="add"></a>Tilføje eller fjerne rodcertifikater

Du kan fjerne der er tillid til certifikat fra Azure. Når du fjerner et certifikat, der er tillid til, vil klientcertifikater, der er dannet ud fra certifikatet ikke længere kunne oprette forbindelse til Azure via punkt-til-websted. Hvis du vil klienter mulighed for at oprette forbindelse, skal de installere en ny klientcertifikat, der er dannet ud fra et certifikat, der er tillid til i Azure.

Du kan administrere listen over på computeren tilbagekaldte klientcertifikater på **punkt-til-site konfigurationen** blade. Dette er bladet, som du brugte til at [overføre et certifikat fra der er tillid til](#uploadfile).

## <a name="revokeclient"></a>Sådan administreres listen over på computeren tilbagekaldte klientcertifikater

Du kan ophæve klientcertifikater. Listen over tilbagekaldte certifikater kan du selektivt nægte punkt-til-Site connectivity baseret på individuelle klientcertifikater. Hvis du fjerner en root certificate .cer fra Azure, tilbagekalder adgangen til alle klientcertifikater oprettet/logget af rodcertifikatet, tilbagekaldt. Hvis du vil ophæve et bestemt klientcertifikat, ikke i roden, kan du gøre det. På denne måde, andre certifikater, der er dannet ud fra certifikatet, der stadig er gyldige. 

Almindelige øvelsen er at bruge certifikatet til at administrere adgang på team eller organisation niveauer, mens du bruger på computeren tilbagekaldte klientcertifikater til detaljerede adgangskontrol på de enkelte brugere.

Du kan administrere listen over på computeren tilbagekaldte klientcertifikater på **punkt-til-site konfigurationen** blade. Dette er bladet, som du brugte til at [overføre et certifikat fra der er tillid til](#uploadfile).


## <a name="next-steps"></a>Næste trin

Du kan føje en virtuel maskine til netværket virtuel. Se [oprette en virtuel maskine](../virtual-machines/virtual-machines-windows-hero-tutorial.md) for trin.