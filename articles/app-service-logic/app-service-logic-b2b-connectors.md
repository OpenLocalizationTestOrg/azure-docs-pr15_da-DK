<properties 
    pageTitle="Business to Business forbindelser og API Apps i logik Apps | Microsoft Azure" 
    description="Lær at oprette og konfigurere eller-Brugergrupper, EDIFACT, AS2 og TPM-forbindelser. microservices arkitektur" 
    services="logic-apps" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="anneta" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="mandia"/> 

# <a name="business-to-business-connectors-and-api-apps"></a>Business to Business forbindelser og API Apps

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Logik Apps indeholder mange BizTalk-API Apps, der er vigtig for integration miljøer. Disse API-Apps er baseret på begreber og værktøjer, der bruges i F.eks, men er nu tilgængelig som en del af logik Apps. 

En kategori af disse API-Apps er Business to Business (B2B) API Apps. Brug af disse B2B API Apps, du kan nemt tilføje partnere, oprette aftaler og gøre alt det, som du ville lokalt ved hjælp af eller-Brugergrupper, AS2 og EDIFACT.  

Disse B2B API Apps tilbyder "Udløser" og "Handling". En udløser starter en ny forekomst, der er baseret på en bestemt hændelse, som modtagelse af en X12 meddelelse fra en partner. En handling er resultatet, som når du modtager en X12 meddelelse, og send meddelelsen ved hjælp af AS2.


## <a name="what-is-a-business-to-business-connector-or-api-apps"></a>Hvad er en Business to Business forbindelse eller API Apps
Funktionen Business to Business (B2B) indeholder eksisterende, færdigbyggede API-Apps, der tillader forskellige firmaer, divisioner, programmer, og så videre kan kommunikere ved hjælp af AS2, eller-Brugergrupper og EDIFACT. 

B2B API Apps omfatter: 

Forbindelse eller API Apps | Beskrivelse
--- | ---
BizTalk handel Partner administration | En API-App, som opretter business to business (B2B) relationer ved hjælp af partnere og aftaler. Disse relationer anvender AS2, EDIFACT og X12 protocol.<br/><br/>TPM API App er af grundlæggende kravet om AS2 forbindelsen, og X12 eller EDIFACT API Apps. 
AS2 forbindelse | En forbindelse, der modtager og sender meddelelser ved hjælp af AS2 transport. Forbindelsen transporter data sikkert og pålideligt via internettet.
BizTalk EDIFACT | En API-App, der modtager og sender meddelelser ved hjælp af EDIFACT. EDIFACT kaldes også ofte UN/EDIFACT (United Nations/elektronisk Data Interchange til Administration, handel og Transport), og der anvendes på tværs af virksomheder.
BizTalk X12 | En API-App, der modtager og sender meddelelser ved hjælp af X12 protocol. X12 kaldes også ofte ASC X 12 (godkendt standarder udvalg X12), og der anvendes på tværs af virksomheder. 


Ved hjælp af disse API-Apps, kan du udføre forskellige eller-Brugergrupper messaging opgaver. For eksempel ved hjælp af AS2 connector, du kan sikkert sende og modtage forskellige typer meddelelser (eller-Brugergrupper, XML, flad-fil, og så videre) til en kunde, som en afdeling i virksomheden hr-afdeling eller alle, der bruger AS2. 

Du kan oprette så mange API Apps, som du vil og opretter dem nemt. Du kan også genbruge en enkelt API App i flere scenarier eller arbejdsprocesser.

Du kan gøre dette uden at skrive kode. Lad os komme i gang. 


## <a name="requirements-to-get-started"></a>Krav til at komme i gang
Når du opretter B2B API Apps, er der nogle ressourcer, der er påkrævet. Disse elementer skal oprettes ved du, før de kan bruges i andre API-Apps. Disse krav, omfatter: 

