<properties 
    pageTitle="Geografisk distribueret Skaler med App Service miljøer" 
    description="Lær, hvordan vandret skalere apps ved hjælp af geografisk-fordeling med trafik overordnet og App-tjenesten miljøer." 
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
    ms.date="09/07/2016" 
    ms.author="stefsch"/>   

# <a name="geo-distributed-scale-with-app-service-environments"></a>Geografisk distribueret Skaler med App Service miljøer

## <a name="overview"></a>Oversigt ##
Programmet scenarier, der kræver meget høj skala kan overskride Beregn ressourcekapacitet, som er tilgængelige for en enkelt installation af en app.  Afstemning programmer, er sportsbegivenheder og fjernsynstransmission underholdning begivenheder alle eksempler på scenarier, der kræver meget høj skala. Høj skala krav kan opfyldes af vandret skalering af apps, med flere app-installationer, der udføres i et enkelt område og på tværs af områder til at håndtere yderste Indlæs krav.

App Service miljøer er en ideel platform til vandret skalering ud.  Én gang en App-tjenesten miljø konfiguration er valgt, der kan understøtte en anmodning om kendte sats, udviklere kan installere flere App tjenesten miljøer på "kageform" måde for at opnå en ønskede spidsbelastning Indlæs kapacitet.

Antag f.eks. en app, der kører på en App-tjenesten miljø konfiguration er testet for at håndtere anmodninger om 20K sekundet (RPS).  Hvis den ønskede spidsbelastning Indlæs kapacitet er 100K RPS, kan fem (5) App Service miljøer oprettet og konfigureret til at sikre programmet kan håndtere den største planlagte belastning.

Da kunder åbner typisk apps ved hjælp af et brugerdefineret (eller vanity) domæne, skal udviklere en metode til at distribuere app-anmodninger på tværs af alle forekomster App Service-miljø.  Er en god måde at gøre dette til at løse det brugerdefinerede domæne ved hjælp af en [Azure trafik Manager profil][AzureTrafficManagerProfile].  Trafik Manager profil kan være konfigureret til at pege på alle de enkelte App Service miljøer.  Trafik Manager håndterer automatisk distribuere kunder på tværs af alle de App Service miljøer, der er baseret på indstillinger i profilen trafik Manager af belastning.  Denne metode fungerer, uanset om alle de App Service miljøer er implementeret i et enkelt Azure område eller installeret på verdensplan på tværs af flere Azure områder.

Desuden da kunder access-apps gennem vanity domænet, oplever kunder antallet af App Service-miljøer, der kører en app.  Som et resultat kan udviklere hurtigt og nemt tilføje og fjerne, App Service miljøer baseret på observerede trafik Indlæs.

Grundlæggende diagrammet nedenfor viser en vandret skaleret på tværs af tre App Service miljøer inden for et enkelt område-app.

![Grundlæggende arkitektur][ConceptualArchitecture] 

Resten af dette emne fører gennem trinnene til at med at konfigurere en distribueret topologi for eksempel appen med flere App Service miljøer.

## <a name="planning-the-topology"></a>Planlægning af topologien ##
Før du opbygge en fordelt app-miljøet hjælper det har et par stykker oplysninger forvejen.

