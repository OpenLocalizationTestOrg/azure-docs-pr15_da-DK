<properties
   pageTitle="Få vist installation operationer med portal | Microsoft Azure"
   description="Beskriver, hvordan du bruger portalen Azure til at registrere fejl fra ressourcestyring installation."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/15/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-portal"></a>Få vist installationshandlinger med Azure-portalen

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure CLI](resource-manager-troubleshoot-deployments-cli.md)
- [REST-API](resource-manager-troubleshoot-deployments-rest.md)

Du kan få vist handlinger for en installation via Azure-portalen. Du kan være mest interesseret i at få vist handlingerne, når du har modtaget en fejl under installation, så i denne artikel fokuserer på visning af handlinger, som har mislykkedes. Portalen indeholder en brugergrænseflade, kan du nemt finde fejlene og bestemme mulige løsninger.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

## <a name="use-deployment-operations-to-troubleshoot"></a>Brug installationshandlinger til at foretage fejlfinding af

Hvis du vil se handlingerne installation, skal du følge nedenstående trin:

1. Bemærk status for den sidste installation for ressourcegruppen involveret i installationen. Du kan vælge denne status for at få flere oplysninger.

    ![status for installation](./media/resource-manager-troubleshoot-deployments-portal/deployment-status.png)

2. Du får vist de seneste historik for installation. Vælg den installation, mislykkedes.

    ![status for installation](./media/resource-manager-troubleshoot-deployments-portal/select-deployment.png)

3. Vælg **mislykkedes. Klik her for at få oplysninger om** at se en beskrivelse af, hvorfor installationen mislykkedes. DNS-posten er ikke entydige i billedet nedenfor.  

    ![få vist mislykket installation](./media/resource-manager-troubleshoot-deployments-portal/view-error.png)

    Denne fejlmeddelelse skal være nok til at starte fejlfinding. Men hvis du har brug for flere oplysninger om hvilke opgaver er fuldført, kan du se handlingerne, som vist i følgende trin.

4. Du kan se alle handlingerne installation i bladet **installation** . Vælg en handling til at få vist flere detaljer.

    ![Vis operationer](./media/resource-manager-troubleshoot-deployments-portal/view-operations.png)

    I dette tilfælde skal se du, lagerplads firmaet, virtuelt netværk og tilgængelighed sæt der blev oprettet. Den offentlige IP-adresse mislykkedes, og andre ressourcer blev ikke forsøgt.

5. Du kan få vist hændelser for installationen ved at vælge **begivenheder**.

    ![Vis hændelser](./media/resource-manager-troubleshoot-deployments-portal/view-events.png)

6. Du se alle hændelserne for installationen og vælge en få mere at vide.

    ![Se begivenheder](./media/resource-manager-troubleshoot-deployments-portal/see-all-events.png)

## <a name="use-audit-logs-to-troubleshoot"></a>Brug overvågningslogge at foretage fejlfinding af

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Hvis du vil se fejl for en installation, skal du følge nedenstående trin:

1. Få vist overvågningsloggene for en ressourcegruppe ved at vælge **Overvåge logfiler**.

    ![Vælg overvågningslogge](./media/resource-manager-troubleshoot-deployments-portal/select-audit-logs.png)

2. I bladet **Overvågningslogge** vises en oversigt over seneste handlinger for alle ressourcegrupperne i dit abonnement. Den indeholder en grafisk repræsentation af tid og status for handlingerne, samt en liste over handlingerne.

    ![Vis handlinger](./media/resource-manager-troubleshoot-deployments-portal/audit-summary.png)

3. Du kan filtrere din visning af overvågningslogge til at fokusere på bestemte betingelser. Vælg **Filter** på øverst del af bladet **overvågningslogge** .

    ![filter logfiler](./media/resource-manager-troubleshoot-deployments-portal/filter-logs.png)

4. Vælg betingelser for at begrænse visningen af overvågningslogge til kun disse handlinger, du vil have vist bladet **Filter** . For eksempel kan du filtrere handlinger for kun at vise fejl for ressourcegruppen.

    ![angive filterindstillinger](./media/resource-manager-troubleshoot-deployments-portal/set-filter.png)

5. Du kan filtrere handlinger yderligere ved at angive et tidsrum. Følgende billede filtrerer visningen til et bestemt 20 minutter timespan.

    ![Angiv klokkeslæt](./media/resource-manager-troubleshoot-deployments-portal/select-time.png)

6. Du kan vælge handlinger på listen. Vælg den handling, der indeholder fejl, du vil undersøge.

    ![Vælg handling](./media/resource-manager-troubleshoot-deployments-portal/select-operation.png)
  
7. Du får vist alle hændelserne for den pågældende operation. Bemærk **Korrelations-ID'ER** i oversigten. Dette ID bruges til at registrere relaterede begivenheder. Det kan være nyttigt, når du arbejder med teknisk support for at foretage fejlfinding af et problem. Du kan vælge nogen af begivenhed til at se oplysninger om begivenheden.

    ![Vælg begivenhed](./media/resource-manager-troubleshoot-deployments-portal/select-event.png)

8. Du vil se oplysninger om begivenheden. Især være opmærksom på **Egenskaber** for oplysninger om fejlen.

    ![vise overvågningslog logoplysninger](./media/resource-manager-troubleshoot-deployments-portal/audit-details.png)

Det filter, du har anvendt til overvågningsloggen bevares næste gang du får vist det, så du skal muligvis ændre disse værdier for at udvide visningen handlinger.

## <a name="next-steps"></a>Næste trin

- Hjælp til løsning af fejl bestemt-installation, skal du se [løse almindelige fejl, når du installerer ressourcer til Azure med Azure ressourcestyring](resource-manager-common-deployment-errors.md).
- Hvis du vil lære at bruge overvågningslogge til at overvåge andre typer handlinger, se [overvåge forskellige handlinger med ressourcestyring](resource-group-audit.md).
- For at validere installationen, før du udfører den, skal du se [Implementer en ressourcegruppe med Azure ressourcestyring skabelon](resource-group-template-deploy.md).
