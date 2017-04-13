<properties
   pageTitle="Fejlfinding af programmet Gateway ugyldig Gateway (502) | Microsoft Azure"
   description="Lær, hvordan du foretager fejlfinding af programmet Gateway 502 fejl"
   services="application-gateway"
   documentationCenter="na"
   authors="amitsriva"
   manager="rossort"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/02/2016"
   ms.author="amitsriva" />

# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Ugyldig gateway fejlfinding i Application Gateway

## <a name="overview"></a>Oversigt

Når du har konfigureret en Azure Application Gateway, er en af fejl som brugere kan støde på "Serverfejl: 502 - webserver modtaget et ugyldigt svar under som en gateway eller proxy-server". Dette kan ske på grund af følgende primære årsager:

- Azure Application Gateway's back end-puljen er ikke konfigureret eller tom.
- Ingen af FOS eller forekomster i VM skala angive er sund.
- Back end-FOS eller forekomster af VM skala angive svarer ikke til standard sundhed efterprøvning af af.
- Ugyldige eller forkert konfiguration af brugerdefineret sundhed sonder.
- Anmode om timeout eller forbindelsesproblemer med anmodninger.

## <a name="empty-backendaddresspool"></a>Tom BackendAddressPool

### <a name="cause"></a>Årsag

Hvis programmet gatewayen har ingen FOS eller VM skala angive konfigureret i puljen back end-adresse, kan ikke distribuere en hvilken som helst kunden har anmodet om og viser en ugyldig gateway-fejl.

### <a name="solution"></a>Løsning

Sørg for, at puljen back end-adresse ikke er tom. Dette kan gøres enten via PowerShell, CLI eller portal.

    Get-AzureRmApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"

Output fra den foregående cmdlet skal indeholde adresse for ikke-tomme back end-puljen. Følgende er et eksempel, hvor to grupper der skal returneres, som er konfigureret med fulde Domænenavn eller IP-adresser til back end-FOS. Tilstanden klargøring af BackendAddressPool skal være 'lykkedes'.
    
    BackendAddressPoolsText: 
            [{
                "BackendAddresses": [{
                    "ipAddress": "10.0.0.10",
                    "ipAddress": "10.0.0.11"
                }],
                "BackendIpConfigurations": [],
                "ProvisioningState": "Succeeded",
                "Name": "Pool01",
                "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
                "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
            }, {
                "BackendAddresses": [{
                    "Fqdn": "xyx.cloudapp.net",
                    "Fqdn": "abc.cloudapp.net"
                }],
                "BackendIpConfigurations": [],
                "ProvisioningState": "Succeeded",
                "Name": "Pool02",
                "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
                "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
            }]


## <a name="unhealthy-instances-in-backendaddresspool"></a>Beskadiget forekomster i BackendAddressPool

### <a name="cause"></a>Årsag

Hvis alle forekomster af BackendAddressPool er beskadiget, vil Application Gateway ikke har en hvilken som helst back-end for at distribuere bruger Anmod om at. Dette kan også være sag, når back end-forekomster er sund, men ikke har det nødvendige program, der er installeret.

### <a name="solution"></a>Løsning

Sikre, at antallet er sund og programmet er konfigureret korrekt. Kontrollere, om back end-forekomster er kunne svare på en ping fra en anden VM i den samme VNet. Hvis konfigureret med en offentlig slutpunkt, kan du sikre, at en browser anmodning om at webprogrammet er funktionsdygtige.

## <a name="problems-with-default-health-probe"></a>Problemer med standard sundhed efterprøvning af af

### <a name="cause"></a>Årsag

502 fejl kan også være hyppige indikatorer, efterprøvning af af med standard tilstand ikke er adgang til back end-FOS. Når en forekomst af Application Gateway er klargjort, konfigureres automatisk en standard sundhed efterprøvning af af til hver BackendAddressPool ved hjælp af egenskaberne for BackendHttpSetting. Ingen brugerinput kræves for at angive denne efterprøvning af af. Specifikt, når en regel til justering af belastning er konfigureret, oprettes en tilknytning mellem en BackendHttpSetting og BackendAddressPool. Efterprøvning af af et standard er konfigureret for hver af disse tilknytninger og Application Gateway starter en periodiske sundhed forbindelse til hver forekomst i BackendAddressPool på den angivne i elementet BackendHttpSetting port. Følgende tabel vises de værdier, der er knyttet til standard sundhed efterprøvning af af.


|Efterprøvning af af egenskab | Værdi | Beskrivelse|
|---|---|---|
| Efterprøvning af af URL-adresse| http://127.0.0.1/ | URL-sti |
| Interval | 30 | Efterprøvning af af intervallet i sekunder |
| Timeout  | 30 | Efterprøvning af af timeout i sekunder |
| Beskadiget grænseværdi | 3 | Sende forespørgsler antal forsøg. Back-end-serveren er markeret, når efterprøvning af hinanden af fejl ved antallet når beskadiget grænseværdi. |

