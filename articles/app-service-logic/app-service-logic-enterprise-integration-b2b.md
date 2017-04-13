<properties 
    pageTitle="Oprettelse af B2B løsninger med Enterprise Integration Pack | Microsoft Azure App Service | Microsoft Azure" 
    description="Få mere at vide om at modtage data ved hjælp af funktionerne B2B i Enterprise-Integration Pack" 
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

# <a name="learn-about-receiving-data-using-the-b2b-features-of-the-enterprise-integration-pack"></a>Få mere at vide om at modtage data ved hjælp af funktionerne B2B i Enterprise-Integration Pack#

## <a name="overview"></a>Oversigt ##

Dette dokument er en del af den logik Apps Enterprise Integration pakke. Se oversigten mere at vide om [funktionerne i Enterprise Integration pakken](./app-service-logic-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Forudsætninger ##

Bruge AS2 og X12 handlinger, du vil have en Enterprise Integration konto

[Sådan oprettes en Enterprise Integration konto](./app-service-logic-enterprise-integration-accounts.md)

## <a name="how-to-use-the-logic-apps-b2b-connectors"></a>Sådan bruger du logik Apps B2B forbindelser ##

Når du har oprettet en integration konto og tilføjet partnere og aftaler, det du er klar til at oprette en logik App implementerer, der en arbejdsproces for business til business (B2B).

I denne walkthru får du vist, hvordan du bruger AS2 og X12 handlinger til at oprette en business til business logik App, der modtager data fra en handel partner.

1. Oprette en ny logik app og [knytte dem til kontoen integration](./app-service-logic-enterprise-integration-accounts.md).  
2. Føje en **anmodning om - når en HTTP-anmodning modtages** udløser til din logik app  
![](./media/app-service-logic-enterprise-integration-b2b/flatfile-1.png)  
3. Føje handlingen **Afkode AS2** ved først at vælge **Tilføj en handling**  
![](./media/app-service-logic-enterprise-integration-b2b/transform-2.png)  
4. Angiv word **as2** i søgefeltet for at filtrere alle handlinger med den, du vil bruge  
![](./media/app-service-logic-enterprise-integration-b2b/b2b-5.png)  
6. Vælg handlingen, **AS2 - afkode AS2 meddelelse**  
![](./media/app-service-logic-enterprise-integration-b2b/b2b-6.png)  
7. Som vist, tilføje **brødtekst** , du tager som input. Markér brødteksten i HTTP-anmodningen, som udløste logik app i dette eksempel. Du kan også angive et udtryk for at angive sidehoveder i feltet**SIDEHOVEDER** :

    @triggerOutputs()['headers']

8. Tilføje **overskrifter** , der kræves til AS2. Disse bliver i HTTP-anmodning om overskrifterne. Vælg overskrifterne for HTTP-anmodningen, som udløste logik app i dette eksempel.
9. Tilføj nu handlingen Afkod X12 meddelelse ved igen at vælge **Tilføj en handling**  
![](./media/app-service-logic-enterprise-integration-b2b/b2b-9.png)   
10. Angiv word **x12** i søgefeltet for at filtrere alle handlinger med den, du vil bruge  
![](./media/app-service-logic-enterprise-integration-b2b/b2b-10.png)  
11. Vælg den **X12-afkode X12 meddelelse** handling at føje det til appen logik  
![](./media/app-service-logic-enterprise-integration-b2b/b2b-as2message.png)  
12. Nu skal du angive input til denne handling, der skal udskrives i handlingen AS2 ovenfor. Faktisk meddelelsesindhold i et JSON-objekt og kodes base64. Du bliver derfor nødt til at angive et udtryk, som input så Indtast følgende udtryk i feltet til **X12 FLADT fil meddelelse til AFKOD** input  

    @base64ToString(body('Decode_AS2_message')?['AS2Message']?['Content'])  

13. Dette trin afkoder X12 data har modtaget fra handel partner og kan udskrives en antallet af elementer i et JSON-objekt. For at lade partner ved for modtagelse af dataene kan du sende tilbage et svar, der indeholder den AS2 meddelelse fordeling meddelelse (MDN) i en HTTP-svar handling  
14. Føje handlingen **svar** ved at vælge **Tilføj en handling**   
![](./media/app-service-logic-enterprise-integration-b2b/b2b-14.png)  
15. Angiv word **svar** i søgefeltet for at filtrere alle handlinger med den, du vil bruge  
![](./media/app-service-logic-enterprise-integration-b2b/b2b-15.png)  
16. Vælg handlingen, **svar** til at tilføje den  
![](./media/app-service-logic-enterprise-integration-b2b/b2b-16.png)  
17. Angive feltet svar **BRØDTEKST** ved hjælp af følgende udtryk til at få adgang til MDN fra outputtet af handlingen **Afkod X12 meddelelse**  

    @base64ToString(body('Decode_AS2_message')?['OutgoingMdn']?['Content'])  

![](./media/app-service-logic-enterprise-integration-b2b/b2b-17.png)  
18. Gem dit arbejde  
![](./media/app-service-logic-enterprise-integration-b2b/transform-5.png)  

På dette tidspunkt, er du færdig med at konfigurere din B2B logik app. I et virkelige til computeren, kan du gemme den afkodede X12 data i en LOB program eller data store. Du kan nemt tilføje yderligere handlinger for at gøre dette, eller skrive brugerdefinerede API'er for at oprette forbindelse til dine egne LOB-programmer og bruge disse API'er i din app, logik.

## <a name="features-and-use-cases"></a>Funktioner og use cases ##

- AS2 og X12 afkode og kode handlinger gør det muligt at hente data fra og sende data til udveksling partnere ved hjælp af branche standard protokoller ved hjælp af logik apps  
- Du kan bruge AS2 og X12 med eller uden hinanden til at udveksle data med handel partnere efter behov
- Handlingerne B2B gør det nemt at oprette partnere og aftaler i kontoen Integration og bruge dem i en logik app  
- Ved at forlænge din logik app med andre handlinger kan du sende og modtage data til og fra andre programmer og tjenester som SalesForce  

## <a name="learn-more"></a>Lær mere ##

[Få flere oplysninger om Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md)  