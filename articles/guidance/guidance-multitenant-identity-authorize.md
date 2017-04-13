<properties
   pageTitle="Godkendelse i multiprofiler programmer | Microsoft Azure"
   description="Hvordan du udfører godkendelse i et multiprofiler til computeren"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/02/2016"
   ms.author="mwasson"/>

# <a name="role-based-and-resource-based-authorization-in-multitenant-applications"></a>Rollebaseret og ressource-baseret godkendelse i multiprofiler programmer

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

I denne artikel er [en del af en række]. Der er også en komplet [Northwind] , der følger med denne serie.

Vores [referenceimplementering] er en ASP.NET Core 1.0-program. I denne artikel vil vi se på to generelle tilgange til godkendelse, ved hjælp af godkendelse API'er fremgår ASP.NET Core 1.0.

-   **Rollebaseret godkendelse**. Godkendelse af en handling, der er baseret på de roller, der er tildelt til en bruger. For eksempel kræver nogle handlinger en administratorrolle.
-   **Ressource-baseret godkendelse**. Godkendelse af en handling, der er baseret på en bestemt ressource. For eksempel har hver ressource en ejer. Ejeren kan slette ressourcen. andre brugere kan ikke.

En typisk app anvender en blanding af begge. Eksempelvis hvis du vil slette en ressource, skal brugeren være ressource ejer _eller_ administrator.


## <a name="role-based-authorization"></a>Rollebaseret godkendelse

[Hold undersøgelser] [ Tailspin] program definerer følgende roller:

- Administrator. Kan udføre alle CRUD-handlinger på en undersøgelse, der hører til den pågældende lejer.
- Creator. Kan oprette nye undersøgelser
- Læser. Kan læse en hvilken som helst undersøgelser, der hører til den pågældende lejer

Roller gælder for _brugere_ af programmet. I programmet undersøgelser er en bruger enten en administrator, creator eller reader.

Du kan finde en beskrivelse af hvordan du kan definere og administrere roller finder [programmet roller].

Uanset hvordan du administrerer rollerne, skal ligner din Godkendelseskode. ASP.NET Core 1.0 introducerer en fremstilling kaldet [godkendelse politikker][policies]. Med denne funktion, du definerer godkendelse politikker kode og derefter anvende disse politikker controller handlinger. Politikken er frakoblet fra controlleren.

### <a name="create-policies"></a>Oprette politikker

Hvis du vil definere en politik, først oprette en klasse, der implementerer `IAuthorizationRequirement`. Det er lettest at afledt af `AuthorizationHandler`. I den `Handle` metode, undersøge den relevante claim(s).

Her er et eksempel fra programmet Hold undersøgelser:

```csharp
public class SurveyCreatorRequirement : AuthorizationHandler<SurveyCreatorRequirement>, IAuthorizationRequirement
{
    protected override void Handle(AuthorizationContext context, SurveyCreatorRequirement requirement)
    {
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin) ||
            context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            context.Succeed(requirement);
        }
    }
}
```

> [AZURE.NOTE] Se [SurveyCreatorRequirement.cs]

Denne klasse definerer kravet om en bruger til at oprette en ny undersøgelse. Brugeren skal være i rollen SurveyAdmin eller SurveyCreator.

Definere et navngivet politik, der indeholder en eller flere krav i klasse Start. Hvis der er flere krav, skal brugeren opfylder _alle_ krav skal godkendes. Følgende kode definerer to politikker:

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy(PolicyNames.RequireSurveyCreator,
        policy =>
        {
            policy.AddRequirements(new SurveyCreatorRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });

    options.AddPolicy(PolicyNames.RequireSurveyAdmin,
        policy =>
        {
            policy.AddRequirements(new SurveyAdminRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });
});
```

> [AZURE.NOTE] Se [Startup.cs]

Denne kode angiver også godkendelse farveskema, som fortæller ASP.NET hvilke godkendelse programmer skal køre, hvis godkendelse mislykkes. I dette tilfælde angive vi cookie godkendelse program, da cookie godkendelse programmer kan omdirigere brugeren til en "Forbudt"-side. Placeringen af siden forbudt er angivet i indstillingen AccessDeniedPath for cookie programmer; Se [konfigurere godkendelse-programmer].

### <a name="authorize-controller-actions"></a>Godkend controller handlinger

Endelig, hvis du vil godkende en handling i en MVC controller, indstiller politikken den `Authorize` attribut:

```csharp
[Authorize(Policy = "SurveyCreatorRequirement")]
public IActionResult Create()
{
    // ...
}
```

I tidligere versioner af ASP.NET, skal du angive egenskaben **roller** på attributten:

```csharp
// old way
[Authorize(Roles = "SurveyCreator")]

