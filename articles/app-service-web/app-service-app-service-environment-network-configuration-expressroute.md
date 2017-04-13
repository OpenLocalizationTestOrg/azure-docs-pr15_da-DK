<properties 
    pageTitle="Oplysninger om konfiguration af netværk til at arbejde med Express rute" 
    description="Oplysninger om konfiguration af netværk til at køre App Service miljøer i et virtuelt Network forbindelse til et ExpressRoute kredsløb." 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="nirma" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="stefsch"/>   

# <a name="network-configuration-details-for-app-service-environments-with-expressroute"></a>Oplysninger om konfiguration af netværk for App Service-miljøer med ExpressRoute 

## <a name="overview"></a>Oversigt ##
Kunderne kan slutte en [Azure ExpressRoute] [ ExpressRoute] kredsløb til deres virtuelle netværksinfrastruktur, og som derfor forlænge deres lokale netværk til Azure.  Et App Service-miljø kan oprettes i et undernet med dette [virtuelt netværk] [ virtualnetwork] infrastruktur.  Apps, der kører på App-tjenesten miljøet kan derefter oprette sikre forbindelser til back end-ressourcer tilgængelige kun via ExpressRoute forbindelsen.  

Kan oprettes et App-tjenesten miljø i **enten** en Azure ressourcestyring virtuelt netværk, **eller** en klassisk installation modellere virtuelt netværk.  Med en seneste ændring, der har foretaget i juni 2016, kan også nu være installeret ASEs til virtuelle netværk, der bruger enten offentlige adresseområder eller RFC1918 adresse mellemrum (dvs. private adresser). 

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="required-network-connectivity"></a>Påkrævet netværksforbindelsen ##
Der er krav til netværksforbindelser for App-tjenesten miljøer, der ikke kan har opfyldt i et virtuelt netværk, der er knyttet til en ExpressRoute.  App Service miljøer kræver alle af følgende for at kunne fungere korrekt:


-  Udgående netværksforbindelse til Azure-lager slutpunkterne på verdensplan på begge port 80 og 443.  Dette omfatter slutpunkter, der er placeret i det samme område, som App servicemiljø samt lagerplads slutpunkter placeret i **andre** Azure områder.  Azure lagerplads slutpunkter løse under følgende DNS-domæner: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* og *file.core.windows.net*.  
-  Udgående netværksforbindelse til tjenesten Azure filer på port 445.
-  Udgående netværksforbindelse til Sql DB slutpunkter, der er placeret i det samme område som App Service-miljø.  SQL DB slutpunkter løse under følgende domæne: *database.windows.net*.  Dette kræver åbne adgang til porte 1433, 11000 11999 og 14000 14999.  Yderligere oplysninger finder du [i denne artikel for brug af Sql Database version 12-port](../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).
-  Udgående netværksforbindelse til Azure management plan slutpunkter (både ASM og ARM slutpunkter).  Dette omfatter udgående forbindelse til både *management.core.windows.net* og *management.azure.com*. 
-  Udgående netværksforbindelse til *ocsp.msocsp.com*, *mscrl.microsoft.com* og *crl.microsoft.com*.  Det er nødvendigt at understøtte SSL funktionalitet.
-  Konfigurationen af DNS for det virtuelle netværk skal kunne løse alle slutpunkter og domæner, der er nævnt i de tidligere punkter.  Hvis disse slutpunkter ikke kan løses, App servicemiljø oprettelse af forsøg mislykkes, og eksisterende App Service-miljøer, markeres som beskadiget.
-  Udgående adgang via port 53 er påkrævet for kommunikation med DNS-servere.
-  Hvis der findes en brugerdefineret DNS-server på anden ende af en VPN-gateway, skal DNS-serveren nås fra det undernet, der indeholder det App Service-miljø. 
-  Udgående netværksstien kan ikke sendes via interne virksomhedens proxyer og kan det være kraft tunnelført til en lokal installation.  Hvis du gør det ændres effektive NAT-adressen for udgående netværkstrafik fra App Service-miljø.  Ændre NAT-adressen på et App-tjenesten miljø udgående netværkstrafik medfører connectivity mislykkede forsøg på at mange af de slutpunkter, der er nævnt ovenfor.  Dette resulterer i mislykkedes App servicemiljø oprettelse af forsøg samt tidligere sund App Service miljøer der er markeret som sikre.  
-  Indgående netværksadgang til nødvendige porte til App-tjenesten miljøer må som beskrevet i denne [artikel][requiredports].

