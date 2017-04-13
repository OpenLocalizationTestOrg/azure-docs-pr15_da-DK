<properties
    pageTitle="Brugergodkendelse til API-Apps i Azure App Service | Microsoft Azure"
    description="Lær, hvordan du beskytter en API-app i Azure App Service ved at tillade adgang kun til godkendte brugere."
    services="app-service\api"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="dotnet"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/30/2016"
    ms.author="rachelap"/>

# <a name="user-authentication-for-api-apps-in-azure-app-service"></a>Brugergodkendelse til API-Apps i Azure App Service

## <a name="overview"></a>Oversigt

Denne artikel beskrives, hvordan du beskytter en Azure API-app, så kun godkendte brugere kan ringe til den. I artiklen antages det, at du har læst [Azure App Service godkendelse oversigt](../app-service/app-service-authentication-overview.md).

Du kan lære følgende:

* Sådan konfigureres en udbyder af godkendelse, med oplysninger om Azure Active Directory (Azure AD).
* Hvordan du kan bruge en beskyttet API-app ved hjælp af [Active Directory Authentication Library (ADAL) JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js).

I artiklen indeholder to sektioner:

* Afsnittet [Sådan konfigureres brugergodkendelse i Azure App Service](#authconfig) generelt forklarer, hvordan du konfigurerer brugergodkendelse for alle API-Apps og gælder også for alle strukturer, der understøttes af App Service, herunder .NET, Node.js og Java.

* I sektionen [fortsætter .NET API Apps selvstudier](#tutorialstart) , artikel hjælpelinjer du gennem konfigurationen af eksempelprogrammet et med en .NET tilbage slutningen og en AngularJS foran afslutte, så det bruger Azure Active Directory til brugergodkendelse. 

## <a id="authconfig"></a>Sådan konfigureres brugergodkendelse i Azure App Service

Dette afsnit indeholder generelle oplysninger, der gælder for alle API-Apps. Vejledning bestemt til at gøre listen .NET eksempelprogrammet, skal du gå til [fortsætter .NET Introduktion selvstudier](#tutorialstart).

1. Gå til bladet **Indstillinger** af API-app, du vil beskytte, finde sektionen **Funktioner** , og klik derefter på [Azure portal](https://portal.azure.com/) **godkendelse / godkendelse**.

    ![Azure portal godkendelse/godkendelse](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. I den **godkendelse / godkendelse** blade, klik **på**.

4. Vælg en af indstillingerne for rullelisten **handling skal udføre, når anmodning ikke er godkendt** .

    * Hvis du vil kun godkendte opkald til at nå din API-app, skal du vælge en af indstillingerne for **logge på med...** . Denne indstilling giver dig mulighed at beskytte appen API uden at skrive kode, der kører i den.

    * Hvis du vil have alle opkald til at nå din API-app skal du vælge **Tillad anmodning (ingen handling)**. Du kan bruge denne indstilling til at dirigere ikke-godkendte ringer til et udvalg af godkendelsesprovidere. Med denne indstilling er det nødvendigt at skrive programkode for at håndtere godkendelse.

    Se [godkendelse og API Apps i Azure App Service](app-service-api-authentication.md#multiple-protection-options)kan finde flere oplysninger.

5. Vælg en eller flere af **Godkendelsesprovidere**.

    Billedet viser valgmuligheder, der kræver alle ringer skal godkendes af Azure AD.
 
    ![Azure portalen godkendelse/godkendelse blade](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

    Når du vælger en udbyder af godkendelse, viser portalen en konfiguration blade for den pågældende provider. 

    Finde detaljerede oplysninger, der beskriver, hvordan du konfigurerer godkendelse udbyder konfiguration blade, se, [hvordan du konfigurerer din App-tjenesteprogrammet bruge logon til Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). (Linket fører til en artikel om Azure AD, men selve artiklen indeholder faner, der linker til artikler til de andre godkendelsesprovidere).

7. Klik på **OK**, når du er færdig med bladet godkendelse udbyder konfiguration.

7. I den **godkendelse / godkendelse** blade, klik på **Gem**.

Når dette er færdig, skal godkendes App Service alle API-kald, før de når API-app. Godkendelsestjenester fungerer på samme måde for alle sprog, der understøtter App service, herunder .NET, Node.js og Java. 

For at gøre godkendte API-kald, omfatter kalderen udbyder af godkendelse 2.0 OAuth-bærertoken i overskriften godkendelse på HTTP-anmodninger. Tokenet kan hentes ved hjælp af udbyder af godkendelse SDK.

## <a id="tutorialstart"></a>Fortsætter .NET API Apps selvstudier

Hvis du følger Node.js eller Java selvstudier til API-apps, gå til den næste artikel [service vigtigste godkendelse til API-apps](app-service-api-dotnet-service-principal-auth.md). 

Hvis du følger .NET selvstudium serie til API-apps og allerede har installeret programmet, som vejledningen i de [første](app-service-api-dotnet-get-started.md) og [anden](app-service-api-cors-consume-javascript.md) selvstudier, gå til afsnittet [konfigurere godkendelse i App Service og Azure AD](#azureauth) .

Hvis du vil følge dette selvstudium uden at gå gennem de første og anden selvstudier, kan du gøre følgende fremgangsmåde som beskriver, hvordan du kommer i gang ved hjælp af en automatiseret proces til at installere eksempelprogrammet.

>[AZURE.NOTE] Følgende trin får du til samme udgangspunktet, som hvis du har de første to selvstudier med én undtagelse: Visual Studio ikke allerede ved, hvilken WebApp eller API-app, der bliver brug af hvert projekt. Det betyder, at du ikke har nøjagtige instruktionerne i dette selvstudium, der beskriver, hvordan du installerer på de rigtige mål. Hvis du ikke er tryg ved at finde ud af, hvordan du gør på din egen installation trinnene, er det bedre at følge selvstudium serie i det [første selvstudium](app-service-api-dotnet-get-started.md) end at starte med denne proces med automatiseret installation.

1. Sørg for, at du har alle de forudsætninger, der er angivet i det [første selvstudium](app-service-api-dotnet-get-started.md). Ud over de forudsætninger, der er angivet, forudsætter selvstudierne godkendelse, at du har arbejdet med App Service webapps og API apps i Visual Studio og Azure portalen.

2. Klik på knappen **Installer til Azure** i [opgavelisten eksempel lager Vigtigt-filen](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/readme.md) til at udrulle API-apps og WebApp. Skal du notere af ressourcegruppen Azure, der oprettes, som du kan bruge denne nyere for at slå op WebApp og API app navne.
 
3. Hent eller klone [opgavelisten eksempel lager](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list) for at få den kode, du vil arbejde med lokalt i Visual Studio.

## <a id="azureauth"></a>Konfigurere godkendelse i App-tjenesten og Azure AD

Nu har du det program, der kører i Azure App Service uden at kræve, at brugere godkendes. I dette afsnit kan du tilføje godkendelse ved at gøre følgende opgaver:

* Konfigurere App Service for at kræve godkendelse af Azure Active Directory (Azure AD) for opkald til midterste niveau API-app.
* Oprette et Azure AD-program.
* Konfigurere Azure AD-programmet til at sende bærertokenet efter logon til AngularJS front end. 

Hvis du støder på problemer under følge vejledningen i selvstudium skal du se afsnittet [fejlfinding](#troubleshooting) i slutningen af selvstudiet. 
 
### <a name="configure-authentication-for-the-middle-tier-api-app"></a>Konfigurere godkendelse til midterste niveau API-app

1. [Azure-portalen](https://portal.azure.com/), gå til bladet **Indstillinger** af API-app, du har oprettet for ToDoListAPI projektet, finde sektionen **Funktioner** , og klik derefter på **godkendelse / godkendelse**.

    ![Azure portal godkendelse/godkendelse](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. I den **godkendelse / godkendelse** blade, klik **på**.

4. Vælg **Log på med Azure Active Directory**i rullelisten **handling skal udføre, når anmodning ikke er godkendt** .

    Denne indstilling sikrer, at ingen unauathenticated anmodninger om vil oprette forbindelse til API-app. 

5. Klik på **Azure Active Directory**under **Godkendelsesprovidere**.

    ![Azure portalen godkendelse/godkendelse blade](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. Klik på **Express** i bladet **Azure Active Directory-indstillinger**

    ![Azure portalen godkendelse/godkendelse Express indstilling](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

    App Service kan automatisk oprette et Azure AD-program i din Azure AD- [lejer](https://msdn.microsoft.com/en-us/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)med indstillingen **Express** . 

    Du behøver ikke at oprette en lejer, fordi alle Azure-konto er en automatisk.

7. Klik på **Opret ny AD App** , hvis den ikke allerede er valgt under **administrationstilstand**, og Bemærk den værdi, der er i boksen **Opret program** . Du kan slå AAD programmet i portalen Azure klassisk senere.

    ![Azure portal Azure AD-indstillinger](./media/app-service-api-dotnet-user-principal-auth/aadsettings2.png)

    Azure opretter automatisk et Azure AD-program med det angivne navn i din Azure AD-lejer. Som standard hedder programmet Azure AD den samme som API-app. Hvis du foretrækker, kan du angive et andet navn.
 
7. Klik på **OK**.

7. I den **godkendelse / godkendelse** blade, klik på **Gem**.

    ![Klik på Gem](./media/app-service-api-dotnet-user-principal-auth/authsave.png)

Nu kan kun brugere i din Azure AD-lejer kalde API-app.

### <a name="optional-test-the-api-app"></a>Valgfrit: Test API-app

1. Gå til URL-adressen til API-app i en browser: bladet **API app** i Azure-portalen, klik på linket under **URL-adresse**.  

    Du har åbnet med en logonskærmen, fordi ikke-godkendte anmodninger ikke er tilladt at nå API-app.

    Hvis din browser går til siden "blev oprettet", browseren allerede kan være logget på – det er tilfældet, åbne en InPrivate eller Inkognito-vindue, og gå til appen API URL-adresse.

2. Log på med legitimationsoplysningerne for en bruger i din Azure AD-lejer.

    Når du er logget på, vises siden "blev oprettet" i browseren.

9. Luk browseren.

### <a name="configure-the-azure-ad-application"></a>Konfigurere Azure AD-programmet

Når du har konfigureret Azure AD-godkendelse, oprettes App Service et Azure AD-program for dig. Som standard den nye Azure AD blev programmet konfigureret for at give bærertokenet til appen API URL-adresse. I dette afsnit kan du konfigurere Azure AD-program til at levere bærertoken til AngularJS front end i stedet for direkte til midterste niveau API-app. AngularJS front end sender tokenet til API-app, når det kalder API-app.

>[AZURE.NOTE] Sådan bevares processen som enkle som muligt, dette selvstudium bruger en enkelt Azure AD klasse-programmet til både front end og midt API app. En anden mulighed er at bruge to Azure AD-programmer. I dette tilfælde vil du skulle give front end Azure AD-program tilladelse til at ringe til det midterste modul Azure AD-program. Denne metode, der er flere programmer, får du mere detaljeret kontrol over tilladelser til hvert niveau.

11. Gå til **Azure Active Directory**i [Azure klassisk portal](https://manage.windowsazure.com/).

    Du skal bruge klassisk-portalen, fordi visse Azure Active Directory-indstillinger, du skal have adgang til ikke endnu er tilgængelige i den aktuelle Azure portal.

12. Klik på din AAD lejer på fanen **mappe** .

    ![Azure AD klassisk-portalen](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. Klik på **programmer > programmer firmaet ejer**, og klik derefter på markeringen.

    Du muligvis også at opdatere siden for at se det nye program.

15. Klik på navnet på den miniature, Azure oprettes til dig, når du har aktiveret godkendelse for din API-app på listen over programmer.

    ![Under fanen Azure AD-programmer](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. Klik på **Konfigurer**.

    ![Azure AD Konfigurer under fanen](./media/app-service-api-dotnet-user-principal-auth/configure.png)

17. Angiv **Sign-on URL-adressen** til URL-adressen for din AngularJS WebApp, ingen efterstillet skråstreg.

    For eksempel: https://todolistangular.azurewebsites.net

    ![Sign-on URL-adresse](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

17. Angive **Svar URL-adressen** til URL-adressen for din online, ingen efterstillet skråstreg.

    For eksempel: https://todolistsangular.azurewebsites.net

16. Klik på **Gem**.

    ![Svar URL-adresse](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

15. Nederst på siden skal du klikke på **Administrer manifest > Hent manifest**.

    ![Hente manifest](./media/app-service-api-dotnet-user-principal-auth/downloadmanifest.png)

17. Hent filen til en placering, hvor du kan redigere den.

16. I hentede manifestfilen, søge efter den `oauth2AllowImplicitFlow` egenskab. Skift værdien for denne egenskab fra `false` til `true`, og Gem derefter filen.

    Denne indstilling er påkrævet for access fra et JavaScript enkeltsidet program. Det gør det muligt for 2.0 Oauth-bærertoken skal returneres i URL-adresse-fragment.

16. Klik på **Administrer manifest > Overfør manifest**, og overfør filen, som du har opdateret i det foregående trin.

    ![Overføre manifest](./media/app-service-api-dotnet-user-principal-auth/uploadmanifest.png)

17. Kopiér værdien **Klient-ID** , og Gem det et sted, hvor du kan hente den senere.

## <a name="configure-the-todolistangular-project-to-use-authentication"></a>Konfigurere ToDoListAngular projektet for at bruge godkendelse

I dette afsnit kan du ændre AngularJS front end så Active Directory Authentication Library (ADAL) til JS bruges til at få fat på en bærertoken for den bruger, der er logget på fra Azure AD. Koden omfatter tokenet i HTTP-anmodninger sendes til det midterste modul, som vist i følgende diagram. 

![Bruger godkendelse diagram](./media/app-service-api-dotnet-user-principal-auth/appdiagram.png)

Foretage følgende ændringer til filer i ToDoListAngular projektet.

1. Åbn filen *index.html* .

2. Fjern kommentar fra de linjer, som henviser til Active Directory Authentication bibliotek (ADAL) til JS scripts.

        <script src="app/scripts/adal.js"></script>
        <script src="app/scripts/adal-angular.js"></script>

1. Åbn filen *app/scripts/app.js* .

2. Skrive en kommentar ud blokering af kode, der er markeret med henblik på "uden godkendelse", og fjern blokering af kode, der er markeret med henblik på "godkendelse med".

    Denne ændring refererer provideren ADAL JS godkendelse og giver konfigurationsværdier til den. I følgende trin kan du angive konfiguration værdierne for din API app og Azure AD-programmet.

8. I den kode, der angiver den `endpoints` variabel, angive API URL-adressen til URL-adressen til API-app, du har oprettet for ToDoListAPI projektet, og angive Azure AD-program-ID til brugerens klient-ID, som du kopierede fra Azure klassisk portal.

    Koden er nu ligner det følgende eksempel.

        var endpoints = {
            "https://todolistapi0121.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
        };

9. I opkaldet til `adalProvider.init`, skal du angive `tenant` til din lejer navn og `clientId` til samme værdi, du har brugt i det forrige trin.

    Koden er nu ligner det følgende eksempel.

        adalProvider.init(
            {
                instance: 'https://login.microsoftonline.com/', 
                tenant: 'contoso.onmicrosoft.com',
                clientId: '1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3',
                extraQueryParameter: 'nux=1',
                endpoints: endpoints
            },
            $httpProvider
            );

    Ændringerne på `app.js` angiver, at den opkald kode og kaldet API er i det samme Azure AD-program.

1. Åbn filen *app/scripts/homeCtrl.js* .

2. Skrive en kommentar ud blokering af kode, der er markeret med henblik på "uden godkendelse", og fjern blokering af kode, der er markeret med henblik på "godkendelse med".

1. Åbn filen *app/scripts/indexCtrl.js* .

2. Skrive en kommentar ud blokering af kode, der er markeret med henblik på "uden godkendelse", og fjern blokering af kode, der er markeret med henblik på "godkendelse med".

### <a name="deploy-the-todolistangular-project-to-azure"></a>Anvende ToDoListAngular-projektet til Azure

8. I **Solution Explorer**, højreklik på ToDoListAngular projektet, og klik derefter på **Udgiv**.

9. Klik på **Udgiv**.

    Visual Studio installerer projektet og åbner en browser til den online base URL-adresse. Dette viser en 403 fejlside, som er normal for et forsøg på at gå til en grundlæggende Web API-URL fra en browser.

    Du har stadig til at foretage en ændring i midterste niveau API-app, før du kan teste programmet.

10. Luk browseren.

## <a name="configure-the-todolistapi-project-to-use-authentication"></a>Konfigurere ToDoListAPI projektet for at bruge godkendelse

Aktuelt ToDoListAPI projektet sender "*" som det `owner` værdi, der skal ToDoListDataAPI. I dette afsnit kan du ændre koden for at sende ID'ET for den bruger, der er logget på.

Foretage følgende ændringer i ToDoListAPI projektet.

1. Åbn filen *Controllers/ToDoListController.cs* , og fjern kommentar fra linjen i hver enkelt metode til handling, der angiver `owner` til Azure AD `NameIdentifier` gøre krav på værdi. Eksempel:

        owner = ((ClaimsIdentity)User.Identity).FindFirst(ClaimTypes.NameIdentifier).Value;

    **Vigtigt**: ikke Fjern kommentar fra kode i den `ToDoListDataAPI` metode; Du kan gøre det senere til tjenesten vigtigste godkendelse selvstudium.

### <a name="deploy-the-todolistapi-project-to-azure"></a>Anvende ToDoListAPI-projektet til Azure

8. I **Solution Explorer**, højreklik på ToDoListAPI projektet, og klik derefter på **Udgiv**.

9. Klik på **Udgiv**.

    Visual Studio installerer projektet og åbner en browser til appen API base URL-adresse.

10. Luk browseren.

### <a name="test-the-application"></a>Teste programmet

9. Gå til URL-adressen til web-app, **ved hjælp af HTTPS, ikke HTTP**.

8. Klik på fanen **Opgavelisten** .

    Du bliver bedt om at logge på.

9. Log på med legitimationsoplysningerne for en bruger i din AAD lejer.

10. Siden **Opgavelisten** vises.

    ![Opgaveliste for siden](./media/app-service-api-dotnet-user-principal-auth/webappindex.png)

    Ingen opgaveelementer vises, fordi indtil nu de alle er ejer "*". Nu det midterste modul anmoder om elementer for den bruger, der er logget på, og ingen endnu har oprettet.

11. Tilføje nye opgaveelementer for at bekræfte, at programmet fungerer.

12. Gå til Swagger brugergrænseflade URL-adressen til ToDoListDataAPI API-app i en anden browser-vindue, og klik **ToDoList > få**. Skriv en stjerne for den `owner` parameter, og klik derefter på **Prøv det ud**.

    Svaret viser, at de nye opgaveelementer har den faktiske Azure AD-bruger-ID i egenskaben ejer.

    ![Ejer-ID i JSON svar](./media/app-service-api-dotnet-user-principal-auth/todolistapiauth.png)


## <a name="building-the-projects-from-scratch"></a>Opbygning af projekter fra bunden

De to Web API-projekter blev oprettet ved hjælp af skabelonen **Azure API App** project og erstatte standard værdier controlleren med en ToDoList controller. 

Finde oplysninger om, hvordan du opretter et AngularJS enkeltsidet program med Web API 2 back-end [hænder på øvelse: oprette en enkelt side program (SPA) med ASP.NET Web API og Angular.js](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs). Du kan finde oplysninger om, hvordan du tilføjer Azure AD-godkendelse kode, i følgende ressourcer:

* [Sikring af AngularJS enkelt side-Apps med Azure AD](../active-directory/active-directory-devquickstarts-angular.md).
* [Introduktion til ADAL JS v1](http://www.cloudidentity.com/blog/2015/02/19/introducing-adal-js-v1/)

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

[AZURE.INCLUDE [troubleshooting](../../includes/app-service-api-auth-troubleshooting.md)]

* Sørg for, at du ikke forveksle ToDoListAPI (midterste niveau) og ToDoListDataAPI (data lag). Kontrollér f.eks, at du har tilføjet godkendelse for den midterste niveau API-Apps, ikke data niveau. 
* Sørg for, at kildekode AngularJS henviser til midterste niveau API app URL-adressen (ToDoListAPI, ikke ToDoListDataAPI) og den korrekte Azure AD klient-ID. 

## <a name="next-steps"></a>Næste trin

Du har lært Sådan bruger du App Service godkendelse for en API-app, og hvordan til at ringe til API-app ved hjælp af biblioteket ADAL JS i dette selvstudium. I det næste selvstudium lærer du, hvordan du [sikker adgang til din API app for scenarier med-tjenester](app-service-api-dotnet-service-principal-auth.md).

