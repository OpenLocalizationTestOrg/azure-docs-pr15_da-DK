<properties
    pageTitle="Administrere tilladelser til ressourcer per bruger Azure stablede (tjenesteadministratoren og lejer) | Microsoft Azure"
    description="Lær, hvordan du administrere tilladelser til ressourcer per bruger som en tjenesteadministratoren eller lejer."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="erikje"/>

# <a name="manage-user-permissions"></a>Administrere brugertilladelser

En bruger i Azure stak kan være en læser, ejer eller bidragyder for hver forekomst af et abonnement, ressourcegruppe eller tjeneste. Bruger A kan for eksempel har Læsertilladelser til abonnement 1, men har tilladelser som ejer til virtuelt 7.

-   Læser: Brugeren kan se alt, men kan ikke foretage eventuelle ændringer.

-   Bidragyder: Brugeren kan administrere alt undtagen adgang til ressourcer.

-   Ejer: Brugeren kan administrere alt, herunder adgang til ressourcer.


## <a name="set-access-permissions-for-a-user"></a>Angive adgangstilladelser for en bruger

1.  Logge på med en konto, der har tilladelser som ejer af den ressource, du vil administrere.

2.  Klik på ikonet **Access** i bladet for ressourcen, ![](media/azure-stack-manage-permissions/image1.png).

3.  Klik på **roller**i bladet **brugere** .

4.  Klik på **Tilføj** for at tilføje tilladelser for brugeren i bladet **roller** .

## <a name="next-steps"></a>Næste trin

[Tilføje en Azure stak lejer](azure-stack-add-new-user-aad.md)
