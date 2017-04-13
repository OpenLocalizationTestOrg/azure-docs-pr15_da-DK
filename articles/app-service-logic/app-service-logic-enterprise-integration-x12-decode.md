<properties 
    pageTitle="Få mere at vide om Enterprise Integration Pack afkode X12 meddelelse Connctor | Microsoft Azure App Service | Microsoft Azure" 
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

# <a name="get-started-with-decode-x12-message"></a>Komme i gang med Afkod X12 meddelelse

Validerer eller-Brugergrupper og partner-specifikke egenskaber, genererer XML-dokument for hver transaktion sæt og genererer bekræftelse for behandlede transaktion.

## <a name="create-the-connection"></a>Oprette forbindelse

### <a name="prerequisites"></a>Forudsætninger

* En Azure konto Du kan oprette en [gratis konto](https://azure.microsoft.com/free)

* En Integration konto er påkrævet for at bruge Afkod X12 meddelelse forbindelse. Se oplysninger om, hvordan du opretter en [Integration konto](./app-service-logic-enterprise-integration-create-integration-account.md), [partnere](./app-service-logic-enterprise-integration-partners.md) og [X12 aftale](./app-service-logic-enterprise-integration-x12.md)

### <a name="connect-to-decode-x12-message-using-the-following-steps"></a>Oprette forbindelse til Afkod X12 meddelelse at benytte følgende fremgangsmåde:

1. [Opret en logik App](./app-service-logic-create-a-logic-app.md) viser et eksempel

2. Denne forbindelse har ikke en hvilken som helst udløsere. Bruge andre udløsere til at starte Appen logik, som en anmodning om udløser.  Tilføje en udløser i designvisningen logik App og føje en handling.  Vælg Vis Microsoft administrerede API'er i rullelisten og derefter skrive "x12" i søgefeltet.  Vælg X12 – afkode X12 meddelelse

    ![søge x12](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage1.png)  

3. Hvis du ikke tidligere har oprettet alle forbindelser til Integration konto, bliver du bedt om forbindelsesoplysningerne

    ![integration af kontoforbindelse](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage4.png)    

4. Angiv Integration Account details.  Egenskaber med en stjerne er påkrævet

  	| Egenskaben | Detaljer |
  	| -------- | ------- |
  	| Forbindelsesnavn * | Angiv et navn til din forbindelse |
  	| Integration konto * | Indtast Integration kontonavn. Sørg for, at dine Integration konto og logik app er i den samme Azure placering |

    Når dette er færdigt, ligner din forbindelsesoplysningerne følgende
    
    ![Integration kontoforbindelse oprettet](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage5.png) 

5. Vælg **Opret**
    
6. Bemærk, at forbindelsen er oprettet.

    ![oplysninger om integration konto-forbindelse](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage6.png) 

7. Vælg X12 fladt meddelelse om fil til at afkode

    ![angive obligatoriske felter](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage7.png) 

## <a name="x12-decode-does-following"></a>X12 afkode bruges til følge

* Valideret konvolutten mod handel partneraftale
* Genererer en XML-dokument for hver transaktion sæt.
* Valideret eller-Brugergrupper og partner-specifikke egenskaber
    * Eller-Brugergrupper strukturel validering og validering af udvidede skema
    * Validering af strukturen i udveksling konvolutten.
    * Skemavalidering af konvolutten mod kontrolelement skemaet.
    * Skemavalidering af transaktions-sæt dataelementer mod meddelelse skemaet.
    * Eller-Brugergrupper validering udføres transaktions-sæt dataelementer 
* Kontrollerer, at udveksling, gruppen og transaktion sæt kontrolelement tal ikke er dubletter
    * Kontrollerer tallets udveksling kontrolelement mod tidligere har modtaget udfletninger.
    * Kontrollerer tallets gruppe kontrolelement mod andre gruppering kontrol af tal i udveksling.
    * Kontrollerer posteringen angive kontrolelementtal forhold til andre transaktion sæt kontrolelement tallene i den pågældende gruppe.
* Konverterer hele udveksling til XML 
    * Opdel udveksling som posteringen sæt - suspendere transaktion sæt om fejl: fortolker hver transaktion, der er angivet i en udveksling i et separat XML-dokument. Hvis en eller flere transaktion angiver i udveksling ikke valideres, X12 Afkod afbryder kun disse transaktion sæt.
    * Opdel udveksling som posteringen sæt - suspendere udveksling om fejl: fortolker hver transaktion, der er angivet i en udveksling i et separat XML-dokument.  Hvis en eller flere transaktion angiver i udveksling ikke valideres, X12 Afkod afbryder hele udveksling.
    * Bevare Interchange - suspendere transaktion sæt om fejl: opretter en XML-dokument til hele batchopdelte udveksling. X12 Afkod afbryder kun disse transaktion sæt, der ikke valideres, mens du fortsætter med at behandle alle andre transaktion angiver
    * Bevare Interchange - suspendere udveksling om fejl: opretter en XML-dokument til hele batchopdelte udveksling. Hvis en eller flere transaktion angiver i udveksling ikke valideres, X12 Afkod afbryder hele udveksling 
* Genererer en tekniske og/eller funktionelle bekræftelse (hvis konfigureret).
    * En teknisk bekræftelse genererer som et resultat af sidehoved validering. Tekniske bekræftelse rapporterer status for behandling af en udveksling sidehoved og påhængsvogn af adresse modtageren.
    * En funktionel bekræftelse genererer som et resultat af brødteksten validering. Funktionelle bekræftelse rapporter hver der opstod fejl under behandlingen af den modtagne dokument

## <a name="next-steps"></a>Næste trin

[Lær mere om Enterprise Integration Pack] (./app-service-logic-enterprise-integration-overview.md "Få mere at vide om Enterprise Integration Pack") 


