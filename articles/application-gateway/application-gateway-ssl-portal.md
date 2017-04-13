<properties
   pageTitle="Konfigurere et program-gateway for SSL aflastning ved hjælp af portalen | Microsoft Azure"
   description="Denne side indeholder instruktioner til at oprette et program-gateway med SSL offload ved hjælp af portalen"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/09/2016"
   ms.author="gwallace"/>

# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-portal"></a>Konfigurere et program-gateway for SSL aflastning ved hjælp af portalen

> [AZURE.SELECTOR]
-[Azure portal](application-gateway-ssl-portal.md)
-[Azure ressourcestyring PowerShell](application-gateway-ssl-arm.md)
-[Azure klassisk PowerShell](application-gateway-ssl.md)

Azure Application Gateway kan konfigureres til at afslutte sessionen Secure Sockets Layer (SSL) på din gateway for at undgå dyrt SSL dekryptering opgaver for at opstå på webfarmen. SSL aflastning forenkler også front end-server-konfiguration og administration af webprogrammet.

## <a name="scenario"></a>Scenarie

I følgende scenario går gennem konfigurationen af SSL offload på et eksisterende program-gateway. Scenariet antager, at du allerede har fulgt trinnene til at [oprette et program-Gateway](application-gateway-create-gateway-portal.md).

## <a name="before-you-begin"></a>Inden du går i gang

Hvis du vil konfigurere SSL aflastning med et program-gateway, kræves et certifikat. Dette certifikat er indlæst på gatewayen programmet og bruges til at kryptere og dekryptere trafikken udsendes via SSL. Certifikatet, der skal være i formatet Personal Information Exchange (pfx). I dette filformat giver mulighed for privat nøgle der skal eksporteres som kræves af gatewayen programmet på computeren til at udføre kryptering og dekryptering af trafik.

## <a name="add-an-https-listener"></a>Tilføje en HTTPS-lytteren

HTTPS lytteren søger efter trafik baseret på konfigurationen, og gør det muligt at omdirigere trafikken til back end-grupper.

### <a name="step-1"></a>Trin 1

Gå til Azure-portalen, og vælg et eksisterende program-gateway

### <a name="step-2"></a>Trin 2

Klik på lyttere, og klik på knappen Tilføj for at tilføje en lytter.

![App gateway oversigt blade][1]

### <a name="step-3"></a>Trin 3

Udfyld de nødvendige oplysninger for lytteren og Overfør .pfx certifikatet, når du er færdig skal du klikke på OK.

**Navn** - denne værdi er et fuldt navn for lytteren.

**Front end IP-konfiguration** – denne værdi er den front end IP-konfiguration, der bruges til lytteren.

**Front end-port (navn/Port)** - et fuldt navn til den port, der bruges på front-end i programmet gatewayen og den faktiske port bruges.

**Protocol** - en parameter til at bestemme Hvis https eller http bruges til front end.

**Certifikat (navn/adgangskode)** – Hvis SSL aflastning bruges, et .pfx certifikat er påkrævet for denne indstilling, og der kræves et brugervenligt navn og en adgangskode.

![tilføje lytteren blade][2]

## <a name="create-a-rule-and-associate-it-to-the-listener"></a>Oprette en regel og knytte dem til lytteren

Lytteren er blevet oprettet. Det er tid til at oprette en regel til at håndtere trafik fra lytteren.

### <a name="step-1"></a>Trin 1

Klik på **regler** for gatewayen programmet, og klik derefter på Tilføj.

![App gateway regler blade][3]

### <a name="step-2"></a>Trin 2

Skrive i det fulde navn til reglen bladet **Tilføj grundlæggende regel** , og vælg lytteren oprettet i ovenstående trin. Vælg den relevante back end-puljen og HTTP-indstilling, og klik på **OK**

![HTTPS vindue med indstillinger][4]

Indstillingerne er nu gemt gatewayen programmet på computeren. Lagringen for disse indstillinger kan tage et stykke tid, før de er tilgængelige til at få vist via portalen eller via PowerShell. Når du har gemt gatewayen programmet håndterer kryptering og dekryptering af trafik. Al trafik mellem gatewayen programmet og back end-webserverne skal håndteres via http. Al kommunikation tilbage til klientprogrammet, hvis startet over https kommer tilbage til den klient, der er krypteret.

## <a name="next-steps"></a>Næste trin

For at lære at konfigurere et brugerdefineret sundhed efterprøvning af af med Azure Application Gateway, skal du se [oprette en brugerdefineret sundhed efterprøvning af af](application-gateway-create-gateway-portal.md).

[1]: ./media/application-gateway-ssl-portal/figure1.png
[2]: ./media/application-gateway-ssl-portal/figure2.png
[3]: ./media/application-gateway-ssl-portal/figure3.png
[4]: ./media/application-gateway-ssl-portal/figure4.png