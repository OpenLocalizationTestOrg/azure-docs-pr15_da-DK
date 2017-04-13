
<properties
    pageTitle="Næste trin for håndtering af access med grupper | Microsoft Azure"
    description="Avancerede hvordan-Sådan administration af sikkerhedsgrupper og hvordan du bruger disse grupper til at administrere adgang til en ressource."
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
    ms.date="09/22/2016"
    ms.author="curtand"/>

# <a name="managing-owners-for-a-group"></a>Administrere ejere for en gruppe
Når ejeren af en ressource er tildelt adgang til en ressource til en Azure AD-gruppe, administreres medlemmerne af gruppen af gruppeejeren af. Ejeren af ressource uddelegerer effektivt tilladelse til at tildele brugere til ressourcen til ejeren af gruppen.

## <a name="assigning-group-ownership"></a>Tildele ejerskab af gruppe

**Føje en ejer til en gruppe**

1. Vælg **Active Directory**i [Azure klassisk portal](https://manage.windowsazure.com), og Åbn derefter organisationens adressekartotek.

2. Vælg fanen **grupper** , og Åbn derefter den gruppe, du vil tilføje ejere, du vil.

3. Vælg **Tilføj ejere**.

4. Vælg den bruger, du vil tilføje som ejer af denne gruppe, og Sørg for, at dette navn føjes til ruden **valgte** på siden **Tilføj ejere** .


**Fjerne en ejer fra en gruppe**

1. Vælg **Active Directory**i [Azure klassisk portal](https://manage.windowsazure.com), og Åbn derefter organisationens adressekartotek.

2. Vælg fanen **grupper** , og Åbn derefter den gruppe, du vil fjerne en ejer fra.

4. Vælg fanen **ejere** .

5. Vælg ejeren, du vil fjerne fra gruppen, og vælg derefter **Fjern**.

## <a name="additional-information"></a>Yderligere oplysninger

Disse artikler giver yderligere oplysninger om Azure Active Directory.

* [Adgangskontrol til ressourcer med Azure Active Directory-grupper](active-directory-manage-groups.md)
* [Azure Active Directory-cmdletter til konfiguration af gruppeindstillinger](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)
* [Hvad er Azure Active Directory?](active-directory-whatis.md)
* [Integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md)
