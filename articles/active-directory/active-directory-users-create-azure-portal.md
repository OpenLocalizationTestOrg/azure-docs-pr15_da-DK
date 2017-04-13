<properties
    pageTitle="Tilføje nye brugere til Azure Active Directory preview | Microsoft Azure"
    description="Forklarer, hvordan du kan tilføje nye brugere eller ændre brugeroplysninger i Azure Active Directory."
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


# <a name="add-new-users-to-azure-active-directory-preview"></a>Tilføje nye brugere til Azure Active Directory preview

> [AZURE.SELECTOR]
- [Azure-portalen](active-directory-users-create-azure-portal.md)
- [Azure klassisk portal](active-directory-create-users.md)

I denne artikel beskrives det, hvordan du tilføjer nye brugere i organisationen i Azure Active Direstory (Azure AD) preview. [Hvad er i preview?](active-directory-preview-explainer.md)

1.  Log på [Azure-portalen](https://portal.azure.com) med en konto, der er en global administrator for kataloget.

2.  Vælge **flere tjenester**, Angiv **brugere og grupper** i tekstfeltet, og tryk derefter på **Enter**.

    ![Åbne brugeradministration](./media/active-directory-users-create-azure-portal/create-users-user-management.png)

3.  Vælg **alle brugere**på bladet **brugere og grupper** , og vælg derefter **Tilføj**.

    ![Vælge kommandoen Tilføj](./media/active-directory-users-create-azure-portal/create-users-add-command.png)

4.  Angiv detaljer for brugeren, som **navn** og **brugernavn**. Domæne navn del af brugernavnet skal enten være indledende domæne "foo.onmicrosoft.com" standarddomænenavnet eller et bekræftet, uden for organisationsnetværket domænenavn som "contoso.com."

5. Kopiere eller notere ellers genererede brugeradgangskode, så du kan angive den til brugeren, når denne proces er fuldført.

6. Du kan eventuelt åbner og udfylder du oplysningerne i bladet **profil** , bladet **grupper** eller bladet **Directory-rolle** til brugeren. Du kan finde flere oplysninger om brugeren og administratoren roller, [tildele administratorroller i Azure AD](active-directory-assign-admin-roles.md).

7.  Vælg **Opret**på bladet **bruger** .

8. Distribuere sikkert oprettet adgangskoden til den nye bruger, så brugeren kan logge på.

## <a name="whats-next"></a>Hvad skal der ske nu

- [Tilføje en ekstern bruger](active-directory-users-create-external-azure-portal.md)
- [Nulstille en brugers adgangskode i den nye Azure portal](active-directory-users-reset-password-azure-portal.md)
- [Ændre en brugers oplysninger](active-directory-users-work-info-azure-portal.md)
- [Administrere brugerprofiler](active-directory-users-profile-azure-portal.md)
- [Slette en bruger i din Azure AD](active-directory-users-delete-user-azure-portal.md)
- [Tildele en bruger til en rolle i din Azure AD](active-directory-users-assign-role-azure-portal.md)
