<properties
   pageTitle="Oversigt over Azure identitet management sikkerhed | Microsoft Azure"
   description=" Microsoft identitet og access management-løsninger, der hjælper IT beskytte adgang til programmer og ressourcer på tværs af virksomhedens datacenter og i skyen, så i flere niveauer af validering som Multi-Factor authentication og betinget access politikker. I denne artikel giver et overblik over grundlæggende Azure sikkerhedsfunktioner, der hjælper med administration af identitet. "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/09/2016"
   ms.author="terrylan"/>

# <a name="azure-identity-management-security-overview"></a>Oversigt over Azure identitet management-sikkerhed

Microsoft identitet og access management-løsninger, der hjælper IT beskytte adgang til programmer og ressourcer på tværs af virksomhedens datacenter og i skyen, så i flere niveauer af validering som Multi-Factor authentication og betinget access politikker. Overvågning mistænkelig aktivitet via avancerede sikkerhedsindstillinger rapportering, overvågning og advarer hjælper med at reducere potentielle sikkerhedsproblemer. [Azure Active Directory Premium](../active-directory/active-directory-editions.md) indeholder single sign-on til tusindvis af skyen (SaaS) apps og adgang til webapps, du har kørt i det lokale miljø.

Sikkerhed fordele af Azure Active Directory (AD) omfatter muligheden for at:

- Oprette og administrere en enkelt identitet for hver bruger på tværs af din virksomheds-hybrid, holde brugere, grupper og enheder synkroniseret
- Angive enkelt enkeltlogon-adgang til dine programmer, herunder tusindvis af allerede integrerede SaaS apps
- Aktivere programmet access-sikkerhed ved at gennemtvinge regler-baserede Multi-Factor Authentication både lokalt og programmer i skyen
- Klargøre sikker fjernadgang til lokale webprogrammer via Azure AD-proxyen

Mål i denne artikel er at tilvejebringe et overblik over grundlæggende Azure sikkerhedsfunktioner, der hjælper med administration af identitet. Vi giver også links til artikler, der giver oplysninger om hver funktion, så du kan få mere at vide.  

I artiklen fokuserer på følgende core Azure identitet management funktioner:

- Enkeltlogon
- Omvendt proxy
- Multi-Factor authentication
- Overvågning af sikkerhed, beskeder og machine learning-baserede rapporter
- Forbruger identitets- og administration
- Enhed registrering
- Administration af privilegerede identitet
- Beskyttelse af din identitet
- Hybrid identitet administration

## <a name="single-sign-on"></a>Enkeltlogon

Enkelt enkeltlogon (SSO) betyder at få adgang til alle programmer og ressourcer, som du skal gøre business, ved at logge på kun én gang ved hjælp af en enkelt brugerkonto. Når du er logget, du kan få adgang til alle de programmer, du har brug for uden at der ikke kræves godkendelse (fx skriver en adgangskode) endnu en gang.

Mange organisationer, der er afhængige af software som en (SaaS) tjenesteprogrammer som Office 365, feltet og Salesforce for slutbrugerens produktivitet. Historisk set IT-medarbejdere, der er nødvendige at oprette og opdatere brugerkonti i hvert SaaS program enkeltvis, og brugere skulle huske en adgangskode for hvert SaaS program.

Azure AD udvider lokale Active Directory i skyen, så brugerne kan bruge deres primære organisationskonto ikke kun logge på deres enheder, der er medlem af et domæne og firmaoplysninger ressourcer, men også alle de web og SaaS programmer bruges til deres arbejde.

Ikke kun brugere har ikke til at administrere flere sæt af brugernavne og adgangskoder, adgang til programmer kan automatisk klargjort eller deaktivere klargjort baseret på organisatoriske grupper og deres status som en medarbejder. Azure AD introducerer sikkerhed og adgang til styring kontrolelementer, der gør det muligt at administrere centralt brugeres adgang på tværs af SaaS programmer.

Lær mere:

