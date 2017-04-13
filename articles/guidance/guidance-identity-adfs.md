<properties
   pageTitle="Implementere ADFS i Azure | Microsoft Azure"
   description="Sådan implementere en sikker hybrid netværksarkitektur med Active Directory Federation Service godkendelse i Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network,active-directory"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/13/2016"
   ms.author="telmos"/>

# <a name="implementing-active-directory-federation-services-adfs-in-azure"></a>Implementering af Active Directory Federation Services (ADFS) i Azure

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

I denne artikel beskriver bedste fremgangsmåder til at implementere en sikker hybrid netværk, der udvider dit lokale netværk til Azure og, der bruger [Active Directory Federation Services (ADFS)] [ active-directory-federation-services] til at udføre samlet godkendelse og autorisation for komponenter, der kører i skyen. Denne arkitektur udvider den struktur, der er beskrevet ved at [Forlænge Active Directory for Azure][implementing-active-directory].

> [AZURE.NOTE] Azure har to forskellige installation modeller: [Ressourcestyring] [ resource-manager-overview] og klassisk. Denne referencearkitektur bruger Ressourcestyring, som Microsoft anbefaler til nye installationer.

ADFS kan køre i det lokale miljø, men i et hybridt scenarie, hvor programmer er placeret i Azure kan det være mere effektivt at implementere denne funktionalitet i skyen. Typisk anvendelse sager for denne arkitektur omfatter:

- Hybrid programmer hvor arbejdsbelastninger Kør delvist i det lokale miljø og delvist i Azure.

- Løsninger, der anvender samlet tilladelse til at fremvise webprogrammer til partnerorganisationer.

- Systemer, der understøtter adgang fra en webbrowser, der kører uden for virksomhedens firewall.

- Systemer, så brugerne kan få adgang til webprogrammer ved at oprette forbindelse fra autoriserede eksterne enheder som eksterne computere, notesbøger og andre mobile enheder. 

Kan finde flere oplysninger om, hvordan ADFS fungerer i [Active Directory Federation Services Overview][active-directory-federation-services-overview]. Desuden i artiklen [ADFS installation i Azure] [ adfs-intro] indeholder en detaljeret trinvise introduktion til implementering af ADFS i Azure.

## <a name="architecture-diagram"></a>Arkitektur diagram

I det følgende diagram fremhæver de vigtige komponenter i denne arkitektur (*Klik for at zoome ind*). Du kan finde flere oplysninger om ethvert element, der ikke er relateret til ADFS læse [implementere en sikker hybrid netværksarkitektur i Azure][implementing-a-secure-hybrid-network-architecture], [implementere en sikker hybrid netværksarkitektur med internetadgang i Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access], og [implementere en sikker hybrid netværksarkitektur med Active Directory identiteter i Azure][implementing-active-directory]:

[! [0]][0]

>[AZURE.NOTE] I dette diagram viser følgende use cases:
>
>- Programmet kode, der kører i en partnerorganisation har adgang til et webprogram, der er hostet i din Azure VNet.
>
>- En ekstern, registrerede bruger (med legitimationsoplysninger, der er gemt i ADDERER) få adgang til et webprogram, der er hostet i din Azure VNet.
>
>- En bruger, der opretter forbindelse til din VNet ved hjælp af en autoriserede enhed og kører et webprogram, der er hostet i din Azure VNet.
>
>Desuden denne arkitektur fokuserer på passive sammenslutning, hvor sammenslutningsserverne beslutte, hvordan og hvornår til at godkende en bruger. Brugeren forventes at give Log på oplysninger, når et program begynder at køre. Dette er den mest almindeligt anvendte af webbrowsere ordning og omfatter en protokol, der omdirigerer browseren til et websted, hvor brugeren kan angive deres legitimationsoplysninger. ADFS understøtter også aktive sammenslutning, hvorved et program overtager ansvaret for angivelse af legitimationsoplysninger uden yderligere brugerinput, men dette tilfælde er uden for omfanget af denne arkitektur.

- **ADDERER undernet.** ADDERER servere befinder sig i deres egen undernet. NSG regler hjælper med at beskytte serverne, der tilføjer og kan give en firewall mod trafik fra uventede kilder.

- **Tilføjer servere.** Dette er domænecontrollere kører som FOS i skyen. Disse servere kan levere godkendelse af lokale identiteter inden for domænet.

- **ADFS undernet.** ADFS-servere kan være placeret i deres egen undernet, med NSG regler, der fungerer som en firewall.

- **ADFS-servere.** ADFS-servere sikre samlet tilladelse og godkendelse. I denne arkitektur udføre de følgende opgaver:

    - De kan modtage sikkerhedstokens, der indeholder krav, der er foretaget af en partner sammenslutningsserveren på vegne af en partner bruger. ADFS kan du kontrollere, at disse tokens er gyldige, før der passerer krav til webprogram, der kører i Azure. Virksomhedens webprogrammet (i Azure) kan bruge disse krav til Godkend anmodninger. I dette scenarie skal virksomhedens webprogrammet er *stole deltagere*, og det er ansvarlig for partner sammenslutningsserveren udsteder udgiver, der er forstået ved virksomhedens webprogrammet. Sammenslutningsservere partner der henvises til som *konto partnere* , fordi de kan sende anmodninger om adgang på vegne af godkendte konti i organisationen partner. ADFS-servere kaldes *ressource partnere* , fordi de giver adgang til ressourcer (webprogrammer, i dette tilfælde).

    - De kan godkende (via tilføjer og [Active Directory enhed registrering Service][ADDRS]) og Godkend indgående anmodninger fra eksterne brugere, der kører en webbrowser eller enhed, der skal have adgang til dine virksomhedens webprogrammer. 

    ADFS-servere er konfigureret som en farm, få adgang til via en Azure justering af belastning. Denne struktur hjælper med at forbedre tilgængelighed og skalerbarhed. Bemærk også, at ADFS-servere vises ikke direkte til internettet, hellere alle internettrafik filtreres gennem ADFS web application proxyservere og en DMZ.

