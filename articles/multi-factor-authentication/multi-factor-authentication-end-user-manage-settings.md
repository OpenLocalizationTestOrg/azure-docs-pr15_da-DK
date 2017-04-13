<properties
    pageTitle="Administrere indstillingerne for to-trins bekræftelse | Microsoft Azure"
    description="Administrere, hvordan du bruger Azure Multi-Factor Authentication, herunder ændre dine kontaktoplysninger eller konfiguration af dine enheder."
    services="multi-factor-authentication"
    keywords = "multifaktor-godkendelse klient, godkendelse problem korrelations-ID"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="manage-your-settings-for-two-step-verification"></a>Administrere dine indstillinger for totrinsbekræftelse

I denne artikel finder du svar på spørgsmål om, hvordan du opdatere indstillinger for totrinsgodkendelse bekræftelse eller multi-Factor. Hvis du har problemer med at logge din konto, kan du se i [har du problemer med totrinsbekræftelse](multi-factor-authentication-end-user-troubleshoot.md) for hjælp til fejlfinding.


## <a name="where-to-find-the-settings-page"></a>Sådan finder du siden med indstillinger
Afhængigt af hvordan din virksomhed konfigureret Azure Multi-Factor Authentication, er der nogle steder, hvor du kan ændre dine indstillinger som dit telefonnummer.

Hvis din IT-administratoren sendt ud af en bestemt URL-adresse eller trin til at administrere totrinsbekræftelse, skal du følge disse instruktioner. De følgende instruktioner skal ellers fungerer for alle øvrige deltagere. Hvis du følge disse trin, men ikke kan se de samme indstillinger, betyder det, at dit arbejde eller skole tilpasset deres egne portal. Bed din administrator for linket til din Azure Multi-Factor Authentication portal.


1. Log på [https://myapps.microsoft.com](https://myapps.microsoft.com)  
2. Øverst skal du vælge **profil**.  
3. Markér **ekstra sikkerhed bekræftelse**.  

    ![Mine Apps](./media/multi-factor-authentication-end-user-manage/myapps1.png)

4. På siden for ekstra sikkerhedskontrol indlæser med dine indstillinger.

    ![Proofup](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)


## <a name="i-want-to-change-my-phone-number-or-add-a-secondary-number"></a>Jeg vil ændre mit telefonnummer eller tilføje en sekundær tal

Det er vigtigt at konfigurere en sekundær godkendelse telefonnummer.  Fordi primære telefonnummer og din-mobilappen er sandsynligvis på den samme telefonlinje, er sekundær telefonnummeret den eneste måde, du vil kunne gå tilbage til din konto, hvis din telefon går tabt eller stjæles.

> [AZURE.NOTE]
> Hvis du ikke har adgang til din primære telefonnummer, og har brug for hjælp til at hente din konto, kan du se vores emner i Hjælp i [har du problemer med totrinsbekræftelse](multi-factor-authentication-end-user-troubleshoot.md).

**Sådan ændrer du din primære telefonnummer:**  

1. Vælg tekstfeltet med dit aktuelle telefonnummer på bekræftelsessiden ekstra sikkerhed og redigere den med din nyt telefonnummer.  
2. Vælg **Gem**.  
3. Hvis dette er det nummer, du bruger til din foretrukne bekræftelsesindstilling, er det nødvendigt at kontrollere tallet for den nye, før du kan gemme den.  


**Sådan tilføjer du et alternativt telefonnummer:**  

1. På siden kontrol af ekstra sikkerhed, Markér afkrydsningsfeltet ud for **alternative godkendelse telefon.**  
2. Angiv telefonnummeret sekundær i tekstfeltet.  
3. Vælg **Gem** og dine ændringer er afsluttet.  


## <a name="how-do-i-clean-up-microsoft-authenticator-from-my-old-device-and-move-to-a-new-one"></a>Hvordan rydde op i Microsoft Authenticator fra min gamle enhed og flytte til en ny?
Når du fjerner appen fra din enhed eller nulstille enheden, fjerner det ikke aktiveringen på back end. Du skal bruge trinnene beskrevet i [flytte til en ny enhed](multi-factor-authentication-microsoft-authenticator.md#how-to-move-to-the-new-microsoft-authenticator-app).

## <a name="next-steps"></a>Næste trin
- Få tip til fejlfinding og hjælp på [har du problemer med totrinsbekræftelse](multi-factor-authentication-end-user-troubleshoot.md)
- Konfigurere [appadgangskoder](multi-factor-authentication-end-user-app-passwords.md) for alle apps, som ikke understøtter totrinsbekræftelse.
