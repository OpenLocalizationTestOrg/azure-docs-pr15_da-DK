<properties
    pageTitle="Forbinde computere og enheder til OMS ved hjælp af gatewayen OMS | Microsoft Azure"
    description="Tilslut din OMS-administreret enheder og Operations Manager-overvåget computere med gatewayen OMS til at sende data til tjenesten OMS, når de ikke har adgang til internettet."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>
<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="banders"/>

# <a name="connect-computers-and-devices-to-oms-using-the-oms-gateway"></a>Forbinde computere og enheder til OMS ved hjælp af gatewayen OMS

I dette dokument beskrives, hvordan din OMS-administreret enheder og System Center Operations Manager SCOM-overvåget computere kan sende data til tjenesten OMS når de ikke har adgang til internettet. Gatewayen OMS kan indsamle dataene, og send det til tjenesten OMS på deres vegne.

Gatewayen er en videresende HTTP-proxy, der understøtter HTTP tunnelføring ved hjælp af kommandoen HTTP-forbindelse. Gatewayen kan håndtere op til 2000 OMS samtidig forbindelse enheder, når kører på en 4-core-processor, 16 GB server, der kører Windows.

Som eksempel din virksomhed eller organisation for store muligvis servere med netværksforbindelsen men kan ikke har forbindelse til internettet. Du kan have mange punkt salg (POS) enheder med nogen mulighed for overvågning dem direkte i et andet eksempel. Og i et andet eksempel Operations Manager kan bruge gatewayen OMS som en proxy-server. I disse eksempler overføre gatewayen OMS data fra de supportmedarbejdere, der er installeret på følgende servere eller POS enheder til OMS.

I stedet for hver enkelt agent sender data direkte til OMS og det kan kræve en direkte internetforbindelse, sendes alle agent data i stedet via en enkelt computer, der har forbindelse til internettet. Computeren er, hvor du installere og bruge gatewayen. I dette scenarie skal installere du supportmedarbejdere på alle computere, hvor du vil indsamle data. Gatewayen derefter overfører data fra supportmedarbejdere til OMS direkte – gatewayen ikke analysere nogle af de data, der overføres.

Hvis du vil overvåge gatewayen OMS og analysere ydeevne eller begivenhed data for den server, hvor den er installeret, skal du installere OMS agent på den computer, hvor gatewayen også er installeret.

Gatewayen skal have adgang til internettet for at overføre data til OMS. Hver agent skal også have netværksforbindelse til dens gateway, så supportmedarbejdere kan automatisk overføre data til og fra en gateway. Du opnår de bedste resultater, skal du ikke installere gatewayen på en computer, der er også et domænenavn fra domænecontrolleren.

Her er et diagram, der viser dataflow fra direkte supportmedarbejdere til OMS.

![direkte agent diagram](./media/log-analytics-oms-gateway/direct-agent-diagram.png)

Her er et diagram, der viser dataflow fra Operations Manager til OMS.

![Operations Manager-diagram](./media/log-analytics-oms-gateway/scom-mgt-server.png)

## <a name="install-the-oms-gateway"></a>Installere gatewayen OMS

Installere denne Gateway erstatter tidligere versioner af gatewayen, at du har installeret (Log Analytics domænes).

Forudsætninger: .net Framework 4.5, Windows Server 2012 R2 SP1 og derover

