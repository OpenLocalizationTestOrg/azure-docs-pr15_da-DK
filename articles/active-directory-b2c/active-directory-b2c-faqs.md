<properties
    pageTitle="Azure Active Directory B2C: Ofte stillede spørgsmål om | Microsoft Azure"
    description="Ofte stillede spørgsmål om Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/09/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-faqs"></a>Azure Active Directory B2C: ofte stillede spørgsmål

Denne side finder du svar på ofte stillede spørgsmål om Azure Active Directory (Azure AD) B2C. Holde søge tilbage efter opdateringer.

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Kan jeg bruge Azure AD B2C funktioner i min eksisterende, medarbejder-baserede Azure AD-lejer?

I øjeblikket Azure AD B2C funktioner kan ikke være slået til i din eksisterende Azure AD-lejer. Vi anbefaler, at du opretter en separat lejer for at bruge Azure AD B2C funktioner til at administrere dine brugere.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Kan jeg bruge Azure AD B2C til at levere sociale login (Facebook og Google +) på Office 365?

Azure AD-B2C kan ikke bruges med Microsoft Office 365. Det kan generelt ikke bruges til at levere godkendelse til en hvilken som helst SaaS apps (Office 365, Salesforce, arbejdsdag osv.). Det giver identitets- og management kun til forbrugere mod web og mobilprogrammer og gælder ikke for medarbejder eller partner scenarier.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Hvad er lokale konti i Azure AD B2C? Hvordan adskiller de sig fra arbejds- eller skolekonto konti i Azure AD?

I en Azure AD-lejer, hver bruger i lejeren (undtagen brugere med eksisterende Microsoft-konti) logger på med en e-mailadresse i formularen `<xyz>@<tenant domain>`, hvor `<tenant domain>` er et af de bekræftede domæner i lejeren eller første `<...>.onmicrosoft.com` domæne. Denne type konto er en arbejds- eller skolekonto konto.

I en Azure AD B2C lejer, som de fleste apps brugeren skal logge på med en vilkårlig mailadresse (for eksempel joe@comcast.net, bob@gmail.com, sarah@contoso.com, eller jim@live.com). Denne type konto er en lokal konto. I dag, understøtter vi også vilkårlig brugernavne (ganske enkelt strenge) som lokale konti (for eksempel joe, bob, sarah eller jim). Du kan vælge en af disse to lokale kontotyper i tjenesten Azure AD B2C.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Hvilke sociale identitetsudbydere understøtter du nu? Hvilke af dem kan du planlægge at understøtter i fremtiden?

Vi understøtter Facebook, Google +, LinkedIn og Amazon. Vi tilføjer understøttelse af andre populære sociale id-udbydere, der er baseret på kundebehov.

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Kan jeg konfigurere områder for at indsamle flere oplysninger om forbrugere fra forskellige sociale identitetsudbydere?

Nej, men denne funktion er på vores vejledning. De standard områder, der bruges til vores understøttede sæt af sociale identitetsudbydere er:

- Facebook: mail
- Google +: mail
- Microsoft-konto: openid e-mail-profil
- Amazon: profil
- LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Har mit program der skal køres på Azure for det arbejde med Azure AD B2C?

Nej, kan du hoste dit program et vilkårligt sted (i skyen eller lokalt). Alle de skal interagere med Azure AD B2C er muligheden for at sende og modtage HTTP-anmodninger på offentligt tilgængelige slutpunkter.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Jeg har flere Azure AD B2C lejere. Hvordan kan jeg administrere dem på Azure-portalen?

Hver Azure AD B2C lejer har sin egen B2C funktioner blade på Azure-portalen. Se [Azure AD B2C: registrere dit program](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) til at få mere at vide, hvordan du kan navigere til en bestemt lejer B2C funktioner blade på Azure-portalen. Skift mellem Azure AD B2C mapper på portalen Azure beholde ikke dine B2C funktioner blade åbne på de fleste browsere.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Hvordan kan jeg tilpasse bekræftelse mails (indholdet og "fra:" felt) sendes af Azure AD B2C?

Du kan bruge [virksomhed mærkning funktion](../active-directory/active-directory-add-company-branding.md) til at tilpasse indholdet af bekræftelse mails. Nærmere betegnet kan kan disse to elementer i mailen tilpasses:

