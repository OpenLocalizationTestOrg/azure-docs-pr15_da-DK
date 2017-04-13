<properties
    pageTitle="Komme i gang med Log Analytics | Microsoft Azure"
    description="Du kan få oppe at køre med Log Analytics i Microsoft Operations Management pakke (OMS) i minutter."
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
    ms.topic="get-started-article"
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="get-started-with-log-analytics"></a>Komme i gang med Log Analytics

Du kan få oppe at køre med Log Analytics i Microsoft Operations Management Suite (OMS) i minutter. Du har to muligheder, når du vælger, hvordan du opretter et OMS arbejdsområde, som ligner en konto:

- Microsoft Operations Management Suite websted
- Microsoft Azure-abonnement

Du kan oprette et gratis OMS arbejdsområde ved hjælp af webstedet OMS. Eller du kan bruge et Microsoft Azure-abonnement til at oprette et OMS arbejdsområde. Begge arbejdsområder er fungerer på samme måde, bortset fra at et gratis OMS arbejdsområde kan kun sende 500 MB data dagligt til OMS-tjenesten. Hvis du bruger et Azure-abonnement, kan du også bruge dette abonnement til at få adgang til andre Azure tjenester. Uanset hvilken metode du bruger til at oprette arbejdsområdet, skal du oprette arbejdsområdet med en Microsoft-konto eller organisationskonto.

Her kan du se nærmere på processen:

![onboarding diagram](./media/log-analytics-get-started/oms-onboard-diagram.png)

## <a name="log-analytics-prerequisites-and-deployment-considerations"></a>Log Analytics forudsætninger og overvejelser om installation

