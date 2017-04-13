<properties 
    pageTitle="Sådan kan du kontrollere indgående trafik til et App Service-miljø" 
    description="Få mere at vide om, hvordan du konfigurerer netværk sikkerhedsregler for at styre indgående trafik til et App-tjenesten miljø." 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/02/2016" 
    ms.author="stefsch"/>   

# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Sådan kan du kontrollere indgående trafik til et App Service-miljø

## <a name="overview"></a>Oversigt ##
Kan oprettes et App-tjenesten miljø i **enten** en Azure ressourcestyring virtuelt netværk, **eller** en klassisk installation modellere [virtuelt netværk][virtualnetwork].  Et nyt virtuelt netværk og nye undernet kan defineres på det tidspunkt, der oprettes et App Service-miljø.  Du kan også kan et App Service-miljø oprettes i et eksisterende virtuelt netværk og allerede eksisterende undernet.  Med en seneste ændringer i juni 2016 kan nu være installeret ASEs til virtuelle netværk, der bruger enten offentlige adresseområder eller RFC1918 adresse mellemrum (dvs. private adresser).  Få mere at vide om oprettelse af et App-tjenesten miljø se [hvordan du kan oprette en App servicemiljø][HowToCreateAnAppServiceEnvironment].

Et App-tjenesten miljø skal altid oprettes i et undernet, fordi et undernet indeholder netværk rammen som kan bruges til at låse indgående trafik bag mod strømmen enheder og tjenester, så HTTP og HTTPS trafik besvares kun fra bestemte før IP-adresser.

Indgående og udgående netværkstrafik på et undernet styres ved hjælp af en [netværk sikkerhedsgruppe][NetworkSecurityGroups]. Styre indgående trafik kræver oprette netværk sikkerhedsregler i en sikkerhedsgruppe netværk, og derefter tildele netværkssikkerheden gruppere det undernet, der indeholder det App Service-miljø.

Når en netværk sikkerhedsgruppe er tildelt til et undernet, er tilladte/blokerede baseret på Tillad indgående trafik til apps i App-tjenesten miljø, og nægte regler, der er defineret i sikkerhedsgruppen netværk.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="network-ports-used-in-an-app-service-environment"></a>Netværk porte, der bruges i en App Service-miljø ##
Før låsning indgående netværkstrafik med en sikkerhedsgruppe netværk, er det vigtigt at kende sæt af krævede og valgfrie netværksporte, der bruges af et App-tjenesten miljø.  Lukke ved et uheld kommer fra trafik til nogle porte, kan det medføre tab af funktionalitet i en App Service-miljø.

Følgende er en liste over porte, der bruges ved en App Service-miljø. Alle porte er **TCP**, medmindre andet er angivet tydeligt:

- 454: **kræves port** bruges af Azure infrastruktur til at administrere og vedligeholde App Service miljøer via SSL.  Bloker ikke trafik til denne port.  Denne port er altid bundet til den offentlige VIP af en ASE.
- 455: **kræves port** bruges af Azure infrastruktur til at administrere og vedligeholde App Service miljøer via SSL.  Bloker ikke trafik til denne port.  Denne port er altid bundet til den offentlige VIP af en ASE.
- 80: standard port for indgående HTTP-trafik til apps, der kører i App-tjenesten planer i en App Service-miljø.  Denne port er bundet til ILB adressen på ASE på en ILB aktiverede ASE.
- 443: standard port for indgående SSL-trafik til apps, der kører i App-tjenesten planer i en App Service-miljø.  Denne port er bundet til ILB adressen på ASE på en ILB aktiverede ASE.
- 21: kanalen til styring af FTP.  Denne port kan blokeres sikkert, hvis FTP ikke bruges.  På en ILB aktiverede ASE bindes denne port til ILB adressen for en ASE.
- 990: kanalen til styring af til FTPS.  Denne port kan blokeres sikkert, hvis FTPS ikke bruges.  På en ILB aktiverede ASE bindes denne port til ILB adressen for en ASE.
- 10001 10020: data kanaler for FTP.  Som i salgskanalen kontrolelement kan portene sikkert blokeres Hvis FTP ikke bruges.  På en ILB aktiverede ASE, kan denne port bindes til den ASE ILB adresse.
- 4016: bruges til fejlfinding i forbindelse med Visual Studio 2012.  Denne port kan blokeres sikkert, hvis funktionen ikke er bruges.  Denne port er bundet til ILB adressen på ASE på en ILB aktiverede ASE.
- 4018: bruges til fejlfinding i forbindelse med Visual Studio-2013.  Denne port kan blokeres sikkert, hvis funktionen ikke er bruges.  Denne port er bundet til ILB adressen på ASE på en ILB aktiverede ASE.
- 4020: bruges til fejlfinding i forbindelse med Visual Studio-2015.  Denne port kan blokeres sikkert, hvis funktionen ikke er bruges.  Denne port er bundet til ILB adressen på ASE på en ILB aktiverede ASE.

