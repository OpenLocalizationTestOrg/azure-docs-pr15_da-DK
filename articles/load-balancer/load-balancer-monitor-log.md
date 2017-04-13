<properties
   pageTitle="Overvåge handlinger, hændelser og tællere til justering af belastning | Microsoft Azure"
   description="Lær, hvordan du aktiverer besked om begivenheder og forespørgsler logføring af besøgsanalyse status for Azure justering af belastning"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="log-analytics-for-azure-load-balancer-preview"></a>Log analytics til justering af belastning Azure (Preview)

Du kan bruge forskellige typer logfiler i Azure til at administrere og foretage fejlfinding af belastning balancere. Nogle af disse logfiler kan åbnes via portalen. Alle logfiler kan udtrækkes fra en Azure blob-lager og vises i forskellige værktøjer, som Excel og PowerBI. Du kan få mere at vide om de forskellige typer logge på listen herunder.

- **Overvågningslogge:** Du kan bruge [Azure overvågningslogge](../../articles/monitoring-and-diagnostics/insights-debugging-with-events.md) (tidligere kendt som funktionsdygtige logfiler) til at få vist alle handlinger, der sendes til dine Azure abonnementer og deres status. Overvågningslogge er aktiveret som standard, og kan ses i portalen Azure.
- **Besked hændelseslogfiler:** Du kan bruge denne log til at få vist, hvilke beskeder for justering af belastning er opløftet. Status for justering af belastning indsamles alle fem minutter. Denne log skrives kun, hvis en besked om hændelsen indlæsning belastningsjusteringstjenesten opløftes.
- **Sundhed efterprøvning af af logfiler:** Du kan bruge denne log for at kontrollere, om efterprøvning af af sundhed Kontrollér status, hvor mange forekomster er online med Indlæs belastningsjusteringstjenesten back end- og procentdel af virtuelle maskiner modtager netværkstrafik fra justering af belastning. Denne log skrives på efterprøvning af af statusændring begivenhed.

>[AZURE.IMPORTANT] Log analyser virker aktuelt kun for indlæsning balancere via internettet. Logfiler er kun tilgængelige for de ressourcer, der er implementeret i implementeringsmodel ressourcestyring. Du kan ikke bruge logfiler til ressourcer i modellen Klassisk installation. Se [forstå ressourcestyring installation og klassisk installation](../../articles/resource-manager-deployment-model.md)kan finde flere oplysninger om installation-modeller.

## <a name="enable-logging"></a>Aktivere logføring

Overvågning af logføring er automatisk aktiveret for hver ressourcestyring ressource. Du skal aktivere begivenhed og sundhed efterprøvning af af logføring for at begynde at indsamle de data, der er tilgængelige via disse logfiler. Brug følgende trin til at aktivere logføring.

