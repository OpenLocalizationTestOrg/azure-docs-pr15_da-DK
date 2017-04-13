<properties
    pageTitle="Liste over tilgængelige forbindelser og API Apps | Microsoft Azure App Service"
    description="Læse om forbindelser og API Apps i Azure App Service"
    services="logic-apps"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="mandia"/>


# <a name="list-of-connectors-and-api-apps-to-use-in-your-logic-apps"></a>Liste over forbindelser og API Apps til brug i dine logik Apps
>[AZURE.NOTE] Denne version i denne artikel gælder for logik apps 2014-12-01-skema prøveversionen. Du kan finde den logik Apps generelle tilgængelighed (GA) version [Ny forbindelser liste](../connectors/apis-list.md).

Få mere at vide om alle de tilgængelige forbindelser og API-Apps, der er oprettet af Microsoft til brug i dine logik Apps.

Priser oplysninger og en liste over hvad der følger med hver webtjenesten, se [Azure App servicepriser](https://azure.microsoft.com/pricing/details/app-service/).

> [AZURE.NOTE] For at komme i gang med logik Apps før tilmelding til en Azure-konto skal du gå til [Prøve logik App](https://tryappservice.azure.com/?appservice=logic). Du kan straks oprette en forbigående starter logik app i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.

## <a name="core-connectors"></a>Core forbindelser
I følgende tabel vises alle de tilgængelige forbindelser og API-Apps, der er oprettet af Microsoft, der er tilgængelige som Core forbindelser:

Navn | Beskrivelse
--- | ---
[Bing oversætter](https://azure.microsoft.com/marketplace/partners/bing/microsofttranslator/) | Bruge Bing til at oversætte tekst til et andet sprog.
[HTTP](app-service-logic-connector-http.md) | HTTP-lytteren åbnes et slutpunkt, der fungerer som en HTTP-server og lytter på indgående HTTP eller HTTPS-anmodninger. Handlingen HTTP kræver ikke en API App og understøttes indbygget logik Apps.
[Slæk](app-service-logic-connector-slack.md) | Oprette forbindelse til slæk og sende meddelelser til slæk kanaler.


## <a name="enterprise-integration-connectors"></a>Virksomhed-Integration forbindelser
I følgende tabel vises alle de tilgængelige forbindelser og API-Apps, der er oprettet af Microsoft, der er tilgængelige som Enterprise Integration forbindelser:

Navn  | Beskrivelse
------------- | -------------
[BizTalk-regler](app-service-logic-use-biztalk-rules.md) | Brug BizTalk-regler til at definere og styre forretningslogik i en organisation. Virksomhedens politikker kan blive opdateret, uden at rekompilere eller uden geninstallation tilknyttede programmer.
[BizTalk XPath udtrækning](app-service-logic-xpath-extract.md) | Søger efter og henter data fra XML-indhold, der er baseret på en XPath, du vælger.
[DB2 forbindelse](app-service-logic-connector-db2.md) | Opretter forbindelse til en IBM DB2-database lokalt og på en Azure virtuelt kører et Windows-operativsystemet. Kan knytte handlingerne Web API og OData-API til Informix Structured Query Language kommandoer. <br/><br/>Ingen udløsere. Handlinger omfatter tabel Vælg, Indsæt, Opdater, Slet og brugerdefineret-sætning<br/><br/>Denne forbindelse indeholder også Microsoft Client for DRDA til at oprette forbindelse til en Informix-server på tværs af et TCP/IP-netværk.
[Fil](app-service-logic-connector-file.md) | Brug af denne forbindelse, du kan oprette forbindelse til lokale filsystem eller netværket og opgaver i fuldført anden fil, herunder overførsel, slette, listen over filer og mere.
[Informix](app-service-logic-connector-informix.md) | Opretter forbindelse til en IBM Informix-database, i det lokale miljø og på en Azure virtuel maskine, der kører et Windows-operativsystemet. Kan knytte handlingerne Web API og OData-API til Informix Structured Query Language kommandoer.<br/><br/>Ingen udløsere. Handlinger omfatter tabel Vælg, Indsæt, Opdater, Slet og brugerdefineret-sætning.<br/><br/>Når du bruger i det lokale miljø, kan VPN- eller Azure ExpressRoute bruges. Denne forbindelse indeholder også Microsoft Client for DRDA til at oprette forbindelse til en Informix-server på tværs af et TCP/IP-netværk.
[Microsoft SQL Server](app-service-logic-connector-sql.md) | Opretter forbindelse til en lokal installation, SQL Server eller en Azure SQL-Database. Du kan oprette, opdatere, få og slette elementer i en SQL-databasetabeller.
MQ | Opretter forbindelse til IBM WebSphere MQ Server version 8, i det lokale miljø og på en Azure virtuel maskine, der kører et Windows-operativsystemet. Når du bruger i det lokale miljø, kan VPN- eller Azure ExpressRoute bruges. Forbindelsen indeholder også Microsoft Client til MQ.<br/><br/>Ingen udløsere. Ingen handlinger.<br/><br/>**Bemærk!** I øjeblikket kan ikke bruges med logik Apps.

## <a name="connectors-as-triggers"></a>Forbindelser som udløsere
Flere forbindelser giver udløsere til logik Apps. Disse udløsere er to typer:

1. Afstemning udløsere: Disse udløsere afstemning din tjeneste med en bestemt hyppighed, der skal kontrolleres for nye data. Når du er kommet nye data, kører en ny forekomst af din logik App med dataene, som input. For at forhindre, at de samme data bliver brugt flere gange, den udløser kan oprydning af data, der er blevet læst og overføres til Appen logik. Eksempler på sådanne forbindelser er filer, SQL og Azure-lager.
2. Push udløsere: Disse udløsere lytte til data i et slutpunkt eller en begivenhed, der skal udføres. Derefter udløse en ny forekomst af en logik App. Eksempler på sådanne forbindelser er HTTP lytteren og Twitter.

## <a name="connectors-as-actions"></a>Forbindelser som handlinger
Forbindelser kan også bruges som handlinger i din logik App. Handlinger er nyttige til opslag af data i den logik App, kan derefter bruges i forbindelse med udførelsen. For eksempel du muligvis slå data op fra en SQL-database for at få yderligere oplysninger om en kunde, når behandling af en ordre. Eller du skal skrive, opdatere eller slette data i en destination. Du kan gøre dette ved hjælp af de handlinger, der leveres af forbindelserne. Handlinger tilknyttes handlinger i API-Apps (som defineret af deres Swagger metadata).

## <a name="create-your-own-connectors-and-api-apps"></a>Oprette dine egne forbindelser og API Apps
[Forbindelser og API Apps Reference](http://aka.ms/appservicesconnectorreference)  
[Azure App Service API app udløsere](../app-service-api/app-service-api-dotnet-triggers.md)  
[Logik App Reference](https://msdn.microsoft.com/library/azure/dn948510.aspx)

## <a name="more-on-connectors-and-api-apps"></a>Flere oplysninger om forbindelser og API Apps
[Hvad er forbindelser og BizTalk-API Apps](app-service-logic-what-are-biztalk-api-apps.md)  
[Brug af Forbindelsesstyring Hybrid i Azure App Service](app-service-logic-hybrid-connection-manager.md)  
[Administrere og overvåge dine indbyggede API Apps og forbindelser](app-service-logic-monitor-your-connectors.md)
