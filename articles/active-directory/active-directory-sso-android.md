<properties
    pageTitle="Sådan aktiveres kryds-app SSO på Android-enhed ved hjælp af ADAL | Microsoft Azure"
    description="Sådan bruges funktionerne af ADAL SDK til at aktivere Single Sign-On på tværs af dine programmer. "
    services="active-directory"
    documentationCenter=""
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>


# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Sådan aktiveres kryds-app SSO på Android-enhed ved hjælp af ADAL


Forudsat at enkelt enkeltlogon (SSO), så brugerne kun skal indtaste deres legitimationsoplysninger én gang og har disse legitimationsoplysninger automatisk arbejde på tværs af forventes programmer nu af kunder. Problemer med at indtaste deres brugernavn og din adgangskode på en lille skærm, ofte gange kombineres med en anden faktor (2FA) som et telefonopkald eller en texted kode resultater i Hurtig utilfredshed, hvis en bruger har at gøre dette mere end én gang for dit produkt.

Desuden, hvis du udnytte en identitet platform, som andre programmer kan bruge som Microsoft Accounts eller en arbejdskonto fra Office 365, forvente kunderne, at de legitimationsoplysninger, der bruges på tværs af alle programmer uanset leverandøren.

Microsoft Identity platformen, sammen med vores Microsoft identitet SDK'er gør alt dette hårde arbejde for dig og giver dig mulighed for at begejstre dine kunder med SSO enten inden for din egen pakke med programmer eller, som med vores broker egenskab og godkenderen programmer, på tværs af hele enheden.

Denne gennemgang fortæller dig, hvordan du konfigurerer vores SDK i dit program at tildele til dine kunder.

Denne gennemgang gælder for:

* Azure Active Directory
* Azure Active Directory B2C
* Azure Active Directory B2B
* Azure Active Directory betinget adgang

