<properties
   pageTitle="Overvåge handlinger, hændelser og tællere til NSGs | Microsoft Azure"
   description="Lær, hvordan du aktiverer tællere, begivenheder og drift hændelseslogføring i NSGs"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/14/2016"
   ms.author="jdial" />

#<a name="log-analytics-for-network-security-groups-nsgs"></a>Log analyser af netværk sikkerhedsgrupper (NSGs)

Du kan bruge forskellige typer logfiler i Azure til at administrere og foretage fejlfinding af NSGs. Nogle af disse logfiler kan åbnes via portalen, og alle logfiler kan udtrækkes fra en Azure blob-lager, og vises i forskellige værktøjer, som [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), Excel og PowerBI. Du kan få mere at vide om de forskellige typer logge på listen herunder.

- **Overvågningslogge:** Du kan bruge [Azure overvågningslogge](../monitoring-and-diagnostics/insights-debugging-with-events.md) (tidligere kendt som funktionsdygtige logfiler) til at få vist alle handlinger, der sendes til dine Azure abonnementer og deres status. Overvågningslogge er aktiveret som standard, og kan ses i portalen Azure preview.
- **Hændelseslogfiler:** Du kan bruge denne log til at få vist, hvilke NSG reglerne skal anvendes på FOS og forekomst roller, der er baseret på MAC-adresse. Status for disse regler, der indsamles hver 60 sekunder.
- **Tæller logfiler:** Du kan bruge denne log til at få vist hvor mange gange hver NSG regel blev anvendt for at nægte eller tillade trafik.

>[AZURE.WARNING] Logfiler er kun tilgængelige for de ressourcer, der er implementeret i implementeringsmodel ressourcestyring. Du kan ikke bruge logfiler til ressourcer i modellen Klassisk installation. Reference til en bedre forståelse af de to modeller, artiklen [om ressourcestyring installation og klassisk installation](../resource-manager-deployment-model.md) .

##<a name="enable-logging"></a>Aktivere logføring
Overvågning af logføring er automatisk aktiveret på hele tiden for hver ressourcestyring ressource. Du skal aktivere begivenhed og tæller logføring til at starte med at samle de data, der er tilgængelige via disse logfiler. Følg nedenstående trin for at aktivere logføring.

