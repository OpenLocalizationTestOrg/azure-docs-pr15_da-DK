<properties
    pageTitle="Azure Active Directory rapportering beskeder"
    description="Sådan bruger du Azure Active Directory rapportering meddelelser om mistænkelige log in-programmer."
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/07/2016"
    ms.author="dhanyahk"/>

# <a name="azure-active-directory-reporting-notifications"></a>Azure Active Directory rapportering beskeder

## <a name="what-reports-generate-email-notifications"></a>Hvilke rapporter generere besked via mail

E-mail-meddelelser på nuværende tidspunkt kun aktivitet rapport udløsere uregelmæssige logge.

## <a name="what-is-an-irregular-sign-in"></a>Hvad er en "uregelmæssige Log på"?

Uregelmæssige logon er dem, der er blevet identificeret af vores machine learning-algoritmer, på grundlag af en "umuligt rejse"-betingelse, kombineres med en uoverensstemmende logonadresse og en enhed. Dette kan betyde, at en hacker har forsøgt at logge på ved hjælp af denne konto.

## <a name="who-receives-the-email-notifications"></a>Hvem der modtager besked via mail?

E-mailen sendes til alle globale administratorer, der har fået tildelt en licens til Active Directory-Premium. For at sikre, at det er leveret, sender vi den til administratorer alternative mailadresse samt. Administratorer bør omfatte aad-alerts-noreply@mail.windowsazure.com deres afsendere på listen, så de ikke går glip af e-mailen.

## <a name="how-often-are-these-emails-sent"></a>Hvor ofte der disse mails, der sendes?

E-mailen sendes, hvis 10 nye uregelmæssige logon forekommer aktiviteter i de seneste 30 dage, eller siden sidste mailen blev sendt, afhængigt af hvad der er mindre.

## <a name="how-do-i-access-the-report-mentioned-in-the-email"></a>Hvordan får jeg adgang til den rapport, der er nævnt i mails?

Når du klikker på linket, omdirigeres du til rapportsiden i Azure klassisk portal. For at få adgang til rapporten, skal du være begge dele:

- En administrator eller co-administrator for dit Azure-abonnement

- En global administrator i mappen, og har fået tildelt en licens til Active Directory-Premium. Du kan finde yderligere oplysninger finder [Azure Active Directory-versioner](active-directory-editions.md).

## <a name="can-i-turn-off-these-emails"></a>Kan jeg deaktivere disse mails?

Ja, hvis du vil deaktivere beskeder, der er relateret til uoverensstemmende logon i portalen Azure klassisk, klik på **Konfigurer**, og vælg derefter **deaktiveret** under afsnittet **meddelelser** .

## <a name="whats-next"></a>Hvad skal der ske nu
- Nysgerrig efter, hvilken sikkerhed, overvågning og aktivitet rapporter der er tilgængelige? Se [Azure AD-sikkerhed, revision, og aktivitetsrapporter](active-directory-view-access-usage-reports.md)
- [Introduktion til Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Tilføje firmalogo til logon og Access Panel sider](active-directory-add-company-branding.md)
