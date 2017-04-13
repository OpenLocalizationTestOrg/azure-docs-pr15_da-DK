<properties
    pageTitle="Sådan aktiveres SSO i tværs-app på iOS ved hjælp af ADAL | Microsoft Azure"
    description="Sådan bruges funktionerne af ADAL SDK til at aktivere Single Sign-On på tværs af dine programmer. "
    services="active-directory"
    documentationCenter=""
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>


# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Sådan aktiveres SSO i tværs-app på iOS ved hjælp af ADAL


Forudsat at enkelt enkeltlogon (SSO), så brugerne kun skal indtaste deres legitimationsoplysninger én gang og har disse legitimationsoplysninger automatisk arbejde på tværs af forventes programmer nu af kunder. Problemer med at indtaste deres brugernavn og din adgangskode på en lille skærm, ofte gange kombineres med en anden faktor (2FA) som et telefonopkald eller en texted kode resultater i Hurtig utilfredshed, hvis en bruger har at gøre dette mere end én gang for dit produkt.

Desuden, hvis du udnytte en identitet platform, som andre programmer kan bruge som Microsoft Accounts eller en arbejdskonto fra Office 365, forvente kunderne, at de legitimationsoplysninger, der bruges på tværs af alle programmer uanset leverandøren.

Microsoft Identity platformen, sammen med vores Microsoft identitet SDK'er gør alt dette hårde arbejde for dig og giver dig mulighed for at begejstre dine kunder med SSO enten inden for din egen pakke med programmer eller, som med vores broker egenskab og godkenderen programmer, på tværs af hele enheden.

Denne gennemgang fortæller dig, hvordan du konfigurerer vores SDK i dit program at tildele til dine kunder.

Denne gennemgang gælder for:

* Azure Active Directory
* Azure Active Directory B2C
* Azure Active Directory B2B
* Azure Active Directory betinget adgang