- Du skal bruge et betalt abonnement Microsoft Azure at udnytte Log analyser. Hvis du ikke har et Azure-abonnement, kan du oprette en [gratis konto](https://azure.microsoft.com/free/) , hvor du kan få adgang til en hvilken som helst Azure service. Eller du kan oprette en gratis OMS-konto på webstedet [Handlinger Management-pakken](http://microsoft.com/oms) , og klik på **Prøv gratis**.
- Et OMS arbejdsområde
- Alle Windows-computere, du vil indsamle data fra skal køre Windows Server 2008 SP1 eller nyere
- [Firewall](log-analytics-proxy-firewall.md) adgang til OMS tjenestens webadresser
- En server til [OMS Log Analytics domænes](https://blogs.technet.microsoft.com/msoms/2016/03/17/oms-log-analytics-forwarder) (Gateway) for at videresende trafik fra servere til OMS, hvis internetadgang ikke er tilgængelig fra computere
- Hvis du bruger Operations Manager, Log Analytics understøtter Operations Manager 2012 SP1 UR6 og over og Operations Manager 2012 R2 UR2 og derover. Proxyunderstøttelse af blev tilføjet i Operations Manager 2012 SP1 UR7 og Operations Manager 2012 R2 UR3. Find ud af, hvordan den vil blive integreret med OMS.
- Afgøre, om computerne har direkte adgang til internettet. Hvis ikke, kræver de en gatewayserver for at få adgang til OMS-tjenesten websteder. Alle adgang er via HTTPS.
- Find ud af, hvilke teknologier og servere, der sender data til OMS. Eksempel: domæne enheder SQL Server, osv.
- Giv tilladelse til brugere i OMS og Azure.
- Hvis du er bekymret for databrug, installere hver løsning individuelt og teste ydeevnen resultatet før du tilføjer yderligere løsninger.
- Gennemgå din brug af data og ydeevnen, når du tilføjer løsninger og funktioner i Log analyser. Dette omfatter indsamling af hændelser, log af websteder, ydeevne dataindsamling osv. Det er bedre skal starte med minimale samling indtil databrug eller identificeret indflydelse på ydeevnen.
- Kontroller, at Windows supportmedarbejdere ikke også administreres ved hjælp af Operations Manager, ellers duplikerede data kan medføre. Dette gælder også for Azure-baseret-supportmedarbejdere, der har Azure diagnosticering aktiveret.
- Når du installerer supportmedarbejdere, kan du kontrollere, at agenten fungerer korrekt. Hvis ikke, kontrolleret, at kryptering API: næste generering af (CNG) nøgle isolationsniveauet ikke er deaktiveret ved hjælp af gruppepolitik.
- Nogle Log Analytics-løsninger, der har ekstra krav



## <a name="sign-up-in-3-steps-using-the-operations-management-suite"></a>Tilmelde dig med 3 trin i pakken handlinger administration

1. Gå til webstedet [Handlinger Management-pakken](http://microsoft.com/oms) , og klik på **Prøv gratis**. Logge på med din Microsoft-konto som Outlook.com eller med en organisationskonto, der er angivet af din virksomhed eller informative institution skal bruges sammen med Office 365 eller andre Microsoft-tjenester.
2. Angiv et entydigt arbejdsområdenavn. Et arbejdsområde er en logisk beholder, hvor dataene management er gemt. Det giver dig en måde at partition data mellem forskellige teams i din organisation, som dataene er eksklusiv til arbejdsområdet. Angiv en mailadresse og det område, hvor du vil have dine data, der er gemt.  
    ![Opret arbejdsområde og sammenkæde abonnement](./media/log-analytics-get-started/oms-onboard-create-workspace-link01.png)
3. Derefter kan du oprette en ny Azure abonnement eller et link til et eksisterende Azure-abonnement. Hvis du gerne vil fortsætte med at bruge den gratis prøveversion, skal du klikke på **Ikke nu**.  
  ![Opret arbejdsområde og sammenkæde abonnement](./media/log-analytics-get-started/oms-onboard-create-workspace-link02.png)

Du er klar til at komme i gang med portalen handlinger Management Suite.

Du kan få mere at vide om at konfigurere dit arbejdsområde og sammenkædning af eksisterende Azure-konti til arbejdsområder, der er oprettet med handlinger Management Suite på [Administrer adgang til Log analyser](log-analytics-manage-access.md).

## <a name="sign-up-quickly-using-microsoft-azure"></a>Tilmelde dig hurtigt ved hjælp af Microsoft Azure

1. Gå til [Azure portal](https://portal.azure.com) og logge på, gennemse listen over tjenester, og vælg derefter **Log Analytics (OMS)**.  
    ![Azure-portalen](./media/log-analytics-get-started/oms-onboard-azure-portal.png)
2. Klik på **Tilføj**, og derefter vælge valgmuligheder for følgende elementer:
    - Navn på **OMS arbejdsområde**
    - **Abonnement** - Hvis du har flere abonnementer, Vælg den, du vil knytte til det nye arbejdsområde.
    - **Ressourcegruppe**
    - **Placering**
    - **Priser niveau**  
        ![Hurtig oprettelse](./media/log-analytics-get-started/oms-onboard-quick-create.png)
3. Klik på **Opret** , og du får vist arbejdsområdet oplysningerne i portalen Azure.       
    ![oplysninger om arbejdsområde](./media/log-analytics-get-started/oms-onboard-workspace-details.png)         
4. Klik på linket **OMS Portal** for at åbne webstedet handlinger Management pakke med det nye arbejdsområde.

Du er klar til at begynde at bruge portalen handlinger Management-pakken.

Du kan få mere at vide om at konfigurere dit arbejdsområde og sammenkæde eksisterende arbejdsområder, du har oprettet med handlinger Management-pakken til Azure abonnementer på [Administrer adgang til Log analyser](log-analytics-manage-access.md).

## <a name="get-started-with-the-operations-management-suite-portal"></a>Introduktion til portalen handlinger Management pakke
Klik på feltet **Indstillinger** for at vælge løsninger og forbinde de servere, du vil administrere, og følg trinnene i dette afsnit.  

![komme i gang](./media/log-analytics-get-started/oms-onboard-get-started.png)  

1. **Tilføje løsninger** - få vist dine installerede løsninger.  
    ![løsninger](./media/log-analytics-get-started/oms-onboard-solutions.png)  
    Klik på **Besøg galleriet** for at tilføje flere løsninger.  
    ![løsninger](./media/log-analytics-get-started/oms-onboard-solutions02.png)  
    Vælg en løsning, og klik derefter på **Tilføj**.
2. **Forbinde en kilde** - Vælg, hvordan du vil oprette forbindelse til din server-miljø at indsamle data:
    - Forbinde en hvilken som helst Windows Server eller klient direkte ved at installere en agent.
    - Tilslut Linux-servere med OMS Agent for Linux.
    - Brug en Azure-lager-konto, der er konfigureret med Windows eller Linux Azure diagnosticering VM filtypenavnet.
    - Brug System Center Operations Manager for at føje dine management grupper eller hele Operations Manager installationen.
    - Aktivere Windows Telemetri bruge opgradere analyser.
        ![tilknyttede kilder](./media/log-analytics-get-started/oms-onboard-data-sources.png)    

3. **Indsamle data** Konfigurere mindst én datakilde for at udfylde data i dit arbejdsområde. Når du er færdig, skal du klikke på **Gem**.    

    ![indsamle data](./media/log-analytics-get-started/oms-onboard-logs.png)    


## <a name="optionally-connect-servers-directly-to-the-operations-management-suite-by-installing-an-agent"></a>Du kan også oprette forbindelse servere direkte til den handlinger Management pakke ved at installere en agent

I følgende eksempel viser, hvordan du installerer en Windows-agent.

1. Klik på feltet **Indstillinger** , klik på fanen **Forbindelse kilder** , skal du klikke på en fane for kildetypen, du vil tilføje, og hente en agent, eller få mere at vide om, hvordan du aktiverer en agent. For eksempel skal du klikke på **Hent Windows Agent (64-bit)**. Du kan kun installere agent på Windows Server 2008 SP 1 eller senere eller på Windows 7 SP1 eller nyere til Windows supportmedarbejdere.
2. Installér agenten på en eller flere servere. Du kan installere supportmedarbejdere én ad gangen, eller ved hjælp af en mere automatiseret metode med et [brugerdefineret script](log-analytics-windows-agents.md), eller du kan bruge en eksisterende fordeling løsning til software, som du muligvis.
3. Når du accepterer licensaftalen, og du vælger installationsmappen, kan du vælge **Opret forbindelse agent til Azure Log Analytics (OMS)**.   
    ![konfiguration af Agent](./media/log-analytics-get-started/oms-onboard-agent.png)

4. På den næste side, bliver du spurgt til dit arbejdsområde-ID og din arbejdsområde nøgle. Dit arbejdsområde-ID og din nøgle vises på skærmen, hvor du har overført agent filen.  
    ![Agent taster](./media/log-analytics-get-started/oms-onboard-mma-keys.png)  

    ![vedhæfte servere](./media/log-analytics-get-started/oms-onboard-key.png)
5. Under installationen, kan du klikke på **Avanceret** for at du kan også konfigurere din proxyserver og angive oplysninger til godkendelse. Klik på knappen **Næste** for at vende tilbage til skærmbilledet oplysninger om arbejdsområde.
6. Klik på **Næste** for at validere dit arbejdsområde-ID og din nøgle. Hvis der bliver fundet fejl, kan du klikke på **tilbage** for at foretage rettelser. Når dit arbejdsområde-ID og din nøgle er blevet godkendt, skal du klikke på **Installer** for at fuldføre installationen agent.
7. I Kontrolpanel, skal du klikke på Microsoft overvågning Agent > Azure Log Analytics (OMS) under fanen. Et grønt fluebensikon vises, når supportmedarbejdere kommunikere med tjenesten handlinger Management-pakken. I første omgang føres omkring 5-10 minutter.

>[AZURE.NOTE] Kapacitet administration og konfiguration vurdering løsningerne understøttes ikke i øjeblikket af servere, der er tilsluttet direkte til den handlinger Management pakke.


Du kan også forbinde agent til System Center Operations Manager 2012 SP1 og nyere. Gør du ved at vælge **Opret forbindelse agent til System Center Operations Manager**. Når du vælger, indstilling, du sender data til tjenesten uden yderligere hardware, eller indlæse på dine administration af grupper.

Du kan læse mere om at oprette forbindelse supportmedarbejdere til handlinger Management pakke på [forbinde Windows-computere at Log analyser](log-analytics-windows-agents.md).

## <a name="optionally-connect-servers-using-system-center-operations-manager"></a>Du kan også oprette forbindelse servere ved hjælp af System Center Operations Manager

1. Vælg **Administration**i konsollen Operations Manager.
2. Udvid noden **Funktionsdygtige viden** , og vælg **Funktionsdygtige indsigt forbindelse**.

  >[AZURE.NOTE] Afhængigt af hvilken opdatering opsummering af SCOM du bruger, kan du se en node til *System Center Advisor*, *Funktionsdygtige indsigt*eller *Handlinger Management-pakken*.

3. Klik på linket **registrere til funktionsdygtige indsigt** med i øverste højre hjørne, og følg vejledningen.
4. Når du har afsluttet guiden Onlineregistrering, skal du klikke på linket **Tilføj en Computer/gruppe** .
5. I dialogboksen **Søg Computer** kan du søge efter computere eller grupper overvåges af Operations Manager. Vælg computere eller grupper til indbyggede dem til Log Analytics, klik på **Tilføj**, og klik derefter på **OK**. Du kan kontrollere, at tjenesten OMS modtager data ved at gå til feltet **brugen** i portalen handlinger Management-pakken. Data skal vises i omkring 5-10 minutter.

Du kan læse mere om at oprette forbindelse Operations Manager til handlinger Management pakke på [Forbinde Operations Manager til Log analyser](log-analytics-om-agents.md).

## <a name="optionally-analyze-data-from-cloud-services-in-microsoft-azure"></a>Du kan også analysere data fra skytjenester i Microsoft Azure

Med handlinger Management-pakken, kan du hurtigt søge IIS logfiler til skytjenester og virtuelle maskiner og begivenhed ved at aktivere diagnosticering til Azure Cloud Services. Du kan også modtage flere indsigt til din Azure virtuelle maskiner ved at installere Microsoft overvågning Agent. Du kan læse mere om at konfigurere dit Azure miljø for at bruge handlinger Management Suite på [forbinde Azure-lager til Log analyser](log-analytics-azure-storage.md).


## <a name="next-steps"></a>Næste trin

- [Tilføje Log Analytics løsninger fra løsningsgalleriet](log-analytics-add-solutions.md) til at tilføje funktionalitet og indsamle data.
- Bliv fortrolig med [log søgninger](log-analytics-log-searches.md) til at få vist detaljerede oplysninger, der indsamles af løsninger.
- Bruge [dashboards](log-analytics-dashboards.md) til at gemme og få vist dine egne brugerdefinerede søgninger.
