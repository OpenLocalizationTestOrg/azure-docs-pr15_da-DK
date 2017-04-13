<properties
   pageTitle="Azure Active Directory v2.0 godkendelse biblioteker | Microsoft Azure"
   description="Kompatibel klientbiblioteker og server program biblioteker og relaterede bibliotek, kilde og eksempler links, til Azure Active Directory v2.0 slutpunkt."
   services="active-directory"
   documentationCenter=""
   authors="skwan"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/30/2016"
   ms.author="skwan;bryanla"/>


# <a name="azure-active-directory-v20-authentication-libraries"></a>Azure Active Directory v2.0 godkendelse biblioteker
Azure Active Directory (Azure AD) v2.0 slutpunktet understøtter branchestandard OAuth 2.0 og OpenID forbinde 1.0 protokoller. Du kan bruge forskellige biblioteker fra Microsoft og andre organisationer med v2.0 slutpunkt.

Når du opretter et program, der bruger v2.0 slutpunktet, anbefaler vi, at du bruger biblioteker, der er skrevet af protocol domæne eksperter som følger en metode til sikkerhed udvikling livscyklus SDL (), som [den, efterfulgt af Microsoft][Microsoft-SDL]. Hvis du beslutter at hånd-kode understøttelse af protokollerne, anbefaler vi, du følger SDL metode og være opmærksom på sikkerhedsovervejelser i standarder specifikationer for hver protokol.

## <a name="types-of-libraries"></a>Bibliotekstyper

Azure AD-v2.0 arbejder med to typer biblioteker:

- **Klient-biblioteker**. Oprindelig klienter og servere bruge klientbiblioteker til at få adgangstokens for opkald til en ressource, som Microsoft Graph.
- **Server program biblioteker**. Onlines bruge server program biblioteker til brugerlogon. Web API'er bruge server program biblioteker til at validere tokens, der sendes af oprindelige klienter eller andre servere.

## <a name="library-support"></a>Understøttelse af bibliotek
Da du kan vælge en hvilken som helst standarder-kompatibel bibliotek, når du bruger v2.0 slutpunktet, er det vigtigt at vide, hvor du kan finde support. Problemer og anmode om funktioner i biblioteket kode ved at kontakte ejeren bibliotek. Kontakt Microsoft for problemer og anmode om funktioner i implementeringen service side-protokollen.

Biblioteker fås i to support kategorier:

- **Microsoft-understøttede**. Microsoft indeholder løsninger til disse biblioteker, og er udført SDL forfaldne omhu på disse biblioteker.
- **Kompatible**. Microsoft har testet disse biblioteker i grundlæggende brugsscenarier og bekræftet, at de fungerer med v2.0 slutpunkt. Microsoft ikke yder løsninger på disse biblioteker og er ikke udført en gennemgang af disse biblioteker. Problemer og anmodninger skal sendes til bibliotekets open source-projekt.

Du kan finde en liste over biblioteker, der arbejder med v2.0 slutpunktet, de næste afsnit i denne artikel.

## <a name="microsoft-supported-client-libraries"></a>Microsoft-understøttet client biblioteker
| Platform| Dokumentbibliotekets navn| Download | Kildekode | Eksempel |
| :-: | :-: | :-: | :-: | :-: |
| .NET, Windows Store Xamarin | Godkendelse af Microsoft-bibliotek (MSAL) til .NET | [Microsoft.Identity.Client (NuGet)][ClientLib-NET-Lib] | [MSAL til .NET (GitHub)][ClientLib-NET-Repo] | [Windows-klienten på computeren oprindelige eksempel][ClientLib-NET-Sample] |
| Node.js | Microsoft Azure Active Directory Passport.js plug-in'en | [Passport-Azure-AD (npm)][ClientLib-Node-Lib] | [Passport-Azure-AD (GitHub)][ClientLib-Node-Repo] | Kommer snart |

<!--- COMMENTING OUT UNTIL THEY ARE READY
| iOS, Mac | Microsoft Authentication Library (MSAL) for ObjC | In development | In development | In development |
| Android | Microsoft Authentication Library (MSAL) for Android | In development | In development | In development |
| JavaScript | Microsoft Authentication Library (MSAL) for JavaScript | In development | In development | In development |
 -->

