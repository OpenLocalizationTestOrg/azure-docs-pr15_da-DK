<properties
    pageTitle="Bruge Azure vigtige samling fra et webprogram | Microsoft Azure"
    description="Brug dette selvstudium til at hjælpe dig med at lære at bruge Azure-tasten samling fra et webprogram."
    services="key-vault"
    documentationCenter=""
    authors="adhurwit"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="adhurwit"/>

# <a name="use-azure-key-vault-from-a-web-application"></a>Bruge Azure vigtige samling fra et webprogram #

## <a name="introduction"></a>Introduktion  
Brug dette selvstudium til at hjælpe dig med at lære at bruge Azure-tasten samling fra et webprogram i Azure. Det vejleder dig gennem processen med at få adgang til et hemmeligt fra samling en Azure-tasten, så den kan bruges i dit webprogram.

**Anslået tid at gennemføre:** 15 minutter


Oversigt oversigtsoplysninger om Azure-tasten samling, du [Hvad er Azure-tasten samling?](key-vault-whatis.md)

## <a name="prerequisites"></a>Forudsætninger

For at fuldføre dette selvstudium, skal du have følgende:

- En URI til et hemmeligt i en Azure-tasten samling
- En klient-ID og en klient hemmeligt for et webprogram, der er registreret med Azure Active Directory, der har adgang til din nøgle samling
- Et webprogram. Vi viser trinnene til et ASP.NET MVC-program, der er implementeret i Azure som en Web App.

> [AZURE.NOTE]  Det er vigtigt, at du har fuldført de trin, der er angivet i [Introduktion til Azure nøgle samling](key-vault-get-started.md) i dette selvstudium, så du har URI til et hemmeligt og klient-ID og klienten hemmeligt for et webprogram.

Det webprogram, der får adgang til den tast samling af legitimationsoplysninger er det, der er registreret i Azure Active Directory og har fået adgang til din nøgle samling. Hvis dette ikke er tilfældet, gå tilbage til Registrer et program i selvstudiet komme i gang og gentage trinnene vises.

Dette selvstudium er udviklet til webudviklere, forstår de grundlæggende regler for oprettelse af webprogrammer på Azure. Se [Oversigt over Web Apps](../app-service-web/app-service-web-overview.md)kan finde flere oplysninger om Azure Web Apps.



## <a id="packages"></a>Tilføje Nuget-pakker ##
Der findes to pakker, som dit webprogram skal har installeret.

- Active Directory Authentication Library - indeholder metoder til at arbejde med Azure Active Directory og administrere bruger-id
- Azure nøgle samling biblioteket - indeholder metoder til at arbejde med Azure-tasten samling


Begge af disse pakker kan installeres ved hjælp af konsollen Package Manager ved hjælp af kommandoen Installer-pakke.

    // this is currently the latest stable version of ADAL
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

    Install-Package Microsoft.Azure.KeyVault


## <a id="webconfig"></a>Ændre Web.Config ##
Der findes tre programindstillinger, som skal tilføjes til web.config-filen på følgende måde.

    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />


Hvis du ikke vil være vært for dit program som en Azure Web App, skal du føje de faktiske ClientId, klienten hemmeligt og hemmeligt URI værdier til web.config. Ellers lade disse dummy værdier, fordi vi vil tilføje de faktiske værdier i portalen Azure for et ekstra niveau af sikkerhed.


## <a id="gettoken"></a>Tilføje metode til at få en adgang Token ##
Hvis du vil bruge tasten samling API, du har brug for et adgangstoken. Tasten samling klienten håndterer opkald til tasten samling API, men du skal angive den med en funktion, der henter adgangstoken.  

Følgende er koden for at få en adgang token fra Azure Active Directory. Denne kode kan gå et vilkårligt sted i dit program. Jeg kan lide at tilføje en af websted eller EncryptionHelper klasse.  

    //add these using statements
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System.Threading.Tasks;
    using System.Web.Configuration;

    //this is an optional property to hold the secret after it is retrieved
    public static string EncryptSecret { get; set; }

    //the method that will be provided to the KeyVaultClient
    public static async Task<string> GetToken(string authority, string resource, string scope)
    {
        var authContext = new AuthenticationContext(authority);
        ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                    WebConfigurationManager.AppSettings["ClientSecret"]);
        AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

        if (result == null)
            throw new InvalidOperationException("Failed to obtain the JWT token");

        return result.AccessToken;
    }

