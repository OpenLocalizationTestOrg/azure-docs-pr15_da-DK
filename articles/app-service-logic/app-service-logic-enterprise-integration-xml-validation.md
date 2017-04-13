<properties 
    pageTitle="Oversigt over XML-validering i Enterprise-Integration Pack | Microsoft Azure App Service | Microsoft Azure" 
    description="Få mere at vide, hvordan validering fungerer i Enterprise-Integration Pack og logik apps" 
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

# <a name="enterprise-integration-with-xml-validation"></a>Virksomhedsintegration med XML-validering

## <a name="overview"></a>Oversigt
Ofte nødt B2B scenarier partnere til en aftale til at validere, meddelelser, de exchange mellem hinanden er gyldige, før kan begynde at behandle data. Du kan bruge validering af XML-forbindelsen til at validere dokumenter mod en foruddefineret skema i Enterprise-Integration Pack.  

## <a name="how-to-validate-a-document-with-the-xml-validation-connector"></a>Sådan validere et dokument med validering af XML-forbindelse
1. Oprette en logik app og [knytte dem til kontoen integration](./app-service-logic-enterprise-integration-accounts.md "lære, hvordan du sammenkæde et integration firma med en logik app") , der indeholder det skema, du vil bruge til at validere XML-data.
2. Føje en **anmodning om - når en HTTP-anmodning modtages** udløser til din logik app  
![](./media/app-service-logic-enterprise-integration-xml/xml-1.png)    
3. Føje handlingen **XML-validering** ved først at vælge **Tilføj en handling**  
4. Angiv *xml* i søgefeltet for at filtrere alle handlinger med den, du vil bruge 
5. Vælg **XML-validering**     
![](./media/app-service-logic-enterprise-integration-xml/xml-2.png)   
6. Vælg feltet **CONTENT** tekst  
![](./media/app-service-logic-enterprise-integration-xml/xml-1-5.png)
7. Vælg mærket brødtekst som det indhold, der kan valideres.   
![](./media/app-service-logic-enterprise-integration-xml/xml-3.png)  
8. Vælg listen **SKEMANAVN** og vælger det skema, du vil bruge til at validere input *indhold* ovenfor     
![](./media/app-service-logic-enterprise-integration-xml/xml-4.png) 
9. Gem dit arbejde  
![](./media/app-service-logic-enterprise-integration-xml/xml-5.png) 

På dette tidspunkt er du færdig med at oprette validering forbindelsen. I et virkelige til computeren, kan du vil gemme valideret dataene i et LOB program som SalesForce. Du kan nemt tilføje en handling for at sende output fra validering til Salesforce. 

Nu kan du teste din validering handling ved at oprette en anmodning til HTTP-slutpunkt.  

## <a name="next-steps"></a>Næste trin

[Lær mere om Enterprise Integration Pack] (./app-service-logic-enterprise-integration-overview.md "Få mere at vide om Enterprise Integration Pack")   