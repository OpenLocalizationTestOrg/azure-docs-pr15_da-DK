<properties
    pageTitle="Logge vigtige overgang på Azure AD | Microsoft Azure"
    description="Denne artikel beskrives de signerende vigtige overgang bedste fremgangsmåder til Azure Active Directory"
    services="active-directory"
    documentationCenter=".net"
    authors="gsacavdm"
    manager="krassk"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="gsacavdm"/>

# <a name="signing-key-rollover-in-azure-active-directory"></a>Logge vigtige overgang i Azure Active Directory

Dette emne beskrives, hvad du bør vide om de offentlige nøgler, der bruges i Azure Active Directory (Azure AD) til at signere sikkerhedstokens. Det er vigtigt at være opmærksom på, at disse taster overgang med jævne mellemrum, og i nødstilfælde, kan sammen med det samme. Alle programmer, der bruger Azure AD skal kunne håndtere vigtige overgang eller oprette en proces i periodiske manuel overgang fra et program. Fortsætte med at læse for at forstå, hvordan de pågældende taster fungerer, hvordan du vurdere virkningen af overgang til dit program og hvordan du kan opdatere dit program eller oprette en periodiske manuel overgang processen for at håndtere vigtige overgang, hvis det er nødvendigt.

## <a name="overview-of-signing-keys-in-azure-ad"></a>Oversigt over logge taster på Azure AD

