<properties
    pageTitle="Administrere grupper din gruppe er medlem af i Azure Active Directory preview | Microsoft Azure"
    description="Grupper kan indeholde andre grupper i Azure Active Directory. Her er, hvordan du administrerer disse medlemskaber."
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


# <a name="manage-the-groups-your-group-is-a-member-of-in-azure-active-directory-preview"></a>Administrere grupper din gruppe er medlem af i Azure Active Directory preview

Grupper kan indeholde andre grupper i Azure Active Directory preview. [Hvad er i preview?](active-directory-preview-explainer.md) Her er, hvordan du administrerer disse medlemskaber.

## <a name="how-do-i-find-the-groups-my-group-is-a-member-of"></a>Hvordan finder jeg min gruppe er medlem af grupperne?

1.  Log på [Azure-portalen](https://portal.azure.com) med en konto, der er en global administrator for kataloget.

2.  Vælge **flere tjenester**, Angiv **brugere og grupper** i tekstfeltet, og tryk derefter på **Enter**.

  ![Åbne brugeradministration](./media/active-directory-groups-membership-azure-portal/search-user-management.png)

3.  Vælg **alle grupper**på bladet **brugere og grupper** .

  ![Åbning af bladet grupper](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)

4. Vælg en gruppe på bladet **brugere og grupper - alle grupper** .

5. På den * *gruppe - *gruppenavn* ** blade, Vælg **gruppere medlemskaber **.

  ![Åbning af bladet gruppe medlemskaber](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)

6. Vælg kommandoen **Tilføj** for at tilføje din gruppe som medlem af en anden gruppe, på bladet **gruppe - gruppemedlemskaber** .

7. Vælg en gruppe fra bladet **Vælg gruppe** , og vælg derefter knappen **Vælg** i bunden af bladet. Du kan tilføje din gruppe til kun én gruppe ad gangen. Feltet **bruger** filtrerer visningen baseret på tilsvarende dit bidrag til en del af navnet på en bruger eller enhed. Ingen jokertegn accepteres i feltet.

  ![Tilføje en gruppemedlemskab](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)

8. Vælg en gruppe for at fjerne din gruppe som medlem af en anden gruppe, på bladet **gruppe - gruppemedlemskaber** .

9. Vælg kommandoen **Remove** på bladet ***gruppenavn*** , og bekræfte dit valg, når du bliver spurgt.

  ![fjerne medlemskab kommando](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)

9. Når du er færdig med at ændre gruppemedlemskaber for din gruppe, kan du vælge **Gem**.


## <a name="additional-information"></a>Yderligere oplysninger

Disse artikler giver yderligere oplysninger om Azure Active Directory.

* [Se en eksisterende gruppe](active-directory-groups-view-azure-portal.md)
* [Oprette en ny gruppe og tilføje medlemmer](active-directory-groups-create-azure-portal.md)
* [Administrere indstillingerne for en gruppe](active-directory-groups-settings-azure-portal.md)
* [Administrere medlemmer af en gruppe](active-directory-groups-members-azure-portal.md)
* [Administrere dynamiske regler for brugere i en gruppe](active-directory-groups-dynamic-membership-azure-portal.md)
