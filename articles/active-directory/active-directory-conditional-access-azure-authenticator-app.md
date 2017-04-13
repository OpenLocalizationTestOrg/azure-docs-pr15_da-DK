
<properties
    pageTitle="Azure godkenderen til Android | Microsoft Azure"
    description="Microsoft Azure godkenderen app kan bruges til at logge på på få adgang til arbejdsressourcer. Azure godkenderen app giver dig besked om en anmodning om ventende to-faktor-godkendelse ved at vise en besked til din mobilenhed."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="azure-authenticator-for-android"></a>Azure godkenderen til Android

IT-administratoren kan have anbefalet du kan bruge Microsoft Azure godkenderen for at logge på at få adgang til din arbejdsressourcer. Dette program indeholder disse to logonindstillinger:

* Multi-Factor Authentication giver dig mulighed at sikre dine arbejds- eller skolekonto konti med totrinsbekræftelse. Du logger på ved hjælp af noget du kender (for eksempel, din adgangskode) og beskytte kontoen yderligere med noget, du har (en sikkerhedsnøgle fra denne app). Azure godkenderen app giver dig besked om en anmodning om ventende to-faktor-godkendelse ved at vise en besked til din mobilenhed. Du skal blot vise anmodningen i appen og trykke på bekræfte eller annullere. Alternativt kan du muligvis bedt om at indtaste den adgangskode, der vises i appen.

* Arbejdskonto kan du omdanne din Android-telefon eller tablet til en der er tillid til enhed og levere enkelt Sign-On (SSO) til virksomhedens programmer. IT-administratoren kan kræve, at du kan tilføje en arbejdskonto for at få adgang til ressourcer i virksomheden. SSO kan du logge på én gang og benytte automatisk ved at logge på alle programmer, dit firma har gjort tilgængelige for dig.

## <a name="installing-the-azure-authenticator-app"></a>Installation af appen Azure godkenderen

Du kan installere Azure godkenderen appen fra Google Play Store.
Vejledningen for at tilføje arbejdskonto fra Samsung Android-enhed eller en ikke - Samsung Android-enhed er en smule anderledes. Instruktioner til begge vises nedenfor.

<a name="adding-the-work-account-from-samsung-android-device"></a>Tilføje kontoen arbejde fra Samsung Android-enhed
----------------------------------------------------------------------------------------------------------------
###<a name="adding-the-work-account-through-the-app-home-screen"></a>Tilføje arbejdskonto gennem app startskærmen

Følgende fremgangsmåde gælder til Samsung GS3 og oven over telefoner eller Bemærk 2 og over tablets.

1. På startskærmen i appen skal du acceptere slutbrugerlicensaftale (EULA).
2. På skærmbilledet aktivere konto skal du klikke på genvejsmenuen til højre og vælge **arbejdskonto**.
3. Vælg** Arbejdskonto**på skærmbilledet Tilføj konto.
4. Aktivér enhed administrator skærmen, klik på **Aktivér**.
5. Markér afkrydsningsfeltet skærmen beskyttelse af personlige oplysninger, og klik på **Bekræft**.
6. Angiv leveres af din organisation bruger-id på skærmbilledet arbejdsplads deltage i og klikke på **Deltag**.
7. For at logge på appen Azure godkenderen, angive dine egne en *** konto og adgangskode, og klik på **Log på**.
8. Det næste skærmbillede, der viser oplysninger om Multi-Factor authentication (MFA) er for ekstra sikkerhed og er valgfrit. Du kan se dette skærmbillede, hvis dit arbejde eller skole kræver andet faktor-godkendelse til oprettelse af arbejdskonto. Den indeholder en vejledning til yderligere Bekræft din konto.
9. Skærmbilledet arbejdsplads Deltag viser meddelelsen "**deltage i din arbejdsplads**". Azure godkenderen app er forsøger at tilslutte sig din enhed til din arbejdsplads.
10. Du bør se meddelelsen arbejdsplads tilsluttet sig på det næste skærmbillede.

>[AZURE.NOTE]
> Du kan en enkelt arbejdskonto på din enhed.

### <a name="adding-the-work-account-from-the-settings-menu"></a>Tilføje arbejde kontoen fra menuen Indstillinger
Når du har installeret appen Azure godkenderen, kan du også oprette en arbejdskonto fra Android Account Manager.

1. Gå til **konti** i menuen Indstillinger, og klik på **Tilføj konto**.
2. Følg trin 3-10 i fremgangsmåden, tilføje arbejdskonto gennem app startskærmen, du tilføjer en arbejdskonto.

<a name="adding-the-work-account-from-a-non-samsung-android-device"></a>Tilføje arbejdskonto fra en ikke - Samsung Android-enhed
------------------------------------------------------------------------------------------------------------------
### <a name="adding-the-work-account-through-the-app-home-screen"></a>Tilføje arbejdskonto gennem app startskærmen

