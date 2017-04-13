<properties
    pageTitle="Hvordan du kan installere en webtjeneste til flere områder | Microsoft Azure"
    description="Trin til at installere (kopi) en ny webtjeneste til andre områder."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondl"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="v-donglo"/>

# <a name="how-to-deploy-a-web-service-to-multiple-regions"></a>Hvordan du kan installere en webtjeneste til flere områder

De nye Azure-webtjenester kan du let at implementere en webtjeneste til flere områder uden brug af flere abonnementer eller arbejdsområder. 

Priser er område, der er specifikke derfor skal du angive en fakturering plan for hvert område, hvor du vil installere webtjenesten.

## <a name="to-create-a-plan-in-another-region"></a>Oprette en plan i et andet område

1. Log på [Microsoft Azure Machine Learning webtjenester](https://services.azureml.net/).
2. Klik på menupunktet **planer** .
3. Plan over på visningssiden, klik på **Ny**.
4. Vælg det abonnement, hvor den nye plan er placeret rullelisten **abonnement** .
5. Vælge et område til den nye plan rullelisten **område** . Planindstillinger for det valgte område vises i afsnittet **Planlægge indstillinger** på siden.
6. Vælg en ressourcegruppe for plan rullelisten **Ressourcegruppe** . Fra flere oplysninger om ressourcegrupper, skal du se [administrere Azure ressourcer via portal](../azure-portal/resource-group-portal.md).
7. Skriv navnet på planen i **Plannavnet** .
8. Klik på niveauet for fakturering for den nye plan under **Planindstillinger**.
9. Klik på **Opret**.


## <a name="deploying-the-web-service-to-another-region"></a>Implementering af webtjenesten til et andet område

1. Klik på menupunktet **Webtjenester** .
2. Vælg den Web-tjeneste, du installerer til et nyt område.
3. Klik på **Kopiér**.
4. Skriv et nyt navn til webtjenesten i **Navn på websted**.
5. Skriv en beskrivelse til webtjenesten i **Beskrivelse af webtjenesten**.
6. Vælg det abonnement, hvor den nye webtjeneste er placeret rullelisten **abonnement** .
7. Vælg en ressourcegruppe for webtjenesten rullelisten **Ressourcegruppe** . Fra flere oplysninger om ressourcegrupper, skal du se [administrere Azure ressourcer via portal](../azure-portal/resource-group-portal.md).
8. Vælg det område, hvor du kan installere webtjenesten rullelisten **område** .
9. Vælge en lagerplads konto i hvor du vil gemme webtjenesten rullelisten **lagerplads konto** .
10. Vælg en plan i det område, du valgte i trin 8 rullelisten **Pris Plan** .
11. Klik på **Kopiér**.

