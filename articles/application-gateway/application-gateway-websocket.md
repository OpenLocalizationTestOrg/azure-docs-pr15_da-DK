<properties
   pageTitle="Gatewayen WebSocket programunderstøttelse | Microsoft Azure"
   description="Denne side indeholder en oversigt over fra programmet Gateway WebSocket support."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/16/2016"
   ms.author="amsriva"/>

# <a name="application-gateway-websocket-support"></a>Gatewayen WebSocket programunderstøttelse

Application Gateway indeholder indbygget understøttelse af WebSocket på tværs af alle størrelser af gatewayen. Der er ingen kan konfigureres af brugeren indstilling til at aktivere eller deaktivere understøttelse af WebSocket enkeltvis. Du kan fortsætte med at bruge en standard HTTPListener på port 80/443 til at modtage WebSocket trafik. WebSocket trafik derefter dirigeres til WebSocket aktiveret back end-serveren ved hjælp af relevante back end-puljen som angivet i programmet gateway regler. WebSocket protocol standardiserede i [RFC6455](https://tools.ietf.org/html/rfc6455) gør det muligt for en fuld duplex kommunikation mellem serveren og klienten via en længerevarende TCP-forbindelse. Denne funktion gør det muligt for en mere interaktiv kommunikation mellem webserveren og klient, som kan være tovejs uden brug af afstemning som kræves i HTTP-baserede installationer.  WebSocket har lavt spild i modsætning til HTTP og genbruge den samme TCP-forbindelsen til flere anmodning/svar, hvilket resulterer i en mere effektiv udnyttelse af ressourcer. WebSocket protokoller er udviklet til at fungere over traditionelle HTTP-portene 80 og 443.

Back end-serveren skal reagere på programmet gateway sonder, som er beskrevet i afsnittet [sundhed efterprøvning af af oversigt](application-gateway-probe-overview.md) . Programmet gateway sundhed sonder er kun HTTP/HTTPS, det betyder, at hver back end-serveren skal reagere på http-sonder programmet gatewayen til at dirigere WebSocket trafik til serveren.

## <a name="listener-configuration-element"></a>Lytteren konfiguration element

Eksisterende HTTPListener kan bruges til at understøtte WebSocket. Følgende er et stykke af HttpListeners element fra en skabelon eksempelfil. Du har brug for både HTTP og HTTPS lyttere til at understøtte WebSocket og sikre WebSocket trafik. På samme måde du kan bruge [portal](application-gateway-create-gateway-portal.md) eller [PowerShell](application-gateway-create-gateway-arm.md) til at oprette et program-gateway med lyttere på port 80/443 til at understøtte WebSocket trafik.


    "httpListeners": [
                {
                    "name": "appGatewayHttpsListener",
                    "properties": {
                        "FrontendIPConfiguration": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
                        },
                        "FrontendPort": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
                        },
                        "Protocol": "Https",
                        "SslCertificate": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
                        },
                    }
                },
                {
                    "name": "appGatewayHttpListener",
                    "properties": {
                        "FrontendIPConfiguration": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
                        },
                        "FrontendPort": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
                        },
                        "Protocol": "Http",
                    }
                }
            ],

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>Konfiguration af BackendAddressPool, BackendHttpSetting og Routing regel

BackendAddressPool skal bruges til at definere en back end-puljen med WebSocket aktiveret servere. BackendHttpSetting skal være defineret med back end-port 80/443 kun. Egenskaber for cookie-baseret forbindelse og requestTimeouts er ikke relevant for WebSocket trafik. Der er ingen ændring, der er påkrævet i reglen routing. Routing-regel 'Grundlæggende' fremover skal bruges til at knytter relevante lytteren at tilsvarende back end-adresse puljen. 

    "requestRoutingRules": [{
        "name": "<ruleName1>",
        "properties": {
            "RuleType": "Basic",
            "httpListener": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener')]"
            },
            "backendAddressPool": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
            },
            "backendHttpSettings": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
            }
        }

    }, {
        "name": "<ruleName2>",
        "properties": {
            "RuleType": "Basic",
            "httpListener": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener')]"
            },
            "backendAddressPool": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
            },
            "backendHttpSettings": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
            }

        }
    }]

## <a name="websocket-enabled-backend"></a>WebSocket aktiveret backend-version

Din backend-version skal have en HTTP/HTTPS-webserver, der kører på den konfigurerede port (som regel 80/443) for WebSocket til at arbejde. Dette krav gælder, fordi WebSocket protocol kræver indledende kvitteringen skal være HTTP med opgraderingen til WebSocket protokol som et Overskriftsfelt.

    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: http://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13

En anden mulig grund til dette er programmet gateway back end-tilstand efterprøvning af af understøtter kun HTTP/HTTPS protokoller. Hvis back end-serveren ikke svarer på HTTP/HTTPS sonder, vil blive ført ud af back end-puljen og ingen anmodninger, herunder WebSocket anmodninger, vil når denne backend-version.

## <a name="next-steps"></a>Næste trin

Gå til at [oprette et program-gateway](application-gateway-create-gateway.md) til at komme i gang med et WebSocket aktiveret webprogram efter lære mere om understøttelse af WebSocket.
