<properties
   pageTitle="Ved hjælp af Informix connector i Microsoft Azure-App tjeneste | Microsoft Azure"
   description="Sådan bruges Informix forbindelsen med logik app udløsere og handlinger"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="gplarsen"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="plarsen"/>

# <a name="informix-connector"></a>Informix forbindelse
>[AZURE.NOTE] Denne version i denne artikel gælder for logik apps 2014-12-01-skema prøveversionen.

Microsoft connector til Informix er en API-app til at oprette forbindelse til programmer via Azure App Service til ressourcer, der er gemt i en IBM Informix-database. Forbindelse omfatter Microsoft Client til at oprette forbindelse til eksterne Informix servercomputere på tværs af en TCP/IP-netværksforbindelse, herunder Azure hybrid forbindelser til lokale Informix servere ved hjælp af Azure Service Bus Relay. Forbindelse understøtter følgende databasehandlinger:

- Læs rækker ved hjælp af Vælg
- Afstemning at læse rækker ved hjælp af Vælg tæller efterfulgt af Vælg
- Tilføje en række eller flere (flere) rækker ved hjælp af Indsæt
- Ændre én række eller flere (flere) rækker ved hjælp af UPDATE
- Fjerne én række eller flere (flere) rækker ved hjælp af Slet
- Læst for at ændre rækker ved hjælp af Vælg MARKØREN efterfulgt af opdatering hvor aktuelle af MARKØR
- Læst for at fjerne rækker ved hjælp af Vælg MARKØREN efterfulgt af opdatering hvor aktuelle af MARKØR
- Køre procedure med input- og parametre, returværdi, resultset, ved hjælp af opkald
- Brugerdefinerede kommandoer og sammensatte handlinger ved hjælp af Vælg, Indsæt, opdatere, slette

## <a name="triggers-and-actions"></a>Udløsere og handlinger
Forbindelse understøtter følgende logik app udløsere og handlinger:

Udløsere | Handlinger
--- | ---
<ul><li>Afstemning Data</li></ul> | <ul><li>Flere Indsæt</li><li>Indsæt</li><li>Masseopdatering</li><li>Opdater</li><li>Opkald</li><li>Flere Slet</li><li>Slet</li><li>Vælg</li><li>Betinget opdatering</li><li>Indlæg til sæt med enheder</li><li>Betinget Slet</li><li>Vælg enkelt objekt</li><li>Slet</li><li>Upsert til sæt med enheder</li><li>Brugerdefinerede kommandoer</li><li>Sammensat handlinger</li></ul>


## <a name="create-the-informix-connector"></a>Oprette Informix-forbindelse
Du kan definere en forbindelse i en logik app eller fra Azure Marketplace, ligesom i følgende eksempel:  

1. Azure startboard, Vælg **Marketplace**.
2. Skriv **informix** i feltet **Søg i alt** i bladet **alt** , og klik derefter på tasten enter.
3. I feltet Søg alt resultater rude skal du vælge **Informix forbindelse**.
4. Vælg **Opret**i bladet Informix forbindelse beskrivelse.
5. Angiv navn (f.eks. "InformixConnectorNewOrders"), App Service planlægge og andre egenskaber i bladet Informix connector-pakke.
6. Vælg **Pakkeindstillinger**, og Angiv følgende Pakkeindstillinger for.

    Navn | Påkrævet |  Beskrivelse
