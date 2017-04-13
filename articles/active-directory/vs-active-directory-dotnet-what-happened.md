<properties
    pageTitle="Hvad skete der med mine MVC projekt (Visual Studio Azure Active Directory forbindelse service) | Microsoft Azure "
    description="I denne artikel beskrives, hvad sker der med projektet MVC, når du opretter forbindelse til Azure AD ved hjælp af Visual Studio forbundne tjenester"
    services="active-directory"
    documentationCenter="na"
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

# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Hvad skete der med mine MVC projekt (Visual Studio Azure Active Directory forbindelse service)?

> [AZURE.SELECTOR]
> - [Kom godt i gang](vs-active-directory-dotnet-getting-started.md)
> - [Hvad skete der](vs-active-directory-dotnet-what-happened.md)



## <a name="references-have-been-added"></a>Referencer er blevet tilføjet

### <a name="nuget-package-references"></a>NuGet pakke referencer

- **Microsoft.IdentityModel.Protocol.Extensions**
- **Microsoft.Owin**
- **Microsoft.Owin.Host.SystemWeb**
- **Microsoft.Owin.Security**
- **Microsoft.Owin.Security.Cookies**
- **Microsoft.Owin.Security.OpenIdConnect**
- **Owin**
- **System.IdentityModel.Tokens.Jwt**

### <a name="net-references"></a>.NET referencer

- **Microsoft.IdentityModel.Protocol.Extensions**
- **Microsoft.Owin**
- **Microsoft.Owin.Host.SystemWeb**
- **Microsoft.Owin.Security**
- **Microsoft.Owin.Security.Cookies**
- **Microsoft.Owin.Security.OpenIdConnect**
- **Owin**
- **System.IdentityModel**
- **System.IdentityModel.Tokens.Jwt**
- **Afsnitsgruppen**

## <a name="code-has-been-added"></a>Koden er blevet tilføjet

### <a name="code-files-were-added-to-your-project"></a>Kodefiler er blevet føjet til projektet

En godkendelse Start klasse **App_Start/Startup.Auth.cs** blev føjet til dit projekt, der indeholder start logik til Azure AD-godkendelse. Controller klassen Controllers/AccountController.cs blev tilføjes også, som indeholder **SignIn()** og **SignOut()** metoder. Til sidst en delvis visning **Views/Shared/_LoginPartial.cshtml** blev føjet, der indeholder et handlingslink af typen til logon/logge af.

### <a name="startup-code-was-added-to-your-project"></a>Start kode blev føjet til projektet

Hvis du allerede har en start-klasse i dit projekt skal blev metoden **konfiguration** opdateret med et opkald til **ConfigureAuth(app)**. Ellers kan blev en start-klasse føjet til projektet.

### <a name="your-appconfig-or-webconfig-has-new-configuration-values"></a>Din app.config eller web.config har nye konfigurationsværdier

Følgende konfigurationsposter er blevet tilføjet.


    <appSettings>
        <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="The selected Azure AD Domain" />
        <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
        <add key="ida:PostLogoutRedirectUri" value="Your project start page" />
    </appSettings>

### <a name="an-azure-active-directory-ad-app-was-created"></a>En Azure Active Directory (AD) App blev oprettet
Et Azure AD-program blev oprettet i den mappe, du har valgt i guiden.

##<a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>Hvis jeg markeret *deaktivere individuelle brugerkonti godkendelse*, hvilke yderligere ændringer der er foretaget på mit projekt?
NuGet pakke referencer er blevet fjernet, og filer er blevet fjernet og sikkerhedskopieret. Afhængigt af tilstanden for dit projekt, kan være du nødvendigt at fjerne flere referencer eller filer manuelt eller redigere koden efter behov.

### <a name="nuget-package-references-removed-for-those-present"></a>NuGet pakke referencer fjernes (for de findes)

