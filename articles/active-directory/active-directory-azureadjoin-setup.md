<properties
    pageTitle="Konfiguration af Azure AD deltage for dine brugere | Microsoft Azure"
    description="Forklarer, hvordan administratorer kan konfigurere Azure AD deltage for lokal adresseliste og enhed registrering."
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
    ms.topic="get-started-article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="setting-up-azure-ad-join-in-your-organization"></a>Konfiguration af Azure AD deltage i din organisation

Før du konfigurerer Azure Active Directory deltage i (Azure AD deltage), skal du enten synkronisere op i mappen lokale brugere i skyen eller manuelt oprette administrerede konti i Azure AD.

Detaljerede anvisninger til synkronisering af dine lokale brugere til Azure AD dækkes integrere [dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).


Du kan manuelt oprette og administrere brugere i Azure AD ved at referere til [Brugeradministration i Azure AD](https://msdn.microsoft.com/library/azure/hh967609.aspx).

## <a name="set-up-device-registration"></a>Konfigurere enheden registrering
1. Log på portalen Azure som administrator.
2. Vælg **Active Directory**i den venstre rude.
3. Vælg mappen under fanen **mappe** .
4. Vælg fanen **Konfigurer** .
5. Gå til afsnittet **enheder** .
6. Angiv følgende under fanen **enheder** :  
   * **Maksimale antal af enheder hver bruger**: Vælg det maksimale antal enheder, en bruger kan have på Azure AD.  Hvis en bruger, når denne kvote, vil de ikke kunne tilføje flere enheder, indtil en eller flere af deres eksisterende enheder fjernes.
   * **Kræver Multi-FACTOR-AUTH til JOINFORBINDELSE enheder**: angive, om brugere skal angive en anden godkendelse faktor for at deltage i deres enhed til Azure AD. Se [Introduktion til Azure Multi-Factor Authentication i skyen](..\multi-factor-authentication\multi-factor-authentication-get-started-cloud.md), kan finde flere oplysninger om Azure Multi-Factor Authentication.
   * **Brugere kan AZURE AD JOINFORBINDELSE enheder**: Vælg brugere og grupper, der har tilladelse til at tilslutte enheder til Azure AD.
   * **Flere ADMINISTRATORER til AZURE AD JOINFORBUNDNE enheder**: Azure AD-Premium eller Enterprise mobilitet pakke (Redigere), kan du vælge, hvilke brugere er tildelt lokale administratorrettigheder til enheden. Globale administratorer og ejere af enhed er tildelt lokale administratorrettigheder som standard.

<center>![Konfigurere enheden regisration](./media/active-directory-azureadjoin/active-directory-aadjoin-configure-devices.png)</center>

Når du konfigureret Azure AD deltage for dine brugere, kan de oprette forbindelse til Azure AD via deres virksomhedens eller personlige enheder.

Følgende er de tre scenarier, du kan bruge til at aktivere dine brugere til at konfigurere Azure AD deltage:

- Brugere deltage i en ejes af firmaet enhed direkte til Azure AD.
- Brugere-forbindelse til et domæne en firmaet ejet enhed til lokale Active Directory og udvid derefter enheden til Azure AD.
- Brugere føje arbejds- eller skolekonto konti til Windows på en personlig enhed

## <a name="additional-information"></a>Yderligere oplysninger
* [Windows 10 for virksomheden: måder at bruge enheder for arbejde](active-directory-azureadjoin-windows10-devices-overview.md)
* [Hvis du vil udvide skyen funktioner til Windows 10-enheder via Azure Active Directory deltage](active-directory-azureadjoin-user-upgrade.md)
* [Få mere at vide om brugsscenarier til Azure AD deltage](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Tilslutte enheder, der er medlem af et domæne til Azure AD til Windows 10 oplevelser](active-directory-azureadjoin-devices-group-policy.md)
* [Konfigurere Azure AD deltage](active-directory-azureadjoin-setup.md)
