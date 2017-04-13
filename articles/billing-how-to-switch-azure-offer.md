<properties
    pageTitle="Skifte dit Azure abonnement til en anden tilbud | Microsoft Azure"
    description="Få mere at vide om at ændre dit Azure-abonnement og skifte til et andet tilbud ved hjælp af portalen abonnement administration"
    services=""
    documentationCenter=""
    authors="genlin"
    manager="mbaldwin"
    editor=""
    tags="billing,top-support-issue"/>

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="genli"/>

# <a name="switch-your-azure-subscription-to-another-offer"></a>Skifte dit Azure abonnement til en anden tilbud

Som en [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) -kunde, kan du muligvis skifte abonnementet Azure til en anden tilbud i [Konto Center](https://account.windowsazure.com/Subscriptions). For eksempel kan du bruge denne funktion til at kunne udnytte [månedlige kredit til Visual Studio abonnenter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Hvis du bruger [Gratis prøveversion](https://azure.microsoft.com/free/), kan du se Sådan [opgraderer du til Pay-As-You-Go](billing-upgrade-azure-subscription.md).

#### <a name="whats-supported"></a>Dette understøttes:

| Fra                                                              | Til                                                                                      |
|-------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Pay-As-You-Go Udviklingscenter/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)              |
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)          |
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)     |
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) | [MSDN platforme](https://azure.microsoft.com/offers/ms-azr-0062p/)                      |
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)            |
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Enterprise (Bizspark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |

> [AZURE.NOTE] Tilbyde ændringer, skal [du kontakte support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)til andre.
    
## <a name="switch-subscription-offer"></a>Skift abonnementstilbud

> [AZURE.VIDEO switch-to-a-different-azure-offer]

1.  Log på [Azure konto Center](https://account.windowsazure.com/Subscriptions).

2.  Vælg dit overkommelige abonnement.

3.  Klik på **Skift til et andet tilbud**. Knappen er kun tilgængelig, hvis du er på Pay-As-You-Go og fra hånden med din første faktureringsperiode.

    ![Bemærk knappen Skift tilbud i højre side af siden](./media/billing-how-to-switch-azure-offer/switchbutton.png)
    
4.  **Vælg det ønskede tilbud** på listen over tilbud dit abonnement, kan skiftes til. Denne liste varierer afhængigt af medlemskaberne, som er tilknyttet din konto. Hvis intet er tilgængelig, kan du kontrollere [listen over tilgængelige tilbud, du kan skifte til](#whats-supported) , og Sørg for, at du har de rigtige medlemskab. 

    ![Vælg et tilbud, du vil skifte til](./media/billing-how-to-switch-azure-offer/selectoffer.png)

5.  Afhængigt af de tilbud, du vil skifte til, kan du se en bemærkning om virkningerne af at skifte. Gennemgå denne liste omhyggeligt, og følg vejledningen, før du fortsætter.

    ![Gennemse noter](./media/billing-how-to-switch-azure-offer/thingstonote.png)

6.  Du kan omdøbe dit abonnement. Som standard angive vi den til det nye navn i tilbuddet. Klik på **Skift tilbyder** for at fuldføre processen.

    ![Klik på den grønne knap](./media/billing-how-to-switch-azure-offer/confirmpage.png)

7.  Succes! Dit abonnement er nu skiftet til det nye tilbud.

## <a name="why-cant-i-switch-offers"></a>Hvorfor kan jeg ikke skifte tilbud?

Du kan ikke se **skifte til et andet tilbud** , hvis:

- Du er ikke på [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/). I øjeblikket kun Pay-As-You-Go abonnementer kan skiftes til en anden tilbud.

    - Hvis du bruger [Gratis prøveversion](https://azure.microsoft.com/free/), kan du se Sådan [opgraderer du til Pay-As-You-Go](billing-upgrade-azure-subscription.md).

    - For at skifte tilbud fra et andet abonnement, kan du [kontakte support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- Du er stadig på din første faktureringsperiode Du skal vente på din første faktureringsperiode du afslutte, før du kan skifte tilbud.

Du kan få vist **der er ingen tilbud, der er tilgængelige i dit område eller land på nuværende tidspunkt** Hvis:

- Du er ikke berettiget til en hvilken som helst tilbud parametre. Se på [listen over tilgængelige tilbud, du kan skifte til](#whats-supported).

## <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>Hvad betyder skifte Azure tilbud gør til min tjeneste og fakturering?

Her er nogle oplysninger om hvad sker der, når du skifter Azure planer i konto Center.

### <a name="access-to-services"></a>Adgang til tjenester

Der er ingen service nedetid for alle brugere, der er knyttet til abonnementet. Dog kan du skifte til tilbuddet har begrænsninger. For eksempel forhindre nogle tilbud daglig drift, så du har brug at flytte ressourcer til et andet abonnement.

### <a name="billing"></a>Fakturering

Den dag, du har skiftet, der oprettes en faktura for alle udestående gebyrer. Derefter er dit abonnement faktureret i det nye tilbud priser udtryk. Dit abonnement fakturering Mærkedag ændres til den dato, hvor du har ændret tilbud. Brugen og fakturering data, før ændringen tilbud ikke bevares, så vi anbefaler, at du hente en kopi, inden du skifter.

> [AZURE.NOTE] På grund af fakturering beslægtede begrænsninger,-tilbud parametre er ikke muligt i den første faktureringscyklussen når du har oprettet et abonnement.

## <a name="can-i-migrate-from-pay-as-you-go-to-cloud-solution-providerhttpspartnermicrosoftcomsolutionscloud-reseller-overview-csp-or-enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement-ea"></a>Kan jeg overføre fra Pay-As-You-Go til [Skyen løsningsudbyder](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) eller [Virksomhedsaftale](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA)?

Vi understøtter ikke tilbud Skift til CSP eller EA i konti Center. Hvis du vil flytte dit eksisterende abonnement til EA, skal du bede din administrator om tilmelding tilføje kontoen i EA. Derefter skal modtager du en invitation via mail. Når du har fulgt vejledningen for at acceptere invitationen, flyttes dine abonnementer automatisk under Enterprise Agreement. Hvis du vil overføre til CSP, se [Azure abonnement overførsel til CSP](https://blogs.technet.microsoft.com/hybridcloudbp/2016/08/26/azure-subscription-migration-to-csp/).

## <a name="next-steps"></a>Næste trin

- Lær, hvordan du kan [administrere administratorroller](billing-add-change-azure-subscription-administrator.md) for dit abonnement

- Spore din brug ved at [hente data om brug af og faktura](billing-download-azure-invoice-daily-usage-date.md)

## <a name="need-help-contact-support"></a>Brug for hjælp? Kontakte support.

Hvis du stadig har yderligere spørgsmål, skal du [kontakte support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) for at få dit problem løses hurtigt.
