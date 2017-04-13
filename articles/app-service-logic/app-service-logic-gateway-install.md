<properties
   pageTitle="Logik Apps installere lokale datastyringsgateway | Microsoft Azure"
   description="Oplysninger om, hvordan du installerer gatewayen lokale data til brug i en logik app."
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/05/2016"
   ms.author="jehollan"/>

# <a name="install-the-on-premises-data-gateway-for-logic-apps"></a>Installere gatewayen lokale data til logik Apps

## <a name="installation-and-configuration"></a>Installation og konfiguration

### <a name="prerequisites"></a>Forudsætninger

Minimum:

* .NET 4.5 framework
* 64-bit version af Windows 7 eller Windows Server 2008 R2 (eller nyere)

Anbefalede:

* 8 core-processor
* 8 GB hukommelse
* 64-bit versionen af Windows 2012 R2 (eller nyere)

Relaterede overvejelser:

* Du kan ikke installere en gateway på et domænenavn fra domænecontrolleren.
* Du bør ikke installere en gateway på en computer, så en bærbar computer, kan være slået fra, i, eller ikke forbindelse til internettet, fordi gatewayen ikke køre under en af disse omstændigheder. Desuden kan lide gateway-ydeevne via et trådløst netværk.

### <a name="install-a-gateway"></a>Installere en gateway

