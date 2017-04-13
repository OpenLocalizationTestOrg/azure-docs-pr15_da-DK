<properties
    pageTitle="Lær at kode eller afkode flade filer ved hjælp af Enterprise Integration Pack og logik apps | Microsoft Azure App Service | Microsoft Azure"
    description="Bruge funktionerne i Enterprise-Integration Pack og logik apps til kode eller afkode flade filer"
    services="app-service\logic"
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

# <a name="enterprise-integration-with-flat-files"></a>Virksomhedsintegration med flad filer

## <a name="overview"></a>Oversigt

Du muligvis vil kode XML-indhold, før du sender den til en business partner i et scenarie med business to business (B2B). I en logik app, der er foretaget af funktionen logik Apps til Azure App-tjenesten, kan du bruge den flade fil kodning connector til at gøre dette. Appen logik, som du opretter kan få dens XML indhold fra en lang række kilder, herunder fra en HTTP-anmodning udløser, fra et andet program eller endda fra et af de mange [forbindelser](../connectors/apis-list.md). Du kan finde flere oplysninger om logik apps, [logik apps dokumentation](./app-service-logic-what-are-logic-apps.md "Få mere at vide om logik apps").  

## <a name="how-to-create-the-flat-file-encoding-connector"></a>Sådan oprettes den flade fil kodning forbindelse

Følg disse trin for at tilføje en flad fil kodning forbindelse til din logik app.

1. Oprette en logik app og [knytte dem til kontoen integration](./app-service-logic-enterprise-integration-accounts.md "lære, hvordan du sammenkæde et integration firma med en logik app"). Denne konto indeholder det skema, du vil bruge til at kode XML-data.  
2. Føje en **anmodning om - når en HTTP-anmodning modtages** udløser til din logik app.  
![Skærmbillede af ved at markere](./media/app-service-logic-enterprise-integration-flatfile/flatfile-1.png)    
3. Tilføje den flade fil kodning handling, som følger:

    en. Vælg **plus** tegnet.

    b. Vælg linket **Tilføj en handling** (vises, når du har valgt plustegnet).

    c. Angiv *flad* for at filtrere alle handlinger med den, du vil bruge, i søgefeltet.

    d. Vælg indstillingen **Flad filkodning** på listen.   
![Skærmbillede af flad filkodning indstilling](./media/app-service-logic-enterprise-integration-flatfile/flatfile-2.png)   
4. Dialogboksen **Flad filkodning** markere tekstboksen **indhold** .  
![Skærmbillede af indhold tekstfelt](./media/app-service-logic-enterprise-integration-flatfile/flatfile-3.png)  
5. Vælg mærket brødtekst som det indhold, du vil kode. Mærket brødtekst udfylder feltet indhold.     
![Skærmbillede af mærket brødtekst](./media/app-service-logic-enterprise-integration-flatfile/flatfile-4.png)  
6. Vælg feltet **Skemanavn** på listen, og vælg det skema, du vil bruge til at kode input indholdet.    
![Skærmbillede af skemanavn liste](./media/app-service-logic-enterprise-integration-flatfile/flatfile-5.png)  
7. Gemme dit arbejde.   
![Skærmbillede af Gem-ikon](./media/app-service-logic-enterprise-integration-flatfile/flatfile-6.png)  

På dette tidspunkt er du færdig med at oprette den flade fil kodning forbindelse. I et virkelige til computeren, kan du vil gemme de kodede data i et line of business-program, som Salesforce. Eller du kan sende, kodede data til en handel partner. Du kan nemt tilføje en handling for at sende output fra handlingen kodning Salesforce eller partneren handel ved hjælp af en af de andre forbindelser, der er angivet.

Nu kan du teste din forbindelse ved at oprette en anmodning til HTTP slutpunktet og herunder XML-indholdet i meddelelsens brødtekst for din anmodning.  

## <a name="how-to-create-the-flat-file-decoding-connector"></a>Sådan oprettes den flade fil kodning forbindelse

>[AZURE.NOTE] For at fuldføre disse trin, skal du have en skemafil allerede har overført til du integration-konto.

1. Føje en **anmodning om - når en HTTP-anmodning modtages** udløser til din logik app.  
![Skærmbillede af ved at markere](./media/app-service-logic-enterprise-integration-flatfile/flatfile-1.png)    
2. Tilføje den flade fil kodning handling, som følger:

    en. Vælg **plus** tegnet.

    b. Vælg linket **Tilføj en handling** (vises, når du har valgt plustegnet).

    c. Angiv *flad* for at filtrere alle handlinger med den, du vil bruge, i søgefeltet.

    d. Vælg indstillingen **Flad fil kodning** på listen.   
![Skærmbillede af flad fil kodning indstilling](./media/app-service-logic-enterprise-integration-flatfile/flatfile-2.png)   
- Vælg **indholdskontrolelementet** . Dette giver en liste over indhold fra de tidligere trin, du kan bruge som indholdet til at afkode. Bemærk, at *teksten* fra den indgående HTTP-anmodning kan bruges som indholdet til at afkode. Du kan også angive indholdet, afkode direkte i kontrolelementet **indhold** .     
- Vælg mærket *brødtekst* . Meddelelse om mærket brødtekst er nu i **indholdskontrolelementet** .
- Vælg navnet på det skema, der skal bruges til at fortolke indholdet. Følgende skærmbillede viser, at *OrderFile* er det valgte skemanavn. Dette skemanavn havde tidligere er blevet overført til integration-konto.

 ![Skærmbillede af flad fil kodning dialogboksen](./media/app-service-logic-enterprise-integration-flatfile/flatfile-decode-1.png)    
- Gemme dit arbejde.  
![Skærmbillede af Gem-ikon](./media/app-service-logic-enterprise-integration-flatfile/flatfile-6.png)    

På dette tidspunkt, er du færdig med at konfigurere din flad fil kodning forbindelse. I et virkelige til computeren, kan du vil gemme de afkodede data i en line of business programmet på computeren som Salesforce. Du kan nemt tilføje en handling for at sende output fra handlingen afkodningsværktøjet til Salesforce.

Nu kan du teste din forbindelse ved at oprette en anmodning til HTTP slutpunktet og medtage det XML-indhold, du vil afkode i brødteksten i anmodningen.  

## <a name="next-steps"></a>Næste trin
- [Lær mere om Enterprise Integration Pack] (./app-service-logic-enterprise-integration-overview.md "Få mere at vide om Enterprise Integration Pack").  