> [AZURE.NOTE] 
> Brug af en klient-ID og klienten hemmeligt er den nemmeste måde at godkende et Azure AD-program. Og brug af det i dit webprogram giver mulighed for en adskillelse af opgaver og mere kontrol over dine key management. Men det afhængige af hvordan man anbringer klient hemmeligt i dine konfigurationsindstillinger, der kan være så risikabel som lægge hemmeligheden, du vil beskytte i dine konfigurationsindstillinger for nogle. Se nedenstående diskussion om, hvordan du bruger en klient-ID og certifikat i stedet for klient-ID og klienten hemmeligt til at godkende Azure AD-programmet.



## <a id="appstart"></a>Hente hemmeligt på programmet starter ##
Nu skal vi kode til at ringe API'EN nøgle samling af legitimationsoplysninger og hente hemmeligheden. Følgende kode kan placere et vilkårligt sted, så længe den hedder, før du skal bruge den. Jeg har indsat denne kode i hændelsen programmet starter i Global.asax, så den kører én gang på start, og gør hemmeligheden tilgængelig for programmet.

    //add these using statements
    using Microsoft.Azure.KeyVault;
    using System.Web.Configuration;

    // I put my GetToken method in a Utils class. Change for wherever you placed your method.
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

    var sec = kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]).Result.Value;

    //I put a variable in a Utils class to hold the secret for general  application use.
    Utils.EncryptSecret = sec;



## <a id="portalsettings"></a>Tilføj App indstillinger på portalen Azure (valgfrit) ##
Hvis du har en Azure Web App kan du nu føje de faktiske værdier for AppSettings i portalen Azure. De faktiske værdier er ikke i web.config ved at gøre dette, men beskyttet via portalen, hvor du har separate adgang control-funktioner. De værdier, du har angivet i filen web.config der erstatter disse værdier. Sørg for, at navnene er den samme.

![Indstillinger for program vises i Azure-portalen][1]


## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>Godkende med et certifikat i stedet for en klient hemmeligt
Der er en anden måde at godkende et Azure AD-program ved hjælp af en klient-ID og et certifikat i stedet for en klient-ID og klienten hemmeligt. Følger disse trin for at bruge et certifikat i en Azure Web App:

1. Hente eller oprette et certifikat
2. Knytte certifikatet til et Azure AD-program
3. Føj kode til din online til at bruge certifikatet
4. Føje et certifikat til din online


**Hente eller oprette et certifikat** Vi vil gøre et testcertifikat til vores formål. Her er nogle af kommandoer, som du kan bruge i en udvikler kommandoprompt til at oprette et certifikat. Skift mappe til det ønskede cert filerne skal oprettes.

    makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 07/31/2015 -e 07/31/2016 -r
    pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123

Noter slutdatoen og adgangskoden til .pfx (i dette eksempel: 07/31/2016 og test123). Du skal bruge dem nedenfor.

Finde flere oplysninger om oprettelse af et testcertifikat [Sådan: Opret din egen teste certifikat](https://msdn.microsoft.com/library/ff699202.aspx)


**Knytte certifikatet med et Azure AD-program** Nu hvor du har et certifikat, skal du knytte den til et Azure AD-program. Men portalen Azure Management understøtter ikke dette lige nu. Du har i stedet bruge Powershell. Her er de kommandoer, der skal du køre:

    $x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2

    PS C:\> $x509.Import("C:\data\KVWebApp.cer")

    PS C:\> $credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())

    PS C:\> $now = [System.DateTime]::Now

    # this is where the end date from the cert above is used
    PS C:\> $yearfromnow = [System.DateTime]::Parse("2016-07-31")

    PS C:\> $adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -KeyValue $credValue -KeyType "AsymmetricX509Cert" -KeyUsage "Verify" -StartDate $now -EndDate $yearfromnow

    PS C:\> $sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId

    PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName $sp.ServicePrincipalName -PermissionsToSecrets all -ResourceGroupName 'contosorg'

    # get the thumbprint to use in your app settings
    PS C:\>$x509.Thumbprint

