<properties
   pageTitle="Flere VIPs til Azure justering af belastning | Microsoft Azure"
   description="Oversigt over flere VIPs på Azure justering af belastning"
   services="load-balancer"
   documentationCenter="na"
   authors="chkuhtz"
   manager="narayan"
   editor=""
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/11/2016"
   ms.author="chkuhtz"
/>

# <a name="multiple-vips-for-azure-load-balancer"></a>Flere VIPs til Azure justering af belastning

Azure belastning giver dig mulighed at indlæse balance tjenester på flere porte, flere IP-adresser eller begge dele. Du kan bruge offentlige og interne Indlæs belastningsjusteringstjenesten definitioner for at indlæse balance flyder på tværs af et sæt af FOS.

I denne artikel beskrives de grundlæggende principper for denne mulighed, vigtige begreber og begrænsninger. Hvis du kun vil have vist tjenester på én IP-adresse, kan du finde forenklet instruktioner til [offentlige](load-balancer-get-started-internet-portal.md) eller [interne](load-balancer-get-started-ilb-arm-portal.md) indlæse belastningsjusteringstjenesten konfigurationer. Tilføjelse af flere VIPs er trinvise til en enkelt VIP konfiguration. Brug af begreberne i denne artikel, kan du udvide en forenklet konfiguration når som helst.

Når du definerer en Azure justering af belastning, er en front end og en back end-konfiguration forbundet med regler. Sundhed efterprøvning af af, der refereres til af reglen bruges til at finde ud af, hvordan nye flyder sendes til en node i back end-puljen. Front end defineres ved en virtuelle IP-adresse (VIP), som er en 3-tupel, består af en IP-adresse (offentlig eller interne), en protokol (UDP eller TCP) og et portnummer. En DIP er en IP-adresse på en Azure virtuelle NIC, der er knyttet til en VM i back end-puljen.

Den følgende tabel indeholder nogle eksempler på frontend konfigurationer:

| VIP | IP-adresse | protokol | port |
|-----|------------|----------|------|
|1|65.52.0.1|TCP|80|
|2|65.52.0.1|TCP|_8080_|
|3|65.52.0.1|_UDP_|80|
|4|_65.52.0.2_|TCP|80|

Tabellen viser fire forskellige frontends. Frontends #1, 2 # og #3 er en enkelt VIP med flere regler. Den samme IP-adresse bruges, men port eller protocol er anderledes ved hver frontend. Frontends #1 og #4 er et eksempel på flere VIPs, hvor samme front end-protokollen og porten igen på tværs af flere VIPs.

Azure belastning giver fleksibilitet i definere belastningsjustering regler. En regel erklærer, hvordan en adresse og port på front end er knyttet til den destinationsadresse og port på back-end. Hvorvidt back end-porte genbruges på tværs af regler, afhænger af typen reglen. Hver type regel har særlige behov, der kan påvirke host konfiguration og efterprøvning af af design. Der findes to typer regler:

1. Standardreglen med genbrug ingen back end-port
2. Flydende IP-reglen hvor back end-porte genbruges

Azure belastning kan du blande begge regeltyper på den samme Indlæs belastningsjusteringstjenesten konfiguration. Justering af belastning kan bruge dem samtidig for en given VM eller en kombination, så længe du overholde begrænsninger på reglen. Hvilke regeltype, du vælger, afhænger af dit program krav og kompleksiteten af understøttende konfigurationen. Du skal evaluere, hvilke regeltyper er mest velegnet til scenariet.

Vi nærmere på følgende scenarier yderligere ved at starte med standardfunktionsmåden.

## <a name="rule-type-1-no-backend-port-reuse"></a>Regel typen #1: ingen back end-port genbrug

