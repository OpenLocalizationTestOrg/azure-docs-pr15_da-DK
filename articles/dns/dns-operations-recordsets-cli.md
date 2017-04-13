<properties
   pageTitle="Administrere DNS-post sæt og poster på Azure DNS ved hjælp af Azure CLI | Microsoft Azure"
   description="Administration af DNS-post sæt og på Azure DNS-poster, når vært for dit domæne på Azure DNS. Alle CLI kommandoer for handlinger på postsæt og poster."
   services="dns"
   documentationCenter="na"
   authors="jtuliani"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="jtuliani"/>

# <a name="manage-dns-records-and-record-sets-by-using-cli"></a>Administrere DNS-poster og postsæt ved hjælp af CLI


> [AZURE.SELECTOR]
- [Azure-portalen](dns-operations-recordsets-portal.md)
- [Azure CLI](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)


I denne artikel beskrives, hvordan du administrerer postsæt og -posterne for din DNS-zone ved hjælp af flere platforme Azure kommandolinjen (CLI).

Det er vigtigt at forstå forskellen mellem DNS-post sæt og enkelte DNS-poster. Et postsæt er en samling af poster i en zone, der har samme navn og er af samme type. Se [forstå postsæt og poster](dns-getstarted-create-recordset-cli.md)kan finde flere oplysninger.


## <a name="configure-the-cross-platform-azure-cli"></a>Konfigurere på tværs af platforme Azure CLI

Azure DNS er en Azure kun ressourcestyring tjeneste. Det har ikke en Azure Service Management API. Sørg for, at Azure CLI er konfigureret til at bruge ressourcestyring tilstand, ved hjælp af den `azure config mode arm` kommandoen.

Hvis du får vist **fejl: 'dns' ikke er en azure-kommando**, det er mest sandsynlige, fordi du bruger Azure CLI i Azure Service Management tilstand og ikke i ressourcestyring tilstand.

## <a name="create-a-new-record-set-and-record"></a>Oprette en ny postsæt og post

For at oprette en ny post, der er angivet i portalen Azure, skal du se [oprette et postsæt og poster](dns-getstarted-create-recordset-cli.md).


## <a name="retrieve-a-record-set"></a>Hente et postsæt

Hvis du vil hente en eksisterende postsæt, bruge `azure network dns record-set show`. Angive ressourcegruppen, zonenavn, angive post relative navn og posttype. Bruge eksemplet nedenfor, erstatter værdierne med dine egne.

    azure network dns record-set show myresourcegroup contoso.com www A


## <a name="list-record-sets"></a>Listen postsæt

Du kan få vist alle poster i en DNS-zone ved hjælp af den `azure network dns record-set list` kommandoen. Du skal angive ressource gruppenavn og navnet på zone.

### <a name="to-list-all-record-sets"></a>Liste over alle postsæt

I dette eksempel returnerer alle poster sæt, uanset navn eller posttype:

    azure network dns record-set list myresourcegroup contoso.com

### <a name="to-list-record-sets-of-a-given-type"></a>På listen post sæt af en bestemt type

I dette eksempel returnerer alle poster sæt, der svarer til den angivne posttype (i dette tilfælde "En" poster):

    azure network dns record-set list myresourcegroup contoso.com A


## <a name="add-a-record-to-a-record-set"></a>Tilføje en post til et postsæt

Du kan føje poster til postsæt ved hjælp af den `azure network dns record-set add-record`kommandoen. Parametre for at tilføje poster i et postsæt varierer afhængigt af typen post, der angives. For eksempel, når du bruger et postsæt af typen "A", du kan kun angive poster med parameteren `-a <IPv4 address>`.

Hvis du vil oprette en post, skal du bruge den `azure network dns record-set create`kommandoen. Angive ressourcegruppen, zonenavn, angive post relative navn, posttype og tid til live (TTL). Hvis den `--ttl` parameter ikke er defineret, værdien (i sekunder) som standard fire.

    azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300


Når du opretter på "En" postsæt, kan du tilføje IPv4-adressen ved hjælp af den `azure network dns record-set add-record`kommandoen.

    azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1


Følgende eksempler viser, hvordan du opretter en post række hver posttype. Hver postsæt indeholder en enkelt post.

[AZURE.INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]


## <a name="update-a-record-in-a-record-set"></a>Opdatere en post i et postsæt