DNS-krav kan opfyldes ved at sikre en gyldig DNS-infrastrukturen er konfigureret og vedligeholdes for det virtuelle netværk.  Hvis DNS-konfigurationen ændres til en eller anden grund efter et App-tjenesten miljø er blevet oprettet, kan udviklere tvinge en App-tjenesten miljø for at fortsætte efter den nye DNS-konfiguration.  Udløse en rullende miljø genstart ved hjælp af ikonet "Genstart" findes i øverst del af bladet App servicemiljø management i [Azure portal] [ NewPortal] medfører miljø at fortsætte efter den nye DNS-konfiguration.

Indgående netværk access krav kan være opfyldt, ved at konfigurere en [netværk sikkerhedsgruppe] [ NetworkSecurityGroups] på App-tjenesten miljø undernet at give den nødvendige adgang, som beskrevet i denne [artikel][requiredports].

## <a name="enabling-outbound-network-connectivity-for-an-app-service-environment"></a>Aktivere udgående netværksforbindelsen for en App Service-miljø##
Som standard anmelder et nyoprettet ExpressRoute kredsløb en standardrute, der tillader udgående forbindelse til internettet.  Med denne konfiguration kunne et App-tjenesten miljø oprette forbindelse til andre Azure slutpunkter.

En almindelig kunde konfiguration er dog til at definere deres egne standardrute (0.0.0.0/0) som Fremtvinger udgående Internettrafik til i stedet dataflow i det lokale miljø.  Denne trafikken sideskift altid App Service-miljøer, fordi den udgående trafik er enten blokerede lokalt, eller NAT ville gøre til en ukendt sæt adresser, der ikke længere arbejder med forskellige Azure slutpunkter.

Løsningen er at definere en (eller flere) brugerdefinerede omdirigerer (UDRs) på det undernet, der indeholder det App Service-miljø.  En UDR definerer undernet-specifikke omdirigerer, der er accepteret i stedet for standardruten.

Hvis det er muligt, anbefales det at bruge følgende konfiguration:

- ExpressRoute konfigurationen anmelder 0.0.0.0/0, og som standard kraft tunnelfører alle udgående trafik i det lokale miljø.
- UDR anvendt til det undernet, der indeholder det App servicemiljø definerer 0.0.0.0/0 med en næste hop type Internet (et eksempel på dette er længere ned i denne artikel).

Kombinerede effekten af at disse trin er, at niveauet undernet UDR vil tilsidesætter den ExpressRoute tvunget tunnelføring således sikre udgående forbindelse til internettet fra App Service-miljø.

> [AZURE.IMPORTANT] Omdirigerer defineret i en UDR **skal** være specifik nok til at tilsidesætter alle omdirigerer meddeles af ExpressRoute konfigurationen.  Eksemplet herunder bruger adresseområde generelle 0.0.0.0/0 og således kan potentielt ved et uheld tilsidesættes ved at distribuere reklamer ved hjælp af mere specifikke adresseområder.
>
>App-tjenesten miljøer, der ikke understøttes med ExpressRoute konfigurationer, **kryds-annoncere omdirigerer fra offentlige peering stien til den private peering sti**.  ExpressRoute konfigurationer, der har konfigureret, offentlige peering modtager distribuere reklamer fra Microsoft for en stor mængde Microsoft Azure IP-adresseområder.  Hvis disse adresseområder er tværs vises på private peering stien, er betyder, at alle udgående netværkspakker fra App servicemiljø undernet er gældende tunnelført til en kundes lokale netværksinfrastruktur.  Dette netværk flow understøttes ikke i øjeblikket i App-tjenesten miljøer.  En løsning på dette problem er at stoppe tværs reklame omdirigerer fra offentlige peering stien til den private peering sti.

Baggrundsoplysninger om brugerdefinerede omdirigerer findes i denne [Oversigt][UDROverview].  

Få at vide om oprettelse og konfiguration af brugerdefinerede omdirigerer findes i denne [Vejledningerne til][UDRHowTo].

## <a name="example-udr-configuration-for-an-app-service-environment"></a>Eksempel på konfiguration af UDR for en App Service-miljø ##

**Forudsætninger**

