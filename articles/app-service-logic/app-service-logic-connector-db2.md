<properties
   pageTitle="Ved hjælp af DB2 connector i Microsoft Azure-App tjeneste | Microsoft Azure"
   description="Sådan bruges DB2 forbindelsen med logik app udløsere og handlinger"
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

# <a name="db2-connector"></a>DB2 forbindelse
>[AZURE.NOTE] Denne version i denne artikel gælder for logik apps 2014-12-01-skema prøveversionen.

Microsoft connector til DB2 er en API-app til at oprette forbindelse til programmer via Azure App Service til ressourcer, der er gemt i en IBM DB2-database. Forbindelse omfatter Microsoft Client til at oprette forbindelse til eksterne DB2 servercomputere på tværs af en TCP/IP-netværksforbindelse, herunder Azure hybrid forbindelser til lokale DB2 servere ved hjælp af Azure Service Bus Relay forbindelse understøtter følgende databasehandlinger:

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


## <a name="create-the-db2-connector"></a>Oprette DB2-forbindelse
Du kan definere en forbindelse i en logik app eller fra Azure Marketplace, ligesom i følgende eksempel:  

1. Azure startboard, Vælg **Marketplace**.
2. Skriv **db2** i feltet **Søg i alt** i bladet **alt** , og klik derefter på tasten enter.
3. I feltet Søg alt resultater rude skal du vælge **DB2 forbindelse**.
4. Vælg **Opret**i bladet DB2 forbindelse beskrivelse.
5. Angiv navn (f.eks. "Db2ConnectorNewOrders"), App Service planlægge og andre egenskaber i bladet DB2 connector-pakke.
6. Vælg **Pakkeindstillinger**, og Angiv følgende Pakkeindstillinger for:  

    Navn | Påkrævet |  Beskrivelse
--- | --- | ---
ConnectionString | Ja | DB2 klient forbindelsesstreng (f.eks., "netværksadresse = servernavn; Network Port = 50000; Bruger-ID = brugernavn; Adgangskode = adgangskode; indledende katalog = eksempel; Pakke samling = NWIND; standard skema = NWIND ").
Tabeller | Ja | Kommasepareret liste over tabel, visning og alias navne, der kræves for OData-handlinger og til at generere swagger dokumentation med eksempler (f.eks. "*NEWORDERS*").
Procedurer | Ja | Kommasepareret liste over procedure og funktionen navne (f.eks. "SPORDERID").
Premise | Nej | Installere ved hjælp af Azure Service Bus Relay lokalt.
ServiceBusConnectionString | Nej | Azure Service Bus Relay-forbindelsesstrengen.
PollToCheckData | Nej | Vælg antal sætning til brug i en logik app udløser (fx "Vælg antal (\*) fra NEWORDERS hvor FORSENDELSESDATO IS NULL").
PollToReadData | Nej | SELECT-sætning til brug med en logik app udløser (fx "Vælg \* fra NEWORDERS, hvor FORSENDELSESDATO er NULL FOR opdatering").
PollToAlterData | Nej | Opdater eller sletningssætning til brug i en logik app udløser (fx "Opdater NEWORDERS angive FORSENDELSESDATO = dags dato WHERE CURRENT OF &lt;MARKØREN&gt;").

7. Vælg **OK**, og vælg derefter **Opret**.
8. Når du er færdig, ligner den Pakkeindstillinger følgende:  
![][1]


## <a name="logic-app-with-db2-connector-action-to-add-data"></a>Logik app med DB2 forbindelse handling til at tilføje data ##
Du kan definere handlingen logik app for at føje data til en DB2-tabel med en API Indsæt eller indlæg til OData-enhed operation. For eksempel kan du indsætte en ny kunde rækkefølge post, ved at behandle en indsætte SQL-sætning i en tabel, der er defineret med en id-kolonne, der returnerer værdien identitet eller rækker, der påvirkes til appen logik (Vælg ORDID fra endelige tabel (indsætte til NWIND. NEWORDERS (CUSTID, SEND, SHIPADDR, MODTAGERBY, SHIPREG, SHIPZIP) VÆRDIER (?,?,?,?,?,?))).

> [AZURE.TIP] DB2 forbindelse "*indlæg til sæt med enheder*" returnerer værdien identitet kolonne og "*API Indsæt*" returnerer rækker påvirkes

