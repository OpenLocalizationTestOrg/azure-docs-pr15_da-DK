<properties
    pageTitle="Azure Active Directory-B2C: Token, session og enkelt sign-on – konfiguration | Microsoft Azure"
    description="Token, session og enkelt enkeltlogon-konfiguration i Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-token-session-and-single-sign-on-configuration"></a>Azure Active Directory-B2C: Token, session og enkelt sign-on – konfiguration

Denne funktion giver dig detaljerede kontrolelement på en [per politik datotype](active-directory-b2c-reference-policies.md)af:
 
1. Levetid for sikkerhedstokens udsendes af Azure Active Directory (Azure AD) B2C.
2. Levetid for web application sessioner administreres af Azure AD B2C.
3. Enkeltlogon (SSO) funktionsmåde på tværs af flere apps og politikker i din B2C lejer.

Du kan bruge denne funktion i din B2C lejer på følgende måde:

1. Følg disse trin til at [navigere til bladet B2C funktioner](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) på Azure-portalen.
2. Klik på **Log på politikker**. *Note: Du kan bruge denne funktion på enhver politiktype, ikke blot på* *Log på politikker***.
3. Åbn en politik ved at klikke på den. For eksempel, klik på **B2C_1_SiIn**.
4. Klik på **Rediger** på øverst del af bladet.
5. Klik på **Token, session og enkelt sign-on – config**.
6. Foretag de ønskede ændringer. Få mere at vide om tilgængelige egenskaber i de efterfølgende afsnit.
7. Klik på **OK**.
8. Klik på **Gem** øverst på bladet.

![Skærmbillede af token, session og enkelt sign-on – config](./media/active-directory-b2c-token-session-sso/token-session-sso.png)

## <a name="token-lifetimes-configuration"></a>Konfiguration af sikkerhedstoken levetid

Azure AD-B2C understøtter [OAuth 2.0 authorization protocol](active-directory-b2c-reference-protocols.md) for at aktivere sikker adgang til beskyttede ressourcer. For at implementere denne understøttelse, udsender Azure AD B2C forskellige [sikkerheds-id](active-directory-b2c-reference-tokens.md). Dette er de egenskaber, du kan bruge til at administrere levetid for sikkerhedstokens fra Azure AD B2C:

- **Access & ID token levetid (minutter)**: levetiden for 2.0 OAuth-bærertoken bruges til at få adgang til en beskyttet ressource. Azure AD-B2C udsteder kun ID tokens på nuværende tidspunkt. Denne værdi skal gælde for access tokens samt, når vi tilføje understøttelse af dem.
   - Standard = 60 minutter.
   - Minimum (inklusive) = 5 minutter.
   - Maksimum (inklusive) = 1440 minutter.
- **Opdater token levetid (dage)**: den maksimale tidsperiode, som en opdatering token kan bruges til at få fat på en ny access eller id-token (og eventuelt et nyt Opdater-id, hvis dit program har fået de `offline_access` omfang).
   - Standard = 14 dage.
   - Minimum (inklusive) = 1 dag.
   - Maksimum (inklusive) = 90 dage.
- **Opdater token skubbe vinduet levetid (dage)**: Når denne periode går brugeren er nødt til at godkende igen, uanset gyldighedsperioden for den seneste opdatering token hentet af programmet. Det kan kun leveres, hvis Skift er indstillet til **Bounded**. Det skal være større end eller lig med den **Opdater token levetid (dage)** værdi. Hvis Skift er indstillet til **Unbounded**, kan du angive en bestemt værdi.
   - Standard = 90 dage.
   - Minimum (inklusive) = 1 dag.
   - Maksimum (inklusive) = 365 dage.

Dette er et par use cases, som du kan aktivere ved hjælp af disse egenskaber:

- Give en bruger til at holde logget på en mobil-program på ubestemt tid, som han eller hun er kontinuerligt aktiv på programmet. Du kan gøre dette ved at angive den **Opdater token skyderen vinduet levetid (dage)** skifte til **Unbounded** i din logon-politik.
- Opfylde din branche sikkerhed og overholdelse af krav ved at angive den relevante access token levetid.

## <a name="session-configuration"></a>Konfiguration af session

Azure AD-B2C understøtter [OpenID forbinde godkendelse protocol](active-directory-b2c-reference-oidc.md) for at aktivere sikker logon på webprogrammer. Dette er de egenskaber, du kan bruge til at administrere web application sessioner:

- **Online session levetid (minutter)**: levetiden for Azure AD B2C sessionscookie gemt på brugerens browser efter vellykket bekræftelse.
   - Standard = 1440 minutter.
   - Minimum (inklusive) = 15 minutter.
   - Maksimum (inklusive) = 1440 minutter.
- **Web app-Sessionstimeout**: Hvis denne parameter er indstillet til **absolutte**, brugeren er nødt til at godkende igen efter den periode, der er angivet af standardtimeouttiden **Online session levetid (minutter)** er udløbet. Hvis denne parameter er indstillet til **rullende** (standardindstilling), forbliver brugeren logget, som brugeren er kontinuerligt aktiv i dit webprogram.

Dette er et par use cases, som du kan aktivere ved hjælp af disse egenskaber:

- Opfylder din branche sikkerhed og overholdelse af krav ved at angive den relevante web application session levetid.
- Gennemtving endnu en godkendelse efter et angivet tidsrum under en brugers interaktion med en høj sikkerhed del af dit webprogram. 

## <a name="single-sign-on-sso-configuration"></a>Konfiguration af enkeltlogon (SSO)

Hvis du har flere programmer og politikker i din B2C lejer, kan du administrere bruger interaktioner på tværs af dem ved hjælp af egenskaben **enkelt sign-on – konfiguration** . Du kan angive egenskaben til en af følgende indstillinger:

- **Lejer**: Dette er standardindstillingen for. Med denne indstilling kan flere programmer og politikker i din B2C lejer til at dele den samme brugersession. For eksempel når en bruger logger på et program, kan Contoso handler, han eller hun også problemfrit logge på en anden én, Contoso-apotek, ved at få adgang til den.
- **Program**: Dette gør det muligt at opretholde en brugersession udelukkende til et program, uafhængigt af andre programmer. Eksempelvis hvis du vil bruger til at logge på Contoso apotek (med de samme legitimationsoplysninger), også selvom han eller hun allerede er logget på Contoso indkøb, et andet program på samme B2C lejer. 
- **Politik**: Dette gør det muligt at opretholde en brugersession udelukkende til en politik, uafhængigt af de programmer, ved hjælp af den. Eksempelvis hvis brugeren har allerede er logget på og færdig et flere faktor-godkendelse (MFA) trin, kan han eller hun have adgang til høj sikkerhed dele af flere programmer så længe den session, der er bundet til politikken ikke udløber.
- **Deaktiveret**: Dette tvinger brugeren til at køre gennem hele bruger rejse på hver udførelse af politikken. For eksempel: Dette vil tillade flere brugere at tilmelde dig til dit program (i et delt skrivebord scenarie), lige under en enkelt bruger der forbliver logget under hele tiden.
