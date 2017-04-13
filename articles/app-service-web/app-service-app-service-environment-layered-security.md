<properties 
    pageTitle="Lagdelte Sikkerhedsarkitekturen i App Service miljøer" 
    description="Implementere et lag Sikkerhedsarkitekturen i App-tjenesten miljøer." 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/30/2016" 
    ms.author="stefsch"/>   

# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Implementere et lag Sikkerhedsarkitekturen i App Service miljøer

## <a name="overview"></a>Oversigt ##
 
Da App Service miljøer give en isolerede runtime-miljø, der er implementeret i et virtuelt netværk, kan udviklere kan oprette et lag sikkerhedsarkitekturen give forskellige niveauer af netværksadgang for hvert niveau af fysisk-program.

En fælles ønske om er at skjule API back-ender fra generel adgang til internettet, og Tillad kun API'er skal ringes op ved før webapps.  [Netværk sikkerhedsgrupper (NSGs)] [ NetworkSecurityGroups] kan bruges på undernet, der indeholder App Service miljøer til at begrænse offentlig adgang til API-programmer.

Diagrammet nedenfor viser et eksempel på arkitektur med en WebAPI baseret app, der er installeret på en App Service-miljø.  Tre separate web app forekomster, installeres på tre separate App Service miljøerne, foretage back end-opkald til den samme WebAPI-app.

![Grundlæggende arkitektur][ConceptualArchitecture] 

Grøn plustegnene angiver, sikkerhedsgruppen netværk på det undernet, der indeholder "apiase" gør det indgående opkald fra før web apps, som godt opkald fra sig selv.  Den samme netværk sikkerhedsgruppe afviser dog eksplicit adgang til generelle indgående trafik fra internettet. 

Resten af dette emne fører gennem de trin, der er behov for at konfigurere sikkerhedsgruppen netværk på det undernet, der indeholder "apiase".

## <a name="determining-the-network-behavior"></a>Afgøre funktionsmåde netværk ##
Hvis du vil vide, hvilke netværk sikkerhedsregler er det er nødvendigt, skal du bestemme, hvilke netværksklienter, tilladelse til at nå det App Service-miljø med API-app, og hvilke klienter vil blive blokeret.

Siden [netværk sikkerhedsgrupper (NSGs)] [ NetworkSecurityGroups] anvendes på undernet, og App-tjenesten miljøer er implementeret i undernet, de regler, der er indeholdt i en NSG gælder for **alle** apps, der kører på en App Service-miljø.  Bruger eksempel arkitekturen til denne artikel, når en netværk sikkerhedsgruppe anvendes på det undernet, der indeholder "apiase", beskyttes alle apps, der kører på "apiase" App servicemiljø af det samme sæt af sikkerhedsregler. 

- **Bestemme udgående IP-adressen på mod strømmen ringer:**  Hvad er IP-adresse eller adressen på de foregående ringer?  Disse adresser skal have eksplicit adgang i NSG.  Da opkald mellem App Service miljøer betragtes som "Internet" opkald, betyder dette udgående IP-adresse, der er tildelt til hver af de tre mod strømmen App Service miljøer skal have adgang i NSG for "apiase" undernettet.   Få mere at vide ved fastlæggelse af udgående IP-adressen til apps, der kører i et App-tjenesten miljø i afsnittet [Netværksarkitektur] [ NetworkArchitecture] oversigt artikel.
- **Skal appen back end-API til at ringe til sig selv?**  Et nogle gange overset og lille punkt er dette scenario, hvor back-end-program skal kalde sig selv.  Hvis et back end-API-program på et App-tjenesten miljø skal ringe til selve, dette fortolkes også som en "" Internetopkald.  Dette kræver, at du tillade adgang fra "apiase" App servicemiljø samt udgående IP-adresse i eksempel arkitekturen.

## <a name="setting-up-the-network-security-group"></a>Konfiguration af sikkerhedsgruppen netværk ##
Når sæt af udgående IP-adresser er blevet konstateret, er næste trin at opbygge en sikkerhedsgruppe netværk.  Netværk sikkerhedsgrupper kan oprettes til begge ressourcestyring baseret virtuelle netværk, samt klassisk virtuelle netværk.  Eksemplerne nedenfor viser, oprette og konfigurere en NSG i et klassisk virtuelt netværk ved hjælp af Powershell.

For eksempel arkitektur findes miljøerne i Syd Central os, så der oprettes en tom NSG i det pågældende område:

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

Først en eksplicit tillade regel føjes til Azure management infrastrukturen som angivet i artiklen på [indgående trafik] [ InboundTraffic] for App-tjenesten miljøer.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
    
Dernæst skal to regler føjes til tillade HTTP og HTTPS opkald fra den første mod strømmen App Service-miljø ("fe1ase").

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Skylles, og Gentag for de andet og tredje mod strømmen App Service miljøer ("fe2ase" og "fe3ase").

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
    
    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Endelig give adgang til API back end-App servicemiljø udgående IP-adresse, så den kan ringe tilbage til selve.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Ingen andre netværk sikkerhed-regler skal konfigureres, fordi alle NSG har et sæt standardregler, der blokerer for indgående adgang fra internettet som standard.

Den komplette liste over regler i sikkerhedsgruppen netværk er vist nedenfor.  Bemærk, hvordan den sidste regel, som er fremhævet, blokerer for indgående adgang fra alle ringer end dem, der har fået eksplicit adgang.

![NSG konfiguration][NSGConfiguration] 

Det sidste trin er at anvende NSG til det undernet, der indeholder "apiase" App Service-miljø.  

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

Med den NSG, der er anvendt til undernettet, er kun de tre mod strømmen App Service miljøer, og det App Service-miljø, der indeholder API back-end, tilladelse til at ringe op til "apiase"-miljøet.


## <a name="additional-links-and-information"></a>Flere Links og oplysninger ##
Alle artikler og hvordan-til er for App-tjenesten miljøer er tilgængelige i [vigtige oplysninger om programmet Service miljøer](../app-service/app-service-app-service-environments-readme.md).

Oplysninger om [netværk sikkerhedsgrupper](../virtual-network/virtual-networks-nsg.md). 

Forstå [udgående IP-adresser] [ NetworkArchitecture] og App-tjenesten miljøer.

[Netværk porte] [ InboundTraffic] bruges af App Service miljøer.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[InboundTraffic]:  https://azure.microsoft.com/en-us/documentation/articles/app-service-app-service-environment-control-inbound-traffic/

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
