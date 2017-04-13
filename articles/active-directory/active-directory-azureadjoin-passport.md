<properties
    pageTitle="Godkendelse af identiteter uden adgangskoder via Microsoft Passport | Microsoft Azure"
    description="Indeholder en oversigt over Microsoft Passport og yderligere oplysninger om installation af Microsoft Passport."
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

# <a name="authenticating-identities-without-passwords-through-microsoft-passport"></a>Godkendelse af identiteter uden adgangskoder via Microsoft Passport

Den aktuelle metoder godkendelse med adgangskoder alene er ikke tilstrækkelige til at beskytte brugere. Brugere genbruge og glemmer adgangskoder. Adgangskoder skelner breachable, phishable, giver risiko for revner og guessable. De får også svært at huske og giver risiko for angreb som "[overfører giver resultat](https://technet.microsoft.com/dn785092.aspx)".

## <a name="about-microsoft-passport"></a>Om Microsoft Passport
Microsoft Passport er en privat/offentlig nøgle eller certifikat-baseret godkendelse metode til organisationer og forbrugere, der går ud over adgangskoder. Denne form for godkendelse er afhængig af nøgle legitimationsoplysninger, der kan erstatte adgangskoder og er solidt brud, tyveri og phishing.

 Microsoft Passport kan en bruger godkendes til en Microsoft-konto, en Windows Server Active Directory-konto, et Microsoft Azure Active Directory (Azure AD)-konto eller en ikke-Microsoft-tjeneste, der understøtter hurtigt identitet Online (FIDO) godkendelse. Microsoft Passport er konfigureret på brugerens enhed efter en indledende totrinsbekræftelse under Microsoft Passport tilmelding, og brugeren angiver en bevægelse, der kan være Windows Hej eller en pinkode. Brugeren indeholder bevægelse for at bekræfte dennes identitet. Windows bruger derefter Microsoft Passport til at godkende brugeren og hjælpe dem med at få adgang til beskyttede ressourcer og -tjenester.

Privat nøgle er gjort tilgængelige udelukkende via en "bruger bevægelse" som en pinkode, biometri eller en fjernenhed som et chipkort, brugeren skal bruge til at logge på enheden. Disse oplysninger er sammenkædet med et certifikat eller en asymmetrisk vigtige par. Privat nøgle er hardware bekræftet Hvis enheden har en chip (Trusted Platform modul). Privat nøgle lader aldrig enheden.

Offentlig nøgle er registreret med Azure Active Directory og Windows Server Active Directory (for lokalt). Identitetsudbydere (IDPs) godkende brugeren ved at knytte den offentlige nøgle for brugeren, til den private nøgle, og giv logonoplysninger via én gang adgangskode (OTP), PhoneFactor eller en anden meddelelse ordning.

## <a name="why-enterprises-should-adopt-microsoft-passport"></a>Hvorfor virksomheder skal indføre Microsoft Passport

Ved at aktivere Microsoft Passport kan virksomheder sikre deres ressourcer endnu mere ved at:

* Konfiguration af Microsoft Passport med en indstilling for hardware-Foretrukne. Det betyder, at der skal oprettes taster på TPM 1.2 eller TPM 2.0, når de er tilgængelige. Når TPM ikke er tilgængelig, oprette software nøglen.

* Definere kompleksitet og længden af PINKODEN, og om Hej brugen er aktiveret i din organisation.

* Konfiguration af Microsoft Passport til at understøtte chipkort-lignende scenarier ved hjælp af certifikat-baseret sikkerhed og rettighedsadministration.

## <a name="how-microsoft-passport-works"></a>Hvordan fungerer Microsoft Passport
1. Nøgler oprettes på hardware af TPM eller software. Mange enheder har en indbygget TPM-chippen, der sikrer hardwaren ved at integrere cryptographic taster i enheder. TPM 1.2 eller TPM 2.0 genererer taster eller certifikater, der er oprettet ud fra de oprettede taster.

2. TPM bekræfter disse hardware-bundne taster.

3. En enkelt unlock bevægelse låser enheden. Denne bevægelse giver adgang til flere ressourcer Hvis enheden er medlem af et domæne eller Azure AD-joinforbundne.

## <a name="how-the-microsoft-passport-lifecycle-works"></a>Hvordan fungerer Microsoft Passport livscyklus

![Microsoft Passport livscyklus](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png)

Det foregående diagram illustrerer parret privat/offentlig nøgle og validering af identitetsprovideren. Hver af disse trin er beskrevet i detaljer her:

1. Brugeren beviser deres identitet gennem flere indbyggede korrekturredskaber metoder (bevægelser, fysisk chipkort, multi-Factor authentication) og sender disse oplysninger til en identitet udbyder (IDP) som Azure Active Directory eller lokale Active Directory.

2. Enheden derefter opretter nøglen, bekræfter tasten, tager den offentlige del af denne tast, vedhæfter med station sætninger, logger på og sender den til IDP til at registrere tasten.

4. Så snart IDP registrerer den offentlige del af nøglen, udfordringer IDP kameraet, for at logge på med den private del af nøglen.

5. IDP derefter validerer og problemer godkendelse tokenet, hvor brugeren og enheden adgang til de beskyttede ressourcer. IDPs kan skrive på tværs af platforme apps eller bruge browserunderstøttelse (via JavaScript/Webcrypto API'er) til at oprette og bruge Microsoft Passport legitimationsoplysninger for deres brugere.

## <a name="the-deployment-requirements-for-microsoft-passport"></a>Installation kravene til Microsoft Passport
### <a name="at-the-enterprise-level"></a>På niveauet for enterprise

* Virksomheden har et abonnement på Azure.

### <a name="at-the-user-level"></a>På brugerniveau

* Brugerens computer kører Windows 10 Professional eller Enterprise.

Du kan finde detaljerede installationsvejledning, [Aktivere Microsoft Passport til at arbejde i organisationen](active-directory-azureadjoin-passport-deployment.md).


## <a name="additional-information"></a>Yderligere oplysninger

* [Windows 10 for virksomheden: måder at bruge enheder for arbejde](active-directory-azureadjoin-windows10-devices-overview.md)
* [Hvis du vil udvide skyen funktioner til Windows 10-enheder via Azure Active Directory deltage](active-directory-azureadjoin-user-upgrade.md)
* [Få mere at vide om brugsscenarier til Azure AD deltage](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Tilslutte enheder, der er medlem af et domæne til Azure AD til Windows 10 oplevelser](active-directory-azureadjoin-devices-group-policy.md)
* [Konfigurere Azure AD deltage](active-directory-azureadjoin-setup.md)
