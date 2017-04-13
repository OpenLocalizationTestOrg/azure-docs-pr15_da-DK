<properties
   pageTitle="Administrere DNS-post sæt og poster ved hjælp af portalen Azure | Microsoft Azure"
   description="Administration af DNS-post sæt og på Azure DNS-poster, når vært for dit domæne på Azure DNS. Alle PowerShell-kommandoer til handlinger på postsæt og poster."
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

# <a name="manage-dns-records-and-record-sets-by-using-powershell"></a>Administrere DNS-poster og postsæt ved hjælp af PowerShell



> [AZURE.SELECTOR]
- [Azure-portalen](dns-operations-recordsets-portal.md)
- [Azure CLI](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)



I denne artikel beskrives, hvordan du administrerer postsæt og -posterne for din DNS-zone ved hjælp af Windows PowerShell.

Det er vigtigt at forstå forskellen mellem DNS-post sæt og enkelte DNS-poster. Et postsæt er en samling af poster i en zone, der har samme navn og er af samme type. Få mere at vide under [oprette DNS-post sæt og poster ved hjælp af portalen Azure](dns-getstarted-create-recordset-portal.md).

Hvis du vil administrere postsæt og poster, skal du den nyeste version af Azure ressourcestyring PowerShell-cmdlet'er. Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md)kan finde flere oplysninger. Se [Bruge Azure PowerShell med Azure ressourcestyring](../powershell-azure-resource-manager.md)kan finde flere oplysninger om at arbejde med PowerShell.



## <a name="create-a-new-record-set-and-a-record"></a>Oprette en ny postsæt og en post

For at oprette en post, der er angivet ved hjælp af PowerShell, skal du se [oprette DNS-post sæt og poster ved hjælp af PowerShell](dns-getstarted-create-recordset.md).

## <a name="get-a-record-set"></a>Få et postsæt

Hvis du vil hente en eksisterende postsæt, bruge `Get-AzureRmDnsRecordSet`. Angive posten relative navn, posttypen og zonen.

    $rs = Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

Som med `New-AzureRmDnsRecordSet`, navnet på posten skal være et relativt navn, hvilket betyder, at det skal udelade zone navnet.

Du kan angive zonen, ved hjælp af zonenavn og ressource gruppenavn eller ved hjælp af en zone objekt:

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
    $rs = Get-AzureRmDnsRecordSet -Name www –RecordType A -Zone $zone

`Get-AzureRmDnsRecordSet`Returnerer et lokalt objekt, der repræsenterer den post, der er oprettet i Azure DNS.

## <a name="list-record-sets"></a>Listen postsæt

Du kan også bruge`Get-AzureRmDnsRecordSet` til listen postsæt, hvis du udelader de *– navn* og/eller *– RecordType* parametre.

### <a name="to-list-all-record-sets"></a>Liste over alle postsæt

I dette eksempel returnerer alle poster sæt, uanset navn eller posttype:

    $list = Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

### <a name="to-list-record-sets-of-a-given-record-type"></a>På listen post sæt af en given posttype

I dette eksempel returnerer alle poster sæt, der svarer til den angivne posttype. Postsættet, der er returneres i dette tilfælde "En" poster:

    $list = Get-AzureRmDnsRecordSet –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

Zonen kan angives ved hjælp af enten *– ZoneName* *– ResourceGroupName* parametrene og (som vist) eller ved at angive en zone objekt:

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
    $list = Get-AzureRmDnsRecordSet -Zone $zone

## <a name="add-a-record-to-a-record-set"></a>Tilføje en post til et postsæt

Du kan føje poster til postsæt ved hjælp af den `Add-AzureRmDnsRecordConfig` cmdlet. Dette er en offline handling. Kun det lokale objekt, der repræsenterer den postsæt ændres.

Parametre for at tilføje poster i et postsæt varierer afhængigt af typen af postsættet. For eksempel, når du bruger et postsæt af typen "A", du kan kun angive poster med parameteren for *-IPv4Address*.

