<properties
   pageTitle="Oprette et program-gateway med firewall til webprogrammer ved hjælp af portalen | Microsoft Azure"
   description="Lær, hvordan du opretter et program-Gateway med firewall til webprogrammer ved hjælp af portalen"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"
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

# <a name="create-an-application-gateway-with-web-application-firewall-by-using-the-portal"></a>Oprette et program-gateway med firewall til webprogrammer ved hjælp af portalen

> [AZURE.SELECTOR]
- [Azure-portalen](application-gateway-web-application-firewall-portal.md)
- [Azure ressourcestyring PowerShell](application-gateway-web-application-firewall-powershell.md)

Web application firewallen (WAF) i Azure Application Gateway beskytter webprogrammer fra fælles webbaserede angreb som SQL-indsættelse, angreb via scripts på tværs af websteder og session hijacks. Webprogram beskytter mod mange af de OWASP øverste 10 almindelige web svagheder.

Azure Application Gateway er lag-7 belastningsjustering. Den indeholder failover, ydeevne-routing HTTP-anmodninger mellem forskellige servere, uanset om de er i skyen eller i det lokale miljø.
Programmet indeholder mange programmet levering Controller (ADC) funktioner, herunder HTTP belastning, cookie-baserede session forbindelse, Secure Sockets Layer (SSL) offload, brugerdefinerede sundhed sonder, understøttelse af flere websted og mange andre.
For at finde en komplet liste over understøttede funktioner skal du besøge [Programmet Gateway oversigt](application-gateway-introduction.md)

## <a name="scenarios"></a>Scenarier

I denne artikel er der to scenarier:

