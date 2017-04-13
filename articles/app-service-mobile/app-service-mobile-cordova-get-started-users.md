<properties
    pageTitle="Tilføje godkendelse på Apache Cordova med Mobile Apps | Azure App Service"
    description="Lær at bruge Mobile-Apps i Azure App-tjeneste til at godkende brugere af din Apache Cordova app gennem en række forskellige identitetsudbydere, herunder Google, Facebook, Twitter og Microsoft."
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-apache-cordova-app"></a>Føje godkendelse til din Apache Cordova-app

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
    
## <a name="summary"></a>Oversigt

I dette selvstudium føjer du godkendelse til projektets todolist Hurtig start på Apache Cordova ved hjælp af en understøttede identitetsudbyder. Dette selvstudium er baseret på det [komme i gang med Mobile Apps] selvstudium, som du skal udføre først.

##<a name="register"></a>Registrere din app til godkendelse og konfigurere tjenesten App

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Se en video, der viser lignende trin](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

##<a name="permissions"></a>Begrænse tilladelser på godkendte brugere

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Nu kan du kontrollere, at anonym adgang til din backend-version er deaktiveret. Åbn det projekt, du har oprettet, når du har fuldført selvstudiet i [Introduktion til Mobile-Apps], i Visual Studio, og derefter køre programmet i **Google Android Emulator** , og Bekræft, at en uventet fejl i forbindelse vises, når programmet starter.

Derefter skal vil du opdatere appen for at godkende brugere inden du anmoder om ressourcer fra Mobile-App backend-version.

##<a name="add-authentication"></a>Føje godkendelse til appen

1. Åbn dit projekt i **Visual Studio**og derefter åbne den `www/index.html` fil til redigering.

2. Find den `Content-Security-Policy` metakode i sektionen hoved.  Du skal føje OAuth vært til listen over tilladte kilder.

  	| Udbyder               | Navn på SDK udbyder | OAuth Host                  |
  	| :--------------------- | :---------------- | :-------------------------- |
  	| Azure Active Directory | aad               | https://login.Windows.NET   |
  	| Facebook               | Facebook          | https://www.Facebook.com    |
  	| Google                 | Google            | https://Accounts.Google.com |
  	| Microsoft              | microsoftaccount  | https://login.live.com      |
  	| Twitter                | Twitter           | https://API.Twitter.com     |

    Et eksempel indhold--sikkerhedspolitik (implementeret til Azure Active Directory) er som følger:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.windows.net https://yourapp.azurewebsites.net; style-src 'self'">

    Du skal erstatte `https://login.windows.net` med værten OAuth fra tabellen ovenfor.  Se [indhold sikkerhedspolitik dokumentation] til flere oplysninger om denne metakode.

    Bemærk, at nogle godkendelsesprovidere ikke kræver indhold sikkerhedspolitik ændres, når den bruges på relevante mobile enheder.  For eksempel der indhold sikkerhedspolitik kræves ingen ændringer når du bruger Google godkendelse på en Android-enhed.

3. Åbn den `www/js/index.js` fil til redigering, finde den `onDeviceReady()` metode, og under oprettelse af klienten kode skal du tilføje følgende:

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    Bemærk, at denne kode erstatte den eksisterende kode, der opretter tabelreferencen og opdaterer Brugergrænsefladen.

    Metoden login() starter godkendelse hos udbyderen. Metoden login() er en asynkron funktion, der returnerer en JavaScript løftet.  Resten af initialiseringen er placeret i løftet svaret, så den ikke udføres indtil metoden login() er fuldført.

4. Erstat teksten i den kode, du lige har tilføjet, `SDK_Provider_Name` med navnet på dit login-udbyder. For eksempel bruge til Azure Active Directory, `client.login('aad')`.

4. Kør projektet.  Når projektet har afsluttet initialiseringen, vises dit program OAuth-logonside for den valgte godkendelse-udbyder.

##<a name="next-steps"></a>Næste trin

* Få mere at vide mere [Om godkendelse] med Azure App Service.
* Fortsæt selvstudiet ved at tilføje [Push-beskeder] til din Apache Cordova app.

Lær at bruge SDK'er.

* [Apache Cordova SDK]
* [ASP.NET Server SDK]
* [Node.js Server SDK]

<!-- URLs. -->
[Introduktion til Mobile-Apps]: app-service-mobile-cordova-get-started.md
[Indhold sikkerhedspolitik dokumentation]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Push-beskeder]: app-service-mobile-cordova-get-started-push.md
[Om godkendelse]: app-service-mobile-auth.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md 
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
