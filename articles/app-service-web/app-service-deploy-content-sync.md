<properties
    pageTitle="Synkroniser indhold fra en skyen mappe til Azure App Service"
    description="Lær at implementere din app på Azure App Service via synkronisering af indhold fra en mappe i skyen."
    services="app-service"
    documentationCenter=""
    authors="dariagrigoriu"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/13/2016"
    ms.author="dariagrigoriu"/>
    
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Synkroniser indhold fra en skyen mappe til Azure App Service

Dette selvstudium viser, hvordan du installerer til [Azure App-tjenesten](http://go.microsoft.com/fwlink/?LinkId=529714) ved synkronisering af dit indhold fra populære lagerplads skytjenester som Dropbox og OneDrive. 

## <a name="overview"></a>Oversigt over indhold Synkroniser installation

Synkronisering af indhold efter behov installation er drevet af [Kudu installation program](https://github.com/projectkudu/kudu/wiki) integreret med App Service. [Azure-portalen](https://portal.azure.com), kan du angive en mappe i din skylagring, arbejde med din app kode og indhold i den pågældende mappe og synkronisere til App-tjenesten ved at klikke på en knap. Synkronisering af indhold anvender Kudu processen for at opbygge og installation. 
    
## <a name="contentsync"></a>Sådan aktiveres synkronisering af indhold installation
For at aktivere synkronisering af indhold fra [Azure-portalen](https://portal.azure.com)skal du følge disse trin:

1. Klik på **Indstillinger**i din app blade i portalen Azure, > **Installation kilde**. Klik på **Vælg kilde**og derefter vælge **OneDrive** eller **Dropbox** som kilde til installation. 

    ![Synkronisering af indhold](./media/app-service-deploy-content-sync/deployment_source.png)

    >[AZURE.NOTE] **OneDrive for Business** understøttes ikke på grund af underliggende forskelle i API'erne på nuværende tidspunkt. 

2. Fuldfør arbejdsprocessen godkendelse for at aktivere App Service til at få adgang til en bestemt foruddefinerede udpegede sti til OneDrive eller Dropbox, hvor alle dine App Service indhold skal lagres.  
    Når du har tilladelse til App-tjenesten giver platform dig mulighed for at oprette en indhold mappe under udpegede indhold stien eller vælge en eksisterende indhold mappe under denne udpegede indhold sti. De udpegede indhold stier under kontiene skyen lagerplads, der bruges til App Service synkronisering er følgende:  
    * **OneDrive**:`Apps\Azure Web Apps` 
    * **Dropbox**:`Dropbox\Apps\Azure`

3. Synkroniser indhold kan startes on demand fra portalen Azure efter den indledende indhold synkronisering. Oversigt over installation er tilgængelig med bladet **installationer** .

    ![Oversigt over installation](./media/app-service-deploy-content-sync/onedrive_sync.png)
 
Du kan finde flere oplysninger til Dropbox installation er tilgængelig under [Implementer fra Dropbox](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx). 


