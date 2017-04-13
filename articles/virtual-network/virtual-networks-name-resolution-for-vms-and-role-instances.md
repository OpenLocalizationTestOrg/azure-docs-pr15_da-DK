<properties 
   pageTitle="Opløsning for FOS og rolle forekomster"
   description="Navngive opløsning scenarier til Azure IaaS, hybride løsninger, mellem forskellige skytjenester, Active Directory og bruger dine egne DNS-server "
   services="virtual-network"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="telmos" />

# <a name="name-resolution-for-vms-and-role-instances"></a>Navneoversættelse for FOS og rolle forekomster

Afhængigt af hvordan du bruger Azure til at hoste IaaS, PaaS og hybrid løsninger, skal du tillade FOS og rolle forekomster, som du opretter for at kommunikere med hinanden. Selvom denne meddelelse kan gøres ved hjælp af IP-adresser, er det meget nemmere at bruge navne, der kan blive nemt husket og ikke ændres. 

Når rolle forekomster og FOS vært Azure har du brug at løse domænenavne til interne IP-adresser, kan de kan bruge en af to metoder:

- [Azure-producenter navneoversættelse](#azure-provided-name-resolution)

- [Navneoversættelse bruger dine egne DNS-server](#name-resolution-using-your-own-dns-server) (som kan videresende forespørgsler til Azure-producenter DNS-serverne) 

Typen navneoversættelse, du bruger, afhænger af, hvordan dine FOS og rolle forekomster brug for at kommunikere med hinanden.

**Tabellen nedenfor illustrerer scenarier og tilsvarende navn opløsning løsninger:**

| **Scenarie** | **Løsning** | **Suffiks** |
|--------------|--------------|----------|
| Navneoversættelse mellem rolle forekomster eller FOS findes i den samme skytjeneste eller virtuelt netværk | [Azure-producenter navneoversættelse](#azure-provided-name-resolution)| værtsnavn eller fulde Domænenavn |
| Navneoversættelse mellem rolle forekomster eller VM'er, der er placeret i forskellige virtuelle netværk | Kunde-administreret DNS-servere viderestilling af forespørgsler mellem vnets til løsning efter Azure (DNS-proxy).  Se [navneoversættelse bruger dine egne DNS-server](#name-resolution-using-your-own-dns-server)| FULDE kun |
| Opløsning af lokale computer og tjenesten navne fra rolle forekomster eller FOS i Azure | Kunde-administreret DNS-servere (fx lokalt domænenavn fra domænecontrolleren, lokale skrivebeskyttet domænenavn fra domænecontrolleren eller en sekundær DNS synkroniseret ved hjælp af zoneoverførsler).  Se [navneoversættelse bruger dine egne DNS-server](#name-resolution-using-your-own-dns-server)|FULDE kun |
| Opløsning af Azure værtsnavne fra lokal computere | Videresende forespørgsler til en kunde-administreret DNS proxy-server i det tilsvarende vnet proxyserveren videresender forespørgsler til Azure for opløsning. Se [navneoversættelse bruger dine egne DNS-server](#name-resolution-using-your-own-dns-server)| FULDE kun |
| Modsat DNS-Posterne for interne IP'er | [Navneoversættelse bruger dine egne DNS-server](#name-resolution-using-your-own-dns-server) | i/t. |
| Navneoversættelse mellem FOS eller rolle forekomster, der er placeret i forskellige skytjenester, ikke i et virtuelt netværk| Ikke relevant. Forbindelsen mellem FOS og rolle forekomster i forskellige skytjenester understøttes ikke uden for et virtuelt netværk.| i/t. |



## <a name="azure-provided-name-resolution"></a>Azure-producenter navneoversættelse

Azure leverer sammen med opløsning på offentlige DNS-navne, interne navneoversættelse for FOS og rolle forekomster, der er placeret i den samme virtuelt netværk eller en skybaseret tjeneste.  FOS/forekomster i en skybaseret tjeneste dele det samme DNS-suffiks (så værtsnavnet alene er tilstrækkelige), men i klassisk virtuelle netværk forskellige cloud services har forskellige DNS-suffikser, så det fulde Domænenavn er nødvendig for at finde navne mellem forskellige skytjenester.  DNS-suffikset er ensartet på tværs af det virtuelle netværk, (så det fulde Domænenavn ikke er nødvendig) i virtuelle netværk i implementeringsmodel ressourcestyring og DNS-navne kan tildeles til både netværkskort og FOS. Selvom Azure-producenter navneoversættelse ikke kræver en hvilken som helst konfiguration, er det ikke det relevante valg til alle installationer, som set fra tabellen ovenfor.

> [AZURE.NOTE] I forbindelse med internettet og arbejder roller, kan du også få adgang til rolle forekomster baseret på den rolle navn og forekomst tal ved hjælp af Azure Service Management REST-API interne IP-adresser. Du kan finde yderligere oplysninger finder [Service Management RESTEN API Reference](https://msdn.microsoft.com/library/azure/ee460799.aspx).

### <a name="features-and-considerations"></a>Funktioner og overvejelser i forbindelse med

**Funktioner:**

- Brugervenlig: ingen konfiguration er påkrævet for at kunne bruge Azure-producenter navneoversættelse.

- Tjenesten Azure-producenter navn opløsning er meget tilgængelige, så du skal oprette og administrere klyngerne med dine egne DNS-servere.

- Kan bruges sammen med dine egne DNS-servere for at løse både lokalt og Azure værtsnavne.

- Navneoversættelse er angivet mellem rolle forekomster/FOS inden for samme skytjenesten uden behov for en fulde Domænenavn.

- Navneoversættelse er angivet mellem FOS i virtuelle netværk, der bruger implementeringsmodel ressourcestyring uden behov for det fulde Domænenavn. Virtuelle netværk i den klassiske implementeringsmodel kræver det fulde Domænenavn, når navnekonflikter i forskellige skytjenester. 

- Du kan bruge værtsnavne, der bedst beskriver din installationer i stedet for at arbejde med automatisk oprettede navne.

**Overvejelser i forbindelse med:**

- Azure-oprettet DNS-suffikset kan ikke ændres.

- Du kan ikke manuelt registrere dine egne poster.

- WINS og NetBIOS understøttes ikke. (Du kan ikke se dine FOS i Windows Stifinder).

- Værtsnavne skal være DNS-kompatible (de skal bruge kun 0-9, a-å og '-', og kan ikke starte eller slutte med et '-'. Se RFC 3696 sektion 2).

- DNS-forespørgsel trafikken er begrænset til hver VM. Dette ikke bør påvirke de fleste programmer.  Hvis anmodning (throttling) er opfyldt, kan du sikre dig, at klientsiden cachelagring er aktiveret.  Yderligere oplysninger finder du [få mest muligt ud af Azure-producenter navneoversættelse](#Getting-the-most-from-Azure-provided-name-resolution).

- Kun FOS i de første 180 skytjenester registreres for hvert virtuelle netværk i en klassisk implementeringsmodel. Dette gælder ikke for virtuelle netværk i ressourcestyring installation modeller.


### <a name="getting-the-most-from-azure-provided-name-resolution"></a>Få mest muligt ud af Azure-producenter navneoversættelse
**Klientsiden cachelagring:**

Ikke alle DNS-forespørgsel skal sendes på tværs af netværket.  Klientsiden cachelagring hjælper med at reducere ventetid og forbedre spændstighed til netværk blips ved at løse tilbagevendende DNS-forespørgsler fra en lokal cache.  DNS-poster indeholder en Time To Live (TTL) som giver mulighed for at gemme posten, så længe som muligt uden at påvirke post friskhed, så klientsiden cachelagring egner sig til de fleste situationer cachen.

Standard Windows DNS-klient har en indbygget DNS-cachen.  Nogle Linux distros ikke skal medtage cachelagring som standard, anbefales det, at en føjes til hver Linux VM (efter at have kontrolleret, at der ikke er en lokal cache allerede).

Der findes en lang række forskellige DNS-cachelagring pakker tilgængelige, f.eks. dnsmasq, nedenstående trin for at installere dnsmasq på de mest almindelige distros:

- **Ubuntu (bruger resolvconf)**:
    - bare installere pakken dnsmasq ("sudo Hovedgaden få Installer dnsmasq").
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

> [AZURE.NOTE] Pakken 'dnsmasq' er kun én af de mange DNS-cache tilgængelig for Linux.  Før du bruger det, skal du se dens er egnet til dine specifikke behov, og der er installeret nogen andre cache.

**Klientsiden forsøg:**

DNS er primært en UDP-protokol.  Som UDP-protokollen ikke garantere levering af meddelelser, skal håndteres forsøg i selve DNS-protokollen.  Hver DNS-klient (operativsystem) kan udvise forskellige forsøg afhængigt af indstillingerne for udviklere:

 - Windows operativsystemer prøv igen efter 1 andet og derefter igen efter en anden 2, 4 og en anden 4 sekunder. 
 - Standard Linux konfiguration gentagelserne efter 5 sekunder.  Det anbefales at ændre denne indstilling for at prøve 5 gange med 1 anden intervaller.  

Kontrollere de aktuelle indstillinger på en Linux VM 'kat /etc/resolv.conf' og kig på linjen 'indstillinger', f.eks.:

    options timeout:1 attempts:5

Filen resolv.conf er som regel automatisk oprettede og ikke skal redigeres.  Bestemte fremgangsmåden for at tilføje linjen 'indstillinger' varierer alt efter distro:

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
Der findes en lang række situationer, hvor dine navn opløsning behov kan gå ud over de funktioner, der leveres af Azure, f.eks når ved hjælp af Active Directory-domæner, eller når du har brug for DNS-opløsning mellem virtuelle netværk (vnets).  Azure leverer dækker disse scenarier, muligheden for, at du kan bruge dine egne DNS-servere.  

DNS-servere i et virtuelt netværk kan videresende DNS-forespørgsler til Azures rekursiv oversættelsesfunktionerne til at løse værtsnavne inden for den virtuelle netværk.  For eksempel kan en domæne Controller (DC) kører i Azure svare på DNS-forespørgsler til dens domæner og videresende alle andre forespørgsler til Azure.  Dette giver mulighed for FOS at se både lokalt ressourcer (via DC) og Azure-producenter værtsnavne (via domænes).  Adgang til Azures rekursiv oversættelsesfunktionerne leveres via den virtuelle IP 168.63.129.16.

Viderestilling af DNS-også aktiverer blandt vnet DNS-opløsning og gør det muligt for dine lokalt maskiner til at løse Azure-producenter værtsnavne.  For at løse en VM hostname, DNS-server VM skal være placeret i det samme virtuelle netværk og konfigureres til at videresende hostname forespørgsler til Azure.  Som DNS-suffiks er forskellige i hver vnet, kan du bruge regler for betinget videresendelse til at sende DNS-forespørgsler til den korrekte vnet for opløsning.  Følgende billede viser to vnets og et lokalt netværk med at gøre blandt vnet DNS-opløsning ved hjælp af denne metode.  Et eksempel på DNS-domænes er tilgængelig i [skabelongalleriet Azure Hurtig start](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) og [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

![Blandt vnet DNS](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Når du bruger Azure-producenter navneoversættelse, et interne DNS-suffiks (*. internal.cloudapp.net) er angivet til hver VM ved hjælp af DHCP.  Dette gør det hostname opløsning, som posterne, hostname er i zonen internal.cloudapp.net.  Når du bruger dit eget navn opløsning løsning, leveret IDN'ER suffikset ikke til FOS, fordi den forstyrrer andre DNS-arkitekturer (som medlem af et domæne scenarier).  I stedet vi giver dig en ikke-fungerende pladsholder (reddog.microsoft.com).  

Hvis det er nødvendigt, kan det interne DNS-suffiks bestemmes ved hjælp af PowerShell eller API:

-  For virtuelle netværk i ressourcestyring installation modeller er suffikset tilgængelige via [netværkskort](https://msdn.microsoft.com/library/azure/mt163668.aspx) ressourcen eller via cmdlet'en [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) til.    
-  I klassisk installation modeller suffikset er tilgængelig via [Få installation API](https://msdn.microsoft.com/library/azure/ee460804.aspx) opkaldet eller via den [Get-AzureVM-fejlfinding af](https://msdn.microsoft.com/library/azure/dn495236.aspx) cmdlet.


Hvis viderestilling af forespørgsler til Azure ikke efter dine behov, skal du angive dine egne DNS-løsning.  Din DNS-løsning skal:

-  Give relevante hostname opløsning, f.eks. via [DDNS](virtual-networks-name-resolution-ddns.md).  Bemærk, at hvis du bruger DDNS, du vil deaktivere DNS-post oprydning som Azures DHCP-rettigheder er meget lang tid og offlinerensning kan fjernes DNS-poster for tidligt. 
-  Give relevante rekursiv opløsning til Tillad opløsningen på eksterne domænenavne.
-  Være tilgængelige (TCP- og UDP på port 53) fra de klienter, det fungerer, og ikke have adgang til internettet.
-  Sikres mod adgang fra internettet, for at reducere risici, som eksterne supportmedarbejdere.

> [AZURE.NOTE] Bedste ydeevne, når du bruger Azure FOS som DNS-servere, IPv6 skal være deaktiveret og skal tildeles en [Forekomst niveau offentlige IP-adresse](virtual-networks-instance-level-public-ip.md) til hver DNS-server VM.  Hvis du vælger at bruge Windows Server som din DNS-server, indeholder [denne artikel](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx) flere ydeevne analyse og optimeringer.


### <a name="specifying-dns-servers"></a>Angive DNS-servere

Når du bruger dine egne DNS-servere, giver Azure mulighed for at angive flere DNS-serverne virtuelt netværk eller per netværkskort (ressourcestyring) / service (klassisk) i skyen.  DNS-servere, der er angivet for en skybaseret tjeneste/netværk grænseflade få rangorden for over dem, der er angivet for det virtuelle netværk.

> [AZURE.NOTE] Netværk forbindelsesegenskaber, som DNS-server IP-adresser, ikke skal redigeres direkte i Windows FOS, som de kan få slettet under service reparere når den virtuelle netværksadapter erstattes. 


Når du bruger implementeringsmodel Ressourcestyring, kan DNS-servere angives i portalen, API/skabeloner ([vnet](https://msdn.microsoft.com/library/azure/mt163661.aspx), [nic](https://msdn.microsoft.com/library/azure/mt163668.aspx)) eller PowerShell ([vnet](https://msdn.microsoft.com/library/mt603657.aspx), [nic](https://msdn.microsoft.com/library/mt619370.aspx)).

Når du bruger den klassiske implementeringsmodel, kan DNS-servere for det virtuelle netværk angives i portalen eller [ *Netværkskonfiguration* filen](https://msdn.microsoft.com/library/azure/jj157100).  DNS-servere er angivet til skytjenester, via [ *Tjenestekonfiguration* filen](https://msdn.microsoft.com/library/azure/ee758710) eller i PowerShell ([Ny AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx)).

> [AZURE.NOTE] Hvis du ændrer DNS-indstillingerne for et virtuelt netværk/virtuelle computer, der allerede er installeret, skal du genstarte hver påvirkede VM for at ændringerne kan træde i kraft.


## <a name="next-steps"></a>Næste trin

Ressourcestyring implementeringsmodel:

- [Oprette eller opdatere et virtuelt netværk](https://msdn.microsoft.com/library/azure/mt163661.aspx)
- [Oprette eller opdatere et netværkskort](https://msdn.microsoft.com/library/azure/mt163668.aspx)
- [Ny AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx)
- [Ny AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx)

 
Klassisk implementeringsmodel:

- [Azure Service konfiguration skema](https://msdn.microsoft.com/library/azure/ee758710)
- [Virtuelt netværk konfiguration skema](https://msdn.microsoft.com/library/azure/jj157100)
- [Konfigurere et virtuelt netværk ved hjælp af et netværk konfigurationsfil](virtual-networks-using-network-configuration-file.md) 

