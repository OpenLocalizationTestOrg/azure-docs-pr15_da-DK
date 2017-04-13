<properties 
    pageTitle="API Apps Introduktion | Microsoft Azure" 
    description="Få mere at vide, hvordan Azure App Service hjælper dig med at udvikle host, og forbruge RESTful API'er." 
    services="app-service\api" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-api" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/23/2016" 
    ms.author="rachelap"/>

# <a name="api-apps-overview"></a>Oversigt over API Apps

API apps i Azure App Service indeholder funktioner, der gør det nemmere at udvikle host, og forbruge API'er i skyen og i det lokale miljø. Med API apps får du enterprise grade sikkerhed, enkle adgangskontrol, hybrid connectivity, automatisk generering af SDK og nem integration med [Logik Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md).

[Azure App Service](../app-service/app-service-value-prop-what-is.md) er en fuldt administreret platform til internettet, mobile, og integrationsmuligheder. API Apps er en af fire app typer, som [Azure App Service](../app-service/app-service-value-prop-what-is.md).

![App typer i Azure App Service](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

## <a name="why-use-api-apps"></a>Hvorfor bruge API Apps?

Her er nogle vigtige funktioner i API Apps:

- **Tage dine eksisterende API som-er** – du behøver ikke at ændre nogen af koden i din eksisterende API'er at drage fordel af API Apps--blot implementere din kode til en API-app. Din API kan bruge en hvilken som helst sprog eller framework understøttes af App Service, herunder ASP.NET og C#, Java, PHP, Node.js og Python.

- **Nemt forbrug** - integreret understøttelse af [Swagger API metadata](http://swagger.io/) gør din API'er nemt forbrugsvarer af en række klienter.  Opret automatisk klienten kode til din API'er i en række forskellige sprog, herunder C#, Java og Javascript. Let konfigurere [CORS](app-service-api-cors-consume-javascript.md) uden at ændre din kode. Se [App Service API Apps metadataene for generering af API registrering og kode](app-service-api-metadata.md) og [forbrug en API-app fra JavaScript ved hjælp af CORS](app-service-api-cors-consume-javascript.md)kan finde flere oplysninger. 

- **Enkel adgangskontrol** – beskytte en API-app fra ikke-godkendte adgang med nogen ændringer i din kode. Indbyggede godkendelsestjenester sikre API'er for adgang til, ved at andre tjenester eller af klienter, der repræsenterer brugere. Understøttede identitetsudbydere omfatter Azure Active Directory, Facebook, Twitter, Google og Microsoft-Account. Mobilklienter kan bruge Active Directory Authentication Library (ADAL) eller Mobile Apps SDK. Se [godkendelse og API Apps i Azure App Service](app-service-api-authentication.md)kan finde flere oplysninger.

- **Integration af visual Studio** - dedikeret værktøjer i Visual Studio strømline arbejdet for oprettelse, implementere, forbrug, fejlfinding og administration af API apps. Se [præsentation af Azure SDK 2.8.1 til .NET](/blog/announcing-azure-sdk-2-8-1-for-net/)kan finde flere oplysninger.

- **Integration med logik Apps** - API-apps, som du opretter kan benyttes fra [App Service logik Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md).  Se [Brug af din brugerdefinerede API hostes på App Service med logik apps](../app-service-logic/app-service-logic-custom-hosted-api.md) og [nye skema version 2015-08-01-preview](../app-service-logic/app-service-logic-schema-2015-08-01.md)kan finde flere oplysninger.

Desuden kan en API app drage fordel af funktioner, som [Web Apps](../app-service-web/app-service-web-overview.md) og [Mobile-Apps](../app-service-mobile/app-service-mobile-value-prop.md). Omvendt gør sig også gældende: Hvis du bruger en WebApp eller en mobilapp som vært for en API, det kan drage fordel af API Apps funktioner som Swagger metadata for klienten oprettelse af kode og CORS for adgang fra en webbrowser på tværs af domæner. Den eneste forskel mellem de tre app typer (API, internettet, mobile) er navn og ikon, der bruges til dem på Azure-portalen.

## <a name="whats-the-difference-between-api-apps-and-azure-api-management"></a>Hvad er forskellen mellem API-Apps og Azure API Management?

API Apps og [Azure API Management](../api-management/api-management-key-concepts.md) er komplementære tjenester:

* Administration af API er om administration af API'er. Du sætte en API Management front-end i en API til at overvåge og gas brugen, manipulere input og output, samle flere API'er i et slutpunkt og osv.. API'er administreres kan være placeret et sted.
* API Apps handler om vært API'er. Tjenesten indeholder funktioner, som det er nemmere at udvikle og arket API'er, men det gøre ikke typer af overvågning, throttling, manipulere, eller konsolidering pågældende API administration vises. Hvis du ikke behøver API administrationsfunktioner, kan du hoste API'er i API-apps uden brug af API administration.

Her er et diagram, der illustrerer API Management bruges til API'er hostet i API-apps og et andet sted.

![Azure API administration og API Apps](./media/app-service-api-apps-why-best-platform/apia-apim.png)

Nogle funktioner i administration af API og API Apps har lignende funktioner.  Begge kan for eksempel automatisere CORS support. Når du bruger de to tjenester sammen, kan du vil bruge API Management for CORS, da den fungerer som front-end til dine API-apps. 

## <a name="getting-started"></a>Kom godt i gang

For at komme i gang med API Apps ved at installere eksempelkode til en skal du se selvstudium til uanset hvilken framework, du foretrækker:

* [ASP.NET](app-service-api-dotnet-get-started.md) 
* [Node.js](app-service-api-nodejs-api-app.md) 
* [Java](app-service-api-java-api-app.md) 

Starte en tråd i- [API Apps-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureAPIApps)for at stille spørgsmål om API apps. 