- **Microsoft.AspNet.Identity.Core**
- **Microsoft.AspNet.Identity.EntityFramework**
- **Microsoft.AspNet.Identity.Owin**

### <a name="code-files-backed-up-and-removed-for-those-present"></a>Kodefiler sikkerhedskopieret og fjernet (for de findes)

Hver af følgende filer blev sikkerhedskopieret og fjernet fra projektet. Sikkerhedskopifilerne er placeret i en ' sikkerhedskopimappe ' i roden af projektets directory.

- **App_Start\IdentityConfig.CS**
- **Controllers\ManageController.CS**
- **Models\IdentityModels.CS**
- **Models\ManageViewModels.CS**

### <a name="code-files-backed-up-for-those-present"></a>Kodefiler,, der er sikkerhedskopieret (for de findes)

Hver af følgende filer blev sikkerhedskopieret før skal erstattes. Sikkerhedskopifilerne er placeret i en ' sikkerhedskopimappe ' i roden af projektets directory.

- **Startup.CS**
- **App_Start\Startup.auth.CS**
- **Controllers\AccountController.CS**
- **Views\Shared\_LoginPartial.cshtml**

## <a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>Hvis jeg markeret *læse directory data*, hvilke yderligere ændringer der er foretaget på mit projekt?

Yderligere referencer er blevet tilføjet.

###<a name="additional-nuget-package-references"></a>Yderligere NuGet pakke referencer

- **EntityFramework**
- **Microsoft.Azure.ActiveDirectory.GraphClient**
- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.IdentityModel.Clients.ActiveDirectory**
- **System.Spatial**

###<a name="additional-net-references"></a>Yderligere .NET referencer

- **EntityFramework**
- **EntityFramework.SqlServer**
- **Microsoft.Azure.ActiveDirectory.GraphClient**
- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.IdentityModel.Clients.ActiveDirectory**
- **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms**
- **System.Spatial**

###<a name="additional-code-files-were-added-to-your-project"></a>Yderligere kodefiler er blevet føjet til projektet

To filer er blevet tilføjet for at understøtte token cachelagring: **Models\ADALTokenCache.cs** og **Models\ApplicationDbContext.cs**.  En ekstra controller og få vist blev føjet til at illustrere adgang til brugerprofiloplysninger ved hjælp af Azure graph API'er.  Disse filer er **Controllers\UserProfileController.cs** og **Views\UserProfile\Index.cshtml**.

###<a name="additional-startup-code-was-added-to-your-project"></a>Yderligere Start kode blev føjet til projektet

Et nyt **OpenIdConnectAuthenticationNotifications** objekt blev føjet til medlemmet **beskeder** i **OpenIdConnectAuthenticationOptions**i filen **startup.auth.cs** .  Dette er at aktivere modtager OAuth-kode og exchange til et adgangstoken.

###<a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>Yderligere ændringer foretaget til app.config eller web.config

Følgende yderligere konfigurationsposter er blevet tilføjet.

    <appSettings>
        <add key="ida:ClientSecret" value="Your Azure AD App's new client secret" />
    </appSettings>

Konfiguration af de følgende afsnit og forbindelsesstreng er blevet tilføjet.

    <configSections>
        <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>
    <connectionStrings>
        <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-[AppName + Generated Id].mdf;Initial Catalog=aspnet-[AppName + Generated Id];Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>
    <entityFramework>
        <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
          <parameters>
            <parameter value="mssqllocaldb" />
          </parameters>
        </defaultConnectionFactory>
        <providers>
          <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
        </providers>
    </entityFramework>


###<a name="your-azure-active-directory-app-was-updated"></a>Din Azure Active Directory App blev opdateret
Din Azure Active Directory App blev opdateret for at medtage tilladelsen *læse directory data* og en ekstra nøgle blev oprettet, blev derefter bruges som *ida: ClientSecret* i filen **web.config** .

[Lær mere om Azure Active Directory](https://azure.microsoft.com/services/active-directory/)
