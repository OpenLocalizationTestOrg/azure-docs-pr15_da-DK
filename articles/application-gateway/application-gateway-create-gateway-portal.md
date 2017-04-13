<properties
   pageTitle="Oprette et program-gateway ved hjælp af portalen | Microsoft Azure"
   description="Lær, hvordan du opretter et program-Gateway ved hjælp af portalen"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace" />

# <a name="create-an-application-gateway-by-using-the-portal"></a>Oprette et program-gateway ved hjælp af portalen

> [AZURE.SELECTOR]
- [Azure-portalen](application-gateway-create-gateway-portal.md)
- [Azure ressourcestyring PowerShell](application-gateway-create-gateway-arm.md)
- [Azure klassisk PowerShell](application-gateway-create-gateway.md)
- [Azure ressourcestyring skabelon](application-gateway-create-gateway-arm-template.md)
- [Azure CLI](application-gateway-create-gateway-cli.md)

Azure Application Gateway er lag-7 belastningsjustering. Den indeholder failover, ydeevne-routing HTTP-anmodninger mellem forskellige servere, uanset om de er i skyen eller i det lokale miljø. Application Gateway indeholder mange programmet levering Controller (ADC) funktioner, herunder HTTP belastning, cookie-baserede session forbindelse, Secure Sockets Layer (SSL) offload, brugerdefinerede sundhed sonder, understøttelse af flere websted og mange andre. For at finde en komplet liste over understøttede funktioner skal du besøge [Programmet Gateway oversigt](application-gateway-introduction.md)

## <a name="scenario"></a>Scenarie

I dette scenarie skal du få mere at vide, hvordan du opretter et program-gateway ved hjælp af portalen Azure.

Dette scenarie skal:

- Opret en mellemstore programmet gateway med to forekomster.
- Oprette et virtuelt netværk med navnet AdatumAppGatewayVNET med en reserveret CIDR blok med 10.0.0.0/16.
- Oprette et undernet, kaldet Appgatewaysubnet, der bruger 10.0.0.0/28 som dens CIDR blok.
- Konfigurere et certifikat for SSL aflastning.

![Scenarieeksempel][scenario]

>[AZURE.IMPORTANT] Yderligere konfiguration af programmet gatewayen, herunder brugerdefinerede sundhed sonder, back end-puljen adresser og yderligere regler er konfigureret, når programmet gatewayen er konfigureret og ikke under indledende installation.

## <a name="before-you-begin"></a>Inden du går i gang

Azure Application Gateway kræver sin egen undernet. Når du opretter et virtuelt netværk, kan du sikre dig, at du lader plads adresse for at få flere undernet. Når du installerer et program-gateway til en undernet, der kun yderligere program-gateways kan føjes til undernettet.

## <a name="create-the-application-gateway"></a>Oprette gatewayen program

### <a name="step-1"></a>Trin 1

Gå til portalen Azure, klik på **Ny** > **netværk** > **Application Gateway**

![Oprette Application Gateway][1]

### <a name="step-2"></a>Trin 2

Udfyld derefter de grundlæggende oplysninger om programmet gatewayen. Klik på **OK** , når du er færdig

De oplysninger, der bruges til de grundlæggende indstillinger er:

- **Navn** - navnet på gatewayen programmet på computeren.
- **Niveau** – dette er niveauet i programmet gatewayen. To niveauer er tilgængelige, **WAF** og **standardversionen**. WAF aktiverer funktionen web application firewall.
- **SKU størrelse** - denne indstilling er størrelsen af gatewayen programmet, er følgende valgmuligheder (**lille**, **Medium**og **stor**). *Små er ikke tilgængelig, når WAF niveau er valgt*
- **Forekomst Tæl** - antallet af forekomster, denne værdi skal være et tal mellem 2 og 10.
- **Ressourcegruppe** - ressourcegruppen til hold gatewayen programmet på computeren, kan det være en eksisterende ressourcegruppe eller et nyt.
- **Placering** – området af programmet gatewayen, det er den samme placering på ressourcegruppen. *Placeringen er vigtige som det virtuelle netværk og offentlige IP-adresse skal være i samme placering, som gatewayen*.

![blade viser grundlæggende indstillinger][2]

>[AZURE.NOTE] En forekomst antal 1 kan vælges til testformål. Det er vigtigt at vide, at en hvilken som helst forekomst Tæl under to forekomster ikke er omfattet af SERVICEAFTALEN og derfor anbefales ikke. Lille gateways, der skal bruges til Udviklingscenter test og ikke til fremstilling formål.

### <a name="step-3"></a>Trin 3

Når de grundlæggende indstillinger er defineret, er næste trin at definere den virtuelle netværk, der skal bruges. Det virtuelle netværk indeholder det program, der gatewayen programmet indlæser belastningsjustering for.

Klik på **Vælg et virtuelt netværk** for at konfigurere det virtuelle netværk.

![blade med indstillinger for programmet gateway][3]

### <a name="step-4"></a>Trin 4

Klik på **Opret ny** i bladet *Vælg virtuelt netværk*

