<properties
   pageTitle="Lokalt datastyringsgateway | Microsoft Azure"
   description="En On-premises gateway er nødvendigt, hvis din Analysis Services-server i Azure vil oprette forbindelse til lokale datakilder."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="owend"/>

# <a name="on-premises-data-gateway"></a>Lokale datastyringsgateway

Lokalt datastyringsgateway fungerer som en bro, give sikker dataoverførsel mellem lokale datakilder og Azure Analysis Services-serveren i skyen.

En gateway er installeret på en computer på dit netværk. Én gateway skal være installeret for hver Azure Analysis Services-server, du har i abonnementet Azure. Eksempelvis hvis du har to servere i abonnementet Azure, der har forbindelse til lokale datakilder, skal der være installeret en gateway på to separate computere i netværket.

## <a name="requirements"></a>Krav

**Minimumskravene:**

- .NET 4.5 framework
- 64-bit version af Windows 7 / Windows Server 2008 R2 (eller nyere)

**Anbefalede:**

- 8 core-processor
- 8 GB hukommelse
- 64-bit versionen af Windows 2012 R2 (eller nyere)

**Vigtige overvejelser:**

- Gatewayen kan ikke installeres på et domænenavn fra domænecontrolleren.

- Kun én gateway kan være installeret på en enkelt computer.

- Installere gatewayen på en computer, der forbliver på og går ikke til dvale. Hvis den ikke er Azure Analysis Services-serveren kan ikke oprette forbindelse til din lokale datakilder for at opdatere data.

- Installer ikke gatewayen på en computer trådløs forbindelse til netværket. Ydeevnen kan være formindsket.

- Hvis du vil ændre servernavnet for en gateway, der allerede er konfigureret, skal du geninstallere og konfigurere en ny gateway.

- I nogle tilfælde kan oprette forbindelse til datakilder ved hjælp af Oprindelig udbydere såsom SQL Server Native Client (SQLNCLI11) tabular-modeller returnere en fejl. Se [datakilde forbindelser](analysis-services-datasource.md)for at få mere for at vide.

## <a name="supported-on-premises-data-sources"></a>Understøttede lokale datakilder
For eksempel understøtter gatewayen forbindelser mellem Azure Analysis Services-serveren og følgende lokale datakilder:

- SQL Server
- SQL datawarehouse
- APS
- Oracle
- Teradata


