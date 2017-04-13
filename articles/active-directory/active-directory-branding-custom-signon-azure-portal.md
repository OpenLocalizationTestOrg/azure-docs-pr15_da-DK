<properties
pageTitle="Tilpasse dine logonside i Azure Active Directory preview | Microsoft Azure"
description="Lær at tilføje et firmalogo til Azure-logon-siden"
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
ms.date="09/30/2016"
ms.author="curtand"/>

# <a name="add-company-branding-to-your-sign-in-page-in-the-azure-active-directory-preview"></a>Tilføje firmalogo på siden Log på i Azure Active Directory preview

For at undgå forvirring, vil mange virksomheder anvende et ensartet udseende på tværs af alle de websteder og tjenester, de styrer. Azure Active Directory-preview indeholder denne egenskab ved at tilpasse udseendet af siden Log på med dit firmalogo og brugerdefinerede farveskemaer. [Hvad er i preview?](active-directory-preview-explainer.md) Siden logon er den side, der vises, når du logger på Office 365 eller andre webbaserede programmer, der bruger Azure AD din identitetsudbyder. Du interagere med denne side for at angive dine legitimationsoplysninger.

Hvis du vil vise din virksomheds-mærke, farver og andre kan tilpasses elementer på denne side, skal du se følgende billederne for at forstå forskellen mellem de to oplevelser.

Følgende skærmbillede viser og eksempel til siden Office 365-logon på en pc **før** en tilpasning:

![Office 365-logon side før tilpasning](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-before-customization.png)

Følgende skærmbillede viser og eksempel til siden Office 365-logon på en pc **efter** en tilpasning:

![Office 365-logon side efter tilpasning](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-after-customization.png)


## <a name="customizing-the-sign-in-page"></a>Tilpasse siden-logon

Typisk, hvis du skal browserbaserede adgang til dine skyen apps og tjenester, som organisationen abonnerer på, skal du bruge siden logon.

Hvis du har anvendt ændringer på siden Log på, kan det tage op til en time for at ændringerne kan vises.

En tilpasset logon side vises kun, når du besøger en tjeneste med en URL-adresse til en lejer-specifikke som https://outlook.com/**contoso**.com eller https://mail. **Contoso**. com.

Når du besøger en tjeneste med ikke-lejer bestemte URL-adresser (f.eks.: https://mail.office365.com), en ikke-mærkede logon side vises. i dette tilfælde vises din branding, når du har angivet dit bruger-ID, eller du har valgt et brugerfelt.

> [AZURE.NOTE]
>
- Domænenavnet skal vises som "Aktiv" i **domæner** del af portalen Azure, hvor du har konfigureret mærkning. Du kan finde yderligere oplysninger finder [Tilføj brugerdefinerede domænenavne](active-directory-domains-add-azure-portal.md).
- På logonsiden branding overføres ikke til forbrugere logonsiden af Microsoft. Hvis du logger på med en Microsoft-konto, du kan se en tilpasset liste over bruger felter gengives ved Azure AD, men mærkning af din organisation gælder ikke for Microsoft-konto Log på siden.

På siden Log på giver afkrydsningsfeltet **Forbliv logget på** en bruger skal være logget på, når de lukke og genåbne deres browser. 

   ![Forbliv logget på](./media/active-directory-branding-custom-signon-azure-portal/01.png)

Det har ikke betydning for session levetid. Du kan skjule afkrydsningsfeltet på siden Azure Active Directory-logon.
Om der skal vises i afkrydsningsfeltet, afhænger af indstillingen af **Forbliv logget på deaktiveret**.

   ![Forbliv logget på](./media/active-directory-branding-custom-signon-azure-portal/02.png)


Hvis du vil skjule afkrydsningsfeltet, skal du konfigurere denne indstilling til **Ja**. 

> [AZURE.NOTE] Nogle funktioner i SharePoint Online og Office 2010, afhænger af brugere mulighed for at markere dette afkrydsningsfelt. Hvis du konfigurerer denne indstilling til skjult, kan brugerne se yderligere og uventede instruktionerne til at logge på.




**Sådan tilføjer du firmalogo til adresselisten:**

1.  Log på [Azure-portalen](https://portal.azure.com) med en konto, der er en global administrator for kataloget.

2.  Vælge **flere tjenester**, Angiv **brugere og grupper** i tekstfeltet, og tryk derefter på **Enter**.

    ![Åbne brugeradministration](./media/active-directory-branding-custom-signon-azure-portal/user-management.png)

3. Vælg **virksomhed branding**bladet **brugere og grupper** .

4. På den **brugere og grupper - firmaoplysninger branding** blade, vælg kommandoen **Rediger** .

    ![Redigere brugerdefinerede branding](./media/active-directory-branding-custom-signon-azure-portal/edit-branding.png)

5. Ændre de elementer, du vil tilpasse. Alle elementer er valgfrit.

6. Klik på **Gem**.

Det kan tage op til en time for eventuelle ændringer, du har foretaget siden Log på branding at blive vist.

## <a name="next-steps"></a>Næste trin

[Tilføje sprogspecifikke firmalogo](active-directory-branding-localize-azure-portal.md)
