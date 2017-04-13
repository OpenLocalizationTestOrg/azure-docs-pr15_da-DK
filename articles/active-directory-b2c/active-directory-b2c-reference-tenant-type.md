<properties
    pageTitle="Azure Active Directory-B2C: Fremstilling skalering kontra preview B2C lejere | Microsoft Azure"
    description="Et emne på typerne af Azure Active Directory B2C lejere"
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
    ms.date="08/30/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-production-scale-vs-preview-b2c-tenants"></a>Azure Active Directory-B2C: Fremstilling skalering kontra preview B2C lejere

Hvis du planlægger at skrive en fremstilling app på Azure Active Directory (Azure AD) B2C, skal du være sikker på, at du har den rigtige lejer "type" for at gå direkte på. Se hvad har du, følge disse trin til at [navigere til bladet B2C funktioner](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) på Azure-portalen og kig under **lejer type**.

## <a name="summary"></a>Oversigt

Azure AD-B2C understøtter fremstilling apps til **fremstilling skalering** B2C lejere i Nordamerika.

| Lejer type | De lande/områder | Generelt-tilgængelig? |
| ----------- | -------------- | --------------------- |
| **Fremstilling skalering lejer** | Nordamerika de lande/områder | Ja |
| **Fremstilling skalering lejer** | Alle de lande/områder undtagen Nordamerika | Nej |
| **Preview lejer** | Alle de lande/områder | Nej |

> [AZURE.NOTE]
Azure AD B2C lejere (til forbrugere) er ikke tilgængelig i øjeblikket i nogle lande eller områder, hvor Azure AD-lejere (for medarbejdere) er tilgængelig. Læs i de følgende afsnit for at få flere oplysninger.

## <a name="production-scale-b2c-tenant-in-north-america"></a>Fremstilling skalering B2C leje i Nordamerika

Hvis du har [oprettet din B2C lejer](active-directory-b2c-get-started.md) i Nordamerika, det vil sige på en af følgende lande eller områder: United stater, Canada, Costa Rica, Dominikanske Republik, El Salvador, Guatemala, Mexico, Panama, Puerto Rico og Trinidad og Tobago, og **lejer type** på din B2C administrator brugergrænseflade står **fremstilling skalering**, din lejer kan bruges til fremstilling apps.

> [AZURE.NOTE]
Fremstilling skalering lejere er i stand til skalering til 100s millioner af forbrugere identiteter per lejer.

![Skærmbillede af en fremstilling skalering lejer](./media/active-directory-b2c-reference-tenant-type/production-scale-b2c-tenant.png)

## <a name="preview-b2c-tenant-in-any-countryregion"></a>Få vist B2C leje i en hvilken som helst land/område

Hvis du har oprettet en lejer B2C i Azure AD B2C preview periode, er det sandsynligvis, at din **lejer Skriv** står **Gennemse lejer**. Hvis det er tilfældet, skal du bruge din lejer, kun til udvikling og testformål og ikke for fremstilling apps.

> [AZURE.IMPORTANT]
Der er ingen migreringsstien fra et eksempel B2C lejer til en fremstilling skalering B2C lejer. Bemærk, at der er kendte problemer, når du sletter en preview B2C lejer og genskabe en fremstilling skalering B2C lejer med navnet på det samme domæne. Du skal oprette en fremstilling skalering B2C lejer med et andet domænenavn.

![Skærmbillede af en preview-lejer](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)

## <a name="production-scale-b2c-tenant-outside-of-north-america"></a>Fremstilling skalering B2C lejer uden for Nordamerika

Azure AD-B2C er ikke i øjeblikket alment-tilgængelig uden for Nordamerika. Men du kan oprette og bruge fremstilling skalering lejere til udvikling og test formål i en af følgende lande eller områder: Algeriet, Østrig, Aserbajdsjan, Bahrain, Belarus, Belgien, Bulgarien, Kroatien, Cypern, Tjekkiet, Danmark, Egypten, Estland, Finland, Frankrig, Tyskland, Grækenland, Ungarn, Island, Irland, Israel, Italien, Jordan, Kasakhstan, Kenya, Kuwait, Lativa, Libanon, Liechtenstein, Lituania, Luxembourg, Republik Makedonien, Malta, Montenegro, Marokko, Nederlandene, Nigeria, Norge , Oman, Pakistan, Polen, Portugal, Qatar, Rumænien, Rusland, Saudi-Arabien, Serbien, Slovakiet, Slovenien, Sydafrika, Spanien, Sverige, Schweiz, Tunesien, Tyrkiet, Ukraine, Forenede Arabiske Emirater og Storbritannien.

Når Azure AD B2C læser generelt tilgængelig i over lande eller områder, kan du fortsætte med at bruge disse fremstilling skalering lejere, og gå direkte til din fremstilling apps uden at miste data.

## <a name="availability-of-b2c-tenants"></a>Tilgængeligheden af B2C lejere

B2C lejere er ikke tilgængelig i øjeblikket i de følgende lande eller områder: Afghanistan, Argentina, Australien, Brasilien, Chile, Colombia, Ecuador, Hong Kong SAR, Indien, Indonesien, Irak, Japan, Korea, Malaysia, New Zealand, Paraguay, Peru, Filippinerne, Singapore, Sri Lanka, Taiwan, Thailand, Uruguay og Venezuela. Vi planlægger at inkludere dem i fremtiden.