## <a name="download"></a>Download
 [Hent gateway](https://aka.ms/azureasgateway)


## <a name="install-and-configure"></a>Installere og konfigurere

1. Kør installationsprogrammet.

2. Vælge en installationsplacering, og Accepter licensvilkårene.

3. Log på Azure.

4. Angiv navnet på din Azure Analysis Server. Du kan kun angive en server per gateway. Klik på **Konfigurer** , og du er klar.

    ![Log på azure](./media\analysis-services-gateway\aas-gateway-configure-server.png)


## <a name="how-it-works"></a>Sådan fungerer det
Gatewayen kører som en Windows-tjeneste, **lokalt datastyringsgateway**, på en computer på din organisations netværk. Den gateway, du har installeret til brug med Azure Analysis Services er baseret på den samme gateway, der bruges til andre tjenester som Power BI, men med nogle forskelle i hvordan den er konfigureret.

![Sådan fungerer det](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Forespørgsler og data flow arbejde således:

1.  En forespørgsel er oprettet af skytjenesten med de krypterede legitimationsoplysninger for den lokale datakilde. Det sendes til en kø af gatewayen til at behandle.

2.  Gateway-skytjeneste analyserer forespørgslen og flytter anmodningen til [Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/).

3.  Gatewayen lokale data undersøger Azure Service Bus for ventende anmodninger.

4.  Gatewayen får forespørgslen, dekrypterer legitimationsoplysningerne og opretter forbindelse til datakilder med disse legitimationsoplysninger.

5.  Gatewayen sender forespørgslen til datakilden for udførelse af.

6.  Resultaterne er sendt fra datakilden, tilbage til gatewayen, og klik derefter på skytjenesten.

## <a name="windows-service-account"></a>Tjenesten Windows-konto

Datastyringsgateway i det lokale miljø er konfigureret til at bruge *NT SERVICE\PBIEgwService* til Windows-tjenesten logon legitimationsoplysninger. Som standard har højre for Logon som en tjeneste i forbindelse med den maskine, du vil installere gatewayen på. Denne legitimationsoplysninger er ikke den samme konto, der bruges til at oprette forbindelse til lokale datakilder eller din Azure-konto.  

Hvis du støder på problemer med din proxyserver på grund af godkendelse, du vil ændre kontoen, Windows-tjenesten til et domæne eller administreret tjenestekonto.

## <a name="ports"></a>Porte

Gatewayen opretter en udgående forbindelse til Azure Service Bus. Den kommunikerer på udgående porte: TCP 443 (standard), 5671, 5672, 9350 gennem 9354.  Gatewayen er ikke nødvendigt indgående porte.

Det anbefales du whitelist IP-adresser til din dataområde i din firewall. Du kan hente [Microsoft Azure Datacenter IP-liste](https://www.microsoft.com/download/details.aspx?id=41653). Denne liste opdateres ugentligt.

> [AZURE.NOTE]  De viste på listen Azure Datacenter IP IP-adresser er i CIDR notation. For eksempel betyder 10.0.0.0/24 ikke 10.0.0.0 gennem 10.0.0.24. Lær mere om [CIDR notation](http://whatismyipaddress.com/cidr).

Følgende er de fulde domænenavne, der bruges af gatewayen.

|Domænenavne|Udgående porte|Beskrivelse|
|---|---|---|
|*. powerbi.com|80|HTTP bruges til at hente installer.|
|*. powerbi.com|443|HTTPS|
|*. analysis.windows.net|443|HTTPS|
|*. login.windows.net|443|HTTPS|
|*. servicebus.windows.net|5671 5672|Avancerede MSMQ Protocol (AMQP)|
|*. servicebus.windows.net|443, 9350 9354|Lyttere på Service Bus Relay over TCP (kræver 443 for adgangskontrol token acquisition)|
|*. frontend.clouddatahub.net|443|HTTPS|
|*. core.windows.net|443|HTTPS|
|login.microsoftonline.com|443|HTTPS|
|*. msftncsi.com|443|Bruges til at teste forbindelse til internettet, hvis gatewayen kan nås ved Power BI-tjenesten.|
|*.microsoftonline p.com|443|Bruges til godkendelse af konfigurationen.|


### <a name="forcing-https-communication-with-azure-service-bus"></a>At tvinge HTTPS kommunikation med Azure Service Bus

Du kan gennemtvinge gatewayen til at kommunikere med Azure Service Bus ved hjælp af HTTPS i stedet for direkte TCP; Dette kan dog reducere ydeevnen betydeligt. Du vil redigere filen *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* . Ændre værdien fra `AutoDetect` til `Https`. Denne fil er placeret, som standard på *C:\Program Files\On lokale datastyringsgateway*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```


## <a name="troubleshooting"></a>Fejlfinding i forbindelse med
Under struktur er gatewayen lokale data, der bruges til at oprette forbindelse Azure Analysis Services til din lokale datakilder den samme gateway, der bruges med Power BI.

Hvis du har problemer med, når installation og konfiguration af en gateway, skal du se [fejlfinding af gatewayen Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem-tshoot/). Hvis du tror, at du har et problem med din firewall, skal du i afsnittene firewall eller proxyserver.

Hvis du tror, du støder på problemer med proxy med gatewayen, se [konfiguration af proxyindstillingerne for Power BI Gateways](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy.md).

## <a name="next-steps"></a>Næste trin
- [Administrere Analysis Services](analysis-services-manage.md)
- [Hente data fra Azure Analysis Services](analysis-services-connect.md)
