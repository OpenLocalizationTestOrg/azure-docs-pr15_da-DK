<properties
    pageTitle="Azure AD rettigheder identitet Management | Microsoft Azure"
    description="Et emne, der forklarer, hvad Azure AD privilegerede identitet Management er, og hvordan PIM til at forbedre sikkerheden skyen."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="kgremban"/>

# <a name="azure-ad-privileged-identity-management"></a>Azure AD rettigheder identitet administration

Du kan administrere, kontrol og overvåge adgang i din organisation med Azure Active Directory (AD) privilegerede identitet administration. Dette omfatter adgang til ressourcer i Azure AD og andre Microsoft online services som Office 365 eller Microsoft Intune.  

> [AZURE.NOTE] Privilegerede identitet Management fås kun til Premium P2 udgaven af Azure Active Directory. Du kan finde yderligere oplysninger finder [Azure Active Directory-versioner](active-directory-editions.md).

Organisationer vil minimere antallet af personer, der har adgang til sikre oplysninger eller ressourcer, fordi, mindsker risikoen for en introduktion, som access hacker. Men brugere stadig har brug at gennemføre privilegerede transaktioner i Azure, Office 365 eller SaaS apps. Organisationer give brugere privilegerede adgang i Azure AD uden overvågning, hvad disse brugere laver med deres administratortilladelser. Azure AD rettigheder identitet Management hjælper med at løse denne risiko.  

Azure AD rettigheder identitet Management hjælper dig med at:  

- Se, hvilke brugere er Azure AD-administratorer
- Aktivere efter behov, "bare i gang" administratoradgang til Microsoft Online Services som Office 365 og Intune
- Få rapporter om administrator se oversigten og ændringer i administrator tildelinger
- Få beskeder om adgang til en privilegerede rolle

Azure AD privilegerede identitet Management kan administrere indbyggede Azure AD organisatoriske roller, herunder:  

- Global Administrator
- Faktureringsadministrator
- Administratoren af tjenesten  
- Brugeradministrator
- Adgangskodeadministrator

## <a name="just-in-time-administrator-access"></a>Bare i gang administratoradgang

Historisk set kan du tildele en bruger til en administratorrolle via Azure klassisk administrationsportalen eller Windows PowerShell. Som et resultat, bliver brugeren en **permanent administrator**altid aktiv i den tildelte rolle. Azure AD rettigheder identitet Management introducerer konceptet med et **gyldigt administrator**. Gyldigt administratorer skal være brugere, der skal have adgang til privilegerede nu og derefter, men ikke hver dag. Rollen er inaktiv, indtil brugeren skal have adgang, så de fuldføre en aktiveringsprocessen, og bliv administrator aktive efter et forudbestemt antal tid.

## <a name="enable-privileged-identity-management-for-your-directory"></a>Aktivere privilegerede identitet Management til adresselisten

Du kan begynde at bruge Azure AD privilegerede identitet Management i [Azure-portalen](https://portal.azure.com/).

>[AZURE.NOTE] Du skal være global administrator med en organisationskonto (for eksempel @yourdomain.com), ikke en Microsoft-konto (for eksempel @outlook.com), til at aktivere Azure AD privilegerede identitet administration for en mappe.

1. Log på [Azure-portalen](https://portal.azure.com/) som global administrator i adresselisten.
2. Hvis din organisation har mere end én mappe, kan du vælge dit brugernavn i øverste højre hjørne af portalen Azure. Vælg den mappe, hvor du vil bruge Azure AD privilegerede identitet administration.
3. Markere **flere tjenester** og bruge tekstfeltet Filter til at søge efter **Azure AD privilegerede identitet administration**.
4. Markér **Fastgør til dashboard** , og klik derefter på **Opret**. Programmet privilegerede identitet Management åbnes.

Hvis du er den første person om at bruge Azure AD privilegerede identitet Management i biblioteket, derefter vejleder [guiden Sikkerhed](active-directory-privileged-identity-management-security-wizard.md) dig gennem den indledende tildeling oplevelse. Efter, bliver du automatisk den første **sikkerhedsadministrator** og **privilegerede rolle administrator** af mappen.

Kun privilegerede rolle administratorer kan administrere adgangen for andre administratorer. Du kan [give andre brugere mulighed for at administrere i PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="privileged-identity-management-dashboard"></a>Privilegerede identitet administrationsdashboard

Azure AD rettigheder identitet Manager indeholder et dashboard, der giver dig vigtige oplysninger f.eks.:

- Beskeder, der peger ud muligheder for at forbedre sikkerheden
- Antallet af brugere, der er tildelt hver privilegerede rolle  
- Antallet af berettiget og permanent administratorer
- Få vedvarende adgang gennemsyn

![PIM dashboard - skærmbillede][2]

## <a name="privileged-role-management"></a>Privilegerede rollestyring

Du kan administrere administratorer ved at tilføje eller fjerne administratorer af permanente eller berettiget til de enkelte roller med Azure AD privilegerede identitet administration.

![Administratorer af Tilføj/fjern PIM - skærmbillede][3]

## <a name="configure-the-role-activation-settings"></a>Konfigurere indstillingerne for rolle aktivering

Du kan konfigurere egenskaberne berettiget rolle aktivering, herunder ved hjælp af [Rolleindstillinger](active-directory-privileged-identity-management-how-to-change-default-settings.md) :

- Varigheden af den rolle aktiveringsperiode
- Rolle aktivering meddelelsen
- Oplysninger, brugeren skal angive under aktiveringsprocessen rolle  

![PIM indstillinger - administrator aktivering - skærmbillede][4]

Bemærk, at i billedet, knapper til **Multi-Factor Authentication** er deaktiveret. For visse, meget følsomme roller, vi Kræv MFA til forhøjede beskyttelse.

## <a name="role-activation"></a>Rolle aktivering  

[Aktivere en rolle](active-directory-privileged-identity-management-how-to-activate-role.md), administrator berettiget anmoder om en tid bundet "aktivering" for rollen. Aktiveringen kan anmodes ved hjælp af indstillingen **Aktivér min rolle** i administration af Azure AD privilegerede identitet.

En administrator, der vil aktivere en rolle skal der Azure AD privilegerede identitet Management i portalen Azure.

Rolle aktivering kan tilpasses. Du kan bestemme længden af aktivering, og hvad i indstillingerne PIM oplysninger administratoren skal oplyse for at aktivere rollen.

![PIM administrator anmodning rolle aktivering - skærmbillede][5]

## <a name="review-role-activity"></a>Gennemse rolle aktivitet

Der er to måder til at registrere, hvordan dine medarbejdere og administratorer bruger privilegerede roller. Den første indstilling bruger [Oversigt over overvågning](active-directory-privileged-identity-management-how-to-use-audit-log.md). Overvåge historikken logfører Registrer ændringer i privilegerede rolletildelinger og rolle aktivering historik.

![PIM aktivering oversigt - skærmbillede][6]

Den anden mulighed er at konfigurere almindelig [access gennemser](active-directory-privileged-identity-management-how-to-start-security-review.md). Disse access anmeldelser kan udføres af og tildelt korrekturlæser (som et team manager) eller medarbejderne kan gennemse sig selv. Dette er den bedste måde at overvåge der stadig kræver access, og der ikke længere fungerer.


## <a name="next-steps"></a>Næste trin
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png
