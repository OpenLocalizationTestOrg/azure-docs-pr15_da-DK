<properties
    pageTitle="Azure AD Connect og sammenslutning | Microsoft Azure"
    description="Denne side er en central placering til alle dokumentation vedrørende AD FS-handlinger ved hjælp af Azure AD-forbindelse"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="anandy"/>


# <a name="azure-ad-connect-and-federation"></a>Azure AD-forbindelse og sammenslutning

Azure AD-forbindelse kan du konfigurere sammenslutning med lokale AD FS og Azure AD. Med sammenslutning sign-on, kan du aktivere brugere til at logge Azure AD baseret services med deres lokale adgangskoder og, mens du er på virksomhedens netværk, uden at skulle angive sin adgangskode igen. Indstillingen sammenslutning med AD FS kan du installere en ny eller angive en eksisterende AD FS i Windows Server 2012 R2 farm.

Dette emne er Startside for oplysninger om sammenslutning relaterede funktioner til Azure AD Connect og lister over links til alle andre emner, der er relateret til den. Links til Azure AD-forbindelse, kan du se integrere dine lokale identiteter med Azure Active Directory.

## <a name="azure-ad-connect---federation-topics"></a>Azure AD-forbindelse - sammenslutning emner

| Emne | Hvad det dækker, og hvornår skal jeg læse |
|:------|:-----------|
| **Azure AD-forbindelse logon brugerindstillinger** ||
| [Forstå brugerindstillinger-logon](active-directory-aadconnect-user-signin.md) | Beskrivelse af forskellige bruger-logon indstillinger, og hvordan de påvirker Azure-logon brugeroplevelsen |
| **AD FS installation ved hjælp af Azure AD-forbindelse**||
| [Forudsætninger](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) | Forudsætninger for en vellykket AD FS-installation via Azure AD-forbindelse|
| [Konfigurere AD FS-farm](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) | Hvordan du installerer en ny AD FS-farm ved hjælp af Azure AD-forbindelse |
| **Ændring af AD FS-konfiguration** | |
| [Hvis du reparerer trust](active-directory-aadconnect-federation-management.md#reparing-the-trust) | Sådan repareres aktuelle tillid mellem lokale AD FS og Office 365 / Azure |
| [Tilføje en ny AD FS-server](active-directory-aadconnect-federation-management.md#adding-a-new-ad-fs-server) | Udvide AD FS-farm med flere AD FS server indlæg indledende installation |
| [Tilføje en ny AD FS WAP-server](active-directory-aadconnect-federation-management.md#adding-a-new-wap-server) | Udvide AD FS-farm med flere WAP server indlæg indledende installation |
| [Tilføje et nyt medlem af organisationsnetværket domæne](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain) | Føje et andet domæne til at være knyttet til Azure AD |
|**Opgaver efter installationen**||
| [Tilføje brugerdefinerede firmalogo / illustration](active-directory-aadconnect-federation-management.md#add-custom-company-logo-or-illustration)| Ændre den logonoplevelse ved at angive den brugerdefineret logo, der vises på siden AD FS-logon |
| [Tilføj beskrivelse-logon](active-directory-aadconnect-federation-management.md#add-sign-in-description) | Ændre logon beskrivelse på siden AD FS-logon | 
| [Ændre AD FS gøre krav på regler](active-directory-aadconnect-federation-management.md#modifying-ad-fs-claim-rules) | Ændre / tilføje Kræv regler i AD FS svarer til Azure AD-forbindelse Synkroniser konfiguration |


## <a name="additional-resources"></a>Yderligere ressourcer

* [Integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md)
* [AD FS installation i Azure](active-directory-aadconnect-azure-adfs.md)
* [Høj tilgængelighed kryds-geografiske AD FS installation i Azure med Azure trafik Manager](active-directory-adfs-in-azure-with-azure-traffic-manager.md)


