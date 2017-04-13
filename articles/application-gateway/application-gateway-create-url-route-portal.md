<properties
   pageTitle="Oprette en sti-baserede regel for et program-gateway ved hjælp af portalen | Microsoft Azure"
   description="Lær at oprette en sti-baserede regel for et program-gateway ved hjælp af portalen"
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

# <a name="create-a-path-based-rule-for-an-application-gateway-by-using-the-portal"></a>Oprette en sti-baserede regel for et program-gateway ved hjælp af portalen

> [AZURE.SELECTOR]
- [Azure-portalen](application-gateway-create-url-route-portal.md)
- [Azure ressourcestyring PowerShell](application-gateway-create-url-route-arm-ps.md)

URL-sti-baserede routing gør det muligt at knytte omdirigerer baseret på URL-sti for HTTP-anmodning. Det kontrollerer, om der er en rute til en back end-puljen, der er konfigureret til listerne over URL-adressen i Application Gateway og send netværkstrafikken til definerede back end-puljen. En almindelig anvendelse af URL-baserede distributionen er at indlæse balance anmodninger om forskellige indholdstyper til anden back end-server grupper.

URL-baserede routing introducerer en ny regeltype til programmet gatewayen. Programmet gateway har to regeltyper: grundlæggende og sti-baserede regler. Grundlæggende regeltype giver round robin-tjeneste til back end-grupper under sti-baserede regler ud over round robin fordeling, også tager sti mønster af URL-anmodningen højde, mens du vælger back end-puljen.

## <a name="scenario"></a>Scenarie

I følgende scenario går til at oprette en sti-baserede regel i et eksisterende program-gateway.
Scenariet antager, at du allerede har fulgt trinnene til at [oprette et program-Gateway](application-gateway-create-gateway-portal.md).

![URL-adresse til distribution][scenario]

## <a name="createrule"></a>Oprette reglen sti-baseret

En Path-baserede regel kræver sin egen lytteren, før oprettelsen af reglen skal du kontrollere har du en tilgængelig lytteren at bruge.

### <a name="step-1"></a>Trin 1

Gå til http://portal.azure.com, og vælg et eksisterende program-gateway. Klik på **regler**

![Programmet Gateway oversigt][1]

### <a name="step-2"></a>Trin 2

Klik på **sti-baserede** knap for at tilføje en ny sti-baserede regel.

### <a name="step-3"></a>Trin 3

Bladet **Tilføj sti-baserede regel** består af to sektioner. Den første sektion er, hvor du har defineret lytteren, navnet på reglen og stien standardindstillingerne. Stien standardindstillingerne er for omdirigerer, der ikke er omfattet brugerdefineret sti-baserede ruten. Den anden del af bladet **Tilføj sti-baserede regel** er, hvor du skal definere reglerne sti-baserede sig selv.

**Grundlæggende indstillinger**

- **Navn** - dette er et fuldt navn til den regel, der er tilgængeligt i portalen.
- **Lytteren** - dette er lytteren, der bruges til reglen.
- **Standard back end-puljen** - denne indstilling er den indstilling, der definerer back end-skal bruges til standard-regel
- **Indstillinger for standard HTTP** - denne indstilling er den indstilling, der definerer indstillingerne HTTP skal bruges til standard-regel.

**Sti-baserede regler**

- **Navn** - dette er et fuldt navn til sti-baserede regel.
- **Stier** - denne indstilling definerer stien reglen søger efter, når du videresender trafik
- **Back end-puljen** - denne indstilling er den indstilling, der definerer back end-skal bruges til reglen
- **HTTP-indstilling** – denne indstilling er den indstilling, der definerer indstillingerne HTTP skal bruges til reglen.

>[AZURE.IMPORTANT] Stier: Listen over stien mønstre til at matche. Hver skal starte med / og det eneste sted en "\*" er tilladt, er i slutningen. Gyldige eksempler er /xyz, /xyz* eller /xyz/*.  

![Tilføje sti-baserede regel blade med oplysninger, der er udfyldt][2]

Tilføjelsen af en path-baserede regel til et eksisterende program-gateway er en nem proces via portalen. Når en path-baserede regel er blevet oprettet, kan den redigeres for at tilføje yderligere regler nemt. 

![føje yderligere sti-baserede regler][3]

## <a name="next-steps"></a>Næste trin

Hvis du vil se, hvordan du konfigurerer se SSL overføre med Azure Application Gateway [Konfigurere SSL Offload](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-url-route-portal/figure1.png
[2]: ./media/application-gateway-create-url-route-portal/figure2.png
[3]: ./media/application-gateway-create-url-route-portal/figure3.png
[scenario]: ./media/application-gateway-create-url-route-portal/scenario.png