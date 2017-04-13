<properties
    pageTitle="Microsoft godkenderen app ofte stillede spørgsmål"
    description="Oversigt over ofte stillede spørgsmål og svar, der er relateret til Microsoft Authentication app og Azure Multi-Factor Authentication."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="pblachar, librown"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="kgremban"/>

# <a name="microsoft-authenticator-application-faq"></a>Ofte stillede spørgsmål om Microsoft Authenticator program

Appen Microsoft Authenticator erstattet Azure godkenderen app, og er det anbefalede app, når du bruger Azure Multi-Factor Authentication. Denne app er tilgængelig til Windows Phone, Android og iOS.

## <a name="frequently-asked-questions"></a>Ofte stillede spørgsmål

- **Hvad er der sket med Azure godkenderen, multi-Factor Auth og Microsoft-konto apps?**

    Appen Microsoft Authenticator erstatter hinanden disse apps. Azure godkenderen opgraderet til Microsoft Authenticator. Hvis du bruger Multi-Factor Auth eller Microsoft-konto apps, kan du installere Microsoft Authenticator og tilføje kontiene igen. Kontrollér, at du har tilføjet dine konti til den nye app før du kan slette de gamle.

- **Jeg bruger allerede programmet Microsoft Authenticator til bekræftelse koder. Hvordan skifter jeg mellem til et enkelt klik pushmeddelelser?**  

    Godkendelse af en logon gennem opslagsnål meddelelse er kun tilgængelig for Microsoft-konti, ikke for tredjeparts-konti som Google eller Facebook. Arbejds- eller skolekonto Microsoft-konto, skal kan din organisation vælge at deaktivere denne indstilling, men.

    Hvis du bruger en Microsoft-konto til din personlige konto, og du vil skifte til pushmeddelelser, skal du tilføje kontoen igen. Registrere igen enhed med din konto, og konfigurere pushmeddelelser.  

    Hvis din konto ikke har totrinsbekræftelse aktiveret, kan du se [om totrinsbekræftelse](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) beslutte, hvis det er bedst for dig.  

- **Når jeg kan bruge et enkelt klik pushmeddelelser på iPhone eller iPad?**  

    Denne funktion er i beta indtil slutningen af August, når det bliver alment tilgængelig for Microsoft-konti. Hvis du vil deltage i vores betaprogram til, sender mails til msauthenticator@microsoft.com. Medtage dit fornavn, Efternavn og Apple-ID i din meddelelse.  

- **Fungerer et enkelt klik pushmeddelelser for ikke-Microsoft-konti?**  

    Nej, arbejde pushmeddelelser kun med Microsoft-konti og Azure Active Directory-konti. Hvis dit arbejde eller skole bruger Azure AD-konti, kan de deaktivere denne funktion.  

- **Jeg gendannet min enhed fra en sikkerhedskopi, og min kontokoder mangler eller ikke virker. Hvad skete der?**  

    Vi gendanne ikke konti fra app sikkerhedskopier af sikkerhedsmæssige årsager. Hvis du gendanner iOS-app fra en sikkerhedskopi, kontiene vises stadig, men de fungerer ikke for at modtage logon kontrol eller generere sikkerhed koder. Når du gendanner app, slette dine konti og tilføje dem igen.

- **Jeg fik en ny enhed. Hvordan fjerner Microsoft Authenticator-app'en fra min gamle enhed og flytte til det nye?**

    Tilføje appen Microsoft Authenticator til en ny enhed fjerner automatisk ikke den fra andre enheder. Hvis du vil administrere, hvilke enheder er konfigureret for din konto, skal du besøge webstedet samme, som du bruger til at administrere totrinsbekræftelse, og vælg for at fjerne gamle apps.

    Personlig Microsoft-konto er dette websted siden [kontosikkerhed](https://account.microsoft.com/security) . Dette websted kan arbejds- eller skolekonto Microsoft-konto, skal være enten [Mine Apps](https://myapps.microsoft.com) eller en brugerdefineret portal, som din organisation har konfigureret.

## <a name="contact-us"></a>Kontakt os

Hvis dit spørgsmål ikke blev besvaret her, kan du skrive en kommentar i bunden af siden. Eller [kontakte support](https://support.microsoft.com/contactus) , og vi vil svare på dit problem, som vi kan.

Hvis du kontakter support, omfatte som mange af de følgende oplysninger, som du kan:

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

- [Ofte stillede spørgsmål om Azure Multi-Factor Authentication](multi-factor-authentication-faq.md)  
- [Om totrinsbekræftelse](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) for Microsoft-konti
- [Har du problemer med totrinsbekræftelse?](multi-factor-authentication-end-user-troubleshoot.md)