## <a name="outbound-connectivity-and-dns-requirements"></a>Udgående forbindelse og DNS-krav ##
For en App Service-miljø kan fungere korrekt, kræver udgående adgang til forskellige slutpunkter. En komplet liste over de eksterne slutpunkter, der bruges af en ASE er i afsnittet "Påkrævet netværksforbindelsen" i artiklen [Netværkskonfiguration for ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

App Service miljøer kræver en gyldig DNS-infrastrukturen, der er konfigureret til det virtuelle netværk.  Hvis DNS-konfigurationen ændres til en eller anden grund efter et App-tjenesten miljø er blevet oprettet, kan udviklere tvinge en App-tjenesten miljø for at fortsætte efter den nye DNS-konfiguration.  Udløse en rullende miljø genstart ved hjælp af ikonet "Genstart" findes i øverst del af bladet App servicemiljø management i [Azure portal] [ NewPortal] medfører miljø at fortsætte efter den nye DNS-konfiguration.

Det anbefales også, at eventuelle brugerdefinerede DNS-servere på vnet indstilles forvejen før du opretter en App Service-miljø.  Hvis et virtuelt netværk DNS-konfiguration ændres, mens der oprettes et App-tjenesten miljø, der medfører App servicemiljø oprettelse af processen fejlbehæftede.  I en lignende vein, hvis en brugerdefineret DNS-server findes på anden ende af en VPN-gateway, og DNS-serveren er ikke tilgængelig eller ikke tilgængelig, mislykkes oprettelsen af App servicemiljø også.

## <a name="creating-a-network-security-group"></a>Oprette en sikkerhedsgruppe netværk ##
Detaljerede oplysninger om hvordan netværkssikkerhed grupperer arbejde se følgende [oplysninger][NetworkSecurityGroups].  Detaljer under touch i fremhæver af netværk sikkerhedsgrupper, med fokus på konfiguration og anvende en netværk sikkerhedsgruppe til en undernet, der indeholder et App Service-miljø.

**Note:** Netværk sikkerhedsgrupper kan konfigureres grafisk ved hjælp af [Azure Portal](https://portal.azure.com) eller via Azure PowerShell.

Netværk sikkerhedsgrupper oprettes først som en enkeltstående enhed, der er knyttet til et abonnement. Da netværk sikkerhedsgrupper oprettes på et Azure område, kan du sikre dig, sikkerhedsgruppen netværk er oprettet i det samme område som App Service-miljø.

Følgende viser oprettelse af en sikkerhedsgruppe netværk:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Når en netværk sikkerhedsgruppe er oprettet, føjes en eller flere netværk sikkerhedsregler til den.  Da sæt regler måske ændres med tiden, anbefales det plads ud nummereringsskemaet bruges til regel prioriteringer i forhold til gør det nemt at indsætte flere regler over tid.

Eksemplet herunder viser en regel, der giver adgang til de nødvendige ved Azure infrastrukturen til at administrere og vedligeholde miljøet App Service management porte eksplicit.  Bemærk, at alle management trafik løber over SSL og er sikret med klientcertifikater, så selvom portene åbnes de er ikke tilgængelig af alle enheder end Azure management infrastruktur.


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
    

Når låsning adgang til port 80 og 443 "skjule" et App-tjenesten miljø bag mod strømmen enheder eller tjenester, skal du vide, før IP-adressen.  Eksempelvis hvis du bruger en firewall til webprogrammer (WAF), WAF har sin egen IP-adresse (eller adresser) som den bruger, når proxyforbindelser trafik til et efterfølgende App Service-miljø.  Du skal bruge denne IP-adresse i parameteren *SourceAddressPrefix* for en regel for netværk sikkerhed.

I eksemplet herunder tillades eksplicit indgående trafik fra en bestemt før IP-adresse.  Adresse *1.2.3.4* bruges som en pladsholder til en tidligere WAF IP-adresse.  Ændre værdien, så det svarer til den adresse, der anvendes af din tidligere enhed eller en tjeneste.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
    
Hvis FTP-understøttelse ønskes, kan følgende regler bruges som en skabelon til at give adgang til FTP-kontrolelement port og data kanal porte.  Da FTP er en med høj sikkerhed protokol, kan du muligvis ikke distribuerer FTP-trafik via en traditionel HTTP/HTTPS firewall eller proxyserver enhed.  I dette tilfælde skal du angive *SourceAddressPrefix* til en anden værdi – for eksempel IP-adresseområdet for udvikler eller installation maskiner på hvilke FTP-klienter, der kører. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Note:** dataområdet kanal port kan blive ændret i perioden, preview.)

Hvis ekstern fejlfinding med Visual Studio bruges, viser følgende regler, hvordan til at give adgang.  Der findes en separat regel for hver understøttet version af Visual Studio, da hver version anvender en anden port til ekstern fejlfinding.  Som med FTP-adgang, kan remote fejlfinding trafik ikke flyder korrekt via en traditionel WAF eller proxy enhed.  *SourceAddressPrefix* kan i stedet indstilles til IP-adresseområder for udvikler maskiner, der kører Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Tildele en sikkerhedsgruppe netværk til et undernet ##
En sikkerhedsgruppe netværk har en standard sikkerhedsregel som afviser adgang til alle eksterne trafik.  Resultatet af at kombinere de netværk sikkerhedsregler, der er beskrevet ovenfor og standard sikkerhedsreglen blokering af indgående trafik er, der kun trafik fra kildeadresse områder, der er knyttet til en *Tillad* handling vil kunne sende trafik til apps, der kører i et App-tjenesten miljø.

Når en netværk sikkerhedsgruppe er udfyldt med sikkerhedsregler, skal den tildeles til det undernet, der indeholder det App Service-miljø.  Kommandoen tildeling refererer til både navnet på det virtuelle netværk, hvor App Service miljøet er placeret, såvel som navnet på det undernet, hvor App Service miljøet blev oprettet.  

Eksemplet herunder viser en sikkerhedsgruppe i netværk, der tildeles til en undernet og virtuelt netværk:


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Når netværket sikkerhed gruppetildeling lykkes (opgaven er en længerevarende handlinger og kan tage et par minutter at gennemføre), kun indgående trafik, der svarer til *Tillad* regler kan nå apps i App-tjenesten miljø.

I følgende eksempel vises for fuldstændighed Sådan fjernes og dermed dis-knytte sikkerhedsgruppen netværk fra undernettet:


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Særlige overvejelser i forbindelse med eksplicitte IP-SSL ##
Hvis en app er konfigureret med en eksplicit IP-SSL løse (gælder ASEs, der kun har en offentlig VIP), skal du i stedet for via standard IP-adressen for det App Service-miljø, både HTTP og HTTPS trafik løber ind i undernet over et andet sæt porte end port 80 og 443.

De enkelte par af porte, der bruges ved hver IP-SSL-adresse kan findes i portalen brugergrænsefladen fra App servicemiljø detaljer UX blade.  Vælg "alle indstillinger"--> "IP-adresser".  Bladet "IP-adresser" viser en liste over alle direkte konfigureret IP-SSL-adresser for tjenesten App-miljø, sammen med parret speciel port, der bruges til at omdirigere HTTP og HTTPS trafikken, der er knyttet til hver IP-SSL-adresse.  Det er denne port par, der skal bruges til parametrene DestinationPortRange, når du konfigurerer regler i en sikkerhedsgruppe netværk.

Når en app på en ASE er konfigureret til at bruge IP-SSL, eksterne kunder kan se ikke og behøver ikke at bekymre dig om særlig port par tilknytning.  Trafik til apps, der ombrydes normalt til den konfigurerede IP-SSL-adresse.  Oversættelse til parret speciel port sker automatisk internt under den sidste del af dirigere trafik til det undernet, der indeholder ASE. 

## <a name="getting-started"></a>Kom godt i gang

For at komme i gang med App-tjenesten miljøer skal du se [Introduktion til App Service-miljø][IntroToAppServiceEnvironment]

Alle artikler og hvordan-til er for App-tjenesten miljøer er tilgængelige i [vigtige oplysninger om programmet Service miljøer](../app-service/app-service-app-service-environments-readme.md).

Få mere at vide om apps sikkert at oprette forbindelse til back end-ressource fra et App-tjenesten miljø se [sikker forbindelse til back end-ressourcer fra et App Service-miljø][SecurelyConnecttoBackend]

Du kan finde flere oplysninger om Azure App Service platformen, se [Azure App Service][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[SecurelyConnecttoBackend]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->
 
