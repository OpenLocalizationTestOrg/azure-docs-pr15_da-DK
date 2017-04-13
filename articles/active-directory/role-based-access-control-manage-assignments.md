<properties
    pageTitle="Få vist Azure access ressourcetildelinger | Microsoft Azure"
    description="Få vist og administrere alle rollebaseret adgangskontrol tildelinger for alle brugere eller grupper i portalen Azure"
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="jeffsta"/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="view-access-assignments-for-users-and-groups-in-the-azure-portal---public-preview"></a>Se access tildelinger for brugere og grupper i portalen Azure - prøveversionen

> [AZURE.SELECTOR]
- [Administrere adgangen ved bruger eller gruppe](role-based-access-control-manage-assignments.md)
- [Administrere adgangen af ressource](role-based-access-control-configure.md)

Med rollebaseret Access kontrolelement (RBAC) i Azure Active Directory preview, kan du administrere adgangen til dine Azure ressourcer. [Hvad er i preview?](active-directory-preview-explainer.md)

Access, der er tildelt med RBAC er detaljerede, fordi du kan begrænse tilladelser på to måder:

- **Omfang:** RBAC rolletildelinger er fastsat til en bestemt abonnement, ressourcegruppe eller ressource. En bruger, får adgang til en enkelt ressource kan ikke få adgang til andre ressourcer i samme-abonnement.
- **Rolle:** Er omfattet af tildelingen access gøres smallere yderligere ved at tildele en rolle. Roller kan være på højt niveau som ejer eller bestemte som virtuelt reader.

Roller kan kun tildeles fra det abonnement, ressourcegruppe eller ressource, som er omfanget for tildelingen. Men du kan få vist alle access-tildelinger for en given bruger eller gruppe på ét sted.

Få mere at vide om, hvordan du [bruge rolletildelinger til at administrere adgang til dine Azure abonnementsressourcer](role-based-access-control-configure.md).

##  <a name="view-access-assignments"></a>Se access tildelinger

Hvis du vil slå op access-tildelinger for en enkelt bruger eller gruppe, skal du starte i Azure Active Directory i [Azure-portalen](http://portal.azure.com).

1. Vælg **Azure Active Directory**. Hvis denne indstilling ikke er synlig på listen over navigation, Vælg **Flere tjenester** , og rul derefter ned til finde **Azure Active Directory**.
2. Vælg **brugere og grupper**, og klik derefter enten **alle brugere** eller **alle grupper**. I dette eksempel skal fokusere vi på de enkelte brugere.
    ![Administrere brugere og grupper i Azure Active Directory - skærmbillede](./media/role-based-access-control-manage-assignments/rbac_users_groups.png)
3. Søg efter bruger via navn eller brugernavn.
4. Vælg **Azure ressourcer** på bladet bruger. Alle access-tildelinger for den pågældende bruger vises.

### <a name="read-permissions-to-view-assignments"></a>Læsetilladelser til at se tildelinger

Denne side vises kun i access-tildelinger, du har tilladelse til at læse. For eksempel du have adgang til abonnement A og gå til bladet Azure ressourcer til at kontrollere en brugers tildelinger. Du kan se hendes access-tildelinger for A-abonnement, men kan ikke se, at hun også har access-tildelinger på abonnement B.

## <a name="delete-access-assignments"></a>Slette access-tildelinger

Fra denne blade, kan du slette access tildelinger, der er tildelt direkte til en bruger eller gruppe. Hvis access opgaven blev overført fra en overordnet gruppe, skal du gå til den ressource eller et andet abonnement og administrere tildelingen der.

1. Vælg det, du vil slette, på listen over alle access-tildelinger for en bruger eller gruppe.
2. Vælg **Fjern** og vælg derefter **Ja** for at bekræfte.
    ![Fjerne access tildeling - skærmbillede](./media/role-based-access-control-manage-assignments/delete_assignment.png)

## <a name="related-topics"></a>Relaterede emner

- Introduktion til rollebaseret adgangskontrol til [Brug rolletildelinger til at administrere adgang til dine Azure abonnementsressourcer](role-based-access-control-configure.md)
- Se [RBAC indbyggede roller](role-based-access-built-in-roles.md)
