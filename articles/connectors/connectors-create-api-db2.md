<properties
    pageTitle="Tilføje DB2 forbindelsen i dine logik Apps | Microsoft Azure"
    description="Oversigt over DB2 forbindelse med REST-API parametre"
    services=""
    documentationCenter="" 
    authors="gplarsen"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="09/26/2016"
   ms.author="plarsen"/>


# <a name="get-started-with-the-db2-connector"></a>Komme i gang med DB2-forbindelse
Microsoft-forbindelse til DB2 forbinder logik Apps til ressourcer, der er gemt i en IBM DB2-database. Denne forbindelse indeholder en Microsoft-klient til at kommunikere med eksterne DB2 servercomputere på tværs af et TCP/IP-netværk. Dette omfatter skyen databaser, som IBM Bluemix dashDB eller IBM DB2 til Windows, der kører i Azure virtualization og lokale databaser ved hjælp af gatewayen lokale data. Se [understøttede liste](connectors-create-api-db2.md#supported-db2-platforms-and-versions) over IBM DB2-platforme og versioner (i dette emne).

>[AZURE.NOTE] Denne version i denne artikel gælder for logik Apps generelt tilgængelig (GA). 

DB2 forbindelsen understøtter følgende databasehandlinger:

- Listen databasetabeller
- Læse en række ved at vælge
- Læs alle rækker ved hjælp af Vælg
- Tilføje en række ved hjælp af Indsæt
- Ændre en række ved hjælp af UPDATE
- Fjerne en række ved hjælp af Slet

Dette emne viser, hvordan du bruger forbindelsen i en logik app til proces databasehandlinger.

Hvis du vil vide mere om logik Apps skal du se [oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="available-actions"></a>Tilgængelige handlinger
DB2 forbindelsen understøtter følgende logik app handlinger:

- GetTables
- GetRow
- GetRows
- InsertRow
- UpdateRow
- DeleteRow


## <a name="list-tables"></a>Liste over tabeller
Oprette en logik app til en hvilken som helst handling består af mange trin, der udføres via Microsoft Azure-portalen.

Logik App, kan du føje en handling til listen tabeller i en DB2-database. Handlingen, der får forbindelsen til at behandle en DB2 skema sætning såsom `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Oprette en logik app
1.  I den **Azure start tavle**, skal du vælge **+** (plustegn), **Web + Mobile**, og klik derefter **Logik App**.
2.  Skriv **navnet**, såsom `Db2getTables`, **abonnement**, **ressourcegruppe**, **placering**og **App Service planlægge**. Vælg **Fastgør til dashboard**, og vælg derefter **Opret**.

### <a name="add-a-trigger-and-action"></a>Tilføje en udløser og en handling
1.  Vælg **Tom LogicApp** i listen over **skabeloner** i **Logik Apps Designer**.
2.  Vælg på listen **Udløsere** **Gentagelse**. 
3.  I udløser **Gentagelse** Klik på **Rediger**, Vælg **hyppighed** rullemenuen til at vælge **dag**og derefter angive **intervallet** til at skrive **7**.  
4.  Markér afkrydsningsfeltet **+ nyt trin** , og vælg derefter **Tilføj en handling**.
5.  I listen **Handlinger** skal du skrive `db2` felt til redigering i feltet **Søg efter flere handlinger** , og vælg derefter **DB2 - få tabeller (Preview)**.

    ![](./media/connectors-create-api-db2/Db2connectorActions.png)  

6.  Marker **afkrydsningsfeltet** for at aktivere **Opret forbindelse via lokale datastyringsgateway**i ruden **DB2 - få tabeller** konfiguration. Bemærk, at indstillingerne ændres fra skyen til en lokal installation.
    - Angiv værdi til **Server**i form af adresse eller alias kolon portnummeret. Skriv f.eks `ibmserver01:50000`.
    - Angiv værdi til **databasen**. Skriv f.eks `nwind`.
    - Vælg værdi til **godkendelse**. For eksempel vælge **grundlæggende**.
    - Angiv værdi til **brugernavn**. Skriv f.eks `db2admin`.
    - Angiv værdi til **adgangskode**. Skriv f.eks `Password1`.
    - Vælg værdi for **Gateway**. For eksempel vælge **datagateway01**.
7. Vælg **Opret**, og vælg derefter **Gem**. 

    ![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)

8.  Markere det første er anført på listen element (den seneste Kør...) i bladet **Db2getTables** i listen **alle kører** under **Oversigt**.
9.  Vælg **Kør oplysninger**i bladet **logik app køre** . Vælg **Get_tables**i listen **handling** . Du kan se værdien for **Status**, som bør være **lykkedes**. Vælg det **input link** til at få vist materialer. Vælg den **udskriver link**, og få vist output; som bør omfatte en liste over tabeller.

    ![](./media/connectors-create-api-db2/Db2connectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>Oprette forbindelserne
Denne forbindelse understøtter forbindelser til lokale databaser, der er hostet og i skyen ved hjælp af følgende egenskaber for forbindelse. 

Egenskaben | Beskrivelse
--- | ---
Server | Der er påkrævet. Accepterer en strengværdi, der repræsenterer en TCP/IP-adresse eller et alias i IPv4 eller IPv6-format, der følges (kolon afgrænset) af et TCP/IP-port tal. 
database | Der er påkrævet. Accepterer en strengværdi, der repræsenterer en DRDA relationel Database navn (RDBNAM). DB2 for z/OS accepterer en 16-byte-streng (database er kendt som en IBM DB2 for z/OS placering). DB2 for i5/OS accepterer en 18-byte-streng (database, der kaldes en IBM DB2 for i relationel database). DB2 for LUW accepterer en 8-byte-streng.
godkendelse | Valgfrit. Accepterer elementværdi på en liste, enten Basic eller Windows (kerberos). 
brugernavn | Der er påkrævet. Accepterer en strengværdi. DB2 for z/OS accepterer en 8-byte-streng. DB2 for jeg accepterer en 10-byte-streng. DB2 for Linux eller UNIX accepterer en 8-byte-streng. DB2 til Windows accepterer en 30-byte-streng.
adgangskode | Der er påkrævet. Accepterer en strengværdi.
gateway | Der er påkrævet. Accepterer elementværdi på en liste, der repræsenterer lokale data gatewayen defineret til logik Apps i gruppen lagerplads.  

## <a name="create-the-on-premises-gateway-connection"></a>Oprette lokale gateway-forbindelse
Denne forbindelse kan få adgang til en lokal DB2-database ved hjælp af den lokale gateway. Se emner i gateway kan finde flere oplysninger. 

1. Markér **afkrydsningsfeltet** for at aktivere **Opret forbindelse via gateway**i ruden **Gateways** konfiguration. Bemærk, at indstillingerne ændres fra skyen til en lokal installation.
2. Angiv værdi til **Server**i form af adresse eller alias kolon portnummeret. Skriv f.eks `ibmserver01:50000`.
3. Angiv værdi til **databasen**. Skriv f.eks `nwind`.
4. Vælg værdi til **godkendelse**. For eksempel vælge **grundlæggende**.
5. Angiv værdi til **brugernavn**. Skriv f.eks `db2admin`.
6. Angiv værdi til **adgangskode**. Skriv f.eks `Password1`.
7. Vælg værdi for **Gateway**. For eksempel vælge **datagateway01**.
8. Vælg **Opret** for at fortsætte. 

    ![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>Oprette skyforbindelse
Denne forbindelse kan få adgang til en skybaseret DB2-database. 

1. Lad **afkrydsningsfeltet** deaktiveret i ruden **Gateways** konfiguration (ikke klikket på) **Opret forbindelse via gateway**. 
2. Angiv værdi til **forbindelsesnavn**. Skriv f.eks `hisdemo2`.
3. Skriv værdien for **servernavn DB2**i form af adresse eller alias kolon portnummeret. Skriv f.eks `hisdemo2.cloudapp.net:50000`.
3. Angiv værdi til **DB2 databasenavn**. Skriv f.eks `nwind`.
4. Angiv værdi til **brugernavn**. Skriv f.eks `db2admin`.
5. Angiv værdi til **adgangskode**. Skriv f.eks `Password1`.
6. Vælg **Opret** for at fortsætte. 

    ![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Hente alle rækker ved hjælp af Vælg
Du kan definere handlingen logik app for at hente alle rækker i en DB2-tabel. Dette giver forbindelsen til at behandle en DB2 SELECT-sætning, såsom `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Oprette en logik app
1.  I den **Azure start tavle**, skal du vælge **+** (plustegn), **Web + Mobile**, og klik derefter **Logik App**.
2.  Skriv **navnet**, såsom `Db2getRows`, **abonnement**, **ressourcegruppe**, **placering**og **App Service planlægge**. Vælg **Fastgør til dashboard**, og vælg derefter **Opret**.

### <a name="add-a-trigger-and-action"></a>Tilføje en udløser og en handling
1.  Vælg **Tom LogicApp** i listen over **skabeloner** i **Logik Apps Designer**.
2.  Vælg på listen **Udløsere** **Gentagelse**. 
3.  Klik på **Rediger**i udløser **Gentagelse** skal vælge **frekvens** rullelisten Vælg **dag**, og derefter vælge **Interval** til at skrive **7**. 
4.  Markér afkrydsningsfeltet **+ nyt trin** , og vælg derefter **Tilføj en handling**.
5.  I listen **Handlinger** skal du skrive `db2` felt til redigering i feltet **Søg efter flere handlinger** , og vælg derefter **DB2 - få rækker (Preview)**.
6. Vælg **Skift forbindelse**i handlingen **få rækker (Preview)** .
7. Vælg **Opret ny**i ruden **forbindelser** konfiguration. 

    ![](./media/connectors-create-api-db2/Db2connectorNewConnection.png)
  
8. Lad **afkrydsningsfeltet** deaktiveret i ruden **Gateways** konfiguration (ikke klikket på) **Opret forbindelse via gateway**.
    - Angiv værdi til **forbindelsesnavn**. Skriv f.eks `HISDEMO2`.
    - Skriv værdien for **servernavn DB2**i form af adresse eller alias kolon portnummeret. Skriv f.eks `HISDEMO2.cloudapp.net:50000`.
    - Angiv værdi til **DB2 databasenavn**. Skriv f.eks `NWIND`.
    - Angiv værdi til **brugernavn**. Skriv f.eks `db2admin`.
    - Angiv værdi til **adgangskode**. Skriv f.eks `Password1`.
9. Vælg **Opret** for at fortsætte.

    ![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)

10. Vælg **pil ned**på listen **tabelnavn** , og vælg derefter **område**.
11. Du kan vælge **Vis avancerede indstillinger** for at angive forespørgselsindstillinger.
12. Vælg **Gem**. 

    ![](./media/connectors-create-api-db2/Db2connectorGetRowsTableName.png)

13. Markere det første er anført på listen element (den seneste Kør...) i bladet **Db2getRows** i listen **alle kører** under **Oversigt**.
14. Vælg **Kør oplysninger**i bladet **logik app køre** . Vælg **Get_rows**i listen **handling** . Du kan se værdien for **Status**, som bør være **lykkedes**. Vælg det **input link** til at få vist materialer. Vælg den **udskriver link**, og få vist output; som bør omfatte en liste over rækker.

    ![](./media/connectors-create-api-db2/Db2connectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Tilføje en række ved hjælp af Indsæt
Du kan definere handlingen logik app for at tilføje en række i en DB2-tabel. Denne handling får forbindelsen til at behandle en DB2 indsætte sætning såsom `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Oprette en logik app
1.  I den **Azure start tavle**, skal du vælge **+** (plustegn), **Web + Mobile**, og klik derefter **Logik App**.
2.  Skriv **navnet**, såsom `Db2insertRow`, **abonnement**, **ressourcegruppe**, **placering**og **App Service planlægge**. Vælg **Fastgør til dashboard**, og vælg derefter **Opret**.

### <a name="add-a-trigger-and-action"></a>Tilføje en udløser og en handling
1.  Vælg **Tom LogicApp** i listen over **skabeloner** i **Logik Apps Designer**.
2.  Vælg på listen **Udløsere** **Gentagelse**. 
3.  Klik på **Rediger**i udløser **Gentagelse** skal vælge **frekvens** rullelisten Vælg **dag**, og derefter vælge **Interval** til at skrive **7**. 
4.  Markér afkrydsningsfeltet **+ nyt trin** , og vælg derefter **Tilføj en handling**.
5.  Skriv **db2** i feltet **Søg efter flere handlinger** Rediger på listen **Handlinger** , og vælg derefter **DB2 - Indsæt række (Preview)**.
6. Vælg **Skift forbindelse**i handlingen **få rækker (Preview)** . 
7. Vælg en forbindelse i ruden **forbindelser** konfiguration. For eksempel vælge **hisdemo2**.

    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. Vælg **pil ned**på listen **tabelnavn** , og vælg derefter **område**.
9. Angiv værdier for alle de påkrævede kolonner (se den røde stjerne). Skriv f.eks `99999` **AREAID**, Skriv `Area 99999`, og skriv `102` for **REGIONID**. 
10. Vælg **Gem**.

    ![](./media/connectors-create-api-db2/Db2connectorInsertRowValues.png)
 
11. Markere det første er anført på listen element (den seneste Kør...) i bladet **Db2insertRow** i listen **alle kører** under **Oversigt**.
12. Vælg **Kør oplysninger**i bladet **logik app køre** . Vælg **Get_rows**i listen **handling** . Du kan se værdien for **Status**, som bør være **lykkedes**. Vælg det **input link** til at få vist materialer. Vælg den **udskriver link**, og få vist output; der skal indeholde den nye række.

    ![](./media/connectors-create-api-db2/Db2connectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Hente en række ved at vælge
Du kan definere handlingen logik app for at hente en række i en DB2-tabel. Denne handling får forbindelsen til at behandle en DB2 Vælg hvor sætning, såsom `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Oprette en logik app
1.  I den **Azure start tavle**, skal du vælge **+** (plustegn), **Web + Mobile**, og klik derefter **Logik App**.
2.  Angiv **navn** (f.eks. "**Db2getRow**"), **abonnement**, **ressourcegruppe**, **placering**og **App Service planlægge**. Vælg **Fastgør til dashboard**, og vælg derefter **Opret**.

### <a name="add-a-trigger-and-action"></a>Tilføje en udløser og en handling
1.  Vælg **Tom LogicApp** i listen over **skabeloner** i **Logik Apps Designer**. 
2.  Vælg på listen **Udløsere** **Gentagelse**. 
3.  Klik på **Rediger**i udløser **Gentagelse** skal vælge **frekvens** rullelisten Vælg **dag**, og derefter vælge **Interval** til at skrive **7**. 
4.  Markér afkrydsningsfeltet **+ nyt trin** , og vælg derefter **Tilføj en handling**.
5.  Skriv **db2** i feltet **Søg efter flere handlinger** Rediger på listen **Handlinger** , og vælg derefter **DB2 - få rækker (Preview)**.
6. Vælg **Skift forbindelse**i handlingen **få rækker (Preview)** . 
7. Vælg en eksisterende forbindelse i ruden **forbindelser** konfigurationer. For eksempel vælge **hisdemo2**.

    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. Vælg **pil ned**på listen **tabelnavn** , og vælg derefter **område**.
9. Angiv værdier for alle de påkrævede kolonner (se den røde stjerne). Skriv f.eks `99999` for **AREAID**. 
10. Du kan vælge **Vis avancerede indstillinger** for at angive forespørgselsindstillinger.
11. Vælg **Gem**. 

    ![](./media/connectors-create-api-db2/Db2connectorGetRowValues.png)

12. Markere det første er anført på listen element (den seneste Kør...) i bladet **Db2getRow** i listen **alle kører** under **Oversigt**.
13. Vælg **Kør oplysninger**i bladet **logik app køre** . Vælg **Get_rows**i listen **handling** . Du kan se værdien for **Status**, som bør være **lykkedes**. Vælg det **input link** til at få vist materialer. Vælg den **udskriver link**, og få vist output; som bør omfatte række.

    ![](./media/connectors-create-api-db2/Db2connectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Ændre en række ved hjælp af UPDATE
Du kan definere handlingen logik app for at ændre én række i en DB2-tabel. Denne handling får forbindelsen til at behandle en DB2 UPDATE-sætning, såsom `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Oprette en logik app
1.  I den **Azure start tavle**, skal du vælge **+** (plustegn), **Web + Mobile**, og klik derefter **Logik App**.
2.  Skriv **navnet**, såsom `Db2updateRow`, **abonnement**, **ressourcegruppe**, **placering**og **App Service planlægge**. Vælg **Fastgør til dashboard**, og vælg derefter **Opret**.

### <a name="add-a-trigger-and-action"></a>Tilføje en udløser og en handling
1.  Vælg **Tom LogicApp** i listen over **skabeloner** i **Logik Apps Designer**.
2.  Vælg på listen **Udløsere** **Gentagelse**. 
3.  Klik på **Rediger**i udløser **Gentagelse** skal vælge **frekvens** rullelisten Vælg **dag**, og derefter vælge **Interval** til at skrive **7**. 
4.  Markér afkrydsningsfeltet **+ nyt trin** , og vælg derefter **Tilføj en handling**.
5.  Skriv **db2** i feltet **Søg efter flere handlinger** Rediger på listen **Handlinger** , og vælg derefter **DB2 - opdatering række (Preview)**.
6. Vælg **Skift forbindelse**i handlingen **få rækker (Preview)** . 
7. Vælg til at vælge en eksisterende forbindelse i ruden **forbindelser** konfigurationer. For eksempel vælge **hisdemo2**.

    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. Vælg **pil ned**på listen **tabelnavn** , og vælg derefter **område**.
9. Angiv værdier for alle de påkrævede kolonner (se den røde stjerne). Skriv f.eks `99999` **AREAID**, Skriv `Updated 99999`, og skriv `102` for **REGIONID**. 
10. Vælg **Gem**. 

    ![](./media/connectors-create-api-db2/Db2connectorUpdateRowValues.png)

11. Markere det første er anført på listen element (den seneste Kør...) i bladet **Db2updateRow** i listen **alle kører** under **Oversigt**.
12. Vælg **Kør oplysninger**i bladet **logik app køre** . Vælg **Get_rows**i listen **handling** . Du kan se værdien for **Status**, som bør være **lykkedes**. Vælg det **input link** til at få vist materialer. Vælg den **udskriver link**, og få vist output; der skal indeholde den nye række.

    ![](./media/connectors-create-api-db2/Db2connectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Fjerne en række ved hjælp af Slet
Du kan definere handlingen logik app for at fjerne én række i en DB2-tabel. Denne handling får forbindelsen til at behandle en DB2 slette sætning såsom `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Oprette en logik app
1.  I den **Azure start tavle**, skal du vælge **+** (plustegn), **Web + Mobile**, og klik derefter **Logik App**.
2.  Skriv **navnet**, såsom `Db2deleteRow`, **abonnement**, **ressourcegruppe**, **placering**og **App Service planlægge**. Vælg **Fastgør til dashboard**, og vælg derefter **Opret**.

### <a name="add-a-trigger-and-action"></a>Tilføje en udløser og en handling
1.  Vælg **Tom LogicApp** i listen over **skabeloner** i **Logik Apps Designer**. 
2.  Vælg på listen **Udløsere** **Gentagelse**. 
3.  Klik på **Rediger**i udløser **Gentagelse** skal vælge **frekvens** rullelisten Vælg **dag**, og derefter vælge **Interval** til at skrive **7**. 
4.  Markér afkrydsningsfeltet **+ nyt trin** , og vælg derefter **Tilføj en handling**.
5.  Vælg **db2** i feltet **Søg efter flere handlinger** Rediger på listen **Handlinger** , og vælg derefter **DB2 - Slet række (Preview)**.
6. Vælg **Skift forbindelse**i handlingen **få rækker (Preview)** . 
7. Vælg en eksisterende forbindelse i ruden **forbindelser** konfigurationer. For eksempel vælge **hisdemo2**.

    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. Vælg **pil ned**på listen **tabelnavn** , og vælg derefter **område**.
9. Angiv værdier for alle de påkrævede kolonner (se den røde stjerne). Skriv f.eks `99999` for **AREAID**. 
10. Vælg **Gem**. 

    ![](./media/connectors-create-api-db2/Db2connectorDeleteRowValues.png)

11. Markere det første er anført på listen element (den seneste Kør...) i bladet **Db2deleteRow** i listen **alle kører** under **Oversigt**.
12. Vælg **Kør oplysninger**i bladet **logik app køre** . Vælg **Get_rows**i listen **handling** . Du kan se værdien for **Status**, som bør være **lykkedes**. Vælg det **input link** til at få vist materialer. Vælg den **udskriver link**, og få vist output; som bør omfatte den slettede række.

    ![](./media/connectors-create-api-db2/Db2connectorDeleteRowOutputs.png)

## <a name="technical-details"></a>Tekniske detaljer

## <a name="actions"></a>Handlinger
En handling er en handling, der er foretaget af den arbejdsproces, der er defineret i en logik app. Databaseforbindelsen DB2 omfatter følgende handlinger. 

|Handling|Beskrivelse|
|--- | ---|
|[GetRow](connectors-create-api-db2.md#get-row)|Henter en enkelt række fra en DB2-tabel|
|[GetRows](connectors-create-api-db2.md#get-rows)|Henter rækker fra en DB2-tabel|
|[InsertRow](connectors-create-api-db2.md#insert-row)|Indsætter en ny række i en DB2-tabel|
|[DeleteRow](connectors-create-api-db2.md#delete-row)|Sletter en række fra en DB2-tabel|
|[GetTables](connectors-create-api-db2.md#get-tables)|Henter tabeller fra en DB2-database|
|[UpdateRow](connectors-create-api-db2.md#update-row)|Opdaterer en eksisterende række i en DB2-tabel|

### <a name="action-details"></a>Handlingsdetaljer

I dette afsnit, skal du se specifikke oplysninger om hver handling, herunder eventuelle nødvendig eller valgfri input egenskaber og noget tilsvarende output, der er knyttet til forbindelsen.

#### <a name="get-row"></a>Få række 
Henter en enkelt række fra en DB2-tabel.  

| Egenskabsnavn| Vist navn |Beskrivelse|
| ---|---|---|
|tabel * | Tabelnavn |Navnet på DB2-tabel|
|id * | Række-id |Entydigt id for rækken for at hente|

En stjerne (*) betyder, at egenskaben er påkrævet.

##### <a name="output-details"></a>Output detaljer
Element

| Egenskabsnavn | Datatype |
|---|---|
|ItemInternalId|streng|


#### <a name="get-rows"></a>Få rækker 
Henter rækker fra en DB2-tabel.  

|Egenskabsnavn| Vist navn|Beskrivelse|
| ---|---|---|
|tabel *|Tabelnavn|Navnet på DB2-tabel|
|$skip|Spring over Tæl|Antallet af poster at springe (standard = 0)|
|$top|Maksimale få Tæl|Maksimale antal poster til at hente (standard = 256)|
|$filter|Filtrere forespørgsel|En forespørgsel til ODATA-filter til at begrænse antallet af poster|
|$orderby|Sortér efter|En ODATA SorterEfter forespørgsel for at angive rækkefølgen af poster|

En stjerne (*) betyder, at egenskaben er påkrævet.

##### <a name="output-details"></a>Output detaljer
ItemsList

| Egenskabsnavn | Datatype |
|---|---|
|værdi|matrix|


#### <a name="insert-row"></a>Indsætte række 
Indsætter en ny række i en DB2-tabel.  

|Egenskabsnavn| Vist navn|Beskrivelse|
| ---|---|---|
|tabel *|Tabelnavn|Navnet på DB2-tabel|
|element *|Række|Træk for at indsætte i den angivne tabel i DB2|

En stjerne (*) betyder, at egenskaben er påkrævet.

##### <a name="output-details"></a>Output detaljer
Element

| Egenskabsnavn | Datatype |
|---|---|
|ItemInternalId|streng|


#### <a name="delete-row"></a>Slet række 
Sletter en række fra en DB2-tabel.  

|Egenskabsnavn| Vist navn|Beskrivelse|
| ---|---|---|
|tabel *|Tabelnavn|Navnet på DB2-tabel|
|id *|Række-id|Entydigt id for rækken for at slette|

En stjerne (*) betyder, at egenskaben er påkrævet.

##### <a name="output-details"></a>Output detaljer
Ingen.

#### <a name="get-tables"></a>Få tabeller 
Henter tabeller fra en DB2-database.  

Der er ingen parametre til dette opkald. 

##### <a name="output-details"></a>Output detaljer 
TablesList

| Egenskabsnavn | Datatype |
|---|---|
|værdi|matrix|

#### <a name="update-row"></a>Opdatere række 
Opdaterer en eksisterende række i en DB2-tabel.  

|Egenskabsnavn| Vist navn|Beskrivelse|
| ---|---|---|
|tabel *|Tabelnavn|Navnet på DB2-tabel|
|id *|Række-id|Entydigt id for rækken for at opdatere|
|element *|Række|Række med opdaterede værdier|

En stjerne (*) betyder, at egenskaben er påkrævet.

##### <a name="output-details"></a>Output detaljer  
Element

| Egenskabsnavn | Datatype |
|---|---|
|ItemInternalId|streng|


### <a name="http-responses"></a>HTTP-svar

Når du foretager opkald til de forskellige handlinger, kan du få bestemte svar. Den følgende tabel beskrives svarene, og beskrivelserne:  

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|202|Accepteret|
|400|Forkert anmodning|
|401|Uautoriseret|
|403|Forbudt|
|404|Blev ikke fundet|
|500|Intern serverfejl. Der opstod en ukendt fejl|
|standard|Mislykkedes.|


## <a name="supported-db2-platforms-and-versions"></a>Understøttede DB2 platforme og versioner
Denne forbindelse understøtter på følgende IBM DB2-platforme og versioner, samt IBM DB2 kompatible produkter (fx IBM Bluemix dashDB), der understøtter distribueret relationel Database arkitektur (DRDA) SQL Access Manager (SQLAM) version 10 og 11:

- IBM DB2 for z/OS 11.1
- IBM DB2 for z/OS 10.1
- IBM DB2 for i 7.3
- IBM DB2 for i 7.2
- IBM DB2 for i 7.1
- IBM DB2 LUW 11
- IBM DB2 for LUW 10.5


## <a name="next-steps"></a>Næste trin

[Opret en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md). Udforske andre tilgængelige forbindelserne i logik Apps i vores [API'er liste](apis-list.md).