1.  Log på [Azure-portalen](https://portal.azure.com). Hvis du ikke allerede har en eksisterende netværk sikkerhedsgruppe, [oprette en NSG](virtual-networks-create-nsg-arm-ps.md) , før du fortsætter.

2.  Klik på **Gennemse**i portalen preview >> **netværk sikkerhedsgrupper**.

    ![Preview portal - netværk sikkerhedsgrupper](./media/virtual-network-nsg-manage-log/portal-enable1.png)

3. Vælg en eksisterende netværk sikkerhedsgruppe.

    ![Preview portal - netværk sikkerhed gruppeindstillinger](./media/virtual-network-nsg-manage-log/portal-enable2.png)

4. Klik på **diagnosticering**i bladet **Indstillinger** , og klik derefter **på** i ruden **diagnosticering** ud for **Status**
5. Klik på **Lagerplads konto**i bladet **Indstillinger** , og enten vælge et eksisterende lagerplads konto eller oprette en ny.  

>[AZURE.INFORMATION] overvågningslogge kræver ikke en særskilt opbevaring-konto. Brug af lagerplads til begivenheden og regel logføring vil betale gebyrer.

6. Vælg, om du vil logge begivenheder, tællere eller begge dele, og klik derefter på **Gem**i rullelisten lige under **Lagerplads konto**.

    ![Preview portal - diagnosticering logfiler](./media/virtual-network-nsg-manage-log/portal-enable3.png)

## <a name="audit-log"></a>Overvågningslog
Denne log (tidligere kendt som "funktionsdygtige logon-") genereres af Azure som standard.  Loggene bevares for 90 dage i Azures hændelseslogfiler store. Lær mere om disse logfiler ved at læse artiklen [se begivenheder og overvågningsloggen](../monitoring-and-diagnostics/insights-debugging-with-events.md) .

## <a name="counter-log"></a>Tællerlog
Denne log genereres kun, hvis du har aktiveret det på grundlag per NSG som beskrevet ovenfor. Data, der er gemt i lagerplads-konto, du angav, da du har aktiveret logføring. Hver regel, der er anvendt på ressourcer, der er logget på JSON-formatet, som vist nedenfor.

    {
        "time": "2015-09-11T23:14:22.6940000Z",
        "systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
        "category": "NetworkSecurityGroupRuleCounter",
        "resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
        "operationName": "NetworkSecurityGroupCounters",
        "properties": {
            "vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
            "subnetPrefix":"10.0.0.0/24",
            "macAddress":"001517D9C43C",
            "ruleName":"DenyAllOutBound",
            "direction":"Out",
            "type":"block",
            "matchedConnections":0
            }
    }

## <a name="event-log"></a>Hændelseslog
Denne log genereres kun, hvis du har aktiveret det på grundlag per NSG som beskrevet ovenfor. Data, der er gemt i lagerplads-konto, du angav, da du har aktiveret logføring. Følgende data logføres:

    {
        "time": "2015-09-11T23:05:22.6860000Z",
        "systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
        "category": "NetworkSecurityGroupEvent",
        "resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
        "operationName": "NetworkSecurityGroupEvents",
        "properties": {
            "vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
            "subnetPrefix":"10.0.0.0/24",
            "macAddress":"001517D9C43C",
            "ruleName":"AllowVnetOutBound",
            "direction":"Out",
            "priority":65000,
            "type":"allow",
            "conditions":{
                "destinationPortRange":"0-65535",
                "sourcePortRange":"0-65535",
                "destinationIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32",
                "sourceIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32"
            }
        }
    }

## <a name="view-and-analyze-the-audit-log"></a>Få vist og analysere overvågningsloggen
Du kan se og analysere overvågningslog logdata ved hjælp af følgende metoder:

- **Azure værktøjer:** Hente oplysninger fra overvågningslogge via Azure PowerShell, Azure Command Line Interface (CLI), Azure REST-API eller portalen Azure preview.  Trinvise instruktioner til hver enkelt metode gennemgås i artiklen [overvågningslog handlinger med ressourcestyring](../resource-group-audit.md) .
- **Power BI:** Hvis du ikke allerede har en [Power BI](https://powerbi.microsoft.com/pricing) -konto, kan du prøve den gratis. Brug af [Azure overvågningslogge indhold pack til Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/) kan du analysere dine data med forudkonfigurerede dashboards, der kan bruges som-er, eller Tilpas.

## <a name="view-and-analyze-the-counter-and-event-log"></a>Få vist og analysere tæller og hændelseslog

Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) kan indsamle tælleren og hændelseslog filer fra kontoen Blob-lager og omfatter visualiseringer og effektive søgefunktioner til at analysere logfilerne.

Du kan også oprette forbindelse til kontoen lager og hente JSON logposter for begivenheden og tæller loggene. Når du henter JSON-filer, kan du konvertere dem til csv- og view i Excel, PowerBI eller andre datavisualisering værktøjer.

>[AZURE.TIP] Hvis du kender til Visual Studio og grundlæggende begreber for at ændre værdier for konstanter og variabler i C#, kan du bruge [log konverteringsprogram værktøjer](https://github.com/Azure-Samples/networking-dotnet-log-converter) tilgængelige fra Github.

## <a name="next-steps"></a>Næste trin

- Visualisere tæller og hændelseslogfiler med [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)
- [Visuelle effekter din Azure overvågningslogge med Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) blogindlæg.
- [Visning og analysere Azure overvågningslogge i Power BI og meget mere](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogindlæg.