1. Hente den nyeste version af gatewayen OMS fra [Microsoft Download Center](http://download.microsoft.com/download/2/5/C/25CF992A-0347-4765-BD7D-D45D5B27F92C/OMS%20Gateway.msi).
2. Dobbeltklik på **OMS Gateway.msi**for at starte installationen.
3. På velkomstsiden, **Næste**.  
    ![Gatewayen konfigurationsguiden](./media/log-analytics-oms-gateway/gateway-wizard01.png)
4. Vælg **jeg accepterer vilkårene i licensaftalen** til accepterer slutbrugerlicensaftale (EULA) og derefter **Næste**på siden Licensaftale.
5. På siden port og proxy adresse:
    1. Skriv TCP-portnummeret, der skal bruges til gatewayen. Konfiguration af åbner dette portnummer fra Windows firewall. Standardværdien er 8080.
    Det gyldige interval for portnummeret er 1-65535. Hvis input ikke falder i dette interval, vises en fejlmeddelelse.
    2. Hvis den server, hvor gatewayen er installeret skal bruge en proxy, Skriv eventuelt proxyadresse, hvor gatewayen skal bruge til at oprette forbindelse. For eksempel `http://myorgname.corp.contoso.com:80` Hvis feltet er tomt, gatewayen forsøger at oprette forbindelse til internettet direkte. Ellers forbinder gatewayen til proxyen. Hvis din proxy-server kræver godkendelse, skal du skrive dit brugernavn og din adgangskode.
        ![Gatewayen guiden Konfiguration af proxy](./media/log-analytics-oms-gateway/gateway-wizard02.png)  
    3. Klik på **Næste**
6. Hvis du ikke har Microsoft Updates aktiveret, vises siden Microsoft Update, hvor du kan vælge at aktivere Microsoft Updates. Foretag et valg, og klik derefter på **Næste**. Ellers skal du fortsætte til næste trin.
7. Lad være standard mappe **%ProgramFiles%\OMS gatewayen** eller angive det sted, hvor du vil installere gateway, og klik derefter på **Næste**på siden destinationsmappe.
8. På siden klar til at installere, og klik på **Installer**. En kontrol af brugerkonti vises muligvis anmoder om tilladelse til at installere. Hvis det er tilfældet, skal du klikke på **Ja**.
9. Når installationen er fuldført, kan du klikke på **Udfør**. Du kan kontrollere, at tjenesten kører ved at åbne snap-in services.msc, og Kontrollér, at **OMS Gateway** vises på listen over tjenester.  
    ![Tjenester – OMS Gateway](./media/log-analytics-oms-gateway/gateway-service.png)

## <a name="install-an-agent-on-devices"></a>Installere en agent på enheder

Hvis det er nødvendigt, kan du se [forbinde Windows-computere at Log Analytics](log-analytics-windows-agents.md) oplysninger om, hvordan du installerer direkte forbindelse supportmedarbejdere. I artiklen beskrives, hvordan du kan installere den ved hjælp af en installationsguide agent eller ved at bruge kommandolinjen.

## <a name="configure-oms-agents"></a>Konfigurere OMS supportmedarbejdere

Se [konfigurere proxy og firewall-indstillinger med Microsoft overvågning Agent](log-analytics-proxy-firewall.md) for oplysninger om at konfigurere en agent for at bruge en proxyserver, som er dette tilfælde er gatewayen.

Operations Manager supportmedarbejdere sende nogle data som Operations Manager beskeder, konfiguration vurdering, forekomst mellemrum og databehandling via Management Server. Andre store mængder data, som IIS logfiler, ydeevne og sikkerhed der sendes direkte til gatewayen OMS. Se [tilføje Log Analytics løsninger fra løsningsgalleriet](log-analytics-add-solutions.md) til en komplet liste over data, der sendes via hver kanal.

>[AZURE.NOTE]
Hvis du planlægger at bruge gatewayen med netværk belastningsjustering, kan du se [du kan også konfigurere netværksbelastning](#optionally-configure-network-load-balancing).

## <a name="configure-a-scom-proxy-server"></a>Konfigurere en SCOM proxyserver

Du konfigurerer Operations Manager for at tilføje gatewayen skal fungere som en proxy-server. Når du opdaterer proxykonfigurationen, anvendes proxykonfigurationen automatisk på alle supportmedarbejdere, der er rapportering til Operations Manager.

Hvis du vil bruge gatewayen til at understøtte Operations Manager, skal du have:

- Microsoft overvågning Agent (agent version – **8.0.10900.0** og nyere) installeret på Gateway-serveren og konfigureret til OMS arbejdsområderne, som du vil kommunikere.
- Gatewayen skal have forbindelse til internettet eller være forbundet med en proxyserver, som gør.

### <a name="to-configure-scom-for-the-gateway"></a>Sådan konfigureres SCOM for gateway

1. Åbne konsollen Operations Manager og under **Handlinger Management-pakken**, skal du klikke på **forbindelsen** , og klik derefter på **Konfigurer Proxy-Server**:  
    ![Operations Manager – konfigurere Proxy-Server](./media/log-analytics-oms-gateway/scom01.png)
2. Vælg **Brug en proxyserver for at få adgang til handlinger Management-pakken** , og skriv derefter IP-adressen på OMS Gateway-serveren. Sikre, at du starter med det `http://` præfiks:  
    ![Operations Manager-proxyadresse server](./media/log-analytics-oms-gateway/scom02.png)
3. Klik på **Udfør**. Serverens Operations Manager er forbundet til arbejdsområdet OMS.

## <a name="configure-network-load-balancing"></a>Konfigurere netværksbelastning

Du kan konfigurere gatewayen for høj tilgængelighed ved hjælp af justering af netværksbelastning ved at oprette en klynge. Klyngen administrerer trafik fra din supportmedarbejdere ved at omdirigere de nødvendige forbindelser fra Microsoft overvågning supportmedarbejdere på tværs af dens noder. Hvis en Gateway-server går ned, viderestilles trafikken til andre noder.

1. Åbn Network Indlæs justering af Manager, og Opret en klynge.
2. Højreklik på klynge før du tilføjer gateways, og vælg **klynge egenskaber.** Konfigurere klynge for at få sin egen IP-adresse:  
    ![Network Indlæs justering af Manager – klynge IP-adresser](./media/log-analytics-oms-gateway/nlb01.png)
3. For at forbinde en OMS Gateway-server med Microsoft overvågning Agent installeret, skal du højreklikke på den klynge IP-adresse, og klik derefter på **Tilføj Host til klynge**.  
    ![Netværk indlæse justering af belastning chef – Tilføj vært til klynge](./media/log-analytics-oms-gateway/nlb02.png)
4. Angiv IP-adressen på den Gateway-server, du vil oprette forbindelse:  
    ![Netværk Indlæs justering af chef – Tilføj vært til klynge: oprette forbindelse](./media/log-analytics-oms-gateway/nlb03.png)
5. På computere, der ikke har forbindelse til internettet, skal du angive IP-adressen, når du konfigurerer **Microsoft overvågning agentegenskaber**:  
    ![Microsoft overvågning agentegenskaber – proxyindstillinger](./media/log-analytics-oms-gateway/nlb04.png)

## <a name="configure-for-automation-hybrid-workers"></a>Konfigurere til automatisering hybrid medarbejdere

Hvis du har automatisering hybrid medarbejdere i dit miljø, indeholder følgende trin manuelt, midlertidige løsninger for at konfigurere gatewayen at understøtte dem.

I de følgende trin skal du vide det Azure område, hvor den automatiske konto er placeret. At finde placeringen:

1. Log på [Azure-portalen](https://portal.azure.com/).
2. Vælg tjenesten, Azure automatisering.
3. Vælge den relevante Azure automatisering-konto.
4. Få vist dets område under **placering**.  
    ![Azure portal – automatisering konto placering](./media/log-analytics-oms-gateway/location.png)

Brug de følgende tabeller til at identificere URL-adressen for hver placering:

**Jobbet runtime data service URL-adresser**

| **placering** | **URL-ADRESSE** |
| --- | --- |
| Nord centrale USA | ncus-jobruntimedata-Prod.ordre-su1.azure-automation.net |
| Vest Europe | Vi-jobruntimedata-Prod.ordre-su1.azure-automation.net |
| Syd centrale USA | scus-jobruntimedata-Prod.ordre-su1.azure-automation.net |
| Indtastning af østasiatiske USA | eus-jobruntimedata-Prod.ordre-su1.azure-automation.net |
| Central Canada | Cc-jobruntimedata-Prod.ordre-su1.azure-automation.net |
| Nord Europe | ne-jobruntimedata-Prod.ordre-su1.azure-automation.net |
| Syd Østasien | Hav-jobruntimedata-Prod.ordre-su1.azure-automation.net |
| Central Indien | CID-jobruntimedata-Prod.ordre-su1.azure-automation.net |
| Japan | jpe-jobruntimedata-Prod.ordre-su1.azure-automation.net |
| Australien | ASE-jobruntimedata-Prod.ordre-su1.azure-automation.net |

**Agent service URL-adresser**

| **placering** | **URL-ADRESSE** |
| --- | --- |
| Nord centrale USA | ncus-agentservice-Prod.ordre-1.azure-automation.net |
| Vest Europe | Vi-agentservice-Prod.ordre-1.azure-automation.net |
| Syd centrale USA | scus-agentservice-Prod.ordre-1.azure-automation.net |
| Indtastning af østasiatiske USA | eus2-agentservice-Prod.ordre-1.azure-automation.net |
| Central Canada | Cc-agentservice-Prod.ordre-1.azure-automation.net |
| Nord Europe | ne-agentservice-Prod.ordre-1.azure-automation.net |
| Syd Østasien | Hav-agentservice-Prod.ordre-1.azure-automation.net |
| Central Indien | CID-agentservice-Prod.ordre-1.azure-automation.net |
| Japan | jpe-agentservice-Prod.ordre-1.azure-automation.net |
| Australien | ASE-agentservice-Prod.ordre-1.azure-automation.net |

Hvis computeren er registreret som en hybrid arbejder automatisk til rettelse af ved hjælp af løsningen Update Management, skal du følge disse trin:

1. Føje Job Runtime Data service URL-adresser til listen over tilladte værter på OMS gatewayen. Eksempel: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. Genstart OMS Gateway-tjenesten ved hjælp af følgende PowerShell-cmdlet:`Restart-Service OMSGatewayService`

Hvis computeren er på-ske til Azure Automation ved hjælp af hybrid arbejder registrering-cmdlet, skal du følge disse trin:

1. Tilføj agent registrering URL-adressen til listen over tilladte værter på OMS gatewayen. Eksempel:`Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. Føje Job Runtime Data service URL-adresser til listen over tilladte værter på OMS gatewayen. Eksempel: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. Genstart OMS Gateway-tjenesten.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Nyttige PowerShell-cmdletter

Cmdletter kan hjælpe dig med at udføre opgaver, der er nødvendige for at opdatere gatewayen OMS konfigurationsindstillinger. Inden du bruger dem, skal du sørge for at:

1. Installere OMS gatewayen (MSI).
2. Åbn vinduet PowerShell.
3. Hvis du vil importere modulet, skal du skrive følgende kommando:`Import-Module OMSGateway`
4. Hvis der opstod ingen fejl i det forrige trin, modulet, der blev importeret, og den kan bruges. Type`Get-Module OMSGateway`
5. Når du har foretaget ændringer ved hjælp af cmdlet'erne, kan du sikre dig, at du genstarter Gateway-tjenesten.

Hvis du får en fejl i trin 3, blev ikke modulet importeret. Fejlen kan opstå, når PowerShell kan ikke finde modulet. Du kan finde det i den Gateway installationssti: C:\Program File\Microsoft OMS Gateway\PowerShell.

| **Cmdlet** | **Parametre** | **Beskrivelse** | **Eksempler** |
| --- | --- | --- | --- |
| `Set-OMSGatewayConfig` | Nøgle (påkrævet) <br> Værdi | Ændringer af konfigurationen af tjenesten | `Set-OMSGatewayConfig -Name ListenPort -Value 8080` |
| `Get-OMSGatewayConfig` | Nøgle | Får konfigurationen af tjenesten | `Get-OMSGatewayConfig` <br> <br> `Get-OMSGatewayConfig -Name ListenPort` |
| `Set-OMSGatewayRelayProxy` | Adresse <br> Brugernavn <br> Adgangskode | Angiver adresse (og legitimationsoplysninger) af relay (mod) proxy | 1. Angiv et svar proxy og legitimationsoplysninger:`Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080 -Username user1 -Password 123` <br> <br> 2. Angiv en svar-proxy, der ikke har brug for godkendelse:`Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080` <br> <br> 3. Ryd de svar proxyindstilling det vil sige, ikke skal bruge et svar proxy:`Set-OMSGatewayRelayProxy -Address ""` |
| `Get-OMSGatewayRelayProxy` |   | Bliver adressen på relay (mod) proxy | `Get-OMSGatewayRelayProxy` |
| `Add-OMSGatewayAllowedHost` | Host (påkrævet) | Tilføjer vært til listen over tilladte | `Add-OMSGatewayAllowedHost -Host www.test.com` |
| `Remove-OMSGatewayAllowedHos`t | Host (påkrævet) | Fjerner værten fra listen tilladte | `Remove-OMSGatewayAllowedHost -Host www.test.com` |
| `Get-OMSGatewayAllowedHost` |   | Bliver aktuelt tilladte værten (kun den lokalt konfigurerede tilladt host, du skal ikke medtage automatisk hentede tilladte hosts) | `Get-OMSGatewayAllowedHost` |
| `Add-OMSGatewayAllowedClientCertificate` | Emne (påkrævet) | Lægger klientcertifikat underlagt listen tilladte | `Add-OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Remove-OMSGatewayAllowedClientCertificate` | Emne (påkrævet) | Fjerner klient certifikatets emne på listen tilladte | `Remove- OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Get-OMSGatewayAllowedClientCertificat`e |   | Bliver aktuelt tilladte klienten certifikat emnerne (kun lokalt konfigurerede tilladte emnerne, ikke du skal medtage automatisk hentede tilladte emner) | `Get-OMSGatewayAllowedClientCertificate` |

## <a name="troubleshoot"></a>Fejlfinding i forbindelse med

Vi anbefaler, at du installerer OMS agent på computere, som har den gateway, der er installeret. Derefter kan du bruge agenten for at indsamle de hændelser, der er logget af gatewayen.

![Logbog – OMS Gateway Log](./media/log-analytics-oms-gateway/event-viewer.png)

**OMS Gateway begivenhed id'er og -beskrivelser**

Følgende tabel viser begivenhed id'er og beskrivelser for OMS Gateway Log begivenheder.

| **ID** | **Beskrivelse** |
| --- | --- |
| 400 | En hvilken som helst programfejl, der ikke har et bestemt ID |
| 401 | Forkert konfiguration. For eksempel: listenPort = "tekst" i stedet for et heltal |
| 402 | Undtagelse under fortolkning af TLS handshake meddelelser |
| 403 | Netværk fejl. For eksempel: kan ikke oprette forbindelse til destinationsserver |
| 100 | Generelle oplysninger |
| 101 | Tjenesten er startet |
| 102 | Tjenesten er stoppet |
| 103 | Modtog en HTTP FORBINDE kommando fra klient |
| 104 | Ikke en HTTP FORBINDE kommandoen |
| 105 | Destinations-serveren er ikke i listen over eller destinationsporten er ikke sikker port (443) <br> <br> Sørg for, at MMA agent på din Gateway-server og supportmedarbejdere kommunikere med gatewayen er sluttet til det samme Log Analytics-arbejdsområde.|
| 105 | FEJL TcpConnection – ugyldige klientcertifikat: CN = Gateway <br><br> Sørg for, at: <br> <br> & #149; Du bruger en Gateway med versionsnummeret 1.0.395.0 eller større. <br> & #149; MMA agent på din Gateway-server og supportmedarbejdere kommunikere med gatewayen er sluttet til det samme Log Analytics-arbejdsområde. |
| 106 | En eller anden grund, der TLS-session er mistænkelige og afvist |
| 107 | TLS-session er blevet bekræftet |

**Ydeevnen tællere til at indsamle**

Den følgende tabel viser de tilgængelige tællere i ydeevne for OMS gatewayen. Du kan føje tællere ved hjælp af Performance-skærm.

| **Navn** | **Beskrivelse** |
| --- | --- |
| OMS Gateway/aktiv klientforbindelse | Antallet af aktive klientforbindelser netværk (TCP) |
| Antal OMS Gateway/fejl | Antallet af fejl |
| OMS Gateway/forbindelse klient | Antallet af indbyrdes forbundne kunder |
| OMS Gateway/afvisning Tæl | Antallet af afviste invitationer på grund af enhver TLS valideringsfejl |

![OMS Gateway tællere i ydeevne](./media/log-analytics-oms-gateway/counters.png)


## <a name="get-assistance"></a>Få hjælp

Når du er logget på portalen Azure, kan du oprette en anmodning for at få hjælp med OMS gatewayen eller andre Azure service eller funktion i en tjeneste.
Klik på spørgsmålstegnet symbolet i øverste højre hjørne af portalen for at få hjælp, og klik derefter på **Ny understøtter anmodning**. Derefter skal du fuldføre den nye formular til anmodning om support.

![Ny anmodning om support](./media/log-analytics-oms-gateway/support.png)

Du kan også give feedback om OMS eller Log Analytics på [Microsoft Azure feedback-forum](https://feedback.azure.com/forums/267889).

## <a name="next-steps"></a>Næste trin

- [Tilføj datakilder](log-analytics-data-sources.md) for at indsamle data fra kilder forbindelse i arbejdsområdet OMS og gemme den i OMS-lager.
