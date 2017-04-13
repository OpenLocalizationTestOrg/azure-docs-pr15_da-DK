<properties
    pageTitle="Retningslinjer for konti og abonnement | Microsoft Azure"
    description="Få mere at vide om de vigtigste design og implementering retningslinjer for abonnementer og -konti på Azure."
    documentationCenter=""
    services="virtual-machines-linux"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="subscription-and-accounts-guidelines"></a>Retningslinjer for abonnement og -konti

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

I denne artikel fokuserer på forstå, hvordan du kan løse abonnement og konto administration som dit miljø og bruger base vokser.


## <a name="implementation-guidelines-for-subscriptions-and-accounts"></a>Implementering retningslinjer for abonnementer og -konti

Beslutninger:

- Hvilke sæt abonnementer og konti gør skal være vært for din IT arbejdsbelastningen eller infrastruktur?
- Hvordan du kan opdele hierarkiet for at tilpasse din organisation?

Opgaver:

- Definere dine logiske organisationshierarki, som du vil have til at administrere fra et abonnementsniveau.
- For at matche denne logiske hierarki skal du Definer de konti, der er påkrævet og abonnementer under hver konto.
- Oprette sættet af abonnementer og firmaer ved hjælp af din navngivningskonvention.


## <a name="subscriptions-and-accounts"></a>Abonnementer og -konti

Hvis du vil arbejde med Azure, skal du en eller flere Azure abonnementer. Ressourcer som virtuelle maskiner (VM'er) eller virtuelle netværk findes i disse abonnementer.

- Enterprise-kunder har typisk en Enterprise tilmelding, som er den øverste ressource i hierarkiet, og der er knyttet til en eller flere konti.
- Den øverste ressource er forbrugere og kunder uden en Enterprise tilmelding kontoen.
- Abonnementer er knyttet til konti, og der kan være en eller flere abonnementer hver konto. Azure poster faktureringsoplysninger på niveauet for abonnement.

På grund af grænsen på to niveauer på konto/abonnement relationen er det vigtigt at justere navnekonventionen af konti og abonnementer til fakturering behovet. Eksempelvis hvis en global virksomhed bruger Azure, de kan vælge at have en konto i område, og abonnementer administrerede på områdeniveauet:

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub01.png)

For eksempel kan du bruge følgende struktur:

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub02.png)

Hvis et område beslutter at have mere end ét abonnement, der er knyttet til en bestemt gruppe, skal navnekonventionen inkorporere en metode til at kode ekstra dataene på kontoen eller på abonnementets navn. Denne organisation tillader massaging fakturering data for at generere de nye niveauer i hierarkiet under fakturering rapporter:

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub03.png)

Organisationen kan se ud som følgende:

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub04.png)

Vi giver detaljerede fakturering via en fil til en enkelt konto eller for alle konti i en enterprise agreement.


## <a name="next-steps"></a>Næste trin

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 