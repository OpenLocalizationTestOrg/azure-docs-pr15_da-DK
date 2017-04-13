<Properties
    pageTitle="Azure Active Directory betinget access | Microsoft Azure"  
    description="Bruge betinget adgangskontrol i Azure Active Directory til at søge efter bestemte betingelser, når godkendelse for at få adgang til programmer."  
    services="active-directory"
    keywords="betinget adgang til apps, betinget adgang med Azure AD, sikker adgang til ressourcer i virksomheden, betinget access politikker"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/21/2016"
    ms.author="markvi"/>


# <a name="conditional-access-in-azure-active-directory"></a>Betinget access i Azure Active Directory   

Control-funktioner i Azure Active Directory (Azure AD) betinget access tilbyder nemme måder til at sikre ressourcer i skyen og i det lokale miljø. Betinget access politikker såsom Multi-Factor authentication kan hjælpe med at beskytte mod risikoen for stjæles og phished legitimationsoplysninger. Andre betinget access politikker kan hjælpe dig med at beskytte din organisations data. For eksempel ud over at kræve legitimationsoplysninger, du muligvis en politik, der kun enheder, som er registreret i et system til administration af mobile enheder, som Microsoft Intune kan få adgang til din organisations følsomme tjenester.


## <a name="prerequisites"></a>Forudsætninger

