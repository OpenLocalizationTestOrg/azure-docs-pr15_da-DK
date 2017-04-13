<properties
    pageTitle="Microsoft Authenticator app til mobiltelefoner | Microsoft Azure"
    description="Lær at opgradere til den nyeste version af Azure godkender."
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

# <a name="microsoft-authenticator"></a>Microsoft godkenderen

Appen Microsoft Authenticator giver en ekstra sikkerhed i Azure-kontoen (for eksempel bsimon@contoso.onmicrosoft.com), dine lokale arbejdskonto (for eksempel bsimon@contoso.com), eller din Microsoft-konto (for eksempel bsimon@outlook.com).

Appen fungerer på en af to måder:

- **Meddelelse om**. Appen kan hjælpe dig med at forhindre uautoriseret adgang til konti og stop falsk transaktioner ved at trykke på en meddelelse til din smartphone eller tablet. Blot se meddelelsen, og hvis det er gyldige, Vælg **Bekræft**. Ellers kan du vælge **Afvis**. Finde oplysninger om afvisning af meddelelser, se, hvordan du bruger funktionen Afvis og rapport svindel til multi-Factor Authentication.

- **Adgangskode med verifikationskoden**. Appen kan bruges som en software-token til at oprette en OAuth verifikationskoden. Du angiver den kode, der leveres af appen i skærmbilledet logon, sammen med det brugernavn og adgangskode, når du bliver bedt om. Verifikationskoden indeholder en anden form for godkendelse.

Med versionen af Microsoft Authenticator app, der skal erstattes den gamle app Azure godkender.  Azure godkenderen app fortsat fungerer, men hvis du beslutter at flytte til den nye Microsoft Authenticator app, i denne artikel kan hjælpe dig.  

## <a name="install-the-app"></a>Installere appen

