<properties
   pageTitle="Visninger i handlinger Management pakke (OMS) management-løsninger | Microsoft Azure"
   description="Management-løsninger i handlinger Management pakke (OMS) indeholder typisk en eller flere visninger for at visualisere data.  I denne artikel beskrives, hvordan du kan eksportere en visning, der er oprettet af Designer visninger og medtage dem i en løsning. "
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="bwren" />

# <a name="views-in-operations-management-suite-oms-management-solutions-preview"></a>Visninger i handlinger Management pakke (OMS) management-løsninger (Preview)

>[AZURE.NOTE]Dette er foreløbig dokumentation til oprettelse af management-løsninger i OMS, der aktuelt findes i Vis udskrift. Et skema, der er beskrevet nedenfor kan blive ændret.    

[Management-løsninger i handlinger Management pakke (OMS)](operations-management-suite-solutions.md) indeholder typisk en eller flere visninger for at visualisere data.  I denne artikel beskrives, hvordan du kan eksportere en visning, der er oprettet af [Designer visninger](../log-analytics/log-analytics-view-designer.md) og medtage dem i en løsning.  

>[AZURE.NOTE]Eksemplerne i denne artikel bruge parametre og variabler, der kræves eller er fælles for management-løsninger og er beskrevet i [oprette management-løsninger i handlinger Management pakke (OMS)](operations-management-suite-solutions-creating.md) 


## <a name="prerequisites"></a>Forudsætninger
Denne artikel forudsætter, at du allerede er kendskab til, hvordan du kan [oprette en løsning til administration](operations-management-suite-solutions-creating.md) og strukturen i en løsningsfil.


## <a name="overview"></a>Oversigt

Hvis du vil medtage en visning i en løsning, opretter du en **ressource** for den i [løsningsfilen](operations-management-suite-solutions-creating.md).  JSON, der beskriver visningens detaljerede konfiguration er typisk kompleks via og ikke noget, en typisk løsning forfatter vil kunne oprette manuelt.  Den mest almindelige metode er at oprette visningen ved hjælp af [Vis Designer](../log-analytics/log-analytics-view-designer.md), eksportere dem og derefter tilføje den detaljerede konfiguration til løsningen. 

De grundlæggende trin til at tilføje en visning til en løsning er som følger:  Hvert trin er beskrevet i yderligere oplysninger i afsnittene herunder.

1. Eksportere visningen til en fil.
2. Opret visning ressourcen i løsningen.
3. Tilføj Vis detaljer.

## <a name="export-the-view-to-a-file"></a>Eksportere visningen til en fil
Følg vejledningen på [Log Analytics visning Designer](../log-analytics/log-analytics-view-designer.md) for at eksportere en visning til en fil.  Den eksporterede fil bliver i JSON format med de samme [elementer som løsningsfilen](operations-management-suite-solutions-creating.md#management-solution-files).  

Elementet **ressourcer** i visningen filen har en ressource med en type af **Microsoft.OperationalInsights/workspaces** , der repræsenterer OMS arbejdsområdet.  Dette element har et underelement med en type af **visninger** , der repræsenterer visningen og indeholder detaljerede konfigurationen.  Du kan kopiere oplysningerne om dette element og derefter kopiere den til din løsning.


## <a name="create-the-view-resource-in-the-solution"></a>Oprette visning ressourcen i løsningen
Føje følgende visning ressourcen til elementet **ressourcer** af din løsningsfil.  Variabler, der er beskrevet nedenfor, skal du også tilføje bruger.  Bemærk, at egenskaberne **Dashboard** og **OverviewTile** pladsholdere, som du vil overskrive med de tilsvarende egenskaber fra den eksporterede visnings-fil.
 
    {
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
        "type": "Microsoft.OperationalInsights/workspaces/views",
        "location": "[parameters('workspaceregionId')]",
        "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
        dependson": [
            ],
        "properties": {
            "Id": "[variables('ViewName')]",
            "Name": "[variables('ViewName')]",
            "DisplayName": "[variables('ViewName')]",
            "Description": "",
            "Author": "[variables('ViewAuthor')]",
            "Source": "Local",
            "Dashboard": ,
            "OverviewTile": 
        }
    }

