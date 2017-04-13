<properties
   pageTitle="Logik Apps eksempler og scenarier | Microsoft Azure"
   description="Se almindelige logik apps eksempler og Lær at implementere almindelige scenarier"
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
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="logic-apps-examples-and-common-scenarios"></a>Logik Apps eksempler og almindelige scenarier

Dette dokument detaljer almindelige scenarier og eksempler, der kan hjælpe dig med at forstå nogle af måder, hvorpå du kan bruge logik apps til at automatisere forretningsprocesser. 

## <a name="custom-triggers-and-actions"></a>Brugerdefinerede udløsere og handlinger

Der er flere måder, du kan udløse en logik app fra en anden app. Her er et par eksempler:

- [Oprette en brugerdefineret udløser eller en handling](app-service-logic-create-api-app.md)
- [Længerevarende handlinger](app-service-logic-create-api-app.md)
- [HTTP-anmodning udløser (INDLÆG)](app-service-logic-http-endpoint.md)
- [Webhook udløsere og handlinger](app-service-logic-create-api-app.md)
- [Afstemning udløsere](app-service-logic-create-api-app.md)

### <a name="scenarios"></a>Scenarier

- [Anmode om synkron svar](app-service-logic-http-endpoint.md)
- [Anmode om svar med SMS](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="error-handling-and-logging"></a>Håndtering af fejl og logføring

- [Undtagelse og fejlhåndtering](app-service-logic-exception-handling.md)
- [Konfigurere Azure beskeder og diagnosticering](app-service-logic-monitor-your-logic-apps.md)

### <a name="scenarios"></a>Scenarier

- [Use Case: Fejl og håndtering af undtagelse](app-service-logic-scenario-error-and-exception-handling.md)

## <a name="deploying-and-managing"></a>Implementering og administration af

- [Oprette en automatiseret installation](app-service-logic-create-deploy-template.md)
- [Opbygge og anvende logik apps fra Visual Studio](app-service-logic-deploy-from-vs.md)
- [Overvåge logik apps](app-service-logic-monitor-your-logic-apps.md)

## <a name="content-types-conversions-and-transformations"></a>Indholdstyper, konverteringer og transformationer

Logik Apps [arbejdsproces definition language](http://aka.ms/logicappsdocs) indeholder mange funktioner gør det muligt at konvertere og arbejde med forskellige indholdstyper.  Desuden foretager programmet hele det kan for at bevare typer indhold som data flyder gennem arbejdsprocessen.

- [Håndtering af typer indhold](app-service-logic-content-type.md) som programmet/json, programmet/XML- og almindelig/tekst
- [Redigering af arbejdsprocesser](app-service-logic-author-definitions.md)
- [Arbejdsproces definition language reference](http://aka.ms/logicappsdocs)

## <a name="batches-and-looping"></a>Navne og løkker

- [SplitOn](app-service-logic-loops-and-scopes.md)
- [ForEach](app-service-logic-loops-and-scopes.md)
- [Indtil](app-service-logic-loops-and-scopes.md)

## <a name="integrating-with-azure-functions"></a>Integration med Azure funktioner

- [Azure funktioner-integration](app-service-logic-azure-functions.md)

### <a name="scenarios"></a>Scenarier

- [Azure funktionen som en Service Bus udløser](app-service-logic-scenario-function-sb-trigger.md)

## <a name="http-rest-and-soap"></a>HTTP, RESTEN og SOAP

 - [Ringe til SOAP](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)


Vi vil beholde føjer eksempler og scenarier til dette dokument. Brug sektionen kommentarer nedenfor til at fortælle os, hvilke eksempler eller scenarier du gerne vil se her.