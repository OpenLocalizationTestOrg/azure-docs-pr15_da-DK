<properties
   pageTitle="Mutiple VIPs til en skybaseret tjeneste"
   description="Oversigt over multiVIP og hvordan du opretter flere VIPs på en skybaseret tjeneste"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="configure-multiple-vips-for-a-cloud-service"></a>Konfigurere flere VIPs til en skybaseret tjeneste

Du kan få adgang til Azure skytjenester via offentlige internettet ved hjælp af en IP-adresse, der leveres af Azure. Denne offentlige IP-adresse er kaldes en VIP (virtuelle IP) siden den sammenkædes med Azure justering af belastning, og ikke Virtual Machine (VM) forekomster i skytjenesten. Du kan få adgang til en hvilken som helst VM forekomst i en skybaseret tjeneste ved hjælp af en enkelt VIP.

Der er dog scenarier, hvor du skal muligvis mere end ét VIP som en post, peg på den samme skybaseret tjeneste. Skybaseret tjeneste kan for eksempel være vært for flere websteder, der kræver SSL-forbindelse ved hjælp af standardporten for 443, som er vært for hvert websted til en anden kunde, eller lejer. I dette scenarie skal du have en anden offentlige modstående IP-adresse for hvert websted. Diagrammet nedenfor illustrerer en typisk med flere lejer web vært for behov for flere SSL-certifikater på den samme offentlige port.

![Flere VIP SSL-scenario](./media/load-balancer-multivip/Figure1.png)

I det foregående eksempel alle VIPs bruge den samme offentlige port (443) og trafik omdirigeres til en eller flere Indlæs afstemmes FOS på en entydig private port til den interne IP-adresse til skyen-tjenesten, der er vært for alle webstederne.

>[AZURE.NOTE] En anden situation, der kræver brug af flere VIPs vært for flere SQL AlwaysOn tilgængelighed gruppe lyttere på det samme sæt af virtuelle maskiner.

VIPs er dynamiske som standard, hvilket betyder, at den faktiske IP-adresse, der er tildelt til skytjenesten måske ændres med tiden. Hvis du vil forhindre, at, der sker, kan du reservere en VIP til din tjeneste. Hvis du vil vide mere om reserveret VIPs skal du se [Reserveret offentlige IP-adresse](../virtual-network/virtual-networks-reserved-public-ip.md).

>[AZURE.NOTE] Se [IP-adresse priser](https://azure.microsoft.com/pricing/details/ip-addresses/) for oplysninger om priser på VIPs og reserveret IP'er.

Du kan bruge PowerShell til at bekræfte VIPs anvendes af din skytjenester, samt tilføje og fjerne VIPs, knytte en VIP til et slutpunkt og konfigurere justering af belastning på en bestemt VIP.

## <a name="limitations"></a>Begrænsninger

På nuværende tidspunkt er flere VIP funktionalitet begrænset til følgende scenarier:

- **Kun IaaS**. Du kan kun aktivere flere VIP til skytjenester, der indeholder FOS. Du kan ikke bruge flere VIP i PaaS scenarier med rolle forekomster.
- **Kun PowerShell**. Du kan kun administrere flere VIP ved hjælp af PowerShell.

Disse begrænsninger, der er midlertidige og kan blive ændret, når som helst. Sørg for at besøge igen denne side for at bekræfte fremtidige ændringer.


## <a name="how-to-add-a-vip-to-a-cloud-service"></a>Sådan føjer du en VIP til en skybaseret tjeneste

Hvis du vil føje en VIP til din tjeneste, skal du køre følgende kommando for PowerShell:

    Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService

Denne kommando viser et resultat, der ligner følgende eksempel:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>Sådan fjernes en VIP fra en skybaseret tjeneste

For at fjerne VIP føjet til din tjeneste i det foregående eksempel skal du køre følgende PowerShell-kommando:

    Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService

>[AZURE.IMPORTANT] Du kan kun fjerne en VIP, hvis der er nogen slutpunkter, der er knyttet til den.

## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>Hvordan du kan hente VIP oplysninger fra en skybaseret tjeneste

For at hente de VIPs, der er knyttet til en skybaseret tjeneste skal du køre følgende PowerShell-script:

    $deployment = Get-AzureDeployment -ServiceName myService
    $deployment.VirtualIPs

Scriptet viser et resultat, der ligner følgende eksempel:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

I dette eksempel har skytjenesten 3 VIPs:

- **Vip1** er standard VIP, du kender, fordi værdien for IsDnsProgrammedName er indstillet til sand.
- **Vip2** og **Vip3** bruges ikke, da de ikke har en IP-adresser. De kan kun bruges, når du knytter et slutpunkt til VIP.

>[AZURE.NOTE] Dit abonnement, kun debiteres for ekstra VIPs, når de er knyttet til et slutpunkt. Se [IP-adresse priser](https://azure.microsoft.com/pricing/details/ip-addresses/)kan finde flere oplysninger om priser.

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>Hvordan du kan knytte en VIP til et slutpunkt

For at knytte en VIP på en skybaseret tjeneste til et slutpunkt, skal du køre følgende PowerShell-kommando:

    Get-AzureVM -ServiceName myService -Name myVM1 `
  	| Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 `
  	| Update-AzureVM

Kommandoen opretter et slutpunkt, der er knyttet til VIP kaldet *Vip2* på port *80*og links den til VM med navnet *myVM1* i en skybaseret tjeneste med navnet *myService* ved hjælp af *TCP* på port *8080*.

Hvis du vil kontrollere konfigurationen, skal du køre følgende kommando for PowerShell:

    $deployment = Get-AzureDeployment -ServiceName myService
    $deployment.VirtualIPs

Output ligner i følgende eksempel:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         : 191.238.74.13
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>Hvordan du aktiverer justering af belastning på en bestemt VIP

Du kan knytte en enkelt VIP med flere virtuelle maskiner til formål justering af belastning. For eksempel, har du en skybaseret tjeneste med navnet *myService*og to virtuelle maskiner med navnet *myVM1* og *myVM2*. Og skybaseret tjeneste, har flere VIPs, en af dem med navnet *Vip2*. Hvis du vil sikre dig, der er al trafik til port *81* på *Vip2* ligeligt mellem *myVM1* og *myVM2* på port *8181*, køre følgende PowerShell-script:

    Get-AzureVM -ServiceName myService -Name myVM1 `
  	| Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet `
        -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe `
  	| Update-AzureVM

    Get-AzureVM -ServiceName myService -Name myVM2 `
  	| Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet `
        -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe `
  	| Update-AzureVM

Du kan også opdatere din justering af belastning for at bruge en anden VIP. Hvis du kører den PowerShell-kommando nedenfor, vil du ændre belastningsjustering indstillet til at bruge en VIP med navnet Vip1:

    Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1

## <a name="next-steps"></a>Næste trin

[Log analyser af Azure belastning](load-balancer-monitor-log.md)

[Oversigt over internettet modstående Indlæs belastningsjusteringstjenesten](load-balancer-internet-overview.md)

[Kom i gang på via justering af belastning internettet](load-balancer-get-started-internet-arm-ps.md)

[Oversigt over virtuelt netværk](../virtual-network/virtual-networks-overview.md)

[Reserverede IP-REST API'er](https://msdn.microsoft.com/library/azure/dn722420.aspx)