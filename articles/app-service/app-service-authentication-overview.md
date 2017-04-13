<properties
    pageTitle="Godkendelse og autorisation i Azure App Service | Microsoft Azure"
    description="Grundlæggende reference og oversigt over godkendelsestyper / godkendelse af funktioner til Azure App Service"
    services="app-service"
    documentationCenter=""
    authors="mattchenderson"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="mahender"/>

# <a name="authentication-and-authorization-in-azure-app-service"></a>Godkendelse og autorisation i Azure App Service

## <a name="what-is-app-service-authentication--authorization"></a>Hvad er App Service godkendelse / godkendelse?

App-tjenesten godkendelse / godkendelse er en funktion, der gør muligt for dit program til at logge på brugere, så du ikke behøver at ændre kode på app back-end. Det gør det nemt at beskytte dit program og arbejde med data for hver bruger.

App Service bruges organisationsnetværket identitet, hvori en tredjepart identitetsudbyder gemmer konti og godkender brugere. Programmet er afhængig af udbyderens identitetsoplysninger, så app ikke behøver at gemme disse oplysninger sig selv. App Service understøtter fem id-udbydere af feltet: Azure Active Directory, Facebook, Google, Microsoft-Account og Twitter. Din app kan bruge en hvilken som helst antal disse identitetsudbydere til at give brugerne med indstillinger for hvordan de logge på. For at udvide den indbyggede understøttelse, kan du integrere en anden identitetsudbyder eller [din egen brugerdefinerede identitet løsning][custom-auth].

Hvis du vil komme i gang, skal du se en af følgende selvstudier:

- [Føje godkendelse til din iOS-app] [ iOS] (eller [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms]eller [Cordova])
- [Brugergodkendelse til API-Apps i Azure App Service][apia-user]
- [Introduktion til Azure App Service - del 2][web-getstarted]

## <a name="how-authentication-works-in-app-service"></a>Hvordan fungerer godkendelse i App-tjeneste

For at godkende ved hjælp af en af udbyderne identitet, skal du først konfigurere identitetsudbyder at vide om dit program. Identitetsudbyder derefter give id'er og hemmeligheder, som du angiver til App-tjenesten. Dette fuldfører tillidsforholdet, så App Service kan validere bruger påstande, såsom godkendelse tokens ud fra identitetsudbyder.

For at logge på en bruger ved hjælp af en af disse udbydere, skal brugeren omdirigeret til et slutpunkt, der logger på brugere for den pågældende provider. Hvis kunder bruger en webbrowser, kan du få App Service automatisk dirigere alle ikke-godkendte brugere til det slutpunkt, der logger på brugere. Ellers skal du dirigere dine kunder til `{your App Service base URL}/.auth/login/<provider>`, hvor `<provider>` er et af følgende værdier: aad, facebook, google, microsoft eller twitter. Mobile og API-scenarier er beskrevet i afsnit senere i denne artikel.

Brugere, der interagerer med dit program via en webbrowser har en cookie, angive, så de kan forblive godkendt, når de søger dit program. For andre klienttyper af som mobile, et JSON web token (JWT), som skal præsenteres i den `X-ZUMO-AUTH` sidehoved, der udstedes af klienten. Mobile-Apps klienten SDK'er håndterer dette for dig. Du kan også en Azure Active Directory identitet token eller adgangstoken kan direkte medtages i den `Authorization` sidehoved som et [bærertoken](https://tools.ietf.org/html/rfc6750).