- **ADFS-proxy undernet.** Proxyservere ADFS kan være indeholdt i deres egen undernet, med NSG bestemmelser beskyttelse. Servere i dette undernet er være tilgængelig på internettet via et sæt af netværk virtuelle hjælpemidler, som giver en firewall mellem din Azure virtuelt netværk og internettet.

- **ADFS web application proxy (WAP) servere.** Disse computere fungere som ADFS-servere til indgående anmodninger fra partnerorganisationer og eksterne enheder. WAP servere fungere som et filter, afskærmningen ADFS-servere fra direkte adgang fra det offentlige Internet. Som med ADFS-servere implementerer WAP servere i en farm med belastning giver større tilgængelighed og skalerbarhed end implementere en samling af separate servere.

    >[AZURE.NOTE] Du kan finde detaljerede oplysninger om installation af WAP servere, se [installere og konfigurere Web Application Proxy-Server][install_and_configure_the_web_application_proxy_server]

- **Partnerorganisation.** Dette er et eksempel partnerorganisation, der kører et webprogram, der anmoder om adgang til webprogrammet kører i Azure. Sammenslutningsserveren på partner organisationen godkender anmodninger lokalt og sender sikkerhedstokens, der indeholder krav til ADFS kører i Azure. ADFS i Azure valideret sikkerhedstokens, og hvis de er gyldige det kan overføre krav til webprogrammet kører i Azure til at give dem.

    >[AZURE.NOTE] Du kan også konfigurere en VPN-tunnel med Azure Gateway til at levere direkte adgang til ADFS til-partnere, der er tillid til. Anmodninger modtaget fra disse partnere ikke gå igennem WAP-servere.

## <a name="recommendations"></a>Anbefalinger

Dette afsnit indeholder en oversigt over anbefalinger til implementering af ADFS i Azure, som dækker:

- VM anbefalinger.

- Netværk anbefalinger.

- Tilgængelighed anbefalinger.

- Anbefalinger til sikkerhed.

- ADFS installation anbefalinger.

- ADFS sikkerhed og rettighedsadministration anbefalinger.

### <a name="vm-recommendations"></a>VM anbefalinger

Oprette FOS med tilstrækkelige ressourcer til at håndtere de forventede mængde trafik. Brug størrelsen på de eksisterende maskiner, der er vært for ADFS lokalt som udgangspunkt. Overvåge anvendelsen ressource. Du kan ændre størrelsen på FOS og skalere, hvis de er for stor.

Følg de anbefalinger, der er angivet i [kører et Windows VM på Azure][vm-recommendations].

### <a name="networking-recommendations"></a>Netværk anbefalinger

Konfigurere netværksgrænsefladen for hver af de FOS vært ADFS og WAP servere med statisk private IP-adresser.

Giv ikke ADFS FOS offentlige IP-adresser. Du kan finde flere oplysninger, se [sikkerhedsovervejelser][security-considerations].

Angiv de foretrukne og sekundære DNS-servere for netværk grænsefladerne til hver ADFS og WAP VM refereres den tilføjer FOS (som skal køre DNS) til IP-adresse. Dette trin er nødvendigt at aktivere hver VM til at deltage i domænet.

### <a name="availability-recommendations"></a>Anbefalinger for tilgængelighed

Oprette en ADFS-farm med mindst to servere at øge tilgængeligheden af ADFS-tjenesten.

Du kan bruge forskellige lagerplads konti for hver ADFS VM i farmen. Denne fremgangsmåde hjælper med at sikre, at en fejl på en enkelt lagerplads konto ikke er nogen hele farmen ikke tilgængelig.

Oprette separate Azure tilgængelighed sæt for ADFS og WAP FOS. Sørg for, at der er mindst to FOS i hvert sæt. Hvert tilgængelighed sæt skal have mindst to update domæner og to fejl domæner.

Konfigurere Indlæs balancere for ADFS FOS og WAP FOS på følgende måde:

- Brug en Azure belastningsjustering til at oprette ekstern adgang til WAP FOS samt en intern belastningsjustering til at distribuere belastning på tværs af ADFS-servere i farmen ADFS.

- Kun overføre trafik vises på port 443 (HTTPS) til ADFS/WAP-serverne.

- Giv justering af belastning en statisk IP-adresse.

- Oprette en tilstand efterprøvning af af, ved hjælp af TCP-protokollen i stedet for HTTPS. Du kan pinge port 443 at kontrollere, at en ADFS-server fungerer.

    >[AZURE.NOTE] ADFS-servere bruger protokollen Server navn angivelse (SNI), så forsøger at sende forespørgsler ved hjælp af HTTPS ark fra Indlæs belastningsjusteringstjenesten opstår.

- Tilføje en DNS- *A* -post til domænet for ADFS justering af belastning. 

    Angiv justering af belastning IP-adresse, og give den et navn i domænet (såsom adfs.contoso.com). Dette er det navn, som klienter og WAP servere adgang til serverfarmen ADFS.

### <a name="security-recommendations"></a>Anbefalinger til sikkerhed

Forhindre direkte visning af ADFS-servere på internettet. ADFS-servere er medlem af et domæne computere, der har fuld tilladelse til at give sikkerhedstokens. Hvis en ADFS-server er afsløret, kan en hacker udstede fuld adgangstokens, at alle webprogrammer og sammenslutningsservere, der er beskyttet af ADFS. Hvis dit system skal håndtere anmodninger fra eksterne brugere, der ikke behøver at oprette forbindelse fra partnerwebsteder, der er tillid til, kan du bruge WAP servere til at håndtere disse anmodninger. Se mere, [hvor du vil placere en Sammenslutningsserverproxy][where-to-place-an-fs-proxy].

Sted ADFS-servere og WAP servere i separat undernet med deres egen firewalls. Du kan bruge NSG regler til at definere firewallregler. Hvis du har brug for mere omfattende beskyttelse kan du implementere en ekstra sikkerhed kanten rundt om servere ved hjælp af et par undernet og NVAs, som beskrevet i dokumentet [implementere en sikker hybrid netværksarkitektur med internetadgang i Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]. Bemærk, at alle firewalls skal tillader trafik på port 443 (HTTPS).

