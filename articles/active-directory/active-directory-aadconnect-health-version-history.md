<properties
    pageTitle="Azure AD Connect sundhed versionshistorik"
    description="I dette dokument beskrives versioner Azure AD forbinde sundheds-og hvad er inkluderet i disse udgaver."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect tilstand: Versionshistorik for udgivelse

Azure Active Directory-gruppe opdaterer regelmæssig Azure AD forbinde tilstand med nye funktioner og funktionalitet. I denne artikel viser de versioner og funktioner, der er blevet frigivet.

## <a name="october-2016"></a>Oktober 2016
**Agent for opdatering:**
- Azure AD forbinde Health agent for AD FS \(version 2.6.408.0\)
    1. Forbedringer til at opdage Klienternes IP-adresser i anmodninger om godkendelse
    2. Fejlrettelser, der er relateret til beskeder
- Azure AD forbinde Health agent til Active Directory-Domænetjenester (version 2.6.408.0)
    1. Fejlrettelser relateret til beskeder.
- Azure AD forbinde Health agent for synkronisering (version 2.6.353.0) udgivet med Azure AD-forbindelse version 1.1.281.0
    1. Angiv de nødvendige data for fejlrapporter synkronisering
    2. Fejlrettelser, der er relateret til beskeder

**Nye funktioner for eksempel:**
- Synkronisering fejlrapporter til Azure AD-forbindelse

**Nye funktioner:**
- Azure AD forbinde tilstand for AD FS - feltet IP-adresse er tilgængelig i rapporten om øverste 50 brugere med forkert brugernavn og adgangskode.

## <a name="july-2016"></a>Juli 2016

**Nye funktioner for eksempel:**

- [Azure AD Connect tilstand til AD DS](active-directory-aadconnect-health-adds.md).


## <a name="january-2016"></a>Januar 2016


**Agent for opdatering:**

- Azure AD forbinde Health agent for AD FS (version 2.6.91.1512)


**Nye funktioner:**

- [Test Connectivity værktøj til Azure AD forbinde sundhed supportmedarbejdere](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)


## <a name="november-2015"></a>November 2015


**Nye funktioner:**

- Understøttelse af [Rollebaseret adgangskontrol](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)


**Nye funktioner for eksempel:**

- [Azure AD forbinde tilstand for synkronisering](active-directory-aadconnect-health-sync.md).

**Fast problemer:**

- Fejlrettelser for fejl, der vises under agent registreringer.

## <a name="september-2015"></a>September 2015

**Nye funktioner:**

- Forkert brugernavn adgangskode rapport for AD FS
- Support for at konfigurere ikke-godkendte HTTP-Proxy
- Support til at konfigurere agent på Server core
- Forbedringer til beskeder for AD FS
- Forbedringer i Azure AD forbinde Health Agent for AD FS for forbindelsen og data Overfør.


**Fast problemer:**

- Fejlrettelser i brugen indsigt for AD FS fejl typer.


## <a name="june-2015"></a>Juni 2015

**Første version af Azure AD forbinde tilstand for AD FS og AD FS-Proxy.**

**Nye funktioner:**

- Beskeder for overvågning AD FS og AD FS-Proxy-servere med besked via mail.
- Nem adgang til AD FS topologi og mønstre i AD FS tællere i ydeevne.
- Tendens i vellykket token anmodninger på AD FS-servere, der er grupperet efter programmer, godkendelsesmetoder, anmode om netværk placering osv.
- Tendenser i mislykkede anmodninger på AD FS-servere, der er grupperet efter programmer, fejl typer osv.
- Nemmere Agent-installation med Azure AD Global administrator brugeroplysninger.  




## <a name="next-steps"></a>Næste trin
Lær mere om [overvåge dine lokale identitet infrastruktur og synkronisering af tjenester i skyen](active-directory-aadconnect-health.md).
