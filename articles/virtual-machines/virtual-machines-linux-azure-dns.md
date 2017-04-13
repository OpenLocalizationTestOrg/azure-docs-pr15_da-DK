<properties 
   pageTitle="Indstillinger for opløsning af DNS-navn for Linux FOS i Azure"
   description="Navn opløsning scenarier, hvor Linux FOS i Azure IaaS, herunder leveres DNS-tjenester, hybride ekstern DNS og tage dine egne DNS-server."
   services="virtual-machines"
   documentationCenter="na"
   authors="RicksterCDN"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/19/2016"
   ms.author="rclaus" />

# <a name="dns-name-resolution-options-for-linux-vms-in-azure"></a>Metoder til DNS-navneoversættelse for Linux FOS i Azure

Azure leverer DNS-navneoversættelse som standard for alle VM'er, der er indeholdt i et enkelt virtuelt netværk. Du kan implementere dine egne DNS-navn opløsning løsning ved at konfigurere dine egne DNS-tjenester på dit Azure hostet FOS. Følgende scenarier kan hjælpe dig med at vælge, hvilken fungerer bedre for den aktuelle situation.

- [Azure-producenter navneoversættelse](#azure-provided-name-resolution)

- [Navneoversættelse bruger dine egne DNS-server](#name-resolution-using-your-own-dns-server) 

Typen navneoversættelse, du bruger, afhænger af, hvordan dine FOS og rolle forekomster brug for at kommunikere med hinanden.

**Tabellen nedenfor illustrerer scenarier og tilsvarende navn opløsning løsninger:**

| **Scenarie** | **Løsning** | **Suffiks** |
|--------------|--------------|----------|
| Navneoversættelse mellem rolle forekomster eller VM'er, der er placeret i det samme virtuelle netværk | [Azure-producenter navneoversættelse](#azure-provided-name-resolution)| værtsnavn eller fulde Domænenavn |
| Navneoversættelse mellem rolle forekomster eller VM'er, der er placeret i forskellige virtuelle netværk | Kunde-administreret DNS-servere viderestilling af forespørgsler mellem vnets til løsning efter Azure (DNS-proxy).  Se [navneoversættelse bruger dine egne DNS-server](#name-resolution-using-your-own-dns-server)| FULDE kun |
| Opløsning af lokale computer og tjenesten navne fra rolle forekomster eller FOS i Azure | Kunde-administreret DNS-servere (for eksempel lokalt domænenavn fra domænecontrolleren, lokale skrivebeskyttet domænenavn fra domænecontrolleren eller en sekundær DNS synkroniseret ved hjælp af zoneoverførsler).  Se [navneoversættelse bruger dine egne DNS-server](#name-resolution-using-your-own-dns-server)|FULDE kun |
| Opløsning af Azure værtsnavne fra lokal computere | Videresende forespørgsler til en kunde-administreret DNS proxy-server i det tilsvarende vnet proxyserveren videresender forespørgsler til Azure for opløsning. Se [navneoversættelse bruger dine egne DNS-server](#name-resolution-using-your-own-dns-server)| FULDE kun |
| Modsat DNS-Posterne for interne IP'er | [Navneoversættelse bruger dine egne DNS-server](#name-resolution-using-your-own-dns-server) | i/t. |

## <a name="azure-provided-name-resolution"></a>Azure-producenter navneoversættelse

Azure leverer sammen med opløsning på offentlige DNS-navne, interne navneoversættelse for FOS og rolle forekomster, der er placeret i det samme virtuelle netværk.  DNS-suffikset er ensartet på tværs af det virtuelle netværk, (så det fulde Domænenavn ikke er nødvendig) i ARM-baseret virtuelle netværk og DNS-navne kan tildeles til både netværkskort og FOS. Selvom Azure-producenter navneoversættelse ikke kræver en hvilken som helst konfiguration, er det ikke det relevante valg til alle installationer, som set i ovenstående tabel.

### <a name="features-and-considerations"></a>Funktioner og overvejelser i forbindelse med

**Funktioner:**

- Brugervenlig: ingen konfiguration er påkrævet for at bruge Azure-producenter navneoversættelse.

- Tjenesten Azure-producenter navn opløsning er meget tilgængelige, så du skal oprette og administrere klyngerne med dine egne DNS-servere.

- Kan bruges sammen med dine egne DNS-servere for at løse både lokalt og Azure værtsnavne.

- Navneoversættelse er angivet mellem FOS i virtuelle netværk uden behov for det fulde Domænenavn. 

- Du kan bruge værtsnavne, der bedst beskriver din installationer i stedet for at arbejde med automatisk oprettede navne.

**Overvejelser i forbindelse med:**

- Azure-oprettet DNS-suffikset kan ikke ændres.

- Du kan ikke manuelt registrere dine egne poster.

- WINS og NetBIOS understøttes ikke.

- Værtsnavne skal være DNS-kompatible (de skal bruge kun 0-9, a-å og '-', og kan ikke starte eller slutte med et '-'. Se RFC 3696 sektion 2).

- DNS-forespørgsel trafikken er begrænset til hver VM. Dette ikke bør påvirke de fleste programmer.  Hvis anmodning (throttling) er opfyldt, kan du sikre dig, at klientsiden cachelagring er aktiveret.  Se [få mest muligt ud af Azure-producenter navneoversættelse](#Getting-the-most-from-Azure-provided-name-resolution)kan finde flere oplysninger.


### <a name="getting-the-most-from-azure-provided-name-resolution"></a>Få mest muligt ud af Azure-producenter navneoversættelse
**Klientsiden cachelagring:**

Ikke alle DNS-forespørgsel, der sendes via netværket.  Klientsiden cachelagring hjælper med at reducere ventetid og forbedre spændstighed til netværk blips ved at løse tilbagevendende DNS-forespørgsler fra en lokal cache.  DNS-poster indeholder en Time To Live (TTL) som gør det muligt cachen til at gemme posten, så længe som muligt uden at påvirke post friskhed.  Derfor er klientsiden cachelagring egnet til de fleste situationer.

Nogle Linux distros skal ikke medtage cachelagring som standard.  Det anbefales, at du føje et til hver Linux VM (efter at have kontrolleret, at der ikke er en lokal cache allerede).

Der er flere forskellige DNS-cachelagring pakker tilgængelige, for eksempel dnsmasq, nedenstående trin for at installere dnsmasq på de mest almindelige distros:

- **Ubuntu (bruger resolvconf)**:
    - installere pakken dnsmasq ("sudo Hovedgaden få Installer dnsmasq").
- **SUSE (bruger netconf)**:
    - installere pakken dnsmasq ("sudo zypper Installer dnsmasq") 
    - aktivere tjenesten dnsmasq ("systemctl Aktivér dnsmasq.service") 
    - starte tjenesten dnsmasq ("systemctl start dnsmasq.service") 
    - redigere "/ osv/sysconfig/netværk/config" og ændre NETCONFIG_DNS_FORWARDER = "" til "dnsmasq"
    - opdatere resolv.conf ("netconfig opdateringen") for at angive cachen som lokale DNS resolver
- **OpenLogic (bruger NetworkManager)**:
    - installere pakken dnsmasq ("sudo yum Installer dnsmasq")
    - aktivere tjenesten dnsmasq ("systemctl Aktivér dnsmasq.service")
    - starte tjenesten dnsmasq ("systemctl start dnsmasq.service")
    - tilføje "føjes domæne--navneservere 127.0.0.1;" til "/etc/dhclient-eth0.conf"
    - Genstart Netværkstjeneste ("service netværk genstart") for at angive cachen som lokale DNS resolver

> [AZURE.NOTE]: Pakken 'dnsmasq' er kun én af de mange DNS-cache tilgængelig for Linux.  Inden du bruger det, Markér dens er egnet til dine specifikke behov, og der er installeret nogen andre cache.

**Klientsiden forsøg:**

DNS er primært en UDP-protokol.  Som UDP-protokollen ikke garantere levering af meddelelser, skal håndteres forsøg i selve DNS-protokollen.  Hver DNS-klient (operativsystem) kan udvise forskellige forsøg afhængigt af indstillingerne for udviklere:

 - Windows operativsystemer prøv igen efter et andet og derefter igen efter en anden to, fire og en anden fire sekunder. 
 - Standard Linux konfiguration gentagelserne efter fem sekunder.  Du skal ændre denne indstilling for at prøve igen fem gange med et andet intervaller.  

Kontrollere de aktuelle indstillinger på en Linux VM 'kat /etc/resolv.conf' og kig på linjen 'indstillinger', for eksempel:

    options timeout:1 attempts:5

Filen resolv.conf genereres automatisk og ikke skal redigeres.  Bestemte fremgangsmåden for at tilføje linjen 'indstillinger' varierer alt efter distro:

- **Ubuntu** (bruger resolvconf):
    - tilføje linjen indstillinger til ' / etc/resolveconf/resolv.conf.d/head' 
    - køre resolvconf -u opdatere
- **SUSE** (bruger netconf):
    - føje 'timeout:1 forsøg: 5' til NETCONFIG_DNS_RESOLVER_OPTIONS = "" parameter i '/ osv/sysconfig/netværk/config' 
    - Kør 'netconfig update' opdatere
- **OpenLogic** (bruger NetworkManager):
    - føje 'ekkoet "indstillinger timeout:1 forsøg: 5" ' til ' / etc/NetworkManager/dispatcher.d/11-dhclient' 
    - køre 'service netværk genstart' opdatere

## <a name="name-resolution-using-your-own-dns-server"></a>Navneoversættelse bruger dine egne DNS-server
Der er mange situationer, hvor dine navn opløsning behov kom ud over funktionerne, forudsat ved Azure, f.eks når du har brug for DNS-opløsning mellem virtuelle netværk (vnets).  Azure leverer dækker dette scenario, muligheden for, at du kan bruge dine egne DNS-servere.  

DNS-servere i et virtuelt netværk kan videresende DNS-forespørgsler til Azures rekursiv oversættelsesfunktionerne til at løse værtsnavne inden for den virtuelle netværk.  For eksempel kan en DNS-server, der kører i Azure svare på DNS-forespørgsler for sine egne DNS-zone filer og videresende alle andre forespørgsler til Azure.  Dette giver mulighed for FOS at få vist begge dine poster i dine zone filer og Azure-producenter værtsnavne (via domænes).  Adgang til Azures rekursiv oversættelsesfunktionerne leveres via den virtuelle IP 168.63.129.16.

Viderestilling af DNS-også aktiverer blandt vnet DNS-opløsning og gør det muligt for dine lokalt maskiner til at løse Azure-producenter værtsnavne.  Du kan løse en VM hostname, DNS-server VM skal være placeret i det samme virtuelle netværk og konfigureres til at videresende hostname forespørgsler til Azure.  Som DNS-suffiks er forskellige i hver vnet, kan du bruge regler for betinget videresendelse til at sende DNS-forespørgsler til den korrekte vnet for opløsning.  Følgende billede viser to vnets og et lokalt netværk med at gøre blandt vnet DNS-opløsning ved hjælp af denne metode:

![Blandt vnet DNS](./media/virtual-machines-linux-azure-dns/inter-vnet-dns.png)

Når du bruger Azure-producenter navneoversættelse, leveres det interne DNS-suffiks til hver VM ved hjælp af DHCP.  Når du bruger dit eget navn opløsning løsning, angivet denne suffiks ikke til FOS, fordi den forstyrrer andre DNS-arkitekturer.  Henvise til maskiner af fulde, eller til at konfigurere suffikset på din FOS, kan suffikset bestemmes ved hjælp af PowerShell eller API:

-  For Azure ressourcestyring administrerede vnets, suffikset er tilgængelige via [netværkskort](https://msdn.microsoft.com/library/azure/mt163668.aspx) ressourcen, eller du kan køre kommandoen `azure network public-ip show <resource group> <pip name>` at få vist oplysninger om din offentlige IP-adresse, herunder det fuldstændige Domænenavn for nic.    


Hvis viderestilling af forespørgsler til Azure ikke efter dine behov, skal du angive dine egne DNS-løsning.  Din DNS-løsningen skal:

-  Give relevante hostname opløsning, for eksempel via [DDNS](../virtual-network/virtual-networks-name-resolution-ddns.md).  Bemærk, at hvis du bruger DDNS, du vil deaktivere DNS-post oprydning som Azures DHCP-rettigheder er meget lang tid og offlinerensning kan fjernes DNS-poster for tidligt. 
-  Give relevante rekursiv opløsning til Tillad opløsningen på eksterne domænenavne.
-  Være tilgængelige (TCP- og UDP på port 53) fra de klienter, det fungerer, og ikke have adgang til internettet.
-  Sikres mod adgang fra internettet, for at reducere risici, som eksterne supportmedarbejdere.

> [AZURE.NOTE] Bedste ydeevne, når du bruger Azure FOS som DNS-servere, IPv6 skal være deaktiveret og skal tildeles en [Forekomst niveau offentlige IP-adresse](../virtual-network/virtual-networks-instance-level-public-ip.md) til hver DNS-server VM.  

