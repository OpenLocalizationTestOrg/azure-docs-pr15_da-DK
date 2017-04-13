<properties
    pageTitle="Azure Active Directory B2C: Multi-Factor Authentication | Microsoft Azure"
    description="Sådan aktiveres Multi-Factor Authentication i consumer mod programmer sikret af Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="msmbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-enable-multi-factor-authentication-in-your-consumer-facing-applications"></a>Azure Active Directory-B2C: Aktivér Multi-Factor Authentication i programmerne consumer ned

Azure Active Directory (Azure AD) B2C integrerer direkte med [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) , så du kan føje en anden sikkerhedslag til tilmelding og log på oplevelser i programmerne consumer ned. Og du kan gøre dette uden at skrive en enkelt linje af kode. Vi understøtter telefonopkald og tekst meddelelse bekræftelse. Hvis du allerede har oprettet tilmelding og log på politikker, kan du stadig aktivere Multi-Factor Authentication.

> [AZURE.NOTE]
Multi-Factor Authentication kan også være aktiveret, når du opretter tilmelding og log på politikker, ikke blot ved at redigere eksisterende politikker.

Denne funktion hjælper med at håndtere scenarier som følgende programmer:

- Du har brug ikke at få adgang til ét program Multi-Factor Authentication, men du har brug for at få adgang til en anden. For eksempel forbrugerne kan logge på et automatisk forsikring program med en sociale eller lokale konto, men skal bekræfte telefonnummeret, før at få adgang til privat forsikring programmet registreret i den samme mappe.
- Du har brug ikke at få adgang til et program generelt Multi-Factor Authentication, men du har brug for at få adgang til de følsomme dele inden for den. For eksempel forbrugerne kan logge på en bank program med en sociale eller lokale konto og Kontrollér saldo, men skal bekræfte telefonnummeret, før du forsøger en tråd overførsel.

## <a name="modify-your-sign-up-policy-to-enable-multi-factor-authentication"></a>Ændre din tilmelding til en politik for at aktivere Multi-Factor Authentication

1. [Følg disse trin for at gå til bladet B2C funktioner på Azure-portalen](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Klik på **tilmelding politikker**.
3. Klik på din tilmelding politik (eksempelvis "B2C_1_SiUp") for at åbne den.
4. Klik på **multi-Factor authentication** og slå **tilstand** til **til**. Klik på **OK**.
5. Klik på **Gem** øverst på bladet.

Du kan bruge funktionen "Kør nu" på politikken for at bekræfte brugeroplevelsen. Ved at kontrollere følgende:

En almindelig konto oprettes i mappen før trinnet Multi-Factor Authentication opstår. Under trinnet, er forbrugeren bedt om at angive vedkommendes telefonnummer og bekræfte den. Hvis bekræftelsen er gået igennem, er telefonnummeret knyttet til kontoen consumer til senere brug. Selvom forbrugeren annullerer eller udelader, han eller hun kan blive bedt om at bekræfte et telefonnummer igen under den næste logon (med Multi-Factor Authentication aktiveret).

## <a name="modify-your-sign-in-policy-to-enable-multi-factor-authentication"></a>Ændre politikken Log på for at aktivere Multi-Factor Authentication

1. [Følg disse trin for at gå til bladet B2C funktioner på Azure-portalen](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Klik på **Log på politikker**.
3. Klik på dit logon politik (eksempelvis "B2C_1_SiIn") for at åbne den. Klik på **Rediger** på øverst del af bladet.
4. Klik på **multi-Factor authentication** og slå **tilstand** til **til**. Klik på **OK**.
5. Klik på **Gem** øverst på bladet.

Du kan bruge funktionen "Kør nu" på politikken for at bekræfte brugeroplevelsen. Ved at kontrollere følgende:

Når forbrugeren tilmelder (ved hjælp af en social eller lokale konto), hvis et bekræftet telefonnummer er knyttet til kontoen consumer, han eller hun er bliver bedt om at bekræfte den. Hvis ingen telefonnummer er tilsluttet, forbrugerne bedt om at give en og bekræfte den. Telefonnummeret er knyttet til kontoen consumer til senere brug på vellykket bekræftelse.

## <a name="multi-factor-authentication-on-other-policies"></a>Multi-Factor Authentication på andre politikker

Som beskrevet for tilmelding og logon politikker ovenfor, det er også mulighed for at aktivere Multi-Factor authentication på tilmelding eller logon politikker og din adgangskode nulstille politikker. Det vil være tilgængelige snart på profil redigere politikker.