1. Installer Azure Powershell fra [Azure Downloads-siden] [ AzureDownloads] (dateret juni 2015 eller nyere).  Under "Kommandolinjen værktøjer" er der et link af typen "Installer" under "Windows Powershell", vil installere de seneste Powershell-cmdletter.

2. Det anbefales, at der oprettes et entydigt undernet med udelt adgang ved et App Service-miljø.  Dette sikrer, at de UDRs, der er anvendt til undernettet kun åbnes udgående trafik for App-tjenesten-miljø.
3. **Vigtigt**: kan ikke installere App Service-miljø indtil **efter** de følgende trin til konfiguration følges.  Dette sikrer, at udgående netværksforbindelsen er tilgængelig, før du forsøger at installere en App Service-miljø.

**Trin 1: Oprette en navngivet distribuere tabel**

Følgende kodestykke opretter en distribution tabel med navnet "DirectInternetRouteTable" i området Vest os Azure:

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**Trin 2: Oprette en eller flere omdirigerer i rutetabellen**

Du skal føje en eller flere omdirigerer til rutetabellen for at aktivere udgående forbindelse til internettet.  

Den anbefalede fremgangsmåde til at konfigurere udgående adgang til internettet er at definere en rute for 0.0.0.0/0 som vist nedenfor.
  
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet

Husk, 0.0.0.0/0 er en bred adresseområde, og som sådan, tilsidesættes af mere specifikke adresseområder meddeles af ExpressRoute.  Hvis du vil igen forbedrer tidligere anbefaling, skal en UDR med en 0.0.0.0/0 rute bruges sammen med en ExressRoute konfiguration, der kun anmelder 0.0.0.0/0 samt. 

Du kan hente en omfattende og opdaterede liste over CIDR områder bruges af Azure som et alternativ.  Den XML-fil, der indeholder alle Azure IP-adresseområder er tilgængelige fra [Microsoft Download Center][DownloadCenterAddressRanges].  

Bemærk via, at disse områder ændrer sig med tiden, og som derfor kræver periodiske manuelle opdateringer til de brugerdefinerede omdirigerer at holde synkroniserede.  Også, skal du "opsummere" Azure IP-adresseområder til at passe ind i grænsen 100 rute, men vær opmærksom, UDR defineret omdirigerer skal være mere specifik end omdirigerer meddeles da der er et standard øvre grænse for 100 omdirigerer i en enkelt UDR, af din ExpressRoute.  


**Trin 3: Tilknytte rutetabellen til det undernet, der indeholder det App Service-miljø**

Det sidste trin i konfigurationen er at knytte rutetabellen til undernettet hvor App Service miljøet installeres.  Følgende kommando knytter "DirectInternetRouteTable" til den "ASESubnet", til sidst skal indeholde en App Service-miljø.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**Trin 4: Sidste trin**

Når rutetabellen er bundet til undernettet, anbefales det at først teste og bekræfte den tilsigtede virkning.  Installere en virtuel maskine til undernettet og Bekræft, f.eks.:


- Udgående trafik til både Azure og ikke Azure slutpunkter nævnt tidligere i denne artikel **ikke** er der flyder ned ExpressRoute kredsløb.  Det er meget vigtigt at bekræfte denne funktionsmåde, da Hvis udgående trafik fra undernettet er stadig bliver tvunget tunnelført lokalt, App servicemiljø altid kan ikke oprettes. 
- DNS-opslag for slutpunkterne nævnt tidligere er løse alle korrekt. 

Når ovenstående trin er bekræftet, skal du slette den virtuelle maskine, fordi undernettet skal "tom" på det tidspunkt App Service miljøet er oprettet.
 
Fortsæt med at oprette et App-tjenesten miljø!

## <a name="getting-started"></a>Kom godt i gang
Alle artikler og hvordan-til er for App-tjenesten miljøer er tilgængelige i [vigtige oplysninger om programmet Service miljøer](../app-service/app-service-app-service-environments-readme.md).

For at komme i gang med App-tjenesten miljøer skal du se [Introduktion til App Service-miljø][IntroToAppServiceEnvironment]

Du kan finde flere oplysninger om Azure App Service platformen, se [Azure App Service][AzureAppService].

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureDownloads]: http://azure.microsoft.com/en-us/downloads/ 
[DownloadCenterAddressRanges]: http://www.microsoft.com/download/details.aspx?id=41653  
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[NewPortal]:  https://portal.azure.com
 

<!-- IMAGES -->
