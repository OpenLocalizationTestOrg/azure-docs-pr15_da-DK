<properties
   pageTitle="Branding retningslinjer for Applications | Microsoft Azure"
   description="En omfattende vejledning til udvikler rundt ressourcer til Azure Active Directory"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/23/2016"
   ms.author="mbaldwin"/>


# <a name="branding-guidelines-for-applications"></a>Branding retningslinjer for programmer


Dette emne beskrives de mærkning retningslinjer, du skal bruge, når du udvikler programmer med Azure Active Directory (Azure AD). Disse retningslinjer lede dine kunder, når de vil bruge deres arbejds- eller skolekonto konto administreres i Azure AD eller deres personlige kontoindstillinger tilmelding og log på dit program.

## <a name="personal-accounts-vs-work-or-school-accounts-from-microsoft"></a>Personlige konti kontra arbejde eller skolekonti fra Microsoft

Microsoft administrerer to typer brugerkonti:

- **Personlige konti** (tidligere kaldet Windows Live ID). Disse konti repræsenterer forholdet mellem *individuelle* brugere og Microsoft, og der bruges til at få adgang til forbrugerenheder og -tjenester fra Microsoft. Disse konti er beregnet til personligt brug.

- **Arbejds- eller skolekonto konti.** Disse konti administreres af Microsoft på vegne af organisationer, der bruger Azure Active Directory. Disse konti bruges til at logge på Office 365 og andre business-tjenester fra Microsoft.

Microsoft arbejds- eller skolekonto konti typisk er tildelt til slutbrugere (medarbejdere, studerende, national medarbejdere) af deres organisationer (virksomhed, skole, government kontoret). Disse konti er enten styr direkte i skyen, i Azure AD-platformen, eller synkroniseret til Azure AD fra en lokal adresseliste, som Windows Server Active Directory. Microsoft er *custodian* af konti arbejds- eller skolekonto, men kontiene, der er ejet og kontrolleret af organisationen.

## <a name="referring-to-azure-ad-accounts-in-your-application"></a>Referere til Azure AD-konti i dit program

Microsoft fremvise ikke slutbrugere på Azure eller Active Directory-mærker, og ingen, skal du.

- Når brugere er logget på, skal du bruge organisationens navn og logo som muligt. Dette er bedre end ved hjælp af generisk udtryk, såsom "organisationen".

- Når brugerne ikke er logget på, skal du henvise til deres konto som "arbejde eller skolekonti" og bruge Microsoft-logoet til at formidle, at disse konti administreres af Microsoft. Brug ikke udtryk opretter brugerforvirring, såsom "enterprise konto", "business-konto" eller "virksomhedskonto".

## <a name="user-account-pictogram"></a>Bruger konto piktogram
I en tidligere version af disse retningslinjer anbefales det ved hjælp af et piktogram "blå badge". Baseret på bruger- og udvikler feedback, anbefaler vi nu brug af Microsoft-logoet i stedet. Dette vil hjælpe brugerne forstår, at de kan genbruge den konto, de bruger med Office 365 eller anden Microsoft business services til at logge på din app.

## <a name="signing-up-and-signing-in-with-azure-ad"></a>Logge og logge på med Azure AD

Din app kan præsentere separate kurver for tilmelding og log på, og de følgende afsnit indeholder visuelle vejledning til begge scenarier.

**Hvis din app understøtter slutbrugerens log op (fx gratis prøveversion eller freemium modellen)**: Du kan vise en **logon** -knap, så brugerne kan få adgang til din app med sin arbejdskonto eller deres personlige konto. Azure AD, vises en meddelelse om samtykke første gang de få adgang til din app.

**Hvis din app kræver tilladelser, der kun administratorer kan samtykke til, eller hvis din app kræver organisatoriske licenser**: Du skal adskille administrator acquisition fra brugerens logonadresse. **Knappen "Hent denne app"** omdirigerer administratorer for at logge på og bede dem om at give tilladelse på vegne af brugerne i organisationen. Dette er en ekstra fordel skjule slutbrugere samtykke vejledningen til din app.

