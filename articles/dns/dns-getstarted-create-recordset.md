<properties
   pageTitle="Oprette et postsæt og poster til en DNS-zone, ved hjælp af PowerShell | Microsoft Azure"
   description="Sådan oprettes host records til Azure DNS. Konfiguration af post angiver og poster ved hjælp af PowerShell"
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



# <a name="create-dns-record-sets-and-records-by-using-powershell"></a>Oprette DNS-post sæt og poster ved hjælp af PowerShell


> [AZURE.SELECTOR]
- [Azure-portalen](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Azure CLI](dns-getstarted-create-recordset-cli.md)

I denne artikel fører dig gennem processen med at oprette poster og poster sæt ved hjælp af Windows PowerShell. Når du har oprettet din DNS-zone, kan du tilføje DNS-posterne for dit domæne. For at gøre dette, skal du først forstå DNS-poster og postsæt.

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## <a name="verify-that-you-have-the-latest-version-of-powershell"></a>Kontrollér, at du har den nyeste version af PowerShell

Kontrollér, at du har installeret den nyeste version af Azure ressourcestyring PowerShell-cmdlet'er. Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) kan finde flere oplysninger om installation af PowerShell-cmdlet'er.

## <a name="create-a-record-set-and-record"></a>Oprette et postsæt og en post

I dette afsnit beskrives, hvordan du opretter et postsæt og post.


### <a name="1-connect-to-your-subscription"></a>1. oprette forbindelse til dit abonnement

Åbn din PowerShell console og oprette forbindelse til din konto. Brug følgende eksempel til at hjælpe dig med at oprette forbindelse:

    Login-AzureRmAccount

Markér abonnementer til kontoen.

    Get-AzureRmSubscription

Angiv det abonnement, du vil bruge.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

Du kan finde flere oplysninger om at arbejde med PowerShell i [Bruge Windows PowerShell med ressourcestyring](../powershell-azure-resource-manager.md).


### <a name="2-create-a-record-set"></a>2. Opret et postsæt

Du opretter postsæt ved hjælp af den `New-AzureRmDnsRecordSet` cmdlet. Når du opretter et postsæt, skal du angive posten navn, zonen, tid til at levetid (TTL) og posttypen.

At oprette en post, der er angivet i i zonen nedre (i dette tilfælde "contoso.com"), Brug navnet på posten "@", også anførselstegnene. Dette er et almindelige DNS-konferencen.

I følgende eksempel oprettes en post, der er konfigureret med det relative navn "www" i den DNS-Zone "contoso.com". Fuldt kvalificeret navnet på den postsæt er "www.contoso.com". Record type er "A", og TTL er 60 sekunder. Når du har fuldført dette trin, har du en tom "www" post, der er tildelt den variable *$rs*.

    $rs = New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 60

#### <a name="if-a-record-set-already-exists"></a>Hvis en post angiver allerede findes

Hvis en post, angive allerede findes, mislykkes kommandoen, medmindre den *-overskrive* parameter bruges. Den *-overskrive* indstilling udløser en bekræftelsesmeddelelsen, som kan udelades ved hjælp af den *-kraft* skifte.


    $rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 -ZoneName contoso.com -ResouceGroupName MyAzureResouceGroup [-Tag $tags] [-Overwrite] [-Force]


I dette eksempel skal angive du zonen ved hjælp af zonenavn og ressource gruppenavn. Alternativt kan du angive en zone objekt, som den returneres af `Get-AzureRmDnsZone` eller `New-AzureRmDnsZone`.

    $zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup
    $rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 –Zone $zone [-Tag $tags] [-Overwrite] [-Force]

`New-AzureRmDnsRecordSet`Returnerer et lokalt objekt, der repræsenterer den post, der er oprettet i Azure DNS.

### <a name="3-add-a-record"></a>3. Tilføj en post

Hvis du vil bruge nyoprettede "www" postsæt, skal du føje poster til den. Du kan tilføje IPv4 *A* poster til "www" post oprettes med følgende eksempel. I dette eksempel er afhængig af den variable *$rs* , der konfigureres i det forrige trin.

Føje poster til en post angive ved hjælp af `Add-AzureRmDnsRecordConfig` er handlingen offline. Kun den lokale variabel *$rs* opdateres.


    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.185.46
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.188.221

### <a name="4-commit-the-changes"></a>4. Foretag ændringerne

Udfør ændringerne til postsættet. Brug `Set-AzureRmDnsRecordSet` at overføre ændringerne til den post, der er angivet til Azure DNS.

    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="5-retrieve-the-record-set"></a>5. hente den postsæt

Du kan hente postsættet fra Azure DNS ved hjælp af `Get-AzureRmDnsRecordSet` som vist i følgende eksempel.


    Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup


    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : MyAzureResourceGroup
    Ttl               : 3600
    Etag              : 68e78da2-4d74-413e-8c3d-331ca48246d9
    RecordType        : A
    Records           : {134.170.185.46, 134.170.188.221}
    Tags              : {}


Du kan også bruge værktøjet nslookup eller andre DNS-værktøjer til at forespørge på nye postsæt.

Hvis du endnu ikke har delegeret domæne til Azure DNS-navneservere, skal du angive eksplicit navn, server og adresse for zonen.


    nslookup www.contoso.com ns1-01.azure-dns.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    Name:    www.contoso.com
    Addresses:  134.170.185.46
                134.170.188.221

## <a name="create-a-record-set-of-each-type-with-a-single-record"></a>Oprette en postsæt af hver type med en enkelt post


Følgende eksempler viser, hvordan du opretter en post række hver posttype. Hver postsæt indeholder en enkelt post.

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]


## <a name="next-steps"></a>Næste trin

[Hvordan du administrerer DNS zones ved hjælp af PowerShell](dns-operations-dnszones.md)

[Administrere DNS-poster og postsæt ved hjælp af PowerShell](dns-operations-recordsets.md)

[Automatisere Azure handlinger med .NET SDK](dns-sdk.md)
