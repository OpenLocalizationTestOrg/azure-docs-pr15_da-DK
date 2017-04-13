<properties
   pageTitle="Oprette en Analysis Services-server i Azure | Microsoft Azure"
   description="Få mere at vide, hvordan du opretter en Analysis Services-forekomst i Azure."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="owend"/>

# <a name="create-an-analysis-services-server"></a>Oprette en Analysis Services-server
I denne artikel vejledes du gennem oprettelse af en ny Analysis Services-serverressource i abonnementet Azure.

## <a name="before-you-begin"></a>Inden du går i gang
For at komme i gang skal bruge du:

- **Azure-abonnement**: Besøg [Azure gratis prøveversion](https://azure.microsoft.com/offers/ms-azr-0044p/) til at oprette en konto.
- **Ressourcegruppe**: Brug af et ressourcegruppe, du allerede har eller [oprette en ny](../azure-resource-manager/resource-group-overview.md).

> [AZURE.NOTE] Oprette en Analysis Services-server, kan det resultere i en ny fakturerbar tjeneste. Se Analysis Services priser for at få mere for at vide.

## <a name="create-an-analysis-services-server"></a>Oprette en Analysis Services-server

1. Log på [Azure-portalen](https://portal.azure.com).

2. Klik på **+ Ny** > **Intelligence + analytics** > **Analysis Services**.

3. Udfyld de obligatoriske felter i bladet **Analysis Services** , og tryk derefter på **Opret**.

    ![Oprette server](./media/analysis-services-create-server/aas-create-server-blade.png)

    - **Servernavn**: Skriv et entydigt navn, der bruges til at referere til serveren.

    - **Abonnement**: Vælg denne server fakturerer til abonnementet.

    - **Ressourcegruppe**: Dette er beholdere, der er udviklet til at hjælpe dig med at administrere en samling af Azure ressourcer. Se [ressourcegrupper](../resource-group-overview.md)for at få mere for at vide.

    - **Placering**: denne Azure datacenter placering vært for serveren. Vælg en placering nærmeste din største bruger base.

    - **Priser niveau**: Vælg en priser niveau. Tabelmodeller op til 100 GB understøttes. Du kan altid ændre din priser niveau senere.

4. Klik på **Opret**.

Oprette tager normalt under et minut ofte blot et par sekunder. Hvis du har valgt **Føj til portalen**, skal du gå til din portal for at se din nye server. Eller gå til **flere tjenester** > **Analysis Services** til at se, om din server er klar. Hvis den ikke vises, kan du opdatere listen.

 ![Dashboard](./media/analysis-services-create-server/aas-create-server-dashboard.png)


## <a name="next-steps"></a>Næste trin
Når du har oprettet din server, kan du [installere en model](analysis-services-deploy.md) til den ved hjælp af SSDT eller med SSMS.

Hvis en model, du installerer til din server opretter forbindelse til lokale datakilder, skal du installere en [lokale datastyringsgateway](analysis-services-gateway.md) på en computer i dit netværk.
