<properties
    pageTitle="Tildele en bruger eller gruppe til en enterprise-app i Azure Active Directory preview | Microsoft Azure"
    description="Sådan markeres en enterprise-app til at tildele en bruger eller gruppe til den i Azure Active Directory"
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
    ms.date="10/03/2016"
    ms.author="curtand"/>

# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory-preview"></a>Tildele en bruger eller gruppe til en enterprise-app i Azure Active Directory preview

Det er nemt at tildele en bruger eller en gruppe til din virksomhedsprogrammer i Azure Active Directory (Azure AD) preview. [Hvad er i preview?](active-directory-preview-explainer.md) Du skal have de relevante tilladelser til at administrere enterprise-app. Du skal være global administrator for mappen i den aktuelle preview.

## <a name="how-do-i-assign-user-access-to-an-enterprise-app"></a>Hvordan tildele brugeradgang til en enterprise-app?

1. Log på [Azure-portalen](https://portal.azure.com) med en konto, der er en global administrator for kataloget.

2. Vælge **flere tjenester**, Angiv Azure Active Directory i tekstfeltet, og tryk derefter på **Enter**.

3. På den * *Azure Active Directory - *directoryname* ** blade (det vil sige, Azure AD bladet til den mappe, du administrerer), Vælg **Enterprise programmer **.

    ![Åbne virksomhedsapps](./media/active-directory-coreapps-assign-user-azure-portal/open-enterprise-apps.png)

4. Vælg **alle programmer**bladet **Enterprise-programmer** . Du får vist en liste over de apps, kan du administrere.

5. Vælg en app på bladet **Enterprise applications - alle programmer** .

6. Vælg **brugere og grupper**på bladet ***programnavn*** (det vil sige, bladet med navnet på den markerede app i titlen).

    ![Vælge kommandoen alle programmer](./media/active-directory-coreapps-assign-user-azure-portal/select-app-users.png)

7. På ***programnavn*** **-bruger og gruppetildeling** blade, markere kommandoen **Tilføj** .

8. Vælg **brugere og grupper**på bladet **Føj opgave** .

    ![Tildele en bruger eller gruppe til appen](./media/active-directory-coreapps-assign-user-azure-portal/assign-users.png)

9. Vælg en eller flere brugere eller grupper på listen, og vælg derefter knappen **Vælg** i bunden af bladet på bladet **brugere og grupper** .

10. Vælg **rolle**på bladet **Føj opgave** . Vælg derefter en rolle til at anvende på de valgte brugere eller grupper, på bladet **Vælg rolle** , og vælg derefter knappen **OK** nederst i bladet.

11. Vælg knappen **tildele** i bunden af bladet på bladet **Føj opgave** . De tildelte brugere eller grupper, får de tilladelser, der er defineret af den valgte rolle for denne enterprise-app.

## <a name="next-steps"></a>Næste trin

- [Se alle mine grupper](active-directory-groups-view-azure-portal.md)
- [Fjerne en bruger eller gruppe opgave fra en enterprise-app](active-directory-coreapps-remove-assignment-azure-portal.md)
- [Deaktivere bruger logon for en enterprise-app](active-directory-coreapps-disable-app-azure-portal.md)
- [Ændre navnet på eller logo på en enterprise-app](active-directory-coreapps-change-app-logo-user-azure-portal.md)
