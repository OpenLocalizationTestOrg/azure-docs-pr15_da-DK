<properties
   pageTitle="Introduktion til Azure DNS | Microsoft Azure"
   description="Lær at oprette DNS-zoner til Azure DNS. Dette er et trin ved trin for at få din første DNS-zone, der er oprettet for at starte vært for din DNS-domæne, ved hjælp af PowerShell."
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

# <a name="create-a-dns-zone-using-powershell"></a>Oprette en DNS-zone, ved hjælp af Powershell

> [AZURE.SELECTOR]
- [Azure-portalen](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)

I denne artikel fører dig gennem trinnene til at oprette en DNS-zone ved hjælp af PowerShell. Du kan også oprette en DNS-zone, ved hjælp af CLI eller Azure portalen.

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="tagetag"></a>Om Etags og mærker

### <a name="etags"></a>Etags

Antag, at to personer eller to processer at forsøge at ændre en DNS-post på samme tid. Hvilken vinder? Og vinderen ved, at de lige har overskrevet ændringer, der er oprettet af en anden?

Azure DNS bruger Etags til at håndtere samtidige ændringer i den samme ressource på en sikker måde. Hver DNS ressource (zone eller postsæt) har en Etag, der er knyttet til den. Når en ressource er hentet, er dens Etag også hentet. Når du opdaterer en ressource, har du mulighed for at videregive tilbage på Etag så Azure DNS kan kontrollere, at Etag server matches. Da hver opdatering til en ressource resulterer i Etag genoprettes, angiver en uoverensstemmelse i Etag der opstod en samtidige ændring. Etags bruges også når du opretter en ny ressource til at sikre, at ressourcen, der ikke allerede findes.

Som standard bruger Azure DNS PowerShell Etags til at blokere samtidige ændringer til zoner og registrere sæt. Det valgfri *-overskrive* switch kan bruges til at skjule Etag kontrol, i hvilket tilfælde de samtidige ændringer, der er opstået vil blive overskrevet.

På niveauet i Azure DNS REST-API er Etags angivet ved hjælp af HTTP-headere.  Deres funktion er givet i den følgende tabel:

|Sidehoved|Funktionsmåde|
|------|--------|
|Ingen|Læg lykkes altid (ingen Etag kontrol)|
|Hvis Sammenlign<etag>|Læg lykkes kun, hvis ressourcen findes og Etag svarer til|
|Hvis Sammenlign *     | Læg lykkes kun, hvis ressource findes|
|IF-none-match * |  Læg lykkes kun, hvis ressourcen ikke findes|

### <a name="tags"></a>Mærker

Mærker er forskellige fra Etags. Mærker er en liste over navne værdipar og bruges ved Azure ressourcestyring til etiket ressourcer til fakturerings- og grupperingsniveau formål. Du kan finde flere oplysninger om mærker, [ved hjælp af mærker til at organisere dine Azure ressourcer](../resource-group-using-tags.md).

Azure DNS PowerShell understøtter mærker på både zoner og postsæt angivet ved hjælp af indstillingerne for `-Tag` parameter.


## <a name="before-you-begin"></a>Inden du går i gang

Kontrollér, at du har følgende elementer, før du starter konfigurationen.

