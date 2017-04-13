<properties
    pageTitle="Azure Active Directory-B2C: Selvbetjening nulstille | Microsoft Azure"
    description="Et emne, der vises, hvordan du konfigurerer selvbetjening for nulstilling af adgangskode til dine brugere i Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="curtand"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>


# <a name="azure-active-directory-b2c-set-up-self-service-password-reset-for-your-consumers"></a>Azure Active Directory-B2C: Konfigurere selvbetjening for nulstilling af adgangskode til dine brugere

Med funktionen selvbetjening Nulstil kan din forbrugere (som har tilmeldt sig til lokale konti) nulstille sin adgangskode på egen hånd. Dette reducerer markant belastning på din supportpersonale, især hvis dit program har millioner af forbrugere, der bruger den med jævne mellemrum. Vi understøtter i øjeblikket kun ved hjælp af en bekræftet mailadresse som en metode til gendannelse. Vi tilføjer yderligere betroet metoder (bekræftet telefonnummer, spørgsmål om sikkerhed, osv.) i fremtiden.

> [AZURE.NOTE]
I denne artikel gælder for selvbetjening Nulstil bruges i forbindelse med en politik for logon. Hvis du har brug for fuldt ud justerbart adgangskode nulstillet politikker, der startes fra din app, skal du se [denne artikel](./active-directory-b2c-reference-policies.md#create-a-password-reset-policy).

Som standard mappen vil ikke have selvbetjening Nulstil slået til. Brug følgende trin til at aktivere den:

1. Log på [Azure klassisk portal](https://manage.windowsazure.com/) som abonnement Administrator. Dette er den samme arbejds- eller skolekonto eller den samme Microsoft-konto, som du brugte til at oprette adresselisten.
2. Gå til i Active Directory-udvidelse på navigationslinjen i venstre side.
3. Find mappen under fanen **mappe** og klikke på den.
4. Klik på **Konfigurer** .
5. Rul ned til sektionen **politik nulstilling af adgangskoden bruger** og Skift indstillingen **brugere aktiveret til adgangskode nulstille** til **Ja**. Bemærk, at indstillingen **Alternative mailadresse** er markeret; Lad den, som det er.

    ![Nulstilling af adgangskode selvbetjening](./media/active-directory-b2c-reference-sspr/sspr.png)

6. Klik på **Gem** nederst på siden. Det var!

For at teste, skal du bruge funktionen "Kør nu" på en hvilken som helst logon-politik, der indeholder lokale konti som identitetsudbyder. På den lokale konto-logon side (hvor du angiver en mailadresse og adgangskode, eller et brugernavn og din adgangskode), klik på **kan ikke få adgang til din konto?** at bekræfte brugeroplevelsen.

> [AZURE.NOTE]
Selvbetjening Nulstil siderne kan tilpasses ved hjælp af [virksomhed mærkning funktion](../active-directory/active-directory-add-company-branding.md).
