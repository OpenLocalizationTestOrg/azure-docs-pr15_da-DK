<properties
   pageTitle="Administrere DNS zones ved hjælp af PowerShell | Microsoft Azure"
   description="Du kan administrere DNS-zoner med Azure Powershell. Hvordan du kan opdatere, slette og oprette DNS-zoner på Azure DNS"
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

# <a name="how-to-manage-dns-zones-using-powershell"></a>Hvordan du administrerer DNS Zones ved hjælp af PowerShell

> [AZURE.SELECTOR]
- [Azure CLI](dns-operations-dnszones-cli.md)
- [PowerShell](dns-operations-dnszones.md)



I denne artikel viser dig, hvordan du administrerer dine DNS-zone ved hjælp af PowerShell. For at udføre disse trin, skal du installere den nyeste version af Azure ressourcestyring PowerShell-cmdlet'er (1.0 eller nyere). Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) kan finde flere oplysninger om installation af PowerShell-cmdlet'er.


## <a name="create-a-new-dns-zone"></a>Oprette en ny DNS-zone

Hvis du vil oprette en DNS-zone, skal du se [oprette en DNS-zone ved hjælp af PowerShell](dns-getstarted-create-dnszone.md).

## <a name="get-a-dns-zone"></a>Få en DNS-zone

Hvis du vil hente en DNS-zone, skal du bruge den `Get-AzureRmDnsZone` cmdlet. Denne handling returnerer en DNS-zone objekt, som svarer til en eksisterende zone i Azure DNS. Objektet indeholder data om zonen (såsom antallet af postsæt), men indeholder ikke post sæt sig selv.

    $zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

## <a name="list-dns-zones"></a>Listen DNS zones

Ved at udelade zone navnet fra `Get-AzureRmDnsZone`, du kan optælle alle zoner i en ressourcegruppe. Denne handling returnerer en matrix med zone objekter.

    $zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup

## <a name="update-a-dns-zone"></a>Opdatere en DNS-zone

Ændringer til en DNS-zone ressource kan foretages ved hjælp af `Set-AzureRmDnsZone`. Dette opdaterer ikke nogen af de DNS-post sæt i zonen (se, [hvordan du administrerer DNS-poster](dns-operations-recordsets.md)). Det bruges kun til at opdatere egenskaberne for zone ressourcen sig selv. Dette er i øjeblikket er begrænset til Azure ressourcestyring 'mærker' for zone ressourcen. Se [Etags og de mærker,](dns-getstarted-create-dnszone.md#Etags-and-tags) du kan finde flere oplysninger.

Benyt en af følgende to måder at opdatere DNS-zone:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Angiv den zone, ved hjælp af gruppen zone navn og en ressource

    Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]

### <a name="specify-the-zone-using-a-zone-object"></a>Angiv den zone, ved hjælp af et $zone objekt

Angiv den zone, ved hjælp af et $zone objekt fra `Get-AzureRmDnsZone`. Når du bruger `Set-AzureRmDnsZone` med en $zone objekt Etag kontrol bruges til at sikre samtidige ændringer ikke tilsidesættes. Du kan bruge det valgfri *-overskrive* Skift undertrykker kontrollen. Se [Etags og de mærker,](dns-getstarted-create-dnszone.md#Etags-and-tags) du kan finde flere oplysninger.


    $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
    <..modify $zone.Tags here...>
    Set-AzureRmDnsZone -Zone $zone [-Overwrite]


## <a name="delete-a-dns-zone"></a>Slette en DNS-Zone

DNS-zoner kan slettes ved hjælp af fjern AzureRmDnsZone cmdlet.

Før du sletter en DNS-zone i Azure DNS, skal du slette alle poster sæt, med undtagelse af de NS og SOA poster i roden af zonen, der er oprettet automatisk, når zonen blev oprettet.

Benyt en af følgende to måder at fjerne en DNS-zone:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Angiv den zone, ved hjælp af zonenavn og ressource gruppenavn

Denne handling er en valgfri *-kraft* parameteren som undertrykker prompten for at bekræfte, at du vil fjerne DNS-zonen.

    Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]

### <a name="specify-the-zone-using-a-zone-object"></a>Angiv den zone, ved hjælp af et $zone objekt

Angiv den zone, ved hjælp af et $zone objekt fra `Get-AzureRmDnsZone`. Denne handling er en valgfri *-kraft* parameteren som undertrykker prompten for at bekræfte, at du vil fjerne DNS-zonen. Som med `Set-AzureRmDnsZone`, der angiver den zone, ved hjælp af et $zone objekt gør det muligt for Etag kontrol for at sikre samtidige ændringer ikke slettes. <BR>
Det valgfri *-overskrive* flaget undertrykker kontrollen. Se [Etags og de mærker,](dns-getstarted-create-dnszone.md#Etags-and-tags) du kan finde flere oplysninger.

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsZone -Zone $zone [-Force] [-Overwrite]



Objektet zone kan også pipes i stedet for de sendes som en parameter:

    Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone [-Force] [-Overwrite]

## <a name="next-steps"></a>Næste trin

Når du har oprettet en DNS-zone, oprette [post sæt og poster](dns-getstarted-create-recordset.md) for at starte navnekonflikter for dit domæne på internettet.