```

Dette understøttes stadig i ASP.NET Core 1.0, men der er visse ulemper sammenlignet med godkendelse politikker:

-   Det antages en bestemt kravtype. Politikker kan søge efter en hvilken som helst kravtype. Roller er bare en type af krav.
-   Rollenavnet på er kopieret direkte ind attributten. Med politikker for er godkendelse logik samlet på ét sted, gør det nemmere at opdatere eller lige Indlæs fra indstillinger for søgekonfiguration.
-   Politikker aktivere mere komplekse godkendelse beslutninger (f.eks., handlingerne alder > = 21), der ikke kan udtrykkes af enkel rollemedlemskab.

## <a name="resource-based-authorization"></a>Ressource baseret godkendelse

_Ressource baseret godkendelse_ opstår, når tilladelsen afhænger af en bestemt ressource, der påvirkes af handlingen. I programmet på hold undersøgelser har hver undersøgelse en ejer og nul-til-mange bidragydere.

-   Ejeren kan læse, opdatere, slette, publicere eller annullere publiceringen af undersøgelsen.
-   Ejeren kan tildele bidragydere til undersøgelsen.
-   Bidragydere kan læse og opdatere undersøgelsen.

Bemærk, at "ejer" og "bidragyder" ikke programmet roller; de er gemt i undersøgelsen, i databasen. For at kontrollere, om en bruger kan slette en undersøgelse, for eksempel kontrollerer appen, om brugeren er ejer af undersøgelsen.

Implementere ressource-baseret godkendelse ved afledt af **AuthorizationHandler** og tilsidesætte metoden **håndtere** ASP.NET Core 1.0.

```csharp
public class SurveyAuthorizationHandler : AuthorizationHandler<OperationAuthorizationRequirement, Survey>
{
     protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
    {
    }
}
```

Bemærk, at klassen kraftigt skrevet for undersøgelse objekter.  Registrere klassen for DI ved start:

```csharp
services.AddSingleton<IAuthorizationHandler>(factory =>
{
    return new SurveyAuthorizationHandler();
});
```

Brug den **IAuthorizationService** brugergrænseflade, som du kan indsætte i din enheder til at udføre godkendelse trykkeri. Følgende kode kontrollerer, om en bruger kan læse en undersøgelse:

```csharp
if (await _authorizationService.AuthorizeAsync(User, survey, Operations.Read) == false)
{
    return new HttpStatusCodeResult(403);
}
```

Da vi sender i en `Survey` objekt, dette opkald skal aktivere den `SurveyAuthorizationHandler`.

I din godkendelseskode er en god metode til samling alle brugerens rollebaseret og ressource-baserede tilladelser, skal du kontrollere, angive et aggregat forhold til den ønskede handling.
Her er et eksempel fra undersøgelser app. Programmet definerer flere Tilladelsestyper:

- Administrator
- Bidragyder
- Creator
- Ejer
- Reader

Programmet definerer også en række mulige handlinger på undersøgelser:

- Oprette
- Læs
- Opdater
- Slet
- Publicere
- Unpublsh

Følgende kode opretter en liste over tilladelser for en bestemt bruger og undersøgelse. Bemærk, at denne kode kigger på både brugerens app roller og felterne ejer/bidragyder i undersøgelsen.

```csharp
protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
{
    var permissions = new List<UserPermissionType>();
    string userTenantId = context.User.GetTenantIdValue();
    int userId = ClaimsPrincipalExtensions.GetUserKey(context.User);
    string user = context.User.GetUserName();

    if (resource.TenantId == userTenantId)
    {
        // Admin can do anything, as long as the resource belongs to the admin's tenant.
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin))
        {
            context.Succeed(operation);
            return;
        }

        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            permissions.Add(UserPermissionType.Creator);
        }
        else
        {
            permissions.Add(UserPermissionType.Reader);
        }

        if (resource.OwnerId == userId)
        {
            permissions.Add(UserPermissionType.Owner);
        }
    }
    if (resource.Contributors != null && resource.Contributors.Any(x => x.UserId == userId))
    {
        permissions.Add(UserPermissionType.Contributor);
    }
    if (ValidateUserPermissions[operation](permissions))
    {
        context.Succeed(operation);
    }
}
```

> [AZURE.NOTE] Se [SurveyAuthorizationHandler.cs].

I et med flere arkitekturer til computeren, skal du kontrollere, at tilladelser ikke "lækkes" til en anden leje data. I app undersøgelser tilladelsen bidragyder er tilladt på tværs af lejere &mdash; kan du tildele en person fra en anden lejer som en contriubutor. De andre Tilladelsestyper er begrænset til ressourcer, der hører til den pågældende brugers lejer. Hvis du vil gennemtvinge dette krav, kontrollerer koden lejer ID før tilladelsen. (Den `TenantId` felt, som tildeles når undersøgelsen oprettes.)

Næste trin er at kontrollere handlingen (læse, opdatere, Slet osv.) med tilladelser. Appen undersøgelser implementerer dette trin ved hjælp af en opslagstabel af funktioner:

```csharp
static readonly Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>> ValidateUserPermissions
    = new Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>>

    {
        { Operations.Create, x => x.Contains(UserPermissionType.Creator) },

        { Operations.Read, x => x.Contains(UserPermissionType.Creator) ||
                                x.Contains(UserPermissionType.Reader) ||
                                x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Update, x => x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Delete, x => x.Contains(UserPermissionType.Owner) },

        { Operations.Publish, x => x.Contains(UserPermissionType.Owner) },

        { Operations.UnPublish, x => x.Contains(UserPermissionType.Owner) }
    };
```


## <a name="next-steps"></a>Næste trin

- Læs næste artikel i denne serie: [sikring af en back-end web API i et multiprofiler til computeren][web-api]
- Hvis du vil vide mere om ressource baseret godkendelse i ASP.NET 1.0 Core, se [Ressource baseret godkendelse][rbac].

<!-- Links -->
[Tailspin]: guidance-multitenant-identity-tailspin.md
[en del af en serie]: guidance-multitenant-identity.md
[Programmet roller]: guidance-multitenant-identity-app-roles.md
[policies]: https://docs.asp.net/en/latest/security/authorization/policies.html
[rbac]: https://docs.asp.net/en/latest/security/authorization/resourcebased.html
[referenceimplementering]: guidance-multitenant-identity-tailspin.md
[SurveyCreatorRequirement.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyCreatorRequirement.cs
[Startup.CS]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs
[Konfigurere godkendelse-programmer]: guidance-multitenant-identity-authenticate.md#configuring-the-authentication-middleware
[SurveyAuthorizationHandler.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyAuthorizationHandler.cs
[Northwind]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
[web-api]: guidance-multitenant-identity-web-api.md