Flere poster kan føjes til hver post, der er angivet af flere opkald til `Add-AzureRmDnsRecordConfig`. Du kan føje op til 20 poster til en hvilken som helst postsæt. Dog postsæt af typen "CNAME" kan indeholde mindst én post, og et postsæt må ikke indeholde to identiske poster. Tom postsæt (med nul records) kan oprettes, men vises ikke i Azure DNS-navneservere.

Når den postsæt indeholder den ønskede samling af poster, skal du bekræfte den ved hjælp af den `Set-AzureRmDnsRecordSet` cmdlet. Når et postsæt har været anvendt, erstatter den eksisterende post, der er angivet i Azure DNS.

### <a name="to-create-an-a-record-set-with-a-single-record"></a>Oprette en A-post, der er konfigureret med en enkelt post

    $rs = New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
    Set-AzureRmDnsRecordSet -RecordSet $rs

Rækkefølgen af handlinger til at oprette en post kan også være *pipes*, hvilket betyder, at du overfører objektet postsæt ved at bruge pipe-tegnet i stedet for at sende det som en parameter. Eksempel:

    New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Add-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### <a name="additional-record-type-examples"></a>Yderligere posttype eksempler

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]

## <a name="modify-existing-record-sets"></a>Ændre eksisterende postsæt

Trin til at redigere en eksisterende postsæt ligner de trin, du tager, når du opretter poster. Rækkefølgen af operationer er som følger:

1.  Hente den eksisterende post, der er angivet ved hjælp af `Get-AzureRmDnsRecordSet`.

2.  Ændre den post, der er angivet af enten tilføje poster, fjerne poster, ændre parametrene post, eller ændre posten Angiv tid levetid (TTL). Dette er en offline handling. Kun det lokale objekt, der repræsenterer den postsæt ændres.

3.  Foretag dine ændringer ved hjælp af den `Set-AzureRmDnsRecordSet` cmdlet. Det erstatter den eksisterende post, der er angivet i Azure DNS.


### <a name="to-update-a-record-in-an-existing-record-set"></a>Opdatere en post i en eksisterende postsæt

I dette eksempel skal ændre vi IP-adressen på en eksisterende "En" post:

    $rs = Get-AzureRmDnsRecordSet -name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    $rs.Records[0].Ipv4Address = "134.170.185.46"
    Set-AzureRmDnsRecordSet -RecordSet $rs

