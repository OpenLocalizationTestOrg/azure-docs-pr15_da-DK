<properties
   pageTitle="Oprette en brugerdefineret efterprøvning af af til et program-gateway ved hjælp af portalen | Microsoft Azure"
   description="Lær at oprette en brugerdefineret efterprøvning af af til Application Gateway ved hjælp af portalen"
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

# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Oprette en brugerdefineret efterprøvning af af til Application Gateway ved hjælp af portalen

> [AZURE.SELECTOR]
- [Azure-portalen](application-gateway-create-probe-portal.md)
- [Azure ressourcestyring PowerShell](application-gateway-create-probe-ps.md)
- [Azure klassisk PowerShell](application-gateway-create-probe-classic-ps.md)

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

## <a name="scenario"></a>Scenarie

I følgende scenario går til at oprette en brugerdefineret sundhed efterprøvning af af i et eksisterende program-gateway.
Scenariet antager, at du allerede har fulgt trinnene til at [oprette et program-Gateway](application-gateway-create-gateway-portal.md).

## <a name="createprobe"></a>Oprette efterprøvning af af

Sonder er konfigureret i en proces via portalen. Det første trin er at oprette efterprøvning af af, næste du tilføje efterprøvning af af til back end-HTTP-indstillinger for programmet gateway.

### <a name="step-1"></a>Trin 1

Gå til http://portal.azure.com, og vælg et eksisterende program-gateway.

![Programmet Gateway oversigt][1]

### <a name="step-2"></a>Trin 2

Klik på **sonder** , og klik på knappen **Tilføj** for at tilføje en ny efterprøvning af af.

![Tilføje efterprøvning af af blade med oplysninger, der er udfyldt][2]

### <a name="step-3"></a>Trin 3

Udfyld de nødvendige oplysninger om efterprøvning af af, og klik på **OK**, når du er færdig.

- **Navn** - dette er et fuldt navn til den efterprøvning af af, der er tilgængeligt i portalen.
- **Host** - dette er den værtsnavn, der bruges til efterprøvning af af. Gælder kun, når flere websted er konfigureret på Application Gateway, ellers skal du bruge '127.0.0.1'. Dette er anderledes end VM værtsnavn.
- **Sti** - resten af den fulde URL-adressen til den brugerdefinerede efterprøvning af af. En gyldig sti starter med '/'
- **Opdateringsinterval (sek.)** -, hvor ofte efterprøvning af af køre for at kontrollere, om tilstand. Det anbefales ikke at angive nederst end 30 sekunder.
- **Timeout (sek.)** – mængden tid, efterprøvning af af venter, før timeout. Interval for timeout skal være høj, der kan foretages en http-opkald til at sikre, at siden back end-tilstand er tilgængelig.
- **Beskadiget grænseværdi** - antallet af mislykkede forsøg skal betragtes som beskadiget. En grænse på 0 betyder, at hvis en tilstand markerer opstår back end-afhænger beskadiget med det samme.

> [AZURE.IMPORTANT] Værtsnavnet er ikke navnet på serveren. Dette er navnet på den virtuelle værter kører på application server. Efterprøvning af af sendes til http://(host name):(port from httpsetting)/urlPath

![efterprøvning af af konfigurationsindstillinger][3]

## <a name="add-probe-to-the-gateway"></a>Føje efterprøvning af af til gatewayen

Nu, hvor efterprøvning af af er blevet oprettet, er det tid at føje det til gatewayen. Efterprøvning af af indstillinger er angivet på siden back end-HTTP-indstillinger for programmet gateway.

### <a name="step-1"></a>Trin 1

Klik på **http-indstillinger** for gateway programmet, og klik derefter på de aktuelle back end-http-indstillingerne i vinduet til at få vist bladet konfiguration.

![HTTPS vindue med indstillinger][4]

### <a name="step-2"></a>Trin 2

Klik på **brugerdefineret efterprøvning af brug af** bladet **appGatewayBackEndHttp** indstillinger, og vælg efterprøvning af af, der er oprettet i afsnittet [oprette efterprøvning af af](#createprobe) .
Når du er færdig, skal du klikke på **OK** og indstillingerne er anvendt.

![appgatewaybackend indstillinger blade][5]

Standard efterprøvning af af kontrollerer standardadgang til webprogrammet. Nu, hvor en brugerdefineret efterprøvning af af er blevet oprettet, bruges gatewayen programmet den brugerdefinerede sti, der er defineret til at overvåge systemtilstand til back-end, der er markeret. Baseret på de kriterier, der er defineret, tjekker gatewayen programmet den fil, der er angivet i efterprøvning af af. Hvis opkaldet til vært: Port / sti returnerer ikke en Http 200 OK status svar, serveren hentes af rotation, når den beskadiget grænse er nået. Test fortsætter på forekomsten af beskadiget til at finde ud af, når det bliver sund igen. Når forekomsten er tilføjet tilbage til sund server puljen begynder trafikken der flyder til den igen, og test på forekomsten af fortsætter med bruger angivet interval som normalt.


## <a name="next-steps"></a>Næste trin

Hvis du vil se, hvordan du konfigurerer se SSL overføre med Azure Application Gateway [Konfigurere SSL Offload](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[3]: ./media/application-gateway-create-probe-portal/figure3.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png