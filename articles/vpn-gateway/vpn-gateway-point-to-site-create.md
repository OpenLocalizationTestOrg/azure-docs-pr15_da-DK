<properties
   pageTitle="Konfigurere en VPN-punkt-til-Site gateway-forbindelse til en Azure virtuelt netværk ved hjælp af portalen klassisk | Microsoft Azure"
   description="Oprette sikker forbindelse til netværket Azure virtuelle ved at oprette en VPN-punkt-til-Site gateway-forbindelse."
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

# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-classic-portal"></a>Konfigurere en punkt-til-Site forbindelse til en VNet ved hjælp af portalen klassisk

> [AZURE.SELECTOR]
- [Ressourcestyring - Azure-portalen](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- [Ressourcestyring - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Klassisk - Azure-portalen](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
- [Klassisk - klassisk Portal](vpn-gateway-point-to-site-create.md)

Konfiguration af et punkt-til-websted (P2S) kan du oprette en sikker forbindelse fra den enkelte klientcomputer til et virtuelt netværk. En P2S forbindelse er nyttig, når du vil oprette forbindelse til din VNet fra en fjernplacering, f.eks, fra home eller en konference, eller hvis du kun har nogle klienter, der har brug for at oprette forbindelse til et virtuelt netværk.

I denne artikel vejledes du gennem oprettelse af en VNet med en punkt-til-Site forbindelse i **Klassisk implementeringsmodel** ved hjælp af **Klassisk portal**.

Punkt-til-Site forbindelser kræver ikke en VPN-enhed eller en offentligt IP-adresse til at arbejde. Er oprettet en VPN-forbindelse ved at starte forbindelsen fra klientcomputeren. Se [Ofte stillede spørgsmål om VPN-Gateway](vpn-gateway-vpn-faq.md#point-to-site-connections) og [planlægning og Design](vpn-gateway-plan-design.md)kan finde flere oplysninger om punkt-til-Site forbindelser.


### <a name="deployment-models-and-methods-for-p2s-connections"></a>Installation-modeller og metoder til P2S forbindelser

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

I følgende tabel vises de to installation-modeller og tilgængelig installationsmetoder til P2S konfigurationer. Når en artikel med konfigurationstrinnene er tilgængelig, link vi direkte til den fra denne tabel.

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 



## <a name="basic-workflow"></a>Grundlæggende arbejdsproces

![Punkt-til-websted-diagram] (./media/vpn-gateway-point-to-site-create/p2sclassic.png "punkt-til-websted")
 
Følgende trin fører dig gennem trinnene til at oprette en sikker forbindelse punkt-til-websted til et virtuelt netværk. 

Konfiguration for et punkt-til-Site forbindelse opdeles i fire sektioner. Den rækkefølge, hvori du konfigurere hver af disse sektioner er vigtigt. Ikke springe trin eller frem.

- **Sektion 1** Oprette et virtuelt netværk og VPN-gateway.
- **Sektion 2** Oprette certifikater bruges til godkendelse, og overfør dem.
- **Sektion 3** Eksportere og installere din klientcertifikater.
- **Del 4** Konfigurere din VPN-klient.

## <a name="vnetvpn"></a>Afsnit 1 - Opret et virtuelt netværk og en VPN-gateway

### <a name="part-1-create-a-virtual-network"></a>Del 1: Oprette et virtuelt netværk

1. Log på [Azure klassisk portal](https://manage.windowsazure.com/). Disse trin bruge klassisk-portalen, ikke på Azure-portalen. I øjeblikket, kan du oprette en P2S forbindelse ved hjælp af portalen Azure.

2. Klik på **Ny**i nederste venstre hjørne af skærmen. Klik på **Netværkstjenester**i navigationsruden, og klik derefter på **Virtuelt netværk**. Klik på **Brugerdefineret oprette** for at starte konfigurationsguiden.

3. Angiv følgende oplysninger på siden **Virtuelle netværk detaljer** , og klik derefter på næste pilen nederst til højre.
    - **Navn**: Navngiv netværket virtuel. Eksempelvis 'VNet1'. Dette er det navn, du vil henvise til, når du installerer FOS til denne VNet.
    - **Placering**: placeringen er direkte relateret til den fysiske placering (område), hvor du vil dine ressourcer (FOS) skal placeres. Hvis du vil FOS, du anvender denne virtuelle netværk, der skal være fysisk er placeret i af USA, Vælg den nye placering. Du kan ikke ændre det område, der er knyttet til dit virtuelle netværk, du har oprettet.

4. Angiv følgende oplysninger på siden **DNS-servere og VPN-forbindelse** , og klik derefter på næste pilen nederst til højre.
    - **DNS-servere**: Angiv navnet på DNS-server og IP-adresse, eller Vælg en tidligere registrerede DNS-server i genvejsmenuen. Denne indstilling oprette ikke en DNS-server. Det kan du angive de DNS-servere, du vil bruge til navneoversættelse for denne virtuelle netværk. Hvis du vil bruge tjenesten Azure standard navn opløsning, Lad feltet være tomt.
    - **Konfigurer punkt-til-Site VPN**: Markér afkrydsningsfeltet.

5. Angiv den IP-adresseområder, hvorfra VPN-klienter, modtager en IP-adresse, når du har forbindelse på siden **Punkt-til-Site Connectivity** . Der er et par reglerne for de adresseområder, som du kan angive. Det er vigtigt at bekræfte, at det område, du angiver ikke overlapper med en af de områder, der er placeret på dit lokale netværk.

6. Angiv følgende oplysninger, og klik derefter på pilen Næste.
 - **Adresseområde**: angive start-IP- og CIDR (adresse antal).
 - **Tilføj adresseområde**: tilføje adresseområde kun, hvis det er nødvendigt for dit netværksdesign.

7. Angiv det adresseområde, du vil bruge til netværket virtuel på siden **Virtuelle netværk adresse mellemrum** . Dette er de dynamiske IP-adresser (FALD), der skal tildeles til FOS og andre rolle forekomster, du anvender denne virtuelle netværk.<br><br>Det er især vigtigt at markere et område, der ikke overlapper med en af de områder, der bruges til dit lokale netværk. Du skal være koordineret med din netværksadministrator, hvem der kan være nødvendigt at skærer ud af et område af IP-adresser fra dit lokale netværk adresseområde så du kan bruge til dit virtuelle netværk.

8. Angiv følgende oplysninger, og klik derefter på markering for at starte oprettelsen netværket virtuel.
 - **Adresseområde**: tilføje den interne IP-adresseområder, du vil bruge til denne virtuelle netværk, inklusive første IP- og antal. Det er vigtigt at markere et område, der ikke overlapper med en af de områder, der bruges til dit lokale netværk. 
 - **Tilføj undernet**: yderligere undernet er ikke påkrævet, men du overveje at oprette et separat undernet for VM'er, der har statisk FALD. Eller du måske har din FOS i et undernet, der er adskilt fra dine andre rolle forekomster.
 - **Tilføj gateway undernet**: gateway-undernet er påkrævet til en VPN-forbindelse til punkt-til-websted. Klik for at tilføje gateway-undernettet. Gateway-undernet bruges kun til gatewayen virtuelt netværk.

9. Når netværket virtuel er blevet oprettet, vises der **oprettet** vises under **Status** på siden netværk i portalen Azure klassisk. Når netværket virtuel er blevet oprettet, kan du oprette din dynamiske routing gateway.

### <a name="part-2-create-a-dynamic-routing-gateway"></a>Del 2: Oprette en dynamisk routing gateway

Typen gateway skal være konfigureret som dynamisk. Statisk routing gateways virker ikke med denne funktion.

1. Klik på det virtuelle netværk, du har oprettet, og gå til siden **Dashboard** i Azure klassisk portalen, på siden **netværk** .

2. Klik på **Opret Gateway**, findes nederst på siden **Dashboard** . Vises en meddelelse, hvor du bliver spurgt **vil du oprette en gateway til virtuelt netværk "VNet1"**. Klik på **Ja** for at starte oprettelsen af gatewayen. Det kan tage omkring 15 minutter, før gatewayen til at oprette.

## <a name="generate"></a>Afsnit 2 – oprette og overføre certifikater

Certifikater bruges til at godkende VPN-klienter til punkt-til-Site VPN. Du kan bruge et certifikat fra genereres af en virksomhedsløsning certifikat, eller du kan bruge et selvsigneret certifikat. Du kan overføre op til 20 rodcertifikater til Azure. Når .cer-filen er overført, kan Azure bruge oplysningerne i den til at godkende klienter, der har et klientcertifikat, der er installeret. Klientcertifikat skal genereres fra det samme certifikat, der repræsenterer .cer-filen.

I dette afsnit kan du gøre følgende:

- Få .cer-filen til et certifikat fra. Dette kan enten være et selvsigneret certifikat, eller du kan bruge systemet enterprise certifikat.
- Overføre .cer-filen til Azure.
- Oprette klientcertifikater.

### <a name="root"></a>Del 1: Hente .cer-filen til certifikatet

Hvis du bruger et enterprise certifikat system, kan du få .cer-filen til det certifikat, du vil bruge. I [del 3](#createclientcert)skal oprette du klientcertifikater fra certifikatet.

Hvis du ikke bruger en virksomhedsløsning certifikat, skal du oprette et selvsigneret certifikat. Til Windows 10 trin, kan du referere til at [arbejde med selvsignerede rodcertifikater punkt-til-Site konfigurationer](vpn-gateway-certificates-point-to-site.md). I artiklen vejleder dig gennem bruge makecert til at oprette et selvsigneret certifikat, og Eksportér derefter .cer-filen.

### <a name="upload"></a>Del 2: Overfør root certificate .cer-filen til portalen Azure klassisk

Føje et pålideligt certifikat til Azure. Når du føjer en Base64-kodet X.509 (.cer)-fil til Azure, får du besked om Azure at have tillid til det certifikat, der repræsenterer filen.

1. Klik på **Overfør et certifikat**i Azure klassisk portalen, på siden **certifikaterne** for netværket virtuel.

2. Søge efter .cer certifikatet på siden **Overføre certifikat** , og klik derefter på markeringen.

### <a name="createclientcert"></a>Del 3: Oprette et klientcertifikat

Dernæst skal oprette klientcertifikater. Du kan enten oprette et entydigt certifikat for hver klient, som opretter forbindelse, eller du kan bruge det samme certifikat på flere forskellige kunder. Fordelen ved oprettelse af entydige klientcertifikater er muligheden for at ophæve et enkelt certifikat, hvis det er nødvendigt. Ellers hvis alle bruger den samme klientcertifikat, og du kan finde, du vil ophæve certifikat for en kunde, skal du oprette og installere nye certifikater for alle de klienter, der bruger certifikatet til at godkende.

- Hvis du bruger en virksomhedsløsning certifikat skal generere et klientcertifikat med almindelige værdi navneformatet 'name@yourdomain.com', i stedet for NetBIOS 'Domæne\brugernavn' formatere. 

- Hvis du bruger et selvsigneret certifikat skal du se [arbejde med selvsignerede rodcertifikater punkt-til-Site konfigurationer](vpn-gateway-certificates-point-to-site.md) til at generere et klientcertifikat.

## <a name="installclientcert"></a>Afsnit 3 - Eksportér, og Installer klientcertifikatet

Installere et klientcertifikat på alle computere, du vil oprette forbindelse til det virtuelle netværk. Et klientcertifikat er påkrævet til godkendelse. Du kan automatisere installerer klientcertifikat, eller du kan installere manuelt. Følgende trin hjælper dig med eksport og installation af klientcertifikat manuelt.

1. Hvis du vil eksportere et klientcertifikat, kan du bruge *certmgr.msc*. Højreklik på det klientcertifikat, du vil eksportere, klik på **alle opgaver**, og klik derefter på **Eksporter**.
2. Eksportere klientcertifikat med den private nøgle. Dette er en *.pfx* -fil. Sørg for at optage eller huske adgangskoden (nøgle), som du angiver for dette certifikat.
3. Kopiere *.pfx* -fil til klientcomputeren. Dobbeltklik på *.pfx* -fil for at installere den på klientcomputeren. Skriv adgangskoden, når der anmodes om. Installationsplaceringen må ikke ændres.

## <a name="vpnclientconfig"></a>Del 4 - konfigurere din VPN-klient

Hvis du vil oprette forbindelse til det virtuelle netværk, skal du også konfigurere en VPN-klient. Klienten kræver både et klientcertifikat og stort VPN-klientkonfiguration for at oprette forbindelse. For at konfigurere en VPN-klient skal du udføre følgende trin i rækkefølge.

### <a name="part-1-create-the-vpn-client-configuration-package"></a>Del 1: Oprette VPN-klient konfigurationspakke

1. Gå til menuen Oversigt over i højre hjørne i Azure klassisk portalen, på siden **Dashboard** for netværket virtuel. Liste over klientoperativsystemer, der understøttes, se [punkt-til-Site forbindelserne](vpn-gateway-vpn-faq.md#point-to-site-connections) sektion af VPN-Gateway ofte stillede spørgsmål. VPN-klientpakken indeholder konfigurationsoplysninger for at konfigurere den VPN-klientsoftware, der er indbygget i Windows. Pakken installerer ikke yderligere software. Indstillingerne er specifikke for det virtuelle netværk, du vil oprette forbindelse til.<br><br>Vælg download pakken, der svarer til klientens operativsystem, hvor det skal være installeret:
 - Klik på **Hent 32-bit klient VPN-pakken**til 32-bit-klienter.
 - Vælg **Download pakken med 64-bit klient VPN**til 64-bit-klienter.

2. Det tager et par minutter til at oprette din klient-pakke. Når pakken er afsluttet, kan du hente filen. *.Exe* -filen, som hentes kan lagres sikkert på din lokale computer.

3. Når du genererer og Hent pakken VPN-klient fra Azure klassisk portal, kan du installere klientpakken på klientcomputeren, hvorfra du vil oprette forbindelse til netværket virtuel. Hvis du planlægger at installere pakken VPN-klient på flere klientcomputere, skal du kontrollere, at de også have et klientcertifikat, der er installeret.

### <a name="part-2-install-the-vpn-configuration-package-on-the-client"></a>Del 2: Installer pakken VPN-konfiguration på klienten

1. Kopiere konfigurationsfilen lokalt til den computer, du vil oprette forbindelse til netværket virtuel og dobbeltklikke på .exe-filen. 

2. Når pakken er installeret, kan du starte VPN-forbindelse. Konfigurationspakken er ikke logget af Microsoft. Du muligvis vil signere pakken ved hjælp af din organisations signerende tjenesten eller logge den dig selv med [SignTool]( http://go.microsoft.com/fwlink/p/?LinkId=699327). Er det OK at bruge funktionen pakke uden at logge. Men hvis pakken ikke er logget, vises en advarsel når du installerer pakken.

3. Gå til **Netværksindstillinger** på klientcomputeren, og klik på **VPN**. Du får vist den forbindelse, der er angivet. Viser navnet på det virtuelle netværk, der opretter forbindelse til, og ser nogenlunde sådan ud: 

    ![VPN-klient] (./media/vpn-gateway-point-to-site-create/vpn.png "VPN-klient")


### <a name="part-3-connect-to-azure"></a>Del 3: Oprette forbindelse til Azure

1. Gå til VPN-forbindelser for at oprette forbindelse til din VNet på klientcomputeren, og Find den VPN-forbindelse, du har oprettet. Den hedder det samme navn som netværket virtuel. Klik på **Opret forbindelse**. Pop op-vises muligvis en meddelelse, som refererer til at bruge certifikatet. Hvis dette sker, skal du klikke på **Fortsæt** for at bruge administratorrettigheder. 

2. Klik på **Opret forbindelse** for at starte forbindelsen på siden **forbindelsen** status. Hvis du ser en **Vælg certifikat** skærmbillede, skal du kontrollere, klientcertifikat, der viser er den effekt, du vil bruge til at oprette forbindelse. Brug pil ned til at vælge det rigtige certifikat, hvis det ikke er, og klik derefter på **OK**.

    ![VPN-klient 2] (./media/vpn-gateway-point-to-site-create/clientconnect.png "VPN klientforbindelse")

3. Din forbindelse bør nu oprettes.

    ![VPN-klient 3] (./media/vpn-gateway-point-to-site-create/connected.png "Klientforbindelse VPN-2")

### <a name="part-4-verify-the-vpn-connection"></a>Del 4: Bekræft VPN-forbindelse

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

Hvis du vil have flere oplysninger om virtuelle netværk, kan du se siden [Virtuelle dokumentationen til netværket](https://azure.microsoft.com/documentation/services/virtual-network/) .
