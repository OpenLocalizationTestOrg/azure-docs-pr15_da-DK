<properties
    pageTitle="Introduktion til Azure CDN biblioteket til .NET | Microsoft Azure"
    description="Lær, hvordan du kan skrive .NET-programmer til at administrere Azure CDN ved hjælp af Visual Studio."
    services="cdn"
    documentationCenter=".net"
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/15/2016"
    ms.author="casoper"/>

# <a name="get-started-with-azure-cdn-development"></a>Introduktion til Azure CDN udvikling

> [AZURE.SELECTOR]
- [Node.js](cdn-app-dev-node.md)
- [.NET](cdn-app-dev-net.md)

Du kan bruge [Azure CDN bibliotek til .NET](https://msdn.microsoft.com/library/mt657769.aspx) til at automatisere oprettelse og administration af CDN profiler og slutpunkter.  Dette selvstudium indeholder en gennemgang oprettelse af et enkelt .NET console-program, der viser flere af de tilgængelige handlinger.  Dette selvstudium er ikke beregnet til at beskrive alle aspekter af biblioteket Azure CDN til .NET detaljeret.

Du skal bruge Visual Studio-2015 til at udføre dette selvstudium.  [Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) er frit tilgængelig til hentning.

> [AZURE.TIP] Den [fuldførte projektet fra dette selvstudium](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) kan hentes på MSDN.

[AZURE.INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>Oprette projektet og tilføje Nuget-pakker

Nu hvor vi har oprettet en ressourcegruppe til vores CDN profiler og har vores Azure AD-program tilladelse til at administrere CDN profiler og slutpunkter i den pågældende gruppe, kan vi begynde at oprette vores program.

Klik på **filer**, **Ny** **Project …** for at åbne dialogboksen nyt projekt i Visual Studio-2015, fra.  Udvid **Visual C#**og derefter vælge **Windows** i ruden til venstre.  Klik på **Console program** i den midterste rude.  Navngive dit projekt, og klik derefter på **OK**.  

![Nyt projekt](./media/cdn-app-dev-net/cdn-new-project.png)

Vores project skal bruge nogle Azure biblioteker, der er indeholdt i Nuget pakker.  Lad os føje dem til projektet.

1. Klik på menuen **Funktioner** , **Nuget Package Manager**og derefter **Pakke Manager-konsollen**.

    ![Administrere Nuget-pakker](./media/cdn-app-dev-net/cdn-manage-nuget.png)

2. Udfør følgende kommando for at installere **Active Directory Authentication Library (ADAL)**i pakke Manager-konsollen:

    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`

3. Udfør følgende for at installere **Azure CDN Management bibliotek**:

    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>Direktiver, konstanter, metoden main og hjælper metoder

Lad os få grundlæggende strukturen i vores program, der er skrevet.

1. Tilbage i fanen Program.cs erstatte den `using` direktiver øverst med følgende:

    ```csharp
    using System;
    using System.Collections.Generic;
    using Microsoft.Azure.Management.Cdn;
    using Microsoft.Azure.Management.Cdn.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

2. Vi vil definere nogle vores metoder anvender konstanter.  I den `Program` klasse, men før den `Main` metode kan du tilføje følgende.  Sørg for at erstatte de pladsholdere, herunder den ** &lt;vinklede parenteser&gt;**, med dine egne værdier efter behov.

    ```csharp
    //Tenant app constants
    private const string clientID = "<YOUR CLIENT ID>";
    private const string clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    private const string authority = "https://login.microsoftonline.com/<YOUR TENANT ID>/<YOUR TENANT DOMAIN NAME>";

    //Application constants
    private const string subscriptionId = "<YOUR SUBSCRIPTION ID>";
    private const string profileName = "CdnConsoleApp";
    private const string endpointName = "<A UNIQUE NAME FOR YOUR CDN ENDPOINT>";
    private const string resourceGroupName = "CdnConsoleTutorial";
    private const string resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```

3. Definere også disse to variabler på niveauet klasse.  Vi bruger disse senere til at bestemme, hvis vores profil- og slutpunkt allerede findes.

    ```csharp
    static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
    ```

4.  Erstatte den `Main` metode på følgende måde:

    ```csharp
    static void Main(string[] args)
    {
        //Get a token
        AuthenticationResult authResult = GetAccessToken();

        // Create CDN client
        CdnManagementClient cdn = new CdnManagementClient(new TokenCredentials(authResult.AccessToken))
            { SubscriptionId = subscriptionId };

        ListProfilesAndEndpoints(cdn);

        // Create CDN Profile
        CreateCdnProfile(cdn);

        // Create CDN Endpoint
        CreateCdnEndpoint(cdn);
        
        Console.WriteLine();

        // Purge CDN Endpoint
        PromptPurgeCdnEndpoint(cdn);

        // Delete CDN Endpoint
        PromptDeleteCdnEndpoint(cdn);

        // Delete CDN Profile
        PromptDeleteCdnProfile(cdn);

        Console.WriteLine("Press Enter to end program.");
        Console.ReadLine();
    }
    ```

5. Nogle af vores andre metoder går at spørge brugeren med "Ja/Nej" spørgsmål.  Tilføj følgende metode for at gøre, lidt nemmere:

    ```csharp
    private static bool PromptUser(string Question)
    {
        Console.Write(Question + " (Y/N): ");
        var response = Console.ReadKey();
        Console.WriteLine();
        if (response.Key == ConsoleKey.Y)
        {
            return true;
        }
        else if (response.Key == ConsoleKey.N)
        {
            return false;
        }
        else
        {
            // They pressed something other than Y or N.  Let's ask them again.
            return PromptUser(Question);
        }
    }
    ```

Nu, hvor den grundlæggende struktur i vores program er skrevet, skal vi oprette metoderne den `Main` metode.

## <a name="authentication"></a>Godkendelse

Før vi kan bruge biblioteket Azure CDN administration, har vi brug at godkende vores service hovedstolen og få et godkendelse token.  Denne metode bruger ADAL til at hente tokenet.

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority); 
    ClientCredential credential = new ClientCredential(clientID, clientSecret);
    AuthenticationResult authResult = 
        authContext.AcquireTokenAsync("https://management.core.windows.net/", credential).Result;

    return authResult;
}
```

Hvis du bruger individuelle brugergodkendelse i `GetAccessToken` metode ser lidt anderledes.

>[AZURE.IMPORTANT] Brug kun denne eksempel-kode, hvis du vælger at have individuelle brugergodkendelse i stedet for en tjeneste vigtigste.

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    AuthenticationResult authResult = authContext.AcquireTokenAsync("https://management.core.windows.net/",
        clientID, new Uri("http://<redirect URI>"), new PlatformParameters(PromptBehavior.RefreshSession)).Result;

    return authResult;
}
```

