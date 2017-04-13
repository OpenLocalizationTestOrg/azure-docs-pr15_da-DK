<properties
    pageTitle="Oprette en ny gruppe i Azure Active Directory preview | Microsoft Azure"
    description="Sådan oprettes en gruppe i Azure Active Directory og tilføje brugere (medlemmer) til gruppen"
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


# <a name="create-a-new-group-in-azure-active-directory-preview"></a>Oprette en ny gruppe i Azure Active Directory preview

> [AZURE.SELECTOR]
- [Azure-portalen](active-directory-groups-create-azure-portal.md)
- [Azure klassisk portal](active-directory-accessmanagement-manage-groups.md)
- [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)

I denne artikel beskrives det, hvordan du opretter og udfylde en ny gruppe i Azure Active Directory (Azure AD) preview. [Hvad er i preview?](active-directory-preview-explainer.md) Bruge en gruppe til at udføre administrationsopgaver som tildeling af licenser eller tilladelser til et antal brugere eller enheder på én gang.

## <a name="how-do-i-create-a-group"></a>Hvordan opretter jeg en gruppe?

1. Log på [Azure-portalen](https://portal.azure.com) med en konto, der er en global administrator for kataloget.

2. Vælge **flere tjenester**, Angiv **bruger og grupper** i tekstfeltet, og tryk derefter på **Enter**.

  ![Åbne brugeradministration](./media/active-directory-groups-create-azure-portal/search-user-management.png)

3. Vælg **alle grupper**på bladet **brugere og grupper** .

  ![Åbning af bladet grupper](./media/active-directory-groups-create-azure-portal/view-groups-blade.png)

4. Vælg kommandoen **Tilføj** på bladet **brugere og grupper - alle grupper** .

  ![Vælge kommandoen Tilføj](./media/active-directory-groups-create-azure-portal/add-group-command.png)

5. Tilføje et navn og en beskrivelse af gruppen på bladet **gruppe** .

6. Vælg **tildelte** i feltet **type af medlemskab** for at vælge medlemmer der skal tilføjes til gruppen, og vælg derefter **medlemmer**. Du kan finde flere oplysninger om, hvordan du administrerer medlemskabet af en gruppe dynamisk [ved hjælp af attributter til at oprette avancerede regler for gruppemedlemskab](active-directory-groups-dynamic-membership-azure-portal.md).

  ![Vælge medlemmer, der skal tilføjes](./media/active-directory-groups-create-azure-portal/select-members.png)

5. Vælg en eller flere brugere eller enheder til at føje til gruppen, og vælg knappen **Vælg** i bunden af bladet du føjer dem til gruppen på bladet **medlemmer** . Feltet **bruger** filtrerer visningen baseret på tilsvarende dit bidrag til en del af navnet på en bruger eller enhed. Ingen jokertegn accepteres i feltet.

6. Når du er færdig med at føje medlemmer til gruppen, kan du vælge **Opret** på bladet **gruppe** .    

  ![Opret gruppe bekræftelse](./media/active-directory-groups-create-azure-portal/create-group-confirmation.png)




## <a name="additional-information"></a>Yderligere oplysninger

Disse artikler giver yderligere oplysninger om Azure Active Directory.

* [Se en eksisterende gruppe](active-directory-groups-view-azure-portal.md)
* [Administrere indstillingerne for en gruppe](active-directory-groups-settings-azure-portal.md)
* [Administrere medlemmer af en gruppe](active-directory-groups-members-azure-portal.md)
* [Administrere medlemskaberne af en gruppe](active-directory-groups-membership-azure-portal.md)
* [Administrere dynamiske regler for brugere i en gruppe](active-directory-groups-dynamic-membership-azure-portal.md)
