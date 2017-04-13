<properties
   pageTitle="Sådan administreres adgangskontrollister (ACLs) for slutpunkter ved hjælp af PowerShell"
   description="Lær, hvordan du administrerer ACLs med PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="how-to-manage-access-control-lists-acls-for-endpoints-by-using-powershell"></a>Sådan administreres adgangskontrollister (ACLs) for slutpunkter ved hjælp af PowerShell

Du kan oprette og administrere netværk adgangskontrollister (ACLs) for slutpunkter ved hjælp af Azure PowerShell eller i portalen administration. I dette emne finder du procedurer til ACL almindelige opgaver, du kan udføre ved hjælp af PowerShell. Du kan finde på listen over Azure PowerShell-cmdlet'er [Cmdletter til administration af Azure](http://go.microsoft.com/fwlink/?LinkId=317721). Finde flere oplysninger om ACLs [Hvad er et netværk listen ACL (Access Control)?](virtual-networks-acl.md). Hvis du vil administrere dine ACLs ved hjælp af portalen Management, skal du se, [hvordan du angive slutpunkter til en virtuel maskine](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).

## <a name="manage-network-acls-by-using-azure-powershell"></a>Administrere netværk ACLs ved hjælp af Azure PowerShell

Du kan bruge Azure PowerShell-cmdlet'er til at oprette, fjerne og konfigurere (angivet) netværk adgangskontrollister (ACLs). Vi har medtaget nogle få eksempler på nogle af de måder, du kan konfigurere en ACL ved hjælp af PowerShell.

Hvis du vil hente en komplet liste over ACL PowerShell-cmdletterne, kan du bruge et af følgende:

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>Oprette en netværk ACL med regler, der giver adgang fra en ekstern undernet

I eksemplet nedenfor illustrerer en metode til at oprette en ny ACL, der indeholder regler. Denne ACL derefter anvendes på et virtuelt slutpunkt. ACL reglerne i eksemplet nedenfor vil give adgang fra en ekstern undernet. For at oprette en ny netværk ACL med tilladelse regler for et remote undernet, skal du åbne en Azure PowerShell ISE. Kopiér og Indsæt scriptet under konfiguration af scriptet med dine egne værdier, og derefter køre scriptet.

1. Oprette det nye netværk ACL objekt.

        $acl1 = New-AzureAclConfig

1. Angive en regel, der giver adgang fra en ekstern undernet. I eksemplet nedenfor kan du angive regel *100* (som prioriteres over regel 200 eller nyere) for at give eksterne undernet *10.0.0.0/8* adgang til virtuelt slutpunktet. Erstat værdier med dine egne konfigurationskrav til. Navnet "SharePoint ACL config" skal erstattes med det fulde navn, du vil ringe til denne regel.

        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"

1. Gentag Cmdletten, erstatter værdierne med dine egne konfigurationskrav til efter flere regler. Sørg for at ændre reglen tal for at afspejle den rækkefølge, hvori du vil reglerne, der anvendes. Nederste regel antallet tilsidesætter det højeste tal.

        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"

1. Derefter skal du enten oprette et nyt slutpunkt (Tilføj) eller angive ACL til et eksisterende slutpunkt (angivet). I dette eksempel kan vi tilføje et nyt virtuelt slutpunkt kaldet "web" og opdatere virtuelt slutpunktet med ACL-indstillinger.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM

1. Derefter kombinere cmdlet'erne og køre scriptet. I dette eksempel skal ville de kombinerede cmdletter se således ud:

        $acl1 = New-AzureAclConfig
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "Sharepoint ACL config"
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        |Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        |Update-AzureVM

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>Fjerne en netværk ACL-regel, der giver adgang fra en ekstern undernet

I eksemplet nedenfor illustrerer en måde at fjerne en netværk ACL regel.  Hvis du vil fjerne en netværk ACL regel med tilladelse regler for et remote undernet, Åbn en Azure PowerShell ISE. Kopiér og Indsæt scriptet under konfiguration af scriptet med dine egne værdier, og derefter køre scriptet.

1. Første trin er at få objektet netværk ACL for virtuelt slutpunkt. Derefter skal du fjerne ACL-reglen. I dette tilfælde vi er ved at fjerne det som regel-ID. Dette fjerner kun regel-ID'ET 0 fra ACL. Det fjerner ikke objektet ACL fra virtuelt slutpunkt.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1

1. Derefter skal du anvende objektet netværk ACL til virtuelt slutpunktet og opdatere den virtuelle maskine.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>Fjerne en netværk ACL fra et virtuelt slutpunkt

I visse scenarier vil du fjerne et netværk ACL objekt fra et virtuelt slutpunkt. Åbn en Azure PowerShell ISE gør. Kopiér og Indsæt scriptet under konfiguration af scriptet med dine egne værdier, og derefter køre scriptet.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>Næste trin

[Hvad er et netværk listen ACL (Access Control)?](virtual-networks-acl.md)
