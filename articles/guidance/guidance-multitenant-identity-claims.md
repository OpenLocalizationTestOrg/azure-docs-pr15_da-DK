<properties
   pageTitle="Arbejde med Kræv-baserede identiteter i multiprofiler programmer | Microsoft Azure"
   description="Hvordan et brug krav til udsteder validering og godkendelse"
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
   ms.date="05/23/2016"
   ms.author="mwasson"/>

# <a name="working-with-claims-based-identities-in-multitenant-applications"></a>Arbejde med kravbaseret identiteter i multiprofiler programmer

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

I denne artikel er [en del af en række]. Der er også en komplet [Northwind] , der følger med denne serie.

## <a name="claims-in-azure-ad"></a>Krav i Azure AD

Når en bruger logger på, sender Azure AD et ID-token, der indeholder et sæt af krav om brugeren. Et krav er blot en del af oplysninger, udtrykt som et tasten/værdi-par. For eksempel `email` = `bob@contoso.com`.  Krav har en udsteder &mdash; i dette tilfælde Azure AD &mdash; , er den enhed, der godkender brugeren og opretter krav. Du har tillid til krav, fordi du har tillid til udstederen. (Omvendt, hvis du ikke har tillid til udstederen, Hav ikke tillid til krav!)

På et højt niveau:

1.  Brugeren skal godkende.
2.  IDP sender et sæt af krav.
3.  Appen normalisere eller augments påstande (valgfrit).
4.  Appen bruger påstande tage beslutninger om tilladelse.

I OpenID opretter forbindelse styres sættet af krav, som du angiver af [omfang parameter] for anmodningen om godkendelse. Dog problemer Azure AD et begrænset antal krav gennem OpenID forbinde; Se [understøttede Token og Kræv typer]. Hvis du ønsker flere oplysninger om brugeren, skal du bruge Azure AD Graph API.

Her er nogle af krav fra AAD, en app kan typisk interesserer:

Gøre krav på type i id-token |    Beskrivelse
-----------------------|--------------
AUD | Der er udstedt tokenet til. Dette er programmets klient-ID. Generelt ikke bør skal du bekymre dig om dette krav, fordi program automatisk validerer den. Eksempel:`"91464657-d17a-4327-91f3-2ed99386406f"`
grupper   | En liste over AAD grupper, som brugeren er medlem. Eksempel:`["93e8f556-8661-4955-87b6-890bc043c30f", "fc781505-18ef-4a31-a7d5-7d931d7b857e"]`
ISS  | [Udsteder] af OIDC tokenet. Eksempel:`https://sts.windows.net/b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4/`
Navn    | Brugerens viste navn. Eksempel:`"Alice A."`
objekt-id | Objekt-id'et for brugeren i AAD. Denne værdi er fast og ikke-genanvendelig id for brugeren. Brug denne værdi, ikke mail, som et entydigt id for brugere. e-mail-adresser kan ændre. Hvis du bruger API'EN Azure AD graf i dit app, er objekt-ID'ET den værdi, der bruges til forespørgsel profiloplysninger. Eksempel:`"59f9d2dc-995a-4ddf-915e-b3bb314a7fa4"`
roller   | En liste over app roller til brugeren. Eksempel:`["SurveyCreator"]`
tid | Lejer-ID. Denne værdi er et entydigt id for lejeren i Azure AD. Eksempel:`"b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4"`
unique_name | Et human læsbare vist navn på brugeren. Eksempel:`"alice@contoso.com"`
UPN | Brugerens hovednavn. Eksempel:`"alice@contoso.com"`

I denne tabel viser krav filtyper, som de vises i ID-tokenet. I ASP.NET Core 1.0 konverterer OpenID forbinde-programmer nogle af typerne krav, når det udfylder samlingen krav til brugerens hovednavn:

-   objekt-id >`http://schemas.microsoft.com/identity/claims/objectidentifier`
-   tid >`http://schemas.microsoft.com/identity/claims/tenantid`
-   unique_name >`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`
-   UPN >`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`

## <a name="claims-transformations"></a>Krav transformationer

Under godkendelse flow vil du ændre de krav, som du angiver IDP. Du kan udføre krav transformation inde i hændelsen **AuthenticationValidated** fra den OpenID forbinde program i ASP.NET Core 1.0. (Se [godkendelseshændelser]).

En hvilken som helst krav, du tilføjer under **AuthenticationValidated** er gemt i en session godkendelse cookie. De ikke kan få overføres tilbage til Azure AD.

Her er nogle eksempler på krav transformation:

-   **Krav normalisering**, eller gør krav ensartede på tværs af brugere. Dette er særligt relevant, hvis du får krav fra flere IDPs, som kan bruge forskellige kravtyper lignende oplysninger.
For eksempel sender Azure AD et "upn" krav, der indeholder brugerens mail. Andre IDPs kan sende et "mail" krav. Følgende kode konverterer "upn"-kravet til et "mail" krav:

    ```csharp
    var email = principal.FindFirst(ClaimTypes.Upn)?.Value;
    if (!string.IsNullOrWhiteSpace(email))
    {
      identity.AddClaim(new Claim(ClaimTypes.Email, email));
    }
    ```