Den `Set-AzureRmDnsRecordSet` cmdlet bruger etag Kontroller til at sikre, at samtidige ændringer ikke tilsidesættes. Brug den *-overskrive* flag for at skjule kontrollen. Se Få mere at vide [om etags og mærker](dns-getstarted-create-dnszone.md#tagetag).

### <a name="to-modify-an-soa-record"></a>Ændre en SOA post

Du kan ikke tilføje eller fjerne poster fra oprettes automatisk SOA postsættet i zone af toppen (navn = "@"). Du kan ændre nogen af parametrene i posten SOA (undtagen "Host"), og posten set TTL.

I følgende eksempel vises, hvordan du kan ændre egenskaben *mail* for SOA posten:

    $rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    $rs.Records[0].Email = "admin.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Ændre navneserverposter i zone af toppen

Du kan ikke tilføje, fjerne eller ændre poster i den automatisk oprettede NS postsæt i zone af toppen (navn = "@"). Der er den eneste ændring, der er tilladt at ændre postsæt TTL.

I følgende eksempel viser, hvordan du kan ændre egenskaben TTL for NS postsæt:

    $rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    $rs.Ttl = 300
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="to-add-records-to-an-existing-record-set"></a>Føje poster til en eksisterende postsæt

I dette eksempel skal tilføje vi to ekstra MX-poster til de eksisterende post:

    $rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
    Set-AzureRmDnsRecordSet -RecordSet $rs

## <a name="remove-a-record-from-an-existing-record-set"></a>Fjerne en post fra en eksisterende postsæt

Poster kan fjernes fra en post, der er angivet ved hjælp af `Remove-AzureRmDnsRecordConfig`. Den post, der fjernes skal være en identisk værdi med en eksisterende post på tværs af alle parametre. Ændringer skal være registreret ved hjælp af `Set-AzureRmDnsRecordSet`.

Fjerne den sidste post fra et postsæt sletter ikke den postsæt. Yderligere oplysninger finder du i [slette en postsæt](#delete-a-record-set) nedenfor.


    $rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
    Set-AzureRmDnsRecordSet -RecordSet $rs

Rækkefølgen af handlinger til at fjerne en post fra et postsæt kan også være pipes, hvilket betyder, at du overfører objektet postsæt ved at bruge pipe-tegnet i stedet for at sende det som en parameter. Eksempel:

    Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### <a name="remove-an-aaaa-record-from-a-record-set"></a>Fjerne en AAAA post fra et postsæt

    $rs = Get-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-a-cname-record-from-a-record-set"></a>Fjerne en CNAME-post fra et postsæt

Da en CNAME-postsæt kan indeholde mindst én post, får Hvis du fjerner posten en tom postsæt.

    $rs =  Get-AzureRmDnsRecordSet -name "test-cname" -RecordType CNAME -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-an-mx-record-from-a-record-set"></a>Fjerne en MX-post fra et postsæt

    $rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-an-ns-record-from-record-set"></a>Fjerne en NS-post postsæt

    $rs = Get-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-an-srv-record-from-a-record-set"></a>Fjerne en SRV-post fra et postsæt

    $rs = Get-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-a-txt-record-from-a-record-set"></a>Fjerne en TXT-post fra et postsæt

    $rs = Get-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
    Set-AzureRmDnsRecordSet -RecordSet $rs

## <a name="delete-a-record-set"></a>Slette et postsæt

Postsæt kan slettes ved hjælp af den `Remove-AzureRmDnsRecordSet` cmdlet. Du kan ikke slette SOA og NS-post angiver i zone af toppen (navn = "@") , der er oprettet automatisk, når zonen blev oprettet. De slettes automatisk, hvis zonen slettes.

Brug en af følgende tre metoder til at fjerne et postsæt:

### <a name="specify-all-the-parameters-by-name"></a>Angive alle parametre ved navn

Det valgfri *-kraft* parameter kan benyttes til at skjule bekræftelsesmeddelelsen.

    Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]


### <a name="specify-the-record-set-by-name-and-type-and-specify-the-zone-by-object"></a>Angiv postsæt ved navn og type, og Angiv zonen af objekt

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]

### <a name="specify-the-record-set-by-object"></a>Angiv den post, der er angivet af objekt

    $rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordSet –RecordSet $rs [-Overwrite] [-Force]

Når du angiver postsættet ved hjælp af et objekt, kan det etag kontrol for at sikre, at samtidige ændringer ikke slettes. Det valgfri *-overskrive* flaget undertrykker kontrollen. Se [Etags og de mærker,](dns-getstarted-create-dnszone.md#tagetag) du kan finde flere oplysninger.

Objektet postsæt kan også pipes i stedet for de sendes som en parameter:

    Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordSet [-Overwrite] [-Force]

## <a name="next-steps"></a>Næste trin

Se [Oversigt over Azure DNS](dns-overview.md)kan finde flere oplysninger om Azure DNS. Finde oplysninger om automatisere DNS, kan du se [oprette DNS-zoner og post angiver ved hjælp af .NET SDK](dns-sdk.md).

Se, [hvordan du administrerer modsat DNS-poster for dine tjenester, ved hjælp af PowerShell](dns-reverse-dns-record-operations-ps.md)kan finde flere oplysninger om modsat DNS-poster.