Mens ikke beskrevet i dette scenario, kan et eksisterende virtuelt netværk vælges på dette tidspunkt.  Hvis en eksisterende virtuelt netværk bruges, er det vigtigt at vide, at det virtuelle netværk kræver en tom undernet eller et undernet kun programmet gateway ressourcer skal bruges.

![Vælg virtuelt netværk blade][4]

### <a name="step-5"></a>Trin 5

Udfyld felterne netværk i bladet **Oprette virtuelle netværk** , som beskrevet i den foregående [scenarie](#scenario) beskrivelse.

![Oprette virtuelle netværk blade med indtastede oplysninger][5]

### <a name="step-6"></a>Trin 6

Når det virtuelle netværk er oprettet, er næste trin at definere den front end IP-adresse til gatewayen programmet på computeren. På dette tidspunkt er valget mellem en offentlig eller en privat IP-adressen til den front end. Valget, afhænger af, om programmet er internet modstående eller interne kun. Dette scenarie antager ved hjælp af en offentlig IP-adresse. Hvis du vil vælge en privat IP-adresse, kan være klikkes på knappen **privat** . En automatisk tildelte IP-adresse er valgt, eller du kan klikke på afkrydsningsfeltet **Vælg en bestemt privat IP-adresse** for at angive et manuelt.

### <a name="step-7"></a>Trin 7

Klik på **Vælg en offentlig IP-adresse**. Hvis en eksisterende offentlige IP-adresse er tilgængelig kan vælges på dette tidspunkt, i dette scenarie du oprette en ny offentlige IP-adresse. Klik på **Opret ny**

![Vælg offentlige IP-adresse blade][6]

### <a name="step-8"></a>Trin 8

Derefter give et fuldt navn til den offentlige IP-adresse, og klik på **OK**

![Oprette offentlige IP-adresse blade][7]

### <a name="step-9"></a>Trin 9

Den sidste indstilling til at konfigurere, når du opretter et program-gateway er lytteren konfiguration.  Hvis **http** bruges, bliver intet tilbage for at konfigurere og kan klikke på **OK** . Hvis du vil bruge **https** yderligere er konfiguration påkrævet.

Hvis du vil bruge **https**, kræves et certifikat. Privat nøgle for certifikatet, der er brug for så en .pfx eksport af certifikat og adgangskoden skal angives.

### <a name="step-10"></a>Trin 10

Klik på **HTTPS**, skal du klikke på **mappeikonet ud for tekstfeltet **overføre PFX certifikat** ** .
Gå til filen .pfx certifikat på filsystemet. Når den er markeret, giv certifikatet et brugervenligt navn, og skriv adgangskoden til .pfx-fil.

Klik på **OK** for at gennemgå indstillingerne for programmet gatewayen, når det er fuldført.

![Lytteren i afsnittet konfiguration på Indstillinger for blade][9]

### <a name="step-11"></a>Trin 11

Gennemgå oversigtssiden, og klik på **OK**.  Gatewayen programmet er nu i kø og oprettet.

### <a name="step-12"></a>Trin 12

Når programmet gatewayen er blevet oprettet, kan du gå til det i portalen for at fortsætte konfigurationen af gatewayen programmet på computeren.

![Programmet Gateway ressourcevisning][10]

Disse trin Opret en grundlæggende programmet gateway med standardindstillingerne for lytteren, back end-puljen, back end-HTTP-indstillinger og regler. Du kan ændre disse indstillinger, så de passer til din installation, når den klargøring er gået igennem.

## <a name="next-steps"></a>Næste trin

Dette scenarie opretter en standard-program gateway. De næste trin er at konfigurere gatewayen program ved at tilføje puljen medlemmer, ændring af indstillinger og justering af regler i gatewayen, så det fungerer korrekt.

Lær at oprette brugerdefinerede sundhed sonder ved at besøge [oprette en brugerdefineret sundhed efterprøvning af af](application-gateway-create-probe-portal.md)

Lær, hvordan du konfigurerer SSL overføre og tage dyrt SSL dekrypteringen fra webserverne ved at besøge [Konfigurere SSL Offload](application-gateway-ssl-portal.md)

Lær, hvordan du beskytter dine programmer med [Firewall til webprogrammer](application-gateway-webapplicationfirewall-overview.md) en funktion for programmet gateway.

<!--Image references-->
[1]: ./media/application-gateway-create-gateway-portal/figure1.png
[2]: ./media/application-gateway-create-gateway-portal/figure2.png
[3]: ./media/application-gateway-create-gateway-portal/figure3.png
[4]: ./media/application-gateway-create-gateway-portal/figure4.png
[5]: ./media/application-gateway-create-gateway-portal/figure5.png
[6]: ./media/application-gateway-create-gateway-portal/figure6.png
[7]: ./media/application-gateway-create-gateway-portal/figure7.png
[8]: ./media/application-gateway-create-gateway-portal/figure8.png
[9]: ./media/application-gateway-create-gateway-portal/figure9.png
[10]: ./media/application-gateway-create-gateway-portal/figure10.png
[scenario]: ./media/application-gateway-create-gateway-portal/scenario.png
