<properties
   pageTitle="Oprette en DNS-zone, ved hjælp af CLI | Microsoft Azure"
   description="Lær at oprette DNS-zoner til Azure DNS trinvis vejledning til at starte vært for din DNS-domæne, ved hjælp af CLI"
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>

# <a name="create-an-azure-dns-zone-using-cli"></a>Oprette en Azure DNS-zone, ved hjælp af CLI


> [AZURE.SELECTOR]
- [Azure-portalen](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)


I denne artikel fører dig gennem trinnene til at oprette en DNS-zone ved hjælp af CLI. Du kan også oprette en DNS-zone, ved hjælp af PowerShell eller Azure portalen.

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]


## <a name="before-you-begin"></a>Inden du går i gang

Du kan bruge Microsoft Azure CLI disse instruktioner. Sørg for at opdatere til den seneste Azure CLI (0.9.8 eller nyere) til at bruge kommandoerne Azure DNS. Skriv `azure -v` til at kontrollere, hvilken version af Azure CLI aktuelt er installeret på computeren.

## <a name="step-1---set-up-azure-cli"></a>Trin 1 - konfigurere Azure CLI

### <a name="1-install-azure-cli"></a>1. Installer Azure CLI

Du kan installere Azure CLI til Windows, Linux eller MAC. Følgende trin skal være fuldført, før du kan administrere Azure DNS ved hjælp af Azure CLI. Flere oplysninger er tilgængelige på [installere Azure CLI](../xplat-cli-install.md). Kommandoerne DNS kræver Azure CLI version 0.9.8 eller nyere.

Alle netværk udbyder kommandoerne på CLI kan være fundet ved hjælp af følgende kommando:

    azure network

### <a name="2-switch-cli-mode"></a>2. Skift CLI tilstand

Azure DNS bruger Azure ressourcestyring. Kontrollér, at du har skiftet CLI tilstand for at bruge ARM kommandoer.

    azure config mode arm

### <a name="3-sign-in-to-your-azure-account"></a>3. log på din Azure-konto

Du bliver bedt om at godkende med dine legitimationsoplysninger. Husk på, at du kan kun bruge ORGID konti.

    azure login -u "username"

### <a name="4-select-the-subscription"></a>4. Vælg abonnementet

Vælg, hvilke af dine Azure abonnementer til brug.

    azure account set "subscription name"

### <a name="5-create-a-resource-group"></a>5. Opret en ressourcegruppe

Azure ressourcestyring kræver, at alle ressourcegrupper angive en placering. Det bruges som standardplacering til ressourcer i denne ressourcegruppe. Da alle DNS-ressourcer er global, ikke internationale, har valget af ressource gruppe placering dog ingen indvirkning på Azure DNS.

Du kan springe dette trin, hvis du bruger en eksisterende ressourcegruppe.

    azure group create -n myresourcegroup --location "West US"


### <a name="6-register"></a>6. Registrer

Tjenesten Azure DNS administreres af provideren Microsoft.Network ressource. Abonnementet Azure skal være registreret for at bruge denne ressource provider, før du kan bruge Azure DNS. Dette er en enkeltstående handling for hvert abonnement.

    azure provider register --namespace Microsoft.Network


## <a name="step-2---create-a-dns-zone"></a>Trin 2 – Opret en DNS-zone

En DNS-zone er oprettet ved hjælp af den `azure network dns zone create` kommandoen. Du kan vælge at oprette en DNS-zone sammen med mærker. Mærker er en liste over navne værdipar og bruges ved Azure ressourcestyring til etiket ressourcer til fakturerings- og grupperingsniveau formål. Du kan finde flere oplysninger om mærker, [ved hjælp af mærker til at organisere dine Azure ressourcer](../resource-group-using-tags.md).

I Azure DNS zone navne skal angives uden en afslutning **'.'**. For eksempel som '**contoso.com**' i stedet '**contoso.com.**'.


### <a name="to-create-a-dns-zone"></a>At oprette en DNS-zone

I nedenstående eksempel oprettes en DNS-zone, kaldet *contoso.com* i ressourcegruppen kaldet *MyResourceGroup*.

Bruge eksemplet til at oprette din DNS-zone, erstatning med værdierne for din egen.

    azure network dns zone create myresourcegroup contoso.com

### <a name="to-create-a-dns-zone-and-tags"></a>Du kan oprette en DNS-zone og mærker.