Sørg for at erstatte `<redirect URI>` med Omdiriger URI, du har angivet, når du har registreret programmet i Azure AD.

## <a name="list-cdn-profiles-and-endpoints"></a>Listen CDN profiler og slutpunkter

Vi er nu klar til at udføre CDN handlinger.  Det første vores metode fungerer er liste over alle de profiler og slutpunkter i vores ressourcegruppe, og hvis der er angivet en match for profil og slutpunkt navnene, der er angivet i vores konstanter, du gør en note i den pågældende til senere, så vi ikke forsøger at oprette dubletter.

```csharp
private static void ListProfilesAndEndpoints(CdnManagementClient cdn)
{
    // List all the CDN profiles in this resource group
    var profileList = cdn.Profiles.ListByResourceGroup(resourceGroupName);
    foreach (Profile p in profileList)
    {
        Console.WriteLine("CDN profile {0}", p.Name);
        if (p.Name.Equals(profileName, StringComparison.OrdinalIgnoreCase))
        {
            // Hey, that's the name of the CDN profile we want to create!
            profileAlreadyExists = true;
        }

        //List all the CDN endpoints on this CDN profile
        Console.WriteLine("Endpoints:");
        var endpointList = cdn.Endpoints.ListByProfile(p.Name, resourceGroupName);
        foreach (Endpoint e in endpointList)
        {
            Console.WriteLine("-{0} ({1})", e.Name, e.HostName);
            if (e.Name.Equals(endpointName, StringComparison.OrdinalIgnoreCase))
            {
                // The unique endpoint name already exists.
                endpointAlreadyExists = true;
            }
        }
        Console.WriteLine();
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Oprette CDN profiler og slutpunkter

Dernæst skal oprette vi en profil.

```csharp
private static void CreateCdnProfile(CdnManagementClient cdn)
{
    if (profileAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating profile {0}.", profileName);
        ProfileCreateParameters profileParms =
            new ProfileCreateParameters() { Location = resourceLocation, Sku = new Sku(SkuName.StandardVerizon) };
        cdn.Profiles.Create(profileName, profileParms, resourceGroupName);
    }
}
```

Når profilen, der er oprettet, skal vi oprette et slutpunkt.

```csharp
private static void CreateCdnEndpoint(CdnManagementClient cdn)
{
    if (endpointAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating endpoint {0} on profile {1}.", endpointName, profileName);
        EndpointCreateParameters endpointParms =
            new EndpointCreateParameters()
            {
                Origins = new List<DeepCreatedOrigin>() { new DeepCreatedOrigin("Contoso", "www.contoso.com") },
                IsHttpAllowed = true,
                IsHttpsAllowed = true,
                Location = resourceLocation
            };
        cdn.Endpoints.Create(endpointName, endpointParms, profileName, resourceGroupName);
    }
}
```

>[AZURE.NOTE] Ovenstående eksempel tildeles slutpunktet en origin med navnet *Contoso* med en hostname `www.contoso.com`.  Du skal ændre denne indstilling for at pege på din egen origin hostname.

## <a name="purge-an-endpoint"></a>Slette et slutpunkt

Hvis slutpunktet er blevet oprettet, er en almindelig opgave, som vi muligvis vil udføre i vores program til sletning indholdet i vores slutpunkt.

```csharp
private static void PromptPurgeCdnEndpoint(CdnManagementClient cdn)
{
    if (PromptUser(String.Format("Purge CDN endpoint {0}?", endpointName)))
    {
        Console.WriteLine("Purging endpoint. Please wait...");
        cdn.Endpoints.PurgeContent(endpointName, profileName, resourceGroupName, new List<string>() { "/*" });
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

>[AZURE.NOTE] I eksemplet ovenfor, strengen `/*` angiver, at jeg vil fjerne alt i roden af slutpunkt stien.  Dette er svarer til kontrol af **Fjern alle** i portalen Azure "Fjern" dialogboksen. I den `CreateCdnProfile` metode, at jeg har oprettet vores profil som **Azure CDN fra Verizon** profil ved hjælp af koden `Sku = new Sku(SkuName.StandardVerizon)`, så det bliver vellykket.  Dog **Azure CDN fra Akamai** profiler ikke understøtter **Fjern alle**, så jeg har brugt af en Akamai profil i dette selvstudium, jeg har brug at medtage bestemte stier at tømme.

## <a name="delete-cdn-profiles-and-endpoints"></a>Slette CDN profiler og slutpunkter

De sidste metoder, slettes vores slutpunkt og profil.

```csharp
private static void PromptDeleteCdnEndpoint(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN endpoint {0} on profile {1}?", endpointName, profileName)))
    {
        Console.WriteLine("Deleting endpoint. Please wait...");
        cdn.Endpoints.DeleteIfExists(endpointName, profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}

private static void PromptDeleteCdnProfile(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN profile {0}?", profileName)))
    {
        Console.WriteLine("Deleting profile. Please wait...");
        cdn.Profiles.DeleteIfExists(profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

## <a name="running-the-program"></a>Køre programmet

Vi kan nu samle og køre programmet ved at klikke på knappen **Start** i Visual Studio.

![Program, der kører](./media/cdn-app-dev-net/cdn-program-running-1.png)

Når programmet når spørgsmålet ovenfor, bør du kunne vende tilbage til din ressourcegruppe i portalen Azure og se, at profilen er blevet oprettet.

![Succes!](./media/cdn-app-dev-net/cdn-success.png)

Vi kan derefter bekræfte instruktionerne til at køre resten af programmet.

![Gennemførelse af program](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>Næste trin

For at se det færdige projekt fra denne gennemgang, [hente stikprøvernes](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

Få vist [reference på MSDN](https://msdn.microsoft.com/library/mt657769.aspx)for at finde yderligere dokumentation på biblioteket Azure CDN administration til .NET.

Administrere dine CDN ressourcer med [PowerShell](./cdn-manage-powershell.md).


