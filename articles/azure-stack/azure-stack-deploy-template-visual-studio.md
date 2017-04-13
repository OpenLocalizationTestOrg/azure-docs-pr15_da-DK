<properties
    pageTitle="Installere skabeloner med Visual Studio Azure stablede | Microsoft Azure"
    description="Lær at implementere skabeloner med Visual Studio Azure stablede."
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="helaw"/>

# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Installere skabeloner Azure stablede ved hjælp af Visual Studio

Brug af Visual Studio skal installeres Azure ressourcestyring skabeloner til Azure stak Konceptet.

Ressourcestyring skabeloner implementere og klargøre alle ressourcerne for dit program i en enkelt, koordineret handling.

1.  Åbne Visual Studio 2015 opdatering 1.

2.  Klik på **filer**, klik på **Ny**, og klik på **Azure ressourcegruppe**i dialogboksen **Nyt projekt** .

3.  Angiv et **navn** til det nye projekt, og klik derefter på **OK**.

4.  Klik på **Windows Virtual Machine**i dialogboksen **Vælg Azure skabelon** , og klik derefter på **OK**.

  I det nye projekt, kan du se en liste over de tilgængelige skabeloner ved at udvide noden **skabeloner** i ruden **Solution Explorer** .

5.  Højreklik på navnet på dit projekt i ruden **Solution Explorer** , klik på **Implementer**og derefter klikke på **Ny installation**.

6.  Vælg abonnementet Microsoft Azure stak i dialogboksen **Installer til ressourcegruppe** i rullemenuen **abonnement** .

7.  Vælge en eksisterende ressourcegruppe på listen **Ressourcegruppe** eller oprette en ny.

8.  Vælg en placering på listen **ressource gruppe placering** , og klik derefter på **Installer**.

9.  Angiv værdier for parametre (som varierer alt efter skabelon) i dialogboksen **Rediger parametre** , og klik derefter på **Gem**.

## <a name="next-steps"></a>Næste trin

[Installere skabeloner med kommandolinjen](azure-stack-deploy-template-command-line.md)
