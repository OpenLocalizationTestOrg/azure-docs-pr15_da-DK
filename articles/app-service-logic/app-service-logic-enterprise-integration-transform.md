<properties 
    pageTitle="Oversigt over Enterprise Integration Pack | Microsoft Azure App Service | Microsoft Azure" 
    description="Bruge funktionerne i Enterprise-Integration Pack til at aktivere business-proces og integration scenarier ved hjælp af Microsoft Azure App service" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="msftman" 
    manager="erikre" 
    editor="cgronlun"/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/08/2016" 
    ms.author="deonhe"/>

# <a name="enterprise-integration-with-xml-transforms"></a>Virksomhedsintegration med XML-transformationer

## <a name="overview"></a>Oversigt
Enterprise integration transformation forbindelsen konverterer data fra ét format til et andet format. For eksempel kan du har en indgående meddelelser, der indeholder dags dato i formatet YearMonthDay. Du kan bruge en transformering omformatere datoen skal være i formatet MonthDayYear.

## <a name="what-does-a-transform-do"></a>Hvad gør en transformering?
En transformation, som er også kendt som et kort, består af et XML-kilde skema (input) og et mål XML-skema (output). Du kan bruge forskellige indbyggede funktioner til at manipulere eller bestemmer, hvilke data, herunder udgør en streng vejledende betinget tildelinger matematiske udtryk, og dato klokkeslæt formatters og endda løkker konstruktioner.

## <a name="how-to-create-a-transform"></a>Sådan oprettes en transformering?
Du kan oprette en transformering/Tilknyt ved hjælp af Visual Studio [Enterprise Integration SDK](https://aka.ms/vsmapsandschemas). Når du er færdig med at oprette og test af transformeringen, kan du overføre transformeringen til kontoen integration. 

## <a name="how-to-use-a-transform"></a>Sådan bruges en transformering
Når du overfører transformeringen til integration-konto, kan du bruge det til at oprette en logik app. Appen logik Kør derefter dine transformationer, hver gang appen logik udløses (og der er input-indhold, der skal være transformeret).

**Her er trinnene for at bruge en transformering**:

### <a name="prerequisites"></a>Forudsætninger 
I eksemplet skal du:  

-  [Opret en Azure funktioner objektbeholder] (https://ms.portal.azure.com/#create/Microsoft.FunctionApp "Opret en Azure funktioner objektbeholder")  
-  [Tilføj en funktion til objektbeholderen Azure funktioner] (https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-transform-function%2Fazuredeploy.json "Denne skabelon opretter en webhook baseret C# azure funktion med transformation funktioner til brug i logik apps integrationsmuligheder")    
-  Oprette en integration konto og tilføje et kort på den  

>[AZURE.TIP] Skal du notere den navnet på objektbeholderen Azure funktioner og funktionen Azure, skal du bruge dem i næste trin.  

Nu hvor du har taget sig af forudsætningerne, er det tid at oprette din logik app:  

1. Oprette en logik app og [knytte dem til kontoen integration](./app-service-logic-enterprise-integration-accounts.md "lære, hvordan du sammenkæde et integration firma med en logik app") , der indeholder kortet.
2. Føje en **anmodning om - når en HTTP-anmodning modtages** udløser til din logik app  
![](./media/app-service-logic-enterprise-integration-transforms/transform-1.png)    
3. Føje handlingen **Transformere XML-** ved først at vælge **Tilføj en handling**   
![](./media/app-service-logic-enterprise-integration-transforms/transform-2.png)   
4. Angiv word *transformere* i søgefeltet for at filtrere alle handlinger med den, du vil bruge  
![](./media/app-service-logic-enterprise-integration-transforms/transform-3.png)  
5. Vælg handlingen, **Transformere XML**   
![](./media/app-service-logic-enterprise-integration-transforms/transform-4.png)  
6. Vælg **Funktionen OBJEKTBEHOLDER** , der indeholder den funktion, du vil bruge. Dette er navnet på objektbeholderen Azure funktioner, du oprettede tidligere i disse trin.
7. Vælg den **funktion** , du vil bruge. Dette er navnet på den Azure-funktion, du oprettede tidligere.
8. Tilføje **indhold** , du vil transformere XML. Bemærk, at du kan bruge en hvilken som helst XML-data, du modtager i HTTP-anmodningen som **indholdet**. Markér brødteksten i HTTP-anmodningen, som udløste logik app i dette eksempel.
9. Vælg navnet på **KORTET** , du vil bruge til at udføre transformeringen. Kortet skal allerede være kontoen integration. I et tidligere trin, du allerede har givet din logik app adgang til kontoen integration, der indeholder dit kort.
10. Gem dit arbejde  
![](./media/app-service-logic-enterprise-integration-transforms/transform-5.png) 

På dette tidspunkt, er du færdig med at konfigurere dit kort. I et virkelige til computeren, kan du vil gemme den transformerede data i et LOB program som SalesForce. Du kan nemt som en handling til at sende output fra transformeringen til Salesforce. 

Nu kan du teste din transformation ved at oprette en anmodning til HTTP-slutpunkt.  

## <a name="features-and-use-cases"></a>Funktioner og use cases

- Den transformation, der er oprettet i et kort kan være simple, såsom kopiere et navn og adresse fra et dokument til en anden. Eller du kan oprette mere komplekse transformationer ved hjælp af handlingerne out box kort.  
- Flere kort handlinger eller funktioner er tilgængelige, herunder strenge, dato klokkeslætsfunktioner osv.  
- Du kan gøre en direkte data kopi mellem skemaerne. Dette er den Mapper, der er inkluderet i SDK, så enkelt som at tegne en streg, der forbinder elementerne i kilde-skema med deres versionerne i Destinationsskemaet.  
- Når du opretter et kort, du får vist en grafisk repræsentation af kortet, der viser alle relationer og links, du opretter.
- Bruge funktionen Test kort til at tilføje en XML-meddelelse. Med et enkelt klik, kan du teste det kort, du har oprettet, og se genererede output.  
- Overføre eksisterende kort  
- Indeholder understøttelse af XML-format.


## <a name="learn-more"></a>Lær mere
- [Lær mere om Enterprise Integration Pack] (./app-service-logic-enterprise-integration-overview.md "Få mere at vide om Enterprise Integration Pack")  
- [Få mere at vide om kort] (./app-service-logic-enterprise-integration-maps.md "Få mere at vide om virksomhedsintegration kort")  
 