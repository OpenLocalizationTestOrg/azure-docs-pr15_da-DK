<properties
    pageTitle="Sikring af privilegerede adgang i Azure AD | Microsoft Azure"
    description="Et emne, der forklarer fremgangsmåder for at sikre privilegerede adgang på tværs af Azure, Azure Active Directory og Microsoft Online Services."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="mwahl"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="kgremban"/>


# <a name="securing-privileged-access-in-azure-ad"></a>Sikring af privilegerede adgang i Azure AD

Sikring af privilegerede access er et vigtigt første trin til at beskytte virksomhedens aktiver i en moderne organisation. Privilegerede konti er dem, administrere og administrere IT-systemer. Cyber hackere målrette disse konti for at få adgang til en organisations data og systemer. For at sikre privilegerede adgang, skal du isolere konti og systemer fra risikoen for at få vist en ondsindet bruger.

Flere brugere begyndt at få privilegerede adgang via skyen services. Dette kan omfatte globale administratorer af Office 365, Azure abonnement administratorer og brugere, der har administratoradgang FOS eller SaaS apps.

Microsoft anbefaler at du har fulgt denne vejledning til [Sikring af privilegerede adgang](https://technet.microsoft.com/library/mt631194.aspx).

For kunder med Azure Active Directory til at administrere adgang til Azure, Office 365, eller andre Microsoft-tjenester og programmer, anvende disse principper om brugerkonti administreres og godkendt af Active Directory eller i Azure Active Directory. Følgende afsnit indeholder flere oplysninger om Azure AD-funktioner til at understøtte sikring af privilegerede adgang.

## <a name="multi-factor-authentication"></a>Multi-Factor authentication

For at øge sikkerheden for administrator godkendelse, skal du kræver Multi-Factor authentication (MFA) før tildeling af rettigheder. MFA er en metode til at bekræfte, hvem du er, der kræver brug af mere end bare et brugernavn og din adgangskode. Den indeholder en anden sikkerhedslag bruger logon og transaktioner.

Azure Multi-Factor Authentication hjælper med at beskytter adgang til data og programmer under overholdelse af brugernes behov for en enkelt logonprocessen. Det leverer stærke godkendelse via et udvalg af nemt bekræftelse herunder telefonopkald, SMS-beskeder, mobilapp meddelelser, eller verifikationskoden og 3 part ed tokens.

Du kan finde en oversigt over, hvordan Azure Multi-Factor Authentication fungerer i den følgende video.

>[AZURE.VIDEO windows-azure-multi-factor-authentication]

Du kan finde flere detaljer, [MFA til Office 365 og MFA til Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/).

## <a name="time-bound-privileges"></a>Tid bundet rettigheder

Nogle organisationer kan være, at de har for mange brugere i meget privilegerede roller. En bruger kan er blevet føjet til rollen til en bestemt aktivitet, som til at tilmelde sig en tjeneste, men bruge ikke disse rettigheder, der ofte bagefter.

Hvis du vil sænke visning tidspunktet for rettigheder og øge din indsigt i deres brug, kan du begrænse brugere til kun at tage på deres rettigheder bare i gang (JIT), når de skal bruge til at udføre en opgave. Du kan bruge [Azure AD privilegerede identitet Administration (PIM)](http://aka.ms/AzurePIM)til Azure Active Directory og Microsoft Online Services.


![PIM dashboard][2]


## <a name="attack-detection"></a>Angreb registrering

[Azure Active Directory identitetsbeskyttelse](../active-directory-identityprotection.md) giver en samlet visning i risikoen begivenheder og potentielle svagheder påvirker din organisations identiteter. Beskyttelse af din identitet beregner på baggrund af risikohændelser, en brugerniveau risikoen for hver bruger, så du kan konfigurere risikoen-baserede politikker for at beskytte automatisk identiteter i din organisation. Disse politikker, sammen med andre betinget access-kontrolelementer, der leveres af Azure Active Directory og EMS, kan automatisk blokere brugeren eller tilbyder forslag, der omfatter nulstilling af adgangskoder og multi-Factor authentication aktivering.

![Azure AD-identitet beskyttelse][3]

## <a name="conditional-access"></a>Betinget adgang

Med betinget adgangskontrol kontrollerer Azure Active Directory på bestemte betingelser, du vælger, når godkendelse af en bruger, før du tillader adgang til et program. Når disse betingelser er opfyldt, kan brugeren er godkendt og har adgang til programmet.


![Angive betinget adgangsregler med MFA][4]


## <a name="related-articles"></a>Relaterede artikler

- Aktivere [Azure Multi-Factor Authentication](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)
- Aktivere [Azure AD rettigheder identitet administration](../active-directory-privileged-identity-management-configure.md)
- Aktivere [Azure AD-identitet beskyttelse](../active-directory-identityprotection.md)
- Aktivere [betinget adgang til kontrolelementer](../active-directory-conditional-access.md)


Se afsnittet "kunde ansvarsområder og vejledning" i [Microsoft Cloud-sikkerhed for Enterprise Architects](http://aka.ms/securecustomer) dokumentet kan finde flere oplysninger om opbygning af en fuldstændig sikkerhed vejledning. Kontakt din Microsoft-repræsentant for flere oplysninger om engagerende Microsoft services for at hjælpe med nogen af disse emner, eller besøge vores [Cybersecurity løsninger side](https://www.microsoft.com/microsoftservices/campaigns/cybersecurity-protection.aspx).

<!--Image references-->
[1]: ../media/active-directory-privileged-identity-management-configure/Search_PIM.png
[2]: ../media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ../media/active-directory-identityprotection/29.png
[4]: ../media/active-directory-conditional-access/conditionalaccess-saas-apps.png
