<properties
    pageTitle="Azure MFA logon-oplevelse med Azure Multi-Factor Authentication"
    description="Denne side giver dig vejledning under Sådan kommer du få vist de forskellige log-metoder, der er tilgængelige med Azure MFA."
    keywords="brugergodkendelse, logonoplevelse, logge på med mobiltelefon, logge på med arbejdstelefon"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="kgremban"/>

# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>Log på oplevelse med Azure Multi-Factor Authentication
> [AZURE.NOTE]  Følgende dokumentation findes på denne side viser en typisk logonoplevelse.  Få hjælp med at logge på under [har du problemer med Azure Multi-Factor Authentication](multi-factor-authentication-end-user-manage-settings.md)



## <a name="what-will-your-sign-in-experience-be"></a>Hvad er dit logonnavn oplevelse?
Afhængigt af hvordan du logger på og bruge Multi-Factor authentication, varierer din oplevelse.  I dette afsnit giver vi oplysninger om, hvad du kan forvente, når du logger på.  Vælg det, der bedst beskriver, hvad du laver:


Hvad laver du?|Beskrivelse
:------------- | :------------- |
[Logge på med mobiltelefon eller office](#signing-in-with-mobile-or-office-phone) | Dette er, hvad du kan forvente i at logge på ved hjælp af din mobiltelefon eller office-telefon.
[Logge på med Microsoft Authenticator appen med meddelelse](#signing-in-with-the-microsoft-authenticator-app-using-notification) | Dette er, hvad du kan forvente ved hjælp af appen Microsoft Authenticator med meddelelser.
[Logge på med Microsoft Authenticator appen med verifikationskoden](#signing-in-with-the-microsoft-authenticator-app-using-verification-code)|Dette er, hvad du kan forvente ved hjælp af Microsoft Authenticator thapp med en bekræftelseskode.
[Logge på med en anden metode](#signing-in-with-an-alternate-method)|Her kan du se hvad du kan forvente, hvis du vil bruge en anden metode.

## <a name="signing-in-with-mobile-or-office-phone"></a>Logge på med mobiltelefon eller office

Følgende oplysninger som beskriver oplevelsen ved at bruge Multi-Factor authentication med din mobiltelefon eller office telefon.

### <a name="to-sign-in-with-a-call-to-your-office-or-mobile-phone"></a>Logge på med et opkald til din office eller mobiltelefon

- Log på med et program eller en tjeneste såsom Office 365 med dit brugernavn og din adgangskode.
- Microsoft ringer dig.

![Microsoft opkald](./media/multi-factor-authentication-end-user-signin-phone/call.png)

- Svar på telefonen, og tryk på tasten #.

![Svar](./media/multi-factor-authentication-end-user-signin-phone/phone.png)

- Du skulle nu være logget på.</li>

## <a name="signing-in-with-the-microsoft-authenticator-app-using-notification"></a>Logge på med Microsoft Authenticator appen med meddelelse

Følgende oplysninger som beskriver oplevelsen ved at bruge Multi-Factor authentication med Microsoft Authenticator app, når du er sendt en meddelelse.

### <a name="to-sign-in-with-a-notification-sent-the-microsoft-authenticator-app"></a>Hvis du vil logge på med en meddelelse sendes appen Microsoft Authenticator

- Log på med et program eller en tjeneste såsom Office 365 med dit brugernavn og din adgangskode.
- Microsoft sender en meddelelse.

![Microsoft sender besked](./media/multi-factor-authentication-end-user-signin-app-notify/notify.png)


- Svar på telefonen, og tryk på tasten Bekræft.  Hvis din virksomhed kræver en pinkode bliver du spurgt, for at det her.

![Bekræfte](./media/multi-factor-authentication-end-user-signin-app-notify/phone.png)

![Konfiguration](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

- Du skulle nu være logget på.


## <a name="signing-in-with-the-microsoft-authenticator-app-using-verification-code"></a>Logge på med Microsoft Authenticator appen med verifikationskoden

Følgende oplysninger som beskriver oplevelsen ved at bruge Multi-Factor authentication med Microsoft Authenticator app, når du bruger den med en bekræftelseskode.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Logge på ved hjælp af en bekræftelseskode med Microsoft Authenticator-app

- Log på med et program eller en tjeneste såsom Office 365 med dit brugernavn og din adgangskode.
- Microsoft beder dig om en bekræftelseskode.

![Angive bekræftelseskode](./media/multi-factor-authentication-end-user-signin-app-verify/verify.png)

- Åbn Microsoft Authenticator appen på din telefon, og Angiv koden i det felt, hvor du logger på.

![Få kode](./media/multi-factor-authentication-end-user-signin-app-verify/phone.png)



- Du skulle nu være logget på.


## <a name="signing-in-with-an-alternate-method"></a>Logge på med en anden metode


Følgende afsnit viser dig, hvordan at logge på med en anden metode, når din primære metode ikke muligvis tilgængelig.

### <a name="to-sign-in-with-an-alternate-method"></a>Logge på med en anden metode

- Log på med et program eller en tjeneste såsom Office 365 med dit brugernavn og din adgangskode.
- Vælg Brug en anden bekræftelsesmetode.  Du kan præsentere med et udvalg af forskellige indstillinger. Det tal der vises, er baseret på hvor mange du har konfigureret.

![Brug alternativ metode](./media/multi-factor-authentication-end-user-signin-alt/alt.png)

- Vælg en anden metode og logge på.