--- | --- | ---
ConnectionString | Ja | Informix klient forbindelsesstreng (f.eks., "netværksadresse = servernavn; Network Port = 9089; Bruger-ID = brugernavn; Adgangskode = adgangskode; indledende katalog = nwind; standard skema = informix ").
Tabeller | Ja | Kommasepareret liste over tabel, visning og alias navne, der kræves for OData-handlinger og til at generere swagger dokumentation med eksempler (f.eks. "NEWORDERS").
Procedurer | Ja | Kommasepareret liste over procedure og funktionen navne (f.eks. "SPORDERID").
Premise | Nej | Installere ved hjælp af Azure Service Bus Relay lokalt.
ServiceBusConnectionString | Nej | Azure Service Bus Relay-forbindelsesstrengen.
PollToCheckData | Nej | Vælg antal sætning til brug i en logik app udløser (fx "Vælg antal (\*) fra NEWORDERS hvor FORSENDELSESDATO IS NULL").
PollToReadData | Nej | SELECT-sætning til brug med en logik app udløser (fx "Vælg \* fra NEWORDERS, hvor FORSENDELSESDATO er NULL FOR opdatering").
PollToAlterData | Nej | Opdater eller sletningssætning til brug i en logik app udløser (fx "Opdater NEWORDERS angive FORSENDELSESDATO = dags dato WHERE CURRENT OF &lt;MARKØREN&gt;").

7. Vælg **OK**, og vælg derefter **Opret**.
8. Når du er færdig, ligner den Pakkeindstillinger følgende:  
![][1]


## <a name="logic-app-with-informix-connector-action-to-add-data"></a>Logik app med Informix forbindelse handling til at tilføje data ##
Du kan definere handlingen logik app for at føje data til en Informix-tabel med en API Indsæt eller indlæg til OData-enhed operation. For eksempel du kan indsætte en ny kunde rækkefølge post, ved at behandle en indsætte SQL-sætning i en tabel, der er defineret med en id-kolonne, der returnerer værdien identitet eller rækker, der påvirkes til appen logik (Vælg ORDID fra endelige tabel (indsætte i NEWORDERS (CUSTID, Send, SHIPADDR, MODTAGERBY, SHIPREG, SHIPZIP) værdier (?,?,?,?,?,?))).

> [AZURE.TIP] Informix forbindelse "*indlæg til sæt med enheder*" returnerer værdien identitet kolonne og "*API Indsæt*" returnerer rækker påvirkes

1. Vælg i den Azure startboard **+** (plustegn), **Web + Mobile**, og klik derefter **logik app**.
2. Angiv navn (f.eks. "NewOrdersInformix"), App Service planlægge, andre egenskaber, og vælg derefter **Opret**.
3. Vælg den logik app, du lige har oprettet, **Indstillinger**og derefter **udløsere og handlinger**i den Azure startboard.
4. Vælg **oprette fra bunden** logik App skabeloner i bladet udløsere og handlinger.
5. Vælg **Gentagelse**i panelet API Apps, angive en frekvens interval, og klik derefter **markering**.
6. Vælg **Informix forbindelse**i panelet API Apps, Udvid listen handlinger for at vælge **Indsæt i NEWORDER**.
7. Udvid parameterlisten for at angive følgende værdier:  

    Navn | Værdi
--- | --- 
CUSTID | 10042
SHIPID | 10000
SEND | Fløde K Kountry Store 
SHIPADDR | 12 orkesters Terrace
MODTAGERBY | Walla Walla 
SHIPREG | WA
SHIPZIP | 99362 

8. Vælg **markering** til at gemme indstillingerne for Objekthandling og derefter **gemme**.
9. Indstillingerne skal se ud som følger:  
![][3]  
10. Markér det første er anført på listen element (den seneste Kør) på listen **alle køres** under **Handlinger**. 
11. Vælg **handling** element **informixconnectorneworders**i bladet **logik app køre** .
12. Vælg **Input LINK**i bladet **logik app handling** . Informix forbindelse bruger input til at behandle en parameteriseret INSERT-sætning.
13. Vælg **Output LINK**i bladet **logik app handling** . Input skal se ud som følger:  
![][4]

#### <a name="what-you-need-to-know"></a>Hvad du skal kende

