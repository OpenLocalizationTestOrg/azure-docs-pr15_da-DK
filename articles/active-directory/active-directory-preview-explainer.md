<properties
    pageTitle="Azure Active Directory preview explainer | Microsoft Azure"
    description="Et emne, der forklarer forskellene mellem Azure Active Directory i portalen klassisk og Azure Active Directory-eksemplet på portalen Azure."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="curtand"/>


# <a name="preview-of-the-azure-active-directory-management-experience-in-the-azure-portal"></a>Eksempelvisning af Azure Active Directory management-oplevelsen i portalen Azure

Azure Active Directory (Azure AD) management oplevelsen er i udskrift på portalen Azure. Du kan prøve den ud ved at logge ind [på Azure](https://portal.azure.com) -portalen som global administrator i adresselisten. Derefter Vælg Azure Active Directory i listen over tjenester, hvis det er synligt, eller Vælg **flere tjenester** til at få vist listen over alle tjenester. Du behøver ikke en Azure-abonnement for at bruge Azure AD management støder på portalen Azure.


## <a name="capabilities-of-the-preview-experience"></a>Funktionerne i preview-oplevelse

Preview oplevelsen gør det muligt at administrere mange directory ressourcer, som brugere, grupper og programmer samt directory indstillinger i portalen Azure. Vi forbedrer denne oplevelse for at medtage alle de funktioner, der findes i Azure AD management oplevelse i [Azure klassisk portal](https://manage.windowsazure.com). Indtil da er der nogle directory styring af opgaver, du skal stadig fuldført i portalen klassisk.

## <a name="manage-the-same-azure-ad-tenants"></a>Administrere de samme Azure AD-lejere

Preview oplevelsen læser og skriver til den samme Azure Active Directory-lejer som klassisk portalen og Office 365 administration. Ændringer i en af disse portaler afspejles i alle de andre.

## <a name="use-the-same-authorization-logic"></a>Brug den samme tilladelse logik

Preview oplevelsen bruger den samme tilladelse logik som eksisterende Active Directory-klienter. Brugere har tilladelse til at foretage ændringer i directory ressourcer, der er baseret på deres directory rollen som global administrator, Brugeradministrator, adgangskodeadministrator. Har du en rolle på Azure ressourcer eller Azure-abonnement tillader ikke en bruger til at administrere directory ressourcer. Du kan finde flere oplysninger om Azure AD management roller, [tildele administratorroller i Azure Active Directory](active-directory-assign-admin-roles.md). 

Eksempel-oplevelse er optimeret til globale administratorer. Hvis du bruger preview-oplevelsen, mens logget på som en bruger, der ikke er en global administrator, skal du muligvis en forringet oplevelse. For eksempel kan du kunne vælge en knap, hvor du kan starte en opgave, du ikke kan gennemføre i kataloget. Vi forbedrer denne oplevelse snart.
 
## <a name="tell-us-what-you-think"></a>Fortæl os din mening

Du kan give feedback om preview oplevelse i sektionen Administration portalen af [Azure AD feedback-forummet](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&filter=alltypes&sort=lastpostdesc).
