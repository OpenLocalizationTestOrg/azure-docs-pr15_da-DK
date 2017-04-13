<properties 
   pageTitle="Administrere Azure ressourcer med skyen Explorer | Microsoft Azure"
   description="Lær at bruge skyen Stifinder til at gennemse og administrere Azure ressourcer i Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="managing-azure-resources-with-cloud-explorer"></a>Administrere Azure ressourcer i skyen Stifinder

##<a name="overview"></a>Oversigt

Skyen Explorer er designet til at du hurtigt og nemt gennemse og administrere dine Azure ressourcer i Visual Studio IDE. Du kan, for eksempel bruge det til at åbne en WebApp, [Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) eller i en browser, eller knytte fejlfinding, eller du kan få vist egenskaberne for en blob objektbeholder og åbne den i Blob objektbeholder Editor.

Skyen Explorer er bygget på Azure ressource manager stablen, ligesom [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040). Det forstår ressourcer som Azure ressourcegrupper og Azure tjenester som logik apps og API apps, og den understøtter [Rollebaseret adgangskontrol](./active-directory/role-based-access-control-configure.md) (RBAC). Vælg knappen **Opdater** på værktøjslinjen i skyen Explorer for at få vist Azure ressourcer, der er blevet tilføjet eller ændret.

Skyen Explorer installeres som en del af Visual Studio Tools til Azure SDK 2.7. 

## <a name="prerequisites"></a>Forudsætninger

- Visual Studio 2015 RTM.

- Visual Studio-værktøjer til Azure SDK. 
- Du skal også have en Azure-konto og er logget ind på den for at få vist Azure ressourcer i skyen Explorer. Hvis du ikke har en, kan du oprette en konto på blot et par minutter. Hvis du har et MSDN-abonnement, skal du se [Azure fordel for MSDN-abonnenter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Ellers skal du se [oprette en gratis prøveversion konto](https://azure.microsoft.com/pricing/free-trial/).

- Hvis skyen Explorer ikke er synlig, kan du se den ved at vælge **Vis** **Andre Windows** **Explorer skyen** på menulinjen.

## <a name="manage-azure-accounts-and-subscriptions"></a>Administrere Azure konti og abonnementer

For at få vist dine Azure ressourcer i skyen Explorer, skal du logge på en Azure-konto med en eller flere aktive abonnementer. Hvis du har mere end én Azure-konto, kan du tilføje dem i skyen Stifinder og derefter vælge de abonnementer, du vil medtage i skyen Explorer ressourcevisning.

Hvis du ikke har brugt Azure før, eller du ikke har tilføjet de nødvendige konti til Visual Studio, bliver du bedt om at gøre dette.

## <a name="to-add-azure-accounts-to-cloud-explorer"></a>Tilføje Azure-konti til skyen Explorer

1. Vælg ikonet indstillinger på værktøjslinjen i skyen Explorer.

1. Vælg linket **Tilføj en konto** . Log på Azure-konto, hvis du vil gennemse ressourcer. Den konto, du lige har tilføjet skal være markeret på rullelisten konto datovælger. Abonnementer for den pågældende konto vises under kontopost.

    ![Tilføje Azure-abonnementer](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819514.png)

    ![Vælge Azure-abonnementer](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819515.png)

1. Markér afkrydsningsfelterne for de konto-abonnementer, du vil gennemse og derefter vælge knappen **Anvend** på.

    Azure ressourcerne for de markerede abonnementer vises i skyen Stifinder.

## <a name="to-remove-an-azure-account"></a>Fjerne en Azure-konto

1. Vælg **filer**, **Kontoindstillinger** på menulinjen.

1. Vælg kommandoen **Remove** ud for kontoen, du vil fjerne, i afsnittet **Alle konti** i dialogboksen **Kontoindstillinger** . Bemærk, at denne kommando kun fjerner kontoen fra Visual Studio-it påvirker ikke kontoen Azure sig selv.

## <a name="view-resource-types-or-groups"></a>Få vist ressourcetyper eller grupper

For at få vist dine Azure ressourcer, kan du vælge mellem **Ressourcetyper** eller **Ressourcegrupper** visning.

![Ressource visning på rullelisten](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819516.png)

- **Ressourcetyper** visning, som er også den almindelige visning, der bruges i [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040), viser dine Azure ressourcer, der er kategoriseret efter type, som Onlines, lagerplads konti og virtuelle computere. Dette minder til hvordan Azure ressourcer, der vises i Server Explorer.

- Grupper ressourcevisning kategoriserer Azure ressourcer efter de er tilknyttet Azure ressourcegruppen.

 
    En ressourcegruppe er en samlet Azure ressourcer, der typisk bruges af et bestemt program. Hvis du vil vide mere om Azure ressourcegrupper, skal du se [Oversigt over Azure ressourcestyring](./resource-group-overview.md).

## <a name="view-and-navigate-resources"></a>Få vist og navigere ressourcer

Udvid elementets type eller tilknyttede ressourcegruppe for at gå til en Azure ressource og få vist dets oplysninger i skyen Explorer, og klik på ressourcen. Når du vælger en ressource, vises oplysninger i de to faner nederst i skyen Explorer.

![Vælg en ressourcevisning](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819517.png)

- Fanen **Handlinger** viser de handlinger, du kan udføre i skyen Explorer for den valgte ressource. Du kan også se tilgængelige handlinger i genvejsmenuen ved en ressource.

- Fanen **Egenskaber** viser egenskaberne for ressourcen, som den type, landestandard og ressource gruppe, som det er tilknyttet.

Hver ressource har handlingen, der er **åben i portalen**. Når du vælger denne handling, viser skyen Explorer den valgte ressource i [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040). Denne funktion er særligt nyttige til at navigere til dybt indlejrede ressourcer.

Flere handlinger og egenskabsværdier kan også blive vist baseret på Azure ressourcen. For eksempel have webapps og logik apps også handlingerne, **Åbn i browser** , og **Vedhæft fejlfindingen** ud over **åben i portalen**. Handlinger til at åbne redaktører vises, når du vælger en lagerplads konto blob, kø eller tabel. Azure apps har **URL-adresse** og **Status** egenskaber, mens lagerplads ressourcer har nøgle og forbindelse strengegenskaber.

## <a name="search-resources"></a>Søg ressourcer

Skriv navnet i feltet Søg i skyen Explorer for at finde ressourcer med et bestemt navn i dine abonnementer på Azure-konto.

![Søge efter ressourcer i skyen Explorer](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC820394.png)

Når du indtaster tegn i feltet Søg, vises kun de ressourcer, der svarer til de pågældende tegn i træet ressource.