- **Brugerdefineret domæne til appen:**  Hvad er det brugerdefinerede domænenavn, kunder kan bruge til at få adgang til appen?  For eksempel app er det brugerdefinerede domænenavn *www.scalableasedemo.com*
- **Trafik Manager domæne:**  Et domænenavn, skal være valgt, når du opretter en [Azure trafik Manager profil][AzureTrafficManagerProfile].  Dette navn, kombineres med *trafficmanager.net* suffiks til at registrere et domæne-post, der administreres af trafik Manager.  For eksempel-app er det navn, der er valgt *SVG-ase-demo*.  Derfor er det fulde domænenavn, der administreres af trafik Manager *SVG-ase-demo.trafficmanager.net*.
- **Strategi for skalering app-miljøet:**  Programmet miljøet fordeles på tværs af miljøer med flere App-tjenesten i et enkelt område?  Flere områder?  En mix og match-af begge tilgange?  Beslutningen skal baseres på forventninger, som kunden trafik stammer fra, samt hvor godt resten af en app understøttende back end-infrastruktur kan skalere.  For eksempel med en 100% uden status program, kan en app stort omfang skaleres ved hjælp af en kombination af flere App Service miljøer per Azure region, multipliceret med App-tjenesten miljøer, der er installeret på tværs af flere Azure områder.  Med 15 + offentlige Azure områder kan vælge mellem, kan kunder virkelig bygge et verdensplan hyper-skala program-miljøet.  For eksempel appen bruges til denne artikel, er tre App Service miljøer oprettet i et enkelt Azure område (syd Central US).
- **Naming convention til App-tjenesten miljøer:**  Hver App servicemiljø kræver et entydigt navn.  Ud over en eller to App Service miljøer er det nyttigt at have en navngivningsregel for at identificere hver App Service-miljø.  For eksempel appen blev brugt en simpel navngivningskonvention.  Navnene på de tre App Service miljøer er *fe1ase*, *fe2ase*og *fe3ase*.
- **Naming convention for apps:**  Da flere forekomster af appen, som skal installeres, bruges et navn for hver forekomst af appen udløst.  En lille kendte, men meget praktisk funktion til App-tjenesten miljøer er, at det samme appnavn kan bruges på tværs af flere App Service miljøer.  Da hver App servicemiljø har et entydigt domænesuffiks, kan udviklere vælge at genbruge den nøjagtige samme app-navn i de enkelte miljøer.  For eksempel en udvikler kan have apps med navnet på følgende måde: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*osv.  For eksempel appen via hver app-forekomst, har også et entydigt navn.  App forekomstnavnene bruges er *webfrontend1*, *webfrontend2*og *webfrontend3*.


## <a name="setting-up-the-traffic-manager-profile"></a>Konfiguration af trafik Manager-profil ##
Når flere forekomster af en app er installeret på miljøer med flere App-tjenesten, kan de enkelte app forekomster registreres med trafik Manager.  For eksempel appen en trafik Manager profil der skal bruges til *SVG-ase-demo.trafficmanager.net* , der kan omdirigere kunder til et af følgende udløst app forekomster:

- **webfrontend1.fe1ase.p.azurewebsites.net:**  En forekomst af appen eksempel, der er installeret på den første App Service-miljø.
- **webfrontend2.fe2ase.p.azurewebsites.net:**  En forekomst af appen eksempel, der er installeret på det andet App Service-miljø.
- **webfrontend3.fe3ase.p.azurewebsites.net:**  En forekomst af appen eksempel, der er installeret på det tredje App Service-miljø.

Den nemmeste måde at registrere flere Azure App Service slutpunkter, alle kører i den **samme** Azure område er med Powershell [Azure Manager trafik ressourceleder, der understøtter][ARMTrafficManager].  

Det første trin er at oprette en Azure trafik Manager profil.  Nedenstående kode viser, hvordan profilen, der er oprettet for eksempel-app:

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Bemærk, hvordan parameteren *RelativeDnsName* er indstillet til *SVG-ase-demo*.  Dette er hvordan domæne navn *SVG-ase-demo.trafficmanager.net* oprettes og er knyttet til en trafik Manager profil.

Parameteren *TrafficRoutingMethod* definerer den politik trafik Manager vil bruge til at finde ud af, hvordan du spreder kunde belastning på tværs af alle de tilgængelige slutpunkter til justering af belastning.  I dette eksempel *vægtet* er metode valgt.  Dette kan medføre kunde anmodninger fordeles på tværs af alle registrerede programmet slutpunkterne baseret på de relative tykkelser, der er knyttet til hvert slutpunkt. 

Med den profil, der er oprettet, tilføjes hver app-forekomst til profilen, som en oprindelig Azure slutpunkt.  Nedenstående kode henter en reference til hver frontend WebApp, og derefter tilføjer hver app som et trafik Manager slutpunkt ved hjælp af parameteren *TargetResourceId* .


    $webapp1 = Get-AzureRMWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzureRMWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzureRMWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10
    
    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile
    
Bemærk, at der er et opkald til *Tilføj AzureTrafficManagerEndpointConfig* for hver enkelt app-forekomst.  Parameteren *TargetResourceId* i hver Powershell-kommando refererer til en af de tre udløst app forekomster.  Profilen trafik Manager udbrede Indlæs alle tre slutpunkter, der er registreret i profilen.

Alle tre slutpunkterne bruger den samme værdi (10) for parameteren *tykkelse* .  Dette resulterer i trafik Manager sprede kunde anmodninger på tværs af alle forekomster af tre app relativt jævnt. 


## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Peger på App eget domæne på trafik Manager domæne ##
Der er det sidste trin, der er nødvendige til at pege dit eget domæne af app på trafik Manager domæne.  Det betyder peger *www.scalableasedemo.com* ved *SVG-ase-demo.trafficmanager.net*, for eksempel appen.  Dette trin skal udføres hos domæneregistratoren, der administrerer dit eget domæne.  

Ved hjælp af værktøjer til administration af din domæneregistrator, en CNAME-poster skal oprettes som peger dit eget domæne på trafik Manager domæne.  Billedet nedenfor viser et eksempel på hvad denne CNAME konfiguration ser sådan ud:

![CNAME for brugerdefineret domæne][CNAMEforCustomDomain] 

Selvom du ikke er omfattet i dette emne, du huske, at hver enkelt app-forekomst, skal have dit eget domæne, der er registreret med den samt.  Ellers hvis en anmodning om gør det til en app-forekomst, og programmet ikke har dit eget domæne, der er registreret med programmet, mislykkes anmodningen.  

I dette eksempel er dit eget domæne *www.scalableasedemo.com*, og hver forekomst af programmet har dit eget domæne, der er knyttet til den.

![Brugerdefineret domæne][CustomDomain] 

Se følgende artikel om [registrering af brugerdefinerede domæner]til en opsummering af til registrering af et brugerdefineret domæne til Azure App Service apps[RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Prøver topologien distribueret ##
Afslut resultatet af trafik overordnet og DNS-konfigurationen er, at teksten anmodninger om *www.scalableasedemo.com* går gennem følgende rækkefølge:

1. En browser eller en enhed vil gøre en DNS-opslag for *www.scalableasedemo.com*
2. CNAME-posten hos domæneregistratoren bevirker, at DNS-opslag til at blive omdirigeret til Azure trafik Manager.
3. En DNS-opslag er gjort til *SVG-ase-demo.trafficmanager.net* mod en af Azure trafik Manager DNS-serverne.
4. Baseret på belastningsjustering politik ( *TrafficRoutingMethod* parameteren bruges tidligere, når du opretter profilen trafik Manager), bliver trafik Manager Vælg en af de konfigurerede slutpunkter og vende tilbage det fuldstændige Domænenavn for pågældende slutpunkt til browser eller enhed.
5.  Da det fuldstændige Domænenavn for slutpunktet er URL-adressen for en app-forekomst, der kører på en App Service-miljø, hvilken browser eller enhed bliver bedt om en Microsoft Azure DNS-server til at løse det fulde Domænenavn til en IP-adresse. 
6. Hvilken browser eller enhed sender HTTP/S anmodningen til IP-adressen.  
7. Anmodningen vil ankommer til en af de app forekomster, der kører på en af de App Service miljøer.

Console billedet herunder viser en DNS-opslag for appen eksempel brugerdefinerede domæne løse korrekt til en app-forekomst, der kører på en af de tre eksempel App Service-miljøer (i dette tilfælde andet af de tre App Service miljøer):

![DNS-opslag][DNSLookup] 

## <a name="additional-links-and-information"></a>Flere Links og oplysninger ##
Alle artikler og hvordan-til er for App-tjenesten miljøer er tilgængelige i [vigtige oplysninger om programmet Service miljøer](../app-service/app-service-app-service-environments-readme.md).

Dokumentation på Powershell [Azure Manager trafik ressourceleder, der understøtter][ARMTrafficManager].  

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]:  https://azure.microsoft.com/documentation/articles/traffic-manager-manage-profiles/
[ARMTrafficManager]:  https://azure.microsoft.com/documentation/articles/traffic-manager-powershell-arm/
[RegisterCustomDomain]:  https://azure.microsoft.com/en-us/documentation/articles/web-sites-custom-domain-name/


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 