App Service validerer eventuelle cookie eller token, som dit program problemer for at godkende brugere. Hvis du vil begrænse, hvem der kan få adgang til dit program, skal du i afsnittet [godkendelse](#authorization) senere i denne artikel.

### <a name="mobile-authentication-with-a-provider-sdk"></a>Mobile godkendelse med en udbyder af SDK

Når alt er konfigureret på back-end, kan du ændre mobilklienter at logge på med App Service. Der er to fremgangsmåder her:

- Bruge en SDK, som en given identitetsudbyder udgiver for at fastslå identitet og derefter få adgang til App-tjenesten.
- Brug en enkelt linje af kode, så klient-Mobile-Apps SDK kan logge på brugere.

>[AZURE.TIP] De fleste programmer skal bruge en udbyder af SDK til at få en mere ensartet oplevelse, når brugere logger på, at bruge Opdater support og for at få andre fordele, der angiver, udbyderen.

Når du bruger en udbyder af SDK, kan brugerne logge på for en, der er mere tæt integreret med det operativsystem, app, der kører på. Dette giver dig også en udbyder token og visse brugeroplysninger på klienten, hvilket gør det meget nemmere at bruge graph API'er og tilpasse brugeroplevelsen. Nogle gange på blogge og fora, kan du se dette kaldes "klient strømmen" eller "klient styrede flow", fordi kode på klienten logger på brugere og Klientkoden har adgang til en udbyder token.

Når der opnås en udbyder token, skal den sendes til App-tjeneste til validering. Når App Service valideret tokenet, opretter App Service et nyt App Service-id, der returneres af klienten. Klient-Mobile-Apps SDK har hjælper metoder til at administrere denne exchange og tildele tokenet til alle anmodninger om at programmet back-end automatisk. Udviklere kan også holde en reference til udbyder tokenet, hvis de så vælger.

### <a name="mobile-authentication-without-a-provider-sdk"></a>Mobile godkendelse uden en udbyder af SDK

Hvis du ikke vil konfigurere en udbyder af SDK, kan du tillade funktionen Mobile-Apps i Azure App Service til at logge på for dig. Klient-Mobile-Apps SDK åbnes en webvisning til udbyderen efter eget valg og logge på brugeren. Nogle gange på blogge og fora vises dette kaldes "server forløb" eller "server styrede flow" fordi serveren administrerer den proces, der logger på brugere og klient-SDK aldrig modtager udbyder tokenet.

Kode til at starte dette flow er inkluderet i selvstudiet godkendelse for hver platform. Klient-SDK har et App Service token enden af strømmen, og tokenet vedhæftes automatisk alle anmodninger om at programmet back-end.

### <a name="service-to-service-authentication"></a>Tjeneste til godkendelse

Selvom du kan give brugere adgang til dit program, kan du også har tillid til et andet program til at ringe til din egen API. Du kan for eksempel have én online kalde en API i en anden WebApp. I dette scenarie skal bruge du legitimationsoplysninger for en tjenestekonto i stedet for brugerens legitimationsoplysninger til at få et token. En tjenestekonto er også kendt som en *tjeneste vigtigste* i Azure Active Directory-terminologi, og godkendelse, der bruger disse en konto er også kendt som et scenarie med-tjenester.

>[AZURE.IMPORTANT] Da mobilapps kører på kunde enheder, gøre mobilprogrammer _ikke_ Tæl, som du har tillid til programmer og ikke skal bruge en tjeneste vigtigste flow. I stedet, de skal bruge et flow, der bruger, der er beskrevet tidligere.

App Service kan beskytte dit program ved hjælp af Azure Active Directory for-tjenester scenarier. Opkald programmet skal blot angive et Azure Active Directory-tjenesten vigtigste godkendelse token, der er hentet ved at indsende klienten-ID og hemmeligt fra Azure Active Directory-klienten. Et eksempel på dette scenario, der bruger ASP.NET API apps forklares ved selvstudiet, [Service vigtigste godkendelse til API Apps til][apia-service].

Hvis du vil bruge App Service godkendelse til at håndtere et scenarie med service-tjenesten, kan du bruge klientcertifikater eller basisgodkendelse. Du kan finde oplysninger om klientcertifikater i Azure, [Hvordan til at konfigurere TLS Fællesgodkendelse for Web Apps](../app-service-web/app-service-web-configure-tls-mutual-auth.md). Du kan finde oplysninger om basisgodkendelse i ASP.NET [Godkendelse filtre i ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/authentication-filters).

Tjenesten godkendelse-konto fra en App Service logik app til en API-app er et særligt tilfælde, der er beskrevet i [ved hjælp af din brugerdefinerede API hostes på App Service med logik apps](../app-service-logic/app-service-logic-custom-hosted-api.md).

## <a name="authorization"></a>Hvordan fungerer godkendelse i App-tjeneste

Du har fuld kontrol over de anmodninger, kan få adgang til dit program. App-tjenesten godkendelse / godkendelse kan konfigureres med en af følgende problemer:

- Tillad kun godkendte anmodninger om at oprette forbindelse til dit program.

    Hvis en browser modtager en anonym anmodning, omdirigerer App Service til en side til den identitetsudbyder, som du vælger, så brugerne kan logge på. Hvis anmodningen stammer fra en mobilenhed, skal er den returnerede svar en HTTP _401 Uautoriseret_ svar.

    Med denne indstilling behøver du ikke at skrive en hvilken som helst godkendelse programkode overhovedet i din app. Hvis du har brug for at få bedre authorization, er oplysninger om brugeren tilgængelige for din kode.

- Tillad alle anmodninger om at nå dit program, men validere godkendte anmodninger og overføre langs oplysninger til godkendelse i HTTP-overskrifterne.

    Denne indstilling kan udskyde godkendelse beslutninger til din programkode. Det giver større fleksibilitet i anmodninger om anonym, men du skal skrive programkode.

- Tillad alle anmodninger om at oprette forbindelse til dit program, og Foretag intet på godkendelsesoplysninger i anmodningerne.

    I dette tilfælde, godkendelsen / Authorization funktion er slået fra. Opgaver af godkendelse og autorisation er helt op til din programkode.

De forrige funktionsmåder styres af indstillingen **handling skal udføre, når der ikke er godkendt anmodning** i portalen Azure. Hvis du * *logger på med *udbydernavn* **, alle anmodninger skal godkendes.** Tillade, at anmodning (ingen handling) ** kan udskyde beslutning om godkendelse i din kode, men stadig giver oplysninger til godkendelse. Hvis du vil have din håndtere alt-kode, kan du deaktivere godkendelsen / godkendelse funktion.

## <a name="working-with-user-identities-in-your-application"></a>Arbejde med bruger-id'er i dit program

App Service overfører visse brugeroplysninger til dit program ved hjælp af specielle sidehoveder. Eksterne anmodninger forbyde disse sidehoveder og kun Præsenter Hvis angives af App Service godkendelse / godkendelse. Nogle eksempel overskrifter omfatter:

* X-MS-KLIENT-HOVEDSTOLEN-NAVN
* X-MS-KLIENT-HOVEDSTOLEN-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON

Kode, der er skrevet i en hvilken som helst sprog eller framework kan få de oplysninger, der skal fra disse overskrifter. ASP.NET 4.6 Apps, skal angives **ClaimsPrincipal** automatisk med de tilsvarende værdier.

Programmet kan også få oplysninger om yderligere bruger via en HTTP GET på den `/.auth/me` slutpunkt for dit program. Et gyldigt token, der følger med anmodningen vil returnere en JSON dataene med oplysninger om den udbyder, der bruges, underliggende udbyder tokenet og nogle andre brugeroplysninger. Mobile-Apps server SDK'er giver hjælper metoder til at arbejde med disse data. Se [hvordan du bruger Azure Mobile Apps Node.js SDK](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity)og [arbejde med .NET back end-serveren SDK til Azure Mobile-Apps](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info)kan finde flere oplysninger.

## <a name="documentation-and-additional-resources"></a>Dokumentation og yderligere ressourcer

### <a name="identity-providers"></a>Identitetsudbydere
Følgende selvstudier viser, hvordan du konfigurerer App Service for at bruge forskellige godkendelsesprovidere:

- [Sådan konfigureres din app for at bruge Azure Active Directory-logon][AAD]
- [Sådan konfigureres din app for at bruge Facebook-logon][Facebook]
- [Sådan konfigureres din app Hvis du vil bruge Google-logon][Google]
- [Sådan konfigureres din app for at bruge Microsoft Account logon][MSA]
- [Sådan konfigureres din app for at bruge Twitter-logon][Twitter]

Hvis du vil bruge et identitetssystem end dem, der findes her, kan du også bruge [Gennemse understøttelse af brugerdefinerede godkendelse i Mobile Apps .NET server SDK][custom-auth], som kan bruges i webapps, mobilapps eller API apps.

### <a name="web-applications"></a>Webprogrammer
Følgende selvstudier viser, hvordan du føjer godkendelse til et webprogram:

- [Introduktion til Azure App Service - del 2][web-getstarted]

### <a name="mobile-applications"></a>Mobilprogrammer
Følgende selvstudier viser, hvordan du føjer godkendelse til din mobilklienter ved hjælp af strømmen styrede server:

- [Føje godkendelse til din iOS-app][iOS]
- [Tilføje godkendelse til din Android-app] [Android]
- [Tilføje godkendelse til din Windows-app] [Windows]
- [Tilføj godkendelse til din Xamarin.iOS-app] [Xamarin.iOS]
- [Tilføj godkendelse til din Xamarin.Android-app] [Xamarin.Android]
- [Tilføj godkendelse til din Xamarin.Forms-app] [Xamarin.Forms]
- [Tilføje godkendelse til din Cordova-app] [Cordova]

Brug følgende ressourcer, hvis du vil bruge strømmen klient-ført til Azure Active Directory:

- [Brug Active Directory Authentication Library til iOS][ADAL-iOS]
- [Bruge Active Directory Authentication Library til Android][ADAL-Android]
- [Brug Active Directory Authentication Library til Windows og Xamarin][ADAL-dotnet]

Brug følgende ressourcer, hvis du vil bruge strømmen klient-ført til Facebook:

- [Brug af SDK til Facebook til iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Brug følgende ressourcer, hvis du vil bruge strømmen klient-ført til Twitter:

- [Brug Twitter-strukturen til iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Brug følgende ressourcer, hvis du vil bruge strømmen klient-ført til Google:

- [Bruge Google-logon SDK til iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

### <a name="api-applications"></a>API-programmer
Følgende selvstudier viser, hvordan du beskytter dine API-apps:

- [Brugergodkendelse til API-Apps i Azure App Service][apia-user]
- [Tjenesten vigtigste godkendelse til API-Apps i Azure App Service][apia-service]









[apia-user]: ../app-service-api/app-service-api-dotnet-user-principal-auth.md
[apia-service]: ../app-service-api/app-service-api-dotnet-service-principal-auth.md

[web-getstarted]: ../app-service-web/app-service-web-get-started-2.md#authenticate-your-users

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: ../app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: ../app-service-mobile/app-service-mobile-how-to-configure-google-authentication.md
[MSA]: ../app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: ../app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
