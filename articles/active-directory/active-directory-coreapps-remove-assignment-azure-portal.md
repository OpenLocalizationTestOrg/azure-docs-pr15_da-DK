<properties
    pageTitle="Fjerne en bruger eller gruppe tildeling fra en enterprise-app i Azure Active Directory preview | Microsoft Azure"
    description="Sådan fjernes en access-tildeling af en bruger eller gruppe fra en enterprise-app i Azure Active Directory"
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


# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory-preview"></a>Fjerne en bruger eller gruppetildeling fra en enterprise-app i Azure Active Directory preview

Det er nemt at fjerne en bruger eller en gruppe i at blive tildelt adgang til en af dine enterprise-programmer i Azure Active Directory (Azure AD) preview. [Hvad er i preview?](active-directory-preview-explainer.md) Du skal have de relevante tilladelser til at administrere enterprise-app. Du skal være global administrator for mappen i den aktuelle preview.

## <a name="how-do-i-remove-a-user-or-group-assignment"></a>Hvordan fjerner jeg en bruger eller gruppetildeling?

1. Log på [Azure-portalen](https://portal.azure.com) med en konto, der er en global administrator for kataloget.

2. Vælge **flere tjenester**, Angiv **Azure Active Directory** i tekstfeltet, og tryk derefter på **Enter**.

3. På den * *Azure Active Directory - *directoryname* ** blade (det vil sige, Azure AD bladet til den mappe, du administrerer), Vælg **Enterprise programmer **.

    ![Åbne virksomhedsapps](./media/active-directory-coreapps-remove-assignment-user-azure-portal/open-enterprise-apps.png)

4. Vælg **alle programmer**bladet **Enterprise-programmer** . Du får vist en liste over de apps, kan du administrere.

5. Vælg en app på bladet **Enterprise applications - alle programmer** .

6. Vælg **brugere og grupper**på bladet ***programnavn*** (det vil sige, bladet med navnet på den markerede app i titlen).

    ![Vælge brugere eller grupper](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-app-users.png)

7. På ***programnavn*** **-bruger og gruppetildeling** blade, Vælg en af flere brugere eller grupper, og vælg derefter kommandoen **Fjern** . Bekræft din beslutning kommandoprompten.

    ![Vælge kommandoen Fjern](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-users.png)

## <a name="next-steps"></a>Næste trin

- [Se alle mine grupper](active-directory-groups-view-azure-portal.md)
- [Tildele en bruger eller gruppe til en enterprise-app](active-directory-coreapps-assign-user-azure-portal.md)
- [Deaktivere bruger logon for en enterprise-app](active-directory-coreapps-disable-app-azure-portal.md)
- [Ændre navnet på eller logo på en enterprise-app](active-directory-coreapps-change-app-logo-user-azure-portal.md)
