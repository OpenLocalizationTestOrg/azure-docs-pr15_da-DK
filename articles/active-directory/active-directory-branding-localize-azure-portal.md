<properties
pageTitle="Tilføje sprogspecifikke firmalogo på siden Log på i Azure Active Directory preview | Microsoft Azure"
description="Lær at føje et bestemt sprog regnskab branding billeder og tekst til en Azure-logon side"
services="active-directory"
documentationCenter=""
authors="curtand"
manager="femila"
editor=""/>

<tags
ms.service="active-directory"
ms.workload="identity"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="09/12/2016"
ms.author="curtand"/>

# <a name="add-language-specific-company-branding-to-your-sign-in-page-in-the-azure-active-directory-preview"></a>Tilføje sprogspecifikke firmalogo på siden Log på i Azure Active Directory preview

For at undgå forvirring, vil mange virksomheder anvende et ensartet udseende på tværs af alle de websteder og tjenester, de styrer. Azure Active Directory-preview indeholder denne egenskab ved at tilpasse udseendet af siden Log på med dit firmalogo og brugerdefinerede farveskemaer. [Hvad er i preview?](active-directory-preview-explainer.md) Siden logon er den side, der vises, når du logger på Office 365 eller andre webbaserede programmer, der bruger Azure AD din identitetsudbyder. Du interagere med denne side for at angive dine legitimationsoplysninger.

## <a name="customizing-the-sign-in-page-for-another-language"></a>Tilpasse siden-logon til et andet sprog

Du kan føje sprogspecifikke elementer til din brugerdefinerede logonside kun, hvis du allerede har oprettet en brugerdefineret logonside som beskrevet i [tilføje firmalogo til siden Log på](active-directory-branding-custom-signon-azure-portal.md). Du kan konfigurere en logonside hver mappe med et standardsæt af elementer, der kan tilpasses. Når du har konfigureret standardsættet af sideelementer, kan du konfigurere flere versioner til forskellige landestandarder. Du kan også blande og svarer til forskellige elementer. Du kan f.eks.:

- Oprette en standard **-logon sidebillede** , der fungerer for alle kulturer, og derefter oprette bestemte versioner til engelsk og fransk. Når du indstiller dit browsere til en af disse to sprog, vises sprogspecifikke billedet, mens standard illustrationen vises for alle andre sprog.

- Konfigurere forskellige logoer for din organisation (for eksempel japansk eller hebraisk versioner).

Vi anbefaler, at du beholder antallet af sprog variationer lav vedligeholdelse og ydeevne årsager.

**Sådan tilføjer du firmalogo til adresselisten:**

1.  Log på [Azure-portalen](https://portal.azure.com) med en konto, der er en global administrator for kataloget.

2.  Vælge **flere tjenester**, Angiv **brugere og grupper** i tekstfeltet, og tryk derefter på **Enter**.

    ![Åbne brugeradministration](./media/active-directory-branding-localize-azure-portal/user-management.png)

3. Vælg **virksomhed branding**bladet **brugere og grupper** .

4. På den **brugere og grupper - firmaoplysninger branding** blade, markere kommandoen **Tilføj sprog** .

    ![Tilføje sprogspecifikke mærkning elementer](./media/active-directory-branding-localize-azure-portal/add-language.png)

5. Ændre de elementer, du vil tilpasse. Alle elementer er valgfrit.

6. Klik på **Gem**.

Det kan tage op til en time for eventuelle ændringer, du har foretaget siden Log på branding at blive vist.

## <a name="next-steps"></a>Næste trin

[Tilføje firmalogo til siden-logon](active-directory-branding-custom-signon-azure-portal.md)
