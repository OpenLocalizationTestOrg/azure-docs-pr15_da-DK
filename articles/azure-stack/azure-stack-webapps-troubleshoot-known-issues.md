<properties
    pageTitle="Web Apps på Azure stak – kendte problemer og fejlfinding | Microsoft Azure"
    description="Detaljeret vejledning til installation af Web Apps i Azure stak"
    services="azure-stack"
    documentationCenter=""
    authors="apwestgarth"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="app-service"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anwestg"/>
    
# <a name="web-apps-resource-provider---known-issues-and-troubleshooting"></a>Web Apps ressource udbyder – kendte problemer og fejlfinding

> [AZURE.NOTE] Følgende oplysninger gælder kun for Azure stak TP1 installationer.

Se vejledningen nedenfor, hvis du oplever problemer under installation af eller arbejde med Web Apps på Azure stablen.

## <a name="azure-stack-web-apps-not-available-in-the-portal"></a>Azure stak Web Apps ikke tilgængelig i portalen

![Azure stak Webapps oprette WebApp][1]

Når du har fuldført [registrering af provideren Azure stak Web Apps](azure-stack-webapps-deploy.md#register-the-newly-deployed-azure-stack-web-apps-provider-with-arm) , hvis du ikke kan finde Web + Mobile blade derefter kan du prøve følgende:
* **Log af portalen** og **lukke browseren** og derefter i et **nyt browser-vinduet logon til portalen** og prøve igen.

Hvis du stadig ikke kan se internettet og mobile blade, kan du prøve følgende:

1.  Find **xRPVM** og **oprette forbindelse** til VM på **Azure stak Værtsmaskinen** i **Hyper-V Manager** .
2.  Åbn en **kommandoprompt som Administrator** , og kør **IISRESET**
3.  Gå tilbage til **ClientVM** og åbne et **nyt browservindue**, **Log på portalen** og prøve igen.

## <a name="deployment-fails-when-creating-a-web-app-in-a-new-resource-group"></a>Installation mislykkes, når du opretter en WebApp i en ny ressourcegruppe

I den første udskrift af Web Apps, skal **Alle Onlines** oprettes i den samme ressourcegruppe som Web Apps ressource udbyder (**AppService-LOCAL**).  Dette er et kendt problem og vil blive løst i en fremtidig preview.

## <a name="other-issues"></a>Andre problemer

Hvis du støder på andre problemer med Web Apps på Azure stak skal du skrive i [forummet Azure stak] (https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) hvor medlemmer af vores team overvåger indlæg.


<!--Image references-->
[1]: ./media/azure-stack-webapps-troubleshoot-known-issues/NewWebandMobile.png



<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[WebAppsDeployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525
