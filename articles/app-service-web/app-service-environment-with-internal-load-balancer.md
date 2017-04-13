<properties
    pageTitle="Oprette og bruge en intern justering af belastning med et App-tjenesten miljø | Microsoft Azure"
    description="Oprette og bruge en ASE med en ILB"
    services="app-service"
    documentationCenter=""
    authors="ccompy"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="ccompy"/>

# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Ved hjælp af en intern justering af belastning med en App Service-miljø #

Funktionen App Service Environments(ASE) er en Premium service indstilling af Azure App-tjeneste, som leverer en forbedret konfiguration egenskab, der ikke er tilgængelig i flere lejer stempler.  Funktionen ASE installerer grundlæggende tjenesten Azure-App i dit Azure virtuelle Network(VNet).  Til at få en større forståelse af de egenskaber findes i App-tjenesten miljøer læse, [Hvad er et App servicemiljø] [ WhatisASE] dokumentation.  Hvis du ikke kender fordelene ved operativsystem i en VNet læse [Ofte stillede spørgsmål til Azure virtuelle netværk][virtualnetwork].  


## <a name="overview"></a>Oversigt ##


En ASE kan installeres sammen med internet handicapvenlige ark eller en IP-adresse i din VNet.  For at angive IP-adressen til en VNet adresse skal du installere din ASE med en intern Indlæs Balancer(ILB).  Når din ASE er konfigureret med en ILB giver du:

- dit eget domæne eller underdomæne.  Gør det nemt, dette dokument forudsætter underdomæne, men du kan konfigurere begge måder.  
- det certifikat, der bruges til HTTPS
- DNS-administration for dit underdomæne.  


Du kan som svar gøre ting som f.eks.:

- Host intranetprogrammer, som line of business-applikationer sikkert i skyen, kan du finde via et websted til websted eller ExpressRoute VPN
- Host apps i skyen, der ikke vises i offentlige DNS-servere
- oprette internet isolerede back end-apps som front-end-apps kan sikker integration med


#### <a name="disabled-functionality"></a>Deaktiveret funktionalitet ####

Der er nogle ting, du ikke kan udføre, når du bruger en ILB ASE.  Disse ting omfatter:

- Brug af IPSSL
- tildele IP-adresser til bestemte apps
- købe og bruge et certifikat med en app via portalen.  Du kan naturligvis stadig s/MIME direkte med et nøglecenter og bruge det sammen med dine apps, bare ikke via Azure-portalen.


## <a name="creating-an-ilb-ase"></a>Oprette en ILB ASE ##

Oprette en ILB ASE er ikke meget forskelligt fra oprettelse af en ASE normalt.  For en dybere diskussion om oprettelse af en ASE læse [Sådan oprettes et App servicemiljø][HowtoCreateASE].  Oprette en ILB ASE er den samme mellem oprettelse af en VNet under oprettelse af ASE eller vælge en eksisterende VNet.  Sådan oprettes en ILB ASE: 

1.  Vælg **Ny -> Web + Mobile -> App servicemiljø** i portalen Azure
2.  Vælg dit abonnement
3.  Vælge eller oprette en ressourcegruppe
4.  Vælge eller oprette en VNet
5.  Oprette et undernet, hvis du vælger en VNet
6.  Vælg **virtuelle netværksplacering-> VNet konfiguration** og sæt typen VIP til interne
7.  Angiv navn på underdomæne (dette er det underdomæne, der bruges til apps, der er oprettet i denne ASE)
8.  Vælg Ok og derefter oprette


![][1]


Inden bladet virtuelt netværk er der en indstilling for VNet konfiguration.  På denne måde kan du vælge mellem en ekstern VIP eller interne VIP.  Standard er ekstern.  Hvis du har den indstillet til ekstern brug din ASE en internet handicapvenlige VIP.  Hvis du vælger interne, konfigureres din ASE med en ILB på en IP-adresse i din VNet.  


