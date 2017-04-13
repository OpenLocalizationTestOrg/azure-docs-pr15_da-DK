<properties 
    pageTitle="Sådan konfigureres virtuelt netværk understøttelse af en Premium Azure Redis Cache | Microsoft Azure" 
    description="Lær at oprette og administrere virtuelt netværk understøttelse af din Premium niveau Azure Redis Cache forekomster" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016" 
    ms.author="sdanie"/>

# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-redis-cache"></a>Sådan konfigureres virtuelle netværkssupport for en Premium Azure Redis Cache
Azure Redis Cache har forskellige cache tilbud som understøtter fleksibilitet valg af cachestørrelse og funktioner, herunder den nye Premium niveau.

Azure Redis Cache premium niveau funktioner omfatter klynge brugerdata og virtuelle netværkssupport (VNet). En VNet er et privat netværk i skyen. Når en Azure Redis Cache forekomst er konfigureret med en VNet, er ikke offentligt tilgængelige og kan kun åbnes fra virtuelle computere og programmer i VNet. I denne artikel beskrives, hvordan du kan konfigurere understøttelse af virtuelt netværk for en forekomst af premium Azure Redis Cache.

>[AZURE.NOTE] Azure Redis Cache understøtter begge klassisk og ARM VNets.

Finde oplysninger om andre premium-funktioner i cachen, kan du se [Introduktion til Azure Redis Cache Premium niveau](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>Hvorfor VNet?
[Azure virtuelt netværk (VNet)](https://azure.microsoft.com/services/virtual-network/) installation indeholder øget sikkerhed og isolationsniveauet for din Azure Redis Cache, samt undernet, politik for adgangskontrol og andre funktioner til at begrænse adgangen til Azure Redis Cache.

## <a name="virtual-network-support"></a>Understøttelse af virtuelle netværk
Virtuel understøttelse af netværk (VNet) er konfigureret på bladet **Ny Redis Cache** under oprettelse af cache. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Når du har valgt en premium priser niveau, kan du konfigurere Azure Redis Cache VNet integration ved at vælge en VNet, der er i samme abonnement og placering, som din cache. Hvis du vil bruge en ny VNet, opretter den først ved at følge trinnene i [oprette et virtuelt netværk ved hjælp af portalen Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) eller [oprette et virtuelt netværk (klassisk) ved hjælp af portalen Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md) , og vend derefter tilbage til bladet **Ny Redis Cache** til at oprette og konfigurere din premium cache.

Klik på **Virtuelt netværk** på bladet **Ny Redis Cache** for at konfigurere VNet til din nye cache, og vælg den ønskede VNet på rullelisten.

![Virtuelt netværk][redis-cache-vnet]

Vælg det ønskede undernet fra rullelisten **undernet** , og Angiv den ønskede **statiske IP-adresse**. Hvis du bruger en klassisk VNet **statiske IP-adresse** felt er valgfrit, og hvis der ikke er angivet en vælges fra det markerede undernet.

>[AZURE.IMPORTANT] Når du installerer en Azure Redis Cache på en ARM VNet, være cachen i et dedikeret undernet, der indeholder ingen andre ressourcer med undtagelse af Azure Redis Cache forekomster. Hvis en forsøgt at installere en Azure Redis Cache på en ARM VNet til et undernet, der indeholder andre ressourcer, installationen mislykkes.

![Virtuelt netværk][redis-cache-vnet-ip]

>[AZURE.IMPORTANT] De første fire adresser i et undernet er reserverede og kan ikke bruges. Du kan finde flere oplysninger, se [er der nogen begrænsninger på ved hjælp af IP-adresser i disse undernet?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Når cachen er oprettet, kan du få vist konfigurationen af VNet ved at klikke på **Virtuelt netværk** fra bladet **Indstillinger** .

![Virtuelt netværk][redis-cache-vnet-info]


Angiv værtsnavnet på din cache i forbindelsesstrengen som vist i følgende eksempel for at oprette forbindelse til din Azure Redis cache-forekomst, når du bruger en VNet.

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

## <a name="azure-redis-cache-vnet-faq"></a>Azure Redis Cache VNet ofte stillede spørgsmål

Følgende liste indeholder svar på ofte stillede spørgsmål om Azure Redis Cache skalering.

-   [Hvad er nogle almindelige forkert konfiguration problemer med Azure Redis Cache og VNets?](#what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets)
-   [Kan jeg bruge VNets med en standard- eller grundlæggende cache?](#can-i-use-vnets-with-a-standard-or-basic-cache)
-   [Hvorfor opretter en Redis cache mislykkes i nogle undernet, men ikke andre?](#why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others)
-   [Fungerer alle cache-funktioner, når vært for en cache i en VNET?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)


## <a name="what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets"></a>Hvad er nogle almindelige forkert konfiguration problemer med Azure Redis Cache og VNets?

Når Azure Redis Cache er placeret i en VNet, anvendes portene i den følgende tabel. Hvis disse porte blokeres, fungerer cachen muligvis ikke korrekt. Har du en eller flere af disse porte, der er blokeret er den mest almindelige forkert konfiguration problem, når du bruger Azure Redis cachen i en VNet.

| Porte     | Retning        | Transport Protocol | Formål                                                                           | Fjern-IP-                           |
|-------------|------------------|--------------------|-----------------------------------------------------------------------------------|-------------------------------------|
| 80, 443     | Udgående         | TCP                | Redis afhængigheder på Azure lagerplads/PKI (Internet)                                | *                                   |
| 53          | Udgående         | TCP/UDP            | Redis afhængigheder af DNS (Internet/VNet)                                         | *                                   |
| 6379, 6380  | Indgående          | TCP                | Klientkommunikation til Redis, Azure belastning                               | VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 8443        | Indgående/udgående | TCP                | Implementering detaljer for Redis                                                   | VIRTUAL_NETWORK                     |
| 8500        | Indgående          | TCP/UDP            | Azure justering af belastning                                                              | AZURE_LOADBALANCER                  |
| 10221 10231 | Indgående/udgående | TCP                | Implementering detaljer for Redis (kan begrænse remote slutpunkt til VIRTUAL_NETWORK) | VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 13000 13999 | Indgående          | TCP                | Klientkommunikation til Redis klynger Azure belastning                      | VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 15000 15999 | Indgående          | TCP                | Klientkommunikation til Redis klynger Azure belastning                      | VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 16001       | Indgående          | TCP/UDP            | Azure justering af belastning                                                              | AZURE_LOADBALANCER                  |
| 20226       | Indgående + udgående | TCP                | Implementering detaljer for Redis klynger                                          | VIRTUAL_NETWORK                     |


Der er krav til netværksforbindelser for Azure Redis Cache, der ikke kan indledningsvis opfyldes i et virtuelt netværk. Azure Redis Cache kræver alle følgende elementer for at kunne fungere korrekt, når den bruges i et virtuelt netværk.

-  Udgående netværksforbindelse til Azure-lager slutpunkter i hele verden. Dette omfatter slutpunkter, der er placeret i det samme område som forekomst Azure Redis Cache samt lagerplads slutpunkter, der er placeret i **andre** Azure områder. Azure lagerplads slutpunkter løse under følgende DNS-domæner: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net*og *file.core.windows.net*. 
-  Udgående netværksforbindelse til *ocsp.msocsp.com*, *mscrl.microsoft.com* og *crl.microsoft.com*. Denne forbindelse skal bruges til at understøtte SSL funktionalitet.
-  Konfigurationen af DNS for det virtuelle netværk skal kunne løse alle slutpunkter og domæner, der er nævnt i de tidligere punkter. Disse DNS-krav kan opfyldes ved at sikre en gyldig DNS-infrastrukturen er konfigureret og vedligeholdes for det virtuelle netværk.



### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Kan jeg bruge VNets med en standard- eller grundlæggende cache?

VNets kan kun bruges med premium cache.

### <a name="why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others"></a>Hvorfor opretter en Redis cache mislykkes i nogle undernet, men ikke andre?

Hvis du anvender en Azure Redis Cache til en ARM VNet, være cachen i et dedikeret undernet, der indeholder ingen andre ressourcetype. Hvis en forsøgt at installere en Azure Redis Cache til en ARM VNet undernet, der indeholder andre ressourcer, installationen mislykkes. Før du kan oprette en ny Redis cache, skal du slette de eksisterende ressourcer i undernettet.

Du kan installere flere typer ressourcer til en klassisk VNet, så længe du har tilstrækkelig IP-adresser, der er tilgængelige.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Fungerer alle cache-funktioner, når vært for en cache i en VNET?

Når din cache er en del af en VNET, kan kun klienter i VNET kan få adgang til cachen. Derfor fungerer funktioner for administration af følgende cachen ikke på nuværende tidspunkt.

-   Redis Console - da Redis Console bruger redis cli.exe klienten hostes på VM'er, der ikke er en del af din VNET, der kan ikke oprettes forbindelse til din cache.


## <a name="use-expressroute-with-azure-redis-cache"></a>Brug ExpressRoute med Azure Redis Cache

Kunderne kan slutte et [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) kredsløb til deres virtuelle netværksinfrastruktur, og som derfor forlænge deres lokale netværk til Azure. 

Som standard anmelder et nyoprettet ExpressRoute kredsløb en standardrute, der tillader udgående forbindelse til internettet. Med denne konfiguration er klientprogrammer kunne oprette forbindelse til andre Azure slutpunkter, herunder Azure Redis Cache.

En almindelig kunde konfiguration er dog til at definere deres egne standardrute (0.0.0.0/0) som Fremtvinger udgående Internettrafik til i stedet dataflow i det lokale miljø. Denne trafikken sideskift altid forbindelse med Azure Redis cachen, fordi den udgående trafik er enten blokerede lokalt, eller NAT ville gøre til en ukendt sæt adresser, der ikke længere arbejder med forskellige Azure slutpunkter.

Løsningen er at definere en (eller flere) brugerdefinerede omdirigerer (UDRs) på det undernet, der indeholder den Azure Redis Cache. En UDR definerer undernet-specifikke omdirigerer, der er accepteret i stedet for standardruten.

Hvis det er muligt, anbefales det at bruge følgende konfiguration:

- ExpressRoute konfigurationen anmelder 0.0.0.0/0, og som standard kraft tunnelfører alle udgående trafik i det lokale miljø.
- UDR anvendt til det undernet, der indeholder den Azure Redis Cache definerer 0.0.0.0/0 med en næste hop type Internet (et eksempel på dette er længere ned i denne artikel).

Kombinerede effekten af at disse trin er, at niveauet undernet UDR tilsidesætter den ExpressRoute tvunget tunnelføring således sikre udgående forbindelse til internettet fra Azure Redis cachen.

Selvom forbindelse til en Azure Redis Cache forekomst fra et lokalt program ved hjælp af ExpressRoute er ikke et typiske scenarie på grund af ydeevnen årsager (for bedste ydeevne Azure Redis Cache klienter skal være i samme område som Azure Redis cachen), i dette scenarie udgående netværksstien ikke kan sendes via interne virksomhedens proxyer og kan det være kraft tunnelført til en lokal installation. Derved ændres effektive NAT-adressen for udgående netværkstrafik fra Azure Redis cachen. Ændring af NAT-adressen for en Azure Redis Cache forårsage forekomsts udgående netværkstrafik connectivity mislykkede forsøg på at mange af de slutpunkter, der er nævnt ovenfor. Dette resulterer i mislykkede forsøg på oprettelse af Azure Redis Cache.

**Vigtigt:**  Omdirigerer defineret i en UDR **skal** være specifik nok til at tilsidesætter alle omdirigerer meddeles af ExpressRoute konfigurationen. I følgende eksempel bruger adresseområde generelle 0.0.0.0/0 og således kan potentielt ved et uheld tilsidesættes ved at distribuere reklamer ved hjælp af mere specifikke adresseområder.

**Meget vigtigt:**  Azure Redis cachen er ikke understøttet i ExpressRoute konfigurationer, der **fejlagtigt kryds-annoncere omdirigerer fra offentlige peering stien til den private peering sti**. ExpressRoute konfigurationer, der har konfigureret, offentlige peering modtager distribuere reklamer fra Microsoft for en stor mængde Microsoft Azure IP-adresseområder. Hvis disse adresseområder er forkert kryds-vises på private peering stien, er betyder, at alle udgående netværkspakker fra forekomsten Azure Redis Cache undernet er forkert kraft-tunnelført til en kundes lokale netværksinfrastruktur. Dette netværk flow sideskift Azure Redis Cache. Løsning på dette problem er at stoppe tværs reklame omdirigerer fra offentlige peering stien til den private peering sti.

Baggrundsoplysninger om brugerdefinerede omdirigerer findes i denne [Oversigt](../virtual-network/virtual-networks-udr-overview.md). 

Du kan finde flere oplysninger om ExpressRoute, se [ExpressRoute teknisk oversigt](../expressroute/expressroute-introduction.md)

## <a name="next-steps"></a>Næste trin
Lær at bruge flere premium-funktioner i cachen.

-   [Introduktion til Azure Redis Cache Premium niveau](cache-premium-tier-intro.md)





  
<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