Når du har kørt disse kommandoer, kan du se programmet i Azure AD. Hvis du ikke kan se programmet første, søge efter "Programmer firmaet ejer" i stedet for "Programmer firmaet bruger".

Hvis du vil vide mere om Azure AD-program og ServicePrincipal objekter skal du se [program og Service hovedstolen objekter](../active-directory/active-directory-application-objects.md)



**Tilføj kode til din online til at bruge certifikatet** Nu vil vi tilføje kode til din online for at få adgang til certifikatet og bruge det til godkendelse.

Der er først koden for at få adgang til certifikatet.

    public static class CertificateHelper
    {
        public static X509Certificate2 FindCertificateByThumbprint(string findValue)
        {
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            try
            {
                store.Open(OpenFlags.ReadOnly);
                X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint,
                    findValue, false); // Don't validate certs, since the test root isn't installed.
                if (col == null || col.Count == 0)
                    return null;
                return col[0];
            }
            finally
            {
                store.Close();
            }
        }
    }


Bemærk, at StoreLocation CurrentUser i stedet for lokale computer. Og, at vi angivelse af 'false' til metoden Find da vi bruger en test cert.


Næste er kode, der bruger CertificateHelper og opretter en ClientAssertionCertificate, som er nødvendig for godkendelse.

    public static ClientAssertionCertificate AssertionCert { get; set; }

    public static void GetCert()
    {
        var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
        AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
    }


Her er den nye kode til at få adgangstoken. Det erstatter metoden GetToken ovenfor. Jeg har givet den et andet navn til nemmere.

    public static async Task<string> GetAccessToken(string authority, string resource, string scope)
    {
        var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
        var result = await context.AcquireTokenAsync(resource, AssertionCert);
        return result.AccessToken;
    }

Jeg har anbringe det hele denne kode i mit Web App-projekt af websted klasse for nemmere at bruge.

Den seneste kodeændring af er i metoden Application_Start. Først skal vi kalder metoden GetCert() for at indlæse ClientAssertionCertificate. Og derefter vi ændre Tilbagekaldsmetoden, som vi leverer, når du opretter en ny KeyVaultClient. Bemærk, at dette erstatter den kode, som vi havde ovenfor.

    Utils.GetCert();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));


**Tilføje et certifikat til din online via Azure-portalen** Føje et certifikat til din online er en enkel proces. Først skal gå til Azure-portalen, og gå til din online. Klik på posten for "Custom domains og SSL" på bladet indstillinger for din online. På bladet, der åbnes, du vil kunne upload det certifikat, du oprettede ovenfor KVWebApp.pfx skal du sørge for at kunne huske adgangskoden til pfx.

![Føje et certifikat til en WebApp i portalen Azure][2]


Den sidste ting, du skal gøre er at føje en indstilling for programmet til din online, der indeholder webstedet navn\_INDLÆS\_certifikater og en værdi på *. Dette sikrer, at alle certifikater er indlæst. Hvis du vil indlæse kun de certifikater, som du har overført, kan du angive en kommasepareret liste over deres thumbprints.

Hvis du vil vide mere om at føje et certifikat til en WebApp, se [Brug af certifikater i Azure websteder programmer](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/)


**Tilføj et certifikat til tasten samling som et hemmeligt** I stedet for overføres direkte dit certifikat til online-tjenesten, kan du gemme den i nøgle samling som et hemmeligt og installere den derfra. Dette er en proces, der er beskrevet i følgende blogindlægget, [Implementere Azure Web App certifikat gennem nøgle samling](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)



## <a id="next"></a>Næste trin ##


Programmering referencer, finde [Azure nøgle samling C# klient API Reference](https://msdn.microsoft.com/library/azure/dn903628.aspx).


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
