<properties
   pageTitle="Sådan kræver Multi-Factor authentication | Microsoft Azure"
   description="Lær at kræve Multi-Factor authentication (MFA) for privilegerede identiteter med Azure Active Directory privilegerede identitet Management filtypenavnet."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/01/2016"
   ms.author="kgremban"/>

# <a name="how-to-require-mfa-in-azure-ad-privileged-identity-management"></a>Sådan kræver MFA Azure AD privilegerede identitet administration

Vi anbefaler, at du kræver Multi-Factor authentication (MFA) for alle dine administratorer. Dette reducerer risikoen for et angreb på grund af en kompromitteret adgangskode.

Du kan kræve, at brugere fuldfører en MFA udfordring, når de logger på. Blogindlæg [MFA til Office 365 og MFA til Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/) sammenligner, hvad der skal medtages i Office og Azure abonnementer med de funktioner, der er indeholdt i Microsoft Azure Multi-Factor Authentication tilbyder.

Du kan også kræve, at brugere fuldfører en MFA udfordring, når de aktiverer en rolle i Azure AD PIM. Denne måde, hvis brugeren ikke gennemføre en MFA udfordring, når de er logget på, de vil blive bedt om det ved PIM.

## <a name="requiring-mfa-in-azure-ad-privileged-identity-management"></a>Kræve MFA Azure AD rettigheder identitet administration

Når du administrerer identiteter i PIM privilegerede rolle som administrator, kan du se beskeder, der anbefales MFA for privilegerede konti. Klik på sikkerhedsmeddelelsen i dashboardet PIM, og en ny blade åbnes med en liste over administratorkonti, der skal kræver MFA.  Du kan kræve MFA ved at markere flere roller og derefter klikke på knappen **Ret** , eller du kan klikke på ellipserne ud for bestemte roller og derefter klikke på knappen **Ret** .

> [AZURE.IMPORTANT] Lige nu, Azure MFA fungerer kun med arbejds- eller skolekonti, ikke Microsoft-konti (normalt en personlig konto, der bruges til at logge på Microsoft-tjenester som Skype, Xbox, Outlook.com osv.). På grund af det, kan alle, der bruger en Microsoft-konto ikke administrator berettiget, fordi de ikke kan bruge MFA til at aktivere deres roller. Hvis disse brugere skal fortsætte med at administrere arbejdsbelastninger, som bruger en Microsoft-konto, skal du ændre dem til permanent administratorer for nu.

Desuden kan du ændre MFA kravet om en bestemt rolle ved at klikke på den i afsnittet roller i dashboardet PIM. Klik derefter på **Indstillinger** i bladet rolle og derefter vælge **Aktivér** under Multi-Factor godkendelse.

## <a name="how-azure-ad-pim-validates-mfa"></a>Hvordan Azure AD PIM valideret MFA

Der er to muligheder for at validere MFA, når en bruger aktiverer en rolle.

Den nemmeste mulighed er at regne Azure MFA for brugere, der aktiverer en privilegerede rolle. For at gøre dette, Kontrollér først, at disse brugere har licens, hvis det er nødvendigt, og har registreret til Azure MFA. Du kan finde flere oplysninger om, hvordan du gør dette, er i [Introduktion til Azure Multi-Factor Authentication i skyen](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md). Det er anbefales, men ikke påkrævet, at du konfigurerer Azure AD for at gennemtvinge MFA for disse brugere, når de logger på. Dette skyldes, at MFA Kontroller foretages ved Azure AD PIM sig selv.

Hvis brugere godkendes lokalt kan du også få din er ansvarlig for MFA-identitetsudbyder. Hvis du har konfigureret AD indeholder Federation Services til at kræve chipkort-baseret godkendelse før at få adgang til Azure AD, [sikring af skyen ressourcer med Azure Multi-Factor Authentication og AD FS](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md) eksempelvis vejledning til konfiguration af AD FS sende krav til Azure AD. Når en bruger forsøger at aktivere en rolle, accepterer Azure AD PIM, MFA allerede er blevet valideret for brugeren, når den modtager de relevante krav.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Næste trin
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