Krav | Beskrivelse
--- | ---
Azure SQL-Database | Gemmer B2B elementer, herunder partnere, skemaer, certifikater og agreeements. Hver af de B2B API Apps kræver sin egen Azure SQL-Database. <br/><br/>**Bemærk!** Kopiere forbindelsesstrengen til denne database.<br/><br/>[Oprette en Azure SQL-Database](../sql-database/sql-database-get-started.md)
Azure Blob-lager objektbeholder | Gemmer meddelelse egenskaber, når AS2 arkivering er aktiveret. Hvis du ikke behøver AS2 meddelelse arkivering, er en objektbeholder til lagring ikke er nødvendigt. <br/><br/>**Bemærk!** Hvis du aktiverer arkivering, kan du kopiere forbindelsesstrengen til denne Blob-lager.<br/><br/>[Om Azure lagerplads konti](../storage/storage-create-storage-account.md)
Tjenesten Bus Namespace og dens nøgle-værdier | Gemmer X12 og EDIFACT samling data. Hvis du ikke har brug for samling, et Service Bus navneområde ikke er nødvendigt.<br/><br/>**Bemærk!** Hvis du aktiverer samling, kan du kopiere disse værdier.<br/><br/>[Oprette en tjeneste Bus Namespace](http://msdn.microsoft.com/library/azure/hh690931.aspx)
TPM forekomst | En forekomst af BizTalk handel Partner Administration (TPM) er påkrævet til at oprette en forbindelse, som AS2 og X12 eller EDIFACT API App. Når du opretter TPM API-App, opretter du den TPM forekomst. <br/><br/>**Bemærk!** Kende navnet på din TPM-API-App. 


## <a name="create-the-api-apps"></a>Oprette API-Apps
B2B API Apps kan oprettes ved hjælp af portalen Azure eller ved hjælp af REST API'er. 


### <a name="create-the-api-apps-using-rest-apis"></a>Oprette API Apps ved hjælp af REST API'er
[Se dokumentationen til, hvordan du bruger REST API'er.](http://go.microsoft.com/fwlink/p/?LinkId=529766)


### <a name="create-the-b2b-api-apps-in-the-azure-portal"></a>Oprette B2B API Apps i portalen Azure
Du kan oprette B2B API Apps i portalen Azure, når du opretter logik Apps, online eller Mobile-Apps. Eller du kan oprette en ved hjælp af sin egen blade. Begge dele er nemt, så det afhænger af dit behov eller indstillinger. Nogle brugere foretrækker at oprette alle B2B API-Apps med deres specifikke egenskaber først. Derefter kan du oprette de logik Apps/Web Apps/Mobile-Apps og tilføje B2B API Apps du har oprettet.  

Følgende trin Opret B2B API Apps ved hjælp af bladet API Apps.


#### <a name="create-the-biztalk-trading-partner-management-tpm-api-apps"></a>Oprette BizTalk handel Partner Administration (TPM) API Apps

> [AZURE.NOTE] En forekomst af BizTalk handel Partner Administration (TPM) er påkrævet til at oprette en forbindelse, som AS2 og X12 eller EDIFACT API App. Når du opretter TPM API-App, opretter du den TPM forekomst.

Følgende trin opretter en TPM forekomst:

1. Vælg **Marketplace**Azure portalen Startboard (startside). **API Apps** viser alle de eksisterende API-Apps og forbindelser. Du kan også **søge** efter bestemte B2B API Apps.
2. Vælg **BizTalk handel Partner administration**. Vælg **Opret**i bladet nye. 
3. Angive egenskaberne: 

    Egenskaben | Beskrivelse
--- | ---
Navn | Angiv et navn til TPM forekomsten. For eksempel kan du navngive den *AccountsPayableTPM*.
Pakkeindstillinger | Du kan angive ADO.NET **Forbindelsesstrengen databasen** Azure SQL database, du har oprettet. <br/><br/>Når du kopierer forbindelsesstrengen, føjes ikke adgangskoden til forbindelsesstrengen. Sørg for at angive adgangskoden i forbindelsesstrengen.
App-serviceaftale | Oversigt over tidsplanen betaling. Du kan ændre det, hvis du har brug for mere eller mindre ressourcer.
Priser niveau | Skrivebeskyttet egenskab, der viser kategorien priser i abonnementet Azure. 
Ressourcegruppe | Oprette en ny eller bruge en eksisterende gruppe. Alle API Apps og forbindelser til logik Apps, Web Apps og Mobile-Apps skal være i samme ressourcegruppe. <br/><br/>[Brug af ressourcegrupper](../azure-resource-manager/resource-group-overview.md) forklares denne egenskab. 
Abonnement | Skrivebeskyttet egenskab, der viser en liste over dit aktuelle abonnement.
Placering | Geografiske placering, der er vært din Azure service. 
Føje til Startboard | Vælg denne indstilling for at føje Appen B2B API til din Starboard (startside).

4. Vælg **Opret**. 

Når APPEN TPM API (TPM forekomst) er oprettet, kan du oprette AS2 forbindelsen og/eller X12 eller EDIFACT API Apps. 


#### <a name="create-the-as2-connector"></a>Oprette AS2 forbindelsen

1. Vælg **Marketplace**Azure portalen Startboard (startside). **API Apps** viser alle de eksisterende API-Apps og forbindelser. Du kan også **søge** efter bestemte B2B API Apps.
2. Markér **AS2 forbindelse**. Vælg **Opret**i bladet nye. 
3. Angive egenskaberne: 

    Egenskaben | Beskrivelse
--- | ---
Navn | Angiv et navn til AS2 forbindelsen. For eksempel kan du navngive den *AS2Connector*.
Pakkeindstillinger | Angiv de bestemte indstillinger for pågældende API-Apps, som navnet på forekomsten af TPM. <br/><br/>Se [Tilføje AS2 Pakkeindstillinger](#AddAS2Conn) i dette emne for de specifikke egenskaber. 
App-serviceaftale | Oversigt over tidsplanen betaling. Du kan ændre det, hvis du har brug for mere eller mindre ressourcer.
Priser niveau | Skrivebeskyttet egenskab, der viser kategorien priser i abonnementet Azure. 
Ressourcegruppe | Oprette en ny eller bruge en eksisterende gruppe. [Brug af ressourcegrupper](../azure-resource-manager/resource-group-overview.md) forklares denne egenskab. 
Abonnement | Skrivebeskyttet egenskab, der viser en liste over dit aktuelle abonnement.
Placering | Geografiske placering, der er vært din Azure service. 
Føje til Startboard | Vælg denne indstilling for at føje Appen B2B API til din Starboard (startside).

    **<a name="AddAS2Conn"></a>AS2 forbindelse Pakkeindstillinger**

    Egenskaben | Beskrivelse
--- | --- 
Forbindelsesstrengen databasen | Angiv forbindelsesstreng ADO.NET Azure SQL database, du har oprettet. Når du kopierer forbindelsesstrengen, føjes ikke adgangskoden til forbindelsesstrengen. Sørg for at angive adgangskoden i forbindelsesstrengen, før du kopierer.
Aktivere arkivering for indgående meddelelser | Valgfrit. Aktivér denne egenskab til at gemme meddelelsen egenskaberne for en indgående AS2 meddelelse, der er modtaget fra en partner. 
Azure Blob Storage forbindelsesstreng  | Skriv forbindelsesstrengen til objektbeholderen Azure Blob-lager, du har oprettet. Når arkivering er aktiveret, gemmes de kodede og afkodede meddelelser i denne objektbeholder til lagring.
Navnet på forekomsten TPM | Skriv navnet på den **BizTalk handel Partner administration** API App du tidligere har oprettet. Når du opretter AS2 forbindelsen, udfører denne forbindelse kun AS2 aftalerne i denne bestemte forekomst af TPM.

4. Vælg **Opret**. 


#### <a name="create-the-x12-or-edifact-api-apps"></a>Oprette X12 eller EDIFACT API Apps

1. Vælg **Marketplace**Azure portalen Startboard (startside). **API Apps** viser alle de eksisterende API-Apps og forbindelser. Du kan også **søge** efter bestemte B2B API Apps.
2. Vælg **BizTalk X 12** eller **BizTalk EDIFACT**. Vælg **Opret**i bladet nye. 
3. Angive egenskaberne: 

    Egenskaben | Beskrivelse
--- | ---
Navn | Angiv et navn til Appen B2B API. For eksempel kan du navngive den *EDI850APIApp*.
Pakkeindstillinger | Angiv de bestemte indstillinger for pågældende API-Apps, som navnet på forekomsten af TPM. <br/><br/>Du kan se [X12 eller EDIFACT Pakkeindstillinger](#AddX12) i dette emne for de specifikke egenskaber. 
App-serviceaftale | Oversigt over tidsplanen betaling. Du kan ændre det, hvis du har brug for mere eller mindre ressourcer.
Priser niveau | Skrivebeskyttet egenskab, der viser kategorien priser i abonnementet Azure. 
Ressourcegruppe | Oprette en ny eller bruge en eksisterende gruppe. [Brug af ressourcegrupper](../azure-resource-manager/resource-group-overview.md) forklares denne egenskab. 
Abonnement | Skrivebeskyttet egenskab, der viser en liste over dit aktuelle abonnement.
Placering | Geografiske placering, der er vært din Azure service. 
Føje til Startboard | Vælg denne indstilling for at føje Appen B2B API til din Starboard (startside).

    **<a name="AddX12"></a>X12 og EDIFACT API Apps Pakkeindstillinger**  

    Egenskaben | Beskrivelse
--- | --- 
Forbindelsesstrengen databasen | Angiv forbindelsesstreng ADO.NET Azure SQL database, du har oprettet. Når du kopierer forbindelsesstrengen, føjes ikke adgangskoden til forbindelsesstrengen. Sørg for at angive adgangskoden i forbindelsesstrengen, før du kopierer.
Tjenesten Bus Namespace | Angiv det Service Bus navneområde, du har oprettet. Kræves kun, når "samling" er aktiveret. 
Tjenesten Bus Namespace delt hurtigtast navn | Angiv navneområdet Service Bus hurtigtast, du har oprettet. Kræves kun, når "samling" er aktiveret. 
Tjenesten Bus Namespace delt hurtigtast værdi | Angiv navneområdet Service Bus hurtigtast værdi, du har oprettet. Kræves kun, når "samling" er aktiveret. 
Navnet på forekomsten TPM | Skriv navnet på den **BizTalk handel Partner administration** API App du tidligere har oprettet. Når du opretter X12 eller EDIFACT API Apps, udfører denne API App kun X12/EDFIACT aftaler i denne bestemte forekomst af TPM.

4. Vælg **Opret**. 


## <a name="add-your-partners-agreements-certificates-and-schemas"></a>Tilføje din partnere, aftaler, certifikater og skemaer 
Åbn din TPM-API-App i Azure-portalen. Tilføj din partnere, aftaler, certifikater og skemaer i sektionen **komponenter** . 

Du kan også tilføje aftaler på dine AS2 forbindelser, X12 API Apps og EDIFACT API Apps. 


## <a name="monitor-your-api-apps"></a>Overvåge dine API-Apps
Åbn din TPM-API-App i Azure-portalen. Du kan få vist forskellige management handlinger i sektionen **Handlinger** . Det kan du f.eks.:

- Vis information og fejloplysninger begivenheder
- Få vist hukommelse brugen og tråd antallet af arbejdsprocessen (w3wp)
- Få vist loggene program og server

Mere på [skærm dine logik Apps](app-service-logic-monitor-your-logic-apps.md).


## <a name="add-the-api-apps-to-your-application"></a>Føje API-Apps til dit program 
Microsoft Azure App Service Fremviser andet programtyper, der kan bruge disse B2B API Apps. Du kan oprette en ny eller føje din eksisterende B2B API Apps til logik Apps, Mobile-Apps eller en Web Apps. 

I din App føjer skal du blot vælge B2B API Apps fra galleriet automatisk den til din App.  

> [AZURE.IMPORTANT] For at tilføje forbindelser og API Apps, du tidligere har oprettet, skal du oprette logik Apps, Mobile-Apps eller Web Apps i samme ressourcegruppe. 

Følgende trin tilføje B2B API Apps på logik Apps, Mobile-Apps eller Web Apps: 

1. Gå til **Marketplace**, og Søg efter din logik, Mobile eller Web Apps i Azure portalen Startboard (startside). 

    Hvis du opretter en ny App, søge efter logik Apps, Mobile-Apps eller Web Apps. Vælg App, og vælg **Opret**i bladet nye. [Opret en logik App](app-service-logic-create-a-logic-app.md) indeholder trinnene. 

2. Åbn din App, og vælg **udløsere og handlinger**. 

3. Vælg B2B API App, som automatisk føjer den til din App i **galleriet**. Du kan også oprette en ny B2B API App.

    > [AZURE.IMPORTANT] AS2 forbindelsen og X12, kræver en forekomst af TPM EDIFACT API Apps. Så hvis du opretter nye B2B API Apps, oprette TPM API App først og derefter oprette AS2 forbindelsen, X12 API App eller EDIFACT API App. 

4. Vælg **OK** for at gemme ændringerne. 

>[AZURE.NOTE] Introduktion til Azure logik Apps før tilmelding til en Azure-konto, [Kan du prøve logik Apps](https://tryappservice.azure.com/?appservice=logic). Du kan straks oprette en forbigående starter logik app. Ingen kreditkort, der kræves. ingen forpligtelser.

## <a name="more-b2b-resources"></a>Flere B2B ressourcer

[Oprette en B2B-proces](app-service-logic-create-a-b2b-process.md)<br/>
[Oprette en handel partneraftale](app-service-logic-create-a-trading-partner-agreement.md)<br/>
[Hvad er forbindelser og BizTalk-API Apps](app-service-logic-what-are-biztalk-api-apps.md)


## <a name="read-about-logic-apps-and-web-apps"></a>Læs mere om logik Apps og Webapps
[Hvad er logik Apps?](app-service-logic-what-are-logic-apps.md)<br/>
[Websteder og Webapps i Azure App-tjeneste](../app-service-web/app-service-web-overview.md)


## <a name="more-connectors"></a>Flere forbindelser

[Forbindelser og listen over Apps API](app-service-logic-connectors-list.md)<br/><br/>
[Hvad er forbindelser og BizTalk-API Apps](app-service-logic-what-are-biztalk-api-apps.md) 
