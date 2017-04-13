<properties
    pageTitle="Sådan overføres logik apps til skemaet version 2015-08-01-preview | Microsoft Azure App Service"
    description="Du kan nemt overføre dine logik apps til den nyeste skemaversion. Ved at følge disse trin."
    services="logic-apps"
    documentationCenter=""
    authors="MSFTMAN"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/23/2016"
    ms.author="deonhe"/>

# <a name="how-to-migrate-logic-apps-to-schema-version-2015-08-01-preview"></a>Sådan overføres logik apps til skemaet version 2015-08-01-eksempel

Hvis du vil flytte dine eksisterende logik apps til det nye skema, skal du gøre følgende:  
1. Åbn din app logik i portalen Azure  
2. Klik på Opdater skema:

 ![API-ikon][step1]   
Siden Update skema viser og indeholder et link til et dokument, der indeholder detaljer på forbedringerne i det nye skema: ![API-ikon][step2]

>[AZURE.NOTE] Når du vælger **Opdater skema**, vi automatisk Kør overførsel trinnene og give kode output for dig. Du kan bruge dette til at opdatere definitionen for din, men, skal du sikre, at du har fulgt god metoder som dem, der er beskrevet i afsnittet **bedste fremgangsmåder** .

## <a name="best-practices-when-migrating-your-logic-apps-to-the-latest-schema-version"></a>Bedste fremgangsmåder, når du overfører dine logik apps til den nyeste skemaversion:  

- Kopiere overførte scriptet til en ny logik App - ikke overskrive den gamle en indtil du har afsluttet din test og bekræftet appen overførte fungerer som forventet.
- Teste din logik app **før** sættes i fremstilling
- Når overførslen er fuldført, kan du begynde at opdatere dine logik apps for at bruge [administrerede API'er](./apis-list.md) , hvor det er muligt. For eksempel kan du begynde at bruge Dropbox v2, findes i videst du bruger DropBox v1.


## <a name="whats-next"></a>Hvad skal der ske nu
-  [Lær, hvordan du manuelt overføre dine logik apps](../app-service-logic/app-service-logic-schema-2015-08-01.md)


<!--Icon references-->
[step1]: ./media/connectors-schema-migration/migrateschema1.png
[step2]: ./media/connectors-schema-migration/migrateschema2.png






