<properties
    pageTitle="Føje brugere fra andre mapper eller partner firmaer i Azure Active Directory preview | Microsoft Azure"
    description="Beskriver, hvordan du tilføjer brugere eller ændre brugeroplysninger i Azure Active Directory, herunder eksterne og gæster brugere."
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

# <a name="add-users-from-other-directories-or-partner-companies-in-azure-active-directory-preview"></a>Tilføje brugere fra andre mapper eller partnervirksomheder i Azure Active Directory preview

> [AZURE.SELECTOR]
- [Azure-portalen](active-directory-users-create-external-azure-portal.md)
- [Azure klassisk portal](active-directory-create-users-external.md)

I denne artikel beskrives det, hvordan du tilføjer brugere fra andre mapper i Vis udskrift Azure Active Directory (Azure AD) eller fra partnere. [Hvad er i preview?](active-directory-preview-explainer.md) Du kan finde oplysninger om at tilføje nye brugere i organisationen og tilføje brugere, der har Microsoft-konti [Tilføj nye brugere til Azure Active Directory](active-directory-users-create-azure-portal.md). Tilføjede brugere har ikke administratorrettigheder som standard, men du kan tildele roller til dem når som helst.

## <a name="add-a-user"></a>Tilføje en bruger

1.  Log på [Azure-portalen](https://portal.azure.com) med en konto, der er en global administrator for kataloget.

2.  Vælge **flere tjenester**, Angiv **brugere og grupper** i tekstfeltet, og tryk derefter på **Enter**.

    ![Åbne brugeradministration](./media/active-directory-users-create-external-azure-portal/create-users-user-management.png)

3.  Vælg **brugere**på bladet **brugere og grupper** , og vælg derefter **Tilføj**.

    ![Vælge kommandoen Tilføj](./media/active-directory-users-create-external-azure-portal/create-users-add-command.png)

4. Angive et navn i **navn** og en brugerens logonnavn i **brugernavn**på bladet **bruger** .

5. Kopiere eller notere ellers genererede brugeradgangskode, så du kan angive den til brugeren, når denne proces er fuldført.

6. Du kan vælge **profil** for at tilføje brugerne først og efternavn, en titel og et afdelingsnavn.
    
    ![Åbne brugerprofilen](./media/active-directory-users-create-external-azure-portal/create-users-user-profile.png)

    - Vælg **grupper** for at føje brugeren til en eller flere grupper.

        ![Tilføje en bruger til grupper](./media/active-directory-users-create-external-azure-portal/create-users-user-groups.png)

    - Vælg **rolle i virksomheden** , tildele brugeren til en rolle fra listen **roller** . Du kan finde flere oplysninger om brugeren og administratoren roller, [tildele administratorroller i Azure AD](active-directory-assign-admin-roles.md).

        ![Tildele en bruger til en rolle](./media/active-directory-users-create-external-azure-portal/create-users-assign-role.png)

7. Vælg **Opret**.

8. Distribuere sikkert oprettet adgangskoden til den nye bruger, så brugeren kan logge på.

> [AZURE.IMPORTANT] Hvis din organisation bruger mere end ét domæne, bør du vide om følgende problemer, når du tilføjer en brugerkonto:
>
> - **Hvis du vil tilføje brugerkonti med det samme brugerens hovednavn (UPN) på tværs af domæner, tilføje, for eksempel** geoffgrisso@contoso.onmicrosoft.com, **efterfulgt af** geoffgrisso@contoso.com.
> - Tilføj **ikke** geoffgrisso@contoso.com før du tilføjer geoffgrisso@contoso.onmicrosoft.com. I denne rækkefølge er vigtigt, og kan være besværligt at fortryde.

Hvis du ændrer oplysninger for en bruger, hvis identitet er synkroniseret med din lokale Active Directory-tjenesten, kan du ikke ændre brugeroplysninger i portalen Azure klassisk. For at ændre brugeroplysninger skal du bruge din lokale Active Directory-administrationsværktøjer.


## <a name="whats-next"></a>Hvad skal der ske nu

- [Tilføje en bruger](active-directory-users-create-azure-portal.md)
- [Nulstille en brugers adgangskode i den nye Azure portal](active-directory-users-reset-password-azure-portal.md)
- [Tildele en bruger til en rolle i din Azure AD](active-directory-users-assign-role-azure-portal.md)
- [Ændre en brugers oplysninger](active-directory-users-work-info-azure-portal.md)
- [Administrere brugerprofiler](active-directory-users-profile-azure-portal.md)
- [Slette en bruger i din Azure AD](active-directory-users-delete-user-azure-portal.md)