Tilføj følgende variabler til elementet [variabler](operations-management-suite-solutions-creating.md#variables) i fil, og Erstat værdier til rettighederne for din løsning.

    "LogAnalyticsApiVersion": "2015-11-01-preview",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."


Bemærk, at du kan kopiere hele visningen ressourcen fra eksporterede visnings-filen, men du har brug at foretage følgende ændringer til det virker i din løsning.  

- **Type** for Vis ressourcen skal ændres fra **visninger** til **Microsoft.OperationalInsights/workspaces**.
- Egenskaben **navn** for visningen ressourcen skal ændres for at medtage navnet på arbejdsområdet.
- Afhængighed af arbejdsområdet skal fjernes, da arbejdsområde ressourcen ikke er defineret i løsningen.
- **Vist navn** egenskaben skal føjes til visningen.  Det **Id**, **navn**og **vist navn** skal alle stemme overens.
- Parameternavne skal ændres, så det svarer til det nødvendige sæt parametre.
- Variabler skal være defineret i løsningen og bruges i de relevante egenskaber.

## <a name="add-the-view-details"></a>Tilføje Vis detaljer
Vis ressourcen i eksporterede visnings-filen skal indeholde to elementer i elementet **Egenskaber** med navnet **Dashboard** og **OverviewTile** , der indeholder detaljerede konfigurationen af visningen.  Kopiere disse to elementer og deres indhold i elementet **Egenskaber** på ressourcen, visning i din løsningsfil. 

## <a name="example"></a>Eksempel
For eksempel viser i følgende eksempel en simpel løsningsfil med en visning.  Ellipser (…) vises for **Dashboard** og **OverviewTile** indholdet af mellemrum årsager.


    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string"
            },
            "accountName": {
                "type": "string"
            },
            "workspaceRegionId": {
                "type": "string"
            },
            "regionId": {
                "type": "string"
            },
            "pricingTier": {
                "type": "string"
            }
        },
        "variables": {
            "SolutionVersion": "1.1",
            "SolutionPublisher": "Contoso",
            "SolutionName": "Contoso Solution",
            "LogAnalyticsApiVersion": "2015-11-01-preview",
            "ViewAuthor":  "user@contoso.com",
            "ViewDescription":  "This is a sample view.",
            "ViewName":  "Contoso View"
        },
        "resources": [
            {
                "name": "[concat(variables('SolutionName'), '(' ,parameters('workspacename'), ')')]",
                "location": "[parameters('workspaceRegionId')]",
                "tags": { },
                "type": "Microsoft.OperationsManagement/solutions",
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspacename'), '/views/', variables('ViewName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspacename'))]",
                    "referencedResources": [
                    ],
                    "containedResources": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
                    ]
                },
                "plan": {
                    "name": "[concat(variables('SolutionName'), '(' ,parameters('workspaceName'), ')')]",
                    "Version": "[variables('SolutionVersion')]",
                    "product": "ContosoSolution",
                    "publisher": "[variables('SolutionPublisher')]",
                    "promotionCode": ""
                }
            },
            {
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
                "type": "Microsoft.OperationalInsights/workspaces/views",
                "location": "[parameters('workspaceregionId')]",
                "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
                "dependson": [
                ],
                "properties": {
                    "Id": "[variables('ViewName')]",
                    "Name": "[variables('ViewName')]",
                    "DisplayName": "[variables('ViewName')]",
                    "Description": "[variables('ViewDescription')]",
                    "Author": "[variables('ViewAuthor')]",
                    "Source": "Local",
                    "Dashboard": ...,
                    "OverviewTile": ...
                }
            }
        ]
    }




## <a name="next-steps"></a>Næste trin

- Få mere at vide fuldstændige oplysninger om oprettelse af [management-løsninger](operations-management-suite-solutions-creating.md).
- Medtage [automatisering runbooks i management-løsning](operations-management-suite-solutions-resources-automation.md).