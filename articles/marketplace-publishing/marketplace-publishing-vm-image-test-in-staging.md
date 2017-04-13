<properties
   pageTitle="Teste din VM tilbud på Marketplace | Microsoft Azure"
   description="Forstå, hvordan du tester VM billedet på Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/01/2016"
   ms.author="hascipio" />

# <a name="test-your-vm-offer-for-the-azure-marketplace-in-staging"></a>Teste din VM tilbud på Azure Marketplace i midlertidige

Midlertidige betyder, at implementere din SKU i en privat "sandkassetilstand", hvor du kan teste og validere funktionaliteten før du anvender til Marketplace. SKU vises i arrangere samme måde som det ville gøre til en kunde, der har installeret den. VM billedet skal være bekræftet kan installeres midlertidige.

## <a name="step-1-push-your-offer-to-staging"></a>Trin 1: Push dit tilbud om midlertidige

1. Klik på **Push til midlertidige**under fanen **Publicer** .

    ![tegning](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)

2. Hvis den Udgivelsesportal får du besked om eventuelle fejl, rette dem.
3.  I den **der kan få adgang til din faseinddelt tilbud?** dialogboksen skal du angive listen over Azure abonnementer, du vil bruge til at få vist dit tilbud i [Azure preview-portalen](https://portal.azure.com).

    >[AZURE.NOTE] I tilfælde af virtuelle maskiner og løsning skabeloner, skal du **ikke gør** whitelist abonnementer af typen Konferencetjenesteudbyderen DreamSpark eller Azure i Åbn.


    > Når du klikker på knappen **PUSH til midlertidige**, udføres følgende trin i tilfælde af virtuelle maskiner, bag scenen. Du kan få vist status for hvert trin under fanen PUBLICER i udgivelse portal. Du skal tjekke denne side til almindelig interval (indtil status viser FASEINDDELT) for eventuelle manglende oplysninger, som skal rettelse fra din ende.

    > - I første omgang går midlertidige anmodningen til den gruppe, certificering der validere den virtuelle harddisk. Men hvis din anmodning har konfigureret kun marketing ændring, derefter trinnet certificering er ignoreret.
    > - Når certificering er fuldført, skal begynde replikering af tilbuddet på tværs af alle Azure datacentre. Det normalt tager 24-48hours for gentagelse til at fuldføre, men du kan gå op til en uge, afhængigt af størrelsen på den virtuelle harddisk. Hvis din anmodning har konfigureret kun marketing ændring, derefter er replikering dog hurtigere.
    > - Når replikering er fuldført, vil tilbuddet være tilgængelige i [Azure-portalen](http:/portal.azure.com). Blive midlertidigt at, der status i udgivelse portal. En faseinddelt tilbud er synligt i [Azure portal](http:/portal.azure.com) kun ved hjælp af de mail-id'er, der er knyttet til abonnementet, som er midlertidigt gemt tilbuddet.

4. Log på [Azure Gennemse portal](https://portal.azure.com) ved hjælp af de Azure abonnementer, der er angivet i det forrige trin.
5. Find dit tilbud og validere dine VM billede punkter:
  - Sørg for, at marketingindhold vises korrekt på markedet.
  - Til slut installation af VM billedet.

      ![IMG-kort-portal](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

> [AZURE.IMPORTANT] Dit tilbud forbliver i midlertidigt, indtil du kontakte Microsoft via portalen publicering [under fanen**Publicer** > Klik på knappen **"anmode om godkendelse til opslagsnål til fremstilling"**], du er klar til at overføre til fremstilling. Dette er en ideel tid at have alle medlemmer af dit team kontrol over alt i forberedelse til dit tilbud, der skal vises.

> Den midlertidige platform er udviklet til test tilbuddet i en eksempelvisning af udgiveren. Vi ikke på det kraftigste skal bruge denne platofrm til pågældendes handels formål.

## <a name="next-steps"></a>Næste trin
Nu, hvor dit tilbud er "midlertidigt", og du har testet dens funktionalitet og marketingindhold, kan du fortsætte til den sidste publicering fase, **trin 4**: [anvender dit tilbud til Marketplace](marketplace-publishing-push-to-production.md).

## <a name="see-also"></a>Se også
- [Introduktion: hvordan du publicerer et tilbud til Azure Marketplace](marketplace-publishing-getting-started.md)