Du kan få [installationsprogrammet til lokale datastyringsgateway her](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

Angive **lokale datastyringsgateway** som tilstanden, logge på med din arbejdskonto eller skolekonto, og derefter enten konfigurere en ny gateway eller overføre, gendanne eller overtage en eksisterende gateway.

* Skriv et **navn** til den og en **gendannelse nøgle**, og klik eller tryk på **Konfigurer**, for at konfigurere en gateway.

    Angive en gendannelse nøgle, der indeholder mindst otte tegn, og hold det i et sikkert sted. Hvis du vil overføre, gendanne eller overtage dens gateway, skal du bruge denne tast.

* Hvis du vil overføre, gendanne eller overtage en eksisterende gateway, give tasten gendannelse, der blev angivet, da gatewayen blev oprettet.

### <a name="restart-the-gateway"></a>Genstart gatewayen

Gatewayen kører som en Windows-tjeneste, og som med alle andre Windows-tjenesten, kan du starte og standse den på flere måder. For eksempel kan du åbne en kommandoprompt med administratorrettigheder på den computer, hvor gatewayen kører og derefter køre en af følgende kommandoer:

* Hvis du vil stoppe tjenesten, du Kør denne kommando:

    `net stop PBIEgwService`

* Hvis du vil starte tjenesten, du Kør denne kommando:

    `net start PBIEgwService`

### <a name="configure-a-firewall-or-proxy"></a>Konfigurere en firewall eller proxyserver

Du kan finde oplysninger om, hvordan du giver proxyoplysninger til din gateway [Konfigurer proxyindstillinger](https://powerbi.microsoft.com/en-us/documentation/powerbi-gateway-proxy/).

Du kan kontrollere, om din firewall eller proxyserver, muligvis blokerer forbindelser ved at køre følgende kommando fra en PowerShell-prompt. Dette vil teste forbindelse til Azure Service Bus. Dette kun tester netværksforbindelsen og har ikke noget, skal du gøre med tjenesten skyen server eller gatewayen. Det hjælper med at finde ud af, om computeren rent faktisk kan komme på internettet.

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

Resultaterne skal ligne eksemplet. Hvis **TcpTestSucceeded** ikke er sand, kan du blive blokeret af en firewall.

```
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

Hvis du vil være omfattende, skal du erstatte **computernavn** og **Port** værdierne med dem, der er angivet under [konfigurere porte](#configure-ports) senere i dette emne.

Firewallen muligvis også blokerer forbindelser, der giver Azure Service Bus for Azure datacentre. Hvis det er tilfældet, du skal whitelist (fjerne blokeringen af) alle IP-adresserne for dit område for disse datacentre. Du kan få en liste over [Azure IP-adresser her](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="configure-ports"></a>Konfigurere porte

Gatewayen opretter en udgående forbindelse til Azure Service Bus. Den kommunikerer på udgående porte: TCP 443 (standard), 5671, 5672, 9350 via 9354. Gatewayen kræver ikke indgående porte.

Lær mere om [hybrid løsninger](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

| DOMÆNENAVNE | UDGÅENDE PORTE | BESKRIVELSE |
| ----- | ------ | ------ |
| *. analysis.windows.net | 443 | HTTPS |
| *. login.windows.net | 443 | HTTPS |
| *. servicebus.windows.net |5671 5672 | Avancerede MSMQ Protocol (AMQP) |
| *. servicebus.windows.net | 443, 9350 9354 | Lyttere på Service Bus Relay over TCP (kræver 443 for adgangskontrol token acquisition) |
| *. frontend.clouddatahub.net | 443 | HTTPS |
| *. core.windows.net | 443 | HTTPS |
| login.microsoftonline.com | 443 | HTTPS |
| *. msftncsi.com | 443 | Bruges til at teste forbindelse til internettet, hvis gatewayen kan nås ved Power BI-tjenesten. |

Hvis du har brug for til hvid liste IP-adresser i stedet for domænerne, kan du hente og bruge [Microsoft Azure Datacenter IP områder liste](https://www.microsoft.com/download/details.aspx?id=41653). I nogle tilfælde foretages Azure Service Bus forbindelser til IP-adresse i stedet for de fulde domænenavne.

### <a name="sign-in-account"></a>Logonkonto

Brugerne kan logge på med enten en arbejds- eller skolekonto. Dette er din organisation-konto. Hvis du har tilmeldt sig en Office 365-tilbud og ikke leverer din mail faktisk arbejde, kan det ligne jeff@contoso.onmicrosoft.com. Din konto, i en skybaseret tjeneste, er gemt i en lejer i Azure Active Directory (AAD). I de fleste tilfælde matcher kontoen AAD UPN mailadressen.

### <a name="windows-service-account"></a>Tjenesten Windows-konto

Datastyringsgateway i det lokale miljø er konfigureret til at bruge NT SERVICE\PBIEgwService til Windows-tjenesten logon legitimationsoplysninger. Som standard har den højre for Log på som en tjeneste. Dette er i konteksten for den computer, hvorpå du installerer gatewayen.

Dette er ikke den konto, der bruges til at oprette forbindelse til lokale datakilder eller kontoen arbejds- eller skolekonto, som du logger på tjenester i skyen.

##<a name="frequently-asked-questions"></a>Ofte stillede spørgsmål

### <a name="general"></a>Generelt

**Spørgsmål**: hvilke datakilder understøtter gatewayen?<br/>
**Answer**: og denne skriver, SQL Server.

**Spørgsmål**: skal jeg bruge en gateway til datakilder i skyen, såsom SQL Azure? <br/>
**Answer**: Nej. En gateway opretter forbindelse til lokale datakilder kun.

**Spørgsmål**: Hvad er den faktiske Windows service kaldet?<br/>
**Answer**: tjenester i gatewayen hedder Power BI Enterprise Gateway-tjenesten.

**Spørgsmål**: er der en hvilken som helst indgående forbindelser til gatewayen fra skyen? <br/>
**Answer**: Nej. Gatewayen bruger udgående forbindelser til Azure Service Bus.

**Spørgsmål**: Hvad nu, hvis jeg blokere udgående forbindelser? Hvad har jeg brug at åbne? <br/>
**Answer**: gå til porte og værter, der bruger gatewayen.


**Spørgsmål**: har gatewayen skal være installeret på den samme computer som datakilde? <br/>
**Answer**: Nej. Gatewayen opretter forbindelse til datakilden ved hjælp af forbindelsesoplysningerne, der blev leveret. Tænk på gatewayen som en klientprogrammet på denne måde. Den skal kun kunne oprette forbindelse til navnet på den server, der blev leveret.


**Spørgsmål**: Hvad er ventetid for at køre forespørgsler til en datakilde fra en gateway? Hvad er den bedste arkitektur? <br/>
**Answer**: for at reducere netværksventetid, skal du installere gatewayen så tæt datakilden som muligt. Hvis du kan installere gatewayen på den faktiske datakilde, minimeres ventetid introduceret. Overvej datacentre samt. Eksempelvis hvis din tjeneste bruger Vest USA datacenter, og du har SQL Server, der er placeret i en Azure VM, vil du vil have Azure VM i Vest USA samt. Dette vil minimere ventetid og undgå udgangspunkt gebyrer på Azure VM.


**Spørgsmål**: er der en hvilken som helst krav til netværksbåndbredde? <br/>
**Answer**: det anbefales at have god overførsel for din netværksforbindelse. Hver miljø er anderledes, og mængden data, der sendes påvirker resultaterne. Brug af ExpressRoute kan hjælpe med at sikre et niveau for overførselshastighed mellem lokale og Azure datacentre.

Du kan bruge værktøj fra tredjepart til Azure hastighedstest app til at måle, hvad din overførselshastighed er.


**Spørgsmål**: kan gatewayen Windows-tjenesten køres med en Azure Active Directory-konto? <br/>
**Answer**: Nej. Windows-tjenesten skal have en gyldig Windows-konto. Som standard kører det med tjenesten SID, NT SERVICE\PBIEgwService.


**Spørgsmål**: hvordan resultaterne sendes tilbage til skyen? <br/>
**Answer**: Dette gøres ved hjælp af Azure Service Bus. Du kan finde flere oplysninger, se, hvordan det fungerer.


**Spørgsmål**: hvor er mine legitimationsoplysninger gemt? <br/>
**Answer**: de legitimationsoplysninger, som du angiver for en datakilde gemmes krypteret i gateway skybaseret tjeneste. Legitimationsoplysningerne, der er dekrypteres på de gateway i det lokale miljø.

### <a name="high-availabilitydisaster-recovery"></a>Høj tilgængelighed/nedbrud

**Spørgsmål**: er der nogen planer for at aktivere høj tilgængelighed scenarier med gateway? <br/>
**Answer**: Dette er i gang med køreplanen, men vi endnu ikke har en tidslinje.


**Spørgsmål**: hvilke indstillinger er tilgængelige for nedbrud? <br/>
**Answer**: Du kan bruge tasten gendannelse til at gendanne eller flytte en gateway. Når du installerer gatewayen, Angiv tasten gendannelse.


**Spørgsmål**: Hvad er fordelene ved tasten gendannelse? <br/>
**Answer**: det er en måde at overføre eller Gendan gatewayindstillingerne for efter nedbrud.

### <a name="troubleshooting"></a>Fejlfinding i forbindelse med

**Spørgsmål**: hvor er loggene gateway? <br/>
**Answer**: få vist værktøjer senere i dette emne.


**Spørgsmål**: Hvordan kan jeg se, hvad forespørgsler der sendes til den lokale datakilde? <br/>
**Answer**: Du kan aktivere sporing af forespørgsel, som indeholder de forespørgsler, der sendes. Husk at ændre det tilbage til den oprindelige værdi, når færdig fejlfinding. Lad forespørgslen sporing aktiveret medfører loggene bliver større.

Du kan også se funktioner, der indeholder din datakilde til sporing af forespørgsler. Du kan for eksempel bruge udvidede begivenheder eller SQL Profiler til SQL Server og Analysis Services.

## <a name="how-the-gateway-works"></a>Hvordan fungerer gatewayen

Når en bruger skal arbejde sammen med et element, der er forbundet til en lokal datakilde:

1. Skytjenesten opretter en forespørgsel, sammen med de krypterede legitimationsoplysninger for datakilden, og sender forespørgslen til køen af gatewayen til at behandle.
1. Tjenesten analyserer forespørgslen og flytter anmodningen til Azure Service Bus.
1. Gatewayen lokale data undersøger Azure Service Bus for ventende anmodninger.
1. Gatewayen får forespørgslen, dekrypterer legitimationsoplysningerne og opretter forbindelse til datakilder med disse legitimationsoplysninger.
1. Gatewayen sender forespørgslen til datakilden for udførelse af.
1. Resultaterne er sendt fra datakilden tilbage til gatewayen, og klik derefter på skytjenesten. Tjenesten bruger derefter resultaterne.

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

### <a name="update-to-the-latest-version"></a>Opdatere til den nyeste version

Mange problemer kan overflade, når gateway versionen er forældet.  Det er praktisk at sikre, at du er i den seneste version af generelle.  Hvis du ikke har opdateret gateway til en måned eller længere, kan du vil overveje at installere den nyeste version for gateway og se, hvis du kan genskabe problemet.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users-"></a>Fejl: Mislykkedes til at føje brugeren til gruppen. (-2147463168 PBIEgwService ydeevne Log brugere)

Du kan få denne fejlmeddelelse, hvis du forsøger at installere gatewayen på et domænenavn fra domænecontrolleren, som ikke understøttes. Du skal installere gatewayen på en computer, der ikke er et domænenavn fra domænecontrolleren.

## <a name="tools"></a>Værktøjer

### <a name="collecting-logs-from-the-gateway-configurator"></a>Indsamling af logfiler fra gateway konfiguratoren

Du kan samle flere logge af gatewayen. Altid start med logfiler!

#### <a name="installer-logs"></a>Installer logfiler

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>Konfiguration af logfiler

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>Enterprise gateway service logfiler

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>Hændelseslogfiler

Loggene Datastyringsgateway og PowerBIGateway findes under **programmer og tjenester logfiler**.

### <a name="fiddler-trace"></a>Fiddler sporing

[Fiddler](http://www.telerik.com/fiddler) er et gratis værktøj fra Telerik, overvåger HTTP-trafik.  Du kan se tilbage og frem med Power BI service fra klientcomputeren. Dette kan vise fejl og andre relaterede oplysninger.

## <a name="next-steps"></a>Næste trin
- [Oprette en lokal forbindelse til logik Apps](app-service-logic-gateway-connection.md)
- [Enterprise funktioner for klientintegration](app-service-logic-enterprise-integration-overview.md)
- [Logik Apps forbindelser](../connectors/apis-list.md)