Log på [Azure-portalen](http://portal.azure.com). Hvis du ikke allerede har en belastningsjustering, [oprette en belastningsjustering](load-balancer-get-started-internet-arm-ps.md) , før du fortsætter.

1. Klik på **Gennemse**i portalen.
2. Vælg **Indlæs balancere**.

    ![Portal - justering af belastning](./media/load-balancer-monitor-log/load-balancer-browse.png)

3. Vælg en eksisterende justering af belastning >> **Alle indstillinger**.
4. Rul ned til **overvågnings**i højre side af dialogboksen under navnet på justering af belastning, skal du klikke på **diagnosticering**.

    ![Portal - Indlæs-belastningsjusteringstjenesten-indstillinger](./media/load-balancer-monitor-log/load-balancer-settings.png)

5. Vælg **på**i ruden **Diagnostics** under **Status**.
6. Klik på **lagerplads konto**.
7. Under **LOGFILER**, Vælg en eksisterende lagerplads-konto eller oprette en ny. Brug skyderen til at finde ud af, hvor mange dage begivenhed dat bevares i hændelseslogfiler. 8. Klik på **Gem**.

    ![Portal - Diagnostics logfiler](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

>[AZURE.INFORMATION] overvågningslogge kræver ikke en særskilt opbevaring-konto. Brug af lagerplads på begivenheden og tilstand efterprøvning af af logføring vil betale gebyrer.

## <a name="audit-log"></a>Overvågningslog

Overvågningsloggen genereres som standard. Loggene bevares for 90 dage i Azures hændelseslogfiler store. Lær mere om disse logfiler ved at læse artiklen [se begivenheder og overvågningsloggen](../../articles/monitoring-and-diagnostics/insights-debugging-with-events.md) .

## <a name="alert-event-log"></a>Beskeder om hændelseslog

Denne log genereres kun, hvis du har aktiveret den på en per Indlæs belastningsjusteringstjenesten datotype. Hændelserne, der er logget på JSON-formatet og gemmes i lagerplads-konto, du angav, da du har aktiveret logføring. Følgende er et eksempel på en begivenhed.

    {
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }

JSON output viser den *eventname* egenskab, der beskriver årsagen til den justering af belastning oprettet en besked. I dette tilfælde blev oprettet beskeden på grund af TCP-port konsumption skyldes kilde IP NAT begrænsninger (SNAT).

## <a name="health-probe-log"></a>Sundhed efterprøvning af af logfiler

Denne log genereres kun, hvis du har aktiveret den på en per Indlæs belastningsjusteringstjenesten datotype som beskrevet ovenfor. Data, der er gemt i lagerplads-konto, du angav, da du har aktiveret logføring. Der oprettes en objektbeholder med navnet 'indsigt-logfiler-loadbalancerprobehealthstatus' og følgende data logføres:

    {
        "records":
        {
            "time": "2016-01-26T10:37:46.6024215Z",
            "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
            "category": "LoadBalancerProbeHealthStatus",
            "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
            "operationName": "LoadBalancerProbeHealthStatus",
            "properties": {
                "publicIpAddress": "40.83.190.158",
                "port": "81",
                "totalDipCount": 2,
                "dipDownCount": 1,
                "healthPercentage": 50.000000
            }
        },
        {
            "time": "2016-01-26T10:37:46.6024215Z",
            "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
            "category": "LoadBalancerProbeHealthStatus",
            "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
            "operationName": "LoadBalancerProbeHealthStatus",
            "properties": {
                "publicIpAddress": "40.83.190.158",
                "port": "81",
                "totalDipCount": 2,
                "dipDownCount": 0,
                "healthPercentage": 100.000000
            }
        }
    }

I JSON-output vises i feltet egenskaber for de grundlæggende oplysninger om efterprøvning af af tilstand. Egenskaben *dipDownCount* viser det samlede antal forekomster på den back-end, der ikke får netværkstrafik på grund af mislykkede efterprøvning af af svar.

## <a name="view-and-analyze-the-audit-log"></a>Få vist og analysere overvågningsloggen

Du kan se og analysere overvågningslog logdata ved hjælp af følgende metoder:

- **Azure værktøjer:** Hente oplysninger fra overvågningslogge via Azure PowerShell, Azure Command Line Interface (CLI), Azure REST-API eller portalen Azure preview. Trinvise instruktioner til hver enkelt metode gennemgås i artiklen [overvågningslog handlinger med ressourcestyring](../../articles/resource-group-audit.md) .
- **Power BI:** Hvis du ikke allerede har en [Power BI](https://powerbi.microsoft.com/pricing) -konto, kan du prøve den gratis. Ved hjælp af den [Azure overvågningslogge indhold pack til Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs), kan du analysere dine data med forudkonfigurerede dashboards, eller du kan tilpasse visninger, så den passer til dine behov.

## <a name="view-and-analyze-the-health-probe-and-event-log"></a>Få vist og analysere sundhed efterprøvning af af og hændelseslog

Du har brug at oprette forbindelse til kontoen lagerplads og hente JSON logposter for begivenheden og tilstand efterprøvning af af logfiler. Når du henter JSON-filer, kan du konvertere dem til csv- og view i Excel, PowerBI eller andre datavisualisering værktøjer.

>[AZURE.TIP] Hvis du kender til Visual Studio og grundlæggende begreber for at ændre værdier for konstanter og variabler i C#, kan du bruge [log konverteringsprogram værktøjer](https://github.com/Azure-Samples/networking-dotnet-log-converter) tilgængelige fra Github.

## <a name="additional-resources"></a>Yderligere ressourcer

- [Visuelle effekter din Azure overvågningslogge med Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) blogindlæg.
- [Visning og analysere Azure overvågningslogge i Power BI og meget mere](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogindlæg.

## <a name="next-steps"></a>Næste trin

[Forstå Indlæs belastningsjusteringstjenesten sonder](load-balancer-custom-probe-overview.md)
