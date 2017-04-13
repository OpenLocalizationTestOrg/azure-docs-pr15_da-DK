<properties
    pageTitle="Azure AD-forbindelse synkronisering: Sådan administreres servicekonto Azure AD | Microsoft Azure"
    description="Dette emne dokumenter, hvordan du kan gendanne Azure AD-tjenestekontoen."
    services="active-directory"
    keywords="AADSTS70002, AADSTS50054, hvordan du kan nulstille adgangskoden for Azure AD-forbindelse Synkroniser forbindelse tjenestekonto"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-sync-how-to-manage-the-azure-ad-service-account"></a>Azure AD-forbindelse synkronisering: Sådan administreres Azure AD-tjenestekonto
Kontoen for tjenesten bruges af Azure AD forbindelsen skal være tjenesten gratis. Hvis du har brug at nulstille legitimationsoplysningerne, er dette emne for dig. Hvis en Global Administrator har ved et uheld nulstiller adgangskoden for kontoen for tjenesten ved hjælp af PowerShell.

## <a name="reset-the-credentials"></a>Nulstille legitimationsoplysningerne
Hvis den tjenestekonto, der er defineret i den Azure AD-forbindelse ikke kan kontaktes Azure AD på grund af problemer med godkendelse, kan være nulstille adgangskoden.

1. Log på Azure AD-forbindelse Synkroniser serveren, og start PowerShell.
2. Køre `Add-ADSyncAADServiceAccount`.  
![PowerShell-cmdlet addadsyncaadserviceaccount](./media/active-directory-aadconnectsync-howto-azureadaccount/addadsyncaadserviceaccount.png)
3. Angive Azure AD Global administrator legitimationsoplysninger.

Denne cmdlet nulstiller adgangskoden for kontoen for tjenesten og Opdater den både i Azure AD og i programmet til synkronisering.

## <a name="known-issues-these-steps-can-solve"></a>Kendte problemer, der kan løse disse trin
Dette afsnit er en liste over fejl rapporteret af kunder, der er blevet løst ved en nulstille på servicekonto Azure AD-legitimationsoplysninger.

-----------
Begivenhed 6900  
Der opstod en uventet fejl under behandling af en meddelelse om ændring af adgangskoden på serveren:  
AADSTS70002: Fejl validering af legitimationsoplysninger. AADSTS50054: Bruges gamle adgangskode til godkendelse.

----------
Begivenhed 659  
Fejl under hentning af adgangskode politik Synkroniser konfiguration. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: Fejl validering af legitimationsoplysninger. AADSTS50054: Bruges gamle adgangskode til godkendelse.

## <a name="next-steps"></a>Næste trin

**Oversigt oversigtsemner**

- [Azure AD-forbindelse synkronisering: forstå og tilpasse synkronisering](active-directory-aadconnectsync-whatis.md)
- [Integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md)
