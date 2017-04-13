

<properties
    pageTitle="Deltage i en personlig enhed for din organisation | Microsoft Azure"
    description="Forklarer, hvordan brugerne kan registrere deres personlige Windows 10-enheder til deres virksomhedens netværk, og indeholder trin til installation på et BYOD scenarie."
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

# <a name="join-a-personal-device-to-your-organization"></a>Deltage i en personlig enhed for din organisation

## <a name="to-join-a-windows-10-device-to-your-organization"></a>Deltage i en Windows 10-enhed til din organisation

1.  Vælg **Indstillinger**fra menuen **Start** .
2.  Vælg **konti**, og klik derefter på **din konto**.
3.  Klik på **Tilføj arbejds- eller skolekonto**, og skriv derefter i din virksomhedskonto.
4.  Angiv dit brugernavn og din adgangskode på siden Log på for din organisation, og klik derefter på **OK**.
5.  Du bliver bedt om til en multi-Factor authentication udfordring. (Denne udfordring er konfigurerbar ved en IT-administrator).
6.  Azure Active Directory (Azure AD) kontrollerer, om enheden kræver tilmelding til administration af mobile enhed.
7.  Windows registrerer enheden i organisationens directory i Azure AD og tilmelder i administration af mobilenheder, hvis det er relevant.
8.  Hvis du bruger en administreret, fører Windows dig til skrivebordet gennem automatisk logon.
9.  Hvis du er medlem af organisationsnetværket bruger, kan du blive ført til en Windows-logonskærm angive dine legitimationsoplysninger.

## <a name="additional-information"></a>Yderligere oplysninger
* [Windows 10 for virksomheden: måder at bruge enheder for arbejde](active-directory-azureadjoin-windows10-devices-overview.md)
* [Hvis du vil udvide skyen funktioner til Windows 10-enheder via Azure Active Directory deltage](active-directory-azureadjoin-user-upgrade.md)
* [Godkendelse af identiteter uden adgangskoder via Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Få mere at vide om brugsscenarier til Azure AD deltage](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Tilslutte enheder, der er medlem af et domæne til Azure AD til Windows 10 oplevelser](active-directory-azureadjoin-devices-group-policy.md)
* [Konfigurere Azure AD deltage](active-directory-azureadjoin-setup.md)