I det første scenario, kan du lære at [tilføje firewall til webprogrammer til et eksisterende program-gateway](#add-web-application-firewall-to-an-existing-application-gateway).

I det andet scenarie skal lære du at [oprette et program-gateway med firewall til webprogrammer](#create-an-application-gateway-with-web-application-firewall)

![Scenarieeksempel][scenario]

>[AZURE.NOTE] Yderligere konfiguration af programmet gatewayen, herunder brugerdefinerede sundhed sonder, back end-puljen adresser og yderligere regler er konfigureret, når programmet gatewayen er konfigureret og ikke under indledende installation.

## <a name="before-you-begin"></a>Inden du går i gang

Azure Application Gateway kræver sin egen undernet. Når du opretter et virtuelt netværk, kan du sikre dig, at du lader plads adresse for at få flere undernet. Når du installerer et program-gateway til en undernet, der kun yderligere program-gateways kan føjes til undernettet.

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Føje firewall til webprogrammer til et eksisterende program-gateway

Dette scenarie opdaterer en eksisterende programmet gateway for at understøtte firewall til webprogrammer i forebyggelse tilstand.

### <a name="step-1"></a>Trin 1

Gå til portalen Azure skal du vælge en eksisterende Application Gateway.

![Oprette Application Gateway][1]

### <a name="step-2"></a>Trin 2

Klik på **konfiguration** , og Opdater gatewayen programindstillinger. Klik på **Gem** , når du er færdig

Indstillingerne for at opdatere et eksisterende program-gateway for at understøtte firewall til webprogrammer er:

- **Niveau** – det niveau, der er markeret, skal være **WAF** til at understøtte firewall til webprogrammer
- **SKU størrelse** - denne indstilling er størrelsen af gatewayen program med firewall til webprogrammer, tilgængelige indstillinger (**Medium** og **stor**).
- **Status for firewall** – denne indstilling deaktiverer eller aktiverer firewall til webprogrammer.
- **Firewall tilstand** - denne indstilling er, hvordan firewall til webprogrammer omhandler skadelig trafik. Tilstanden **registrering** gemmes kun de hændelser, hvor **forebyggelse** tilstand logger begivenheder og stopper skadelig trafikken.

![blade viser grundlæggende indstillinger][2]

>[AZURE.NOTE] For at få vist web application firewall logfiler, der skal være aktiveret diagnosticering og ApplicationGatewayFirewallLog markeret. En forekomst antal 1 kan vælges til testformål. Det er vigtigt at vide, at en hvilken som helst forekomst Tæl under to forekomster ikke er omfattet af SERVICEAFTALEN og derfor anbefales ikke. Lille gateways er ikke tilgængelige, når du bruger firewall til webprogrammer.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>Oprette et program-gateway med firewall til webprogrammer

Dette scenarie skal:

- Opret en mellemstore web application firewall programmet gateway med to forekomster.
- Oprette et virtuelt netværk med navnet AdatumAppGatewayVNET med en reserveret CIDR blok med 10.0.0.0/16.
- Oprette et undernet, kaldet Appgatewaysubnet, der bruger 10.0.0.0/28 som dens CIDR blok.
- Konfigurere et certifikat for SSL aflastning.

### <a name="step-1"></a>Trin 1

Gå til portalen Azure, klik på **Ny** > **netværk** > **Application Gateway**

![Oprette Application Gateway][1-1]

### <a name="step-2"></a>Trin 2

Udfyld derefter de grundlæggende oplysninger om programmet gatewayen. Sørg for at vælge **WAF** som niveauet. Klik på **OK** , når du er færdig

De oplysninger, der bruges til de grundlæggende indstillinger er:

- **Navn** - navnet på gatewayen programmet på computeren.
- **Niveau** – niveauet i gatewayen programmet tilgængelige indstillinger er (**Standard** og **WAF**). Firewall til webprogrammer er kun tilgængelig i WAF lag.
- **SKU størrelse** - denne indstilling er størrelsen af gatewayen programmet, er følgende valgmuligheder (**Medium** og **stor**).
- **Forekomst Tæl** - antallet af forekomster, denne værdi skal være et tal mellem **2** og **10**.
- **Ressourcegruppe** - ressourcegruppen til hold gatewayen programmet på computeren, kan det være en eksisterende ressourcegruppe eller et nyt.
- **Placering** – området af programmet gatewayen, det er den samme placering på ressourcegruppen. *Placeringen er vigtige som det virtuelle netværk og offentlige IP-adresse skal være i samme placering, som gatewayen*.

![blade viser grundlæggende indstillinger][2-2]

>[AZURE.NOTE] En forekomst antal 1 kan vælges til testformål. Det er vigtigt at vide, at en hvilken som helst forekomst Tæl under to forekomster ikke er omfattet af SERVICEAFTALEN og derfor anbefales ikke. Lille gateways understøttes ikke for web application firewall scenarier.

### <a name="step-3"></a>Trin 3

Når de grundlæggende indstillinger er defineret, er næste trin at definere den virtuelle netværk, der skal bruges. Det virtuelle netværk indeholder det program, der gatewayen programmet indlæser belastningsjustering for.

Klik på **Vælg et virtuelt netværk** for at konfigurere det virtuelle netværk.

![blade med indstillinger for programmet gateway][3]

### <a name="step-4"></a>Trin 4

Klik på **Opret ny** i bladet **Vælg virtuelt netværk**

Mens ikke beskrevet i dette scenario, kan et eksisterende virtuelt netværk vælges på dette tidspunkt.  Hvis en eksisterende virtuelt netværk bruges, er det vigtigt at vide, at det virtuelle netværk kræver en tom undernet eller et undernet kun programmet gateway ressourcer skal bruges.

![Vælg virtuelt netværk blade][4]

### <a name="step-5"></a>Trin 5

Udfyld felterne netværk i bladet **Oprette virtuelle netværk** , som beskrevet i den foregående [scenarie](#scenario) beskrivelse.

![Oprette virtuelle netværk blade med indtastede oplysninger][5]

### <a name="step-6"></a>Trin 6

Når det virtuelle netværk er oprettet, er næste trin at definere den front end IP-adresse til gatewayen programmet på computeren. På dette tidspunkt er valget mellem en offentlig eller en privat IP-adressen til den front end. Valget, afhænger af, om programmet er internet modstående eller interne kun. Dette scenarie antager ved hjælp af en offentlig IP-adresse. Hvis du vil vælge en privat IP-adresse, kan være klikkes på knappen **privat** . En automatisk tildelte IP-adresse er valgt, eller du kan klikke på afkrydsningsfeltet **Vælg en bestemt privat IP-adresse** for at angive et manuelt.

Klik på **Vælg en offentlig IP-adresse**. Hvis en eksisterende offentlige IP-adresse er tilgængelig kan vælges på dette tidspunkt, i dette scenarie du oprette en ny offentlige IP-adresse. Klik på **Opret ny**

![Vælg offentlige IP-adresse blade][6]

### <a name="step-7"></a>Trin 7

Derefter give et fuldt navn til den offentlige IP-adresse, og klik på **OK**

![Oprette offentlige IP-adresse blade][7]

### <a name="step-8"></a>Trin 8

Derefter skal konfigurere du Lytterkonfigurationen.  Hvis **http** bruges, bliver intet tilbage for at konfigurere og kan klikke på **OK** . Hvis du vil bruge **https** yderligere er konfiguration påkrævet.

Hvis du vil bruge **https**, kræves et certifikat. Privat nøgle for certifikatet, der er behov, så en .pfx eksport af certifikat behov der skal gives og adgangskoden til fil.

Klik på **HTTPS**, skal du klikke på **mappeikonet ud for tekstfeltet **overføre PFX certifikat** ** .
Gå til filen .pfx certifikat på filsystemet. Når den er markeret, giv certifikatet et brugervenligt navn, og skriv adgangskoden til .pfx-fil.

Klik på **OK** for at gennemgå indstillingerne for programmet gatewayen, når det er fuldført.

![Lytteren i afsnittet konfiguration på Indstillinger for blade][8]

### <a name="step-9"></a>Trin 9

Konfigurere **WAF** specifikke indstillinger.

- **Status for firewall** – denne indstilling slår WAF til eller fra.
- **Firewall tilstand** - denne indstilling bestemmer handlinger WAF tager på skadelig trafik. Hvis **registrering** er valgt, logføres kun trafik.  Hvis **forebyggelse** er valgt, er trafik logget og ikke længere med en 403 uautoriseret.

![indstillinger for firewall et webprogram][9]

### <a name="step-10"></a>Trin 10

Gennemgå oversigtssiden, og klik på **OK**.  Gatewayen programmet er nu i kø og oprettet.

### <a name="step-12"></a>Trin 12

Når programmet gatewayen er blevet oprettet, kan du gå til det i portalen for at fortsætte konfigurationen af gatewayen programmet på computeren.

![Programmet Gateway ressourcevisning][10]

Disse trin Opret en grundlæggende programmet gateway med standardindstillingerne for lytteren, back end-puljen, back end-HTTP-indstillinger og regler. Du kan ændre disse indstillinger, så de passer til din installation, når den klargøring er gået igennem

## <a name="next-steps"></a>Næste trin

Lær, hvordan du kan konfigurere logføring af diagnostik, hvis du vil logge de hændelser, der er fundet eller forhindret med Firewall til webprogrammer ved at besøge [Programmet Gateway diagnosticering](application-gateway-diagnostics.md)

Lær at oprette brugerdefinerede sundhed sonder ved at besøge [oprette en brugerdefineret sundhed efterprøvning af af](application-gateway-create-probe-portal.md)

Lær, hvordan du konfigurerer SSL overføre og tage dyrt SSL dekrypteringen fra webserverne ved at besøge [Konfigurere SSL Offload](application-gateway-ssl-portal.md)

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[1-1]: ./media/application-gateway-web-application-firewall-portal/figure1-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[4]: ./media/application-gateway-web-application-firewall-portal/figure4.png
[5]: ./media/application-gateway-web-application-firewall-portal/figure5.png
[6]: ./media/application-gateway-web-application-firewall-portal/figure6.png
[7]: ./media/application-gateway-web-application-firewall-portal/figure7.png
[8]: ./media/application-gateway-web-application-firewall-portal/figure8.png
[9]: ./media/application-gateway-web-application-firewall-portal/figure9.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png