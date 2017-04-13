<properties 
    pageTitle="Få mere at vide om Enterprise Integration Pack afkode AS2 meddelelse Connctor | Microsoft Azure App Service | Microsoft Azure" 
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

# <a name="get-started-with-decode-as2-message"></a>Komme i gang med afkode AS2 meddelelse

Oprette forbindelse til afkode AS2 meddelelse at fastlægge, sikkerhed og pålidelighed under overførsel af meddelelser. Den indeholder digital signering, dekryptering og godkendelser via meddelelse fordeling meddelelser (MDN).

## <a name="create-the-connection"></a>Oprette forbindelse

### <a name="prerequisites"></a>Forudsætninger

* En Azure konto Du kan oprette en [gratis konto](https://azure.microsoft.com/free)

* En Integration konto er påkrævet for at bruge afkode AS2 meddelelse forbindelse. Se oplysninger om, hvordan du opretter en [Integration konto](./app-service-logic-enterprise-integration-create-integration-account.md), [partnere](./app-service-logic-enterprise-integration-partners.md) og en [AS2 aftale](./app-service-logic-enterprise-integration-as2.md)

### <a name="connect-to-decode-as2-message-using-the-following-steps"></a>Oprette forbindelse til afkode AS2 meddelelse at benytte følgende fremgangsmåde:

1. [Opret en logik App](./app-service-logic-create-a-logic-app.md) viser et eksempel.

2. Denne forbindelse har ikke en hvilken som helst udløsere. Bruge andre udløsere til at starte Appen logik, som en anmodning om udløser.  Tilføje en udløser i designvisningen logik App og føje en handling.  Vælg Vis Microsoft administrerede API'er i rullelisten listen, og angiv derefter "AS2" i søgefeltet.  Vælg AS2 – afkode AS2 meddelelse

    ![Søg AS2](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage1.png)

3. Hvis du ikke tidligere har oprettet alle forbindelser til Integration konto, bliver du bedt om forbindelsesoplysningerne

    ![Oprette forbindelse til integration](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage2.png)

4. Angiv Integration Account details.  Egenskaber med en stjerne er påkrævet

  	| Egenskaben   | Detaljer |
  	| --------   | ------- |
  	| Forbindelsesnavn *    | Angiv et navn til din forbindelse |
  	| Integration konto * | Indtast Integration kontonavn. Sørg for, at dine Integration konto og logik app er i den samme Azure placering |

    Når dette er færdigt, ligner din forbindelsesoplysningerne følgende

    ![integration af forbindelse](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage3.png)

5. Vælg **Opret**
    
6. Bemærk, at forbindelsen er oprettet.  Nu fortsætte med andre trinnene i din logik App

    ![Integration forbindelse oprettet](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage4.png) 

7. Vælg brødtekst og overskrifter anmodning output

    ![angive obligatoriske felter](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage5.png) 

## <a name="the-as2-decode-does-the-following"></a>AS2 afkode gør følgende

* Behandler AS2/HTTP sidehoveder
* Bekræfter signaturen (hvis konfigureret)
* Dekrypterer meddelelser (hvis konfigureret)
* Dekomprimerer meddelelsen (hvis konfigureret)
* Afstemmer en modtaget MDN med den oprindelige udgående meddelelse
* Opdaterer og svarer poster i databasen ikke afvisning
* Skriver poster for AS2 rapportering
* Output data indholdet er base64-kodet
* Bestemmer, om en MDN er påkrævet, og om MDN skal være synkron eller asynkron baseret på konfiguration i AS2 aftale
* Genererer en synkron eller asynkron MDN (baseret på aftale konfigurationer)
* Angiver MDN korrelationstokens og egenskaber

##<a name="try-it-for-yourself"></a>Prøv det for dig selv

Hvorfor ikke Prøv det. Klik på [her](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/) til at installere en app på din egen brug af funktionerne logik Apps AS2 en fuldt funktionsdygtige logik 

## <a name="next-steps"></a>Næste trin

[Lær mere om Enterprise Integration Pack] (./app-service-logic-enterprise-integration-overview.md "Få mere at vide om Enterprise Integration Pack") 