- Forbindelse afkorter Informix tabelnavne, når der udgør logik app Handlingsnavne. For eksempel afkortes handlingen **indsætte i NEWORDERS** til at **indsætte i NEWORDER**.
- Når du har gemt logik app **udløsere og handlinger**, behandler logik app handlingen. Der kan være en forsinkelse af et antal sekunder (fx 3-5 sekunder), før logik app behandler handlingen. Du kan også du kan klikke på **Kør nu** for at behandle handlingen.
- Informix forbindelse definerer sæt med enheder medlemmer med attributter, herunder om medlemmet svarer til en Informix kolonne med en standard- eller genereres kolonner (fx identitet). Logik app viser en rød stjerne ud for sæt med enheder medlemmets ID navn, til angivelse af Informix kolonner, der kræver værdier. Du skal ikke angiver en værdi for medlemmet ORDID, som svarer til Informix id-kolonne. Du kan angive værdier for andre valgfri medlemmer (elementer ORDDATE REQDATE, SHIPID, fragt, og SHIPCTRY), som svarer til Informix kolonner med standardværdier. 
- Informix forbindelse, der returnerer til logik app svaret på Send til sæt med enheder, der indeholder værdierne for identitetskolonner, der er afledt DRDA SQLDARD (SQL Data område svardata) på forberedt indsætte SQL-sætning. Informix server returnerer ikke de indsatte værdier for kolonner med standardværdier.  


## <a name="logic-app-with-informix-connector-action-to-add-bulk-data"></a>Logik app med Informix forbindelse handling til at tilføje flere data ##
Du kan definere handlingen logik app for at føje data til en Informix-tabel ved hjælp af handlingen API flere indsætte. For eksempel kan du indsætte to nye rækkefølge kundeposter, ved at behandle en indsætte SQL-sætning ved hjælp af en matrix med rækkeværdier i en tabel, der er defineret med en id-kolonne, der returnerer rækker, der påvirkes til appen logik (Vælg ORDID fra endelige tabel (indsætte i NEWORDERS (CUSTID, Send, SHIPADDR, MODTAGERBY, SHIPREG, SHIPZIP) værdier (?,?,?,?,?,?))).

1. Vælg i den Azure startboard **+** (plustegn), **Web + Mobile**, og klik derefter **logik app**.
2. Angiv navn (f.eks. "NewOrdersBulkInformix"), App Service planlægge, andre egenskaber, og vælg derefter **Opret**.
3. Vælg den logik app, du lige har oprettet, **Indstillinger**og derefter **udløsere og handlinger**i den Azure startboard.
4. Vælg **oprette fra bunden** logik App skabeloner i bladet udløsere og handlinger.
5. Vælg **Gentagelse**i panelet API Apps, angive en frekvens interval, og klik derefter **markering**.
6. Vælg **Informix forbindelse**i panelet API Apps, Udvid listen handlinger for at markere **Flere indsætte i ny**.
7. Angiv værdien, der **rækker** som en matrix. For eksempel kopiere og indsætte følgende:  

    ```
    [{"custid":10081,"shipid":10000,"shipname":"Trail's Head Gourmet Provisioners","shipaddr":"722 DaVinci Blvd.","shipcity":"Kirkland","shipreg":"WA","shipzip":"98034"},{"custid":10088,"shipid":10000,"shipname":"White Clover Markets","shipaddr":"305 14th Ave. S. Suite 3B","shipcity":"Seattle","shipreg":"WA","shipzip":"98128","shipctry":"USA"}]
    ```
        
8. Vælg **markering** til at gemme indstillingerne for Objekthandling og derefter **gemme**. Indstillingerne skal se ud som følger:  
![][6]

9. Klik på det første er anført på listen element (den seneste Kør) på listen **alle kører** under **Handlinger**.
10. Klik på **handling** elementet i bladet **logik app køre** .
11. Klik på **Input LINK**i bladet **logik app handling** . Output skal se ud som følger:  
[][7]
12. Klik på **Output LINK**i bladet **logik app handling** . Output skal se ud som følger:  
![][8]

#### <a name="what-you-need-to-know"></a>Hvad du skal kende