1. Vælg i den Azure startboard **+** (plustegn), **Web + Mobile**, og klik derefter **logik app**.
2. Angiv navn (f.eks. "NewOrdersDb2"), App Service planlægge, andre egenskaber, og vælg derefter **Opret**.
3. Vælg den logik app, du lige har oprettet, **Indstillinger**og derefter **udløsere og handlinger**i den Azure startboard.
4. Vælg **oprette fra bunden** logik App skabeloner i bladet udløsere og handlinger.
5. Vælg **Gentagelse**i panelet API Apps, angive en frekvens interval, og klik derefter **markering**.
6. Vælg **DB2 forbindelse**i panelet API Apps, Udvid listen handlinger for at vælge **Indsæt i NEWORDER**.
7. Udvid parameterlisten for at angive følgende værdier:  

    Navn | Værdi
--- | --- 
CUSTID | 10042
SEND | Fløde K Kountry Store 
SHIPADDR | 12 orkesters Terrace
MODTAGERBY | Walla Walla 
SHIPREG | WA
SHIPZIP | 99362 

8. Vælg **markering** til at gemme indstillingerne for Objekthandling og derefter **gemme**.
9. Indstillingerne skal se ud som følger:  
![][3]

10. Markér det første er anført på listen element (den seneste Kør) på listen **alle køres** under **Handlinger**. 
11. Vælg **handling** element **db2connectorneworders**i bladet **logik app køre** .
12. Vælg **Input LINK**i bladet **logik app handling** . DB2 forbindelse bruger input til at behandle en parameteriseret INSERT-sætning.
13. Vælg **Output LINK**i bladet **logik app handling** . Input skal se ud som følger:  
![][4]

#### <a name="what-you-need-to-know"></a>Hvad du skal kende

- Forbindelse afkorter DB2 tabelnavne, når der udgør logik app Handlingsnavne. For eksempel afkortes handlingen **indsætte i NEWORDERS** til at **indsætte i NEWORDER**.
- Når du har gemt logik app **udløsere og handlinger**, behandler logik app handlingen. Der kan være en forsinkelse af et antal sekunder (fx 3-5 sekunder), før logik app behandler handlingen. Du kan også du kan klikke på **Kør nu** for at behandle handlingen.
- DB2 forbindelse definerer sæt med enheder medlemmer med attributter, herunder om medlemmet svarer til en DB2 kolonne med en standard- eller genereret kolonner (fx identitet). Logik app viser en rød stjerne ud for sæt med enheder medlemmets ID navn, til angivelse af DB2 kolonner, der kræver værdier. Du skal ikke angiver en værdi for medlemmet ORDID, som svarer til DB2 id-kolonne. Du kan angive værdier for andre valgfri medlemmer (elementer ORDDATE REQDATE, SHIPID, fragt, og SHIPCTRY), som svarer til DB2 kolonner med standardværdier. 
- DB2 forbindelse, der returnerer til logik app svaret på Send til sæt med enheder, der indeholder værdierne for identitetskolonner, der er afledt DRDA SQLDARD (SQL Data område svardata) på forberedt indsætte SQL-sætning. DB2 server returnerer ikke de indsatte værdier for kolonner med standardværdier.  


## <a name="logic-app-with-db2-connector-action-to-add-bulk-data"></a>Logik app med DB2 forbindelse handling til at tilføje flere data ##
Du kan definere handlingen logik app for at føje data til en DB2-tabel ved hjælp af handlingen API flere indsætte. For eksempel kan du indsætte to nye rækkefølge kundeposter, ved at behandle en indsætte SQL-sætning ved hjælp af en matrix med rækkeværdier i en tabel, der er defineret med en id-kolonne, der returnerer rækker, der påvirkes til appen logik (Vælg ORDID fra endelige tabel (indsætte til NWIND. NEWORDERS (CUSTID, SEND, SHIPADDR, MODTAGERBY, SHIPREG, SHIPZIP) VÆRDIER (?,?,?,?,?,?))).

