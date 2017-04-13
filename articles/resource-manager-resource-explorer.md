<properties
   pageTitle="Azure ressource Explorer | Microsoft Azure"
   description="I denne artikel beskrives Azure ressource Explorer, og hvordan det kan bruges til at få vist og opdatere installationer via Azure ressourcestyring"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="stuartleeks"
   manager="ankodu"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/01/2016"
   ms.author="stuartle;tomfitz"/>

# <a name="use-azure-resource-explorer-to-view-and-modify-resources"></a>Brug Azure ressource Stifinder til at få vist og rediger ressourcer
[Azure ressource Explorer](https://resources.azure.com) er et godt værktøj til studere ressourcer, som du allerede har oprettet i dit abonnement. Ved hjælp af dette værktøj, kan du forstå, hvordan ressourcerne, der er struktureret, og få vist de egenskaber, der er tildelt hver enkelt ressource. Du kan få mere at vide om REST-API handlinger og PowerShell-cmdlet'er, der er tilgængelige for en ressourcetype, og du kan udføre kommandoer via brugergrænsefladen på. Ressource Explorer kan være særligt nyttige, når du vil oprette ressourcestyring skabeloner, fordi det giver dig mulighed at få vist egenskaberne for eksisterende ressourcer.

Kilde til værktøjet ressource Explorer er tilgængelig på [github](https://github.com/projectkudu/ARMExplorer), som indeholder en værdifuld reference, hvis du har brug at implementere en lignende funktionsmåde i dine egne programmer.

## <a name="view-resources"></a>Vis ressourcer
Gå til [https://resources.azure.com](https://resources.azure.com) og logge på med de samme legitimationsoplysninger, du vil bruge til [Azure-portalen](https://portal.azure.com).

Når indlæst, treeview i venstre side giver dig mulighed at analysere ned i dine abonnementer og ressourcegrupper:

![TreeView](./media/resource-manager-resource-explorer/are-01-treeview.png)

Når du analysere i en ressourcegruppe får du vist udbydere, der findes ressourcer i den pågældende gruppe:

![udbydere](./media/resource-manager-resource-explorer/are-02-treeview-providers.png)

Derfra kan du starte analysere i ressource forekomster. I skærmbilledet nedenfor kan du se de `sltest` SQL Server-forekomst i trævisningen. I højre side, kan du se oplysninger om de REST-API-anmodninger, du kan bruge med denne ressource. Ved at navigere til noden for en ressource, gør ressource Explorer automatisk GET-anmodning til at hente oplysninger om ressourcen. I tekstområdet store under URL-adressen skal kun du se svaret fra API. 

Når du gør dig bekendt med ressourcestyring skabeloner starter brødtekst indholdet til at slå velkendte! Sektionen **Egenskaber** for svaret svarer til de værdier, du kan angive i sektionen **Egenskaber** af din skabelon.

![SQL server](./media/resource-manager-resource-explorer/are-03-sqlserver-with-response.png)

Ressource Explorer tillader dig at beholde analysere ned til at udforske underordnede ressourcer, hvis det er den SQL-databaseserver, der er underordnet ressourcer for ting som databaser og firewallregler.

Udforskning af en database, viser os egenskaberne for den pågældende database. I skærmbilledet nedenfor kan vi se, at databasen `edition` er `Standard` og `serviceLevelObjective` (eller databaseniveau) er `S1`.

![SQL-database](./media/resource-manager-resource-explorer/are-04-database-get.png)

## <a name="change-resources"></a>Ændre ressourcer

Når du har navigeret til en ressource, kan du vælge knappen Rediger for at gøre indholdet JSON redigerbar. Du kan derefter bruge ressource Stifinder til at redigere JSON og sende en indkaldelse til læg ændre ressourcen. For eksempel billedet nedenfor viser, kan databasen niveauet ændres til `S0`:

![database - læg anmodning](./media/resource-manager-resource-explorer/are-05-database-put.png)

Ved at vælge **PLACERE** sender du anmodningen. 

Når anmodningen er blevet sendt udsteder ressource Explorer igen GET-anmodning til at opdatere status. I dette tilfælde kan vi se, at den `requestedServiceObjectiveId` er blevet opdateret og er forskellig fra den `currentServiceObjectiveId` angiver, at en skalering er i gang. Du kan klikke på knappen Hent for at opdatere status manuelt.

![database - Hent request2](./media/resource-manager-resource-explorer/are-06-database-get2.png)

## <a name="performing-actions-on-resources"></a>Udføre handlinger på ressourcer

Fanen **Handlinger** gør det muligt at se og udføre yderligere RESTEN handlinger. Når du har valgt en websted ressource, vises fanen Handlinger for eksempel en lang liste over tilgængelige handlinger, som er vist nedenfor.

![Web - POST-anmodning](./media/resource-manager-resource-explorer/are-web-post.png)

## <a name="invoking-the-api-via-powershell"></a>Aktivering af API via PowerShell
Fanen PowerShell i ressource Explorer viser cmdlet'er til at bruge til at interagere med den ressource, du i øjeblikket udforske. Afhængigt af typen ressourcer, du har valgt, den viste PowerShell-script kan variere fra enkle cmdletter (såsom `Get-AzureRmResource` og `Set-AzureRmResource`) til mere kompliceret cmdletter (såsom Skift pladser på et websted). 

![PowerShell](./media/resource-manager-resource-explorer/are-07-powershell.png)

Du kan finde flere oplysninger i feltet Azure PowerShell-cmdlet'er i [Bruge PowerShell for Azure med Azure ressourcestyring](powershell-azure-resource-manager.md)

## <a name="summary"></a>Oversigt
Når du arbejder med Ressourcestyring, kan ressource-Explorer være en meget nyttigt værktøj. Det er en god måde at finde måder at bruge PowerShell til at forespørge og foretage ændringer. Hvis du arbejder med REST-API er det en god måde at komme i gang og hurtigt at afprøve API-kald, før du begynder at skrive kode. Og hvis du skriver skabeloner kan det være en god måde at forstå ressourcehierarkiet og finde hvor skal placeres konfiguration – du kan foretage en ændring i portalen og derefter finde de tilsvarende poster i ressource Explorer!

Du kan finde flere oplysninger, se [kanal 9 video med Søren Hanselman og David Ebbo](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Resource-Manager-Explorer-with-David-Ebbo)


