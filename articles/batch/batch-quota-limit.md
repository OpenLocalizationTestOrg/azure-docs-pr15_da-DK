<properties
    pageTitle="Batch kvoter for tjenesten og begrænsninger for | Microsoft Azure"
    description="Få mere at vide om standard Azure batchen kvoter, grænser og begrænsninger, og hvordan du anmoder om kvote øges"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/10/2016"
    ms.author="marsma"/>

# <a name="quotas-and-limits-for-the-azure-batch-service"></a>Kvotaer og begrænsninger for tjenesten Azure batchen

Som med andre Azure tjenester, der er begrænsninger på bestemte ressourcer, der er knyttet til tjenesten batchen. Mange af disse begrænsninger er standard kvoter anvendt af Azure-abonnement eller kontoniveau. I denne artikel beskrives standardindstillingerne, og hvordan du kan anmode om kvote øges.

Hvis du vil køre fremstilling arbejdsmængder i batchen, skal du kan øge en eller flere af kvoter over standard. Hvis du vil hæve en kvote, kan du åbne en online [kundesupport anmodning om](#increase-a-quota) gratis.

>[AZURE.NOTE] En kvote er en kreditkontrol grænse, ikke en kapacitet garanti. Hvis du har kapacitetsbehov for store, skal du kontakte Azure support.

## <a name="subscription-quotas"></a>Abonnement kvoter
**Ressource**|**Standardgrænse**|**Højeste antal**
---|---|---
Batchen konti per område per abonnement | 1 | 50

## <a name="batch-account-quotas"></a>Batchen konto kvoter
[AZURE.INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="other-limits"></a>Andre begrænsninger
**Ressource**|**Højeste antal**
---|---
[Samtidige opgaver](batch-parallel-node-tasks.md) per beregningsnode | 4 x antal node kerner
[Programmer](batch-application-packages.md) per batchen konto        | 20
Programpakker per program  | 40
Programmet pakke størrelse (hver)       | Hukommelse 195GB<sup>1</sup>

<sup>1</sup> azure lagergrænsen for maksimale blokken blob størrelse

## <a name="view-batch-quotas"></a>Få vist batchen kvoter

Få vist din konto kvoter for batchen i [Azure portal][portal].

1. Markér **batchen konti** i portalen og derefter vælge den Batch-konto, du er interesseret i.

2. Vælg **Egenskaber** på kontoen batchen menuen blade

3. Bladet egenskaber viser **kvoter** , der aktuelt anvendes i batchen konto

    ![Batchen konto kvoter][account_quotas]

## <a name="increase-a-quota"></a>Øge en kvote

Følg nedenstående trin for at anmode om en kvote øge ved hjælp af [Azure portal][portal].

1. Vælg feltet **Hjælp + support** på dashboardet portalen eller spørgsmålstegn (**?**) i øverste højre hjørne af portalen.

2. Vælg **Ny understøtter anmodning om** > **grundlæggende funktioner**.

3. På bladet **grundlæggende** :

    en. **Problemtype** > **kvote**

    b. Vælg dit abonnement.

    c. **Kvote type** > **batchen**

    d. **Understøtter plan** > **kvote support - inkluderet**

    Klik på **Næste**.

4. På bladet **problemet** :

    en. Vælg en **alvorlighed** ifølge din [business virkning][support_sev].

    b. Angiv hver kvote, du vil ændre, på batchen kontonavnet og den nye grænse i **Detaljer**.

    Klik på **Næste**.

5. På bladet **kontaktoplysninger** :

    en. Vælg en **foretrukken kontaktmetode**.

    b. Bekræft, og Angiv de nødvendige kontaktliste oplysninger.

    Klik på **Opret** for at sende anmodning om support.

Når du har indsendt din supportanmodning, vil Azure support vil kontakte dig. Bemærk, at fuldføre anmodningen kan tage op til 2 arbejdsdage.

## <a name="related-topics"></a>Relaterede emner

* [Oprette en Azure batchen konto ved hjælp af portalen Azure](batch-account-create-portal.md)

* [Azure batchen funktionsoversigt](batch-api-basics.md)

* [Azure-abonnement og begrænsninger for tjenesten, kvoter og begrænsninger](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG
