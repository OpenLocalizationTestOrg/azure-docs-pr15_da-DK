<properties 
    pageTitle="Sådan oprettes et App Service-miljø" 
    description="Oprettelse af flow beskrivelse for app-tjenesten miljøer" 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="stefsch" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/22/2016" 
    ms.author="ccompy"/>

# <a name="how-to-create-an-app-service-environment"></a>Sådan oprettes et App Service-miljø #

### <a name="overview"></a>Oversigt ###

App Service miljøer (ASE) er en Premium service indstilling af Azure App-tjeneste, som leverer en forbedret konfiguration egenskab, der ikke er tilgængelig i flere lejer stempler.  Funktionen ASE installerer grundlæggende tjenesten Azure-App til en kundes virtuelt netværk.  Til at få en større forståelse af de egenskaber findes i App-tjenesten miljøer læse, [Hvad er et App servicemiljø] [ WhatisASE] dokumentation.

### <a name="before-you-create-your-ase"></a>Før du opretter din ASE ###

Det er vigtigt at være opmærksom på de ting, du ikke kan ændre.  Disse aspekter, du ikke kan ændre om din ASE, når den er oprettet er:

- Placering
- Abonnement
- Ressourcegruppe
- VNet bruges
- Undernet bruges 
- Undernet størrelse

Når du vælge en VNet og angive et undernet Sørg for, at er det stort nok til en hvilken som helst fremtidig vækst.  

### <a name="creating-an-app-service-environment"></a>Oprette en App Service-miljø ###

Der er to måder at få adgang til ASE oprettelse af Brugergrænsefladen.  Det kan være findes ved at søge i Azure Marketplace efter ***App servicemiljø*** eller ved at gå gennem ny -> Web + Mobile -> App Service-miljø.  Sådan oprettes en ASE:

1. Angiv navnet på din ASE.  Det navn, der er angivet for ASE der skal bruges til de apps, der er oprettet i ASE.  Hvis navnet på ASE er appsvcenvdemo derefter underdomænenavnet skal være. *appsvcenvdemo.p.azurewebsites.net*.  Hvis du har oprettet en app, med navnet *mytestapp* dermed vil derefter det være tilgængelige på *mytestapp.appsvcenvdemo.p.azurewebsites.net*.  Du kan ikke bruge luft navnet på din ASE.  Hvis du bruger store bogstaver tegn i navnet, bliver domænenavnet den samlede små version af dette navn.  Hvis du bruger en ILB derefter navnet på din ASE bruges ikke i et underdomæne, men er i stedet eksplicit er angivet under oprettelse af ASE

    ![][1]

2. Vælg dit abonnement.  Det abonnement, der bruges til din ASE er også en, der alle apps i pågældende ASE oprettes med.  Du kan placere dine ASE i en VNet, der er i et andet abonnement

3. Vælge eller angive en ny ressourcegruppe.  Ressourcegruppen, der bruges til din ASE skal være den samme, der bruges til din VNet.  Hvis du vælger en eksisterende VNet opdateres ressource gruppe markeringen for din ASE for at afspejle dine VNet.

    ![][2]

4. Foretag dine valg virtuelt netværk og placering.  Du kan vælge at oprette en ny VNet eller vælge en eksisterende VNet.  Hvis du vælger en ny VNet, og derefter kan du angive et navn og placering. Den nye VNet har adresse område 192.268.250.0/23 og et undernet med navnet **standard** , der er defineret som 192.168.250.0/24.  Du kan også blot vælge et eksisterende Classic eller ressource Manager VNet.  Valgmuligheden VIP Type bestemmer, om din ASE kan åbnes direkte fra internettet (ekstern), eller hvis den anvender en intern Indlæs belastningsjusteringstjenesten (ILB).  For at lære mere om dem læsevenlig [ved hjælp af en intern justering af belastning, miljøet App Service][ILBASE].  Hvis du vælger en ekstern VIP type kan du vælge hvor mange eksterne IP-adresser systemet er oprettet med henblik på IPSSL.  Hvis du vælger interne skal du angive det underdomæne, din ASE vil bruge.  ASEs kan installeres i virtuelle netværk, der bruger *enten* offentlige adresseområder *eller* RFC1918 adresse mellemrum (dvs. private adresser).  Hvis du vil bruge et virtuelt netværk med en offentlig adresseområde, skal du oprette VNet forvejen.  Når du vælger en eksisterende VNet skal du oprette en ny undernet under oprettelse af ASE.  **Du kan ikke bruge et allerede er oprettet undernet på portalen.  Du kan oprette en ASE med et eksisterende undernet, hvis du opretter din ASE ved hjælp af en ressource manager skabelon.**  For at oprette en ASE fra en skabelon Brug oplysningerne her, [oprette en App servicemiljø fra skabelon] [ ILBAseTemplate] , og her, [oprette et ILB App servicemiljø fra skabelon][ASEfromTemplate].

### <a name="details"></a>Detaljer ###

Der oprettes en ASE med 2 Front slutter og 2 medarbejdere.  Forside slutter fungere som HTTP/HTTPS-slutpunkter og sende trafik til de medarbejdere, som er de roller, der hoster dine apps.   Du kan justere antallet efter oprettelse af ASE og kan også konfigurere disse ressourcepuljer Autoskalering regler.  Du kan finde flere oplysninger om manuel skalering, administration og overvågning af miljøet App-tjenesten skal du gå hertil: [hvordan du konfigurerer et App Service-miljø][ASEConfig] 

Kun én ASE kan findes i det undernet, der anvendes af ASE.  Undernettet kan ikke bruges til noget andet end ASE

### <a name="after-app-service-environment-creation"></a>Efter oprettelse af App Service-miljø ###

Du kan justere efter oprettelse af ASE:

- Antallet af Front slutter (minimum: 2)
- Antal medarbejdere (minimum: 2)
- Antallet af IP-adresser, der er tilgængelige for IP SSL
- Beregne ressource størrelser, der bruges af Front slutter eller medarbejdere (Front End minimumstørrelse er P2)


Der er flere oplysninger om manuel skalering, administration og overvågning af App Service miljøer her: [hvordan du konfigurerer et App Service-miljø][ASEConfig] 

Oplysninger om Autoskalering der er en vejledning her: [hvordan du konfigurerer Autoskalering for en App Service-miljø][ASEAutoscale]

Der er flere faktorer, der ikke er tilgængelige for tilpasning som database og lagerplads.  Dette er håndteret af Azure og følger med systemet.  Systemet lagring understøtter op til 500 GB for hele App-miljøet og databasen justeres med Azure efter behov ved skalaen på systemet.


## <a name="getting-started"></a>Kom godt i gang
Alle artikler og hvordan-til er for App-tjenesten miljøer er tilgængelige i [vigtige oplysninger om programmet Service miljøer](../app-service/app-service-app-service-environments-readme.md).

For at komme i gang med App-tjenesten miljøer skal du se [Introduktion til App Service miljøer][WhatisASE]

Du kan finde flere oplysninger om Azure App Service platformen, se [Azure App Service][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/ 
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/
[ILBAseTemplate]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-create-ilb-ase-resourcemanager/
