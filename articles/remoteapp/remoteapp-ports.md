
<properties
    pageTitle="Liste over porte og URL-adresser til whitelist til Azure RemoteApp installeret i virtuelle kundenetværket | Microsoft Azure"
    description="Få mere at vide, hvilke porte og URL-adresser skal du konfigurere til kommunikation via Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="mghosh1616"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="elizapo" />



# <a name="list-of-ports-and-urls-to-permit-access-for-azure-remoteapp-deployed-in-customer-virtual-network"></a>Liste over porte og URL-adresser til at tillade adgang til Azure RemoteApp installeret i kunde virtuelt netværk 

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Følgende gælder for Azure RemoteApp en skyen eller hybrid samling Hvis du installerer den i et virtuelt netværk (VNET). Læs [Oversigt over virtuelle netværk](../virtual-network/virtual-networks-overview.md)kan finde flere oplysninger om virtuelle netværk. Hvis du har oprettet en netværk-sikkerhedsgruppe (NSG), begrænse trafik til dit virtuelt netværksressourcer, som du har valgt til Azure RemoteApp, skal du kontrollere, at følgende er tilgængelige og tilladte gennem sikkerhedspolitikker på det virtuelle netværk. Yderligere oplysninger om netværk sikkerhedsgrupper, skal du læse [Hvad er en sikkerhedsgruppe netværk? (NSG)](../virtual-network/virtual-networks-nsg.md).

##  <a name="azure-remoteapp-subnet-needs-access-to-these-endpoints-and-urls"></a>Azure RemoteApp undernet skal have adgang til disse slutpunkter og URL-adresser: 
*   *. servicebus.windows.net
*    *. servicebus.net
*    https://*.RemoteApp.windwsazure.com  
*    https://www.RemoteApp.windowsazure.com 
*    https://*RemoteApp.windowsazure.com  
*    https://*.Core.Windows.NET  
*    Udgående: TCP: 443, TCP: 10101 10175 
*    Valgfri – UDP: 10201 10275  
 
## <a name="azure-remoteapp-clients-need-access-to-these-endpoints-and-urls"></a>Azure RemoteApp klienter skal have adgang til disse slutpunkter og URL-adresser: 

Med klienter mener jeg stationære computere, enheder osv., at folk skal bruge til at oprette forbindelse til de apps, der er implementeret i samlingen Azure RemoteApp.

-  https://telemetry.RemoteApp.windowsazure.com  
-  https://*.RemoteApp.windowsazure.com (valgfrit UDP-porte er for denne adresse) 
-  https://login.Windows.NET  
-  https://login.microsoftonline.com  
-  https://www.RemoteApp.windowsazure.com 
-  https://*.Core.Windows.NET  
-  Udgående: TCP: 443  
-  Valgfrit – UDP: 3391 