Bemærk, at dokumentet nedenfor antages det, du har viden om, hvordan til [klargøring programmer i portalen ældre til Azure Active Directory](./develop/active-directory-how-to-integrate.md) samt har integreret dit program med [Microsoft identitet iOS SDK](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>SSO begreber i Microsoft identitet Platform

### <a name="microsoft-identity-brokers"></a>Microsoft identitet mæglere

Microsoft giver programmer til alle mobil platform, som giver mulighed for til at lukke legitimationsoplysninger på tværs af programmer fra forskellige leverandører samt giver mulighed for særlige forbedrede funktioner, der kræver en sikker samle skal validere legitimationsoplysninger fra. Vi kalder disse **mæglere**. På iOS og Android leveres disse via kan downloades programmer, kunder enten installere enkeltvis eller kan installeres på enheden af et firma, der administrerer nogle af eller alle på enheden for deres medarbejder. Disse mæglere understøtter administration af sikkerhed kun for visse programmer eller for hele enheden, der er baseret på hvad IT-administratorer ønsker. Denne funktionalitet findes i Windows i en konto, der er indbygget i operativsystemet, kendte teknisk som Web godkendelse Broker.

At forstå, hvordan vi bruge disse mæglere, og hvordan dine kunder kan se dem i deres login flow til Microsoft Identity platformen Læs videre for at få mere at vide.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Mønstre til at logge på på mobile enheder

Adgang til legitimationsoplysninger på enheder skal du følge to grundlæggende mønstre for Microsoft Identity platform:

* Ikke-broker assisteret logon
* Broker assisteret logon

#### <a name="non-broker-assisted-logins"></a>Ikke-broker assisteret logon

Ikke-broker assisteret logon er login oplevelser, der sker på linje med programmet og bruge det lokale lager på enheden for det pågældende program. Denne lagerplads kan deles på tværs af programmer, men legitimationsoplysningerne, der er tæt bundet til den app eller pakke med apps ved hjælp af legitimationsoplysninger. Dette er den oplevelse, du har sandsynligvis oplevede i mange mobilprogrammer, hvor du kan angive et brugernavn og din adgangskode selve programmet.

Disse logon har følgende fordele:

-  Brugeroplevelsen findes helt i programmet.
-  Legitimationsoplysninger kan deles på tværs af programmer, der er signeret af det samme certifikat, give en enkelt enkeltlogon-oplevelsen til din pakke med programmer.
-  Kontrolelementet omkring opleve logføring i er angivet til programmet, før og efter logon.

Disse logon har følgende ulemper:

- Brugere kan opleve single-sign-on på tværs af alle apps, der bruger en Microsoft-Identity kun på tværs af disse Microsoft Identities, som er dit program ejer og har konfigureret.
- Programmet kan ikke bruges med mere avancerede business funktioner som betinget adgang eller brug InTune pakke med produkter.
- Dit program understøtter ikke baseret certifikatgodkendelse for business-brugere.

Her er en repræsentation af, hvordan Microsoft identitet SDK'er fungerer med den delte lagringen af dine programmer til at aktivere SSO:

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| Azure SDK  | | Azure SDK  |  | Azure SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>Broker assisteret logon

Assisteret Broker logon er login oplevelser, der opstår i programmet broker og bruge lager og sikkerheden for broker til at dele legitimationsoplysninger på tværs af alle programmer på enheden, der udnytter Microsoft Identity platform. Det betyder, at dine programmer er afhængige af broker for at kunne logge brugere. På iOS og Android leveres disse via kan downloades programmer, kunder enten installere enkeltvis eller kan installeres på enheden af et firma, der administrerer enheden for deres bruger. Et eksempel af denne type af programmet er programmet Azure godkenderen på iOS. Denne funktionalitet findes i Windows i en konto, der er indbygget i operativsystemet, kendte teknisk som Web godkendelse Broker.
Oplevelsen varierer efter platform og nogle gange kan være forstyrrende for brugere Hvis de ikke administreres korrekt. Du er sandsynligvis mest fortrolig med dette mønster, hvis du har installeret Facebook-programmet og bruge Facebook Login funktionalitet i et andet program. Microsoft Identity platformen, der har det samme mønster.

Animation, hvor dit program der sendes til baggrunden, mens Azure godkenderen programmerne leveres til iOS fører til en "overgang" i forgrunden for brugeren at vælge den konto, de vil have til at logge på med.  

Konto er til Android og Windows vises oven på dit program, som er mindre forstyrrende for brugeren.

#### <a name="how-the-broker-gets-invoked"></a>Hvordan broker bliver aktiveret

Hvis en kompatible mægler er installeret på enheden, som programmet Azure godkenderen gøre Microsoft identitet SDK'er automatisk arbejdet for aktivering broker for dig, når en bruger angiver de ønsker at logge på ved hjælp af en konto fra Microsoft Identity platformen. Det kan være en personlig Microsoft-Account, et arbejdsnummer eller skolekonto eller en konto, du angiver og host i Azure ved hjælp af vores B2C og B2B produkter. Ved hjælp af meget sikker algoritmer og kryptering sikre vi, at legitimationsoplysningerne er bedt om at angive og leveret tilbage til dit program på en sikker måde. Den nøjagtige tekniske oplysninger om disse mekanismer udgives ikke, men er udviklet med samarbejde af æble- og Google.

**Udvikleren har mulighed for at vælge, hvis Microsoft identitet SDK kalder broker eller bruger ikke broker assisteret strømmen.** Men hvis udvikleren vælger ikke at bruge strømmen broker assisteret miste de fordelen ved at udnytte SSO legitimationsoplysninger, at brugeren måske allerede har tilføjet på enheden og forhindrer deres program i som bruges sammen med Microsoft yder sine kunders som betinget adgang, Intune administrationsfunktioner business-funktioner samt certifikat baseret på godkendelse.

Disse logon har følgende fordele:

-  Brugeren har SSO alle programmer uanset leverandøren.
-  Programmet kan udnytte mere avancerede business funktioner som betinget adgang eller brug InTune pakke med produkter.
-  Programmet kan understøtte baseret certifikatgodkendelse for business-brugere.
- Meget mere sikker logonoplevelse som på identiteten for programmet, og brugeren er godkendt af programmet broker med ekstra sikkerhedsalgoritmer og kryptering.

Disse logon har følgende ulemper:

- I iOS kildekørselstilstand brugeren af dit program oplevelse, mens legitimationsoplysninger er valgt.
- Tab af muligheden for at administrere login oplevelsen for kunder i dit program.



Her er en repræsentation af, hvordan Microsoft identitet SDK'er fungerer sammen med broker-programmer til at aktivere SSO:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
| Azure SDK  | | Azure SDK  |   | Azure SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+
```

Men med denne baggrundsoplysninger, du bør kunne bedre at forstå og implementere SSO i dit program ved hjælp af Microsoft Identity platform og SDK'er.


## <a name="enabling-cross-app-sso-using-adal"></a>Aktivere kryds-app SSO-Teknologier med ADAL

Her vi bruger ADAL iOS SDK til:

- Slå ikke broker assisteret SSO din pakke med apps
- Aktivere understøttelse af broker assisteret SSO

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Aktivering af SSO for ikke-broker assisteret SSO

Ikke-broker assisteret SSO på tværs af programmer administrere Microsoft identitet SDK'er for meget af SSO kompleksitet for dig. Dette omfatter at finde den rigtige bruger i cachen og vedligeholde en liste over logget på brugere for dig at forespørge på.

Hvis du vil aktivere SSO-programmer kan du ejer, skal du gøre følgende:

1. Sikre, at alle dine programmer bruger den samme klient-ID eller program-ID.
* Sørg for, at alle dine programmer deler det samme certifikat fra Apple, så du kan dele keychains
* Anmode om samme nøglering rettigheder for hver af dine programmer.
* Fortæl Microsoft identitet SDK'er om delte nøgleringen du have os til at bruge.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Ved hjælp af den samme klient-ID / program-ID for alle programmer i din pakke med apps

Hvis Microsoft Identity platform at vide, at det har tilladelse til at dele tokens på tværs af dine programmer, skal skal hver af dine programmer til at dele den samme klient-ID eller program-ID. Dette er det entydige id, som blev knyttet til dig, når du har registreret dit første program på portalen.

Du kan usikker på, hvordan du vil identificere forskellige apps til tjenesten Microsoft Identity, hvis det bruger den samme program-ID. Svaret er med **Omdirigere URI'er**. Hvert program kan have flere omdirigere URI'er, der er registreret i portalen onboarding. Hver app i din pakke har en anden omdirigering URI. Der er et eksempel på hvordan det ser ud under:

App1 Omdiriger URI:`x-msauth-mytestiosapp://com.myapp.mytestapp`

App2 Omdiriger URI:`x-msauth-mytestiosapp://com.myapp.mytestapp2`

App3 Omdiriger URI:`x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Disse er indlejret under den samme klient-ID / program-ID og baseret på Omdiriger URI du vender tilbage til os i konfigurationen af SDK har slået op.

```
+-------------------+
|                   |
|  Client ID        |
+---------+---------+
          |
          |           +-----------------------------------+
          |           |  App 1 Redirect URI               |
          +----------^+                                   |
          |           +-----------------------------------+
          |
          |           +-----------------------------------+
          +----------^+  App 2 Redirect URI               |
          |           |                                   |
          |           +-----------------------------------+
          |
          +----------^+-----------------------------------+
                      |  App 3 Redirect URI               |
                      |                                   |
                      +-----------------------------------+

```


*Bemærk, at formatet af disse omdirigere URI'er er beskrevet nedenfor. Du kan bruge en hvilken som helst omdirigere URI medmindre du ønsker at understøtte mægler, i hvilket tilfælde de skal se ud noget i retning af ovenstående*



#### <a name="create-keychain-sharing-between-applications"></a>Oprette nøglering dele mellem programmer

Aktivere nøgleringsdeling er ikke medtaget i dette dokument og dækket af Apple i deres dokument [Tilføje funktioner](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html). Hvad er vigtige er, at du beslutte, hvad du skal din nøglering, der skal ringes op og føje denne mulighed på tværs af alle dine programmer.

Når du har rettigheder, der er angivet korrekt du bør se en fil i projektmappen berettiger `entitlements.plist` , der indeholder noget, der ligner følgende:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

Når du har nøglering ret er aktiveret i hver af dine programmer, og du er klar til at bruge SSO, fortælle Microsoft identitet SDK om din nøglering ved hjælp af følgende indstilling i din `ADAuthenticationSettings` med de følgende indstillinger:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [AZURE.WARNING]
Når du deler en nøglering på tværs af dine programmer kan et program slette brugere eller dårligere slette alle tokens på tværs af dit program. Dette er særligt uoverskuelige, hvis du har programmer, der er afhænger af tokens til at baggrunden arbejde. Dele en nøglering fjerne betyder, at du skal være forsigtig i alle handlinger via Microsoft identitet SDK'er.

Det var det! Microsoft identitet SDK kan nu dele legitimationsoplysninger på tværs af alle dine programmer. Listen over brugere vil også blive delt på tværs af forekomster af tjenesteprogrammer.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Aktivering af SSO for broker assisteret SSO

Muligheden for, at et program kan bruge en hvilken som helst broker, der er installeret på enheden er **som standard slået fra**. For at bruge dit program med broker skal du foretage nogle yderligere konfiguration og føje kode til dit program.

Trinnene for at følge er:

1. Aktivere broker tilstand i din programkode opkald til MS SDK.
2. Få en ny omdirigering URI ved at indsende, både appen og din app registrering.
3. Registrere en URL-skema.
4. Understøttelse af iOS9: føje en tilladelse til filen info.plist.


#### <a name="step-1-enable-broker-mode-in-your-application"></a>Trin 1: Aktivere broker tilstand i dit program
Muligheden for, at dit program tilladelse til at bruge broker, der er slået til, når du opretter "kontekst" eller indledende installation af godkendelse objektet. Du kan gøre dette ved at angive dine legitimationsoplysninger type i din kode:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
Den `AD_CREDENTIALS_AUTO` indstillingen får Microsoft identitet SDK til at forsøge at foretage et opkald til broker `AD_CREDENTIALS_EMBEDDED` forhindrer Microsoft identitet SDK i opkald til broker.

#### <a name="step-2-registering-a-url-scheme"></a>Trin 2: Registrere en URL-skema
Microsoft Identity platform bruger URL-adresser til at kalde broker og derefter returnere kontrollen tilbage til dit program. For at afslutte returkørslen, du har brug for en URL-skema, der er registreret for dit program, som Microsoft Identity platform skal vide om. Det kan være ud over andre app-skemaer, du kan have tidligere registreret med dit program.

> [AZURE.WARNING]
Vi anbefaler, at URL-skema ret entydige for at minimere risikoen for en anden app ved hjælp af den samme URL-skema. Apple gennemtvinger ikke er entydige, URL-skemaer, der er registreret i app store.

Nedenfor er et eksempel på, hvordan dette vises i dit projekt konfiguration. Du kan også gøre dette i XCode samt:

```
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.myapp.mytestapp</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>x-msauth-mytestiosapp</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Trin 3: Oprette en ny omdirigering URI med URL-skema

For at sikre, at vi altid returnerer legitimationsoplysninger tokens til det korrekte program, har vi brug at sikre, at vi kalder tilbage til dit program på en måde, kan du kontrollere iOS-operativsystem. Operativsystemet iOS rapporter til Microsoft broker programmer programmet kalder samlet ID. Dette kan ikke efterlignes af en uautoriseret program. Derfor udnytte vi dette sammen med URI af vores broker programmet for at sikre, at tokens returneres til den korrekte anvendelse. Vi kræver, at du at få denne entydige omdirigering URI begge i dit program ved at angive som en omdirigere URI i vores udvikler portal.

Omdirigeringen URI skal være i form af et stort:

`<app-scheme>://<your.bundle.id>`

ex: *x-msauth-mytestiosapp://com.myapp.mytestapp*

Denne omdirigere URI skal angives i din app registrering ved hjælp af [Azure klassisk portal](https://manage.windowsazure.com/). Du kan finde flere oplysninger om Azure AD app registrering, [integration med Azure Active Directory](./develop/active-directory-how-to-integrate.md).


##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Trin 3a: tilføje en omdirigering URI i din app og Udviklingscenter portal til at understøtte baseret certifikatgodkendelse

For at understøtte cert baseret godkendelse af en anden "msauth" skal være registreret i dit program og [Azure klassisk portal](https://manage.windowsazure.com/) til at håndtere certifikatgodkendelse, hvis du vil tilføje, der understøtter i programmet.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

ex: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*


#### <a name="step-4-ios9-add-a-configuration-parameter-to-your-app"></a>Trin 4: iOS9: føje en konfigurationsparameter til din app

ADAL bruger – canOpenURL: Hvis broker, der er installeret på enheden. I iOS låst 9 Apple hvad farveskemaer et program kan forespørge efter. Du skal føje "msauth" til afsnittet LSApplicationQueriesSchemes i din `info.plist file`.

<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>

### <a name="youve-configured-sso"></a>Du har konfigureret SSO!

Nu skal Microsoft identitet SDK automatisk både dele legitimationsoplysninger på tværs af dine programmer og aktivere broker, hvis den findes på deres enhed.
