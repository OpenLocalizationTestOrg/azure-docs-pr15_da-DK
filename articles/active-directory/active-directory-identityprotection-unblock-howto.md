<properties
    pageTitle="Azure Active Directory identitetsbeskyttelse - hvordan du kan fjerne blokeringen af brugere | Microsoft Azure"
    description="Få mere at vide hvordan fjerne blokeringen af brugere, der er blevet blokeret af en politik for beskyttelse mod Azure Active Directory identitet."
    services="active-directory"
    keywords="Azure active directory identitetsbeskyttelse, Fjern blokering af bruger"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection---how-to-unblock-users"></a>Azure Active Directory identitetsbeskyttelse - hvordan du kan fjerne blokeringen af brugere

Du kan konfigurere politikker for at blokere brugere, hvis de konfigurerede betingelser er opfyldt med Azure Active Directory identitetsbeskyttelse. Typisk en blokerede bruger kontakter helpdesk at blive blokeret. Denne emner forklares de trin, du kan udføre for at fjerne blokeringen af en blokerede bruger.


## <a name="determine-the-reason-for-blocking"></a>Fastlægge årsagen til at blokere

Som et første trin til at fjerne blokeringen af en bruger, skal du se, hvilken type af politik, som brugeren har blokeret, fordi de næste trin er knyttet til den. Med Azure Active Directory identitetsbeskyttelse skal kan en bruger være enten blokeret af en politik for logon risiko eller en bruger risikoen politik. 

Du kan finde typen politik, der er blokeret en bruger fra overskriften i den dialogboks, der blev vises for brugeren under forsøg på en logon:

|Politik | Brugerdefineret dialogboks|
|--- | --- |
|Log på risikoen | ![Blokerede logon](./media/active-directory-identityprotection-unblock-howto/02.png) |
|Bruger risikoen | ![Blokerede konto](./media/active-directory-identityprotection-unblock-howto/104.png) |


En bruger, der er blokeret af:

- En politik for logon risikoen er også kendt som mistænkelige logon
- En bruger risikoen politik er også kendt som en konto på risiko

 
## <a name="unblocking-suspicious-sign-ins"></a>Fjerne blokering mistænkelige logon

Hvis du vil fjerne blokeringen af en mistænkelige logon, har du følgende indstillinger:

1. **Log på fra en velkendte placering eller enhed** - en almindelig årsag til blokerede mistænkelige logon er logon forsøg fra fremmede placeringer eller enheder. Dine brugere se hurtigt, om det er grunden til blokering af forsøger at logge på fra en velkendte placering eller enhed.


3. **Udelade fra politikken** - Hvis du tror, at den aktuelle konfiguration af politikken logon forårsager problemer med for bestemte brugere, kan du udelade brugerne fra den. Se [politik for logon risikoen](active-directory-identityprotection.md#sign-in-risk-policy) for at få flere oplysninger.
 
4. **Deaktiverer politik** - Hvis du tror, at din politik konfiguration forårsager problemer med for alle dine brugere, kan du deaktivere politikken. Se [politik for logon risikoen](active-directory-identityprotection.md#sign-in-risk-policy) for at få flere oplysninger.


## <a name="unblocking-accounts-at-risk"></a>Fjerne blokering konti på risiko

Hvis du vil fjerne blokeringen af en konto på risiko, har du følgende indstillinger:

1. **Nulstil adgangskode** - du kan nulstille brugerens adgangskode. Se [Manuel sikker adgangskode nulstille](active-directory-identityprotection.md#manual-secure-password-reset) få mere at vide.

2. **Afvise alle risikoen begivenheder** - politikken bruger risikoen blokerer en bruger, hvis risikoen konfigurerede brugerniveau forhindrer adgang for er nået. Du kan reducere en bruger er risikoen niveau ved at lukke manuelt rapporteret risikoen begivenheder. Få mere at vide ved at se [lukning risikoen begivenheder manuelt](active-directory-identityprotection.md#closing-risk-events-manually).

3. **Udelade fra politikken** - Hvis du tror, at den aktuelle konfiguration af politikken logon forårsager problemer med for bestemte brugere, kan du udelade brugerne fra den. Se [politik bruger risikoen](active-directory-identityprotection.md#user-risk-policy) for at få flere oplysninger.
 
4. **Deaktiverer politik** - Hvis du tror, at din politik konfiguration forårsager problemer med for alle dine brugere, kan du deaktivere politikken. Se [politik bruger risikoen](active-directory-identityprotection.md#user-risk-policy) for at få flere oplysninger.




## <a name="next-steps"></a>Næste trin

 Vil du vide mere om beskyttelse med Azure AD identitet? Se [Azure Active Directory identitetsbeskyttelse](active-directory-identityprotection.md).
 

