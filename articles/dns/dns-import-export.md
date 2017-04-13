<properties
   pageTitle="Importere og eksportere en domæne zonefil til Azure DNS ved hjælp af CLI | Microsoft Azure"
   description="Lær, hvordan du importere og eksportere en DNS zone file til Azure DNS ved hjælp af Azure CLI"
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>

# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>Importere og eksportere en DNS-zone-fil med Azure CLI


I denne artikel fører dig gennem Sådan importere og eksportere DNS-zone filer til Azure DNS ved hjælp af Azure CLI.

## <a name="introduction-to-dns-zone-migration"></a>Introduktion til DNS-zone overførsel

En DNS-zonefil er en tekstfil, der indeholder oplysninger om hver Domain Name System (DNS) post i zonen. Det følger et standardformat, så det passer til overførsel af DNS-poster mellem DNS-systemer. Brug af en zonefil er en hurtig, pålidelig og nem metode til at overføre en DNS-zone, ind eller ud af Azure DNS.

Azure DNS understøtter import og eksport af zone filer ved hjælp af Azure kommandolinjen (CLI). Azure CLI er et kommandolinjen på tværs af platforme-værktøj, der bruges til at styre Azure tjenester. Den er tilgængelig til Windows, Mac og Linux platforme fra [Azure downloads-siden](https://azure.microsoft.com/downloads/). Understøttelse af flere platforme er særligt vigtig til import og eksport zone filer, fordi den mest almindelige navn-serversoftware, [BINDE](https://www.isc.org/downloads/bind/), kører typisk på Linux.

## <a name="obtain-your-existing-dns-zone-file"></a>Få din eksisterende DNS zone file

Før du importerer en DNS zone file til Azure DNS, skal du hente en kopi af filen zone. Kilden til denne fil afhænger af, hvor DNS-zonen i øjeblikket er hostet.

- Hvis din DNS-zone hostes af en partner tjeneste (såsom en domæneregistrator, dedikeret DNS-udbyder eller alternative skyen provider), kan denne tjeneste give mulighed for at hente DNS zone file.

- Hvis din DNS-zone hostes på Windows DNS, er standardmappen for zone filerne **%systemroot%\system32\dns**. Den fulde sti til hver zonefil viser også under fanen **Generelt** i DNS service management console.

- Hvis din DNS-zone hostes ved hjælp af BIND, angives placeringen af filen zone for hver zone i BIND konfiguration fil **named.conf**.

**Arbejde med zone filer fra GoDaddy**<BR>
Zone filer overført fra GoDaddy har en smule formater. Du vil rette dette, før du importerer filerne zone til Azure DNS. DNS-navne i RData hver DNS-post er angivet som fuldstændige navne, men de ikke har en afslutning "." Det betyder, at de fortolkes af andre DNS-systemer som relative navne. Du skal redigere filen zone for at tilføje den afsluttende "." til deres navne, før du importere dem til Azure DNS.

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Importere en DNS zone file til Azure DNS


Importere en zonefil opretter en ny zone i Azure DNS Hvis der ikke allerede findes. Hvis zonen allerede findes, skal postsæt i filen zone flettes med de eksisterende post sæt.

### <a name="merge-behavior"></a>Flette funktionsmåde

- Som standard flettes eksisterende og nye postsæt. Identiske poster i en flettet postsæt er deaktivere dublerede.

- Du kan også ved at angive den `--force` indstillingen importprocessen erstatter eksisterende postsæt med nye postsæt. Eksisterende post sæt, der ikke har en tilsvarende post, der er angivet i filen importerede zone fjernes ikke.

- Når postsæt flettes, bruges tidspunkt levetid (TTL) af eksisterende postsæt. Når `--force` er brugt, bruges TTL på den nye postsæt.

- Start af nøglecenter SOA-parametre (undtagen `host`) er altid tages fra filen importerede zone, uanset om du `--force` bruges. På samme måde for name server postsættet i toppen zone, hentes TTL altid fra den importerede zonefil.

- En importeret CNAME-post erstatter ikke en eksisterende CNAME-post med samme navn, medmindre den `--force` parameter er angivet.

- Når der opstår en konflikt mellem en CNAME-post og en anden post i det samme navn, men anden type (uanset hvor eksisterende eller nye), bevares den eksisterende post. Dette er uafhængig af brugen af `--force`.

### <a name="additional-information-about-importing"></a>Yderligere oplysninger om at importere

Følgende noter giver yderligere tekniske oplysninger om zonen importen.

- Den `$TTL` direktiv er valgfrit, og det understøttes. Når ingen `$TTL` direktiv gives, poster uden en eksplicit TTL bliver importeret indstillet til standard TTL på 3.600 sekunder. Når to poster i den samme postsæt angiver forskellige TTLs, bruges den nederste værdi.

- Den `$ORIGIN` direktiv er valgfrit, og det understøttes. Når ingen `$ORIGIN` er konfigureret, bruges standardværdien er zone navnet som angivet på kommandolinjen (plus de afslutning ".").

- Den `$INCLUDE` og `$GENERATE` direktiver understøttes ikke.

- Disse poster af typen understøttes: A, AAAA, CNAME, MX, NS, SOA, SRV og TXT.

- SOA posten oprettes automatisk efter Azure DNS, når der oprettes en zone. Når du importerer en zonefil, alle parametre for SOA hentes fra zone filer *undtagen* den `host` parameter. Denne parameter bruges den værdi, der leveres af Azure DNS. Dette skyldes, at denne parameter skal referere til feltet primary name server, der leveres af Azure DNS.

- Name server posterne i toppen zone oprettes også automatisk efter Azure DNS, når zonen oprettes. Kun TTL på denne postsæt importeres. Disse poster indeholder servernavne navn fra Azure DNS. Postens data overskrives ikke af de værdier, der er indeholdt i filen importerede zone.

- Under Public Preview understøtter Azure DNS kun én streng TXT records. MultiString TXT records er sammenføjet og afkortet til 255 tegn.

### <a name="cli-format-and-values"></a>CLI format og værdier


Formatet af Azure CLI kommandoen til at importere en DNS-zone er:<BR>`azure network dns zone import [options] <resource group> <zone name> <zone file name>`

Værdier:

- `<resource group>`er navnet på ressourcegruppen for zonen i Azure DNS.
- `<zone name>`er navnet på zonen.
- `<zone file name>`er stien/navnet på filen zone der skal importeres.

Hvis et område med dette navn ikke findes i ressourcegruppen, oprettes den for dig. Hvis der findes allerede zonen, flettes de importerede postsæt med eksisterende postsæt. Hvis du vil overskrive de eksisterende post sæt, skal du bruge den `--force` indstilling.

Du kan kontrollere formatet for en zonefil uden at importere dataene faktisk, bruge den `--parse-only` indstilling.

### <a name="step-1-import-a-zone-file"></a>Trin 1. Importere en zonefil

Importere en zonefil til zone **contoso.com**.

1. Log på abonnementet Azure ved hjælp af Azure CLI.

        azure login

2. Vælg det abonnement, hvor du vil oprette din nye DNS-zone.

        azure account set <subscription name>

3. Azure DNS er en Azure kun ressourcestyring tjeneste, så Azure CLI skal skiftes til Ressourcestyring tilstand.

        azure config mode arm

4. Før du bruger tjenesten Azure DNS, skal du registrere dit abonnement for at bruge provideren Microsoft.Network ressource. (Dette er en enkeltstående handling for hvert abonnement).

        azure provider register Microsoft.Network

5. Hvis du ikke allerede har et, skal du også oprette en ressourcestyring ressourcegruppe.

        azure group create myresourcegroup westeurope

6. Kør kommandoen for at importere zone **contoso.com** fra filen **contoso.com.txt** til en ny DNS zone i ressource gruppe **myresourcegroup**, `azure network dns zone import`.<BR>Denne kommando vil indlæse zone filen og fortolke den. Kommandoen vil udføre en række kommandoer på Azure DNS-tjenesten til at oprette zonen, og alle posten angiver i zonen. Kommandoen kan også rapportere status i vinduet console sammen med eventuelle fejl eller advarsler. Fordi postsæt er oprettet i serien, kan det tage et par minutter at importere en stor zonefil.

        azure network dns zone import myresourcegroup contoso.com contoso.com.txt



### <a name="step-2-verify-the-zone"></a>Trin 2. Bekræfte zonen

For at bekræfte DNS-zonen, når du har importeret filen, kan du bruge en af følgende metoder:

- Du kan få vist posterne ved hjælp af kommandoen følgende Azure CLI.

        azure network dns record-set list myresourcegroup contoso.com

- Du kan få vist posterne ved hjælp af PowerShell-cmdlet `Get-AzureRmDnsRecordSet`.

- Du kan bruge `nslookup` at bekræfte navneoversættelse for posterne. Fordi zonen ikke delegerede endnu, skal du angive de korrekte Azure DNS-navneservere eksplicit. Eksemplet nedenfor viser, hvordan du hente de navn servernavne, der er tildelt til zonen. IT også viser, hvordan du forespørge "www"-post ved hjælp af `nslookup`.

        C:\>azure network dns record-set show myresourcegroup contoso.com @ NS
        info:Executing command network dns record-set show
        + Looking up the DNS Record Set "@" of type "NS"
        data:Id: /subscriptions/…/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
        data:Name: @
        data:Type: Microsoft.Network/dnszones/NS
        data:Location: global
        data:TTL : 3600
        data:NS records
        data:Name server domain name : ns1-01.azure-dns.com
        data:Name server domain name : ns2-01.azure-dns.net
        data:Name server domain name : ns3-01.azure-dns.org
        data:Name server domain name : ns4-01.azure-dns.info
        data:
        info:network dns record-set show command OK

        C:\> nslookup www.contoso.com ns1-01.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221

### <a name="step-3-update-dns-delegation"></a>Trin 3. Opdatere DNS-delegering

Når du har bekræftet, at zonen er blevet importeret korrekt, skal du opdatere DNS-delegering til at pege på Azure DNS-navneservere. Få mere at vide artiklen [opdatere DNS-delegering](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Eksportere en DNS-zonefil fra Azure DNS

Formatet af Azure CLI kommandoen til at importere en DNS-zone er:<BR>`azure network dns zone export [options] <resource group> <zone name> <zone file name>`

Værdier:

- `<resource group>`er navnet på ressourcegruppen for zonen i Azure DNS.
- `<zone name>`er navnet på zonen.
- `<zone file name>`er stien/navnet på filen zone der skal eksporteres.

Som med importen zone har brug for du først at logge på, Vælg dit abonnement, og Konfigurer Azure CLI til brug af Ressourcestyring tilstand.

### <a name="to-export-a-zone-file"></a>Sådan eksporteres en zone


1. Log på abonnementet Azure ved hjælp af Azure CLI.

        azure login

2. Vælg det abonnement, hvor du vil oprette din nye DNS-zone.

        azure account set <subscription name>

3. Azure DNS er en Azure kun ressourcestyring tjeneste. Azure CLI skal skiftes til Ressourcestyring tilstand.

        azure config mode arm

4. Hvis du vil eksportere de Azure DNS zone- **contoso.com** , der eksisterende i ressource gruppe **myresourcegroup** til filen **contoso.com.txt** (i den aktuelle mappe), kører `azure network dns zone export`. Denne kommando ringer Azure DNS-tjenesten for at Optæl post sæt i zonen og eksportere resultaterne til en BIND-kompatible zonefil.

        azure network dns zone export myresourcegroup contoso.com contoso.com.txt