## <a name="visual-guidance-for-app-acquisition"></a>Visuelle vejledning til app acquisition

Hyperlinket "Hent appen" skal omdirigere brugeren til Azure AD give adgang (Godkend) side for at tillade en organisations administrator for at tillade, at din app skal have adgang til organisationens data, der er hostet af Microsoft. Oplysninger om, hvordan du anmoder om adgang gennemgås i artiklen [Integrering af programmer med Azure Active Directory](active-directory-integrating-applications.md) .

Når administratorer samtykke til din app, kan de vælge at føje det til brugernes app launcher grænseflade i Office 365 (tilgængeligt fra vaflen og fra [https://portal.office.com/myapps](https://portal.office.com/myapps)). Hvis du vil gøre opmærksom på denne egenskab, kan du bruge udtryk, såsom "Tilføj denne app til din organisation" og vise en knap således:

![Programmet typer og -scenarier](./media/active-directory-branding-guidelines/add-to-my-org.png)

Men, anbefales det, at du skriver forklarende tekst i stedet for stole på knapperne. Eksempel:
> *Hvis du allerede bruger Office 365 eller andre business-tjeneste fra Microsoft, kan du blot give < your_app_name > adgang til din organisations data. Dette, så dine brugere at få adgang til < your_app_name > med deres eksisterende arbejde konti.*


## <a name="visual-guidance-for-sign-in"></a>Visuelle vejledning til logon
Din app skal vises et tegn i knap, der omdirigerer brugere til det logon-slutpunkt, der svarer til den protokol, du kan bruge til at integrere med Azure AD. Følgende afsnit indeholder oplysninger om hvordan knappen skal se ud.

### <a name="pictogram-and-sign-in-with-microsoft"></a>Piktogram og "Logge på med Microsoft"
Det er tilknytningen af Microsoft-logoet og for "Log på med Microsoft", der identificerer repræsenterer Azure AD blandt andre identitetsudbydere din app kan understøtte. Hvis du ikke har nok ledig plads til "Log på med Microsoft", er det ok at afkorte "Logger jeg på".

![Programmet typer og -scenarier](./media/active-directory-branding-guidelines/sign-in-with-microsoft-light.png)

![Programmet typer og -scenarier](./media/active-directory-branding-guidelines/sign-in-light.png)

Du kan også bruge et mørke farveskema for knapperne.

![Programmet typer og -scenarier](./media/active-directory-branding-guidelines/sign-in-with-microsoft-dark.png)

![Programmet typer og -scenarier](./media/active-directory-branding-guidelines/sign-in-dark.png)

## <a name="branding-dos-and-donts"></a>Branding råd

**Bruger "arbejds- eller skolekonto konto" sammen med knappen "Log på med Microsoft" til at angive yderligere forklaring for at hjælpe slutbrugere genkender om de kan bruge den.** Brug **ikke** andre begreber som "enterprise konto", "business-konto" eller "virksomhedskonto."

Brug **ikke** "Office 365-ID" eller "Azure-ID". Office 365 er også navnet på en forbruger tilbud fra Microsoft, som ikke bruge Azure AD til godkendelse.

**Undgå** at ændre Microsoft-logoet.

**Ikke** vise slutbrugere til Azure eller Active Directory mærker. Dog er det ok at bruge disse vilkår med udviklere, IT-medarbejdere og administratorer.

## <a name="navigation-dos-and-donts"></a>Navigation råd

**Giver en måde, hvorpå brugere logger af og skifte til en anden brugerkonto.** De fleste personer har en enkelt personlig konto fra Microsoft/Facebook/Google/Twitter, men personer er ofte knyttet til mere end én organisation. Understøttelse af flere logget på brugere kommer snart.
