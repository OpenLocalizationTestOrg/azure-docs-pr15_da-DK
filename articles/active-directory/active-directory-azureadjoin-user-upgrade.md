<properties
    pageTitle="Konfigurere en Windows 10-enhed med Azure AD fra indstillinger | Microsoft Azure"
    description="Forklarer, hvordan brugere kan deltage til Azure AD via menuen Indstillinger."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="set-up-a-windows-10-device-with-azure-ad-from-settings"></a>Konfigurere en Windows 10-enhed med Azure AD under indstillinger
Hvis du allerede bruger Windows 7 eller Windows 8, og din computer eller enhed er blevet opgraderet til Windows 10, kan du deltage til Azure Active Directory (Azure AD) via menuen Indstillinger.

## <a name="to-join-to-azure-ad-from-the-settings-menu"></a>Azure AD tilsluttes fra menuen Indstillinger


1. Klik på amuletten **Indstillinger** fra menuen **Start** .
2. Under **Indstillinger**, vælge **System**->**om**->**deltage i Azure AD**.
<center>
![Deltage i Azure AD fra menuen Indstillinger](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png)</center>

3. Klik på **Fortsæt** i meddelelsesvinduet Azure AD deltage i.
<center>
![Deltag i Azure AD meddelelsesvinduet](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png)</center>
4. Angive dine logonoplysninger. Denne logonoplevelse omfatter alle de trin, der kræves til fuldført godkendelse. Hvis du er en del af en samlet lejer, får administratoren du den sammenslutning oplevelse, der er hostet af din organisation.
<center>
![Angive logonoplysninger](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png)</center>
5. Hvis din organisation har konfigureret Azure Multi-Factor Authentication for deltagelse i til Azure AD, kan du give den anden faktor før du fortsætter.
6. Klik på **Acceptér** på skærmbilledet **Tillad, at denne enhed administreres** .
7. Du bør se meddelelsen "enheden er nu knyttet til din organisation i Azure AD".


## <a name="additional-information"></a>Yderligere oplysninger
* [Få mere at vide om brugsscenarier til Azure AD deltage](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Tilslutte enheder, der er medlem af et domæne til Azure AD til Windows 10 oplevelser](active-directory-azureadjoin-devices-group-policy.md)
* [Konfigurere Azure AD deltage](active-directory-azureadjoin-setup.md)
* [Godkendelse af identiteter uden adgangskoder via Microsoft Passport](active-directory-azureadjoin-passport.md)
