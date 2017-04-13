<properties 
    pageTitle="Få mere at vide om Enterprise Integration Pack kode EDIFACT meddelelse Connctor | Microsoft Azure App Service | Microsoft Azure" 
    description="Lær at bruge partnere til Enterprise-Integration Pack og logik-apps" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="padmavc" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="padmavc"/>

# <a name="get-started-with-encode-edifact-message"></a>Komme i gang med kode EDIFACT meddelelse

Valideret eller-Brugergrupper og partner-specifikke egenskaber 

## <a name="create-the-connection"></a>Oprette forbindelse

### <a name="prerequisites"></a>Forudsætninger

* En Azure konto Du kan oprette en [gratis konto](https://azure.microsoft.com/free)

* En Integration konto er påkrævet for at bruge kode EDIFACT meddelelse forbindelse. Se oplysninger om, hvordan du opretter en [Integration konto](./app-service-logic-enterprise-integration-create-integration-account.md), [partnere](./app-service-logic-enterprise-integration-partners.md) og [EDIFACT aftale](./app-service-logic-enterprise-integration-edifact.md)

### <a name="connect-to-decode-edifact-message-using-the-following-steps"></a>Oprette forbindelse til afkode EDIFACT meddelelse at benytte følgende fremgangsmåde:

1. [Opret en logik App](./app-service-logic-create-a-logic-app.md) viser et eksempel.

2. Denne forbindelse har ikke en hvilken som helst udløsere. Bruge andre udløsere til at starte Appen logik, som en anmodning om udløser.  Tilføje en udløser i designvisningen logik App og føje en handling.  Vælg Vis Microsoft administrerede API'er i rullelisten listen, og angiv derefter "EDIFACT" i søgefeltet.  Vælg enten kode EDIFACT meddelelse ved navn på aftale eller kodes EDIFACT meddelelse ved identiteter.

    ![søge EDIFACT](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage1.png)  

3. Hvis du ikke tidligere har oprettet alle forbindelser til Integration konto, bliver du bedt om forbindelsesoplysningerne

    ![oprette forbindelse til integration af konto](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage1.png)  

4. Angiv kontooplysninger Integration.  Egenskaber med en stjerne er påkrævet

  	| Egenskaben | Detaljer |
  	| -------- | ------- |
  	| Forbindelsesnavn * | Angiv et navn til din forbindelse |
  	| Integration konto * | Indtast Integration kontonavn. Sørg for, at dine Integration konto og logik app er i den samme Azure placering 

    Når dette er færdigt, ligner din forbindelsesoplysningerne følgende

    ![integration af kontoforbindelse](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage2.png)
    
5. Vælg **Opret**
    
6. Bemærk, at forbindelsen er oprettet

    ![oplysninger om integration konto-forbindelse](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage4.png)
    
#### <a name="encode-edifact-message-by-agreement-name"></a>Kode EDIFACT meddelelse ved navn på aftale

7.  Angive EDIFACT aftale navn og en XML-meddelelse til at kode.

    ![angive obligatoriske felter](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage6.png)
    
#### <a name="encode-edifact-message-by-identities"></a>Kode EDIFACT meddelelse ved identiteter

7. Give afsenderen id, afsenderen operator, modtager-id og modtager operator, der er konfigureret i EDIFACT aftalen.  Vælg XML-meddelelse til at kode

    ![angive obligatoriske felter](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage7.png)
    
## <a name="edifact-encode-does-following"></a>EDIFACT kodes betyder følge

* Løse aftalen ved at sammenligne afsender operator & id og modtager operator og id
* Serializes eller-Brugergrupper udveksling, konvertering af XML-kodet meddelelser til eller-Brugergrupper transaktion sæt i udveksling.
* Gælder transaktion sæt sidehoved- og trailersider målgrupper
* Genererer en udveksling kontrolelementtal, en gruppe kontrolelementtal og et transaktion sæt kontrolelement tal for hver udgående udveksling
* Erstatter separatorer i selve dataene
* Valideret eller-Brugergrupper og partner-specifikke egenskaber
    * Skemavalidering af transaktions-sæt dataelementer mod meddelelse skemaet.
    * Eller-Brugergrupper validering udført på transaktions-sæt dataelementer.
    * Udvidet validering udføres transaktions-sæt dataelementer
* Genererer en XML-dokument for hver transaktion sæt.
* Anmoder om en tekniske og/eller funktionelle bekræftelse (hvis konfigureret).
    * Som en teknisk bekræftelse angiver CONTRL meddelelsen modtagelse af en udveksling.
    * Som en funktionel bekræftelse angiver CONTRL meddelelsen godkendelse eller afvisning af den modtagne udveksling, gruppe eller meddelelse med en liste over fejl eller ikke-understøttede funktioner

## <a name="next-steps"></a>Næste trin

[Lær mere om Enterprise Integration Pack] (./app-service-logic-enterprise-integration-overview.md "Få mere at vide om Enterprise Integration Pack") 