1. Vælg i den Azure startboard **+** (plustegn), **Web + Mobile**, og klik derefter **logik app**.
2. Angiv navn (f.eks. "NewOrdersBulkDb2"), App Service planlægge, andre egenskaber, og vælg derefter **Opret**.
3. Vælg den logik app, du lige har oprettet, **Indstillinger**og derefter **udløsere og handlinger**i den Azure startboard.
4. Vælg **oprette fra bunden** logik App skabeloner i bladet udløsere og handlinger.
5. Vælg **Gentagelse**i panelet API Apps, angive en frekvens interval, og klik derefter **markering**.
6. Vælg **DB2 forbindelse**i panelet API Apps, Udvid listen handlinger for at markere **Flere indsætte i ny**.
7. Angiv værdien, der **rækker** som en matrix. For eksempel kopiere og indsætte følgende:

    ```
    [{"CUSTID":10081,"SHIPNAME":"Trail's Head Gourmet Provisioners","SHIPADDR":"722 DaVinci Blvd.","SHIPCITY":"Kirkland","SHIPREG":"WA","SHIPZIP":"98034"},{"CUSTID":10088,"SHIPNAME":"White Clover Markets","SHIPADDR":"305 14th Ave. S. Suite 3B","SHIPCITY":"Seattle","SHIPREG":"WA","SHIPZIP":"98128","SHIPCTRY":"USA"}]
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

- Forbindelse afkorter DB2 tabelnavne, når der udgør logik app Handlingsnavne. For eksempel afkortes handlingen **Flere indsætte i NEWORDERS** til **Flere indsætte i ny**.
- Ved at udelade identitetskolonner (fx ORDID), null-værdi kolonner (fx FORSENDELSESDATO) og kolonner med standardværdier (fx ORDDATE, REQDATE, SHIPID, fragt, SHIPCTRY), genererer DB2-database værdier.
- Ved at angive "i dag" og "i morgen", DB2 forbindelse genererer "Dags dato" og "Dags dato + 1 dag" fungerer (fx REQDATE). 


## <a name="logic-app-with-db2-connector-trigger-to-read-alter-or-delete-data"></a>Logik app med DB2 forbindelse ved at læse, ændre eller slette data ##
Du kan angive en logik app udløser for at afstemning og læse data fra en DB2-tabel ved hjælp af handlingen API afstemning Data sammensatte. Eksempelvis finder du en eller flere nye rækkefølge kundeposter, der returnerer posterne til appen logik. Pakke/program DB2 forbindelsesindstillingerne skal se ud som følger:

    App-indstilling | Værdi
--- | --- | ---
PollToCheckData | Vælg antal (\*) fra NEWORDERS, hvor FORSENDELSESDATO er NULL
PollToReadData | Vælg \* fra NEWORDERS, hvor FORSENDELSESDATO er NULL FOR opdatering
PollToAlterData | <no value specified>


Du kan også angive en logik app udløser for at afstemning, læse og ændre data i en DB2-tabel ved hjælp af handlingen API afstemning Data sammensatte. Eksempelvis finder du en eller flere nye rækkefølge kundeposter, opdatere de rækkeværdier,, der returnerer de valgte (før opdatering) poster til appen logik. Pakke/program DB2 forbindelsesindstillingerne skal se ud som følger:

    App-indstilling | Værdi
--- | --- | ---
PollToCheckData | Vælg antal (\*) fra NEWORDERS, hvor FORSENDELSESDATO er NULL
PollToReadData | Vælg \* fra NEWORDERS, hvor FORSENDELSESDATO er NULL FOR opdatering
PollToAlterData | Opdater NEWORDERS sæt FORSENDELSESDATO = dags dato hvor aktuelle af &lt;MARKØREN&gt;


Desuden kan du definerer en logik app udløser for at afstemning, læse og fjerne data fra en DB2-tabel ved hjælp af handlingen API afstemning Data sammensatte. Eksempelvis finder du en eller flere nye rækkefølge kundeposter, slette de rækker, returnerer de valgte (før Slet) poster til appen logik. Pakke/program DB2 forbindelsesindstillingerne skal se ud som følger:

    App-indstilling | Værdi
--- | --- | ---
PollToCheckData | Vælg antal (\*) fra NEWORDERS, hvor FORSENDELSESDATO er NULL
PollToReadData | Vælg \* fra NEWORDERS, hvor FORSENDELSESDATO er NULL FOR opdatering
PollToAlterData | Slet NEWORDERS hvor aktuelle af &lt;MARKØREN&gt;

I dette eksempel vil logik app afstemning, læse, opdatere og Læs derefter dataene i tabellen DB2 igen.

1. Vælg i den Azure startboard **+** (plustegn), **Web + Mobile**, og klik derefter **logik app**.
2. Angiv navn (f.eks. "ShipOrdersDb2"), App Service planlægge, andre egenskaber, og vælg derefter **Opret**.
3. Vælg den logik app, du lige har oprettet, **Indstillinger**og derefter **udløsere og handlinger**i den Azure startboard.
4. Vælg **oprette fra bunden** logik App skabeloner i bladet udløsere og handlinger.
5. I panelet API Apps Markér **DB2 forbindelse**, skal du angive en frekvens og interval, og klik derefter **markering**.
6. Vælg **DB2 forbindelse**i panelet API Apps, Udvid listen handlinger for at vælge **Vælg NEWORDERS**.
7. Vælg **markering** til at gemme indstillingerne for Objekthandling og derefter **gemme**. Indstillingerne skal se ud som følger:  
![][10]  
8. Klik for at lukke bladet **udløsere og handlinger** , og klik derefter på for at lukke bladet **Indstillinger** .
9. Klik på det første er anført på listen element (den seneste Kør) på listen **alle kører** under **Handlinger**.
10. Klik på **handling** elementet i bladet **logik app køre** .
11. Klik på **Output LINK**i bladet **logik app handling** . Output skal se ud som følger:  
![][11]


## <a name="logic-app-with-db2-connector-action-to-remove-data"></a>Logik app med DB2 forbindelse handling til at fjerne data ##
Du kan definere handlingen logik app for at fjerne data fra en DB2-tabel med en API Slet eller indlæg til OData-enhed operation. For eksempel kan du indsætte en ny kunde rækkefølge post, ved at behandle en indsætte SQL-sætning i en tabel, der er defineret med en id-kolonne, der returnerer værdien identitet eller rækker, der påvirkes til appen logik (Vælg ORDID fra endelige tabel (indsætte til NWIND. NEWORDERS (CUSTID, SEND, SHIPADDR, MODTAGERBY, SHIPREG, SHIPZIP) VÆRDIER (?,?,?,?,?,?))).

## <a name="create-logic-app-using-db2-connector-to-remove-data"></a>Oprette logik app ved hjælp af DB2 connector til at fjerne data ##
Du kan oprette en ny logik-app fra Azure Marketplace og derefter bruge DB2 forbindelsen som en handling til at fjerne kundeordrer. For eksempel du kan bruge DB2 forbindelse betinget-sletning til at behandle en slette SQL-sætning (slette fra NEWORDERS hvor ORDID > = 10000).

1. Klik på i menuen hub i tavlen Azure **starte** **+** (plustegn), skal du klikke på **Web + Mobile**, og klik derefter på **logik app**. 
2. Skriv et **navn**, for eksempel **RemoveOrdersDb2**i bladet **oprette logik app** .
3. Vælg eller Definer værdier for de andre indstillinger (fx serviceaftale, ressourcegruppe).
4. Indstillingerne skal se ud som følger. Klik på **Opret**:  
![][12]  
5. Klik på **udløsere og handlinger**i bladet **Indstillinger** .
6. Klik på **Opret fra bunden**i bladet **udløsere og handlinger** på listen **logik app skabeloner** .
7. Klik på **Gentagelse**i bladet **udløsere og handlinger** i panelet **API Apps** i ressourcegruppen.
8. Designoverfladen logik app, skal du klikke på **Gentagelse** elementet, angive en **frekvens** og **Interval**, for eksempel **dage** og **1**, og klik derefter på **markering** for at gemme indstillingerne for gentagelse.
9. Klik på **DB2 connector**bladet **udløsere og handlinger** i panelet **API Apps** i ressourcegruppen.
10. På designoverfladen logik app skal du klikke på handlingspunkter **DB2 forbindelse** , klik på ellipserne (**...**) for at udvide listen handlinger og klik derefter på **Slet betinget fra N**.
11. Skriv **ORDID Flet 10000** for et **udtryk, der identificerer et undersæt af poster**på handlingspunkter DB2 forbindelse.
12. Klik på **markering** for at gemme indstillingerne for handlingen, og klik derefter på **Gem**. Indstillingerne skal se ud som følger:  
![][13]  
13. Klik for at lukke bladet **udløsere og handlinger** , og klik derefter på for at lukke bladet **Indstillinger** .
14. Klik på det første er anført på listen element (den seneste Kør) på listen **alle kører** under **Handlinger**.
15. Klik på **handling** elementet i bladet **logik app køre** .
16. Klik på **Output LINK**i bladet **logik app handling** . Output skal se ud som følger:  
![][14]

**Note:** Logik app designer afkorter tabelnavne. For eksempel afkortes handlingen **betinget slette fra NEWORDERS** til **betinget slette fra N**.


> [AZURE.TIP] Brug følgende SQL-sætningerne til at oprette den eksempeltabel og lagrede procedurer. 

Du kan oprette eksempeltabellen NEWORDERS ved hjælp af følgende DB2 SQL DDL-sætninger:
 
    CREATE TABLE ORDERS (  
        ORDID INT NOT NULL GENERATED BY DEFAULT AS IDENTITY (START WITH 10000, INCREMENT BY 1) ,  
        CUSTID INT NOT NULL ,  
        EMPID INT NOT NULL DEFAULT 10000 ,  
        ORDDATE DATE NOT NULL DEFAULT CURRENT DATE ,  
        REQDATE DATE DEFAULT CURRENT DATE ,  
        SHIPDATE DATE ,  
        SHIPID INT NOT NULL DEFAULT 10000,  
        FREIGHT DECIMAL (9,2) NOT NULL DEFAULT 0.00 ,  
        SHIPNAME CHAR (40) NOT NULL ,  
        SHIPADDR CHAR (60) NOT NULL ,  
        SHIPCITY CHAR (20) NOT NULL ,  
        SHIPREG CHAR (15) NOT NULL ,  
        SHIPZIP CHAR (10) NOT NULL ,  
        SHIPCTRY CHAR (15) NOT NULL DEFAULT 'USA' ,  
        PRIMARY KEY(ORDID)  
        )  
 
    CREATE UNIQUE INDEX XORDID ON ORDERS (ORDID ASC)  



Du kan oprette eksempel SPOERID gemt procedure ved hjælp af følgende DB2 DDL-sætning:
 
    CREATE OR REPLACE PROCEDURE NWIND.SPORDERID (IN ORDERID VARCHAR(128))  
        DYNAMIC RESULT SETS 1  
    P1: BEGIN  
        DECLARE CURSOR1 CURSOR WITH RETURN FOR  
            SELECT * FROM NWIND.NEWORDERS  
                WHERE ORDID = ORDERID;  
        OPEN CURSOR1;  
    END P1  
    ') 


## <a name="hybrid-configuration-optional"></a>Hybridkonfiguration (valgfrit)

> [AZURE.NOTE] Dette trin er påkrævet, kun, hvis du bruger DB2 forbindelse lokale bag firewallen.

App Service bruges Hybrid Configuration Manager til at oprette sikker forbindelse til dit lokale system. Hvis forbindelsen bruger en lokal IBM DB2 Server til Windows, er Forbindelsesstyring Hybrid påkrævet.

Under [Brug af Forbindelsesstyring Hybrid](app-service-logic-hybrid-connection-manager.md).


## <a name="do-more-with-your-connector"></a>Få mere ud af forbindelsen
Nu hvor forbindelsen er oprettet, kan du tilføje den til en arbejdsprocesser for virksomheder ved hjælp af en logik app. Se [Hvad er logik apps?](app-service-logic-what-are-logic-apps.md).

Oprette API Apps ved hjælp af REST API'er. Se [forbindelser og API Apps Reference](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Du kan også gennemse ydeevne statistik og kontrollere sikkerhed til forbindelsen. Se [administrere og overvåge dine indbyggede API Apps og forbindelser](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-db2/ApiApp_Db2Connector_Create.png
[2]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_Create.png
[3]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_TriggersActions.png
[4]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_Outputs.png
[5]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Create.png
[6]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_TriggersActions.png
[7]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Inputs.png
[8]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Outputs.png
[9]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_Create.png
[10]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_TriggersActions.png
[11]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_Outputs.png
[12]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_Create.png
[13]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_TriggersActions.png
[14]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_Outputs.png

