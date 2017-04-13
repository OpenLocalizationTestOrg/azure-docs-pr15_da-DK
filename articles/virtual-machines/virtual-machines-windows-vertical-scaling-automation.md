<properties
    pageTitle="Lodret skalere Azure virtuelle maskiner med Automation Azure | Microsoft Azure"
    description="Sådan lodret skalere et Windows Virtual Machine som svar på overvågning beskeder med Automation Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/29/2016"
    ms.author="singhkay"/>

# <a name="vertically-scale-azure-virtual-machines-with-azure-automation"></a>Lodret skalere Azure virtuelle maskiner med Automation Azure

Lodret skalering er processen med at øge eller mindske ressourcer på en computer som svar på arbejdsbelastningen. I Azure kan dette gøres ved at ændre størrelsen på den virtuelle maskine. Dette kan hjælpe i følgende scenarier

- Hvis den virtuelle maskine ikke er bruges ofte, kan du tilpasse det til en mindre størrelse til at reducere dine månedlige udgifter
- Hvis den virtuelle maskine vist en spidsbelastning, kan den ændres til en større at øge kapaciteten

Dispositionen for at se trinnene til at udføre dette er som nedenfor

1. Konfigurere Azure automatisering for at få adgang til din virtuelle maskiner
2. Importere Azure automatisering lodret skalering runbooks til dit abonnement
3. Føje en webhook til din runbook
4. Føje en besked til din virtuelle maskine

> [AZURE.NOTE] På grund af størrelsen på den første virtuelle maskine, størrelsen den kan skaleres til, kan være begrænset på grund af tilgængeligheden af de andre størrelser i klyngen aktuelle virtuelt er implementeret i. I den publicerede automatisering runbooks, der er benyttet i denne artikel vi tager sig af dette tilfælde og skalere kun inden for den under VM størrelse par. Det betyder, at en Standard_D1v2 virtuel maskine ikke pludselig vil skaleret til Standard_G5 eller skaleret til Basic_A0.

>| VM størrelser skalering par |   |
|---|---|
|  Basic_A0 |  Basic_A4 |
|  Standard_A0 | Standard_A4 |
|  Standard_A5 | Standard_A7  |
|  Standard_A8 | Standard_A9  |
|  Standard_A10 |  Standard_A11 |
|  Standard_D1 |  Standard_D4 |
|  Standard_D11 | Standard_D14  |
|  Standard_DS1 |  Standard_DS4 |
|  Standard_DS11 | Standard_DS14  |
|  Standard_D1v2 |  Standard_D5v2 |
|  Standard_D11v2 |  Standard_D14v2 |
|  Standard_G1 |  Standard_G5 |
|  Standard_GS1 |  Standard_GS5 |

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Konfigurere Azure automatisering for at få adgang til din virtuelle maskiner

Det første, du skal gøre, er at oprette en Azure automatisering-konto, der er vært for runbooks bruges til at skalere et virtuelt. Senest introduceret Automation service funktionen "Kør som konto", som gør indstilling af tjenesten hovedstolen for automatisk kørsel af runbooks på brugerens vegne meget nemt. Du kan læse mere om dette i følgende artikel:

* [Godkende Runbooks med Azure Kør som konto](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importere Azure automatisering lodret skalering runbooks til dit abonnement

De runbooks, som er nødvendige for lodret skalering din virtuelle maskine er allerede publiceret i galleriet Azure automatisering Runbook. Du skal importere dem til dit abonnement. Du kan få mere at vide hvordan du importerer runbooks ved at læse følgende artikel.

* [Modulet og Runbook gallerier til Azure automatisering](../automation/automation-runbook-gallery.md)

De runbooks, der skal importeres er vist i billedet nedenfor

![Importere runbooks](./media/virtual-machines-vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Føje en webhook til din runbook

Når du har importeret føje runbooks skal du en webhook til runbook, så den kan udløses af en påmindelse fra en virtuel maskine. Oplysninger om oprettelse af en webhook til din Runbook kan læses her

* [Azure automatisering webhooks](../automation/automation-webhooks.md)

Kontrollér, at du kopierer webhook før du lukker dialogboksen webhook, som du skal bruge dette i næste afsnit.

## <a name="add-an-alert-to-your-virtual-machine"></a>Føje en besked til din virtuelle maskine

1. Vælg indstillinger for virtuel maskine
2. Vælg "Regler for påmindelser"
3. Vælg "Tilføj besked"
4. Vælg en metrikværdi forespørgselsreglen beskeden på
5. Vælg en tilstand, og når opfyldt er medføre den vigtige besked til forespørgselsreglen
6. Vælg en grænseværdi for betingelsen i trin 5. skal være opfyldt
7. Vælg en periode, hvor tjenesten overvågning skal kontrollere for betingelse og grænseværdi i trin 5 og 6
8. Indsæt den webhook, du har kopieret fra den forrige sektion.

![Føje besked til virtuelt 1](./media/virtual-machines-vertical-scaling-automation/add-alert-webhook-1.png)

![Føje besked til virtuelt 2](./media/virtual-machines-vertical-scaling-automation/add-alert-webhook-2.png)
