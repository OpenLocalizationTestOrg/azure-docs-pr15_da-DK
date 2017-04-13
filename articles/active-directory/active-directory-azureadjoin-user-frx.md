<properties
    pageTitle="Konfigurere en ny enhed med Azure AD under installationen | Microsoft Azure"
    description="Et emne, der forklarer, hvordan brugere kan konfigurere Azure AD deltage under deres første køre oplevelse."
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

# <a name="set-up-a-new-device-with-azure-ad-during-setup"></a>Konfigurere en ny enhed med Azure AD under installationen

I Windows 10 skal brugere kan deltage i deres enheder til Azure Active Directory (Azure AD) i den første kørsel (FRX). Dette giver mulighed for virksomheder at distribuere shrink-wrapped enheder til deres medarbejdere eller studerende, eller lade dem vælge deres egen enheder (CYOD).
Hvis Windows 10 Professional eller Windows 10 Enterprise udgaver er installeret på en enhed, skal oplevelsen som standard under installationen af firmaet ejet enheder.

## <a name="to-join-a-device-to-azure-ad"></a>Deltage i en enhed til Azure AD


1. Når du slår på din nye enhed og start konfigurationen af den, skal du se **Få klar** meddelelsen. Følg vejledningen for at konfigurere din enhed.
2. Start ved at tilpasse dit område og sprog. Vælge accept licensvilkårene for Microsoft-Software.
![Tilpasse til dit område](./media/active-directory-azureadjoin/active-directory-azureadjoin-customize-region.png)
3. Vælg det netværk, du vil bruge til at oprette forbindelse til internettet.
4. Vælg, om du bruger en personlig enhed eller en firmaet ejet enhed. Hvis det er firmaet ejer, skal du klikke på **denne enhed hører til min organisation**. Dette starter den Azure AD deltage oplevelse. Følgende er en skærm, får du vist, hvis du bruger Windows 10 Professional.
<center>
![Hvem ejer denne PC-skærm](./media/active-directory-azureadjoin/active-directory-azureadjoin-who-owns-pc.png)

5.  Angiv de legitimationsoplysninger, som blev leveret af din organisation.
<center>
![-Logonskærm](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png)
6.  Når du har angivet dit brugernavn, er en tilsvarende lejer placeret i Azure AD. Hvis du er i et medlem af organisationsnetværket domæne, omdirigeres du til din lokale Service STS (Secure Token) server – for eksempel Active Directory Federation Services (AD FS).
7. Hvis du bruger et uden for organisationsnetværket domæne, skal du angive dine legitimationsoplysninger direkte på siden Azure AD-vært. Hvis firmalogo blev konfigureret, kan du også se organisationens logo og understøtter tekst.
8.  Du bliver bedt om til en multi-Factor authentication udfordring. Denne udfordring konfigureres af IT-administrator.
9.  Azure AD kontrollerer, om denne bruger/enhed kræver tilmelding til administration af mobilenheder.
10. Windows registrerer enheden i organisationens directory i Azure AD og tilmelder i administration af mobilenheder, hvis det er relevant.
11. Hvis du bruger en administreret, fører dig til skrivebordet gennem automatisk logon-processen i Windows.
12. Hvis du er medlem af organisationsnetværket bruger, bliver du bedt om at Windows-logonskærm angive dine legitimationsoplysninger.

> [AZURE.NOTE] Deltage i et lokalt Windows Server Active Directory-domæne i Windows out of box-oplevelsen understøttes ikke. Derfor, hvis du planlægger at deltage i en computer til et domæne, skal du markere linket **konfigurere Windows med en lokal konto** i stedet. Du kan derefter deltage domæne fra indstillingerne på din computer, som du har udført før.

## <a name="additional-information"></a>Yderligere oplysninger
* [Windows 10 for virksomheden: måder at bruge enheder for arbejde](active-directory-azureadjoin-windows10-devices-overview.md)
* [Hvis du vil udvide skyen funktioner til Windows 10-enheder via Azure Active Directory deltage](active-directory-azureadjoin-user-upgrade.md)
* [Godkendelse af identiteter uden adgangskoder via Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Få mere at vide om brugsscenarier til Azure AD deltage](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Tilslutte enheder, der er medlem af et domæne til Azure AD til Windows 10 oplevelser](active-directory-azureadjoin-devices-group-policy.md)
* [Konfigurere Azure AD deltage](active-directory-azureadjoin-setup.md)
