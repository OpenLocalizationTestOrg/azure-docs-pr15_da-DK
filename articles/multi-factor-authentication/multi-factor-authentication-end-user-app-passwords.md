<properties
    pageTitle="Hvad er Appadgangskoder i Azure MFA?"
    description="Denne side kan hjælpe brugerne at forstå, hvad appadgangskoder er, og hvad de anvendes til med hensyn til Azure MFA."
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
    ms.date="08/04/2016"
    ms.author="kgremban"/>



# <a name="what-are-app-passwords-in-azure-multi-factor-authentication"></a>Hvad er Appadgangskoder i Azure Multi-Factor Authentication?

Visse ikke-browser-apps, som den Apple oprindelige e-mail-klient, der bruger Exchange Active Sync, understøtter i øjeblikket ikke Multi-Factor authentication. Multi-Factor authentication er aktiveret per bruger. Det betyder, at hvis en bruger er blevet aktiveret til multi-Factor authentication, og de forsøger at bruge ikke-browserapps, de vil ikke kunne gøre dette. En appadgangskode tillader det skal udføres.

>[AZURE.NOTE] Moderne godkendelse til Office 2013-klienter
>
> Office 2013-klienter (herunder Outlook) understøtter nye godkendelsesprotokoller nu og kan aktiveres til at understøtte Multi-Factor Authentication.  Det betyder, at når aktiveret, appadgangskoder ikke er påkrævet til brug sammen med Office 2013-klienter.  Få vist [Office 2013 moderne godkendelse offentlige eksempel offentliggjort](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)kan finde flere oplysninger.

## <a name="how-to-use-app-passwords"></a>Sådan bruger du appadgangskoder

Følgende er nogle ting at huske om, hvordan du bruger appadgangskoder.

- Den aktuelle adgangskode genereres automatisk, og der ikke er angivet af brugeren. Dette er, fordi den automatisk oprettede adgangskode er sværere for en hacker at gætte og er mere sikker.
- I øjeblikket er der en grænse på 40 adgangskoder per bruger. Hvis du forsøger at oprette et, når du har nået grænsen, bliver du bedt om at slette et af dine eksisterende appadgangskoder for at oprette en ny.
- Det anbefales, at der oprettes appadgangskoder efter enhed og ikke efter programmet på computeren. Du kan for eksempel oprette en appadgangskode for din bærbare computer og bruge appadgangskode for alle dine programmer på den bærbare computer.
- Du får en appadgangskode for første gang du logger på.  Hvis du har brug for yderligere dem, kan du oprette dem.

![Konfiguration](./media/multi-factor-authentication-end-user-app-passwords/app.png)

Når du har en appadgangskode, kan du bruge dette i stedet for din oprindelige adgangskode med disse ikke-browser-apps.  Så for eksempel, hvis du bruger Multi-Factor authentication og Apple oprindelige mail-klienten på din telefon.  Brug appadgangskoden, så den kan tilsidesætte Multi-Factor authentication og fortsætte med at arbejde.

## <a name="creating-and-deleting-app-passwords"></a>Oprette og slette appadgangskoder
Under din indledende logon får du en appadgangskode, som du kan bruge.  Desuden kan du også oprette og slette appadgangskoder senere.  Hvordan du gør dette, afhænger af, hvordan du bruger godkendelse i flere niveauer.  Vælg det, at det meste gælder for dig.

