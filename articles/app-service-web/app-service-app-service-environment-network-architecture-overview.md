<properties 
    pageTitle="Netværk arkitektur oversigt over App Service miljøer" 
    description="Arkitektonisk oversigt over netværk topologi ofApp Service miljøer." 
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
    ms.date="10/04/2016" 
    ms.author="stefsch"/>   

# <a name="network-architecture-overview-of-app-service-environments"></a>Netværk arkitektur oversigt over App Service miljøer

## <a name="introduction"></a>Introduktion ##
App-tjenesten miljøer altid er oprettet i et undernet af et [virtuelt netværk] [ virtualnetwork] -apps, der kører i et App-tjenesten miljø kan kommunikere med private slutpunkter, der er placeret i den samme virtuelle netværkstopologi.  Da kunder kan låse, dele af deres virtuelle netværksinfrastruktur, er det vigtigt at forstå typerne netværk kommunikation flyder, der opstå i forekommer med en App Service-miljø.

## <a name="general-network-flow"></a>Generelt netværk Flow ##
 
Når en App Service miljø (ASE) benytter en offentlig virtuelle IP-adresse (VIP) til apps, ankommer al indgående trafik på den offentlige VIP.  Dette omfatter HTTP og HTTPS trafik til apps og anden trafik til FTP, remote fejlfinding funktionalitet og Azure management handlinger.  En komplet liste over de bestemte porte (både krævede og valgfrie), der er tilgængelige på den offentlige VIP artiklen på [styre indgående trafik] [ controllinginboundtraffic] en App Service-miljø. 

App Service miljøer understøtter også kører apps, der er bundet kun til virtuelt netværk interne adresse, der også kaldes en ILB (interne justering af belastning) adresse.  På en ILB ankommer aktiveret ASE, HTTP og HTTPS trafik til apps samt remote fejlfinding opkald på ILB adresse.  De mest almindelige ILB ASE konfigurationer leveres FTP-/ FTPS trafik, også på ILB adresse.  Men Azure management handlinger flyder til porte 454/455 på den offentlige VIP af en ILB aktiveret ASE.

Diagrammet nedenfor viser en oversigt over de forskellige indgående og udgående netværk flow for en App Service-miljø, hvor apps er bundet til en offentlig virtuelle IP-adresse:

![Generelt netværk flyder][GeneralNetworkFlows]

Et App Service-miljø kan kommunikere med en række forskellige privat kunde slutpunkter.  For eksempel apps, der kører i App-tjenesten miljøet kan oprette forbindelse til databasen Server(e) kører på IaaS virtuelle maskiner i den samme virtuelle netværkstopologi.

>[AZURE.IMPORTANT] Studere netværksdiagrammet, er installeret på "anden beregne ressourcer" i et andet undernet fra App Service-miljø. Implementering af ressourcer i det samme undernet med ASE blokerer forbindelse fra ASE til disse ressourcer (med undtagelse af bestemte handel-ASE routing). Installere på et andet undernet i stedet (i den samme VNET). App-tjenesten miljøet vil derefter kunne oprette forbindelse. Ingen yderligere konfiguration er nødvendigt.

App Service miljøer også kommunikere med Sql DB og Azure-lager ressourcerne til at administrere og en App Service-miljø.  Nogle af de Sql og lager ressourcer, der et App-tjenesten miljø kommunikerer med er placeret i det samme område som App Service-miljø, mens andre er placeret i remote Azure områder.  Udgående forbindelse til internettet er derfor altid påkrævet for en App Service-miljø kan fungere korrekt. 

Da et App-tjenesten miljø er installeret i et undernet, kan netværk sikkerhedsgrupper bruges til at styre indgående trafik til undernettet.  Yderligere oplysninger om, hvordan du styre indgående trafik til et App-tjenesten miljø, finder du i følgende [artikel][controllinginboundtraffic].

Yderligere oplysninger om, hvordan du Tillad udgående forbindelse til internettet fra et App Service-miljø, finder du i følgende artikel om at arbejde med [Express distribuere][ExpressRoute].  Den samme fremgangsmåde, der er beskrevet i artiklen gælder, når du arbejder med forbindelse til websted og ved hjælp af tvunget tunnelføring.

## <a name="outbound-network-addresses"></a>Udgående netværksadresser ##
Når et App-tjenesten miljø foretager udgående opkald, er en IP-adresse er altid knyttet til de udgående opkald.  Den specifikke IP-adresse, der bruges, afhænger af, om det slutpunkt, der hedder er placeret i den virtuelle netværkstopologi eller uden for den virtuelle netværkstopologi.