Begrænse direkte login adgangen til ADFS og WAP-serverne. Kun DevOps personale skal kunne oprette forbindelse.

Deltag ikke WAP servere til domænet.

### <a name="adfs-installation-recommendations"></a>ADFS installation anbefalinger

I artiklen [anvender en sammenslutning serverfarm] [ Deploying_a_federation_server_farm] indeholder detaljerede instruktioner til installation og konfiguration af ADFS. Inden du konfigurerer den første ADFS-server i farmen, skal du udføre følgende opgaver:

1. Få et offentligt der er tillid til certifikat for at udføre server-godkendelse. *Emnenavn* skal indeholde det navn, som kunder få adgang til tjenesten sammenslutning. Det kan være DNS-navnet registreret for justering af belastning, for eksempel *adfs.contoso.com* (undgå at bruge jokertegn navne såsom **. contoso.com*, af sikkerhedsmæssige årsager). Bruge det samme certifikat på alle ADFS-server FOS. Du kan købe et certifikat fra et nøglecenter, der er tillid til, men hvis din organisation anvender Active Directory Certificate Services kan du oprette din egen. 

    *Alternative emnenavn* bruges af DRS til at aktivere adgang fra eksterne enheder. Det skal være formular *enterpriseregistration.contoso.com*.

    Få mere at vide under [fås og konfigurere et SSL-certifikat til ADFS][adfs_certificates].

2. Opret en ny rod-nøgle for tjenesten nøgle fordeling på domænecontrolleren. Angiv effektive time skal være det aktuelle klokkeslæt minus 10 timer (denne konfiguration reducerer forsinkelsen, der kan opstå i distribuere og synkronisere taster på tværs af domænet). Dette trin er nødvendige for at understøtte oprettelse af den gruppe tjenestekonto, som bruges til at køre tjenesten ADFS. Den følgende Powershell-kommando viser et eksempel på, hvordan du gør dette:

    ```powershell
    Add-KdsRootKey -EffectiveTime (Get-Date).AddHours(-10)
    ```

3. Føje hver ADFS-server VM til domænet.

>[AZURE.NOTE] For at installere ADFS skal være domænecontrolleren kører PDC-emulator FSMO-rolle for domænet, kører og er tilgængelige fra ADFS FOS.

### <a name="adfs-trust-recommendations"></a>ADFS sikkerhed og rettighedsadministration anbefalinger

Oprette sammenslutning tillid mellem din ADFS-installation og sammenslutningsservere af enhver partnerorganisationer. Konfigurere en hvilken som helst krav, filtrering og tilknytninger efter behov. Denne proces kræver:

- DevOps personale **på hver partnerorganisation** for at tilføje en afhængige part tillid til de webprogrammer, der er tilgængelige via din ADFS-servere.

- DevOps ansatte **i organisationen** for at konfigurere krav udbyder tillid for at aktivere din ADFS-servere at have tillid til de krav, som giver partnerorganisationer.

- DevOps ansatte **i organisationen** for at konfigurere ADFS for at overføre krav til din organisations webprogrammer.

    Se Få mere at vide [Om oprettelse af sammenslutning tillid til][establishing-federation-trust].

Udgive din organisations webprogrammer og gøre dem tilgængelige for eksterne partnere ved hjælp af forhåndsgodkendelse gennem WAP-servere. Du kan finde flere oplysninger, se [publicere programmer ved hjælp af ADFS Preauthentication][publish_applications_using_AD_FS_preauthentication]

Bemærk, at ADFS understøtter token transformation og udvidelsen af topologien. Azure Active Directory giver ikke denne funktion. Med ADFS, når du konfigurerer tillidsforhold, kan du:

- Konfigurere Kræv transformationer for regler for godkendelse. For eksempel kan du knytte gruppe sikkerhed fra en repræsentation af en partnerorganisation, ikke-Microsoft til noget, der tilføjer kan Godkend i din organisation.

- Transformere krav fra ét format til en anden. For eksempel kan du knytte fra SAML 2.0 til SAML 1.1 Hvis dit program understøtter kun SAML 1.1 krav. 

## <a name="availability-considerations"></a>Overvejelser i forbindelse med tilgængelighed

