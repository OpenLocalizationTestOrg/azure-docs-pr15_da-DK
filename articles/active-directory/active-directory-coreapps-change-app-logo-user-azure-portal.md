<properties
    pageTitle="Ændre navnet på eller logo på en enterprise-app i Azure Active Directory preview | Microsoft Azure"
    description="Hvordan du kan ændre navnet eller logoet til en brugerdefineret enterprise-app i Azure Active Directory"
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

# <a name="change-the-name-or-logo-of-an-enterprise-app-in-azure-active-directory-preview"></a>Ændre navnet på eller logo på en enterprise-app i Azure Active Directory preview

Det er nemt at ændre navnet på eller logo til et brugerdefineret enterprise-program i Azure Active Directory (Azure AD) preview. [Hvad er i preview?](active-directory-preview-explainer.md) Du skal have de relevante tilladelser til at foretage disse ændringer. Du skal være forfatteren af brugerdefineret app i den aktuelle preview.

## <a name="how-do-i-change-an-enterprise-apps-name-or-logo"></a>Hvordan ændrer jeg en enterprise-app navn eller et logo?

1. Log på [Azure-portalen](https://portal.azure.com) med en konto, der er en global administrator for kataloget.

2. Vælge **flere tjenester**, Angiv **Azure Active Directory** i tekstfeltet, og tryk derefter på **Enter**.

3. På den * *Azure Active Directory - *directoryname* ** blade (det vil sige, Azure AD bladet til den mappe, du administrerer), Vælg **Enterprise programmer **.

    ![Åbne virksomhedsapps](./media/active-directory-coreapps-change-app-logo-azure-portal/open-enterprise-apps.png)

4. Vælg **alle programmer**bladet **Enterprise-programmer** . Du får vist en liste over de apps, kan du administrere.

5. Vælg en app på bladet **Enterprise applications - alle programmer** .

6. Vælg **Egenskaber**på bladet ***programnavn*** (det vil sige, bladet med navnet på den markerede app i titlen).

    ![Vælge egenskabskommandoen](./media/active-directory-coreapps-change-app-logo-azure-portal/select-app.png)

7. På ***programnavn*** **-Egenskaber** blade, Søg efter en fil for at bruge som et nyt logo, eller rediger appnavnet eller begge dele.

    ![Ændre kommandoen app logo eller nameproperties](./media/active-directory-coreapps-change-app-logo-azure-portal/change-logo.png)

8. Vælge kommandoen **Gem** .

## <a name="next-steps"></a>Næste trin

- [Se alle mine grupper](active-directory-groups-view-azure-portal.md)
- [Tildele en bruger eller gruppe til en enterprise-app](active-directory-coreapps-assign-user-azure-portal.md)
- [Fjerne en bruger eller gruppe opgave fra en enterprise-app](active-directory-coreapps-remove-assignment-azure-portal.md)
- [Deaktivere bruger logon for en enterprise-app](active-directory-coreapps-disable-app-azure-portal.md)
