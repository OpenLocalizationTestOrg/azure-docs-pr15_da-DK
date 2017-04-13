<properties 
    pageTitle="Oprette forbindelse til Media Services konto ved hjælp af .NET" 
    description="Dette emne beskrives, hvordan du opretter forbindelse til Media Services uisng .NET." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="juliako"/>


# <a name="connecting-to-media-services-account-using-media-services-sdk-for-net"></a>Oprette forbindelse til Media Services konto ved hjælp af Media Services SDK til .NET

> [AZURE.SELECTOR]
- [RESTEN](media-services-rest-connect-programmatically.md)
- [.NET](media-services-dotnet-connect-programmatically.md)


Dette emne beskrives, hvordan du får en programmeringsmæssig forbindelse til Microsoft Azure Media Services, når du programming med Media Services SDK til .NET.


## <a name="connecting-to-media-services"></a>Oprette forbindelse til Media Services

For at oprette forbindelse til Media Services fra et program, skal du have har konfigureret en Azure-konto, konfigureret Media Services på kontoen, og konfigurere et Visual Studio-projekt til udvikling med Media Services SDK til .NET. Se installation af udvikling med Media Services SDK til .NET kan finde flere oplysninger.

I slutningen af konfigurationen af den Media Services-konto, du har hentet følgende værdier for internetforbindelsen. Brug disse til Opret automatisk forbindelse til Media Services.

- Navnet på din Media Services konto.

- Produktnøglen Media Services-konto.

Gå til portalen Azure Management for at finde disse værdier skal, Vælg din medier tjenestekonto, og klik på ikonet "**ADMINISTRER nøgler**" i bunden af vinduet portalen. At klikke på ikonet ud for hver tekstboks kopieres til Udklipsholder.


## <a name="creating-a-cloudmediacontext-instance"></a>Oprette en forekomst af CloudMediaContext

Hvis du vil starte programming mod Media Services skal du oprette en forekomst af **CloudMediaContext** , der repræsenterer serverkonteksten. **CloudMediaContext** indeholder referencer til vigtige websteder, herunder job, aktiver, filer, access politikker og locators.

>[AZURE.NOTE] Klassen **CloudMediaContext** er ikke sikker tråd. Du skal oprette en ny CloudMediaContext tråd eller per sæt handlinger.


CloudMediaContext har fem parametre overloads. Det anbefales at bruge konstruktører, der kræver **MediaServicesCredentials** som en parameter. Du kan finde yderligere oplysninger finder **Genbruge Access Control Service Tokens** , der følger efter. 

I følgende eksempel bruges den offentlige CloudMediaContext(MediaServicesCredentials credentials) parametre:

    // _cachedCredentials and _context are class member variables. 
    _cachedCredentials = new MediaServicesCredentials(
                    _mediaServicesAccountName,
                    _mediaServicesAccountKey);
    
    _context = new CloudMediaContext(_cachedCredentials);


## <a name="reusing-access-control-service-tokens"></a>Genbruge Access Control Service Tokens

Dette afsnit viser, hvordan du genbruge Access Control Service tokens ved hjælp af CloudMediaContext konstruktører, der kræver MediaServicesCredentials som en parameter.