1. På startskærmen i appen skal du acceptere slutbrugerlicensaftale (EULA).
2. På skærmbilledet aktivere konto skal du klikke på genvejsmenuen til højre og vælge **arbejdskonto**.
3. På skærmbilledet konti skal du klikke på **Tilføj konto**.
4. Hvis du får vist skærmbilledet konti, skal du klikke på **Tilføj konto**. Hvis en arbejdskonto er allerede oprettet tidligere, vises der en synkronisering-skærm, der viser den nuværende arbejdskonto. Du kan bevare arbejdskonto ved blot at trykke på pil tilbage til startskærmen. Alternativt kan du vælge kontoen for at fjerne og genskabe en ny arbejds-konto på feltet arbejdsplads-skærmbilledet Deltag, Angiv det bruger-id leveres af din organisation og klikke på Deltag.
5. Skriv din organisationskonto og adgangskode for at logge på appen Azure godkender, og klik på **Log på**.
7. Det næste skærmbillede, der viser oplysninger om Multi-Factor authentication (MFA) er for ekstra sikkerhed og er valgfrit. Du kan se dette skærmbillede, hvis dit arbejde eller skole kræver andet faktor-godkendelse til oprettelse af arbejdskonto. Den indeholder en vejledning til yderligere Bekræft din konto.
8. Klik på **OK** på det næste skærmbillede. Undlad at ændre navnet på certifikatet.
meddelelsen "Deltage i din arbejdsplads". Azure godkenderen app er forsøger at tilslutte sig din enhed til din arbejdsplads.
Du bør se meddelelsen arbejdsplads tilsluttet sig på det næste skærmbillede.

>[AZURE.NOTE]
> Du kan en enkelt arbejdskonto på din enhed.

Når du har installeret appen Azure godkenderen, kan du også oprette en arbejdskonto fra Android Account Manager.

1. Gå til konti i menuen **Indstillinger** , og klik på **Tilføj konto**.
2. Følg trin 2-7 i fremgangsmåden, tilføje arbejdskonto gennem den app privat skærmen **, hvis du vil tilføje en arbejdskonto.

### <a name="how-to-find-out-which-version-is-installed"></a>Sådan finder du ud af, hvilken version er installeret

1. Du kan finde ud af, hvilken version af Azure godkenderen app og tilknyttede tjeneste versioner er installeret på din enhed.
2. Klik på **om**pop op-menuen.
3. Skærmbilledet om viser tjenesterne over appen og de versioner, der er installeret på din enhed.
 
### <a name="sending-log-files-to-report-issues"></a>Sende logfiler til rapportere problemer

1. Følg vejledningen på Microsoft Support for at rapportere en hændelse med Azure godkenderen app, få en hændelsen tal og sende logfiler mod tildelte hændelsen nummeret på følgende måde:
2. Pop op-menuen, klik på **logføring**.
3. Hvis du har en åben hændelse med Microsoft Support, noter hændelsen antallet (du skal bruge den til en nyere trin). Hvis du ikke allerede har oprettet en support-hændelse og gerne vil have hjælp, skal du følge vejledningen i [Microsoft Support](https://support.microsoft.com/en-us/contactus) for at åbne en ny hændelse.
4. Klik på **Send nu**på skærmbilledet logføring.
5. Vælg den mailudbyder, du vil bruge.
7. Hvis du allerede har en åben Microsoft Support-hændelse, kan du kontakte den supporttekniker, der er knyttet til dit problem at finde ud af, hvordan du kan sende dataene i logfilen og har den der er knyttet til din hændelse. Understøttelse af engineering giver dig oplysningerne om den e-mail-adresse og emne. Hvis du ikke allerede har en support-hændelse, skal du følge vejledningen i Microsoft Support for at åbne en ny hændelse.
9. Redigere linjen **til** og **emnelinjen med de oplysninger, du har modtaget fra Microsoft Support** .
10. Azure godkenderen app vedhæfter logfilen i den mail, du sender. Beskriver det problem, du oplever, opdatere modtagerlisten (valgfrit), og send derefter mailen.

### <a name="deleting-the-work-account-and-leaving-your-workplace"></a>Slette arbejdskonto og efterlade dit arbejdsområde

Du kan fjerne kontoen arbejde, du har oprettet når som helst på følgende måde:

**Sådan slettes kontoen arbejde fra menuen Indstillinger**

1. Vælg **arbejdskonto**manager konti.
2. Vælg **Kontoindstillinger – Lad dit firmanetværk**under **Generelle indstillinger**på skærmbilledet arbejdskonto.
3. Vælg **Forlad** på skærmen **På arbejdspladsen deltage i** .
4. Klik på **OK** , når meddelelsen "Er du sikker på, at du vil lade på arbejdspladsen" vises.
5. Dette sikrer, at du har slettet din arbejdskonto fra arbejdspladsen.

>[AZURE.NOTE]
>Det anbefales, at du ikke bruge indstillingen Fjern konto til at slette en arbejdskonto, som denne indstilling ikke fungerer muligvis i tidligere versioner af Android.

##<a name="uninstalling-the-app"></a>Hvis du fjerner appen

På en Samsung Android-enhed enhed administratorrettigheder skal fjernes således inden du fjerner den 
1. Vælg **sikkerhed**under **Indstillinger**, under **System**.
2. D**evice Administration**, klikke på **administratorer af enhed**. Sørg for, at markeringen i afkrydsningsfeltet ud for **Azure godkender** .

##<a name="troubleshooting"></a>Fejlfinding i forbindelse med

Hvis du får vist **Keystore fejl**, kan dette være, fordi du ikke har låsen skærmbilledet sæt op med en pinkode. Du kan løse dette problem ved fjerne Azure godkenderen appen, konfigurere en pinkode til din låseskærm og geninstallere appen.