- Et Azure-abonnement. Hvis du ikke allerede har et Azure-abonnement, kan du aktivere din [MSDN abonnement fordele](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller Log op for at få [gratis konto](https://azure.microsoft.com/pricing/free-trial/).

- Du skal installere den nyeste version af Azure ressourcestyring PowerShell-cmdlet'er (1.0 eller nyere). Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) kan finde flere oplysninger om installation af PowerShell-cmdlet'er.

## <a name="step-1---sign-in"></a>Trin 1 - logon

Åbn din PowerShell console og oprette forbindelse til din konto. Yderligere oplysninger finder du i [Bruge Windows PowerShell med ressourcestyring](../powershell-azure-resource-manager.md).

Brug følgende eksempel til at hjælpe dig med at oprette forbindelse:

    Login-AzureRmAccount

Markér abonnementer til kontoen.

    Get-AzureRmSubscription

Angiv det abonnement, du vil bruge.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## <a name="step-2---create-a-resource-group"></a>Trin 2 – Opret en ressourcegruppe

Azure ressourcestyring kræver, at alle ressourcegrupper angive en placering. Det bruges som standardplacering til ressourcer i denne ressourcegruppe. Da alle DNS-ressourcer er global, ikke internationale, har valget af ressource gruppe placering dog ingen indvirkning på Azure DNS.

Du kan springe dette trin, hvis du bruger en eksisterende ressourcegruppe.

    New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"


## <a name="step-3---register"></a>Trin 3 – Registrer

Tjenesten Azure DNS administreres af provideren Microsoft.Network ressource. Abonnementet Azure skal være registreret for at bruge denne ressource provider, før du kan bruge Azure DNS. Dette er en enkeltstående handling for hvert abonnement.

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network


## <a name="step-4----create-a-dns-zone"></a>Trin 4 – Opret en DNS-zone

En DNS-zone er oprettet ved hjælp af den `New-AzureRmDnsZone` cmdlet. Der findes eksempler nedenfor for at oprette en DNS-zone, med eller uden mærker. Du kan finde flere oplysninger om mærker, i afsnittet på [mærker](#tags) i denne artikel.

>[AZURE.NOTE] I Azure DNS zone navne skal angives uden en afslutning **'.'**. For eksempel som '**contoso.com**' i stedet '**contoso.com.**'.

### <a name="to-create-a-dns-zone"></a>At oprette en DNS-zone

I nedenstående eksempel oprettes en DNS-zone, kaldet *contoso.com* i ressourcegruppen kaldet *MyResourceGroup*. Bruge eksemplet til at oprette en DNS-zone, erstatning med værdierne for din egen.

    New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

### <a name="to-create-a-dns-zone-with-tags"></a>At oprette en DNS-zone med mærker

Følgende eksempel viser, hvordan du opretter en DNS-zone med to mærker *project = demo* og *env = test*. Bruge eksemplet til at oprette en DNS-zone, erstatning med værdierne for din egen.

    New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @( @{ Name="project"; Value="demo" }, @{ Name="env"; Value="test" } )

## <a name="view-records"></a>Få vist poster

Oprette en DNS-zone, oprettes der også følgende DNS-poster:

- *Start af nøglecenter* (SOA) post. Dette er til stede i roden af alle DNS-zone.

- Autoritative (NS) navneserverposter. Disse viser, hvilke navneservere er vært for zonen. Azure DNS bruger en samling af navneservere og så forskellige navneservere kan tildeles til forskellige zoner i Azure DNS. Se [uddelegere et domæne til Azure DNS](dns-domain-delegation.md) kan finde flere oplysninger.

For at få vist disse poster, bruge `Get-AzureRmDnsRecordSet`:

    Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

    Name              : @
    ZoneName          : contoso.com
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 2b855de1-5c7e-4038-bfff-3a9e55b49caf
    RecordType        : SOA
    Records           : {[ns1-01.azure-dns.com,msnhst.microsoft.com,900,300,604800,300]}
    Tags              : {}

    Name              : @
    ZoneName          : contoso.com
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
    RecordType        : NS
    Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                  ns4-01.azure-dns.info}
    Tags              : {}


Post sæt roden (eller *toppen*) Brug en DNS-Zone **@** posten angivet navn.


## <a name="test"></a>Test

Du kan teste din DNS-zone ved hjælp af DNS-funktioner som nslookup, grave ud eller [Løs DnsName PowerShell-cmdlet](https://technet.microsoft.com/library/jj590781.aspx).

Hvis du endnu ikke har uddelegeret domænet til at bruge den nye zone i Azure DNS, skal du dirigere forespørgslen DNS direkte til en navneservere for zonen. På DNS-serverne for zonen fremgår NS-poster, som vises ved `Get-AzureRmDnsRecordSet` ovenfor. Sørg for, på Udskift de korrekte værdier for zonen til kommandoen nedenfor.

    nslookup
    > set type=SOA
    > server ns1-01.azure-dns.com
    > contoso.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    contoso.com
            primary name server = ns1-01.azure-dns.com
            responsible mail addr = msnhst.microsoft.com
            serial  = 1
            refresh = 900 (15 mins)
            retry   = 300 (5 mins)
            expire  = 604800 (7 days)
            default TTL = 300 (5 mins)


## <a name="next-steps"></a>Næste trin

Når du har oprettet en DNS-zone, oprette [post sæt og poster](dns-getstarted-create-recordset.md) for at starte navnekonflikter for dit domæne på internettet.