![MultiVIP Illustration](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

I dette scenarie skal konfigureres front end-VIPs på følgende måde:

| VIP | IP-adresse | protokol | port |
|-----|------------|----------|------|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1|65.52.0.1|TCP|80|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2|*65.52.0.2*|TCP|80|

DIP er destinationen for den indgående strøm. I back end-puljen Fremviser hver VM tjenesten ønskede på en entydig port på en DIP. Denne tjeneste er knyttet til front end via en regel definition.

Vi definere to regler:

| Regel | Tilknytte front end | Til back end-puljen |
|------|--------------|-----------------|
| 1 | ![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) VIP1:80 | ![backend-version](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![backend-version](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 | ![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) VIP2:80 | ![backend-version](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![backend-version](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

Fuldført tilknytning i Azure justering af belastning er nu på følgende måde:

| Regel | VIP IP-adresse | protokol | port | Destination | port |
|------|----------------|----------|------|-----|------|
|![regel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1|65.52.0.1|TCP|80|DIP IP-adresse|80|
|![regel](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2|65.52.0.2|TCP|80|DIP IP-adresse|81|

Hver regel skal give et rutediagram med en entydig kombination af destination IP-adresse og destinationsport. Flere regler kan levere flyder til den samme DIP til forskellige porte ved varierende destinationsporten af strømmen.

Sundhed sonder dirigeres altid til DIP af en VM. Du skal sikre, at du, at din efterprøvning af af afspejler tilstanden af VM.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Regel typen #2: back end-port genbrug ved hjælp af flydende IP-adresse

Azure justering af belastning er muligt at genbruge front end-porten på tværs af flere VIPs uanset regel bruges. Desuden kan visse scenarier med programmet foretrækker eller kræver den samme port der skal bruges i flere forekomster af tjenesteprogrammer på en enkelt VM i back end-puljen. Almindelige eksempler på port genbrug omfatter databaseserver for høj tilgængelighed, netværk virtuelle anvendelser og eksponere flere TLS slutpunkter uden fornyet kryptering.

Hvis du vil genbruge back end-porten på tværs af flere regler, skal du aktivere flydende IP-adresse i reglens definition.

Flydende IP-adresse er en del af det, der kendes som direkte Server returnere (Dti). Dti består af to dele: en flow topologi og en IP-tilknytning farveskema. På et platformniveau fungerer Azure justering af belastning altid i en Dti flow topologi uanset om flydende IP-adresse er aktiveret eller ej. Det betyder, at den udgående del af et flow er altid korrekt omskrives for at dataflow direkte tilbage til origin.

Med regeltype standard Fremviser Azure en traditionel IP-adresse tilknytning farveskema for nemmere at bruge af belastning. Aktivere flydende IP-ændres IP-adresse tilknytning farveskema til Tillad ekstra fleksibilitet som beskrevet nedenfor.

I følgende diagram vises denne konfiguration:

![MultiVIP Illustration](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

I dette scenarie har hver VM i back end-puljen tre netværksgrænseflader:

* DIP: et virtuelt NIC tilknyttet VM (Azure's NIC ressource)
* VIP1: en tilbagekoblingsgrænseflade i gæst OS, der er konfigureret med IP-adressen på VIP1
* VIP2: en tilbagekoblingsgrænseflade i gæst OS, der er konfigureret med IP-adressen på VIP2

>[AZURE.IMPORTANT] Konfiguration af de logiske grænseflader udføres i gæst OS. Denne konfiguration er ikke udføres eller administreres af Azure. Reglerne fungerer ikke uden denne konfiguration. Sundhed efterprøvning af af definitioner Brug DIP af VM i stedet for de logiske VIP. Derfor skal din tjeneste angive efterprøvning af af svar på en DIP port, der viser status for tjenesten tilbydes på den logiske VIP.

Lad os antage den samme front end-konfiguration som i det forrige scenario:

| VIP | IP-adresse | protokol | port |
|-----|------------|----------|------|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1|65.52.0.1|TCP|80|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2|*65.52.0.2*|TCP|80|

Vi definere to regler:

| Regel | Tilknytte front end | Til back end-puljen |
|------|--------------|-----------------|
| 1 | ![regel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) VIP1:80 | ![backend-version](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) VIP1:80 (i VM1 og VM2) |
| 2 | ![regel](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) VIP2:80 | ![backend-version](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) VIP2:80 (i VM1 og VM2) |

Den følgende tabel viser den fulde tilknytning i justering af belastning:

| Regel | VIP IP-adresse | protokol | port | Destination | port |
|------|----------------|----------|------|-------------|------|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1|65.52.0.1|TCP|80|samme som VIP (65.52.0.1)|samme som VIP (80)|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2|65.52.0.2|TCP|80|samme som VIP (65.52.0.2)|samme som VIP (80)|

Destinationen for den indgående strøm er VIP-adresse på tilbagekoblingsgrænseflade i VM. Hver regel skal give et rutediagram med en entydig kombination af destination IP-adresse og destinationsport. Port genbrug er muligt på den samme VM som varierende destination IP-adresse med forløb. Din tjeneste, der vises til justering af belastning ved at binde den til VIP IP-adresse og port af de respektive tilbagekoblingsgrænseflade.

Bemærk, at dette eksempel ikke ændrer destinationsporten. Selvom det er et scenarie med flydende IP-, understøtter Azure justering af belastning også definerer en regel, at omskrive destinationsporten back end- og for at gøre det adskiller sig fra destinationsporten front end.

Regeltype flydende IP-adresse er grundlaget for flere Indlæs belastningsjusteringstjenesten konfiguration mønstre. Et eksempel, der er tilgængelig i øjeblikket er [SQL AlwaysOn med flere lyttere](../virtual-machines/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) konfigurationen. Over tid, kan vi dokumenterer flere af disse scenarier.

## <a name="limitations"></a>Begrænsninger

* Flere VIP konfigurationer understøttes kun med IaaS FOS.
* Dit program skal bruge DIP for udgående flyder med den flydende IP-regel. Hvis dit program binder til den VIP-adresse, der er konfigureret på tilbagekoblingsgrænseflade i gæst OS, derefter SNAT er ikke tilgængelig for omskrive udgående strømmen og strømmen mislykkes.
* Offentlige IP-adresser har en effekt på fakturering. Du kan finde flere oplysninger, se [IP-adresse priser](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Abonnement begrænsninger gælder. Yderligere oplysninger finder du i [Service begrænsninger](../azure-subscription-service-limits.md#networking-limits) få mere at vide.
