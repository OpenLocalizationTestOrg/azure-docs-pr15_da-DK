

<properties
   pageTitle="Sundhed overvågning oversigt til Azure Application Gateway | Microsoft Azure"
   description="Få mere at vide om overvågningsfunktionerne i Azure Application Gateway"
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

# <a name="application-gateway-health-monitoring-overview"></a>Programmet Gateway sundhed overvågning oversigt

Azure Application Gateway som standard overvåger tilstanden for alle ressourcer i dets back end-puljen og fjerner automatisk alle de ressourcer, der betragtes som beskadiget fra puljen. Application Gateway fortsætter med at overvåge beskadiget forekomster og føjer dem tilbage til sund back end-puljen, når de bliver tilgængelige, og besvare sundhed sonder. Programmet gateway sender tilstanden sonder med den samme port, der er defineret i back end-HTTP-indstillinger. Dette sikrer, at efterprøvning af af tester den samme port, kunder vil bruge til at oprette forbindelse til backend-version.

![programmet gateway efterprøvning af af eksempel][1]

Du kan også tilpasse tilstand efterprøvning af af så den passer til dit program krav ud over at bruge standard sundhed efterprøvning af af overvågning. I denne artikel er både standard og brugerdefinerede sundhed sonder dækket.

## <a name="default-health-probe"></a>Efterprøvning af af standard tilstand

Et program-gateway konfigureres automatisk efterprøvning af af et standard-tilstand, når du ikke konfigurerer en brugerdefineret efterprøvning af af konfiguration. Funktionen overvågning fungerer ved at gøre en HTTP-anmodning til de IP-adresser, der er konfigureret til back end-puljen. Til standard sonder Hvis back end-http indstillinger er konfigureret til HTTPS, bruger efterprøvning af af https samt til at teste tilstanden for en back-end.

For eksempel: du konfigurerer din programmet gateway for at bruge back end-servere A, B og C til at modtage HTTP netværkstrafik på port 80. De tre servere tester standard sundhedsovervågning hver 30 sekunder, før en sund HTTP-svar. En sund HTTP-svar har en [statuskode](https://msdn.microsoft.com/library/aa287675.aspx) mellem 200 og 399.

Hvis standard efterprøvning af af kontrol mislykkes for server A, gatewayen programmet fjerner den fra dets back end-puljen, og netværkstrafik afbrydes til denne server. Standard efterprøvning af af stadig fortsætter med at kontrollere, om serveren en hvert 30. Når server A reagerer korrekt på en anmodning fra efterprøvning af af et standard-tilstand, den er tilføjet tilbage som sund til back end-puljen og trafik starter der flyder til serveren igen.

### <a name="default-health-probe-settings"></a>Efterprøvning af af standardindstillingerne tilstand

|Efterprøvning af af egenskab | Værdi | Beskrivelse|
|---|---|---|
| Efterprøvning af af URL-adresse| http://127.0.0.1:\<port\>/ | URL-sti |
| Interval | 30 | Efterprøvning af af intervallet i sekunder |
| Timeout  | 30 | Efterprøvning af af timeout i sekunder |
| Beskadiget grænseværdi | 3 | Sende forespørgsler antal forsøg. Back-end-serveren er markeret, når efterprøvning af hinanden af fejl ved antallet når beskadiget grænseværdi. |

> [AZURE.NOTE] Port være altid den samme port som back end-HTTP-indstillinger.

Standard efterprøvning af af ser kun på http://127.0.0.1:\<port\> til at bestemme sundhedsstatus. Hvis du vil konfigurere efterprøvning af af den tilstand for at gå til en brugerdefineret URL-adresse eller ændre andre indstillinger, skal du bruge brugerdefinerede sonder, som beskrevet i følgende trin.

## <a name="custom-health-probe"></a>Efterprøvning af af brugerdefinerede tilstand

Brugerdefineret sonder gør det muligt at have en mere detaljeret kontrol over sundhedsovervågning. Når du bruger brugerdefinerede sonder, kan du konfigurere efterprøvning af af intervallet, URL-adressen og stien til at afprøve og hvor mange mislykkedes svar til at acceptere før markeres back end-puljen forekomsten som beskadiget.

### <a name="custom-health-probe-settings"></a>Brugerdefineret sundhed efterprøvning af af indstillinger

Den følgende tabel indeholder definitioner af egenskaberne for en brugerdefineret sundhed efterprøvning af af.

|Efterprøvning af af egenskab| Beskrivelse|
|---|---|
| Navn | Navnet på efterprøvning af af. Dette navn bruges til at referere til efterprøvning af af i back end-HTTP-indstillinger. |
| Protokol | Protokol, der bruges til at sende efterprøvning af af. Efterprøvning af af anvender den protokol, der er defineret i back end-HTTP-indstillinger |
| Host |  Værtsnavn til at sende efterprøvning af af. Gælder kun, når flere websted er konfigureret på Application Gateway, ellers skal du bruge '127.0.0.1'. Dette er anderledes end VM værtsnavn. |
| Sti | Relative stien til efterprøvning af af. Gyldige stien starter fra '/'. |
| Interval | Sende forespørgsler interval i sekunder. Dette er et tidsinterval mellem to efterfølgende sonder.|
| Timeout | Sende forespørgsler timeout i sekunder. Efterprøvning af af er markeret som mislykket, hvis et gyldigt svar inden for denne timeout-periode ikke er modtaget. |
| Beskadiget grænseværdi | Sende forespørgsler antal forsøg. Back-end-serveren er markeret, når efterprøvning af hinanden af fejl ved antallet når beskadiget grænseværdi. |

> [AZURE.IMPORTANT] Hvis Application Gateway er konfigureret for et enkelt websted, som standard værten skal være angivet som '127.0.0.1', medmindre andet er konfigureret i brugerdefineret efterprøvning af af.
En brugerdefineret efterprøvning af af sendes til reference til \<protocol\>://\<host\>:\<port\>\<stien\>. Den port, vil være den samme port, som defineret i back end-HTTP-indstillinger.

## <a name="next-steps"></a>Næste trin

Når du lære mere om Application Gateway sundhedsovervågning, kan du konfigurere en [brugerdefineret sundhed efterprøvning af af](application-gateway-create-probe-portal.md) i portalen Azure eller en [brugerdefineret sundhed efterprøvning af af](application-gateway-create-probe-ps.md) ved hjælp af PowerShell og implementeringsmodel Azure ressourcestyring.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png