Azure DNS CLI understøtter mærker af DNS-zoner angives ved hjælp af det valgfri *-mærke* parameter. Følgende eksempel viser, hvordan du kan oprette en DNS-zone med to mærker, = demo og env = test.

Brug eksemplet nedenfor til at oprette en DNS-zone og de mærker, erstatning med værdierne for din egen.

    azure network dns zone create myresourcegroup contoso.com -t "project=demo";"env=test"

## <a name="view-records"></a>Få vist poster

Oprette en DNS-zone, oprettes der også følgende DNS-poster:

- 'Start af nøglecenter' (SOA) post. Dette er til stede i roden af alle DNS-zone.

- Autoritative (NS) navneserverposter. Disse viser, hvilke navneservere er vært for zonen. Azure DNS bruger en samling af navneservere og så forskellige navneservere kan tildeles til forskellige zoner i Azure DNS. Du kan få flere oplysninger i [stedfortræder et domæne til Azure DNS](dns-domain-delegation.md) .

For at få vist disse poster, bruge `azure network dns-record-set show`.<BR>
*Format: netværk dns-post-sæt show <-ressourcegruppe >< dns-zone-navn > <name><type>*


I eksemplet nedenfor, hvis du kører kommandoen med ressource gruppe *myresourcegroup*post angivet navn *"@"* (for en rod-post), og skriv *SOA*, det resulterer i følgende eksempel:


    azure network dns record-set show myresourcegroup "contoso.com" "@" SOA
    info:    Executing command network dns-record-set show
    + Looking up the DNS record set "@"
    data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/SOA
    data:    Location                        : global
    data:    TTL                             : 3600
    data:    SOA record:
    data:      Email                         : msnhst.microsoft.com
    data:      Expire time                   : 604800
    data:      Host                          : edge1.azuredns-cloud.net
    data:      Minimum TTL                   : 300
    data:      Refresh time                  : 900
    data:      Retry time                    : 300
    data:                                    :
<BR>
For at se på NS-poster, der er oprettet med zonen, skal du bruge følgende kommando:

    azure network dns record-set show myresourcegroup "contoso.com" "@" NS
    info:    Executing command network dns-record-set show
    + Looking up the DNS record set "@"
    data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/NS
    data:    Location                        : global
    data:    TTL                             : 3600
    data:    NS records
    data:        Name server domain name     : ns1-05.azure-dns.com
    data:        Name server domain name     : ns2-05.azure-dns.net
    data:        Name server domain name     : ns3-05.azure-dns.org
    data:        Name server domain name     : ns4-05.azure-dns.info
    data:
    info:    network dns-record-set show command OK

>[AZURE.NOTE] Post sæt roden (eller *toppen*) Brug en DNS-Zone **@** posten angivet navn.

## <a name="test"></a>Test

Du kan teste din DNS-zone ved hjælp af DNS-funktioner som nslookup, grave ud, eller den `Resolve-DnsName` PowerShell-cmdlet.

Hvis du endnu ikke har uddelegeret domænet til at bruge den nye zone i Azure DNS, skal du dirigere forespørgslen DNS direkte til en navneservere for zonen. Navneservere for zonen fremgår NS-poster, som er angivet af "azure netværk dns record set Vis" ovenfor. Sørg for, på Udskift de korrekte værdier for zonen i kommandoen nedenfor.

I følgende eksempel bruger grave ud til at forespørge på domænet contoso.com ved hjælp af DNS-servere, der er tildelt til DNS-zonen. Forespørgslen indeholder til at pege på en navneserver, hvor vi har brugt * @ * og zonenavn ved hjælp af grave ud.

     <<>> DiG 9.10.2-P2 <<>> @ns1-05.azure-dns.com contoso.com
    (1 server found)
    global options: +cmd
    Got answer:
    ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
    flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
    WARNING: recursion requested but not available

    OPT PSEUDOSECTION:
    EDNS: version: 0, flags:; udp: 4000
    QUESTION SECTION:
    contoso.com.                        IN      A

    AUTHORITY SECTION:
    contoso.com.         300     IN      SOA     edge1.azuredns-cloud.net.
    msnhst.microsoft.com. 6 900 300 604800 300

    Query time: 93 msec
    SERVER: 208.76.47.5#53(208.76.47.5)
    WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
    MSG SIZE  rcvd: 120

## <a name="next-steps"></a>Næste trin

Når du har oprettet en DNS-zone, oprette [post sæt og poster](dns-getstarted-create-recordset-cli.md) for at starte navnekonflikter for dit domæne på internettet.
