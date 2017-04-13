<properties
    pageTitle="Hvad skete der med mine WebApi projekt (Visual Studio Azure Active Directory forbindelse service) | Microsoft Azure "
    description="I denne artikel beskrives, hvad sker der med projektet MVC WebApi, du opretter forbindelse til Azure AD ved hjælp af Visual Studio"
  services="active-directory"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="web"
    ms.tgt_pltfrm="vs-what-happened"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Hvad skete der med mine WebApi projekt (Visual Studio Azure Active Directory forbindelse service)

> [AZURE.SELECTOR]
> - [Kom godt i gang](vs-active-directory-webapi-getting-started.md)
> - [Hvad skete der](vs-active-directory-webapi-what-happened.md)

##<a name="references-have-been-added"></a>Referencer er blevet tilføjet

###<a name="nuget-package-references"></a>NuGet pakke referencer

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

###<a name="net-references"></a>.NET referencer

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

##<a name="code-changes"></a>Kodeændringer

###<a name="code-files-were-added-to-your-project"></a>Kodefiler er blevet føjet til projektet

En godkendelse Start klasse **App_Start/Startup.Auth.cs** blev føjet til dit projekt, der indeholder start logik til Azure AD-godkendelse.

###<a name="startup-code-was-added-to-your-project"></a>Start kode blev føjet til projektet

Hvis du allerede har en start-klasse i dit projekt skal metoden **konfiguration** blev opdateret for at medtage et opkald til `ConfigureAuth(app)`. Ellers kan blev en start-klasse føjet til projektet.


###<a name="your-appconfig-or-webconfig-file-has-new-configuration-values"></a>App.config eller web.config filen indeholder nye konfigurationsværdier.

Følgende konfigurationsposter er blevet tilføjet.
```
    `<appSettings>
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:Tenant" value="Your selected Azure AD Tenant" />
            <add key="ida:Audience" value="The App ID Uri from the wizard" />
    </appSettings>`
```

###<a name="an-azure-ad-app-was-created"></a>En Azure AD-App blev oprettet

Et Azure AD-program blev oprettet i den mappe, du har valgt i guiden.

[Lær mere om Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

##<a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>Hvis jeg markeret *deaktivere individuelle brugerkonti godkendelse*, hvilke yderligere ændringer der er foretaget på mit projekt?
NuGet pakke referencer er blevet fjernet, og filer er blevet fjernet og sikkerhedskopieret. Afhængigt af tilstanden for dit projekt, kan være du nødvendigt at fjerne flere referencer eller filer manuelt eller redigere koden efter behov.

###<a name="nuget-package-references-removed-for-those-present"></a>NuGet pakke referencer fjernes (for de findes)

- `Microsoft.AspNet.Identity.Core`
- `Microsoft.AspNet.Identity.EntityFramework`
- `Microsoft.AspNet.Identity.Owin`

###<a name="code-files-backed-up-and-removed-for-those-present"></a>Kodefiler sikkerhedskopieret og fjernet (for de findes)

Hver af følgende filer blev sikkerhedskopieret og fjernet fra projektet. Sikkerhedskopifilerne er placeret i en ' sikkerhedskopimappe ' i roden af projektets directory.

- `App_Start\IdentityConfig.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Providers\ApplicationOAuthProvider.cs`

###<a name="code-files-backed-up-for-those-present"></a>Kodefiler,, der er sikkerhedskopieret (for de findes)

Hver af følgende filer blev sikkerhedskopieret før skal erstattes. Sikkerhedskopifilerne er placeret i en ' sikkerhedskopimappe ' i roden af projektets directory.

- `Startup.cs`
- `App_Start\Startup.Auth.cs`

##<a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>Hvis jeg markeret *læse directory data*, hvilke yderligere ændringer der er foretaget på mit projekt?

###<a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>Yderligere ændringer foretaget til app.config eller web.config

Følgende yderligere konfigurationsposter er blevet tilføjet.

```
    `<appSettings>
        <add key="ida:Password" value="Your Azure AD App's new password" />
    </appSettings>`
```

###<a name="your-azure-active-directory-app-was-updated"></a>Din Azure Active Directory App blev opdateret
Din Azure Active Directory App blev opdateret for at medtage tilladelsen *læse directory data* og en ekstra nøgle blev oprettet, blev derefter bruges som *Ida: adgangskode* i den `web.config` fil.

[Lær mere om Azure Active Directory](https://azure.microsoft.com/services/active-directory/)
