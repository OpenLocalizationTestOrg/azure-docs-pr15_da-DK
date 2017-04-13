<properties
   pageTitle="Azure Active Directory Authentication biblioteker | Microsoft Azure"
   description="Azure AD godkendelse bibliotek (ADAL) kan klienten programmerudviklere nemt godkende brugere til skyen eller lokale Active Directory (AD), og derefter få access tokens til sikring af API-kald."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/11/2016"
   ms.author="mbaldwin" />

# <a name="azure-active-directory-authentication-libraries"></a>Azure Active Directory Authentication biblioteker

Azure AD-godkendelse bibliotek (ADAL) gør det muligt for udviklere klienten til at godkende nemt brugere til skyen eller lokale Active Directory (AD), og derefter få access tokens til sikring af API-kald. ADAL har mange funktioner, at foretage godkendelse nemmere for udviklere, som asynkron understøttelse af en konfigurerbar token cache, som lagrer access tokens og Opdater tokens, automatisk token opdatering, når et adgangstoken udløber, og et Opdater token er tilgængelig, og meget mere. Ved at håndtere de fleste af kompleksitet, ADAL hjælpe udvikler fokus på forretningslogik i deres program og nemt secure ressourcer uden at være en ekspert på sikkerhed.

ADAL findes på en række forskellige platforme.