### <a name="solution"></a>Løsning

- Sikre, at en standard-webstedet er konfigureret og lytter på 127.0.0.1.
- Hvis BackendHttpSetting angiver en port end 80, skal være konfigureret standard-webstedet for at lytte på denne port.
- Opkaldet til http://127.0.0.1:port skal returnere en HTTP Resultatkode på 200. Dette skal returneres inden for 30 sek timeout-perioden.
- Kontrollér, at port konfigureret er åbent og, der er ingen firewallregler eller Azure netværk sikkerhedsgrupper, som blokerer indgående eller udgående trafik på porten konfigureret.
- Hvis Azure klassisk FOS eller skybaseret tjeneste bruges sammen med fulde Domænenavn eller offentlige IP-adresse, kan du sikre dig, at det tilsvarende [slutpunkt](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md) åbnes.
- Hvis VM er konfigureret via Azure ressourcestyring og er uden for den VNet, hvor Application Gateway er installeret, skal være konfigureret [Netværk sikkerhedsgruppe](../virtual-network/virtual-networks-nsg.md) for at tillade adgang på den ønskede port.


## <a name="problems-with-custom-health-probe"></a>Problemer med brugerdefinerede sundhed efterprøvning af af

### <a name="cause"></a>Årsag

Brugerdefineret sundhed sonder giver yderligere mulighed for at teste funktionsmåde standard. Når du bruger brugerdefinerede sonder, kan brugere konfigurere efterprøvning af af intervallet, URL-adressen, og stien til at teste og hvor mange mislykkedes svar til at acceptere før markeres back end-puljen forekomsten som beskadiget. Følgende yderligere egenskaber tilføjes.

|Efterprøvning af af egenskab| Beskrivelse|
|---|---|
| Navn | Navnet på efterprøvning af af. Dette navn bruges til at referere til efterprøvning af af i back end-HTTP-indstillinger. |
| Protokol | Protokol, der bruges til at sende efterprøvning af af. Efterprøvning af af anvender den protokol, der er defineret i back end-HTTP-indstillinger |
| Host |  Værtsnavn til at sende efterprøvning af af. Gælder kun, når flere websted er konfigureret på Application Gateway. Dette er anderledes end VM værtsnavn.  |
| Sti | Relative stien til efterprøvning af af. Gyldige stien starter fra '/'. Efterprøvning af af sendes til \<protocol\>://\<host\>:\<port\>\<sti\> |
| Interval | Sende forespørgsler interval i sekunder. Dette er et tidsinterval mellem to efterfølgende sonder.|
| Timeout | Sende forespørgsler timeout i sekunder. Efterprøvning af af er markeret som mislykket, hvis et gyldigt svar inden for denne timeout-periode ikke er modtaget. |
| Beskadiget grænseværdi | Sende forespørgsler antal forsøg. Back-end-serveren er markeret, når efterprøvning af hinanden af fejl ved antallet når beskadiget grænseværdi. |


### <a name="solution"></a>Løsning

Valider, brugerdefineret sundhed forespørgsler er konfigureret korrekt som ovenstående tabel. Ud over de foregående fejlfindingstrin, du også sikre følgende:

- Sørg for, at efterprøvning af af er angivet korrekt ud fra [vejledning](application-gateway-create-probe-ps.md).
- Hvis Application Gateway er konfigureret for et enkelt websted, som standard værten skal være angivet som '127.0.0.1', medmindre andet er konfigureret i brugerdefineret efterprøvning af af.
- Sørg for, at et opkald til http://\<host\>:\<port\>\<stien\> returnerer en HTTP Resultatkode på 200.
- Sørg for, Interval, timeout og UnhealtyThreshold er inden for acceptable områder.

## <a name="request-time-out"></a>Anmode om timeout

### <a name="cause"></a>Årsag

Når der modtages en brugeranmodning, Application Gateway gælder konfigurerede reglerne for anmodningen og sender den til en forekomst af back end-puljen. Den venter på en konfigurerbar tidsinterval for et svar fra den back end-forekomst. Som standard er dette interval **30 sekunder**. Hvis Application Gateway ikke modtager et svar fra back end-program i dette interval, skal brugeren anmoder om se fejlen 502.

### <a name="solution"></a>Løsning

Application Gateway gør det muligt for brugerne at konfigurere denne indstilling via BackendHttpSetting, som kan anvendes derefter til forskellige grupper. Anden back end-grupper kan have forskellige BackendHttpSetting og dermed forskellige anmodning om timeout, der er konfigureret.

    New-AzureRmApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60

## <a name="next-steps"></a>Næste trin

Hvis de forrige trin ikke løser problemet, kan du åbne en [understøtter brugertilladelse](https://azure.microsoft.com/support/options/).
