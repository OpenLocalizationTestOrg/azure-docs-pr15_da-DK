<properties
    pageTitle="Fejlfinding i forbindelse med totrinsbekræftelse | Microsoft Azure"
    description="Dette dokument giver brugerne oplysninger om, hvad du skal gøre, hvis de opstår et problem med Azure Multi-Factor Authentication."
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

# <a name="having-trouble-with-two-step-verification"></a>Har du problemer med totrinsbekræftelse

I denne artikel beskrives nogle problemer, som du kan opleve med totrinsbekræftelse. Hvis du har problemet ikke er medtaget her, skal du angive detaljerede feedback i sektionen kommentarer, så vi kan forbedre.

## <a name="i-lost-my-phone-or-it-was-stolen"></a>Jeg har mistet min telefon eller det blev stjæles

Der er to måder at komme tilbage din konto. Først er at logge på med dit alternative godkendelse telefonnummer, hvis du har konfigureret et. Andet er at spørge din administrator for at fjerne markeringen i dine indstillinger.

Hvis din telefon blev mistede eller stjæles, anbefaler vi også, at du har administratoren nulstille appadgangskoder din og Ryd en husket enheder. Hvis din administrator ikke sikker på, at hvordan du gør dette, skal du henvise dem til denne artikel: [Administrer brugere og enheder](multi-factor-authentication-manage-users-and-devices.md#delete-users-existing-app-passwords).


### <a name="use-an-alternate-phone-number"></a>Bruge et andet telefonnummer.

Hvis du har oprettet flere muligheder, kontrol, herunder et alternativt telefonnummer eller en godkender app på en anden enhed, kan du bruge en af følgende til at logge på.

Hvis du vil logge på ved hjælp af det alternative telefonnummer, skal du følge disse trin:

1. Log på, som du normalt ville gøre.
2. Vælg **Brug en anden bekræftelsesindstilling**, når du bliver bedt om at yderligere bekræfte din konto.

    ![Anden bekræftelse](./media/multi-factor-authentication-end-user-manage/differentverification.png)

3. Vælg det telefonnummer, du har adgang til.

    ![Alternative telefon](./media/multi-factor-authentication-end-user-manage/altphone2.png)

4. Når du er tilbage i din konto, [administrere indstillingerne for](multi-factor-authentication-end-user-manage-settings.md) at ændre telefonnummeret til godkendelse.

>[AZURE.IMPORTANT]
>Det er vigtigt at konfigurere en sekundær godkendelse telefonnummer. Hvis din primære telefonnummer og din mobile-app er på den samme telefonlinje, du har brug for en tredje valgmulighed Hvis telefonen går tabt eller stjæles.

### <a name="clear-your-settings"></a>Fjern markeringen i dine indstillinger

Hvis du ikke har konfigureret en sekundær godkendelse telefonnummer og derefter er du nødt til at kontakte din administrator for at få hjælp. Få dem rydde indstillingerne, så næste gang du logger på, du vil blive bedt om at [konfigurere din konto](multi-factor-authentication-end-user-first-time.md) igen.


## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>Jeg modtager ikke en tekst eller et opkald på min telefon

Der er flere årsager Hvorfor kan du prøve at logge på, men ikke modtage den tekst eller et telefonopkald. Hvis du har blev modtaget tekster eller telefonopkald til din telefon tidligere, derefter sandsynligvis dette et problem med telefon-udbyder ikke din konto. Sørg for, at du har god celle signal, og hvis du forsøger at modtage en tekstmeddelelse skal du kontrollere, at din telefon og service plan understøtter SMS-beskeder.

Hvis du har ventet nogle minutter for en tekst eller et opkald, er den hurtigste måde at komme til din konto, kan du prøve en anden indstilling.

1. Vælg **Brug en anden bekræftelsesindstilling** på den side, der venter, før bekræftelsen.

    ![Anden bekræftelse](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

2. Vælg den telefon tal eller levering metode, du vil bruge.

    Hvis du har modtaget flere bekræftelsen koder, fungerer kun den nyeste ene.

Hvis du ikke har en anden metode, der er konfigureret, Kontakt din administrator og bede dem om at fjerne markeringen i dine indstillinger. Næste gang du logger på, du vil blive bedt om at [konfigurere Multi-Factor authentication](multi-factor-authentication-end-user-first-time.md) igen.


Hvis du ofte har forsinkelser på grund af forkert celle signal, anbefaler vi, du bruger [Microsoft godkenderen app](multi-factor-authentication-microsoft-authenticator.md) på din smartphone. Appen kan generere tilfældige sikkerhed koder, du bruger til at logge på, og disse koder kræver ikke en hvilken som helst celle signal eller internetforbindelse.


## <a name="app-passwords-are-not-working"></a>Appadgangskoder virker ikke

Først skal du sørge for, at du har angivet appadgangskoden korrekt.  Hvis den stadig ikke virker prøve at logge på og [oprette en ny appadgangskode](multi-factor-authentication-end-user-app-passwords.md).  Hvis dette ikke fungerer, Kontakt administratoren, og få dem [slette din eksisterende appadgangskoder](multi-factor-authentication-manage-users-and-devices.md#delete-users-existing-app-passwords) og derefter du kan oprette en ny.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Jeg finde ikke et svar på mit problem.

Hvis du har prøvet disse fejlfindingstrin, men er stadig støder på problemer, skal du kontakte administratoren eller den person, der konfigurere Multi-Factor authentication for dig. De skal kunne hjælpe dig.

Du kan stille spørgsmål på [Azure AD-fora](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD) eller [kontakte support](https://support.microsoft.com/contactus) og vi vil svare på dit problem, som vi kan også.

Hvis du kontakter support, skal du medtage følgende oplysninger:

- **Bruger-ID** – som Hvad er den mailadresse, du forsøgte at logge på med?
- **Generel beskrivelse af fejlen over** – hvad præcise fejlmeddelelse mente du se?  Hvis der var ingen fejlmeddelelse, skal du beskrive de uventede funktionsmåder du bemærket, detaljeret.
- **Siden** – var hvilken side du på når du fik vist fejlen (omfatte URL-adressen)?
- **Fejlkode** - fejlkoden du modtager.
- **Session-id** - det specifikke session-id, du modtager.
- **Korrelations-ID'ET** – som Hvad var koden Korrelations-id genereres, når brugeren fik vist fejlen.
- **Tidsstempel** – Hvad var nøjagtige dato og klokkeslæt, du fik vist fejlen (omfatte tidszonen)?

Mange af disse oplysninger kan findes på siden Log på. Når du ikke har bekræftet dit logon tidspunkt, Vælg **Vis detaljer**.

![Logge på flere oplysninger om fejlen](./media/multi-factor-authentication-end-user-troubleshoot/view_details.png)

Herunder oplysningerne hjælper med at du kan løse dit problem så hurtigt som muligt.

## <a name="related-topics"></a>Relaterede emner
- [Administrere dine indstillinger for totrinsbekræftelse](multi-factor-authentication-end-user-manage-settings.md)  
- [Ofte stillede spørgsmål om Microsoft Authenticator program](multi-factor-authentication-app-faq.md)