### <a name="to-add-another-ip-address-1234-to-an-existing-a-record-set-www"></a>Angiver ("www") for at tilføje en anden IP-adresse (1.2.3.4) til en eksisterende "En" post:

    azure network dns record-set add-record  myresourcegroup contoso.com  A
    -a 1.2.3.4
    info:    Executing command network dns record-set add-record
    Record set name: www
    + Looking up the dns zone "contoso.com"
    + Looking up the DNS record set "www"
    + Updating DNS record set "www"
    data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/a/www
    data:    Name                            : www
    data:    Type                            : Microsoft.Network/dnszones/a
    data:    Location                        : global
    data:    TTL                             : 4
    data:    A records:
    data:        IPv4 address                : 192.168.1.1
    data:        IPv4 address                : 1.2.3.4
    data:
    info:    network dns record-set add-record command OK

### <a name="to-remove-an-existing-value-from-a-record-set"></a>Fjerne en eksisterende værdi fra et postsæt
Brug `azure network dns record-set delete-record`.

    azure network dns record-set delete-record myresourcegroup contoso.com www A -a 1.2.3.4
    info:    Executing command network dns record-set delete-record
    + Looking up the DNS record set "www"
    Delete DNS record? [y/n] y
    + Updating DNS record set "www"
    data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/A/www
    data:    Name                            : www
    data:    Type                            : Microsoft.Network/dnszones/A
    data:    Location                        : global
    data:    TTL                             : 4
    data:    A records:
    data:    IPv4 address                    : 192.168.1.1
    data:
    info:    network dns record-set delete-record command OK



## <a name="remove-a-record-from-a-record-set"></a>Fjerne en post fra et postsæt

Poster kan fjernes fra en post, der er angivet ved hjælp af `azure network dns record-set delete-record`. Den post, der fjernes skal være en identisk værdi med en eksisterende post på tværs af alle parametre.

Fjerne den sidste post fra et postsæt sletter ikke den postsæt. Du kan finde flere oplysninger i afsnittet i denne artikel, kaldet [slette et postsæt](#delete).

    azure network dns record-set delete-record myresourcegroup contoso.com www A -a 192.168.1.1

    azure network dns record-set delete myresourcegroup contoso.com www A

### <a name="remove-an-aaaa-record-from-a-record-set"></a>Fjerne en AAAA post fra et postsæt

    azure network dns record-set delete-record myresourcegroup contoso.com test-aaaa  AAAA -b "2607:f8b0:4009:1803::1005"

### <a name="remove-a-cname-record-from-a-record-set"></a>Fjerne en CNAME-post fra et postsæt

    azure network dns record-set delete-record myresourcegroup contoso.com test-cname CNAME -c www.contoso.com


### <a name="remove-an-mx-record-from-a-record-set"></a>Fjerne en MX-post fra et postsæt

    azure network dns record-set delete-record myresourcegroup contoso.com "@" MX -e "mail.contoso.com" -f 5

### <a name="remove-an-ns-record-from-record-set"></a>Fjerne en NS-post postsæt

    azure network dns record-set delete-record myresourcegroup contoso.com  "test-ns" NS -d "ns1.contoso.com"

### <a name="remove-a-ptr-record-from-a-record-set"></a>Fjerne en PTR-registreringen fra et postsæt
I dette tilfælde ' min-arpa-zone.com' repræsenterer zonen ARPA, der repræsenterer din IP-område.  Hver PTR-registreringen, der er angivet i denne zone svarer til en IP-adresse i denne IP-område.

    azure network dns record-set delete-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"

### <a name="remove-an-srv-record-from-a-record-set"></a>Fjerne en SRV-post fra et postsæt

    azure network dns record-set delete-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 -w 5 -o 8080 -u "sip.contoso.com"

### <a name="remove-a-txt-record-from-a-record-set"></a>Fjerne en TXT-post fra et postsæt

    azure network dns record-set delete-record myresourcegroup contoso.com  "test-TXT" TXT -x "this is a TXT record"

## <a name="delete"></a>Slette et postsæt

Postsæt kan slettes ved hjælp af den `Remove-AzureRmDnsRecordSet` cmdlet. Du kan ikke slette SOA og NS-post angiver i zone af toppen (navn = "@") , der er oprettet automatisk, når zonen blev oprettet. De slettes automatisk, hvis zonen slettes.

I eksemplet nedenfor vil blive fjernet "En" postsættet "test-a" fra "contoso.com" DNS-zone:

    azure network dns record-set delete myresourcegroup contoso.com  "test-a" A

Valgfri *-q* Skift kan bruges til at skjule bekræftelsesmeddelelsen.


## <a name="next-steps"></a>Næste trin

Se [Oversigt over Azure DNS](dns-overview.md)kan finde flere oplysninger om Azure DNS. Finde oplysninger om automatisere DNS, kan du se [oprette DNS-zoner og post angiver ved hjælp af .NET SDK](dns-sdk.md).

Hvis du vil arbejde med modsat DNS-poster, se, [hvordan du administrerer modsat DNS-poster for dine tjenester, ved hjælp af Azure CLI](dns-reverse-dns-record-operations-cli.md).