Når du har valgt interne, muligheden for at føje flere IP-adresser til din ASE fjernes og i stedet skal du angive underdomæne til ASE.  Navnet på ASE bruges i en ASE med en ekstern VIP i underdomænet til apps, der er oprettet i pågældende ASE.  
Hvis din ASE hedder ***contosotest*** og din app i den valgte ASE hedder ***mytest*** derefter underdomænet ville være af formatet ***contosotest.p.azurewebsites.net*** og URL-adressen for app, der ville være ***mytest.contosotest.p.azurewebsites.net***.  
Hvis du angiver typen VIP til interne, bruges navnet på din ASE ikke i underdomænet til ASE.  Du angiver underdomænet eksplicit.  Hvis dit underdomæne blev ***contoso.corp.net*** , og du har oprettet en app i den valgte ASE med navnet ***timereporting*** derefter URL-adressen for app der ville være ***timereporting.contoso.corp.net***.


## <a name="apps-in-an-ilb-ase"></a>Apps i en ILB ASE ##

Oprette en app i en ILB ASE er den samme som oprettelse af en app i en ASE normalt.  

1. Vælg **Ny -> Web + Mobile -> Web** eller **Mobile** eller **API App** i portalen Azure
2. Angiv navnet på appen
2. Vælg abonnement
3. Vælge eller oprette ressourcegruppe
4. Vælg eller Opret App Service Plan(ASP).  Hvis oprettelse af en ny ASP derefter Vælg din ASE som placeringen, og vælg arbejder puljen ønskede din ASP skal oprettes i.  Når du opretter ASP kan du vælge din ASE som placeringen og arbejder puljen.  Når du angiver navnet på den app, du vil se, at underdomænet under appnavnet på din erstattes af underdomænet til din ASE.   
5. Vælg Opret.  Hvis du vil have appen vises på dashboardet, skal du markere afkrydsningsfeltet **Fastgør til dashboard** .  

![][2]


Under appnavnet opdateres underdomænenavnet afspejler underdomænet af din ASE.  


## <a name="post-ilb-ase-creation-validation"></a>Indlæg ILB ASE oprettelse af valideringsregler ##

En ILB ASE er lidt anderledes end den ikke - ILB ASE.  Som tidligere nævnt skal du administrere dine egne DNS, og du har også til at angive dine egne for HTTPS-forbindelser.  


Når du har oprettet din ASE vil du bemærke, at underdomænet viser underdomænet, du har angivet, og der ikke er et nyt element i menuen **indstilling** kaldet **ILB certifikat**.  ASE er oprettet med et selvsigneret certifikat, som gør det nemmere at teste HTTPS.  På portalen fortæller dig, skal du angive dit eget certifikat til HTTPS, men det er drev, du har et certifikat, der passer til dit underdomæne.  

![][3]


Hvis du blot afprøver ting og ikke ved, hvordan du opretter et certifikat, kan du bruge programmet IIS MMC console til at oprette en selv signeret certifikat.  Når den er oprettet kan du eksportere den som en .pfx-fil og Overfør det derefter ILB certifikat brugergrænsefladen. Når du åbner et websted, der er sikret med et selvsigneret certifikat, kan browseren give dig en advarsel, du får adgang til webstedet ikke er sikker på grund af manglende evne til at validere certifikatet.  Hvis du vil undgå denne advarsel, du har brug for et korrekt signeret certifikat, der svarer til et underdomæne og har en kæde af sikkerhed og rettighedsadministration, som genkendes af din browser.

![][6]

Hvis du vil prøve strømmen med dine egne certifikater og teste både HTTP og HTTPS adgang til din ASE:

1.  Gå til ASE Brugergrænsefladen efter ASE er oprettet **ASE -> Indstillinger -> ILB certifikater**
2.  Opret ILB certifikat ved at vælge certifikatets pfx-fil, og Angiv adgangskode.  Dette trin tager et lille stykke tid at behandle og den meddelelse, en skalering er i gang, vises.
3.  Hente adressen, ILB for din ASE (**ASE -> egenskaber virtuelle IP-adresse ->**)
4.  Oprette en web-app i ASE efter oprettelse af 
5.  Oprette en VM, hvis du ikke har en i pågældende VNET (ikke i det samme undernet som ASE eller ting sideskift)
6.  Konfigurere DNS for dit underdomæne.  Du kan bruge et jokertegn med et underdomæne i din DNS- eller hvis du vil gøre nogle enkle test redigere hosts-filen på din VM til at angive web app-navn til VIP IP-adresse.  Hvis din ASE havde navnet på underdomæne. ilbase.com, og du foretaget web app-mytestapp, så det ville være behandlet på mytestapp.ilbase.com og angiv derefter, i din hosts-fil.  (I Windows hosts-filen er i C:\Windows\System32\drivers\etc\)
7.  Bruge en browser på pågældende VM og gå til http://mytestapp.ilbase.com (eller en anden web app-navn med et underdomæne)
8.  Bruge en browser på pågældende VM, og gå til https://mytestapp.ilbase.com du nødt til at acceptere manglen sikkerhed, hvis ved hjælp af et selvsigneret certifikat.  