- [Oversigt over Single Sign-On](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
- [Hvad er adgang til programmer og single sign-on med Azure Active Directory?](../active-directory/active-directory-appssoaccess-whatis.md)
- [Integrere Azure Active Directory single sign-on – med SaaS apps](../active-directory/active-directory-sso-integrate-saas-apps.md)

## <a name="reverse-proxy"></a>Omvendt proxy

Azure AD-proxyen, kan du publicere lokale programmer, som [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) -websteder, [Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx)og [IIS](http://www.iis.net/)-baseret apps på dit eget privat netværk og giver sikker adgang til brugere uden for dit netværk. Proxyen indeholder fjernadgang og single sign-on (SSO) til mange typer lokale webprogrammer med tusindvis af SaaS programmer, der understøtter Azure AD. Medarbejdere kan logge på dine apps fra home på deres egne enheder og godkendes via denne skybaseret proxy.

Lær mere:

- [Aktivere Azure AD-proxyen](../active-directory/active-directory-application-proxy-enable.md)
- [Publicere programmer, der bruger Azure AD-proxyen](../active-directory/active-directory-application-proxy-publish.md)
- [Single-sign-on med proxyen](../active-directory/active-directory-application-proxy-sso-using-kcd.md)
- [Arbejde med betinget adgang](../active-directory/active-directory-application-proxy-conditional-access.md)

## <a name="multi-factor-authentication"></a>Multi-Factor authentication
Azure Multi-Factor authentication (MFA) er en metode til godkendelse, der kræver brug af mere end én metode til bekræftelse og tilføjer en kritiske anden sikkerhedslag bruger logon og transaktioner. MFA hjælper med at beskytter adgang til data og programmer under overholdelse af brugernes behov for en enkelt logonprocessen. Det leverer stærke godkendelse via et udvalg af bekræftelse – telefonopkald, SMS-besked eller mobilapp besked eller bekræftelse kode og 3 part OAuth tokens.

Lær mere:

- [Multi-Factor authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
- [Hvad er Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)
- [Hvordan fungerer Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-how-it-works.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Overvågning af sikkerhed, beskeder og machine learning-baserede rapporter

Sikkerhed overvågning og beskeder og machine learning-baserede rapporter, der identificerer inkonsekvent access mønstre kan hjælpe dig med at beskytte din virksomhed. Du kan bruge Azure Active Directorys adgang og anvendelsesrapporter for at få indsigt i integritet og sikkerheden for organisationens adressekartotek. Disse oplysninger, kan en administrator for directory bedre bestemme hvor mulige sikkerhedsrisici kan ligge, så de kan planlægge en dækkende måde at reducere disse risici.

I Azure klassisk portalen kategoriseres rapporter på følgende måder:

- Anomali rapporter – indeholde logon begivenheder, vi fandt være uoverensstemmende. Vores mål er at gøre dig opmærksom på sådanne aktiviteter og giver dig mulighed at kunne foretages en bestemmelse om, hvorvidt en begivenhed er mistænkelige.
- Integreret programmet rapporter – giver indsigt i hvordan skyen programmer bliver brugt i organisationen. Azure Active Directory tilbyder integration med tusindvis af skyen programmer.
- Fejlrapporter – angive fejl, der kan opstå, når konti til eksterne programmer.
- Brugerdefinerede rapporter – få vist enhed/logonside i aktivitetsdata for en bestemt bruger.
- Aktivitetslogfiler – indeholder en oversigt over alle reviderede begivenheder i seneste 24 timer, sidste 7 dage, eller de seneste 30 dage, samt gruppe aktivitet ændringer og adgangskode Nulstil og registrering aktivitet.

Lær mere:

- [Få vist din access- og brugsrapporter](../active-directory/active-directory-view-access-usage-reports.md)
- [Introduktion til Azure Active Directory-rapportering](../active-directory/active-directory-reporting-getting-started.md)
- [Azure Active Directory rapportering vejledning](../active-directory/active-directory-reporting-guide.md)

## <a name="consumer-identity-and-access-management"></a>Forbruger identitets- og administration

Azure Active Directory-B2C er en meget tilgængelig, global, identitet management-tjenesten til forbrugere mod programmer, der kan skaleres til hundredvis af millioner af identiteter. Det kan være integreret på tværs af mobile og web platforme. Dine brugere kan logge på alle dine programmer til at tilpasse oplevelser ved hjælp af deres eksisterende konti til sociale eller ved at oprette nye legitimationsoplysninger.

Tidligere ville udviklere der ønskede at tilmelde dig og logge på forbrugere til deres programmer være skrevet deres egen kode. Og de vil har brugt lokale databaser eller systemer til at gemme brugernavne og adgangskoder. Azure Active Directory-B2C tilbyder en bedre måde at integrere consumer identitet management i programmer med hjælp af en sikker, baseret på standarder platform og en stor mængde extensible politikker for din organisation.

Når du bruger Azure Active Directory B2C, kan dine brugere tilmelde dig til dine programmer ved hjælp af deres eksisterende konti til sociale (Facebook, Google, Amazon, LinkedIn) eller ved at oprette nye legitimationsoplysninger (mailadresse og adgangskode, eller brugernavn og adgangskode).

Lær mere:

- [Hvad er Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
- [Azure Active Directory B2C preview: logge op og logge på forbrugere i dine programmer](../active-directory-b2c/active-directory-b2c-overview.md)
- [Azure Active Directory B2C Preview: Typer programmer](../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>Enhed registrering

Azure AD-enhed registrering er grundlaget for scenarier med enhed-baserede [betinget adgang](../active-directory/active-directory-conditional-access-on-premises-setup.md) . Når en enhed er registreret, indeholder Azure Active Directory enhed registrering enhed med en identitet, som bruges til at godkende enheden, når brugeren logger ind. Den godkendte enhed, og attributterne for enheden, kan derefter bruges til at gennemtvinge politikker betinget adgang til programmer, der er placeret i skyen og i det lokale miljø.

Når kombineres med en mobilenhed management (MDM)-løsning som Intune, er enhed attributter i Azure Active Directory opdateret med yderligere oplysninger om enheden. Dette kan du oprette betingede adgangsregler, håndhæves adgang fra enheder til at opfylde dine standarder for sikkerhed og overholdelse af regler.

Lær mere:

- [Introduktion til Azure Active Directory enhed registrering](../active-directory/active-directory-conditional-access-device-registration-overview.md)
- [Konfiguration af lokale betinget access ved hjælp af Azure Active Directory enhed registrering](../active-directory/active-directory-conditional-access-on-premises-setup.md)
- [Automatisk enhed registrering med Azure Active Directory til Windows medlem af et domæne enheder](../active-directory/active-directory-conditional-access-automatic-device-registration.md)

## <a name="privileged-identity-management"></a>Administration af privilegerede identitet
Azure Active Directory (AD) privilegerede identitet administration kan du administrere, styre, og overvåge dine privilegerede identiteter og få adgang til ressourcer i Azure AD samt andre Microsoft online services som Office 365 eller Microsoft Intune.

Nogle gange skal brugere gennemføre privilegerede transaktioner i Azure- eller Office 365 ressourcer eller andre SaaS apps. Det betyder ofte organisationer har give dem permanent privilegerede adgang i Azure AD. Dette er en voksende sikkerhedsrisikoen for ressourcer, der er hostet skyen, fordi organisationer ikke kan tilstrækkeligt overvåge hvad disse brugere laver med deres administratortilladelser. Desuden, hvis en brugerkonto med privilegerede access afsløret, påvirke den ét brud deres overordnede skyen sikkerhed. Azure AD rettigheder identitet Management hjælper med at løse denne risiko.

Azure AD rettigheder identitet administration kan du:

- Se, hvilke brugere er Azure AD-administratorer
- Aktivere efter behov, "bare i gang" administratoradgang til Microsoft Online Services som Office 365 og Intune
- Få rapporter om administrator se oversigten og ændringer i administrator tildelinger
- Få beskeder om adgang til en privilegerede rolle

Lær mere:

- [Azure AD rettigheder identitet administration](../active-directory/active-directory-privileged-identity-management-configure.md)
- [Roller i Azure AD følsomme identitet administration](../active-directory/active-directory-privileged-identity-management-roles.md)
- [Azure AD privilegerede identitet administration: Hvordan du kan tilføje eller fjerne en brugerrolle](../active-directory/active-directory-privileged-identity-management-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Beskyttelse af din identitet
Azure AD identitet beskyttelse er en sikkerhed-tjeneste, som indeholder en samlet visning i risikoen begivenheder og potentielle svagheder påvirker din organisations identiteter. Beskyttelse af din identitet bruger eksisterende Azure Active Directorys anomali registrering funktioner (tilgængelige via Azure AD uoverensstemmende aktivitetsrapporter), og introducerer nye risikoen begivenhed typer, der kan registrere afvigelser i realtid.

Lær mere:

- [Beskyttelse af Azure Active Directory din identitet](../active-directory/active-directory-identityprotection.md)
- [Kanal 9: Azure AD og identitet Vis: identitet beskyttelse Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-management"></a>Hybrid identitet administration

Microsofts tilgang til identitet strækker sig over i det lokale miljø og skyen, oprette en enkelt bruger-id for godkendelse og autorisation til alle de ressourcer, uanset placering.

Lær mere:

- [Hybrid identitet hvidbog](http://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
- [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
- [Active Directory-teamets Blog](https://blogs.technet.microsoft.com/ad/)
