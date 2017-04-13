<properties
   pageTitle="Test din løsning skabelon tilbud på Marketplace | Microsoft Azure"
   description="Forstå, hvordan du kan teste din løsning skabelon tilbud på Azure Marketplace."
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
   ms.date="12/04/2015"
   ms.author="hascipio; v-divte" />

# <a name="test-your-solution-template-offer-in-staging"></a>Teste din løsning skabelon tilbud i midlertidige
Midlertidige betyder, at implementere dit tilbud i en privat "sandkassetilstand", hvor du kan teste og bekræfte funktionaliteten før skubbe fremstilling. Tilbuddet vises i arrangere samme måde som det ville gøre til en kunde, der har installeret den. Dit tilbud skal være bekræftet kan installeres midlertidige.

Når tilbuddet er midlertidigt, kan du få vist og teste tilbuddet i [Azure-portalen](https://portal.azure.com/).

Følg nedenstående trin for at push dit tilbud om midlertidige og tester den på [Azure-portalen](https://portal.azure.com/):

1.  Gå til [Udgivelsesportal](https://publish.windowsazure.com) > fanen**Løsning skabeloner** > dit tilbud > **Publicer** > **Push til midlertidige**.
2.  Angiv på listen over Azure abonnementer, du vil bruge til at få vist og teste dit tilbud.
3.  Log på portalen Azure preview ved hjælp af det abonnement-ID, du har brugt i det forrige trin.
4.  Foretage mindst én runde på test i portalen Azure preview på de punkter, der er nævnt nedenfor:
  - Sørg for, at marketingindhold vises korrekt i Azure Marketplace.
  - Til slut installation af topologien.
  - Udføre test af ydeevne og test af belastning.
  - Sørg for, at din topologi overholder de bedste fremgangsmåder.

## <a name="next-steps"></a>Næste trin
Hvis du er tilfreds med resultatet, og derefter kan du fortsætte til den endelige tilbud publicering fase, **trin 4**: [anvender dit tilbud til Marketplace](marketplace-publishing-push-to-production.md). Ellers skal foretage ændringer i dit tilbud og anmode om certificering igen.

> [AZURE.NOTE] For marketing indholdsændringer-certificering er ikke påkrævet.

Se [Introduktion: hvordan du publicerer et tilbud til Azure Marketplace](marketplace-publishing-getting-started.md) for en guide til alle publisher-opgaver.