Bemærk, at dokumentet nedenfor antages det, du har viden om, hvordan til [klargøring programmer i portalen ældre til Azure Active Directory](./develop/active-directory-how-to-integrate.md) samt har integreret dit program med [Microsoft identitet Android SDK](https://github.com/AzureAD/azure-activedirectory-library-for-android).

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

Hvis en kompatible mægler er installeret på enheden, som programmet Azure godkenderen gøre Microsoft identitet SDK'er automatisk arbejdet for aktivering broker for dig, når en bruger angiver de ønsker at logge på ved hjælp af en konto fra Microsoft Identity platformen. Det kan skyldes en en personlig Microsoft-Account, en arbejds- eller skolekonto eller en konto, du leverer og være vært for i Azure ved hjælp af vores B2C og B2B produkter. Ved hjælp af meget sikker algoritmer og kryptering sikre vi, at legitimationsoplysningerne er bedt om at angive og leveret tilbage til dit program på en sikker måde. Den nøjagtige tekniske oplysninger om disse mekanismer udgives ikke, men er udviklet med samarbejde af æble- og Google.

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

Her skal vi bruge ADAL Android SDK, til at:

- Slå ikke broker assisteret SSO din pakke med apps
- Aktivere understøttelse af broker assisteret SSO


### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Aktivering af SSO for ikke-broker assisteret SSO

Ikke-broker assisteret SSO på tværs af programmer administrere Microsoft identitet SDK'er for meget af SSO kompleksitet for dig. Dette omfatter at finde den rigtige bruger i cachen og vedligeholde en liste over logget på brugere for dig at forespørge på.

Hvis du vil aktivere SSO-programmer kan du ejer, skal du gøre følgende:

1. Sikre, at alle dine programmer bruger den samme klient-ID eller program-ID.
* Sikre, at alle dine programmer har det samme sæt SharedUserID.
* Sørg for, at alle dine programmer deler det samme certifikat fra Google Play store, så du kan dele lagerplads.

#### <a name="step-1-using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Trin 1: Brug den samme klient-ID / program-ID for alle programmer i din pakke med apps

Hvis Microsoft Identity platform at vide, at det har tilladelse til at dele tokens på tværs af dine programmer, skal skal hver af dine programmer til at dele den samme klient-ID eller program-ID. Dette er det entydige id, som blev knyttet til dig, når du har registreret dit første program på portalen.

Du kan usikker på, hvordan du vil identificere forskellige apps til tjenesten Microsoft Identity, hvis det bruger den samme program-ID. Svaret er med **Omdirigere URI'er**. Hvert program kan have flere omdirigere URI'er, der er registreret i portalen onboarding. Hver app i din pakke har en anden omdirigering URI. Der er et eksempel på hvordan det ser ud under:

App1 Omdiriger URI:`msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D`

App2 Omdiriger URI:`msauth://com.example.userapp1/KmB7PxIytyLkbGHuI%2UitkW%2Fejk%4E`

App3 Omdiriger URI:`msauth://com.example.userapp2/Pt85PxIyvbLkbKUtBI%2SitkW%2Fejk%9F`

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


#### <a name="step-2-configuring-shared-storage-in-android"></a>Trin 2: Konfigurere delt storage i Android

Angive den `SharedUserID` er ikke medtaget i dokumentet, men kan være lært ved at læse i dokumentationen til Google Android på [manifestet](http://developer.android.com/guide/topics/manifest/manifest-element.html). Hvad er vigtige er, at du bestemme, hvad din sharedUserID bliver kaldt og bruge, på tværs af alle dine programmer.

Når du har den `SharedUserID` i alle dine programmer er du klar til at bruge SSO.

> [AZURE.WARNING]
Når du deler en lagerplads på tværs af dine programmer kan et program slette brugere eller dårligere slette alle tokens på tværs af dit program. Dette er særligt uoverskuelige, hvis du har programmer, der er afhænger af tokens til at baggrunden arbejde. Dele lagerplads betyder, at du skal være forsigtig i alle Fjern handlinger via Microsoft identitet SDK'er.

Det var det! Microsoft identitet SDK kan nu dele legitimationsoplysninger på tværs af alle dine programmer. Listen over brugere vil også blive delt på tværs af forekomster af tjenesteprogrammer.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Aktivering af SSO for broker assisteret SSO

Muligheden for, at et program kan bruge en hvilken som helst broker, der er installeret på enheden er **som standard slået fra**. For at bruge dit program med broker skal du foretage nogle yderligere konfiguration og føje kode til dit program.

Trinnene for at følge er:

1. Aktivere broker tilstand i din programkode opkald til MS SDK
2. Få en ny omdirigering URI ved at indsende, både appen og din app-registrering
3. Konfiguration af de rette tilladelser i Android manifestet


#### <a name="step-1-enable-broker-mode-in-your-application"></a>Trin 1: Aktivere broker tilstand i dit program
Muligheden for, at dit program tilladelse til at bruge broker, der er slået til, når du opretter "indstillinger" eller indledende installation af din godkendelse forekomst. Du kan gøre dette ved at angive din ApplicationSettings type i din kode:

```
AuthenticationSettings.Instance.setUseBroker(true);
```


#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>Trin 2: Oprette en ny omdirigering URI med URL-skema

For at sikre, at vi altid returnerer legitimationsoplysninger tokens til det korrekte program, har vi brug at sikre, at vi kalder tilbage til dit program på en måde, kan du kontrollere Android operativsystemet. Android operativsystemet bruger hashværdien af certifikatet i Google Play store. Dette kan ikke efterlignes af en uautoriseret program. Derfor udnytte vi dette sammen med URI af vores broker programmet for at sikre, at tokens returneres til den korrekte anvendelse. Vi kræver, at du at få denne entydige omdirigering URI begge i dit program ved at angive som en omdirigere URI i vores udvikler portal.

Omdirigeringen URI skal være i form af et stort:

`msauth://packagename/Base64UrlencodedSignature`

ex: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

Denne omdirigere URI skal angives i din app registrering ved hjælp af [Azure klassisk portal](https://manage.windowsazure.com/). Du kan finde flere oplysninger om Azure AD app registrering, [integration med Azure Active Directory](./develop/active-directory-how-to-integrate.md).


#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>Trin 3: Opsætning af de rette tilladelser i dit program

Vores broker programmet i Android bruger funktionen Accounts Manager i Android OS til at administrere legitimationsoplysninger på tværs af programmer. For at bruge broker i Android skal din app manifestet have tilladelse til at bruge AccountManager konti. Det er beskrevet i detaljer i [Google dokumentationen til Account Manager her] (http://developer.android.com/reference/android/accounts/AccountManager.html)

Især, er disse tilladelser:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>Du har konfigureret SSO!

Nu skal Microsoft identitet SDK automatisk både dele legitimationsoplysninger på tværs af dine programmer og aktivere broker, hvis den findes på deres enhed.
