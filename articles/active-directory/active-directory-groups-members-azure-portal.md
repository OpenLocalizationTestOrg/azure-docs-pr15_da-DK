<properties
    pageTitle="Administrere medlemmer for en gruppe i Azure Active Directory preview | Microsoft Azure"
    description="Sådan brugere og enheder, der er medlemmer af en gruppe i Azure Active Directory"
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


# <a name="manage-the-members-for-a-group-in-azure-active-directory-preview"></a>Administrere medlemmer for en gruppe i Azure Active Directory preview

I denne artikel beskrives det, hvordan du administrerer medlemmer for en gruppe i Azure Active Directory (Azure AD) preview. [Hvad er i preview?](active-directory-preview-explainer.md)

## <a name="how-do-i-find-the-members-and-manage-them"></a>Hvordan finder medlemmerne og administrere dem?

1.  Log på [Azure-portalen](https://portal.azure.com) med en konto, der er en global administrator for kataloget.

2.  Vælge **flere tjenester**, Angiv **brugere og grupper** i tekstfeltet, og tryk derefter på **Enter**.

  ![Åbne brugeradministration](./media/active-directory-groups-members-azure-portal/search-user-management.png)

3.  Vælg **alle grupper**på bladet **brugere og grupper** .

  ![Åbning af bladet grupper](./media/active-directory-groups-members-azure-portal/view-groups-blade.png)

4. Vælg en gruppe på bladet **brugere og grupper - alle grupper** .

5. På den * *gruppe – *gruppenavn* ** blade, Vælg **medlemmer **.

  ![Åbning af bladet medlemmer](./media/active-directory-groups-members-azure-portal/view-group-members.png)

6. Vælg **Tilføj medlemmer**for at føje medlemmer til gruppen på bladet **gruppe - medlemmer** .

  ![Tilføje medlemmer kommando](./media/active-directory-groups-members-azure-portal/add-group-members-command.png)

7. Vælg en eller flere brugere eller enheder til at føje til gruppen, og vælg knappen **Vælg** i bunden af bladet du føjer dem til gruppen på bladet **medlemmer** . Feltet **bruger** filtrerer visningen baseret på tilsvarende dit bidrag til en del af navnet på en bruger eller enhed. Ingen jokertegn accepteres i feltet.

8. Vælg et medlem, hvis du vil fjerne medlemmer fra gruppen på bladet **gruppe - medlemmer** .

9. Vælg kommandoen **Remove** på bladet ***medlemsnavn*** , og bekræfte dit valg, når du bliver spurgt.

  ![fjerne medlemmer kommando](./media/active-directory-groups-members-azure-portal/remove-group-members-command.png)

9. Når du er færdig med at ændre medlemmer til gruppen, kan du vælge **Gem**.


## <a name="additional-information"></a>Yderligere oplysninger

Disse artikler giver yderligere oplysninger om Azure Active Directory.

* [Se en eksisterende gruppe](active-directory-groups-view-azure-portal.md)
* [Oprette en ny gruppe og tilføje medlemmer](active-directory-groups-create-azure-portal.md)
* [Administrere indstillingerne for en gruppe](active-directory-groups-settings-azure-portal.md)
* [Administrere medlemskaberne af en gruppe](active-directory-groups-membership-azure-portal.md)
* [Administrere dynamiske regler for brugere i en gruppe](active-directory-groups-dynamic-membership-azure-portal.md)
