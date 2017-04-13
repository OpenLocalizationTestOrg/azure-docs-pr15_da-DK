<properties
   pageTitle="Oprette brugerdefinerede DNS-poster til en WebApp | Microsoft Azure  "
   description="Sådan oprettes brugerdefinerede domæne DNS-poster for online ved hjælp af Azure DNS."
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

# <a name="create-dns-records-for-a-web-app-in-a-custom-domain"></a>Oprette DNS-poster til en WebApp i et brugerdefineret domæne

Du kan bruge Azure DNS til at hoste et brugerdefineret domæne til din online. For eksempel, du opretter en Azure-WebApp, og du vil dine brugere at få adgang til den ved hjælp af en bruger contoso.com eller www.contoso.com som et fuldt Domænenavn.

For at gøre dette, har du oprette to poster:

- En rod "A"-post, der peger på contoso.com
- En "CNAME"-post for www navnet, der peger på A-posten

Husk på, hvis du opretter en A-post til en WebApp i Azure, A-posten skal manuelt opdateres, hvis den underliggende IP-adresse til web app-ændringerne.

## <a name="before-you-begin"></a>Inden du går i gang

Før du begynder, skal du først oprette en DNS-zone i Azure DNS og uddelegere zonen i din domæneregistrator til Azure DNS.

1. Hvis du vil oprette en DNS-zone, skal du følge trinnene i [oprette en DNS-zone](dns-getstarted-create-dnszone.md).
2. Hvis du vil uddelegere DNS til Azure DNS, skal du følge trinnene i [uddelegering af DNS-domæne](dns-domain-delegation.md).

Når du opretter en zone og uddelegere til Azure DNS, kan du derefter oprette poster for dit brugerdefinerede domæne.


## <a name="1-create-an-a-record-for-your-custom-domain"></a>1. oprette en A-post for dit brugerdefinerede domæne

En A-post, der bruges til at knytte et navn til dens IP-adresse. I eksemplet nedenfor vil vi tildele @ som en A-post til en IPv4-adresse:

### <a name="step-1"></a>Trin 1

Oprette en A-post og tildele til en variabel $rs

    $rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600

### <a name="step-2"></a>Trin 2

Føje værdien IPv4 til den tidligere oprettet postsæt "@" ved hjælp af variablen $rs tildelt. IPv4-værdi, der er tildelt bliver IP-adressen for din online.

For at finde IP-adressen for en web-app, skal du følge trinnene i [konfigurere et brugerdefineret domænenavn i Azure App-tjeneste](../web-sites-custom-domain-name.md#Find-the-virtual-IP-address).

    Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address <your web app IP address>

### <a name="step-3"></a>Trin 3

Udfør ændringerne til postsættet. Brug `Set-AzureRMDnsRecordSet` at overføre ændringerne til den post, der er angivet til Azure DNS:

    Set-AzureRMDnsRecordSet -RecordSet $rs

## <a name="2-create-a-cname-record-for-your-custom-domain"></a>2. Opret en CNAME-post til dit eget domæne

Hvis dit domæne administreres allerede af Azure DNS (se [uddelegering af DNS-domæne](dns-domain-delegation.md), du kan bruge følgende eksempel til at oprette en CNAME-post til contoso.azurewebsites.net.

### <a name="step-1"></a>Trin 1

Åbne PowerShell og oprette en ny CNAME postsæt og tildele til en variabel $rs. I dette eksempel opretter et postsæt type CNAME med en "time to live" af 600 sekunder i DNS-zone med navnet "contoso.com".

    $rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600

    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
    RecordType        : CNAME
    Records           : {}
    Tags              : {}


### <a name="step-2"></a>Trin 2

Når sættet CNAME-post er oprettet, skal du oprette et alias værdi, som peger på WebApp.

Ved hjælp af variablen tidligere tildelte "$rs" kan du bruge kommandoen PowerShell nedenfor til at oprette alias for web app-contoso.azurewebsites.net.

    Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"

    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}

### <a name="step-3"></a>Trin 3

Foretag ændringerne ved hjælp af den `Set-AzureRMDnsRecordSet` cmdlet:

    Set-AzureRMDnsRecordSet -RecordSet $rs

Du kan validere posten blev oprettet korrekt ved at forespørge på "www.contoso.com" ved hjælp af nslookup, som vist nedenfor:

    PS C:\> nslookup
    Default Server:  Default
    Address:  192.168.0.1

    > www.contoso.com
    Server:  default server
    Address:  192.168.0.1

    Non-authoritative answer:
    Name:    <instance of web app service>.cloudapp.net
    Address:  <ip of web app service>
    Aliases:  www.contoso.com
    contoso.azurewebsites.net
    <instance of web app service>.vip.azurewebsites.windows.net

## <a name="create-an-awverify-record-for-web-apps"></a>Oprette en "awverify" post for webapps


Hvis du beslutter at bruge en A-post til din online, skal du gå gennem en bekræftelsesprocessen at sikre, at du ejer dit eget domæne. Denne kontrol trin gøres ved at oprette en speciel CNAME-post med navnet "awverify". Dette afsnit gælder for kun en poster.


### <a name="step-1"></a>Trin 1

Oprette "awverify"-posten. I eksemplet nedenfor vil vi oprette "aweverify" posten for contoso.com at bekræfte ejerskab for det brugerdefinerede domæne.

    $rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "awverify" -RecordType "CNAME" -Ttl 600

    Name              : awverify
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
    RecordType        : CNAME
    Records           : {}
    Tags              : {}


### <a name="step-2"></a>Trin 2

Når postsæt "awverify" er oprettet, kan du tildele CNAME-posten angive alias. I eksemplet nedenfor vil vi tildele CNAMe-posten angivet alias til awverify.contoso.azurewebsites.net.

    Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"

    Name              : awverify
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {awverify.contoso.azurewebsites.net}
    Tags              : {}

### <a name="step-3"></a>Trin 3

Foretag ændringerne ved hjælp af den `Set-AzureRMDnsRecordSet cmdlet`, som vist i kommandoen nedenfor.

    Set-AzureRMDnsRecordSet -RecordSet $rs



## <a name="next-steps"></a>Næste trin

Følg trinnene i [konfiguration af et brugerdefineret domænenavn til App-tjenesten](../app-service-web/web-sites-custom-domain-name.md) til at konfigurere din online for at bruge et brugerdefineret domæne.