|Platform|Pakkenavn|Klient/Server|Download|Kildekode|Dokumentation og eksempler|
|---|---|---|---|---|---|
|.NET-klienten skal Windows Store UWP, Xamarin iOS og Android|Active Directory Authentication Library (ADAL) til .NET v3 |Klient|[Microsoft.IdentityModel.Clients.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory)|[ADAL til .NET (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet)|[Dokumentation](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory)|
|.NET-klienten skal Windows Store, Windows Phone 8.1 |Active Directory Authentication Library (ADAL) til .NET v2 |Klient|[Microsoft.IdentityModel.Clients.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.2)|[ADAL til .NET (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.2)|[Dokumentation](https://docs.microsoft.com/active-directory/adal/v2/microsoft.identitymodel.clients.activedirectory)|
|JavaScript|Active Directory Authentication Library (ADAL) for JavaScript|Klient|[ADAL JavaScript (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|[ADAL JavaScript (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|Eksempel: [SinglePageApp-DotNet (Github)](https://github.com/AzureADSamples/SinglePageApp-DotNet)|
|OS X, iOS|Active Directory Authentication Library (ADAL) for mål-C|Klient|[ADAL for mål-C (CocoaPods)](http://cocoadocs.org/docsets/ADAL/)|[ADAL for mål-C (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-objc)|Eksempel: [NativeClient-iOS (Github)](https://github.com/AzureADSamples/NativeClient-iOS)|
|Android|Active Directory Authentication Library (ADAL) til Android|Klient|[ADAL til Android (det centrale lager)](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/)|[ADAL til Android (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-android)|Eksempel: [NativeClient-Android (Github)](https://github.com/AzureADSamples/NativeClient-Android)|
|Node.js|Active Directory Authentication Library (ADAL) til Node.js|Klient|[ADAL for Node.js (npm)](https://www.npmjs.com/package/adal-node)|[ADAL for Node.js (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs)|Eksempel: [WebAPI-Nodejs (Github)](https://github.com/AzureADSamples/WebAPI-Nodejs)|
|Node.js|Microsoft Azure Active Directory Passport godkendelse program for Node|Klient|[Azure Active Directory-Passport til Node.js (npm)](https://www.npmjs.com/package/passport-azure-ad)|[Azure Active Directory for Node.js (Github)](https://github.com/AzureAD/passport-azure-ad)||
|Java|Active Directory Authentication Library (ADAL) til Java|Klient|[ADAL til Java (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)|[ADAL til Java (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)||
|.NET|Identitet Protocol-udvidelser til Microsoft .NET Framework 4.5|Server|[Microsoft.IdentityModel.Protocol.Extensions (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions)|[Azure AD identitet model filtypenavne for .NET (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|JSON Web Token Handler til Microsoft .net Framework 4.5|Server|[System.IdentityModel.Tokens.Jwt (NuGet)](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt)|[Azure AD identitet model filtypenavne for .NET (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|OWIN programmer, der gør det muligt for et program tilladelse til at bruge Microsoft-teknologi til godkendelse.|Server|[Microsoft.Owin.Security.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)||
|.NET|OWIN programmer, der gør det muligt for et program, der skal bruges OpenIDConnect til godkendelse.|Server|[Microsoft.Owin.Security.OpenIdConnect (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)|Eksempel: [WebApp-OpenIDConnecty-DotNet (Github)](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet)|
|.NET|OWIN programmer, der gør det muligt for et program, der skal bruges WS-Federation til godkendelse.|Server|[Microsoft.Owin.Security.WsFederation (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)|Eksempel: [WebApp-WSFederation-DotNet (Github)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet)|

## <a name="scenarios"></a>Scenarier

Her er tre almindelige scenarier, hvor ADAL kan bruges til godkendelse.  

### <a name="authenticating-users-of-a-client-application-to-a-remote-resource"></a>Godkendelse af brugere af en-klientprogrammet til en ekstern ressource

I dette scenarie skal har en udvikler en klient, som et WPF-program, der skal have adgang til en ekstern ressource sikret med Azure AD, som en web API. Hun har et abonnement på Azure, ved, hvordan du kan aktivere efterfølgende web API og ved, hvilken web API bruger Azure AD-lejer. Han kan derfor bruge ADAL for at lette godkendelse med Azure AD, enten ved at fuldt uddelegere godkendelse-oplevelsen til ADAL eller ved at eksplicit håndtering af brugerlegitimationsoplysninger. ADAL gør det nemt at godkende brugeren, få en adgangstoken, og Opdater token fra Azure AD og derefter bruge adgangstoken til at foretage anmodninger på World Wide web API.

Du kan finde en kode prøve som demonstrerer dette scenario, ved hjælp af godkendelse til Azure AD og [Oprindelige WPF-klientprogrammet til Web API](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-server-application-to-a-remote-resource"></a>Godkendelse af en Server-program til en ekstern ressource

I dette scenarie skal har en udvikler et program, der kører på en server, der skal have adgang til en ekstern ressource sikret med Azure AD, som en web API. Hun har et abonnement på Azure, ved, hvordan du kan starte tjenesten efterfølgende og kender Azure AD-lejer web API bruger. Han kan derfor bruge ADAL for at lette godkendelse med Azure AD ved eksplicit håndtering af programmets legitimationsoplysninger. ADAL gør det nemt at hente et token fra Azure AD ved hjælp af programmets klient legitimationsoplysninger og derefter bruge dette token til at foretage en anmodning på World Wide web API. ADAL håndterer også administration af levetiden for adgangstoken ved cachelagring af den og forny det efter behov. For kodeeksempel en, der har demonstrerer dette scenario, skal du se [Console program tilladelse til at Web API](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-server-application-on-behalf-of-a-user-to-access-a-remote-resource"></a>Godkendelse af en Server-program på vegne af en bruger til at få adgang til en ekstern ressource

I dette scenarie skal har en udvikler et program, der kører på en server, der skal have adgang til en ekstern ressource sikret med Azure AD, som en web API. Anmodningen skal også foretages på vegne af en bruger i Azure AD. Hun har et abonnement på Azure, ved, hvordan du aktiverer den efterfølgende web API og kender den Azure AD lejer tjeneste bruger. Når brugeren er godkendt til webprogrammet, kan programmet åbne en godkendelseskode for for brugeren, Azure AD. Webprogrammet kan derefter bruge ADAL til at få et adgangstoken, og Opdater token på vegne af en bruger ved hjælp af godkendelse kode og klienten legitimationsoplysningerne, der er knyttet til programmet fra Azure AD. Når webprogrammet er i besiddelse af adgangstoken, kan det kalde web API, indtil tokenet udløber. Når tokenet udløber, kan webprogrammet bruge ADAL for at få en ny adgangstoken ved hjælp af opdateringen token, der tidligere blev modtaget.


## <a name="see-also"></a>Se også

[Azure Active Directory-developer guide](active-directory-developers-guide.md)

[Godkendelse scenarier, hvor Azure Active directory](active-directory-authentication-scenarios.md)

[Azure Active Directory-kodeeksempler](active-directory-code-samples.md)
