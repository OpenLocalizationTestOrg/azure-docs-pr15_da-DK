<properties
   pageTitle="Oprette et program-gateway ved hjælp af Azure CLI i ressourcestyring | Microsoft Azure"
   description="Lær, hvordan du opretter et program-Gateway ved hjælp af Azure CLI i ressourcestyring."
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

# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Oprette et program-gateway ved hjælp af Azure CLI

> [AZURE.SELECTOR]
- [Azure-portalen](application-gateway-create-gateway-portal.md)
- [Azure ressourcestyring PowerShell](application-gateway-create-gateway-arm.md)
- [Azure klassisk PowerShell](application-gateway-create-gateway.md)
- [Azure ressourcestyring skabelon](application-gateway-create-gateway-arm-template.md)
- [Azure CLI](application-gateway-create-gateway-cli.md)

Azure Application Gateway er lag-7 belastningsjustering. Den indeholder failover, ydeevne-routing HTTP-anmodninger mellem forskellige servere, uanset om de er i skyen eller i det lokale miljø. Programmet gateway har følgende levering programfunktioner: HTTP indlæse justering, cookie-baserede session forbindelse og Secure Sockets Layer (SSL) aflastning brugerdefinerede sundhed sonder og understøttelse af flere websted.

## <a name="prerequisite-install-the-azure-cli"></a>Forudsætninger: Installere Azure CLI

For at udføre trinnene i denne artikel, skal du [installere Azure kommandolinjen til Mac, Linux, og Windows (Azure CLI)](../xplat-cli-install.md) , og du har brug for til at [logge på Azure](../xplat-cli-connect.md). 

> [AZURE.NOTE] Hvis du ikke har en Azure-konto, skal du en. Gå log op til en [gratis prøveversion her](../active-directory/sign-up-organization.md).

## <a name="scenario"></a>Scenarie

I dette scenarie skal du få mere at vide, hvordan du opretter et program-gateway ved hjælp af portalen Azure.

Dette scenarie skal:

- Opret en mellemstore programmet gateway med to forekomster.
- Oprette et virtuelt netværk med navnet AdatumAppGatewayVNET med en reserveret CIDR blok med 10.0.0.0/16.
- Oprette et undernet, kaldet Appgatewaysubnet, der bruger 10.0.0.0/28 som dens CIDR blok.
- Konfigurere et certifikat for SSL aflastning.

![Scenarieeksempel][scenario]

>[AZURE.NOTE] Yderligere konfiguration af programmet gatewayen, herunder brugerdefinerede sundhed sonder, back end-puljen adresser og yderligere regler er konfigureret, når programmet gatewayen er konfigureret og ikke under indledende installation.

## <a name="before-you-begin"></a>Inden du går i gang

Azure Application Gateway kræver sin egen undernet. Når du opretter et virtuelt netværk, kan du sikre dig, at du lader plads adresse for at få flere undernet. Når du installerer et program-gateway til en undernet, der kun yderligere program-gateways kan føjes til undernettet.

## <a name="log-in-to-azure"></a>Log på Azure

Åbn **Microsoft Azure kommandoprompten**, og log på. 

    azure login

Når du skriver det foregående eksempel, leveres en kode. Gå til https://aka.ms/devicelogin i en browser for at fortsætte logon.

![cmd viser enhed logon][1]

Angiv den kode, du har modtaget i browseren. Du har åbnet med en logonsiden.

![browser for at indsætte kode][2]

Når der er indsat koden du er logget på, skal du lukke browseren til at fortsætte på med dette scenario.

![logget på][3]

## <a name="switch-to-resource-manager-mode"></a>Skifte til Ressourcestyring tilstand

    azure config mode arm

## <a name="create-the-resource-group"></a>Oprette ressourcegruppen

Før du opretter gatewayen program, der oprettes en ressourcegruppe indeholde gatewayen programmet. Følgende viser kommandoen.

    azure group create -n AdatumAppGatewayRG -l eastus

## <a name="create-a-virtual-network"></a>Oprette et virtuelt netværk

Når ressourcegruppen er oprettet, oprettes et virtuelt netværk til gatewayen programmet på computeren.  I følgende eksempel blev adresseområder som 10.0.0.0/16, som defineret i de foregående scenarie noter.

    azure network vnet create -n AdatumAppGatewayVNET -a 10.0.0.0/16 -g AdatumAppGatewayRG -l eastus

## <a name="create-a-subnet"></a>Oprette et undernet

Når det virtuelle netværk er oprettet, føjes et undernet af programmet gatewayen.  Hvis du planlægger at bruge programmet gateway med en WebApp, der er det samme virtuelle netværk som gatewayen program som vært, skal du sørge for at forlade nok ledig plads til en anden undernet.

    azure network vnet subnet create -g AdatumAppGatewayRG -n Appgatewaysubnet -v AdatumAppGatewayVNET -a 10.0.0.0/28 

## <a name="create-the-application-gateway"></a>Oprette gatewayen program

Når virtuelle Netværks- og undernet er oprettet, er forudsætninger for gatewayen programmet er fuldført. Desuden et tidligere eksporterede .pfx certifikat og adgangskoden til certifikatet, der kræves til følgende trin: IP-adresser, der bruges til backend-version, er IP-adresserne til back end-serveren. Disse værdier kan være enten privat IP'er i det virtuelle netværk, offentlige IP-adresser eller fulde domænenavne for din back end-servere.

    azure network application-gateway create -n AdatumAppGateway -l eastus -g AdatumAppGatewayRG -e AdatumAppGatewayVNET -m Appgatewaysubnet -r 134.170.185.46,134.170.188.221,134.170.185.50 -y c:\AdatumAppGateway\adatumcert.pfx -x P@ssw0rd -z 2 -a Standard_Medium -w Basic -j 443 -f Enabled -o 80 -i http -b https -u Standard

> [AZURE.NOTE] En liste over parametre, der kan sikres under oprettelse af køre følgende kommando: **azure netværk programmet gateway-oprette – Hjælp**.

I dette eksempel oprettes en grundlæggende programmet gateway med standardindstillinger for lytteren, back end-puljen, back end-HTTP-indstillinger og regler. Den konfigurerer også SSL aflastning. Du kan ændre disse indstillinger, så de passer til din installation, når den klargøring er gået igennem.
Hvis du allerede har dit webprogram, der er defineret med den back end-puljen i de forrige trin, når oprettet, belastningsjustering begynder.

## <a name="next-steps"></a>Næste trin

Lær at oprette brugerdefinerede sundhed sonder ved at besøge [oprette en brugerdefineret sundhed efterprøvning af af](application-gateway-create-probe-portal.md)

Lær, hvordan du konfigurerer SSL overføre og tage dyrt SSL dekrypteringen fra webserverne ved at besøge [Konfigurere SSL Offload](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png