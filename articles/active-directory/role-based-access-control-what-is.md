<properties
    pageTitle="Rollebaseret adgangskontrol | Microsoft Azure"
    description="Komme i gang med administration af adgang med Azure rollebaseret adgangskontrol i portalen Azure. Brug rolletildelinger til at tildele tilladelser i mappen."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/03/2016"
    ms.author="kgremban"/>

# <a name="get-started-with-access-management-in-the-azure-portal"></a>Komme i gang med administration af adgang i portalen Azure

Sikkerhed rundt firmaer skal fokusere på giver medarbejdere de nøjagtige nødvendige tilladelser. For mange tilladelser Fremviser en konto til hackere. For få tilladelser betyder, at medarbejdere ikke kan få arbejdet fra hånden effektivt. Azure rollebaseret Access kontrolelement (RBAC) hjælper med at løse problemet ved at tilbyde detaljerede access-administration for Azure.

Ved hjælp af RBAC, kan du udskille opgaver i dit team og give kun mængden adgang til brugere, at de skal udføre deres arbejde. Ubegrænset tilladelser i stedet for at give alle i din Azure abonnement eller ressourcer, kan du tillade kun visse handlinger. For eksempel bruge RBAC til at lade en medarbejder administrere virtuelle maskiner i et abonnement, mens en anden kan administrere SQL-databaser i det samme abonnement.

## <a name="basics-of-access-management-in-azure"></a>Grundlæggende om access management i Azure
Hvert Azure abonnement er knyttet til en mappe til Azure Active Directory (AD). Brugere, grupper og programmer fra mappen kan administrere ressourcer i Azure-abonnement. Tildele disse rettigheder med Azure-portalen, Azure kommandolinjen værktøjer og Azure Management API'er.

Give adgang ved at tildele den relevante RBAC rolle til brugere, grupper og programmer på et bestemt område. Omfanget af en rolletildeling kan være et abonnement, en ressourcegruppe eller en enkelt ressource. En rolle, der er tildelt på et overordnet omfang giver også adgang til de underordnede, der er indeholdt i den. For eksempel kan en bruger med adgang til en ressourcegruppe administrere alle de ressourcer, den indeholder, som websteder, virtuelle maskiner og undernet.

![Relation mellem elementer Azure Active Directory - diagram](./media/role-based-access-control-what-is/rbac_aad.png)

Rollen RBAC, som du tildeler bestemmer, hvilke ressourcer, der bruger, gruppe eller -programmer kan administrere inden for området.

## <a name="built-in-roles"></a>Indbyggede roller
Azure RBAC har tre grundlæggende roller, der gælder for alle ressourcetyper:

- **Ejeren** har fuld adgang til alle de ressourcer, herunder ret til at stedfortræderadgang til andre.
- **Bidragyder** kan oprette og administrere alle typer Azure ressourcer, men kan ikke give adgang til andre.
- **Læser** kan få vist eksisterende Azure ressourcer.

Resten af RBAC roller i Azure Tillad styring af bestemte Azure ressourcer. Rollen bidragyder virtuelt kan for eksempel brugeren til at oprette og administrere virtuelle maskiner. Det ikke giver dem adgang til det virtuelle netværk eller det undernet, som den virtuelle maskine opretter forbindelse til.

[RBAC indbyggede roller](role-based-access-built-in-roles.md) viser de roller, der er tilgængelige i Azure. Det angiver de handlinger og omfang, der hver indbyggede rolle tildeler til brugere. Hvis du vil definere dine egne roller for endnu mere kontrol, se, hvordan du opretter [brugerdefinerede roller i Azure RBAC](role-based-access-control-custom-roles.md).

## <a name="resource-hierarchy-and-access-inheritance"></a>Nedarvning af ressource hierarki og access
- Hvert **abonnement** i Azure hører til kun én mappe.
- Hver **ressourcegruppe** hører til kun én abonnement.
- Hver **ressource** tilhører kun én ressourcegruppe.

Access, som du giver på overordnede områder nedarves på underordnede områder. Eksempel:

- Du kan tildele rollen Læser til en Azure AD-gruppe i området abonnement. Medlemmer af denne gruppe kan få vist alle ressourcegruppe og ressource i abonnement.
- Du kan tildele rollen bidragyder til et program i området ressource gruppe. Det kan administrere ressourcer af alle typer i ressourcegruppen, men ikke andre ressourcegrupper i abonnement.

## <a name="azure-rbac-vs-classic-subscription-administrators"></a>Azure RBAC kontra klassisk abonnement administratorer
Klassisk abonnement administratorer og flere administratorer har fuld adgang til Azure abonnementet. De kan administrere ressourcer ved hjælp af [Azure-portalen](https://portal.azure.com) med Azure ressourcestyring API'er eller den klassiske implementeringsmodel [Azure klassisk portal](https://manage.windowsazure.com) og Azure. I modellen RBAC er klassisk administratorer tildelt rollen som ejer i området abonnement.

Kun Azure portalen og nye Azure ressourcestyring API'er understøtter Azure RBAC. Brugere og programmer, der er tildelt RBAC roller kan ikke bruge portalen administration af klassiske og Azure klassisk implementeringsmodel.

## <a name="authorization-for-management-vs-data-operations"></a>Tilladelse til at administrere kontra handlinger med data
Azure RBAC understøtter kun management handlinger for de ressourcer, Azure i Azure portalen og Azure ressourcestyring API'er. Det kan ikke tillade, at alle data niveau handlinger for Azure ressourcer. Du kan for eksempel Godkend nogen til at administrere lagerplads konti, men ikke til BLOB eller tabeller i en lagerplads konto ikke. På samme måde, en SQL-database kan administreres, men ikke tabellerne i modellen.

## <a name="next-steps"></a>Næste trin
- Introduktion til [Rollebaseret adgangskontrol i portalen Azure](role-based-access-control-configure.md).
- Se [RBAC indbyggede roller](role-based-access-built-in-roles.md)
- Definere dine egne [brugerdefinerede roller i Azure RBAC](role-based-access-control-custom-roles.md)
