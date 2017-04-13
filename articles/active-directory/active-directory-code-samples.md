<properties
   pageTitle="Azure Active Directory kodeeksempler | Microsoft Azure"
   description="Et indeks over Azure Active Directory kodeeksempler, organiseret efter scenarie."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="priyamohanram"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# <a name="azure-active-directory-code-samples"></a>Azure Active Directory kodeeksempler

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Du kan bruge Microsoft Azure Active Directory (Azure AD) til at føje godkendelse og autorisation til webprogrammer og web API'er. Dette afsnit indeholder hyperlinks til kodeeksempler, der viser, hvordan du gør og kodestykker, du kan bruge i dine programmer. På siden kode eksempel du kan finde detaljerede læst-mig emner, der hjælper med krav, installation og konfiguration. Og koden er kommenterede kan hjælpe dig med at forstå de kritiske afsnit.

For at forstå det grundlæggende scenario for hver type eksempel, skal du se godkendelse scenarier til Azure AD.

Bidrage til vores eksempler på GitHub: [Microsoft Azure Active Directory eksempler og dokumentation](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="web-browser-to-web-application"></a>Webbrowser til webprogram

Disse eksempler viser, hvordan du kan skrive et webprogram, der omdirigerer brugerens browser til at logge dem på Azure AD.



| Sprog/Platform | Eksempel | Beskrivelse
| ----------------- | ------ | -----------
| C# / .NET | [WebApp-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) | Bruge OpenID Connect (ASP.Net OpenID forbinde OWIN et program) til at godkende brugere fra en Azure AD-lejer.
| C# / .NET | [WebApp-MultiTenant-OpenIdConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) | En med flere lejer .NET MVC webprogram, der bruger OpenID forbinde (ASP.Net OpenID forbinde OWIN et program) til at godkende brugere fra flere Azure AD-lejere.
| C# / .NET | [WebApp-WSFederation-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) | Brug WS-Federation (ASP.Net WS-Federation OWIN et program) til at godkende brugere fra en Azure AD-lejer.






## <a name="single-page-application-spa"></a>Enkelt side program (SPA)

Dette eksempel vises, hvordan du kan skrive en enkelt side-program, der er sikret med Azure AD.  

| Sprog/Platform | Eksempel | Beskrivelse
| ----------------- | ------ | -----------
| JavaScript, C# / .NET | [SinglePageApp DotNet](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | Brug ADAL til JavaScript og Azure AD til at sikre en AngularJS-baserede enkelt side-app, der er implementeret med en ASP.NET web API backend.


## <a name="native-application-to-web-api"></a>Oprindelige program til Web API

Disse kodeeksempler, der viser, hvordan du opretter oprindelige klientprogrammer, der kalder web API'er, der er sikret med Azure AD. De bruger [Azure AD godkendelse bibliotek (ADAL)](active-directory-authentication-libraries.md) og [OAuth 2.0 i Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Sprog/Platform | Eksempel | Beskrivelse
| ----------------- | ------ | -----------
| JavaScript | [NativeClient-MultiTarget-Cordova](https://github.com/Azure-Samples/active-directory-cordova-multitarget) | Bruge ADAL plug-in'et til Apache Cordova til at oprette en Apache Cordova-app, der kalder en web API og bruger Azure AD til godkendelse.
| C# / .NET | [NativeClient DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) | En .NET WPF-program, der kalder en web API, der er sikret ved hjælp af Azure AD.
| C# / .NET | [NativeClient WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) | En Windows Store-program, der kalder en web API, der er sikret med Azure AD.
| C# / .NET | [NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) | Et Windows Store-program, der kalder en med flere lejer web API, der er sikret med Azure AD.
| C# / .NET | [WebAPI-OnBehalfOf-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof) | Et native client-program, der kalder en web API, som får et token til at handle på vegne af den oprindelige bruger, og derefter bruger tokenet til at ringe til en anden web API.
| C# / .NET | [NativeClient WindowsPhone8.1](https://github.com/Azure-Samples/active-directory-dotnet-windowsphone-8.1) | En Windows Store-programmet til Windows Phone 8.1, der kalder en web API, der er sikret med Azure AD.
| ObjC | [NativeClient iOS](https://github.com/Azure-Samples/active-directory-ios) | En iOS-program, der kalder en web API, der kræver Azure AD til godkendelse.
| C# / .NET | [WebAPI-ManuallyValidateJwt-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation) | Et native client-program, der indeholder logik for at behandle en JWT token i en web API, i stedet for ved hjælp af OWIN programmer.
| C# / Xamarin | [NativeClient-Xamarin-Android](https://github.com/Azure-Samples/active-directory-xamarin-android) | En Xamarin binding til den oprindelige Azure AD godkendelse bibliotek (ADAL) for biblioteket Android.
| C# / Xamarin | [NativeClient-Xamarin-iOS](https://github.com/Azure-Samples/active-directory-xamarin-ios) | En Xamarin binding til den oprindelige Azure AD godkendelse bibliotek (ADAL) til iOS.
| C# / Xamarin | [NativeClient-MultiTarget-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-multitarget) | Et Xamarin projekt, rettet mod fem platforme og kalder en web API, der er sikret med Azure AD.
| C# / .NET | [NativeClient-konsolløs-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-headless) | En oprindelige program, udfører ikke-interaktiv godkendelse og kalder en web API, der er sikret med Azure AD.



## <a name="web-application-to-web-api"></a>Webprogrammet Web API

Disse kodeeksempler, Vis hvordan bruge [OAuth 2.0 i Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) til at opbygge Webprogrammer, der kalder web API'er, der er sikret med Azure AD.

| Sprog/Platform | Eksempel | Beskrivelse
| ----------------- | ------ | -----------
| C# / .NET | [WebApp-WebAPI-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect) | Ringe til et websted API med logget på brugerens tilladelser.
|  C# / .NET | [WebApp-WebAPI-OAuth2-AppIdentity-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity) | Ringe til et websted API med programmets tilladelser.
| C# / .NET | [WebApp-WebAPI-OAuth2-UserIdentity-Dotnet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | Føje tilladelse med [OAuth 2.0 i Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) til et eksisterende webprogram, så den kan ringe til en web API.
| JavaScript | [WebAPI Nodejs](https://github.com/Azure-Samples/active-directory-node-webapi) | Konfigurere en REST-API-tjeneste, der er integreret med Azure AD til API beskyttelse. Indeholder en Node.js server med en Web API.
| C# / .NET | [WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-multitenant-openidconnect) |  En med flere lejer MVC webprogram, der bruger OpenID forbinde (ASP.Net OpenID forbinde OWIN et program) til at godkende brugere fra en Azure AD-lejer. Bruger en kode for tilladelse til at kalde API'EN Graph.

## <a name="server-or-daemon-application-to-web-api"></a>Server eller daemonprogram til Web API

Disse kodeeksempler, der viser, hvordan du opretter en daemon eller server-program, der henter ressourcer fra en web API ved hjælp af [Azure AD godkendelse bibliotek (ADAL)](active-directory-authentication-libraries.md) og [OAuth 2.0 i Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Sprog/Platform | Eksempel | Beskrivelse
| ----------------- | ------ | -----------
| C# / .NET | [Daemon DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon) | En console programmet kalder en web API. Klient-legitimationsoplysninger er en adgangskode.
| C# / .NET | [Daemon-CertificateCredential-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) | En console-program, der kalder en web API. Klient-legitimationsoplysninger er et certifikat.


## <a name="calling-azure-ad-graph-api"></a>Ringe til Azure AD-graf API

Eksempel på disse kode viser, hvordan du opbygge programmer, der kalder API'EN Azure AD Graph for at læse og skrive directory-data.

| Sprog/Platform | Eksempel | Beskrivelse
| ----------------- | ------ | -----------
| Java | [WebApp-GraphAPI-Java](https://github.com/Azure-Samples/active-directory-java-graphapi-web) | Et webprogram, der bruger Graph API til at få adgang til Azure AD directory data.
| PHP | [WebApp-GraphAPI-PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-web) | Et webprogram, der bruger Graph API til at få adgang til Azure AD directory data.
| C# / .NET | [WebApp-GraphAPI-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) | Et webprogram, der bruger Graph API til at få adgang til Azure AD directory data.
| C# / .NET | [ConsoleApp-GraphAPI-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-console) | Denne console app viser almindelige læse- og skrivetilladelser opkald til Graph API og viser, hvordan du kan udføre bruger licensaftalen og opdatere en brugers miniature og links.
| C# / .NET | [ConsoleApp-GraphAPI-DiffQuery-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-diffquery) | Et console-program, der bruger forespørgslen forskelle i Graph API til at hente periodiske ændres til user-objekter i en Azure AD-lejer.
| C# / .NET | [WebApp-GraphAPI-DirectoryExtensions-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-directoryextensions-web) | Et MVC program bruges Graph API forespørgsler til at oprette en simpel virksomhed organisationsdiagram.
| PHP | [WebApp-GraphAPI-DirectoryExtensions-PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-directoryextensions-web) | En PHP program, der kalder API'EN Graph for at registrere et filtypenavn og derefter læse, opdatere og slette værdier i attributten filtypenavn.


## <a name="authorization"></a>Godkendelse

Disse kodeeksempler, der viser, hvordan du bruger Azure AD til godkendelse.

| Sprog/Platform | Eksempel | Beskrivelse
| ----------------- | ------ | -----------
| C# / .NET | [WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) | Udføre rolle baseret adgangskontrol (RBAC) ved hjælp af Azure Active Directory-gruppe krav i et program, der er integreret med Azure AD.
| C# / .NET | [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Udføre rolle baseret adgangskontrol (RBAC) ved hjælp af Azure Active Directory-programroller i et program, der er integreret med Azure AD.


## <a name="legacy-walkthroughs"></a>Ældre gennemgange

Disse gennemgange bruge lidt ældre teknologi, men stadig være af interesse.

| Sprog/Platform | Eksempel | Beskrivelse
| ----------------- | ------ | -----------
| C# / .NET | [Rollebaseret og ACL-baseret godkendelse i et Microsoft Azure AD-program](http://go.microsoft.com/fwlink/?LinkId=331694) | Udføre rollebaseret godkendelse (RBAC) og ACL-baseret godkendelse i et program, der er integreret med Azure AD.
| C# / .NET |  [AAL - RESTEN tjenesten Windows Store app - godkendelse](http://go.microsoft.com/fwlink/?LinkId=330605) |  Brug [Azure AD godkendelse bibliotek (ADAL)](active-directory-authentication-libraries.md) , der (tidligere AAL) til Windows Store Beta til at føje brugeren godkendelse funktioner til en Windows Store app.
| C# / .NET | [ADAL - oprindelig App til RESTEN service - godkendelse med AAD via browserdialogboksen](http://go.microsoft.com/fwlink/?LinkId=259814) |  Brug [Azure AD godkendelse bibliotek (ADAL)](active-directory-authentication-libraries.md) til at føje brugeren godkendelse funktioner til en WPF-klient.
| C# / .NET | [ADAL - oprindelig App til RESTEN service - godkendelse med ACS via browserdialogboksen](http://code.msdn.microsoft.com/AAL-Native-App-to-REST-de57f2cc) |  Brug [Azure AD godkendelse bibliotek (ADAL)](active-directory-authentication-libraries.md) og [Access 2.0 ACS (Control Service)](http://msdn.microsoft.com/library/azure/hh147631.aspx) til at tilføje bruger godkendelse funktioner til en WPF-klient.
| C# / .NET | [ADAL - Server til Server-godkendelse](http://go.microsoft.com/fwlink/?LinkId=259816) | Brug [Azure AD godkendelse bibliotek (ADAL)](active-directory-authentication-libraries.md) til at sikre tjeneste opkald fra en server side proces til en MVC4 Web API RESTEN tjeneste.
| C# / .NET | [Tilføje Sign-On til din webprogrammet ved hjælp af Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) | Konfigurere en .NET program til at udføre web enkeltlogon mod adresselisten Azure AD enterprise.
| C# / .NET | [Udvikling af flere lejer webprogrammer med Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) | Brug Azure AD til at føje til single sign-on og directory access funktionerne i én .NET program tilladelse til at arbejde på tværs af flere organisationer.
JAVA | [Java eksempel App til Azure AD Graph API](http://go.microsoft.com/fwlink/?LinkId=263969) | Brug Graph API til access directory data fra Azure AD.
PHP | [PHP eksempel App til Azure AD Graph API](http://code.msdn.microsoft.com/PHP-Sample-App-For-Windows-228c6ddb) | Brug Graph API til access directory data fra Azure AD.
| C# / .NET | [Eksempel-App til Azure AD Graph API](http://go.microsoft.com/fwlink/?LinkID=262648) | Brug Graph API til access directory data fra Azure AD.
| C# / .NET | [Eksempel-App til Azure AD-graf forskelle forespørgsel](http://go.microsoft.com/fwlink/?LinkId=275398) | Brug forespørgslen forskelle i Graph API til at hente periodiske ændringer til user-objekter i en Azure AD-lejer.
| C# / .NET | [Eksempel-App til integration med flere lejer Cloud-programmet til Azure AD](http://go.microsoft.com/fwlink/?LinkId=275397) | Integrere et program med flere arkitekturer i Azure AD.
| C# / .NET | [Sikring af en Windows Store-program og REST-webtjeneste ved hjælp af Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) | Oprette en simpel web API-ressource og en Windows Store-klientprogrammet ved hjælp af Azure AD og [Azure AD godkendelse bibliotek (ADAL)](active-directory-authentication-libraries.md).
| C# / .NET| [Brug af Graph API til at forespørge Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) | Konfigurere en Microsoft .NET program du bruger Azure AD Graph API til at få adgang til data fra en Azure AD-lejer adresseliste.

## <a name="see-also"></a>Se også

##### <a name="other-resources"></a>Andre ressourcer

[Azure Active Directory Developer Guide](active-directory-developers-guide.md)

[Azure AD Graph API grundlæggende og Reference](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Azure AD-graf API-hjælpeprogram bibliotek](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)

