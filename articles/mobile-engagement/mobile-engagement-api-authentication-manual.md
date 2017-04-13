<properties 
    pageTitle="Godkende med Mobile aftale REST API'er - manuel konfiguration"
    description="Beskriver, hvordan du manuelt konfigurerer godkendelse til Mobile aftale REST API'er" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="08/19/2016"
    ms.author="piyushjo"/>

# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Godkende med Mobile aftale REST API'er - manuel konfiguration

Dette er et tillæg dokumentation, der skal [godkendes på Mobile aftale REST API'er](mobile-engagement-api-authentication.md). Kontrollér, at du læser den først for at få konteksten. Dette beskriver en anden måde at gøre den enkeltstående installation til konfiguration af din godkendelse for de Mobile aftale REST API'er ved hjælp af portalen Azure. 

>[AZURE.NOTE] Vejledningen nedenfor er baseret på denne [vejledning til Active Directory](../resource-group-create-service-principal-portal.md) og tilpasset til hvad der kræves til godkendelse til Mobile aftale API'er. Så referere til det, hvis du ønsker at forstå trinnene nedenfor detaljeret. 

1. Log på kontoen Azure via [Klassisk portal](https://manage.windowsazure.com/).

2. Vælg **Active Directory** i venstre rude.

     ![Vælg Active Directory][1]

3. Vælg den **Standard Active Directory** i din Azure portal. 

     ![Vælg mappe][2]

    >[AZURE.IMPORTANT] Denne metode fungerer kun, når du arbejder i standard Active Directory på din konto og fungerer ikke, hvis du laver dette i en Active Directory, du har oprettet på din konto. 

4. For at få vist programmerne i mappen, klik på **programmer**.

     ![få vist programmer][3]

5. Klik på **Tilføj**. 

     ![tilføje program][4]

6. Klik på **Tilføj et program, udvikling af min organisation**

     ![nyt program][5]

6. Udfyld navnet på programmet, og vælg typen program som **API WEB APPLICATION og/eller WEB** , og klik på knappen Næste.

     ![navn program][6]

7. Du kan angive en hvilken som helst dummy URL-adresser for **SIGN-ON URL-adresse** og **APP ID URI**. De bruges ikke til vores scenarie og URL-adresserne selv valideres ikke.  

     ![Egenskaber for tjenesteprogram][7]

8. I slutningen af dette får du en AAD app med det navn, du tidligere har angivet som følger. Dette er din **AD\_APP\_navnet** og notere den.  

     ![App-navn][8]

9. Klik på appnavnet, og klik på **Konfigurer**.

     ![konfigurere app][9]

10. Skal du notere den klient-ID, der skal bruges som **klient\_ID** for din API-opkald. 

     ![konfigurere app][10]

11. Rul ned til sektionen **taster** , og Føj en nøgle med helst 2 år (udløbet) varighed, og klik på **Gem**. 

     ![konfigurere app][11]


12. Kopiere den værdi, der vises for nøglen, som den vises kun nu og er ikke gemt, så vil ikke blive vist nogensinde igen med det samme. Hvis du mister den derefter nødt du til at oprette en ny nøgle. Dette er **CLIENT_SECRET** for dine API-opkald. 

     ![konfigurere app][12]

    >[AZURE.IMPORTANT] Denne tast udløber i slutningen af den varighed, du har angivet, så sørg for, at forny det, når bliver tid ellers din API godkendelse ikke længere virker. Du kan også slette og gendanne denne tast, hvis du tror, at den er blevet afsløret.
 
13. Klik på knappen **Vis SLUTPUNKTER** nu, åbnes dialogboksen **App slutpunkter** . 

    ![][13]

14. Fra dialogboksen App slutpunkter skal du kopiere **OAUTH 2.0 TOKEN SLUTPUNKT**. 

    ![][14]

15. Dette slutpunkt bliver i den følgende formular, hvor GUID i URL-adressen er din **TENANT_ID** så skal du notere den: 

        https://login.microsoftonline.com/<GUID>/oauth2/token

16. Vi vil nu fortsætte med at konfigurere tilladelserne på denne app. I dette er du nødt til at åbne [Azure-portalen](https://portal.azure.com). 

17. Klik på **Ressourcegrupper** , og find den **Mobile aftale** ressourcegruppe.  

    ![][15]

18. Klik på den **Mobile aftale** ressourcegruppe, og gå til bladet **Indstillinger** her. 

    ![][16]

19. Klik på **brugere** i bladet indstillinger, og klik derefter på **Tilføj** for at tilføje en bruger. 

    ![][17]

20. Klik på **Vælg en rolle**

    ![][18]

21. Klik på **ejer**

    ![][19]

22. Søge efter navnet på dit program **AD\_APP\_navn** i feltet Søg. Du kan ikke se det som standard her. Når du finder den, skal du markere det og klik på **Markér** i bunden af bladet. 

    ![][20]

23. På bladet **Tilføje Access** vises den som **1 bruger, 0 grupper**. Klik på **OK** på denne blade at bekræfte ændringen. 

    ![][21]

Du har nu fuldført den påkrævede AAD konfiguration, og du er alle indstillet til at ringe til API'erne. 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client_secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png



