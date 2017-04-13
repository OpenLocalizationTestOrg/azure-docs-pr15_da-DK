<properties
    pageTitle="Deaktivere bruger logon for en enterprise-app i Azure Active Directory preview | Microsoft Azure"
    description="Sådan deaktiveres et enterprise-program, så ingen brugere kan logge på til den i Azure Active Directory"
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
    ms.date="10/17/2016"
    ms.author="curtand"/>


# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory-preview"></a>Deaktivere bruger logon for en enterprise-app i Azure Active Directory preview

Det er nemt at deaktivere en systemer i virksomhed, så ingen brugere kan logge på til den i Azure Active Directory (Azure AD) preview. [Hvad er i preview?](active-directory-preview-explainer.md) Du skal have de relevante tilladelser til at administrere enterprise-app. Du skal være global administrator for mappen i den aktuelle preview.

## <a name="how-do-i-disable-user-sign-ins"></a>Hvordan deaktiverer jeg bruger logon?

1. Log på [Azure-portalen](https://portal.azure.com) med en konto, der er en global administrator for kataloget.

2. Vælge **flere tjenester**, Angiv **Azure Active Directory** i tekstfeltet, og tryk derefter på **Enter**.

3. På den * *Azure Active Directory - *directoryname* ** blade (det vil sige, Azure AD bladet til den mappe, du administrerer), Vælg **Enterprise programmer **.

    ![Åbne virksomhedsapps](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)

4. Vælg **alle programmer**bladet **Enterprise-programmer** . Du kan se en liste over de apps, kan du administrere.

5. Vælg en app på bladet **Enterprise applications - alle programmer** .

6. Vælg **Egenskaber**på bladet ***programnavn*** (det vil sige, bladet med navnet på den markerede app i titlen).

    ![Vælge kommandoen alle programmer](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)

7. På ***programnavn*** **-Egenskaber** blade, Vælg **ikke** **aktiveret for brugerne at logge på?**.

8. Vælge kommandoen **Gem** .

## <a name="next-steps"></a>Næste trin

- [Se alle mine grupper](active-directory-groups-view-azure-portal.md)
- [Tildele en bruger eller gruppe til en enterprise-app](active-directory-coreapps-assign-user-azure-portal.md)
- [Fjerne en bruger eller gruppe opgave fra en enterprise-app](active-directory-coreapps-remove-assignment-azure-portal.md)
- [Ændre navnet på eller logo på en enterprise-app](active-directory-coreapps-change-app-logo-user-azure-portal.md)