- Tilføje **standard gøre krav på værdier** for krav, der ikke er til stede &mdash; for eksempel tildele en bruger til standardrollen som. I nogle tilfælde kan dette forenkle godkendelse logik.
- Tilføje **brugerdefinerede Kræv typer** med programmet-specifikke oplysninger om brugeren. For eksempel kan du gemme nogle oplysninger om brugeren i en database. Du kan tilføje et brugerdefineret krav disse oplysninger til godkendelse brugertilladelse. Kravet er gemt i en cookie, så du kun har brug at få det fra databasen én gang for hver login session. På den anden side vil du også undgå at oprette for meget store cookies, så du skal overveje forholdet mellem cookie størrelse kontra databaseopslag.   

Når godkendelse strømmen er fuldført, skal krav er tilgængelige i `HttpContext.User`. På det pågældende tidspunkt, skal du behandle dem som en skrivebeskyttet samling &mdash; f.eks., bruge dem tage beslutninger om tilladelse.

## <a name="issuer-validation"></a>Udsteder validering
I OpenID opretter forbindelse identificerer udsteder kravet ("iss") IDP, udstedt id-token. En del af OIDC godkendelse strømmen er at kontrollere, at udsteder kravet stemmer overens med den faktiske udsteder. OIDC programmer håndterer dette for dig.

I Azure AD udsteder værdien er entydige for hver AD-lejer (`https://sts.windows.net/<tenantID>`). Programmet skal derfor gøre en ekstra Kontrollér, at sikre, at udstederen repræsenterer en lejer, der har tilladelse til at logge på appen.

Om et enkelt lejer program, kan du kun se, udstederen er din egen lejer. Faktisk sker OIDC program automatisk, som standard. I en med flere lejer app skal du tillade for flere udstedere, svarer til de anden lejere. Her er et generelt tilgang bruge:

-   I indstillingerne OIDC program skal du angive **ValidateIssuer** til falsk. Dette deaktiverer automatisk kontrol.
-   Når en lejer tilmelder sig, kan du gemme lejeren og udstederen i din bruger DB.
-   Når en bruger logger på, kan du slå op udsteder i databasen. Hvis udstederen ikke blev fundet, betyder det pågældende lejer ikke er tilmeldt. Du kan dirigeres til en Tilmeld side.
-  Du kan også blacklist visse lejere for eksempel for kunder, der ikke betale deres abonnement.

Finde en mere detaljeret beskrivelse [tilmelding og lejer onboarding i et multiprofiler computeren][signup].

## <a name="using-claims-for-authorization"></a>Brug af krav om godkendelse

Med krav er en brugers identitet ikke længere en monolitisk enhed. For eksempel har en bruger en e-mail-adresse, telefonnummer, Fødselsdag, køn osv. Måske gemmer brugerens IDP alle disse oplysninger. Men når du godkende brugeren, får du typisk vist et undersæt af disse som krav. I denne tabel er det bruger-id blot en samlet af krav. Når du tager godkendelse beslutninger om en bruger, kan du søge efter bestemt sæt af krav. Med andre ord spørgsmålet "Kan brugeren X udføre handling Y" i sidste ende bliver "Betyder bruger X har gøre krav på Z".

Her er nogle grundlæggende mønstre for at kontrollere krav.

-  Sådan kontrollerer du, at brugeren har en bestemt krav med en bestemt værdi:

    ```csharp
    if (User.HasClaim(ClaimTypes.Role, "Admin")) { ... }
    ```
    Denne kode kontrollerer, om brugeren har en rolle krav med værdien "Admin". Korrekt heltal den sag, hvor brugeren har ingen rolle krav eller flere rolle krav.

    Klassen **ClaimTypes** definerer konstanter for ofte anvendte kravtyper. Dog kan du bruge en hvilken som helst strengværdi for typen krav.

-   Sådan hentes en enkelt værdi for typen krav, når du forventer, at der kan være mindst én værdi:
    ```csharp
     string email = User.FindFirst(ClaimTypes.Email)?.Value;
    ```
-   Sådan får alle værdier for typen af krav:

    ```csharp
     IEnumerable<Claim> groups = User.FindAll("groups");
    ```

Du kan finde flere oplysninger, se [rollebaseret og ressource-baseret godkendelse i multiprofiler programmer][authorization].

## <a name="next-steps"></a>Næste trin

- Læs næste artikel i denne serie: [tilmelding og lejer onboarding i et multiprofiler computeren][signup]
- Lær mere om [kravbaseret godkendelse] i dokumentationen til ASP.NET Core 1.0

<!-- Links -->
[en del af en serie]: guidance-multitenant-identity.md
[omfang parameter]: http://nat.sakimura.org/2012/01/26/scopes-and-claims-in-openid-connect/
[Understøttede Token og Kræv typer]: ../active-directory/active-directory-token-and-claims.md
[udsteder]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken
[Godkendelse begivenheder]: guidance-multitenant-identity-authenticate.md#authentication-events
[signup]: guidance-multitenant-identity-signup.md
[Kravbaseret godkendelse]: https://docs.asp.net/en/latest/security/authorization/claims.html
[Northwind]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
[authorization]: guidance-multitenant-identity-authorize.md