- Forbindelse afkorter Informix tabelnavne, når der udgør logik app Handlingsnavne. For eksempel afkortes handlingen **Flere indsætte i NEWORDERS** til **Flere indsætte i ny**.
- Informix database kan være bogstaver til tabel- og kolonnenavne. For eksempel kolonnenavne flere indsætte handlingen matrix muligvis være angivet i små bogstaver ("custid") i stedet for store bogstaver ("CUSTID").
- Ved at udelade identitetskolonner (fx ORDID), null-værdi kolonner (fx FORSENDELSESDATO) og kolonner med standardværdier (fx ORDDATE, REQDATE, SHIPID, fragt, SHIPCTRY), genererer Informix database værdier.
- Ved at angive "i dag" og "i morgen", Informix forbindelse genererer "Dags dato" og "Dags dato + 1 dag" fungerer (fx REQDATE). 


## <a name="logic-app-with-informix-connector-trigger-to-read-alter-or-delete-data"></a>Logik app med Informix forbindelse ved at læse, ændre eller slette data ##
Du kan angive en logik app udløser for at afstemning og læse data fra en Informix-tabel ved hjælp af handlingen API afstemning Data sammensatte. Eksempelvis finder du en eller flere nye rækkefølge kundeposter, der returnerer posterne til appen logik. Pakke/program Informix forbindelsesindstillingerne skal se ud som følger:

    App-indstilling | Værdi
--- | --- | ---
PollToCheckData | Vælg antal (\*) fra NEWORDERS, hvor FORSENDELSESDATO er NULL
PollToReadData | Vælg \* fra NEWORDERS, hvor FORSENDELSESDATO er NULL FOR opdatering
PollToAlterData | <no value specified>


Du kan også angive en logik app udløser for at afstemning, læse og ændre data i en Informix-tabel ved hjælp af handlingen API afstemning Data sammensatte. Eksempelvis finder du en eller flere nye rækkefølge kundeposter, opdatere de rækkeværdier,, der returnerer de valgte (før opdatering) poster til appen logik. Pakke/program Informix forbindelsesindstillingerne skal se ud som følger:

    App-indstilling | Værdi
--- | --- | ---
PollToCheckData | Vælg antal (\*) fra NEWORDERS, hvor FORSENDELSESDATO er NULL
PollToReadData | Vælg \* fra NEWORDERS, hvor FORSENDELSESDATO er NULL FOR opdatering
PollToAlterData | Opdater NEWORDERS sæt FORSENDELSESDATO = dags dato hvor aktuelle af &lt;MARKØREN&gt;


Desuden kan du definerer en logik app udløser for at afstemning, læse og fjerne data fra en Informix-tabel ved hjælp af handlingen API afstemning Data sammensatte. Eksempelvis finder du en eller flere nye rækkefølge kundeposter, slette de rækker, returnerer de valgte (før Slet) poster til appen logik. Pakke/program Informix forbindelsesindstillingerne skal se ud som følger:

    App-indstilling | Værdi
--- | --- | ---
PollToCheckData | Vælg antal (\*) fra NEWORDERS, hvor FORSENDELSESDATO er NULL
PollToReadData | Vælg \* fra NEWORDERS, hvor FORSENDELSESDATO er NULL FOR opdatering
PollToAlterData | Slet NEWORDERS hvor aktuelle af &lt;MARKØREN&gt;

I dette eksempel vil logik app afstemning, læse, opdatere og Læs derefter dataene i tabellen Informix igen.

