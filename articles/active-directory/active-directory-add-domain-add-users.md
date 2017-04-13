<properties
    pageTitle="Føje brugere til et brugerdefineret domæne i Azure Active Directory | Microsoft Azure"
    description="Sådan indsættes et brugerdefineret domæne i Azure Active Directory med brugerkonti."
    services="active-directory"
    documentationCenter=""
    authors="jeffsta"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="curtand;jeffsta"/>

# <a name="assign-users-to-a-custom-domain"></a>Føje brugere til et brugerdefineret domæne

Når du har tilføjet dit brugerdefinerede domæne til Azure Active Directory, skal du tilføje brugerkonti for dette domæne, så du kan begynde at godkende dem.

## <a name="users-synced-in-from-a-directory-on-your-corporate-network"></a>Brugere, der er synkroniseret i fra en mappe på virksomhedens netværk

Hvis du har allerede konfigureret en forbindelse mellem dine lokale Active Directory og Azure Active Directory, kan synkronisering udfylde kontiene. Du kan finde flere oplysninger om, hvordan du synkronisere Azure Active Directory med din lokale Active Directory, kan du se [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).

## <a name="users-added-and-managed-in-the-cloud"></a>Brugere, der er tilføjet og administreres i skyen

Sådan ændres til domænet for en eksisterende brugerkonto:

1.  Åbn Azure-klassisk portalen ved hjælp af en konto, der er en global administrator eller en bruger.

2.  Åbn mappen.

3.  Vælg fanen **brugere** .

4.  Vælg brugeren på listen.

5.  Ret domænet for brugeren, og vælg derefter **Gem**.

Dette kan også gøres ved hjælp af [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) eller [Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

## <a name="select-a-custom-domain-when-creating-a-new-user"></a>Vælg et brugerdefineret domæne, når du opretter en ny bruger

1.  Åbn Azure-klassisk portalen ved hjælp af en konto, der er en global administrator eller en bruger.

2.  Åbn mappen.

3.  Vælg fanen **brugere** .

4.  I kommandolinjen skal du vælge **Tilføj**.

5.  Når du tilføjer brugernavnet, Vælg dit eget domæne domænelisten.

6.  Vælg **Gem**.

## <a name="next-steps"></a>Næste trin

-   [Brug af brugerdefinerede domænenavne til at forenkle logonoplevelse for dine brugere](active-directory-add-domain.md)

-   [Administrere brugerdefinerede domænenavne](active-directory-add-manage-domain-names.md)

-   [Få mere at vide om domænet management begreber i Azure AD](active-directory-add-domain-concepts.md)