[Azure Active Directory adgangskontrol](https://msdn.microsoft.com/library/hh147631.aspx) (også kendt som Access Control Service eller ACS) er en skybaseret tjeneste, som gør det nemt kontrollere og godkende brugere kan få adgang til deres webprogrammer. Microsoft Azure Media Services styrer adgangen til sine tjenester, men OAuth-protokollen, der kræver en ACS token. ACS-tokens modtager Media Services, fra en server til godkendelse.

Når udvikling med Media Services SDK, kan du vælge ikke håndtere tokens, fordi SDK kode ledere dem for dig. Men så SDK fuldt administrere ACS-tokens fører til unødvendige token anmodninger. Anmoder om tokens tager tid og bruger klient- og ressourcer. Også throttles ACS serveren anmodningerne, hvis rente er for stor. Grænsen på er 30 anmodninger sekundet, skal du se [ACS Tjenestebegrænsningerne](https://msdn.microsoft.com/library/gg185909.aspx) få mere at vide.

Start med version 3.0.0.0 af Media Services SDK, og kan du genbruge ACS-tokens. De **CloudMediaContext** konstruktører, der tager **MediaServicesCredentials** som en parameter aktivere Deling ACS-tokens mellem flere kontekster. Klassen MediaServicesCredentials indkapsler Media Services-legitimationsoplysninger. Hvis et ACS token er tilgængelig, og dets udløbsdatoen kendes, kan du oprette en ny forekomst af MediaServicesCredentials med tokenet og sende dem til parametre af CloudMediaContext. Bemærk, at Media Services SDK automatisk opdaterer tokens, når de udløber. Der er to måder at genbruge ACS tokens, som vist i eksemplerne nedenfor.

- Du kan gemme i cachen objektet **MediaServicesCredentials** i hukommelsen (for eksempel i en statisk klasse variabel). Derefter overføre cachelagrede objektet til CloudMediaContext parametre. Objektet MediaServicesCredentials indeholder et ACS token, der kan genbruges, hvis det er stadig er gyldig. Hvis tokenet ikke er gyldige, opdateres ved Media Services SDK ved hjælp af de legitimationsoplysninger, der blev angivet MediaServicesCredentials parametre.

    Bemærk, at objektet **MediaServicesCredentials** får et gyldigt token, efter at RefreshToken hedder. **CloudMediaContext** kalder metoden **RefreshToken** i konstruktøren. Hvis du planlægger at gemme de token værdier til en ekstern lager, skal du sørge for at kontrollere, om værdien TokenExpiration er gyldig, før du gemmer token dataene. Hvis det ikke er gyldige, kan du ringe RefreshToken før cachelagring.

        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);

        
        // Use the cached credentials to create a new CloudMediaContext object.
        if(_cachedCredentials == null)
        {
            _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);
        }
        
        CloudMediaContext context = new CloudMediaContext(_cachedCredentials);

- Du kan også gemme cachen strengen AccessToken og TokenExpiration værdier. Værdierne, der kan senere kan bruges til at oprette et nyt MediaServicesCredentials objekt med de cachelagrede token data.  Dette er særligt nyttigt for scenarier, hvor tokenet kan sikkert deles af flere processer eller computere.

    De følgende kodestykker ringe metoderne SaveTokenDataToExternalStorage, GetTokenDataFromExternalStorage og UpdateTokenDataInExternalStorageIfNeeded, der ikke er defineret i dette eksempel. Du kan definere disse metoder til at gemme, hente og opdatere token data i en ekstern storage. 

        CloudMediaContext context1 = new CloudMediaContext(_mediaServicesAccountName, _mediaServicesAccountKey);
        
        // Get token values from the context.
        var accessToken = context1.Credentials.AccessToken;
        var tokenExpiration = context1.Credentials.TokenExpiration;
        
        // Save token values for later use. 
        // The SaveTokenDataToExternalStorage method should check 
        // whether the TokenExpiration value is valid before saving the token data. 
        // If it is not valid, call MediaServicesCredentials’s RefreshToken before caching.
        SaveTokenDataToExternalStorage(accessToken, tokenExpiration);
        
    Brug de gemte token værdier til at oprette MediaServicesCredentials.


        var accessToken = "";
        var tokenExpiration = DateTime.UtcNow;
        
        // Retrieve saved token values.
        GetTokenDataFromExternalStorage(out accessToken, out tokenExpiration);
        
        // Create a new MediaServicesCredentials object using saved token values.
        MediaServicesCredentials credentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey)
        {
            AccessToken = accessToken,
            TokenExpiration = tokenExpiration
        };
        
        CloudMediaContext context2 = new CloudMediaContext(credentials);

    Opdatere den token kopi i tilfælde af tokenet blev opdateret af Media Services SDK. 
    
        if(tokenExpiration != context2.Credentials.TokenExpiration)
        {
            UpdateTokenDataInExternalStorageIfNeeded(accessToken, context2.Credentials.TokenExpiration);
        }
        