1. Vælg i den Azure startboard **+** (plustegn), **Web + Mobile**, og klik derefter **logik app**.
2. Angiv navn (f.eks. "ShipOrdersInformix"), App Service planlægge, andre egenskaber, og vælg derefter **Opret**.
3. Vælg den logik app, du lige har oprettet, **Indstillinger**og derefter **udløsere og handlinger**i den Azure startboard.
4. Vælg **oprette fra bunden** logik App skabeloner i bladet udløsere og handlinger.
5. I panelet API Apps Markér **Informix forbindelse**, skal du angive en frekvens og interval, og klik derefter **markering**.
6. Vælg **Informix forbindelse**i panelet API Apps, Udvid listen handlinger for at vælge **Vælg NEWORDERS**.
7. Vælg **markering** til at gemme indstillingerne for Objekthandling og derefter **gemme**. Indstillingerne skal se ud som følger:  
![][10]
8. Klik for at lukke bladet **udløsere og handlinger** , og klik derefter på for at lukke bladet **Indstillinger** .
9. Klik på det første er anført på listen element (den seneste Kør) på listen **alle kører** under **Handlinger**.
10. Klik på **handling** elementet i bladet **logik app køre** .
11. Klik på **Output LINK**i bladet **logik app handling** . Output skal se ud som følger:  
![][11]


## <a name="logic-app-with-informix-connector-action-to-remove-data"></a>Logik app med Informix forbindelse handling til at fjerne data ##
Du kan definere handlingen logik app for at fjerne data fra en Informix-tabel med en API Slet eller indlæg til OData-enhed operation. For eksempel du kan indsætte en ny kunde rækkefølge post, ved at behandle en indsætte SQL-sætning i en tabel, der er defineret med en id-kolonne, der returnerer værdien identitet eller rækker, der påvirkes til appen logik (Vælg ORDID fra endelige tabel (indsætte i NEWORDERS (CUSTID, Send, SHIPADDR, MODTAGERBY, SHIPREG, SHIPZIP) værdier (?,?,?,?,?,?))).

## <a name="create-logic-app-using-informix-connector-to-remove-data"></a>Oprette logik app ved hjælp af Informix connector til at fjerne data ##
Du kan oprette en ny logik-app fra Azure Marketplace og derefter bruge Informix forbindelsen som en handling til at fjerne kundeordrer. For eksempel du kan bruge Informix forbindelse betinget-sletning til at behandle en slette SQL-sætning (slette fra NEWORDERS hvor ORDID > = 10000).

1. Klik på i menuen hub i tavlen Azure **starte** **+** (plustegn), skal du klikke på **Web + Mobile**, og klik derefter på **logik app**. 
2. Skriv et **navn**, for eksempel **RemoveOrdersInformix**i bladet **oprette logik app** .
3. Vælg eller Definer værdier for de andre indstillinger (fx serviceaftale, ressourcegruppe).
4. Indstillingerne skal se ud som følger. Klik på **Opret**:  
![][12]
5. Klik på **udløsere og handlinger**i bladet **Indstillinger** .
6. Klik på **Opret fra bunden**i bladet **udløsere og handlinger** på listen **logik app skabeloner** .
7. Klik på **Gentagelse**i bladet **udløsere og handlinger** i panelet **API Apps** i ressourcegruppen.
8. Designoverfladen logik app, skal du klikke på **Gentagelse** elementet, angive en **frekvens** og **Interval**, for eksempel **dage** og **1**, og klik derefter på **markering** for at gemme indstillingerne for gentagelse.
9. Klik på **Informix connector**bladet **udløsere og handlinger** i panelet **API Apps** i ressourcegruppen.
10. På designoverfladen logik app skal du klikke på handlingspunkter **Informix forbindelse** , klik på ellipserne (**...**) for at udvide listen handlinger og klik derefter på **Slet betinget fra N**.
11. Skriv på handlingspunkter Informix forbindelse **ordid Flet 10000** for et **udtryk, der identificerer et undersæt af poster**.
12. Klik på **markering** for at gemme indstillingerne for handlingen, og klik derefter på **Gem**. Indstillingerne skal se ud som følger:  
![][13]
13. Klik for at lukke bladet **udløsere og handlinger** , og klik derefter på for at lukke bladet **Indstillinger** .
14. Klik på det første er anført på listen element (den seneste Kør) på listen **alle kører** under **Handlinger**.
15. Klik på **handling** elementet i bladet **logik app køre** .
16. Klik på **Output LINK**i bladet **logik app handling** . Output skal se ud som følger:  
![][14]