Azure AD betinget access er en funktion af [Azure Active Directory Premium](http://www.microsoft.com/identity). De enkelte brugere, der åbner et program, der har betinget access politikker anvendt skal have en Azure AD Premium-licens. Du kan få mere at vide om licenskrav i [rapporten brug uden licens](https://aka.ms/utc5ix).


## <a name="how-is-conditional-access-control-enforced"></a>Sådan gennemtvinges betinget adgangskontrol?  

Med betinget adgangskontrol på plads, Azure AD undersøger, om specifikke betingelser du angive for en bruger til at få adgang til et program. Når access krav er opfyldt, brugeren er godkendt og har adgang til programmet.  

![Oversigt over betinget adgang](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## <a name="conditions"></a>Betingelser

Disse er betingelser, som du kan medtage i en betinget access-politik:

- **Gruppemedlemskab**. Kontrollere en brugers adgang baseret på medlemskab af en gruppe.

- **Placering**. Brug sted, hvor brugeren udløser Multi-Factor authentication, og bruge kontrolelementer af typen, når en bruger ikke, på et netværk, der er tillid til.

- **Enhed platform**. Du kan bruge enhed platformen, såsom iOS-, Android-, Windows Mobile, eller Windows, som en betingelse for anvendelse af politik.

- **Enhed er aktiveret**. Status for enheden, valideres om aktiveres eller deaktiveres, ved evaluering af enhed politikker. Hvis du deaktiverer en mistede eller stjålne enhed i kataloget, kan den ikke længere opfylder politik for krav.

- **Log på og bruger risiko**. Du kan bruge [Azure AD identitet beskyttelse](active-directory-identityprotection.md) betinget access risikoen politikker for. Betinget access risikoen politikker, kan hjælpe din organisation advance beskyttelse, der er baseret på risikoen begivenheder og usædvanlige logon-aktiviteter.


## <a name="controls"></a>Kontrolelementer

Dette er objekter, som du kan bruge til at gennemtvinge en betinget access-politik:

- **Multi-Factor authentication**. Du kan kræve stærke-godkendelse gennem Multi-Factor authentication. Du kan bruge Multi-Factor authentication med Azure Multi-Factor Authentication eller ved hjælp af en udbyder af lokale Multi-Factor godkendelse, kombineres med Active Directory Federation Services (AD FS). Ved hjælp af Multi-Factor godkendelse hjælper med at beskytte ressourcer fra der åbnes af uautoriserede brugere, som muligvis har fået adgang til legitimationsoplysninger for en gyldig bruger.

- **Bloker**. Du kan anvende betingelser som brugerplacering blokere brugeradgang. Du kan for eksempel Bloker adgang, når en bruger ikke, på et netværk, der er tillid til.

- **Kompatible enheder**. Du kan indstille betinget access politikker på niveauet for enhed. Du kan konfigurere en politik, så kun computere, der er medlem af et domæne, eller mobile enheder, som er registreret i et mobilenhed administrationsprogram kan få adgang til din organisations ressourcer. Du kan for eksempel bruge Intune til at kontrollere enhed overholdelse af regler og derefter rapportere det til Azure AD for aktivering, når brugeren forsøger at få adgang til et program. Se [Beskyt apps og data med Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/protect-apps-and-data-with-microsoft-intune)for at få detaljeret vejledning om, hvordan du bruger Intune til at beskytte apps og data. Du kan også bruge Intune til at gennemtvinge databeskyttelse til mistede eller stjålne enheder. Se [at beskytte dine data med fuld eller selektiv sletning ved hjælp af Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)kan finde flere oplysninger.

## <a name="applications"></a>Programmer

Du kan gennemtvinge en betinget access-politik på niveauet for programmet på computeren. Angive adgangsniveauer for programmer og tjenester i skyen eller i det lokale miljø. Politikken anvendes direkte til det websted eller den tjeneste. Politikken træder i kraft for at få adgang til browseren, og til programmer, få adgang til tjenesten.


## <a name="device-based-conditional-access"></a>Enhed-baserede betinget adgang

Du kan begrænse adgangen til programmer fra enheder, der er registreret med Azure AD, og som opfylder specifikke betingelser. Enhed-baserede betinget access beskytter en organisations ressourcer fra brugere, der forsøger at få adgang til ressourcer fra:

- Ukendt eller ikke-administreret enheder.
- Enheder, der ikke opfylder sikkerhedspolitikkerne organisationen konfigureret.

Du kan indstille politikker, der er baseret på disse krav:

- **Medlem af et domæne enheder**. Angive en politik om at begrænse adgangen til enheder, der er sammenføjet til et lokalt Active Directory-domæne, og som også er registreret med Azure AD. Denne politik gælder for Windows stationære computere, bærbare computere og enterprise-tablets.
Du kan finde flere oplysninger om, hvordan du konfigurerer automatisk registrering af medlem af et domæne enheder med Azure AD i [Opsætning af automatisk registrering af Windows medlem af et domæne enheder med Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).

- **Kompatible enheder**. Angive en politik om at begrænse adgangen til enheder, der er markeret **kompatibel** i systembiblioteket administration. Denne politik sikrer, at kun enheder, der opfylder sikkerhedspolitikker som sikres kryptering af filer på en enhed, der har adgang. Du kan bruge denne politik til at begrænse adgangen fra de følgende enheder:

    - **Windows medlem af et domæne enheder**. Administrerede ved System Center Configuration Manager (i den aktuelle gren) implementeret i en hybridkonfiguration.
    - **Windows 10 Mobile arbejds- eller personlige enheder**. Administreres af Intune eller ved et system til administration af mobilenheder i understøttes fra tredjepart.
    - **iOS og Android-enheder**. Administreres af Intune.


Brugere, der har adgang til programmer, der er beskyttet af en enhed-baseret, politikken skal adgang til programmet fra en enhed, der opfylder denne politik. Adgang nægtet Hvis forsøgt på en enhed, der ikke opfylder politik for krav.

Finde oplysninger om, hvordan du kan konfigurere en baseret på enheden, certificering Nøglecenterpolitik i Azure AD, i [Opsætning enhed-baserede betinget access-politik for Azure Active Directory-kompatible programmer](active-directory-conditional-access-policy-connected-applications.md).

## <a name="resources"></a>Ressourcer

Se følgende ressource kategorier og artikler for at lære mere om at angive betinget adgang til din organisation.


### <a name="multi-factor-authentication-and-location-policies"></a>Multi-Factor politikker for godkendelse og placering

- [Introduktion til betinget adgang til Azure AD-forbindelse apps, der er baseret på gruppen, placering og multi-Factor authentication politikker](active-directory-conditional-access-azuread-connected-apps.md)
- [Programmer, der understøttes](active-directory-conditional-access-supported-apps.md)


### <a name="device-based-conditional-access"></a>Enhed-baserede betinget adgang

- [Angive enhed-baserede betinget access-politik for at kontrollere adgangen til Azure Active Directory-kompatible programmer](active-directory-conditional-access-policy-connected-applications.md)
- [Konfigurere automatisk registrering af Windows medlem af et domæne enheder med Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md)
- [Fejlfinding af Azure Active Directory adgangsproblemer](active-directory-conditional-access-device-remediation.md)
- [Beskytte data på mistede eller stjålne enheder ved hjælp af Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)


### <a name="protect-resources-based-on-sign-in-risk"></a>Beskytte ressourcer, der er baseret på logon risikoen

-   [Azure AD identitetsbeskyttelse](active-directory-identityprotection.md)

### <a name="next-steps"></a>Næste trin

- [Betinget access ofte stillede spørgsmål](active-directory-conditional-faqs.md)
- [Teknisk vejledning](active-directory-conditional-access-technical-reference.md)