Hvis det slutpunkt, der hedder er **uden for** af den virtuelle netværkstopologi, er den udgående adresse (også kendt den udgående NAT-adresse), der bruges den offentlige VIP af App Service-miljø.  Denne adresse kan findes i portalen-brugergrænsefladen for App-tjenesten miljø i egenskaber blade.
 
![Udgående IP-adresse][OutboundIPAddress]

Denne adresse kan også bestemmes for ASEs, der kun har en offentlig VIP ved at oprette en app i App-tjenesten miljø, og derefter udføre en *nslookup* på den app mailadresse. Den resulterende IP-adresse er både den offentlige VIP samt App servicemiljø udgående NAT-adresse.

Hvis det slutpunkt, der hedder er **inden** for den virtuelle netværkstopologi, bliver den udgående opkald app-adresse interne IP-adressen på den enkelte Beregn ressource, der kører på app.  Der er dog ikke en fast tilknytningen af virtuelt netværk interne IP-adresser til apps.  Apps kan flytte rundt på tværs af forskellige Beregn ressourcer og puljen af tilgængelige Beregn ressourcer i en App Service-miljø kan ændre på grund af skalering handlinger.

Men da et App-tjenesten miljø altid er placeret i et undernet, du er garanti for, at interne IP-adressen på en Beregn ressource, der kører en app vil altid ligge inden for området CIDR til undernettet.  Som et resultat, når detaljerede ACLs eller netværk sikkerhedsgrupper der anvendes til sikker adgang til andre slutpunkter inden for det virtuelle netværk, den undernet område, der indeholder det App Service-miljø, skal have tildelt adgang.

I følgende diagram vises disse begreber mere detaljeret:

![Udgående netværksadresser][OutboundNetworkAddresses]

I diagrammet ovenfor:

- Da den offentlige VIP af App Service miljøet er 192.23.1.2, det er den udgående IP-adresse bruges, når du foretager opkald til "Internet" slutpunkter.
- CIDR celleområde med undernet for App-tjenesten miljøet er 10.0.1.0/26.  Andre slutpunkter i den samme virtuelle netværksinfrastruktur ser opkald fra apps som kommer fra et sted, hvor denne adresseområde.

## <a name="calls-between-app-service-environments"></a>Opkald mellem App Service miljøer ##
Et scenarie med mere komplekse kan opstå, hvis du installerer miljøer med flere App-tjenesten i det samme virtuelle netværk og foretage udgående opkald fra et App-tjenesten miljø til en anden App Service-miljø.  Disse typer cross App servicemiljø opkald behandles også som "Internet" opkald.

I det følgende diagram viser et eksempel på en lagdelt arkitektur med apps på en App Service-miljø (f.eks. "Foran dør" webapps) kalder apps på en anden App Service-miljø (fx interne back end-API apps ikke skal være tilgængeligt via internettet). 

![Opkald mellem App Service miljøer][CallsBetweenAppServiceEnvironments] 

I eksemplet ovenfor har App servicemiljø "ASE en" 192.23.1.2 udgående IP-adressen.  Hvis en app, der kører på denne behandlet App servicemiljø gør en udgående opkald til en app, der kører på en anden App servicemiljø ("ASE to"), der er placeret i det samme virtuelle netværk og den udgående opkald bliver som en "" Internetopkald.  Som et resultat netværkstrafikken ankommer på andet App Service-miljø, vises som kommer fra 192.23.1.2 (det vil sige ikke undernet adresseområde på det første App servicemiljø).

Selvom opkald mellem forskellige App Service-miljøer, behandles det som "Internet" opkald, når begge App Service miljøer er placeret i det samme Azure område netværkstrafikken forbliver på det regionale Azure netværk og vil ikke fysisk løber over offentlige internettet.  Som et resultat kan du bruge en netværk sikkerhedsgruppe på undernet af det andet App Service-miljø til kun for at tillade indgående opkald fra den første App Service-miljø (hvis udgående IP-adresse er 192.23.1.2), og som derfor kontrollere, at sikre kommunikationen mellem App Service miljøer.

## <a name="additional-links-and-information"></a>Flere Links og oplysninger ##
Alle artikler og hvordan-til er for App-tjenesten miljøer er tilgængelige i [vigtige oplysninger om programmet Service miljøer](../app-service/app-service-app-service-environments-readme.md).

Få at vide om indgående porte, der bruges af App Service miljøer og bruger netværk sikkerhedsgrupper til at styre indgående trafik findes [her][controllinginboundtraffic].

Oplysninger om brugen af brugeren defineret omdirigerer til at give udgående internetadgang til App-tjenesten miljøer er tilgængelig i denne [artikel][ExpressRoute]. 


<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[ExpressRoute]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

