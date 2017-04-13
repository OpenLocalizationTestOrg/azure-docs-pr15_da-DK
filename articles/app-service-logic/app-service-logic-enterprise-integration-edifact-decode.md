<properties 
    pageTitle="Få mere at vide om Enterprise Integration Pack afkode EDIFACT meddelelse forbindelse | Microsoft Azure App Service | Microsoft Azure" 
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

# <a name="get-started-with-decode-edifact-message"></a>Komme i gang med afkode EDIFACT meddelelse

Validerer eller-Brugergrupper og partner-specifikke egenskaber, genererer XML-dokument for hver transaktion sæt og genererer bekræftelse for behandlede transaktion.

## <a name="create-the-connection"></a>Oprette forbindelse

### <a name="prerequisites"></a>Forudsætninger

* En Azure konto Du kan oprette en [gratis konto](https://azure.microsoft.com/free)

* En Integration konto er påkrævet for at bruge afkode EDIFACT meddelelse forbindelse. Se oplysninger om, hvordan du opretter en [Integration konto](./app-service-logic-enterprise-integration-create-integration-account.md), [partnere](./app-service-logic-enterprise-integration-partners.md) og [EDIFACT aftale](./app-service-logic-enterprise-integration-edifact.md)

### <a name="connect-to-decode-edifact-message-using-the-following-steps"></a>Oprette forbindelse til afkode EDIFACT meddelelse at benytte følgende fremgangsmåde:

1. [Opret en logik App](./app-service-logic-create-a-logic-app.md) viser et eksempel.

2. Denne forbindelse har ikke en hvilken som helst udløsere. Bruge andre udløsere til at starte Appen logik, som en anmodning om udløser.  Tilføje en udløser i designvisningen logik App og føje en handling.  Vælg Vis Microsoft administrerede API'er i rullelisten listen, og angiv derefter "EDIFACT" i søgefeltet.  Vælg afkode EDIFACT meddelelse

    ![søge EDIFACT](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage1.png)
    
3. Hvis du ikke tidligere har oprettet alle forbindelser til Integration konto, bliver du bedt om forbindelsesoplysningerne

    ![oprette integration konto](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage2.png)  

4. Angiv Integration Account details.  Egenskaber med en stjerne er påkrævet

  	| Egenskaben | Detaljer |
  	| -------- | ------- |
  	| Forbindelsesnavn * | Angiv et navn til din forbindelse |
  	| Integration konto * | Indtast Integration kontonavn. Sørg for, at dine Integration konto og logik app er i den samme Azure placering |

    Når dette er færdigt, ligner din forbindelsesoplysningerne følgende

    ![Integration-konto, der er oprettet](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage3.png)  

5. Vælg **Opret**

6. Bemærk, at forbindelsen er oprettet

    ![oplysninger om integration konto-forbindelse](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage5.png)  

7. Vælg EDIFACT flad fil meddelelse til at afkode

    ![angive obligatoriske felter](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage5.png)  

## <a name="edifact-decode-does-following"></a>EDIFACT afkode betyder følge

* Løse aftalen ved at sammenligne afsender operator & id og modtager operator & id
* Opdeles flere udfletninger i en enkelt meddelelse i separat.
* Valideret konvolutten mod handel partneraftale
* Opdeler udveksling.
* Valideret eller-Brugergrupper og partner-specifikke egenskaber omfatter
    * Validering af strukturen i udveksling konvolutten.
    * Skemavalidering af konvolutten mod kontrolelement skemaet.
    * Skemavalidering af transaktions-sæt dataelementer mod meddelelse skemaet.
    * Eller-Brugergrupper validering udføres transaktions-sæt dataelementer
* Kontrollerer, at udveksling, gruppen og transaktion sæt kontrolelement tal ikke er dubletter (hvis konfigureret) 
    * Kontrollerer tallets udveksling kontrolelement mod tidligere har modtaget udfletninger. 
    * Kontrollerer tallets gruppe kontrolelement mod andre gruppering kontrol af tal i udveksling. 
    * Kontrollerer posteringen angive kontrolelementtal forhold til andre transaktion sæt kontrolelement tallene i den pågældende gruppe.
* Genererer en XML-dokument for hver transaktion sæt.
* Konverterer hele udveksling til XML 
    * Opdel udveksling som posteringen sæt - suspendere transaktion sæt om fejl: fortolker hver transaktion, der er angivet i en udveksling i et separat XML-dokument. Hvis et eller flere transaktion sæt i udveksling ikke valideres, og derefter EDIFACT afkode afbryder kun disse transaktion sæt. 
    * Opdel udveksling som posteringen sæt - suspendere udveksling om fejl: fortolker hver transaktion, der er angivet i en udveksling i et separat XML-dokument.  Hvis et eller flere transaction sæt i udveksling ikke valideres, og derefter EDIFACT afkode afbryder hele udveksling.
    * Bevare Interchange - suspendere transaktion sæt om fejl: opretter en XML-dokument til hele batchopdelte udveksling. EDIFACT afkode afbryder kun disse transaction sæt, der ikke valideres, mens du fortsætter med at behandle alle andre transaction sæt
    * Bevare Interchange - suspendere udveksling om fejl: opretter en XML-dokument til hele batchopdelte udveksling. Hvis en eller flere transaction angiver i udveksling ikke valideres, og derefter EDIFACT afkode afbryder hele udveksling 
* Genererer en tekniske (objekt) og/eller funktionelle bekræftelse (hvis konfigureret).
    * En teknisk bekræftelse eller CONTRL ACK oplyser om resultatet af en syntaksfejl kontrol af fuldført modtaget udveksling.
    * En funktionel bekræftelse accepterer acceptere eller afvise en modtaget udveksling eller en gruppe

## <a name="next-steps"></a>Næste trin

[Lær mere om Enterprise Integration Pack] (./app-service-logic-enterprise-integration-overview.md "Få mere at vide om Enterprise Integration Pack") 