Azure AD bruger kryptering med offentlig nøgle bygget på branchestandarder til at oprette tillid mellem selve og de programmer, der bruger den. I praksis det fungerer på følgende måde: Azure AD benytter en signerende nøgle, der består af et offentlige og private nøgler par. Når en bruger logger ind på et program, der bruger Azure AD til godkendelse, opretter Azure AD et sikkerhedstoken, der indeholder oplysninger om brugeren. Dette token er signeret af Azure AD ved hjælp af dens privat nøgle, før det sendes tilbage til programmet. For at bekræfte, at Tokenet er gyldige og faktisk tilhørende fra Azure AD, skal programmet validere det token signatur ved hjælp af den offentlige nøgle, der vises af Azure AD, der er indeholdt i lejerens [OpenID forbinde discovery-dokumentet](http://openid.net/specs/openid-connect-discovery-1_0.html) eller SAML/WS-Fed [sammenslutning metadata dokument](active-directory-federation-metadata.md).

Af sikkerhedsmæssige årsager kan Azure AD logge vigtige ruller periodisk og, hvis det er nødstilfælde, rulles med det samme. Alle programmer, der integreres med Azure AD skal være forberedt til at håndtere hændelsen vigtige overgang uanset hvor ofte det kan forekomme. Hvis det ikke, og programmet forsøger at bruge en udløbet nøgle til at kontrollere signaturen i en token, mislykkes anmodningen logon.

Der er altid mere end én gyldig nøgle OpenID forbinde discovery-dokumentet og i sammenslutning Metadatadokumentet. Programmet skal være forberedt på at bruge en af de taster, der er angivet i dokumentet, da én tast kan rulles snart, en anden kan være dens erstatning, og osv..

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Sådan Vurder, hvis dit program påvirkes og hvad du skal gøre det.

Hvordan programmet håndterer vigtige overgang, afhænger af variabler som typen program eller hvilke identitet protokollen og bibliotek blev brugt. Afsnittene herunder vurdere, om de mest almindelige typer programmer påvirkes af de vigtigste overgang og giver vejledning om, hvordan du opdaterer programmet til at understøtte automatisk overgang eller manuelt opdatere tasten.

* [Oprindelig klientprogrammer adgang til ressourcer](#nativeclient)
* [Webprogrammer / API'er adgang til ressourcer](#webclient)
* [Webprogrammer / API'er beskytte ressourcer og oprettet ved hjælp af Azure App Services](#appservices)
* [Webprogrammer / API'er beskytte ressourcer ved hjælp af .NET OWIN OpenID forbinde, WS Fed eller WindowsAzureActiveDirectoryBearerAuthentication program](#owin)
* [Webprogrammer / API'er beskytte ressourcer ved hjælp af .NET Core OpenID kontakt eller JwtBearerAuthentication program](#owincore)
* [Webprogrammer / API'er beskytte ressourcer ved hjælp af Node.js passport-azure-ad-modulet](#passport)
* [Webprogrammer / API'er beskytte ressourcer og oprettet med Visual Studio 2015](#vs2015)
* [Webprogrammer beskytte ressourcer og oprettet med Visual Studio 2013](#vs2013)
* [Web API'er beskytte ressourcer og oprettet med Visual Studio 2013](#vs2013_webapi)
* [Webprogrammer beskytte ressourcer og oprettet med Visual Studio 2012](#vs2012)
* [Webprogrammer beskytte ressourcer og oprettet med Visual Studio 2010, 2008 o ved hjælp af Windows Identity Foundation](#vs2010)
* [Webprogrammer / API'er beskytte ressourcer ved hjælp af andre biblioteker eller manuelt implementering af nogen af de understøttede protokoller](#other)

Denne vejledning er **ikke** tilgængelig for:

* Programmer føjet fra Azure AD-program galleri (herunder brugerdefinerede) har særskilte retningslinjer forbindelse logge taster. [Du kan finde flere oplysninger.](active-directory-sso-certs.md)
* Lokale programmer, der er publiceret via proxyen ikke behøver at bekymre dig om at logge taster.

### <a name="nativeclient"></a>Oprindelig klientprogrammer adgang til ressourcer

Programmer, der kun har adgang til ressourcerne (Windows XP Microsoft Graph, KeyVault, Outlook API og andre Microsoft-APIs) generelt kun få et token og sende dem langs til ejeren af ressource. Da, de ikke beskytte ressourcerne, at de undersøge ikke tokenet og derfor er ikke nødvendigt at sikre, at den er signeret korrekt.

Oprindelig klientprogrammer, om pc eller mobil, falder inden for denne kategori og dermed er ikke påvirkes af overgang.

### <a name="webclient"></a>Webprogrammer / API'er adgang til ressourcer

Programmer, der kun har adgang til ressourcerne (Windows XP Microsoft Graph, KeyVault, Outlook API og andre Microsoft-APIs) generelt kun få et token og sende dem langs til ejeren af ressource. Da, de ikke beskytte ressourcerne, at de undersøge ikke tokenet og derfor er ikke nødvendigt at sikre, at den er signeret korrekt.

Webprogrammer og web API'er, der bruger kun app strømmen (klient legitimationsoplysninger / klientcertifikat), falder inden for denne kategori, og dermed er ikke påvirkes af overgang.

### <a name="appservices"></a>Webprogrammer / API'er beskytte ressourcer og oprettet ved hjælp af Azure App Services

Azure App Services' godkendelse / godkendelse (EasyAuth) funktionalitet allerede har den nødvendige logik til at håndtere vigtige overgang automatisk.

### <a name="owin"></a>Webprogrammer / API'er beskytte ressourcer ved hjælp af .NET OWIN OpenID forbinde, WS Fed eller WindowsAzureActiveDirectoryBearerAuthentication program

Hvis dit program bruger den .NET OWIN OpenID forbinde, WS Fed eller WindowsAzureActiveDirectoryBearerAuthentication program, har den allerede den nødvendige logik til at håndtere vigtige overgang automatisk.

Du kan bekræfte, at dit program bruger et af følgende ved at kigge på grund af følgende kodestykker i dit program Startup.cs eller Startup.Auth.cs

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
     // ...
    });
```
```
 app.UseWindowsAzureActiveDirectoryBearerAuthentication(
     new WindowsAzureActiveDirectoryBearerAuthenticationOptions
     {
     // ...
     });
```

### <a name="owincore"></a>Webprogrammer / API'er beskytte ressourcer ved hjælp af .NET Core OpenID kontakt eller JwtBearerAuthentication program

Hvis dit program bruger .NET Core OWIN OpenID kontakt eller JwtBearerAuthentication program, har den allerede den nødvendige logik til at håndtere vigtige overgang automatisk.

Du kan bekræfte, at dit program bruger et af følgende ved at kigge på grund af følgende kodestykker i dit program Startup.cs eller Startup.Auth.cs

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
    });
```

### <a name="passport"></a>Webprogrammer / API'er beskytte ressourcer ved hjælp af Node.js passport-azure-ad-modulet

Hvis dit program bruger Node.js passport-ad-modulet, har den allerede den nødvendige logik til at håndtere vigtige overgang automatisk.

Du kan bekræfte, dit program passport-ad ved at søge efter følgende kodestykke i dit program app.js

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="vs2015"></a>Webprogrammer / API'er beskytte ressourcer og oprettet med Visual Studio 2015

Hvis dit program blev oprettet ved hjælp af en skabelon program i Visual Studio-2015 og du har valgt **Arbejde og Skolekonti** i menuen **Rediger godkendelse** , har den allerede den nødvendige logik til at håndtere vigtige overgang automatisk. Denne logik, der er integreret i programmer OWIN OpenID forbinde henter og gemmer nøglerne fra OpenID forbinde discovery-dokumentet og opdaterer dem regelmæssigt.

Hvis du har tilføjet godkendelse din løsning manuelt, kan dit program ikke har den nødvendige vigtige overgang logik. Skal du skrive det selv, eller Følg trinnene i [webprogrammer / API'er ved hjælp af andre biblioteker eller manuelt implementering af nogen af de understøttede protokoller.](#other).

### <a name="vs2013"></a>Webprogrammer beskytte ressourcer og oprettet med Visual Studio 2013

Hvis dit program blev oprettet ved hjælp af en web application skabelon i Visual Studio 2013, og du har valgt **Organisationskonti** i menuen **Rediger godkendelse** , har den allerede den nødvendige logik til at håndtere vigtige overgang automatisk. Denne logik gemmer din organisations entydigt id og den signerende vigtige oplysninger i to databasetabeller, der er knyttet til projektet. Du kan finde forbindelsesstrengen til databasen i projektets Web.config-fil.

Hvis du manuelt føjet godkendelse til din løsning, kan dit program ikke har den nødvendige vigtige overgang logik. Skal du skrive det selv, eller Følg trinnene i [webprogrammer / API'er ved hjælp af andre biblioteker eller manuelt implementering af nogen af de understøttede protokoller.](#other).

Følgende trin kan hjælpe dig med at kontrollere, at logikken fungerer korrekt i programmet.

1. Åbn løsningen i Visual Studio 2013, og klik derefter på fanen **Server Explorer** vinduet højre.
2. Udvid **dataforbindelser**, **DefaultConnection**og derefter **tabeller**. Find tabellen **IssuingAuthorityKeys** , skal du højreklikke på den, og klik derefter på **Vis tabeldata**.
3. I tabellen **IssuingAuthorityKeys** , vil der være mindst én række, der svarer til miniatureværdien for nøglen. Slette alle rækker i tabellen.
4. Højreklik på tabellen **lejere** , og klik derefter på **Vis tabeldata**.
5. I tabellen **lejere** , vil der være mindst én række, der svarer til en entydig mappe lejer-id. Slette alle rækker i tabellen. Hvis du ikke slette rækker i både den **lejere** tabel og **IssuingAuthorityKeys** , får du en fejl på kørselstidspunktet.
6. Oprette og køre programmet. Når du har logget på din konto, kan du stoppe programmet.
7. Vende tilbage til **Server Explorer** , og kig på værdierne i tabellen **IssuingAuthorityKeys** og **lejere** . Du vil bemærke, at de er blevet automatisk igen med de relevante oplysninger fra sammenslutning Metadatadokumentet.

### <a name="vs2013"></a>Web API'er beskytte ressourcer og oprettet med Visual Studio 2013

Hvis du har oprettet en web API-program i Visual Studio 2013 ved hjælp af skabelonen Web API og derefter valgt **Organisationskonti** i menuen **Rediger godkendelse** , har du allerede den nødvendige logik i dit program.

Hvis du manuelt konfigurerede godkendelse, skal du følge vejledningen nedenfor for at se, hvordan du konfigurerer din Web API for at opdatere automatisk dens vigtige oplysninger.

Følgende kodestykke viser, hvordan du få de seneste nøgler fra sammenslutning Metadatadokumentet, og brug derefter [JWT Token Handler](https://msdn.microsoft.com/library/dn205065.aspx) til at validere tokenet. Kodestykket antages det, du vil bruge din egen cache-funktionen til vedvarer tasten til at validere fremtidige tokens ud fra Azure AD, uanset om det er i en database, konfigurationsfil eller et andet sted.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Classic Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="vs2012"></a>Webprogrammer beskytte ressourcer og oprettet med Visual Studio 2012

Hvis dit program blev oprettet i Visual Studio 2012, bruges du sandsynligvis identitet og værktøj i Access til at konfigurere dit program. Det er også sandsynligvis, når du bruger [Validere udsteder navn registreringsdatabasen (VINR)](https://msdn.microsoft.com/library/dn205067.aspx). VINR er ansvarlig for at vedligeholde oplysninger om der er tillid til identitetsudbydere (Azure AD) og de pågældende taster bruges til at validere tokens, der er udstedt af dem. VINR gør det også nemt for automatisk at opdatere den vigtige oplysninger, der er gemt i en Web.config-fil ved at hente det nyeste sammenslutning metadata dokument, der er knyttet til adresselisten, kontrollerer, om konfigurationen er forældet med det nyeste dokument og opdatering af programmet til at bruge den nye nøgle efter behov.

Hvis du oprettede dit program ved hjælp af kodeeksempler eller gennemgang dokumentationen fra Microsoft, findes allerede vigtige overgang logik i projektet. Du vil bemærke, at nedenstående kode allerede findes i dit projekt. Hvis dit program ikke allerede har denne logik, skal du følge trinnene nedenfor til at tilføje den og kontrollere, at den fungerer korrekt.

1. I **Solution Explorer**skal du tilføje en reference til samlingen **System.IdentityModel** for det relevante projekt.
2. Åbn filen **Global.asax.cs** og tilføje følgende ved hjælp af direktiver:
```
using System.Configuration;
using System.IdentityModel.Tokens;
```
3. Tilføj følgende metode til filen **Global.asax.cs** :
```
protected void RefreshValidationSettings()
{
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
}
```
4. Kalde metoden **RefreshValidationSettings()** i metoden **Application_Start()** i **Global.asax.cs** , som vist:
```
protected void Application_Start()
{
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
}
```

Når du har fulgt disse trin, opdateres dit program Web.config med de seneste oplysninger fra sammenslutning Metadatadokumentet, herunder de seneste taster. Denne opdatering skal forekomme, hver gang genbruger programgruppen i IIS. IIS er som standard angivet til Papirkurv programmer hver 29 timer.

Følg nedenstående trin for at bekræfte, at den vigtige overgang logik fungerer.

1. Når du har bekræftet, at dit program bruger ovenstående kode, Åbn **Web.config** -filen, og Naviger til den **<issuerNameRegistry>** Bloker, specifikt leder du efter følgende par linjer:
```
<issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
```
2. I den **<add thumbprint=””>** indstille, ændre miniatureværdien ved at erstatte alle tegn med et andet navn. Gem filen **Web.config** .

3. Opret programmet, og derefter køre den. Hvis du kan gennemføre de logonprocessen, opdaterer programmet korrekt tasten ved at hente de nødvendige oplysninger fra din directory federation metadata dokument. Hvis du har problemer med at logge, kan du sikre dig ændringer i dit program er korrekte ved at læse emne [Tilføje Sign-On til din Web-program ved hjælp af Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) eller downloade og undersøge følgende eksempel-kode: [Flere lejer Cloud-programmet til Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).


### <a name="vs2010"></a>Webprogrammer beskytte ressourcer og oprettet med Visual Studio 2008 eller 2010 og Windows Identity Foundation (WIF) version 1.0 til .NET 3.5

Hvis du har oprettet et program på WIF version 1.0, er der ingen medfølgende ordning til automatisk at opdatere dit program konfiguration for at bruge en ny nøgle.

- *Nemmest* Brug den FedUtil værktøj, der er inkluderet i WIF SDK, som kan hente den nyeste metadata-dokument og opdatere din konfiguration.
- Opdater dit program til .NET 4.5, som indeholder den nyeste version af WIF, der er placeret i navneområdet System. Du kan derefter bruge [Validere udsteder navn registreringsdatabasen (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) til at udføre automatiske opdateringer af programmets konfiguration.
- Udføre en manuel overgang ifølge instruktionerne i slutningen af dokumentet vejledning.

Vejledning til brug på FedUtil til at opdatere din konfiguration:

1. Kontrollér, at du har WIF version 1.0 SDK installeret på computeren udvikling til Visual Studio 2008 eller 2010. Du kan [hente det fra her](https://www.microsoft.com/en-us/download/details.aspx?id=4451) , hvis du endnu ikke har installeret den.
2. I Visual Studio, åbne løsningen, og højreklik på det relevante projekt og vælg derefter **opdatere sammenslutning metadata**. Hvis denne indstilling ikke er tilgængelig, er FedUtil og/eller WIF version 1.0 SDK ikke installeret.
3. Vælg **Opdater** for at begynde at opdatere metadata for din sammenslutning prompten. Hvis du har adgang til den server-miljø, der er vært for programmet, kan du eventuelt bruge Fedutil's [automatisk metadata opdatere scheduler](https://msdn.microsoft.com/library/ee517272.aspx).
4. Klik på **Udfør** for at fuldføre opdateringen.

### <a name="other"></a>Webprogrammer / API'er beskytte ressourcer ved hjælp af andre biblioteker eller manuelt implementering af nogen af de understøttede protokoller

Hvis du bruger nogle andet bibliotek eller implementeret en af de understøttede protokoller manuelt, skal du gennemse biblioteket eller din implementeringen for at sikre, at tasten hentes fra OpenID forbinde discovery-dokumentet eller sammenslutning Metadatadokumentet. En metode til at kontrollere, om dette er at foretage en søgning i din kode eller bibliotekets kode for en hvilken som helst opkald ud til OpenID discovery-dokumentet eller sammenslutning Metadatadokumentet.

Om de vigtigste bliver gemt et sted, hvor eller hårdt i programmet, kan du manuelt hente nøgle og Opdater den tilsvarende til at udføre en manuel overgang ifølge instruktionerne i slutningen af dokumentet vejledning. **Det er stærkt, at du forbedre dit program til at understøtte automatisk overgang** ved hjælp af fremgangsmåder disposition i denne artikel for at undgå fremtidige afbrydelser og spild, hvis Azure AD øges er overgang cadence eller har en akutte out-of-band-overgang.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Hvordan du kan teste dit program til at bestemme, hvis påvirkes

Du kan kontrollere, om dit program understøtter automatisk vigtige overgang ved at downloade scripts og følge vejledningen i [GitHub lageret.](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-you-application-does-not-support-automatic-rollover"></a>Hvordan du udfører en manuel overgang, hvis du program ikke understøtter automatisk overgang

Hvis dit program indeholder **ikke** understøttelse af automatisk overgang, skal du oprette en proces, der jævnligt overvåger Azure AD signerende nøgler og udfører en manuel overgang i overensstemmelse hermed. [Denne GitHub lager](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) indeholder scripts og oplysninger om, hvordan du gør dette.
