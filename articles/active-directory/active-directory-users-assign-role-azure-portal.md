<properties
    pageTitle="Tildele en bruger til administratorroller i Azure Active Directory preview | Microsoft Azure"
    description="Forklarer, hvordan du kan ændre administrative brugeroplysninger i Azure Active Directory"
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

# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory-preview"></a>Tildele en bruger til administratorroller i Azure Active Directory preview

I denne artikel beskrives det, hvordan du tildeler en administrativ rolle til en bruger i Azure Active Directory (Azure AD) preview. [Hvad er i preview?](active-directory-preview-explainer.md) Du kan finde oplysninger om tilføjelse af nye brugere i organisationen [Tilføj nye brugere til Azure Active Directory](active-directory-users-create-azure-portal.md). Tilføjede brugere har ikke administratorrettigheder som standard, men du kan tildele roller til dem når som helst.

## <a name="assign-a-role-to-a-user"></a>Tildel en rolle til en bruger

1.  Log på [Azure-portalen](https://portal.azure.com) med en konto, der er en global administrator for kataloget.

2.  Vælge **flere tjenester**, Angiv **brugere og grupper** i tekstfeltet, og tryk derefter på **Enter**.

    ![Åbne brugeradministration](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)

3.  Vælg **alle brugere**på bladet **brugere og grupper** .

    ![Åbning af bladet alle brugere](./media/active-directory-users-assign-role-azure-portal/create-users-open-users-blade.png)

4. Vælg en bruger på listen på bladet **brugere og grupper - alle brugere** .

5. Vælg **Directory rolle**på bladet for den valgte bruger, og derefter tildele brugeren til en rolle fra listen **Directory-rolle** . Du kan finde flere oplysninger om brugeren og administratoren roller, [tildele administratorroller i Azure AD](active-directory-assign-admin-roles.md).

      ![Tildele en bruger til en rolle](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)

6. Vælg **Gem**.


## <a name="whats-next"></a>Hvad skal der ske nu

- [Tilføje en bruger](active-directory-users-create-azure-portal.md)
- [Nulstille en brugers adgangskode i den nye Azure portal](active-directory-users-reset-password-azure-portal.md)
- [Ændre en brugers oplysninger](active-directory-users-work-info-azure-portal.md)
- [Administrere brugerprofiler](active-directory-users-profile-azure-portal.md)
- [Slette en bruger i din Azure AD](active-directory-users-delete-user-azure-portal.md)
