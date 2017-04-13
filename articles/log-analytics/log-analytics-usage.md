<properties
    pageTitle="Analysere databrug i Log Analytics | Microsoft Azure"
    description="Du kan bruge siden brugen i Log Analytics til at få vist, hvor meget data skal sendes til OMS-tjenesten."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/11/2016"
    ms.author="banders"/>

# <a name="analyze-data-usage-in-log-analytics"></a>Analysere databrug i Log Analytics

Log Analytics i handlinger Management pakke (OMS) indsamler data og sender den til tjenesten OMS med jævne mellemrum.  Du kan bruge siden **Brug** af til at få vist, hvor meget data skal sendes til OMS-tjenesten. **Brug** siden viser også du hvor meget data er blevet sendt dagligt ved løsninger, og hvor ofte serverne sender data.

>[AZURE.NOTE] Hvis du har en gratis konto, der er oprettet ved hjælp af [OMS websted](http://www.microsoft.com/oms), er du begrænset til at sende 500 MB data til tjenesten OMS dagligt. Hvis du kommer til den daglige grænse, stopper dataanalyse og CV i starten af den næste dag. Du skal også sende de data, der ikke blev accepteret eller behandlet af OMS.

Du kan få vist din brugen ved hjælp af feltet **brugen** på dashboardet **Oversigt** i OMS.

![Brug af side om side](./media/log-analytics-usage/usage-tile.png)

Hvis du har overskredet lagergrænsen daglig brugen, eller hvis du er i nærheden af din grænse, kan du eventuelt fjerne en løsning for at reducere mængden data, du sender til OMS-tjenesten. Se [tilføje Log Analytics løsninger fra løsningsgalleriet](log-analytics-add-solutions.md), du kan finde flere oplysninger om at fjerne løsninger.

![brugen dashboard](./media/log-analytics-usage/usage-dashboard.png)

Siden **brugen** viser følgende oplysninger:

- Gennemsnitlig brugen dagen
- Brug af data for hver løsning over de seneste 30 dage
- Hvor meget data servere i dit miljø sendes til tjenesten OMS over de seneste 30 dage
- Din dataabonnement priser niveau og anslåede omkostninger
- Oplysninger om din serviceaftale (SLA), herunder, hvor lang tid det tager OMS til at behandle dine data

## <a name="to-work-with-usage-data"></a>Arbejde med data om brug af

1. Klik på feltet **brugen** på siden **Oversigt** .
2. Få vist de Brug kategorier, der viser områder, du er bekymret for, på siden **Brug** .
3. Hvis du har en løsning, der forbrug for mange af dine daglige Overfør kvote, kan du overveje at fjerne løsningen.

## <a name="to-view-your-estimated-cost-and-billing-information"></a>Sådan får du vist dine anslåede omkostninger og faktureringsoplysninger
1. Klik på feltet **brugen** på siden **Oversigt** .
2. Klik på vinklen på siden **Brug** under **brugen**(**>**) ud for **Anslået omkostninger**.
3. I den udvidede **din dataabonnement** detaljer, du kan se din anslåede månedligt omkostninger.  
    ![Din dataabonnement](./media/log-analytics-usage/usage-data-plan.png)
4. Hvis du vil have vist dine betalingsoplysninger, skal du klikke på **få vist min faktura** for at få vist dine abonnementsoplysninger.
    - Klik på dit abonnement for at få vist detaljer og en linjeelementer liste over brugen på siden abonnementer.  
        ![abonnement](./media/log-analytics-usage/usage-sub01.png)
    - På siden Oversigt for dit abonnement, kan du udføre en række opgaver til at administrere og få vist flere oplysninger om dit abonnement.  
        ![abonnementsoplysninger](./media/log-analytics-usage/usage-sub02.png)

## <a name="to-view-data-batches-for-your-sla"></a>Sådan får du vist data navne for din SERVICENIVEAUAFTALE
1. Klik på feltet **brugen** på siden **Oversigt** .
2. Klik på **Hent SLA detaljer**under **Serviceaftale**.
3. En Excel-XLSX-filen er downloadet til at gennemgå.  
    ![SLA detaljer](./media/log-analytics-usage/usage-sla-details.png)

## <a name="next-steps"></a>Næste trin

- Se [Log søgninger i Log Analytics](log-analytics-log-searches.md) til at få vist detaljerede oplysninger, der indsamles af løsninger.
