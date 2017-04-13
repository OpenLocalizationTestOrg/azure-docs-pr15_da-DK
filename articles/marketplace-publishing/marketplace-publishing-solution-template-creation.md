<properties
   pageTitle="Vejledning i at oprette en løsning skabelon på Marketplace | Microsoft Azure"
   description="Detaljerede oplysninger om, hvordan du opretter, bekræfte og installerer en Multi-VM billede løsning skabelon til køb på Azure Marketplace."
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
      ms.date="07/27/2016"
      ms.author="hascipio; v-divte" />

# <a name="guide-to-create-a-solution-template-for-azure-marketplace"></a>Vejledning til at oprette en løsning skabelon til Azure Marketplace
Efter du har udført trin 1, [oprettelse af en konto og registrering][link-acct-creation], vi automatiseret du på oprettelse af en skabelon til Azure-kompatible løsning på [tekniske forudsætninger for oprettelse af en løsning skabelon](marketplace-publishing-solution-template-creation-prerequisites.md). Nu vi fører dig gennem trinnene til at oprette en løsning skabelon til flere FOS på [Udgivelsesportal] [ link-pubportal] på Azure Marketplace.

## <a name="create-your-solution-template-offer-in-the-publishing-portal"></a>Oprette din løsning skabelon tilbud i portalen publicering
Gå til [https://publish.windowsazure.com](http://publish.windowsazure.com). Når du logger på for første gang på [Udgivelsesportal](https://publish.windowsazure.com/), skal du benytte den samme konto, som virksomhedens sælgeren profil blev registreret. Senere, kan du tilføje en medarbejder i virksomheden som en co-administrator i portalen publicering.

### <a name="1-select-solution-templates"></a>1. Vælg "Løsning skabeloner"

  ![tegning][img-pubportal-menu-sol-templ]

### <a name="2-create-a-new-solution-template"></a>2. Opret en ny løsning skabelon

  ![tegning][img-pubportal-sol-templ-new]

### <a name="3-start-with-topologies"></a>3. start med topologier
En løsning skabelon er en "overordnet" på alle dens topologier. Du kan angive flere topologier i én tilbud/løsning skabelon. Når et tilbud rykkes til midlertidige, rykkes den med alle dens topologier. Følg nedenstående trin for at definere dine tilbud:     

- Oprette en topologi: "Topologi id" er typisk navnet på topologien for skabelonen løsning. Topologi-id bruges i URL-adressen, som vist nedenfor:

  Azure Marketplace: http://azure.microsoft.com/marketplace/partners/ {PublisherNamespace} / {OfferIdentifier} {TopologyIdentifier}

  Azure-portalen: https://portal.azure.com/#gallery/ {PublisherNamespace}. {OfferIdentifier} {TopologyIdentifier}

- Tilføje en ny version.

### <a name="4-get-your-topology-versions-certified"></a>4. få dine topologi versioner, der er certificeret
Overføre en zip-fil, der indeholder alle de filer, der er nødvendige for at klargøre den bestemte version af topologien. Denne zip-filen skal indeholde følgende:

- *mainTemplate.json* og *createUiDefinition.json* fil på dets rodmappe.
- En hvilken som helst sammenkædede skabeloner og alle de nødvendige scripts.

  > [AZURE.TIP] Mens udviklerne arbejder med oprettelse af løsningen skabelon topologier og få dem certificeret, virksomheden, kan marketing og/eller juridiske afdelinger i virksomheden arbejde på det marketing og juridisk indhold.

## <a name="next-steps"></a>Næste trin
Nu, hvor du har oprettet din løsning skabelon og overføres zip-filen, skal du følge vejledningen i [Marketplace marketing indhold vejledning](marketplace-publishing-push-to-staging.md) før skubbe tilbuddet til midlertidige. Hvis du vil se det samlede sæt af marketplace publicering af artikler, skal du besøge [Introduktion: hvordan du publicerer et tilbud til Azure Marketplace](marketplace-publishing-getting-started.md).

Du kan også være interesseret i disse relaterede artikler:

- VM billeder: [Om virtuelt billeder i Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)
- VM filtypenavne: [VM Agent og VM filtypenavne oversigt](https://msdn.microsoft.com/library/azure/dn832621.aspx) og [Azure VM Extensions og funktioner](https://msdn.microsoft.com/library/azure/dn606311.aspx)
- Azure ressourcestyring: [redigering Azure ARM skabeloner](../resource-group-authoring-templates.md) og [enkel ARM skabelon eksempler](https://github.com/rjmax/ArmExamples)
- Lagerplads konto throttles: [Sådan skærm til lagerplads konto (throttling)](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) og [Premium lager](../storage/storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage)

[img-pubportal-menu-sol-templ]:media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]:media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-pubportal]:https://publish.windowsazure.com
