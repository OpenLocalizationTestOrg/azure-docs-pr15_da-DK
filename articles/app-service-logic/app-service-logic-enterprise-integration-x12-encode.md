<properties 
    pageTitle="Få mere at vide om Enterprise Integration Pack kode X12 meddelelse Connctor | Microsoft Azure App Service | Microsoft Azure" 
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

# <a name="get-started-with-encode-x12-message"></a>Komme i gang med kodes X12 meddelelse

Valideret eller-Brugergrupper og partner-specifikke egenskaber, konverterer XML-kodet meddelelser til eller-Brugergrupper transaktion sæt i udveksling og anmoder om en tekniske og/eller funktionelle bekræftelse

## <a name="create-the-connection"></a>Oprette forbindelse

### <a name="prerequisites"></a>Forudsætninger

* En Azure konto Du kan oprette en [gratis konto](https://azure.microsoft.com/free)

* En Integration konto er påkrævet for at bruge kodes x12 meddelelse forbindelse. Se oplysninger om, hvordan du opretter en [Integration konto](./app-service-logic-enterprise-integration-create-integration-account.md), [partnere](./app-service-logic-enterprise-integration-partners.md) og [X12 aftale](./app-service-logic-enterprise-integration-x12.md)

### <a name="connect-to-encode-x12-message-using-the-following-steps"></a>Oprette forbindelse til kodes X12 meddelelse at benytte følgende fremgangsmåde:

1. [Opret en logik App](./app-service-logic-create-a-logic-app.md) viser et eksempel

2. Denne forbindelse har ikke en hvilken som helst udløsere. Bruge andre udløsere til at starte Appen logik, som en anmodning om udløser.  Tilføje en udløser i designvisningen logik App og føje en handling.  Vælg Vis Microsoft administrerede API'er i rullelisten og derefter skrive "x12" i søgefeltet.  Vælg enten X12-kode X12 meddelelse ved navn på aftale eller X12-kode til X 12 meddelelse ved identiteter.  

    ![søge x12](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage1.png) 

3. Hvis du ikke tidligere har oprettet alle forbindelser til Integration konto, bliver du bedt om forbindelsesoplysningerne

    ![integration af kontoforbindelse](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage1.png) 


4. Angiv Integration Account details.  Egenskaber med en stjerne er påkrævet

  	| Egenskaben | Detaljer |
  	| -------- | ------- |
  	| Forbindelsesnavn * | Angiv et navn til din forbindelse |
  	| Integration konto * | Indtast Integration kontonavn. Sørg for, at dine Integration konto og logik app er i den samme Azure placering |

    Når dette er færdigt, ligner din forbindelsesoplysningerne følgende

    ![Integration kontoforbindelse oprettet](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage2.png) 


5. Vælg **Opret**

6. Bemærk, at forbindelsen er oprettet.

    ![oplysninger om integration konto-forbindelse](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage3.png) 

#### <a name="x12---encode-x12-message-by-agreement-name"></a>X12-kode X12 meddelelse ved navn på aftale

7. Vælg X12 aftale fra rullelisten og XML-meddelelsen til at kode.

    ![angive obligatoriske felter](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage4.png) 

#### <a name="x12---encode-x12-message-by-identities"></a>X12-kode X12 meddelelse via identiteter

7.  Giver afsender-id, afsenderen operator, modtager-id og modtager operator, som det er konfigureret i X12 aftale.  Vælg XML-meddelelse til at kode

    ![angive obligatoriske felter](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage5.png) 

## <a name="x12-encode-does-following"></a>X12 kode bruges til følgende:

* Aftale opløsning ved at sammenligne afsenderen og modtageren kontekst egenskaber.
* Serializes eller-Brugergrupper udveksling, konvertering af XML-kodet meddelelser til eller-Brugergrupper transaktion sæt i udveksling.
* Gælder transaktion sæt sidehoved- og trailersider målgrupper
* Genererer en udveksling kontrolelementtal, en gruppe kontrolelementtal og et transaktion sæt kontrolelement tal for hver udgående udveksling
* Erstatter separatorer i selve dataene
* Valideret eller-Brugergrupper og partner-specifikke egenskaber
    * Validering af skema af transaktions-sæt dataelementer mod meddelelsen skema
    * Eller-Brugergrupper validering udført på transaktions-sæt dataelementer.
    * Udvidet validering udføres transaktions-sæt dataelementer
* Anmoder om en tekniske og/eller funktionelle bekræftelse (hvis konfigureret).
    * En teknisk bekræftelse genererer som et resultat af sidehoved validering. Tekniske bekræftelse rapporterer status for behandling af en udveksling sidehoved og påhængsvogn ved modtagerens adresse
    * En funktionel bekræftelse genererer som et resultat af brødteksten validering. Funktionelle bekræftelse rapporter hver der opstod fejl under behandlingen af den modtagne dokument

## <a name="next-steps"></a>Næste trin

[Lær mere om Enterprise Integration Pack] (./app-service-logic-enterprise-integration-overview.md "Få mere at vide om Enterprise Integration Pack") 