## <a name="microsoft-supported-server-middleware-libraries"></a>Microsoft-understøttede server program-biblioteker
| Platform| Dokumentbibliotekets navn| Download | Kildekode | Eksempel |
| :-: | :-: | :-: | :-: | :-: |
| .NET 4.x | OWIN OpenID forbinde programmer til ASP.NET | [Microsoft.Owin.Security.OpenIdConnect (NuGet)][ServerLib-Net4-Owin-Oidc-Lib] | [Katana Project (CodePlex)][ServerLib-Net4-Owin-Oidc-Repo] | [Eksempel på Web app][ServerLib-Net4-Owin-Oidc-Sample] |
| .NET 4.x | OWIN OAuth bæreren programmer til ASP.NET | [Microsoft.Owin.Security.OAuth (NuGet)][ServerLib-Net4-Owin-Oauth-Lib] | [Katana Project (CodePlex)][ServerLib-Net4-Owin-Oauth-Repo] | [Web API-eksempel][ServerLib-Net4-Owin-Oauth-Sample] |
| .NET core | OWIN OpenID forbinde programmer til .NET Core | [Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] | [ASP.NET-sikkerhed (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] | [Eksempel på Web app][ServerLib-NetCore-Owin-Oidc-Sample] |
| .NET core | OWIN OAuth bæreren programmer til .NET Core | [Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] | [ASP.NET-sikkerhed (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] | Kommer snart |
| Node.js | Microsoft Azure Active Directory Passport.js plug-in'en | [Passport-Azure-AD (npm)][ServerLib-Node-Lib] | [Passport-Azure-AD (GitHub)][ServerLib-Node-Repo] | [Eksempel på Web app][ServerLib-Node-Sample] |
<!--- COMMENTING UNTIL SAMPLE IS AVAILABLE
| .NET 4.x, .NET Core | JSON Web Token Handler for .NET | [System.IdentityModel.Tokens.Jwt (NuGet)][ServerLib-Net-Jwt-Lib] | [Azure AD identity model extensions for .NET (GitHub)][ServerLib-Net-Jwt-Repo] | Coming soon |
--->
## <a name="compatible-client-libraries"></a>Kompatibel klientbiblioteker
| Platform| Dokumentbibliotekets navn | Testet version | Kildekode | Eksempel |
| :-: | :-: | :-: | :-: | :-: |
| Android | [OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) | 0.2.1 | [OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) | [Oprindelig app eksempel](active-directory-v2-devquickstarts-android.md) |
| iOS | [NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) | 1.2.8 | [NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) | [Oprindelig app eksempel](active-directory-v2-devquickstarts-ios.md)|
| JavaScript | [Hello.js](https://adodson.com/hello.js/) | 1.13.5 | [Hello.js](https://github.com/MrSwitch/hello.js) | Kommer snart |
| Python anbringes | [Anbringes OAuthlib](https://github.com/lepture/flask-oauthlib) | 0.9.3 | [Anbringes OAuthlib](https://github.com/lepture/flask-oauthlib) | Kommer snart |
| Fonetisk | [OmniAuth](https://github.com/omniauth/omniauth/wiki) | omniauth:1.3.1</br>omniauth-oauth2:1.4.0 | [OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) | Kommer snart |
<!--- REMOVING BRANDON'S FOR NOW
|  |  |  |  |  |
| Android | [OAuth2 Client](https://github.com/wuman/android-oauth-client) |   | [OAuth2 Client](https://github.com/wuman/android-oauth-client)  | Coming soon  |
| Java | [WSO2 Identity Server](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server) | [Version 5.2.0](http://wso2.com/products/identity-server/) | [Source](https://docs.wso2.com/display/IS500/Building+from+Source) | [Samples index](https://docs.wso2.com/display/IS500/Samples)  |
| Java | [Java Gluu Server](https://gluu.org/docs/) |   | [oxAuth](https://github.com/GluuFederation/oxAuth)  | Coming soon |
| Node.js | [NPM passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect) | 0.0.1  | [Passport-OpenID Connect](https://github.com/jaredhanson/passport-openidconnect) | Coming soon  |
| PHP | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP) |   | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP)  | Coming soon  |
-->

## <a name="compatible-server-middleware-libraries"></a>Kompatible server program-biblioteker
Kommer snart

## <a name="related-content"></a>Relateret indhold
Få vist [Azure AD app model v2.0 oversigt]kan finde flere oplysninger om Azure AD v2.0 slutpunktet[AAD-App-Model-V2-Overview].

For at hjælpe os med at tilpasse og forme vores indhold, skal du bruge funktionen Disqus kommentarer i slutningen af denne artikel til at give feedback.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:
[ClientLib-Iosmac-Lib]:
[ClientLib-Iosmac-Repo]:
[ClientLib-Iosmac-Sample]:
[ClientLib-Android-Lib]:
[ClientLib-Android-Repo]:
[ClientLib-Android-Sample]:
[ClientLib-Js-Lib]:
[ClientLib-Js-Repo]:
[ClientLib-Js-Sample]:
[Microsoft-SDL]: http://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-node-web/
