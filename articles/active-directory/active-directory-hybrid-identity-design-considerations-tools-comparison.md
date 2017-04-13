<properties
    pageTitle="Hybrid identitet: Katalogintegration værktøjer til sammenligning | Microsoft Azure"
    description="Dette er side indeholder en omfattende tabel, der sammenligner de forskellige katalogintegrationsværktøjer, der kan bruges til katalogintegration."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Hybrid identitet katalogintegration værktøjer til sammenligning

År har katalogintegrationsværktøjer vokset og udviklet.  Dette dokument er for at angive en samlet visning af disse værktøjer og en sammenligning af de funktioner, der er tilgængelige i hver.

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

>[AZURE.NOTE] Azure AD-forbindelse inkorporerer komponenter og funktionalitet, der er tidligere udgivet som Dirsync og AAD Sync. Disse funktioner er ikke længere blive udgivet enkeltvis, og alle fremtidige forbedringer medtages i opdateringer til Azure AD-forbindelse, så du altid ved, hvor du kan få de nyeste funktioner.
>
>DirSync og Azure Active Directory-synkronisering er forældet. Kan finde flere oplysninger i [her](active-directory-aadconnect-dirsync-deprecated.md).


Brug følgende nøgle for hver af tabellerne.

● = Tilgængelige nu  
Fransk = kommende versioner  
Sider = Public Preview  

## <a name="on-premises-to-cloud-synchronization"></a>Lokalt til skyen synkronisering

| Funktion  | Oprette forbindelse Azure Active Directory  | Azure Active Directory-Synkroniseringstjenester (AAD Sync) | Azure Active Directory-Synkroniseringsværktøj (DirSync)| Forefront identitet Manager 2010 R2 (FIM) |Microsoft identitet Manager 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Oprette forbindelse til enkelt lokale AD skov | ● | ● | ● | ● |● |
| Oprette forbindelse til flere lokale AD områder |●  | ● |  | ● |● |
| Oprette forbindelse til flere lokale Exchange Orgs | ● |  |  |  | |
| Oprette forbindelse til enkelt lokalt LDAP-adresseliste | FRANSK |  |  | ● |● |
| Oprette forbindelse til flere lokale LDAP-mapper |FRANSK  |  |  | ● |● |
| Oprette forbindelse til lokale AD og lokale LDAP-mapper |FRANSK  |  |  | ● |● |
| Oprette forbindelse til brugerdefineret systemer (det vil sige SQL, Oracle, MySQL osv.) | FRANSK |  |  | ● |● |
| Synkronisere brugerdefinerede attributter (directory extensions) | ● |  |  |  |  |
|Oprette forbindelse til lokale HR (det vil sige SAP, Oracle eBusiness, PeopleSoft)| FRANSK |  |  | ● |● |
|Understøtter FIM synkronisering regler og forbindelser til klargøring af til lokale systemer.|  |  |  | ● |● |

## <a name="cloud-to-on-premises-synchronization"></a>Skyen lokal synkronisering

| Funktion  | Oprette forbindelse Azure Active Directory  | Azure Active Directory-Synkroniseringstjenester | Azure Active Directory-Synkroniseringsværktøj (DirSync) | Forefront identitet Manager 2010 R2 (FIM) |Microsoft identitet Manager 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Tilbageførsel enheder | ● |  | ● |  ||
| Attributten tilbageførsel (for Exchange-hybridinstallation) | ● | ● | ● | ● |● |
| Tilbageførsel af brugere og grupper objekter |  ●|  | |  ||
| Tilbageførsel af adgangskoder (fra nulstilling af adgangskode selvbetjening (SSPR) og ændring af adgangskode) |  ● | ● |  |  ||



## <a name="authentication-feature-support"></a>Understøttelse af funktioner godkendelse

| Funktion  | Oprette forbindelse Azure Active Directory | Azure Active Directory-Synkroniseringstjenester | Azure Active Directory-Synkroniseringsværktøj (DirSync) | Forefront identitet Manager 2010 R2 (FIM) |Microsoft identitet Manager 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Synkronisering af adgangskoder for enkelt lokalt AD skov | ● | ● | ● |  ||
| Synkronisering af adgangskoder for flere lokale AD områder |  ●| ● |  |  ||
| Single Sign-on med sammenslutning | ● | ● | ● | ● |● |
| Tilbageførsel af adgangskoder (fra SSPR og din adgangskode ændring) |●  | ● |  |  ||



## <a name="set-up-and-installation"></a>Konfiguration og Installation

| Funktion  | Oprette forbindelse Azure Active Directory  | Azure Active Directory-Synkroniseringstjenester | Azure Active Directory-Synkroniseringsværktøj (DirSync) | Microsoft identitet Manager 2016 (MIM) |
| :-------- |:--------:|:--------:|:--------:|:--------:
| Understøtter installation på et domænenavn fra domænecontrolleren | ● | ● | ● |  |
| Understøtter installation ved hjælp af SQL Express | ● | ● | ● |  |
| Nem opgradering fra DirSync |● |  |  |  |
| Lokalisering af Admin UX til sprog, Windows Server | ● | ● | ● |  |
|Lokalisering for slutbruger UX til sprog, Windows Server| |  |  |● |
| Understøttelse af Windows Server 2008 og Windows Server 2008 R2 | ● Synkroniseres, til ikke til organisationsnetværk| ● | ●  | ● |
| Understøttelse af Windows Server 2012 og Windows Server 2012 R2 | ● | ● | ● | ● |

## <a name="filtering-and-configuration"></a>Filtrere og konfiguration

Funktion  | Oprette forbindelse Azure Active Directory | Azure Active Directory-Synkroniseringstjenester | Azure Active Directory-Synkroniseringsværktøj (DirSync) | Forefront identitet Manager 2010 R2 (FIM)| Microsoft identitet Manager 2016 (MIM)
:-------- |:--------:|:--------:|:--------:|:--------:|:--------:|
Filtrere efter domæner og organisatoriske enheder | ● | ● | ● | ●  | ●
Filtrere på objekternes attributværdier | ● | ● | ● | ●| ●
Tillad minimalt sæt af egenskaber skal være synkroniseret (MinSync) | ● | ● |  ||
Tillad anden tjeneste skabeloner der skal anvendes for attributten flyder |●  | ● |  ||
Tillad, at fjerne attributter fra der flyder fra AD til Azure AD | ● | ● |  |  |
Tillad Avanceret tilpasning for attributten flyder | ● | ● |  | ●  | ●

## <a name="next-steps"></a>Næste trin
Lær mere om at [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).
