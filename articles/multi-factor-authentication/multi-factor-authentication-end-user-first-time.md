<properties
    pageTitle="Konfigurere totrinsbekræftelse for min arbejds- eller skolekonto konto"
    description="Når din virksomhed konfigurerer Azure Multi-Factor Authentication, bliver du bedt om at tilmelde sig totrinsbekræftelse. Lær, hvordan du konfigurerer den. "
    services="multi-factor-authentication"
    keywords="Sådan bruger du azure directory, active directory i skyen, active directory-selvstudium"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="pblachar"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="set-up-my-account-for-two-step-verification"></a>Konfigurere min konto for totrinsbekræftelse

Totrinsbekræftelse er en ekstra sikkerhed trin, der hjælper med at beskytte din konto ved at gøre det sværere for andre at bryde i. Hvis du læser i denne artikel, har du sikkert en mail fra din arbejds- eller skolekonto administrator om Multi-Factor Authentication. Eller måske du forsøgte at logge på og fået en meddelelse, hvor du bliver spurgt til konfiguration af ekstra sikkerhed bekræftelse. Hvis det er tilfældet, skal **du ikke kan logge på, indtil du har fuldført processen automatisk registrering**.

I denne artikel hjælper dig med at konfigurere din **arbejds- eller skolekonto**. Hvis du vil aktivere totrinsbekræftelse til dine egne, personlig Microsoft-konto, skal du se [om totrinsbekræftelse](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="determine-how-you-will-use-multi-factor-authentication"></a>Finde ud af, hvordan du skal bruge Multi-Factor authentication

Totrinsbekræftelse virker ved at spørge dig om to filer identifikation, når du logger på. Først skal vi beder dig om dit brugernavn og din adgangskode på sædvanlig vis. Vi kontakter en telefon, som vi vide tilhører, og Bekræft derefter, logon forsøg på er gyldige.  

For at komme i gang med konfigurationen af den, du forsøge at logge på din konto, som du normalt gør. Hvis din administrator har konfigureret kontoen for totrinsbekræftelse, bliver du bedt om at starte automatisk registrering processen. Starte denne proces ved at klikke på **konfigurere det nu.**

![Konfiguration](./media/multi-factor-authentication-end-user-first-time/first.png)

Det første spørgsmål i tilmeldingsprocessen er, hvordan du have os til at kontakte dig. Se nærmere på indstillingerne i tabellen, og bruge linkene til at gå til konfigurationstrinnene for hver enkelt metode.

| Kontaktmetode | Beskrivelse |
| --- | --- |
[-Mobilappen](#use-a-mobile-app-as-the-contact-method) | - **Modtage beskeder til bekræftelse.** Denne indstilling flytter en meddelelse til godkenderen app på din smartphone eller tablet. Få vist meddelelsen, og hvis det er gyldige, Vælg **Godkend** i appen. Dit arbejde eller skole kan kræve, at du angiver en pinkode, før du godkender.<br>- **Brug verifikationskoden.** I denne tilstand genereres i godkenderen en bekræftelseskode, der opdaterer hvert 30. Angive de mest aktuelle bekræftelseskode i grænsefladen logon.<br>Microsoft Authenticator app er tilgængelig til [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)og [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
[Mobiltelefon opkald eller tekst](#use-your-mobile-phone-as-the-contact-method) | - **Telefonopkald** placerer et automatiseret taleopkald til det telefonnummer, du angiver. Besvar opkaldet, og tryk på # i telefon tastatur til at godkende.<br>- **SMS-besked** slutter en SMS-besked, der indeholder en bekræftelseskode. Svar tekst eller indtast verifikationskoden angivet i grænsefladen logon efter prompt i feltet tekst. |  
[Office telefonopkald](#use-your-office-phone-as-the-contact-method) | Placerer et automatiseret taleopkald til det telefonnummer, du angiver. Besvar opkaldet og trykker på # i telefon tastatur til at godkende. |

## <a name="use-a-mobile-app-as-the-contact-method"></a>Bruge en mobilapp som den kontaktmetode

Brug denne metode kræver, at du installerer en godkender app på din telefon eller tablet. Trinnene i denne artikel er baseret på Microsoft Authenticator app, som fås til [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)og [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

1. Vælg **mobilapp** på rullelisten.
2. Vælg enten **modtage meddelelser til godkendelse,** eller **Brug verifikationskoden**og derefter **konfigurere**.

    ![Ekstra sikkerhed bekræftelse skærmen](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)

3. Åbn appen på din telefon eller tablet, og vælg **+** at tilføje en konto. (På Android-enheder, Vælg de tre prikker, derefter **Tilføj konto**).
4. Angive, at du vil føje en arbejds- eller skolekonto. QR-kode scanner på din telefon åbnes. Hvis dit kamera ikke fungerer korrekt, kan du vælge for at angive dine firmaoplysninger manuelt. Se [tilføje en konto manuelt](#add-an-account-manually)kan finde flere oplysninger.  
5. Scan QR-kode billedet, der blev vist på skærmen for at konfigurere mobilappen.  Vælg **udført** at lukke skærmbilledet QR-kode.  

    ![QR-kode skærmen](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

6. Når aktivering afsluttes på telefonen, skal du vælge **Kontakt mig**.  Dette trin sender en meddelelse eller en bekræftelseskode til din telefon. Vælg **Bekræft**.  
7. Hvis din virksomhed kræver en pinkode til godkendelse af bekræftelse-logon, skal du angive den.

    ![Feltet for at angive en pinkode](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

8. Når pinkoden er fuldført, skal du vælge **Luk**. På dette tidspunkt skal din bekræftelse være fuldført.
9. Vi anbefaler, at du angiver dit mobiltelefonnummer i tilfælde af, at du miste adgangen til dine-mobilappen. Angiv dit land fra på rullelisten, og Angiv dit mobiltelefonnummer i feltet ud for landenavn. Klik på **Næste**.
10. På dette tidspunkt bliver du bedt om at konfigurere appadgangskoder til ikke-browser-apps som Outlook 2010 eller ældre eller den indbyggede mailapp på Apple-enheder. Dette skyldes, at nogle programmer ikke understøtter totrinsbekræftelse. Hvis du ikke bruger disse apps skal du klikke på **udført** og springe resten af trinnene.
11. Hvis du bruger disse apps, Kopiér appadgangskoden leveres og sætte det ind i dit program i stedet for adgangskoden regelmæssigt. Du kan bruge den samme appadgangskode for flere apps. For at få flere oplysninger, [Hjælp til appadgangskoder].
12. Klik på **udført**.


### <a name="add-an-account-manually"></a>Tilføje en konto manuelt
Følg disse trin, hvis du vil tilføje en konto i mobilappen manuelt i stedet for ved hjælp af læser QR.

1. Vælg knappen **angive konto manuelt** .  
2. Angiv koden og URL-adressen, der kan bruges på samme side, der viser stregkoden. Denne oplysninger vises i felterne **kode** og **URL-adressen** på mobilappen.

    ![Konfiguration](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

3. Når aktiveringen er færdig, skal du markere **kontakten besked**. Dette trin sender en meddelelse eller en bekræftelseskode til din telefon. Vælg **Bekræft**.

## <a name="use-your-mobile-phone-as-the-contact-method"></a>Brug din mobiltelefon som den kontaktmetode

1. Vælg **Godkendelse telefon** på rullelisten.  

    ![Konfiguration](./media/multi-factor-authentication-end-user-first-time-mobile-phone/phone.png)  

2. Vælg dit land på rullelisten, og Angiv dit mobiltelefonnummer.
3. Vælg den metode, du foretrækker at bruge sammen med din mobiltelefon - tekst eller opkald.
4. Vælg **Kontakt mig** at bekræfte dit telefonnummer. Afhængigt af den tilstand, du har valgt, vi sender dig en tekstbesked eller ringe dig op. Følg vejledningen på skærmen og derefter vælge **Bekræft**.
5. På dette tidspunkt bliver du bedt om at konfigurere appadgangskoder til ikke-browser-apps som Outlook 2010 eller ældre eller den indbyggede mailapp på Apple-enheder. Dette skyldes, at nogle programmer ikke understøtter totrinsbekræftelse. Hvis du ikke bruger disse apps skal du klikke på **udført** og springe resten af trinnene.
6. Hvis du bruger disse apps, Kopiér appadgangskoden leveres og sætte det ind i dit program i stedet for adgangskoden regelmæssigt. Du kan bruge den samme appadgangskode for flere apps. For at få flere oplysninger, [Hjælp til appadgangskoder].
7. Klik på **udført**.

## <a name="use-your-office-phone-as-the-contact-method"></a>Brug din arbejdstelefon som den kontaktmetode

1. Vælg **Arbejdstelefon** på rullelisten  

    ![Konfiguration](./media/multi-factor-authentication-end-user-first-time-office-phone/office.png)  

2. Feltet telefonnummer udfyldes automatisk med dine kontakter firmaoplysninger. Hvis tallet er forkert eller manglende, kan du bede din administrator til at foretage ændringer.
4. Vælg **Kontakt mig** at bekræfte dit telefonnummer, og vi kalder dit nummer. Følg vejledningen på skærmen og derefter vælge **Bekræft**.
5. På dette tidspunkt bliver du bedt om at konfigurere appadgangskoder til ikke-browser-apps som Outlook 2010 eller ældre eller den indbyggede mailapp på Apple-enheder. Dette skyldes, at nogle programmer ikke understøtter totrinsbekræftelse. Hvis du ikke bruger disse apps skal du klikke på **udført** og springe resten af trinnene.
6. Hvis du bruger disse apps, Kopiér appadgangskoden leveres og sætte det ind i dit program i stedet for adgangskoden regelmæssigt. Du kan bruge den samme appadgangskode for flere apps. Du finder flere oplysninger i [Hvad er Appadgangskoder](multi-factor-authentication-end-user-app-passwords.md).
7. Klik på **udført**.

## <a name="next-steps"></a>Næste trin

- Ændre dine foretrukne indstillinger og [administrere dine indstillinger for totrinsbekræftelse](multi-factor-authentication-end-user-manage-settings.md)
- Konfigurere [appadgangskoder](multi-factor-authentication-end-user-app-passwords.md) for oprindelige enhed apps, som ikke understøtter totrinsbekræftelse.
- Se [Microsoft godkenderen app](multi-factor-authentication-microsoft-authenticator.md) til hurtig, sikker godkendelse selvom du ikke har celle tjeneste.