**Note:** Logik app designer afkorter tabelnavne. For eksempel afkortes handlingen **betinget slette fra NEWORDERS** til **betinget slette fra N**.


> [AZURE.TIP] Brug følgende SQL-sætningerne til at oprette den eksempeltabel og lagrede procedurer. 

Du kan oprette eksempeltabellen NEWORDERS ved hjælp af følgende Informix SQL DDL-sætninger:
 
    create table neworders (  
        ordid serial(10000) unique ,  
        custid int not null ,  
        empid int not null default 10000 ,  
        orddate date not null default today ,  
        reqdate date default today ,  
        shipdate date ,  
        shipid int not null default 10000 ,  
        freight decimal (9,2) not null default 0.00 ,  
        shipname char (40) not null ,  
        shipaddr char (60) not null ,  
        shipcity char (20) not null ,  
        shipreg char (15) not null ,  
        shipzip char (10) not null ,  
        shipctry char (15) not null default ''USA'' 
        )


Du kan oprette eksempel SPORDERID gemt procedure ved hjælp af følgende Informix DDL-sætning:
 
    create procedure sporderid ( ord_id int)  
        returning int, int, int, date, date, date, int, decimal (9,2), char (40), char (60), char (20), char (15), char (10), char (15)  
        define xordid, xcustid, xempid, xshipid int;  
        define xorddate, xreqdate, xshipdate date;  
        define xfreight decimal (9,2);  
        define xshipname char (40);  
        define xshipaddr char (60);  
        define xshipcity char (20);  
        define xshipreg, xshipctry char (15);  
        define xshipzip char (10);  
        select ordid, custid, empid, orddate, reqdate, shipdate, shipid, freight, shipname, shipaddr, shipcity, shipreg, shipzip, shipctry  
            into xordid, xcustid, xempid, xorddate, xreqdate, xshipdate, xshipid, xfreight, xshipname, xshipaddr, xshipcity, xshipreg, xshipzip, xshipctry  
            from neworders where ordid = ord_id;  
        return xordid, xcustid, xempid, xorddate, xreqdate, xshipdate, xshipid, xfreight, xshipname, xshipaddr, xshipcity, xshipreg, xshipzip, xshipctry;  
    end procedure; 


## <a name="hybrid-configuration-optional"></a>Hybridkonfiguration (valgfrit)

> [AZURE.NOTE] Dette trin er påkrævet, kun, hvis du bruger DB2 forbindelse lokale bag firewallen.

App Service bruges Hybrid Configuration Manager til at oprette sikker forbindelse til dit lokale system. Hvis forbindelsen bruger en lokal IBM DB2 Server til Windows, er Forbindelsesstyring Hybrid påkrævet.

Under [Brug af Forbindelsesstyring Hybrid](app-service-logic-hybrid-connection-manager.md).


## <a name="do-more-with-your-connector"></a>Få mere ud af forbindelsen
Nu hvor forbindelsen er oprettet, kan du tilføje den til en arbejdsprocesser for virksomheder ved hjælp af en logik app. Se [Hvad er logik apps?](app-service-logic-what-are-logic-apps.md).

Oprette API Apps ved hjælp af REST API'er. Se [forbindelser og API Apps Reference](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Du kan også gennemse ydeevne statistik og kontrollere sikkerhed til forbindelsen. Se [administrere og overvåge dine indbyggede API Apps og forbindelser](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-informix/ApiApp_InformixConnector_Create.png
[2]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_Create.png
[3]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_TriggersActions.png
[4]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_Outputs.png
[5]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Create.png
[6]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_TriggersActions.png
[7]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Inputs.png
[8]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Outputs.png
[9]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_Create.png
[10]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_TriggersActions.png
[11]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_Outputs.png
[12]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_Create.png
[13]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_TriggersActions.png
[14]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_Outputs.png


