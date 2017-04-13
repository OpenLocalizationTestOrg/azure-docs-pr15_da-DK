<properties
    pageTitle="Adgangskontrol til ressourcer med Azure Active Directory-grupper | Microsoft Azure"
    description="Hvordan du kan bruge grupper i Azure Active Directory til at administrere brugeradgang til i det lokale miljø og skyen programmer og ressourcer."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""
/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="curtand"/>


# <a name="managing-access-to-resources-with-azure-active-directory-groups"></a>Adgangskontrol til ressourcer med Azure Active Directory-grupper

Azure Active Directory (Azure AD) er en omfattende identitets- og management-løsning, der indeholder et solidt sæt af funktioner til at administrere adgang til i det lokale miljø og skyen programmer og ressourcer, herunder Microsoft online services som Office 365 og en verden over Microsoft SaaS programmer. I denne artikel indeholder en oversigt, men hvis du vil begynde at bruge Azure AD grupper lige nu, skal du følge instruktionerne i [administrere sikkerhedsgrupper i Azure AD](active-directory-accessmanagement-manage-groups.md). Hvis du vil se, hvordan du kan bruge PowerShell til at administrere grupper i Azure Active directory kan du læse mere i [Azure Active Directory preview-cmdletter til gruppeadministration af](active-directory-accessmanagement-groups-settings-v2-cmdlets.md).


> [AZURE.NOTE] Hvis du vil bruge Azure Active Directory, skal du en Azure-konto. Hvis du ikke har en konto, kan du [tilmelde dig en gratis Azure-konto](https://azure.microsoft.com/pricing/free-trial/).


I Azure AD er en af de vigtigste funktioner muligheden for at administrere adgang til ressourcer. Disse ressourcer kan være en del af mappen, som er tilfældet med tilladelser til at administrere objekter gennem roller i den mappe eller ressourcer, som er ekstern i forhold til mappen, som SaaS programmer, Azure services og SharePoint-websteder eller på en lokal ressourcer. Der er fire måder, som en bruger kan være tildelt adgangsrettigheder til en ressource:


1. Direkte tildeling

    Brugere kan tildeles direkte til en ressource af ejeren af ressourcen.

2. Gruppemedlemskab

    En gruppe kan være tildelt en ressource ressource ejeren af og ved at gøre dette, give medlemmer af denne gruppeadgang til ressourcen. Medlemskab af gruppen kan derefter administreres af ejeren af gruppen. Ejeren af ressource uddelegerer effektivt, tilladelse til at tildele brugere til deres ressource til ejeren af gruppen.

3. Regel-baseret

    Ejeren af ressource kan bruge en regel til at udtrykke, hvilke brugere skal tildeles adgang til en ressource. Resultatet af reglen, der er afhængig af de attributter, der bruges i denne regel og deres værdier for bestemte brugere, og gør det, uddelegerer ejeren af ressource effektivt rettigheder til at administrere adgang til deres ressource til autoritative kilden for de attributter, der bruges i reglen. Ejeren af ressource stadig administrerer selve reglen og bestemmer, hvilke attributter og værdier giver adgang til deres ressource.

4. Eksternt nøglecenter

    Adgang til en ressource er udledt fra en ekstern kilde en gruppe, der er synkroniseret fra en autoritative kilde som en lokal adresseliste eller en SaaS app som arbejdsdag. Ejeren af ressourcen tildeles gruppen for at give adgang til ressourcen og den eksterne kilde administrerer medlemmerne af gruppen.

  ![Oversigt over access management diagram](./media/active-directory-access-management-groups/access-management-overview.png)


## <a name="watch-a-video-that-explains-access-management"></a>Se en video, der forklarer, access administration

Du kan se en kort video, der forklarer mere om dette:

**Azure AD: Introduktion til dynamiske medlemskab for grupper**

> [AZURE.VIDEO azure-ad--introduction-to-dynamic-memberships-for-groups]

## <a name="how-does-access-management-in-azure-active-directory-work"></a>Hvordan har adgang til administration af i Azure Active Directory arbejde?
Access management-løsning er midt i Azure AD sikkerhedsgruppen. Brug af en sikkerhedsgruppe til at administrere adgang til ressourcer er et kendt paradigme, som giver mulighed for en fleksibel og nemt forstået metode til at give adgang til en ressource til de tilsigtede gruppe af brugere. Ejeren af ressource (eller administratoren af mappen) kan tildele en gruppe for at angive en bestemt adgang direkte til de ressourcer, de ejer. Medlemmer af gruppen får adgang til, og ejeren af ressource kan uddelegere rettigheder til at administrere listen medlemmer af en gruppe til en anden, som en afdeling chef eller en administrator for helpdesk.

![Azure Active Directory access management-diagram](./media/active-directory-access-management-groups/active-directory-access-management-works.png)

Ejeren af en gruppe kan også fremlægge gruppen selvbetjening anmodninger om. I at gøre dette, kan en slutbruger søge efter og finde gruppen og gør en anmodning om at deltage i, effektivt ønsker tilladelse til at få adgang til de ressourcer, der administreres til gruppen. Ejeren af gruppen kan konfigurere gruppen, så joinforbindelse anmodninger godkendes automatisk eller kræve godkendelse af ejeren af gruppen. Når en bruger foretager en anmodning om at deltage i en gruppe, er anmodning om at deltage videresendt til ejerne af gruppen. Hvis et af ejerne godkender anmodningen, får besked om brugeren, og brugeren er optaget i gruppen. Hvis et af ejerne afviser anmodningen, er brugeren besked, men ikke er medlem af gruppen.


## <a name="getting-started-with-access-management"></a>Introduktion til access management
Klar til at komme i gang? Du skal prøve nogle af de grundlæggende opgaver, du kan gøre med Azure AD-grupper. Brug disse funktioner til at give specialiserede adgang til forskellige grupper af personer til forskellige ressourcer i din organisation. En liste over første grundlæggende trin vises nedenfor.

* [Oprette en simpel regel for at konfigurere dynamiske medlemskaber for en gruppe](active-directory-accessmanagement-manage-groups.md#how-can-i-manage-the-membership-of-a-group-dynamically)

* [Brug af en gruppe til at administrere adgang til SaaS programmer](active-directory-accessmanagement-group-saasapps.md)

* [Gøre en gruppe tilgængelige for slutbruger selvbetjening](active-directory-accessmanagement-self-service-group-management.md)

* [Synkronisere en lokal gruppe til Azure ved hjælp af Azure AD-forbindelse](active-directory-aadconnect.md)

* [Administrere ejere for en gruppe](active-directory-accessmanagement-managing-group-owners.md)


## <a name="next-steps-for-access-management"></a>Næste trin til at administrere adgang
Nu hvor du har forstået de grundlæggende regler for administration af access, er her nogle flere avancerede funktioner tilgængelige i Azure Active Directory til administration af adgang til dine programmer og ressourcer.

* [Oprette avancerede regler ved hjælp af attributter](active-directory-accessmanagement-groups-with-advanced-rules.md)

* [Administration af sikkerhedsgrupper i Azure AD](active-directory-accessmanagement-manage-groups.md)

* [Opsætning af dedikeret grupper i Azure AD](active-directory-accessmanagement-dedicated-groups.md)

* [Graph API reference til grupper](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#GroupFunctions)

* [Azure Active Directory-cmdletter til konfiguration af gruppeindstillinger](active-directory-accessmanagement-groups-settings-cmdlets.md)