Din ILB IP-adresse er angivet under Egenskaber for din som den virtuelle IP-adresse

![][4]


## <a name="using-an-ilb-ase"></a>Ved hjælp af en ILB ASE ##

#### <a name="network-security-groups"></a>Netværk sikkerhedsgrupper ####

En ILB ASE aktiverer netværkets isolation for dine apps, som apps ikke er tilgængeligt eller endda kendte ved internettet.  Dette er fremragende til at være vært intranetsteder som line of business-applikationer.  Når du har brug at begrænse adgangen selv kan yderligere du stadig bruge netværk sikkerhed Groups(NSGs) til at styre adgangen på niveauet for netværk. 


Hvis du vil bruge NSGs yderligere begrænse adgangen til skal du kontrollere, at du ikke bryder kommunikationen, som ASE skal bruge for at betjene.  Selvom HTTP/HTTPS-adgang er kun via ILB bruges af ASE afhænger ASE stadig ressource uden for VNet.  Se, hvilke netværksadgang kræves der stadig se på oplysningerne i dokumentet på [Styre indgående trafik til et App servicemiljø] [ ControlInbound] og dokumentet på [Netværkskonfigurationsoplysninger til App Service miljøer med ExpressRoute][ExpressRoute].  


Du skal kende den IP-adresse, der bruges af Azure til at administrere din ASE for at konfigurere din NSGs.  IP-adressen er også den udgående IP-adresse fra din ASE, hvis det giver internetanmodninger.  Finde dette IP-adresse, skal du gå til **Indstillinger -> egenskaber** og finde den **Udgående IP-adresse**.  

![][5]


#### <a name="general-ilb-ase-management"></a>Generelt ILB ASE administration ####

Administrere en ILB ASE er stort set det samme som at administrere en ASE normalt.  Du vil skalere op din kollega grupper til at være vært for flere forekomster af ASP og skalere op Front End-servere til at håndtere øget mængder HTTP/HTTPS-trafikken.  Til generelle oplysninger om administration af konfigurationen af en ASE læse dokumentet om [konfiguration af et App servicemiljø][ASEConfig].  


Administration af flere elementer er Certifikatstyring og DNS-administration.  Du har brug at hente og upload det certifikat, der bruges til HTTPS efter oprettelse af ILB ASE og erstatte den, før det udløber.  Da Azure ejer base domænet kan vi giver dig certifikater til ASEs med en ekstern VIP.  Da det underdomæne, der bruges af en ILB ASE kan være noget, skal du angive dine egne til HTTPS. 


#### <a name="dns-configuration"></a>DNS-konfiguration ####

Når du bruger en ekstern VIP DNS administreres af Azure.  En app, der er oprettet i din ASE føjes automatisk til Azure DNS, som er en offentlig DNS.  I en ILB ASE har du administrere dine egne DNS.  For et givet underdomæne som contoso.corp.net skal du oprette A DNS-poster, der peger på din ILB adresse for:

    * 
    *.SCM ftp publicere 


## <a name="getting-started"></a>Kom godt i gang
Alle artikler og hvordan-til er for App-tjenesten miljøer er tilgængelige i [vigtige oplysninger om programmet Service miljøer](../app-service/app-service-app-service-environments-readme.md).

For at komme i gang med App-tjenesten miljøer skal du se [Introduktion til App Service miljøer][WhatisASE]

Du kan finde flere oplysninger om Azure App Service platformen, se [Azure App Service][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!--Image references-->
[1]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createilbase.png
[2]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createapp.png
[3]: ./media/app-service-environment-with-internal-load-balancer/ilbase-newase.png
[4]: ./media/app-service-environment-with-internal-load-balancer/ilbase-vip.png
[5]: ./media/app-service-environment-with-internal-load-balancer/ilbase-externalvip.png
[6]: ./media/app-service-environment-with-internal-load-balancer/ilbase-ilbcertificate.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[vnetnsgs]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
