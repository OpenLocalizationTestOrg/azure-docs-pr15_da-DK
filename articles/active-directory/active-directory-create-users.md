<properties
    pageTitle="Tilføje nye brugere til Azure Active Directory | Microsoft Azure"
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
    ms.topic="get-started-article"
    ms.date="09/22/2016"
    ms.author="curtand"/>

# <a name="add-new-users--or-users-with-microsoft-accounts-to-azure-active-directory"></a>Tilføje nye brugere eller brugere med Microsoft-konti til Azure Active Directory

Tilføje brugere for at udfylde adresselisten. I denne artikel beskrives det, hvordan du tilføjer nye brugere i organisationen, og hvordan du tilføjer brugere, der har Microsoft-konti. Du kan finde flere oplysninger om at tilføje brugere fra andre mapper i Azure Active Directory eller tilføje brugere fra partnervirksomheder, [Tilføj brugere fra andre mapper eller partnervirksomheder i Azure Active Directory](active-directory-create-users-external.md). Tilføjede brugere har ikke administratorrettigheder som standard, men du kan tildele roller til dem når som helst.

## <a name="add-a-user"></a>Tilføje en bruger

1. Log på [Azure klassisk portal](https://manage.windowsazure.com) med en konto, der er en global administrator for kataloget.
2. Vælg **Active Directory**, og vælg derefter navnet på din organisation-mappe.
3. Vælg fanen **brugere** , og vælg derefter **Tilføj bruger**i kommandolinjen.
4. På siden **fortælle os om denne bruger** under **Type af bruger**enten vælge:

    - **Ny bruger i organisationen** – tilføjer en ny brugerkonto i mappen.
    - **Bruger med en eksisterende Microsoft-konto** – føjer en eksisterende Microsoft forbruger-konto til adresselisten (for eksempel en Outlook-konto)

5. Afhængigt af **typen af brugeren**skal angive et brugernavn (for nye brugere) eller en e-mail-adresse (for en bruger med en Microsoft-konto).
6. På siden **profil** , angive et første og sidste navn, et brugervenligt navn og en brugerrolle fra listen **roller** . Du kan finde flere oplysninger om brugeren og administratoren roller, [tildele administratorroller i Azure AD](active-directory-assign-admin-roles.md). Angiv, om du vil **Aktivere Multi-Factor Authentication** for brugeren.
7. Vælg **Opret**på siden **Hent midlertidige adgangskode** .

> [AZURE.IMPORTANT] Hvis din organisation bruger mere end ét domæne, bør du vide om følgende problemer, når du tilføjer en brugerkonto:
>
> - **Hvis du vil tilføje brugerkonti med det samme brugerens hovednavn (UPN) på tværs af domæner, tilføje, for eksempel** geoffgrisso@contoso.onmicrosoft.com, **efterfulgt af** geoffgrisso@contoso.com.
> - Tilføj **ikke** geoffgrisso@contoso.com før du tilføjer geoffgrisso@contoso.onmicrosoft.com. I denne rækkefølge er vigtigt, og kan være besværligt at fortryde.

## <a name="change-user-information"></a>Ændre brugeroplysninger

Du kan ændre en hvilken som helst bruger attribut med undtagelse af objekt-ID.

1. Åbn mappen.
2. Vælg fanen **brugere** , og vælg derefter det viste navn på den bruger, du vil ændre.
3. Udfør ændringerne, og klik derefter på **Gem**.

Hvis den bruger, du vil ændre er synkroniseret med din lokale Active Directory-tjenesten, kan du ikke ændre brugeroplysningerne på denne måde. Hvis du vil ændre brugeren, kan bruge din lokale Active Directory-administrationsværktøjer.

## <a name="guest-user-management-and-limitations"></a>Gæst brugeradministration og begrænsninger

Gæstekonti er brugere fra andre mapper, der er inviteret til dit bibliotek til at få adgang til SharePoint-dokumenter, programmer eller andre Azure ressourcer. En gæstekonto i mappen har dens underliggende UserType-attribut, der er angivet til "Gæst". Almindelige brugere (nærmere betegnet kan medlemmer af adresselisten) har attributten UserType "Medlem."

Gæster har et begrænset antal rights i kataloget. Disse rettigheder begrænse muligheden for, at gæster til at finde oplysninger om andre brugere i kataloget. Gæstebrugere kan dog stadig interagere med de brugere og grupper, der er knyttet til de ressourcer, de arbejder på. Gæstebrugere kan:

- Få vist andre brugere og grupper, der er knyttet til en Azure abonnement, som de er tildelt
- Få vist medlemmerne af grupper, de tilhører
- Slå op andre brugere i kataloget, hvis de ved den fulde mailadresse på brugeren
- Se et begrænset antal attributter for de brugere, de slå op – begrænset til at få vist navn, mailadresse, brugerens hovednavn (UPN) og miniature
- Få en liste over bekræftede domæner i kataloget
- Samtykke til programmer, give dem samme adgang, der har medlemmer i adresselisten

## <a name="set-guest-user-access-policies"></a>Angiv gæst access brugerpolitikker

Fanen **Konfigurer** i en mappe indeholder indstillinger til at styre adgangen til gæstebrugere. Disse indstillinger kan ændres kun i Azure klassisk portal af directory global administrator. Der findes i øjeblikket ingen PowerShell eller API metode.

Vælg **Active Directory**, og vælg derefter navnet på mappen for at åbne fanen **Konfigurer** i portalen Azure klassisk.

![Konfigurere fane i Azure Active Directory][1]

Derefter kan du redigere indstillingerne til at styre adgangen til gæstebrugere.

![indstillinger til styring af adgang til gæstebrugere][2]


## <a name="whats-next"></a>Hvad skal der ske nu

- [Tilføje brugere fra andre mapper eller partnervirksomheder i Azure Active Directory](active-directory-create-users-external.md)
- [Administrere Azure AD](active-directory-administer.md)
- [Administrere adgangskoder i Azure AD](active-directory-manage-passwords.md)
- [Administrere grupper i Azure AD](active-directory-manage-groups.md)

<!--Image references-->
[1]: ./media/active-directory-create-users/RBACDirConfigTab.png
[2]: ./media/active-directory-create-users/RBACGuestAccessControls.png