Microsoft Authenticator app er tilgængelig til [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)og [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## <a name="add-accounts-to-the-app"></a>Tilføje konti til appen

Bruge en af følgende procedurer for hver konto, du vil føje til appen Microsoft Authenticator.

### <a name="add-an-account-to-the-app-by-using-the-qr-code-scanner"></a>Tilføje en konto til appen ved hjælp af QR-kode scanner

1. Gå til skærmbilledet sikkerhed bekræftelse.  Se [ændre sikkerhedsindstillingerne](multi-factor-authentication-end-user-manage-settings.md)for at få oplysninger om, hvordan du få adgang til denne skærm.

2. Vælg **konfigurere**.

    ![Knappen Konfigurer på skærmen indstillinger til sikkerhed bekræftelse](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

    Dette viser en skærm med en QR-kode på den.

    ![Skærmbillede, der indeholder QR-koden](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

3. Åbn appen Microsoft godkender. På skærmbilledet **konti** skal du vælge **+**, og derefter angive, at du vil føje en arbejds- eller skolekonto.

    ![Skærmbilledet konti med plustegnet (+)](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

    ![Skærmen for at angive arbejds-eller skolekonto](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

4. Bruge kameraet til scan QR-koden, og vælg derefter **udført** at lukke skærmbilledet QR-kode.

    ![Skærmen for at scanne en QR-kode](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

    Hvis dit kamera ikke fungerer korrekt, kan du angive QR-kode og URL-adressen manuelt. Yderligere oplysninger finder du se [tilføje en konto til appen manuelt](#add-an-account-to-the-app-manually).

5. Vent, mens kontoen er aktiveret. Når aktivering afsluttes, skal du vælge **Kontakt mig**.  Dette sender en meddelelse eller en bekræftelseskode til din telefon.  Vælg **Bekræft**.

    ![Skærmbillede, hvor du kan vælge Bekræft til at logge på](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

6. Hvis din virksomhed kræver en pinkode til godkendelse af bekræftelse-logon, skal du angive den.

    ![Feltet for at angive en pinkode](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

7. Når pinkoden er fuldført, skal du vælge **Luk**. På dette tidspunkt skal din bekræftelse være fuldført.
8. Vi anbefaler, at du angiver dit mobiltelefonnummer i tilfælde af, at du miste adgangen til din app. Angiv dit land fra på rullelisten, og Angiv dit mobiltelefonnummer i feltet ud for landenavn. Klik på **Næste**.
9. På dette tidspunkt, har du konfigureret din kontaktmetode. Nu er det tid til at konfigurere appadgangskoder til ikke-browser-apps, som Outlook 2010 eller ældre. Hvis du ikke bruger disse apps skal du vælge **udført**. Ellers skal du fortsætte til næste trin.

    ![Skærmen til at oprette en appadgangskode](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

10. Hvis du bruger ikke browserapps, skal du kopiere den medfølgende appadgangskode og indsætte adgangskoden i dine apps. Se hvordan du ændrer din adgangskode i din mail til appadgangskoden og hvordan du kan ændre adgangskoden i dit program til appadgangskoden trin til individuelle apps som Outlook og Lync.
11. Vælg **udført**.

Du bør nu se den nye konto på skærmbilledet **konti** .

![Skærmbillede af konti](./media/multi-factor-authentication-azure-authenticator/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Tilføje en konto til appen manuelt

1. Gå til skærmbilledet sikkerhed bekræftelse.  Se [ændre sikkerhedsindstillingerne](multi-factor-authentication-end-user-manage-settings.md)for at få oplysninger om, hvordan du få adgang til denne skærm.

2. Vælg **konfigurere**.

    ![Knappen Konfigurer på skærmen indstillinger til sikkerhed bekræftelse](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

    Dette viser en skærm med en QR-kode på den.  Bemærk kode og URL-adresse.

    ![Skærmbillede, der indeholder QR-kode og URL-adresse](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

3. Åbn appen Microsoft godkender. På skærmbilledet **konti** skal du vælge **+**, og derefter angive, at du vil føje en arbejds- eller skolekonto.

    ![Skærmbilledet konti med plustegnet (+)](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

    ![Skærmen for at angive arbejds-eller skolekonto](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

4. Vælg scanneren, **angive koden manuelt**.

    ![Skærmen for at scanne en QR-kode](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

5. Angiv koden og URL-adressen i de relevante felter i appen.

    ![Skærmen til indtastning af koden og URL-adresse](./media/multi-factor-authentication-azure-authenticator/manual.png)

    ![Skærmen til indtastning af koden og URL-adresse](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

6. Vent, mens kontoen er aktiveret. Når aktiveringen afsluttes, skal du vælge **Kontakt mig**. Dette sender en meddelelse eller en bekræftelseskode til din telefon. Vælg **Bekræft**.

Du bør nu se den nye konto på skærmbilledet **konti** .

![Skærmbillede af konti](./media/multi-factor-authentication-azure-authenticator/accounts.png)

### <a name="add-an-account-to-the-app-by-using-touch-id"></a>Tilføje en konto til appen ved hjælp af berøring ID

Microsoft Authenticator app på iOS understøtter Touch-ID.  Azure Multi-Factor Authentication kan organisationer til at kræve en pinkode for enheder. Med tryk-ID behøver iOS brugere at angive en pinkode. De kan i stedet scanne deres fingeraftryk og vælge **Godkend**.

Det er nemt at konfigurere tryk-ID med Microsoft Authenticator. Du har fuldført en normal bekræftelse udfordring med en pinkode. Hvis enheden understøtter tryk-ID, vil Microsoft Authenticator konfigurere det automatisk for den pågældende konto.

![Kontrol af tryk-ID-konfiguration](./media/multi-factor-authentication-azure-authenticator/touchid1.png)

Fra, peg frem, når du skal bekræfte din logonadresse, Vælg den modtagne push-besked og scanne dit fingeraftryk i stedet for at angive din pinkode.

![Push-meddelelse](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

## <a name="uninstall-the-old-azure-authentication-app"></a>Fjerne den gamle app Azure-godkendelse

Når du har tilføjet alle kontiene til den nye app, kan du fjerne den gamle app fra din telefon.

## <a name="delete-an-account"></a>Slette en konto

Vælg kontoen, hvis du vil fjerne en konto fra Microsoft Authenticator app, og vælg derefter **Slet**.

![Knappen Slet](./media/multi-factor-authentication-azure-authenticator/remove.png)