- **Banner Logo**: vises nederst til højre.
- **Baggrundsfarve**: vises øverst.

    ![Skærmbillede af en tilpasset bekræftelsesmailen](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

E-mail-signatur indeholder B2C lejerens navn, du angav, da du oprindeligt oprettede B2C lejeren. Du kan ændre navnet ved hjælp af disse instruktioner:

- Logge på [Azure klassisk portal](https://manage.windowsazure.com/) som abonnement Administrator.
- Gå til din B2C lejer.
- Klik på fanen **Konfigurer** .
- Ændre feltet **navn** under sektionen **Egenskaber for mappe** .
- Klik på **Gem** nederst på siden.

Der findes i øjeblikket ingen måde at ændre den "fra:" på mailen. Hvis du er interesseret i denne egenskab og i fuldt tilpasning af brødteksten i bekræftelsesmailen, stemme for funktionen på [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/15334335-fully-customizable-verification-emails).

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Hvordan kan jeg overføre min eksisterende brugernavne, adgangskoder og profiler fra min database til Azure AD B2C?

Du kan bruge Azure AD Graph API til at skrive din overførselsværktøjet. Se [Graph API eksempel](active-directory-b2c-devquickstarts-graph-dotnet.md) få mere at vide. Vi giver forskellige indstillinger for overførsel og værktøjer out-of-the-box i fremtiden.

### <a name="what-password-policy-is-used-for-local-accounts-in-azure-ad-b2c"></a>Hvilke adgangskodepolitik bruges til lokale konti i Azure AD B2C?

Azure AD B2C adgangskodepolitik for lokale konti er baseret på politikken for Azure AD. Azure AD-B2C Microsofts tilmelding, tilmelding eller logon og adgangskode nulstille politikker bruger "stærk" adgangskode styrken og adgangskoder ikke udløber. Læs [Azure AD adgangskodepolitik](https://msdn.microsoft.com/library/azure/jj943764.aspx) få mere at vide.

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Kan jeg bruge Azure AD-forbindelse til at overføre consumer identiteter, der er gemt på min lokale Active Directory for Azure AD B2C?

Nej, Azure AD Connect er ikke beregnet til at arbejde med Azure AD B2C. Vi giver forskellige indstillinger for overførsel og værktøjer out-of-the-box i fremtiden.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Fungerer Azure AD B2C med som Microsoft Dynamics CRM-systemer?

Ikke i øjeblikket. Integrering af disse systemer er på vores vejledning.

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Betyder Azure AD B2C fungerer med SharePoint lokalt 2016 eller tidligere?

Ikke i øjeblikket. Azure AD-B2C har ikke understøttelse af SAML 1.1 tokens, portaler og e-handel programmer bygget på har du brug for SharePoint i det lokale miljø. Bemærk, at Azure AD B2C ikke er beregnet til SharePoint ekstern Deling af partner scenariet; Se [Azure AD B2B](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx) i stedet.

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Skal jeg bruge Azure AD B2C eller B2B til at administrere eksterne identiteter?

Læs denne artikel om [eksterne identiteter](../active-directory/active-directory-b2b-compare-external-identities.md) for at lære mere om at anvende de relevante funktioner til din eksterne identitet scenarier.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Hvilke rapportering og overvågning funktioner Azure AD B2C giver? Er de samme som i Azure AD Premium?

Nej, Azure AD B2C understøtter ikke det samme sæt rapporter som Azure AD Premium. Azure AD-B2C vil frigive grundlæggende rapportering og overvågning API'er snart.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Kan jeg lokalisere Brugergrænsefladen af websider ved Azure AD B2C? Hvilke sprog er understøttet?

I øjeblikket er Azure AD B2C optimeret til engelsk kun. Vi planlægger at udrulle lokalisering funktioner så tidligt som muligt.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>Kan jeg bruge mit eget URL-adresser på min tilmelding og log på sider, der leveres af Azure AD B2C? For eksempel kan jeg ændre URL-adressen fra login.microsoftonline.com til login.contoso.com?

Ikke i øjeblikket. Denne funktion er på vores vejledning. Bemærk også, at bekræfte dit domæne under fanen **domæner** i din lejer på portalen Azure klassisk ikke gør dette.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Hvordan sletter jeg min Azure AD B2C lejer?

Følg disse trin for at slette din Azure AD B2C lejer:

- Følg disse trin til at [navigere til bladet B2C funktioner](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) på Azure-portalen.
- Gå til **programmer**, **identitetsudbydere** og **alle politikker** blade og slette alle elementerne i hver af dem.
- Log nu på [Azure klassisk portal](https://manage.windowsazure.com/) som abonnement Administrator. (Dette er den samme arbejds- eller skolekonto eller den samme Microsoft-konto, du har brugt til at tilmelde sig Azure.)
- Gå til i Active Directory-udvidelse til venstre, og klik på din B2C lejer.
- Klik på fanen **brugere** .
- Markér hver enkelt bruger i slå (udelade den bruger, du er logget på som, og det vil sige abonnement administratoren). Klik på **Slet** nederst på siden, og klik på **Ja** , når du bliver bedt om.
- Klik på fanen **programmer** .
- Vælg **programmer firmaet ejer** i feltet **Vis** rullelisten, og klik på markeringen.
- Du får vist et program kaldet **b2c-filtypenavne-app** nedenfor. Klik på **Slet** nederst på siden, og klik på **Ja** , når du bliver bedt om.
- Gå til filtypenavnet Active Directory igen, og vælg din B2C lejer.
- Klik på **Slet** nederst på siden. Følg vejledningen på skærmen for at fuldføre processen.

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Kan jeg få Azure AD B2C som en del af Enterprise mobilitet pakke?

Nej, Azure AD B2C er en pay-as-you-go Azure service og er ikke en del af Enterprise mobilitet pakke.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Hvordan rapporterer jeg problemer med Azure AD B2C?

Se [understøtter anmodninger om Azure Active Directory B2C-fil](active-directory-b2c-support.md).

## <a name="more-information"></a>Få mere at vide

Du kan også gennemse aktuelle [tjenestebegrænsninger, begrænsninger, og begrænsninger](active-directory-b2c-limitations.md).