Du kan bruge enten SQL Server eller Windows interne Database (arbejdsgruppe-ID'ET) til at holde ADFS konfigurationsoplysninger. Arbejdsgruppe-ID'ET indeholder grundlæggende redundans. Ændringer skrives direkte til kun én af ADFS databaserne i klynge ADFS, mens de andre servere Brug hente gentagelse til at holde deres databaser opdateret. Brug af SQL Server kan give fuld database redundans og høj tilgængelighed ved hjælp af failover klynge eller spejling.

## <a name="security-considerations"></a>Overvejelser om sikkerheden

ADFS anvender HTTPS-protokollen, så skal du kontrollere, at NSG reglerne for det undernet, der indeholder webdelen klasse FOS tilladelse HTTPS-anmodninger. Disse anmodninger kan stammer fra det lokale netværk undernet, der indeholder web niveauet, business niveau, data niveau, private DMZ, offentlige DMZ og det undernet, der indeholder ADFS-serverne.

Overvej at bruge et sæt af netværk virtuelle hjælpemidler, der logger detaljerede oplysninger om trafik gå på tværs kanten af netværket virtuel for overvågning formål.

## <a name="scalability-considerations"></a>Overvejelser i forbindelse med skalerbarhed

Overvej følgende, opsummeres fra dokumentet [planlægge din ADFS-][plan-your-adfs-deployment], give et udgangspunkt for at ændre størrelse ADFS farme:

- Hvis du har færre end 1000 brugere, Opret ikke dedikerede ADFS-servere, men i stedet installere ADFS på hver af tilføjer serverne i skyen (Sørg for, at du har mindst to ADDERER-servere at opretholde tilgængeligheden). Oprette en enkelt WAP-server.

- Hvis du har mellem 1000 og 15000 brugere, kan du oprette to dedikerede ADFS-servere og to dedikerede WAP-servere.

- Hvis du har mellem 15000 og 60000 brugere, kan du oprette mellem tre og fem dedikerede ADFS-servere og mindst to dedikerede WAP-servere.

Disse tal forudsætter, at du bruger to fire grundlæggende FOS (Standard D4_v2 eller bedre) til at hoste servere i Azure.

Bemærk, at hvis du bruger Windows interne Database til at gemme ADFS konfigurationsdata, du er begrænset til otte ADFS-servere i farmen. Hvis du forventer at skulle der er brug for mere, skal du bruge SQL Server. Kan finde flere oplysninger i [Feltet rolle konfigurationsdatabase ADFS][adfs-configuration-database].

## <a name="management-considerations"></a>Overvejelser i forbindelse med administration

DevOps personale skal være forberedt til at udføre følgende opgaver:

- Administration af sammenslutningsservere (administrere ADFS-farm, administration af sikkerhed og rettighedsadministration politik på sammenslutningsserverne og administrere de certifikater, der bruges af federation services).

- Administration af WAP servere (administrere WAP farmen, administrere WAP certifikater).

- Administrere webprogrammer (konfiguration af afhængige parter, godkendelsesmetoder og krav tilknytninger).

- Sikkerhedskopiere ADFS-komponenterne.

## <a name="monitoring-considerations"></a>Overvågning overvejelser i forbindelse med

[Microsoft System Center Management Pack til Active Directory Federation Services 2012 R2] [ oms-adfs-pack] indeholder både proaktiv og aktiv overvågning af din ADFS-sammenslutningsserveren. Denne management pack overvåger:

- Hændelser, at ADFS-poster i ADFS hændelseslogfiler.

- Ydelsesdata, tællerne i ADFS ydeevne indsamler. 

- Den overordnede sundhed for i ADFS-systemet og web-programmer (afhængige parter), og leverer beskeder til alvorlige problemer og advarsler.

## <a name="solution-components"></a>Løsningskomponenter

Et eksempel løsning script, [Implementer ReferenceArchitecture.ps1][solution-script], er tilgængelig, som du kan bruge til at implementere arkitekturen, der følger efter de anbefalinger, der er beskrevet i denne artikel. Dette script anvender Azure ressourcestyring skabeloner. Skabelonerne er tilgængelige som et sæt af grundlæggende dokumentkomponenter, hver især udfører en bestemt handling som oprette en VNet eller konfigurere en NSG. Formålet med scriptet er at dirigerer installation af skabelon.

Skabelonerne, har parametre, sammen med parametrene i separate JSON-filer. Du kan ændre parametrene i disse filer for at konfigurere installationen, så det opfylder dine egne krav. Du behøver ikke at ændre skabelonerne sig selv. Bemærk, at du ikke må ændre skemaer objekter i parameter-filer.

Når du redigerer skabelonerne, du opretter objekter, som følger de navngivningskonventioner, der er beskrevet i [Anbefales navngivningskonvention for perioder for Azure ressourcer][naming-conventions].

Eksempel løsningen opretter og konfigurerer miljøet i skyen består af tilføjer undernet og servere, ADFS undernet og servere, ADFS proxy undernet og -servere DMZ, web niveau, business niveau, og data access niveau komponenter, VPN-gateway og administration af lag. Eksempel-løsning indeholder også en valgfri konfiguration til at oprette en simuleret lokalt miljø.

I følgende afsnit beskrives elementerne i lokale og skyen konfigurationer.

### <a name="on-premises-components"></a>Lokalt komponenter

>[AZURE.NOTE] Disse komponenter er ikke den primære fokus af arkitekturen er beskrevet i dette dokument, og der leveres blot for at give dig mulighed for at teste skyen miljøet sikkert, i stedet for at bruge et reelt tal produktionsmiljø. Derfor i dette afsnit kun indeholder en oversigt over de vigtigste parameter filer. Du kan ændre indstillinger som IP-adresser eller størrelsen på FOS, men det anbefales at lade mange af de andre parametre være uændret.

Dette miljø består af et AD-område for et domæne med navnet contoso.com. Domænet indeholder to tilføjer servere med IP-adresser 192.168.0.4 og 192.168.0.5. Disse to servere kører også DNS-tjenesten. Den lokale administratorkonto på begge FOS hedder `testuser` med adgangskode `AweS0me@PW`. Desuden konfigurerer konfigurationen en VPN-gateway til at oprette forbindelse til VNet i skyen. Du kan ændre konfigurationen ved at redigere de følgende JSON-filer, der er placeret i [**parametre/Premise**] [ on-premises-folder] mappe:

- ** [virtualNetwork.parameters.json][on-premises-vnet-parameters]**. Denne fil definerer adresseområde netværk til det lokale miljø.

- ** [virtualMachines adds.parameters.json][on-premises-virtualmachines-adds-parameters]**. Denne fil indeholder konfigurationen af de lokale FOS tilføjer værtstjenester. Som standard oprettes to *Standard-DS3-v2* FOS.

- ** [virtualNetworkGateway.parameters.json] [ on-premises-virtualnetworkgateway-parameters] ** og ** [connection.parameters.json][on-premises-connection-parameters]**. Disse filer hold indstillingerne for VPN-forbindelse til Azure VPN-gateway i skyen, herunder den delte nøgle skal bruges til at beskytte trafik gå på tværs af gatewayen.

De resterende filer i mappen indeholder konfigurationsoplysninger bruges til at oprette det lokale domæne ved hjælp af denne infrastruktur. Du kan bruge dem til at installere tilføjer, konfigurere DNS, Opret et område, og Konfigurer gentagelse websteder til området.

### <a name="cloud-components"></a>Skyen komponenter

Disse komponenter udgør kernen i denne arkitektur. [**Parametre/azure**] [ azure-folder] mappe indeholder følgende parameter filer til konfiguration af disse komponenter:

- ** [virtualNetwork.parameters.json][vnet-parameters]**. Denne fil definerer strukturen af VNet for FOS og andre komponenter i skyen. Den indeholder indstillinger, som navnet, adresseområde, undernet og adresserne på alle DNS-servere, der er påkrævet. Bemærk, at de DNS-adresser, der er vist i dette eksempel henviser til IP-adresserne på de lokale DNS-servere, og standard Azure DNS-serveren. Ændre disse adresser, hvis du vil indsætte dine egne DNS-konfiguration, hvis du ikke bruger eksempel lokalt miljø:

    ```json
    {
        "virtualNetworkSettings": {
            "value": {
                "name": "ra-adfs-vnet",
                "resourceGroup": "ra-adfs-network-rg",
                "addressPrefixes": [
                    "10.0.0.0/16"
                ],
                "subnets": [
                    {
                        "name": "dmz-private-in",
                        "addressPrefix": "10.0.0.0/27"
                    },
                    {
                        "name": "dmz-private-out",
                        "addressPrefix": "10.0.0.32/27"
                    },
                    {
                        "name": "dmz-public-in",
                        "addressPrefix": "10.0.0.64/27"
                    },
                    {
                        "name": "dmz-public-out",
                        "addressPrefix": "10.0.0.96/27"
                    },
                    {
                        "name": "mgmt",
                        "addressPrefix": "10.0.0.128/25"
                    },
                    {
                        "name": "GatewaySubnet",
                        "addressPrefix": "10.0.255.224/27"
                    },
                    {
                        "name": "web",
                        "addressPrefix": "10.0.1.0/24"
                    },
                    {
                        "name": "biz",
                        "addressPrefix": "10.0.2.0/24"
                    },
                    {
                        "name": "data",
                        "addressPrefix": "10.0.3.0/24"
                    },
                    {
                        "name": "adds",
                        "addressPrefix": "10.0.4.0/27"
                    },
                    {
                        "name": "adfs",
                        "addressPrefix": "10.0.5.0/27"
                    },
                    {
                        "name": "proxy",
                        "addressPrefix": "10.0.6.0/27"
                    }
                ],
                "dnsServers": [
                    "192.168.0.4",
                    "192.168.0.5",
                    "168.63.129.16"
                ]
            }
        }
    }
    ```

- ** [virtualMachines adds.parameters.json ] [ virtualmachines-adds-parameters] **. Denne fil konfigurerer de VM'er, der kører ADDERER i skyen. Konfigurationen består af to FOS. Du skal ændre administrator brugernavn og adgangskode i den `virtualMachineSettings` sektion, og du kan eventuelt redigere VM størrelse for at imødekomme behov for domænenavn:

    Finde flere oplysninger i [Forlænge Active Directory til Azure][extending-ad-to-azure].

    ```json
            "virtualMachinesSettings": {
                "value": {
                    "namePrefix": "ra-adfs-ad",
                    "computerNamePrefix": "aad",
                    "size": "Standard_DS3_v2",
                    "osType": "Windows",
                    "adminUsername": "testuser",
                    "adminPassword": "AweS0me@PW",
                    "osAuthenticationType": "password",
                    "nics": [
                        {
                            "isPublic": "false",
                            "subnetName": "adds",
                            "privateIPAllocationMethod": "Static",
                            "startingIPAddress": "10.0.4.4",
                            "enableIPForwarding": false,
                            "dnsServers": [
                            ],
                            "isPrimary": "true"
                        }
                    ],
                    "imageReference": {
                        "publisher": "MicrosoftWindowsServer",
                        "offer": "WindowsServer",
                        "sku": "2012-R2-Datacenter",
                        "version": "latest"
                    },
                    "dataDisks": {
                        "count": 1,
                        "properties": {
                            "diskSizeGB": 127,
                            "caching": "None",
                            "createOption": "Empty"
                        }
                    },
                    "osDisk": {
                        "caching": "ReadWrite"
                    },
                    "extensions": [
                    ],
                    "availabilitySet": {
                        "useExistingAvailabilitySet": "No",
                        "name": "ra-adfs-as"
                    }
                }
            },
            "virtualNetworkSettings": {
                "value": {
                    "name": "ra-adfs-vnet",
                    "resourceGroup": "ra-adfs-network-rg"
                }
            },
            "buildingBlockSettings": {
                "value": {
                    "storageAccountsCount": 2,
                    "vmCount": 2,
                    "vmStartIndex": 1
                }
            }
        }
    ```

- ** [Tilføj-tilføjer-domæne-controller.parameters.json][add-adds-domain-controller-parameters]**. Denne fil indeholder indstillinger til at oprette det CONTOSO-domæne, der strækker sig over serverne, der tilføjer. Brugerdefinerede udvidelser, oprette domænet og føje tilføjer servere til den bruges. Medmindre du opretter flere tilføjer servere (i dette tilfælde skal du føje dem til den `vms` matrix), ændre deres navne fra standard eller ønsker at oprette et domæne med et andet navn, du ikke behøver at redigere denne fil.

- ** [loadBalancer adfs.parameters.json] [loadbalancer-adfs-parameters] ** Filen indeholder to sæt af konfiguration af parametre. Den `virtualMachineSettings` sektion definerer de VM'er, der hoster ADFS-tjeneste i skyen. Som standard opretter scriptet to af disse FOS i det samme sæt tilgængelighed:

    ```json
        "virtualMachinesSettings": {
            "value": {
                "namePrefix": "ra-adfs-adfs",
                "computerNamePrefix": "adfs",
                "size": "Standard_DS1_v2",
                "osType": "windows",
                "adminUsername": "testuser",
                "adminPassword": "AweS0me@PW",
                "osAuthenticationType": "password",
                "nics": [
                    {
                        "isPublic": "false",
                        "subnetName": "adfs",
                        "privateIPAllocationMethod": "Static",
                        "startingIPAddress": "10.0.5.4",
                        "isPrimary": "true",
                        "enableIPForwarding": false,
                        "dnsServers": [ ]
                    }
                ],
                "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version": "latest"
                },
                "dataDisks": {
                    "count": 1,
                    "properties": {
                        "diskSizeGB": 128,
                        "caching": "None",
                        "createOption": "Empty"
                    }
                },
                "osDisk": {
                    "caching": "ReadWrite"
                },
                "extensions": [ ],
                "availabilitySet": {
                    "useExistingAvailabilitySet": "No",
                    "name": "ra-adfs-adfs-vm-as"
                }
            }
        }
        ...
        "buildingBlockSettings": {
            "value": {
                "storageAccountsCount": 2,
                "vmCount": 2,
                "vmStartIndex": 1
            }
        }
    ```

    Den `loadBalancerSettings` afsnit indeholder en beskrivelse af justering af belastning for disse FOS. Justering af belastning videresender trafik, der vises på port 443 (HTTPS) til én eller andre af FOS:

    ```json
        "loadBalancerSettings": {
            "value": {
                "name": "ra-adfs-adfs-lb",
                "frontendIPConfigurations": [
                    {
                        "name": "ra-adfs-adfs-lb-fe",
                        "loadBalancerType": "internal",
                        "internalLoadBalancerSettings": {
                            "privateIPAddress": "10.0.5.30",
                            "subnetName": "adfs"
                        }
                    }
                ],
                "backendPools": [
                    {
                        "name": "ra-adfs-adfs-lb-bep",
                        "nicIndex": 0
                    }
                ],
                "loadBalancingRules": [
                    {
                        "name": "https-rule",
                        "frontendPort": 443,
                        "backendPort": 443,
                        "protocol": "Tcp",
                        "backendPoolName": "ra-adfs-adfs-lb-bep",
                        "frontendIPConfigurationName": "ra-adfs-adfs-lb-fe",
                        "probeName": "https-probe",
                        "enableFloatingIP": false
                    }
                ],
                "probes": [
                    {
                        "name": "https-probe",
                        "port": 443,
                        "protocol": "Tcp",
                        "requestPath": null
                    }
                ],
                "inboundNatRules": [ ]
            }
        },
        "virtualNetworkSettings": {
            "value": {
                "name": "ra-adfs-vnet",
                "resourceGroup": "johns-adfs-network-rg"
            }
        }
    ```

- ** [adfs-farm-domæne-join.parameters.json ] [ adfs-farm-domain-join-parameters] **. Denne fil indeholder de indstillinger, der bruges til at føje ADFS-serverne til CONTOSO-domæne. Du skal kun bruge til at redigere denne fil, hvis du har oprettet flere ADFS-servere (opdatere den `vms` array i dette tilfælde), eller du har ændret domænenavnet.

- ** [gmsa.parameters.json][gmsa-parameters]**, ** [adfs-farm-first.parameters.json][adfs-farm-first-parameters]**, og ** [adfs-farm-rest.parameters.json][adfs-farm-rest-parameters]**. Scriptet bruger indstillingerne i disse filer til at oprette serverfarmen ADFS. 

    Filen *gmsa.parameters.json* indeholder indstillingerne for den gruppe, der administreres servicekonto, der bruges af tjenesten ADFS. Du kan redigere denne fil, hvis du vil ændre navnet på kontoen eller domænet.

    *Adfs-farm-first.parameters.json* filen indeholder de oplysninger, der er behov for at oprette serverfarmen ADFS og tilføje den første server. Hvis du har skiftet domænet eller navnet på den gruppe, der administreres service-konto, skal du opdatere denne fil.

    Filen *adfs-farm-rest.parameters.json* bruges til at føje de resterende ADFS-servere til farmen. Igen, hvis du har skiftet domænet eller navnet på den gruppe, der administreres service-konto, skal du opdatere denne fil. Opdater den `vms` matrix, hvis du har oprettet flere ADFS-servere.

- ** [loadBalancer adfsproxy.parameters.json][loadBalancer-adfsproxy-parameters]**. Denne fil minder i strukturen og indholdet i filen *loadBalancer adfs.parameters.json* . Den indeholder dataene til opbygning af ADFS proxyservere og justering af belastning.

- ** [adfsproxy-farm-first.parameters.json][adfsproxy-farm-first-parameters]**, og ** [adfsproxy-farm-rest.parameters.json][adfsproxy-farm-rest-parameters]**. Scriptet bruger indstillingerne i disse filer til at oprette ADFS proxy serverfarmen. 

- ** [virtualNetworkGateway.parameters.json][virtualnetworkgateway-parameters]**. Denne fil indeholder de indstillinger, der bruges til at oprette gatewayen Azure VPN i skyen bruges til at oprette forbindelse til den lokale netværk. Du skal ændre den `sharedKey` værdi i den `connectionsSettings` afsnit for at matche, på den lokale VPN-enhed. Du kan finde flere oplysninger, se [implementere en Hybrid netværksarkitektur med Azure og lokale VPN][hybrid-azure-on-prem-vpn].

- ** [dmz-private.parameters.json] [ dmz-private-parameters] ** og ** [dmz-public.parameters.json ] [ dmz-public-parameters] **. Disse filer konfigurere indgående (offentlig) og udgående (privat) sider af FOS, der udgør DMZ, beskytte servere i skyen. Finde flere oplysninger om disse elementer og deres konfiguration, [implementere en DMZ mellem Azure og internettet][implementing-a-secure-hybrid-network-architecture-with-internet-access].

- ** [loadBalancer-web.parameters-json][loadBalancer-web-parameters]**, ** [loadBalancer-biz.parameters-json][loadBalancer-biz-parameters]**, og ** [loadBalancer-data.parameters-json][loadBalancer-data-parameters]**. Filerne parametre indeholder VM specifikationer for data, business og web access lag, og Konfigurer Indlæs balancere for hvert niveau. Dette er de VM'er, der hoster webapps og databaser og udføre business arbejdsmængder for organisationen. Du kan ændre størrelsen og antallet af FOS i hvert niveau efter dine behov.

- ** [virtualMachines mgmt.parameters.json][virtualMachines-mgmt-parameters]**. Denne fil indeholder konfigurationen til at gå feltet/administrere FOS. Det er kun muligt at få logon og administratoradgang til FOS i web, business og niveauer af data fra feltet hyperlink. Scriptet opretter en enkelt *Standard_DS1_v2* VM som standard, men du kan redigere denne fil for at oprette større eller eventuelle yderligere FOS, hvis management arbejdsbelastningen der sandsynligvis er betydeligt.

## <a name="solution-deployment"></a>Løsningsinstallation

Løsningen antages det, at følgende forudsætninger:

- Du har et eksisterende Azure-abonnement, kan du oprette ressourcegrupper.

- Du har downloadet og installeret den nyeste version af Azure Powershell. Se [her] [ azure-powershell-download] vejledning.

For at køre scriptet installerer, der løsningen:

1. Flytte til en praktisk mappe på din lokale computer, og Opret følgende undermapper:

    - Scripts

    - Scripts/parametre

    - Scripts/parametre/Premise

    - Scripts/parametre/Azure

2. Hente [Implementer ReferenceArchitecture.ps1] [ solution-script] filen til mappen Scripts

3. Hente indholdet af [parametre/Premise] [ on-premises-folder] mappe til mappen Scripts/parametre/Premise:

4. Hente indholdet af [parametre/azure] [ azure-folder] mappe til mappen Scripts/parametre/Azure.

5. Redigere filen Implementer ReferenceArchitecture.ps1 i mappen Scripts og ændre følgende linjer for at angive de ressource, der skal oprettes eller bruges til at indeholde de ressourcer, der er oprettet af scriptet:

    ```powershell
    # Azure Onpremise Deployments
        $onpremiseNetworkResourceGroupName = "ra-adfs-onpremise-rg"
        
        # Azure ADDS Deployments
        $azureNetworkResourceGroupName = "ra-adfs-network-rg"
        $workloadResourceGroupName = "ra-adfs-workload-rg"
        $securityResourceGroupName = "ra-adfs-security-rg"
        $addsResourceGroupName = "ra-adfs-adds-rg"
        $adfsResourceGroupName = "ra-adfs-adfs-rg"
        $adfsproxyResourceGroupName = "ra-adfs-proxy-rg"
    ```

6. Rediger parameter filerne i Scripts/parametre/Premise og Scripts/parametre/Azure mapper. Opdater ressource gruppen referencer i disse filer, der svarer til navnene på de tilknyttede variabler i filen Implementer ReferenceArchitecture.ps1 ressourcegrupper. De følgende tabel viser, hvilke parameter filer henviser til hvilke ressourcegruppe. Bemærk, at de *ra-adfs-arbejdsbelastning-indbyggede rg*, *ra-adfs-sikkerhed-indbyggede rg*, *ra-adfs-tilføjer-indbyggede rg*, *ra-adfs-adfs-indbyggede rg*og *ra-adfs-proxy-indbyggede rg* grupper bruges kun i PowerShell-script og ikke vises i parameter-filer.

  	|Ressourcegruppe|Parameter fil(er)|
  	|--------------|--------------|
  	|RA-adfs-Premise-indbyggede rg|parameters\onpremise\connection.Parameters.JSON<br /> parameters\onpremise\virtualMachines-Adds.Parameters.JSON<br />parameters\onpremise\virtualNetwork-Adds-DNS.Parameters.JSON<br />parameters\onpremise\virtualNetwork.Parameters.JSON<br />parameters\onpremise\virtualNetworkGateway.Parameters.JSON<br />parameters\azure\virtualNetworkGateway.Parameters.JSON
  	|RA-adfs-netværk-indbyggede rg|parameters\onpremise\connection.Parameters.JSON<br />parameters\azure\dmz-private.Parameters.JSON<br />parameters\azure\dmz-public.Parameters.JSON<br />parameters\azure\loadBalancer-ADFS.Parameters.JSON<br />parameters\azure\loadBalancer-adfsproxy.Parameters.JSON<br />parameters\azure\loadBalancer-Biz.Parameters.JSON<br />parameters\azure\loadBalancer-data.Parameters.JSON<br />parameters\azure\loadBalancer-Web.Parameters.JSON<br />parameters\azure\virtualMachines-Adds.Parameters.JSON<br />parameters\azure\virtualMachines-MGMT.Parameters.JSON<br />parameters\azure\virtualNetwork-with-OnPremise-and-Azure-DNS.Parameters.JSON<br />parameters\azure\virtualNetwork.Parameters.JSON<br />parameters\azure\virtualNetworkGateway.Parameters.JSON (*to forekomster*)

    Desuden konfiguration for lokale og i skyen komponenter, som beskrevet i afsnittet [løsning Components] [komponenter løsning].

7. Åbne en Azure PowerShell-vinduet, gå til mappen Scripts og køre følgende kommando:

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> <mode>
    ```

    Erstatte `<subscription id>` med dit Azure abonnement-ID.

    For `<location>`, Angiv en Azure region, såsom `eastus` eller `westus`.

    Den `<mode>` parameter kan have en af følgende værdier:

    - `Onpremise`, til at oprette simuleret lokalt miljø.

    - `Infrastructure`, til at oprette VNet infrastruktur og springe feltet i skyen.

    - `CreateVpn`, at opbygge Azure virtuelt netværksgateway og forbinde den til den lokale netværk.

    - `AzureADDS`, hvis du vil oprette de FOS, som tilføjer servere, installere Active Directory til disse FOS og oprette domænet i skyen.

    - `AdfsVm`Sådan oprettes ADFS FOS og deltage i dem til domænet i skyen.

    - `ProxyVm`at opbygge ADFS proxy FOS og deltage i dem til domænet i skyen.

    - `Prepare`, som udfører alle ovenstående opgaver. **Dette er den anbefalede indstilling, hvis du opretter en helt ny installation og du ikke har et eksisterende lokalt infrastruktur.**

    >[AZURE.NOTE] Du kan også køre scriptet med en `<mode>` -parameter for `Workload` til at oprette websteder, business, og data niveau FOS og netværk. Denne konfiguration er ikke inkluderet som en del af den `Prepare` tilstand.

    Hvis du bruger den `Prepare` indstillingen, scriptet tager flere timer at fuldføre, og er færdig med meddelelsen *forberedelse er fuldført. Installer certifikat til alle ADFS og proxy FOS.*

8.  Genstart feltet hyperlink (*ra-adfs-mgmt-vm1* i gruppen *ra-adfs-sikkerhed-indbyggede rg* ) for at tillade dens DNS-indstillingerne træder i kraft.

9.  [Hente et SSL-certifikat ADFS] [ adfs_certificates] og installere dette certifikat på ADFS FOS. Bemærk, at du kan oprette forbindelse til ADFS FOS gennem boksen gå. IP-adresser er *10.0.5.4* og *10.0.5.5*. Standard-brugernavn er *contoso\testuser* med adgangskode *AweSome@PW*.

    >[AZURE.NOTE] Kommentarer i scriptet Implementer ReferenceArchitecture.ps1 på dette tidspunkt give detaljerede anvisninger til at oprette et selvsigneret testcertifikat og nøglecenter ved hjælp af den `makecert` kommandoen. Brug dog ikke certifikater genereres af makecert i et produktionsmiljø.

10. Kør følgende Powershell-kommando for at konfigurere ADFS-serverfarm:

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Adfs
    ```

11. I boksen gå skal du gå til *https://adfs.contoso.com/adfs/ls/idpinitiatedsignon.htm* til at teste ADFS installationen (du kan modtage en certifikatadvarsel, som du kan ignorere denne test). Kontrollér, at siden Contoso Corporation logon vises. Log på som *contoso\testuser* med adgangskode *AweS0me@PW*.

12. Installere SSL-certifikatet på ADFS proxy FOS. IP-adresser er *10.0.6.4* og *10.0.6.5*.

13. Kør følgende Powershell-kommando for at konfigurere den første ADFS proxy-server:

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Proxy1
    ```

14. Følg vejledningen vises som scriptet til at teste installationen af den første ADFS proxy-server.

15. Kør følgende Powershell-kommando for at konfigurere den anden første ADFS proxyserver:

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Proxy2
    ```

16. Følg vejledningen vises som scriptet til at teste den komplette konfiguration af ADFS-proxy.

## <a name="next-steps"></a>Næste trin

- [Azure Active Directory][aad].

- [Azure Active Directory B2C][aadb2c].

<!-- links -->

[vm-recommendations]: ./guidance-compute-single-vm.md#Recommendations
[naming-conventions]: ./guidance-naming-conventions.md
[implementing-active-directory]: ./guidance-identity-adds-extend-domain.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ./guidance-iaas-ra-secure-vnet-dmz.md
[DRS]: https://technet.microsoft.com/library/dn280945.aspx
[where-to-place-an-fs-proxy]: https://technet.microsoft.com/library/dd807048.aspx
[ADDRS]: https://technet.microsoft.com/library/dn486831.aspx
[plan-your-adfs-deployment]: https://msdn.microsoft.com/library/azure/dn151324.aspx
[ad_network_recommendations]: #network_configuration_recommendations_for_AD_DS_VMs
[domain_and_forests]: https://technet.microsoft.com/library/cc759073(v=ws.10).aspx
[adfs_certificates]: https://technet.microsoft.com/library/dn781428(v=ws.11).aspx
[create_service_account_for_adfs_farm]: https://technet.microsoft.com/library/dd807078.aspx
[import_server_authentication_certificate]: https://technet.microsoft.com/library/dd807088.aspx
[adfs-configuration-database]: https://technet.microsoft.com/en-us/library/ee913581(v=ws.11).aspx
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[security-considerations]: #security-considerations
[recommendations]: #recommendations
[claims-aware applications]: https://msdn.microsoft.com/en-us/library/windows/desktop/bb736227(v=vs.85).aspx
[active-directory-federation-services-overview]: https://technet.microsoft.com/en-us/library/hh831502(v=ws.11).aspx
[establishing-federation-trust]: https://blogs.msdn.microsoft.com/alextch/2011/06/27/establishing-federation-trust/
[Deploying_a_federation_server_farm]: https://technet.microsoft.com/library/dn486775.aspx
[install_and_configure_the_web_application_proxy_server]: https://technet.microsoft.com/library/dn383662.aspx
[publish_applications_using_AD_FS_preauthentication]: https://technet.microsoft.com/library/dn383640.aspx
[managing-adfs-components]: https://technet.microsoft.com/library/cc759026.aspx
[oms-adfs-pack]: https://www.microsoft.com/download/details.aspx?id=41184
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[aad]: https://azure.microsoft.com/documentation/services/active-directory/
[aadb2c]: https://azure.microsoft.com/documentation/services/active-directory-b2c/
[adfs-intro]: ../active-directory/active-directory-aadconnect-azure-adfs.md
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/Deploy-ReferenceArchitecture.ps1
[on-premises-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adfs/parameters/onpremise
[on-premises-vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/virtualNetwork.parameters.json
[on-premises-virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/virtualMachines-adds.parameters.json
[on-premises-virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/virtualNetworkGateway.parameters.json
[on-premises-connection-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/connection.parameters.json
[azure-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adfs/parameters/azure
[vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualNetwork.parameters.json
[dmz-private-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/dmz-private.parameters.json
[dmz-public-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/dmz-public.parameters.json
[virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualNetworkGateway.parameters.json
[hybrid-azure-on-prem-vpn]: ./guidance-hybrid-network-vpn.md
[virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualMachines-adds.parameters.json
[extending-ad-to-azure]: ./guidance-identity-adds-extend-domain.md
[loadbalancer-adfs-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-adfs.parameters.json
[add-adds-domain-controller-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/add-adds-domain-controller.parameters.json
[gmsa-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/gmsa.parameters.json
[adfs-farm-first-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfs-farm-first.parameters.json
[adfs-farm-rest-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfs-farm-rest.parameters.json
[adfs-farm-domain-join-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfs-farm-domain-join.parameters.json
[loadBalancer-web-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-web.parameters.json
[loadBalancer-biz-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-biz.parameters.json
[loadBalancer-data-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-data.parameters.json
[virtualMachines-mgmt-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualMachines-mgmt.parameters.json
[loadBalancer-adfsproxy-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-adfsproxy.parameters.json
[adfsproxy-farm-first-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfsproxy-farm-first.parameters.json
[adfsproxy-farm-rest-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfsproxy-farm-rest.parameters.json
[0]: ./media/guidance-iaas-ra-secure-vnet-adfs/figure1.png "Secure hybrid netværksarkitektur med Active Directory"