Hvordan du bruger Multi-Factor authentiation|Beskrivelse
:------------- | :------------- |
[Jeg bruge det sammen med Office 365](#creating-and-deleting-app-passwords-with-office-365)|  Det betyder, at du vil oprette appadgangskoder via Office 365-portalen.
[Jeg ved det ikke](#creating-and-deleting-app-passwords-with-myapps-portal)|Det betyder, at du vil oprette appadgangskoder gennem [https://myapps.microsoft.com](https://myapps.microsoft.com)
[Jeg bruge det sammen med Microsoft Azure](#create-app-passwords-in-the-azure-portal)| Det betyder, at du vil oprette appadgangskoder via Azure-portalen.

## <a name="creating-and-deleting-app-passwords-with-office-365"></a>Oprette og slette appadgangskoder med Office 365

Hvis du bruger Multi-Factor authentication med Office 365 vil du oprette og slette appadgangskoder via Office 365-portalen.

### <a name="to-create-app-passwords-in-the-office-365-portal"></a>Du kan oprette appadgangskoder i Office 365-portalen
--------------------------------------------------------------------------------

1. Log på [Office 365-portalen](https://login.microsoftonline.com/).
2. Vælg widgeten i øverste højre hjørne og vælge Office 365-indstillinger.
3. Klik på ekstra sikkerhed bekræftelse.
4. Klik på linket, hvor der står i højre side **opdatere mine telefonnumre, der bruges til kontosikkerhed.** 
 ![Konfiguration](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Dette omdirigerer dig til den side, der gør det muligt at ændre dine indstillinger.
![Skyen](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. Klik på øverst ud for ekstra sikkerhed bekræftelse på **appadgangskoder.**
7. Klik på **Opret**.
![Skyen](./media/multi-factor-authentication-end-user-app-passwords-create-o365/apppass.png)
8. Skriv et navn til appadgangskoden, og klik på **Næste**.
![Oprette en appadgangskode](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
9. Kopiere appadgangskoden til Udklipsholder og sætte det ind i din app.
![Oprette en appadgangskode](./media/multi-factor-authentication-end-user-app-passwords/create2.png)


### <a name="to-delete-app-passwords-using-the-office-365-portal"></a>Slette appadgangskoder ved hjælp af Office 365-portalen
--------------------------------------------------------------------------------


1. Log på [Office 365-portalen](https://login.microsoftonline.com/).
2. Vælg widgeten i øverste højre hjørne og vælge Office 365-indstillinger.
3. Klik på ekstra sikkerhed bekræftelse.
4. Klik på linket, hvor der står i højre side **opdatere mine telefonnumre, der bruges til kontosikkerhed.** 
 ![Konfiguration](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Dette omdirigerer dig til den side, der gør det muligt at ændre dine indstillinger.
![Skyen](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. Klik på øverst ud for ekstra sikkerhed bekræftelse på **appadgangskoder.**
7. Klik på **Slet**ud for den appadgangskode, du vil slette.
![Slette en appadgangskode](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
8. Bekræfte sletningen ved at klikke på **Ja**.
![Bekræft afkrydsningsfeltet Slet](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
9. Når appadgangskoden er blevet slettet, kan du klikke på **Luk**.
![Luk](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)


## <a name="creating-and-deleting-app-passwords-with-myapps-portal"></a>Oprette og slette appadgangskoder med mine Apps portal.
Hvis du ikke er sikker på, hvordan du bruger Multi-Factor godkendelse, kan du altid oprette og slette appadgangskoder via portalen mine Apps.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>Oprette en appadgangskode, ved hjælp af portalen mine Apps

1. Log på [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Øverst skal du vælge profil.
3. Markér ekstra sikkerhed bekræftelse.
![Skyen](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Dette omdirigerer dig til den side, der gør det muligt at ændre dine indstillinger.
![Konfiguration](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)
5. Klik på øverst ud for ekstra sikkerhed bekræftelse på **appadgangskoder.**
6. Klik på **Opret**.
![Oprette en appadgangskode](./media/multi-factor-authentication-end-user-app-passwords/create3.png)
7. Skriv et navn til appadgangskoden, og klik på **Næste**.
![Oprette en appadgangskode](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
8. Kopiere appadgangskoden til Udklipsholder og sætte det ind i din app.
![Oprette en appadgangskode](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>Slette en appadgangskode, ved hjælp af portalen mine Apps

1. Log på [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Øverst skal du vælge profil.
3. Markér ekstra sikkerhed bekræftelse.
![Skyen](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Dette omdirigerer dig til den side, der gør det muligt at ændre dine indstillinger.
![Konfiguration](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)
5. Klik på øverst ud for ekstra sikkerhed bekræftelse på **appadgangskoder.**
6. Klik på **Slet**ud for den appadgangskode, du vil slette.
![Slette en appadgangskode](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
7. Bekræfte sletningen ved at klikke på **Ja**.
![Bekræft afkrydsningsfeltet Slet](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
8. Når appadgangskoden er blevet slettet, kan du klikke på **Luk**.
![Luk](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)


## <a name="create-app-passwords-in-the-azure-portal"></a>Oprette appadgangskoder i portalen Azure

Hvis du bruger Multi-Factor authentication med Azure, skal du oprette appadgangskoder via Azure-portalen.

### <a name="to-create-app-passwords-in-the-azure-portal"></a>Du kan oprette appadgangskoder i portalen Azure

1. Log på portalen Azure Management.
2. Højreklik på dit brugernavn øverst, og vælg ekstra sikkerhed bekræftelse.
3. På siden proofup øverst skal du vælge appadgangskoder
4. Klik på **Opret**.
5. Angiv et navn til appadgangskoden og klikke på **Næste**
6. Kopiere appadgangskoden til Udklipsholder og sætte det ind i din app.
![Skyen](./media/multi-factor-authentication-end-user-app-passwords-create-azure/app2.png)

### <a name="to-delete-app-passwords-in-the-azure-portal"></a>Slette appadgangskoder i portalen Azure

1. Log på portalen Azure Management.
2. Højreklik på dit brugernavn øverst, og vælg ekstra sikkerhed bekræftelse.
3. Klik på øverst ud for ekstra sikkerhed bekræftelse på **appadgangskoder.**
4. Klik på **Slet**ud for den appadgangskode, du vil slette.
5. Bekræfte sletningen ved at klikke på **Ja**.
6. Når appadgangskoden er blevet slettet, kan du klikke på **Luk**.
![Luk](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)