- Hvis du har flere Media Services-konti (for eksempel for indlæsning deling formål eller geografisk-fordeling) kan du gemme cachen MediaServicesCredentials objekter ved hjælp af samlingen System.Collections.Concurrent.ConcurrentDictionary (samlingen ConcurrentDictionary repræsenterer en tråd sikker samling af nøgle/værdi-par, der kan åbnes af flere tråde samtidig). Du kan derefter bruge metoden GetOrAdd for at finde de cachelagrede legitimationsoplysninger. 

        // Declare a static class variable of the ConcurrentDictionary type in which the Media Services credentials will be cached.  
        private static readonly ConcurrentDictionary<string, MediaServicesCredentials> mediaServicesCredentialsCache = 
            new ConcurrentDictionary<string, MediaServicesCredentials>();
        

        // Cache (or get already cached) Media Services credentials. Use these credentials to create a new CloudMediaContext object.
        static public CloudMediaContext CreateMediaServicesContext(string accountName, string accountKey)
        {
            CloudMediaContext cloudMediaContext;
            MediaServicesCredentials mediaServicesCredentials;
        
            mediaServicesCredentials = mediaServicesCredentialsCache.GetOrAdd(
                accountName,
                valueFactory => new MediaServicesCredentials(accountName, accountKey));
        
            cloudMediaContext = new CloudMediaContext(mediaServicesCredentials);
        
            return cloudMediaContext;
        }
        
## <a name="connecting-to-a-media-services-account-located-in-the-north-china-region"></a>Oprette forbindelse til en Media Services-konto, der er placeret i området North Kina

Hvis din konto er placeret i området North Kina, Brug den følgende:

    public CloudMediaContext(Uri apiServer, string accountName, string accountKey, string scope, string acsBaseAddress)

Eksempel:


    _context = new CloudMediaContext(
        new Uri("https://wamsbjbclus001rest-hs.chinacloudapp.cn/API/"),
        _mediaServicesAccountName,
        _mediaServicesAccountKey,
        "urn:WindowsAzureMediaServices",
        "https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn");


## <a name="storing-connection-values-in-configuration"></a>Gemme forbindelse værdier i konfiguration

Det er en meget anbefalede fremgangsmåde at gemme forbindelse værdier, især følsomme værdier som dit kontonavn og din adgangskode, i konfigurationen. Det er også en anbefalede fremgangsmåde at kryptere følsomme konfigurationsdata. Du kan kryptere filen hele konfigurationen ved hjælp af Windows System (ENCRYPTING File). Hvis du vil aktivere genoprettelse af krypterede data på en fil, højreklik på filen, Vælg **Egenskaber**og aktivere kryptering i **Avanceret** under fanen Indstillinger. Eller du kan oprette en brugerdefineret løsning til at kryptere markerede dele af en konfigurationsfil ved hjælp af beskyttet konfiguration. Se [kryptering konfiguration oplysninger ved hjælp af beskyttet konfiguration](https://msdn.microsoft.com/library/53tyfkaw.aspx).

Følgende App.config-filen indeholder værdierne, der kræves forbindelse. Værdierne i den <appSettings> element er de påkrævede værdier, du fik fra konfigurationen af Media Services-konto.

    <configuration>
      <appSettings>
        <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
        <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
      </appSettings>
    </configuration>


For at hente forbindelse værdier fra konfiguration, kan du bruge klassen **ConfigurationManager** og derefter tildele værdierne til felter i din kode:
    
    private static readonly string _accountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
    private static readonly string _accountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];



##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
