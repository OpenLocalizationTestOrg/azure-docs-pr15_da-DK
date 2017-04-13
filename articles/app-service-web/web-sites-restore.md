<properties 
    pageTitle="Gendanne en app i Azure" 
    description="Lær, hvordan du kan gendanne din app fra en sikkerhedskopi." 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/06/2016" 
    ms.author="cephalin"/>

# <a name="restore-an-app-in-azure"></a>Gendanne en app i Azure

I denne artikel beskrives, hvordan du kan gendanne en app i [Azure App Service](../app-service/app-service-value-prop-what-is.md) , som du tidligere har sikkerhedskopieret (se [Sikkerhedskopiér din app i Azure](web-sites-backup.md)). Du kan gendanne din app med dens sammenkædede databaser (SQL-Database eller MySQL) efter behov i en tidligere tilstand, eller Opret en ny app, der er baseret på en af din oprindelige app sikkerhedskopi. Oprette en ny app, der kører parallelt til den nyeste version kan være nyttige til A / B test.

Gendanne fra sikkerhedskopier er tilgængelig til apps, der kører i **Standard** - og **Premium** lag. Finde oplysninger om skalering af din app, [skalere op en app i Azure](web-sites-scale.md). **Premium** niveau giver mulighed for et større antal daglige sikkerhedskopier udføres end **Standard** niveau.

<a name="PreviousBackup"></a>
## <a name="restore-an-app-from-an-existing-backup"></a>Gendanne en app fra en eksisterende sikkerhedskopi

1. Klik på **Sikkerhedskopier** for at få vist bladet **Sikkerhedskopier** bladet **Indstillinger** af din app i portalen Azure. Klik derefter på **Gendan nu** i kommandolinjen. 
    
    ![Vælg Gendan nu][ChooseRestoreNow]

3. I bladet **gendanne** først vælge sikkerhedskopiering kilden. 

    ![](./media/web-sites-restore/021ChooseSource.png)
    
    Indstillingen **App sikkerhedskopi** viser alle de eksisterende sikkerhedskopier af den aktuelle app, og du kan nemt vælge en. 
    Indstillingen **lagerplads** kan du vælge en hvilken som helst sikkerhedskopiering ZIP-fil fra en eksisterende konto Azure-lager og beholder i dit abonnement. 
    Hvis du forsøger at gendanne en sikkerhedskopi af en anden app, kan du bruge indstillingen **lagerplads** .

4. Angiv derefter destinationen for app-Gendan i **gendanne destination**.

    ![](./media/web-sites-restore/022ChooseDestination.png)
    
    >[AZURE.WARNING] Hvis du vælger **Overskriv**, slettes alle eksisterende data i din aktuelle app. Før du klikker på **OK**, skal du kontrollere, at det er præcis hvad du vil gøre.
    
    Du kan vælge **Eksisterende App** til at gendanne app sikkerhedskopien til en anden app i samme for gruppen. Før du bruger denne indstilling, bør du allerede har oprettet en anden app i dit ressourcegruppe med samme konfiguration af database til den, der er defineret i app sikkerhedskopien. 
    
5. Klik på **OK**.

<a name="StorageAccount"></a>
## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Hente eller slette en sikkerhedskopi fra en lagerplads konto
    
1. Fra hovedvinduet **Gennemse** blade af Azure-portalen, Vælg **lagerplads konti**.
    
    Der vises der en liste over dine eksisterende lagerplads konti. 
    
2. Vælg kontoen lagerplads, der indeholder den sikkerhedskopi, du vil hente eller slette.
    
    Bladet for kontoen lagerplads vises.

3. Vælg den beholder, du vil i bladet lagerplads accountn
    
    ![Vis objektbeholdere][ViewContainers]

4. Vælg sikkerhedskopifil, du vil hente eller slette.

    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)

5. Klik på **Hent** eller **slette** afhængigt af hvad du vil gøre.  

<a name="OperationLogs"></a>
## <a name="monitor-a-restore-operation"></a>Overvåge en gendannelse
    
1. Hvis du vil se oplysninger om lykkes eller mislykkes gendannelsen app skal du gå til bladet **Overvågningsloggen** på portalen Azure. 
    
    Bladet **overvågningslogge** viser alle dine handlinger, sammen med niveau, status, ressource og detaljer.
    
2. Rul ned til at finde den ønskede gendannelse og klikke på for at markere den.

Bladet detaljer vises de tilgængelige oplysninger i forbindelse med gendannelsen.
    
## <a name="next-steps"></a>Næste trin

Du kan også sikkerhedskopiere og gendanne App Service apps ved hjælp af REST-API (se [Brug RESTEN til at sikkerhedskopiere og gendanne App Service apps](websites-csm-backup.md)).

>[AZURE.NOTE] Hvis du vil komme i gang med Azure App Service før tilmelding til en Azure-konto, skal du gå til [Prøve App Service](http://go.microsoft.com/fwlink/?LinkId=523751), hvor du straks kan oprette en forbigående starter WebApp i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
[StorageAccountFile]: ./media/web-sites-restore/02StorageAccountFile.png
[BrowseCloudStorage]: ./media/web-sites-restore/03BrowseCloudStorage.png
[StorageAccountFileSelected]: ./media/web-sites-restore/04StorageAccountFileSelected.png
[ChooseRestoreSettings]: ./media/web-sites-restore/05ChooseRestoreSettings.png
[ChooseDBServer]: ./media/web-sites-restore/06ChooseDBServer.png
[RestoreToNewSQLDB]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
[NewSQLDBConfig]: ./media/web-sites-restore/08NewSQLDBConfig.png
[RestoredContosoWebSite]: ./media/web-sites-restore/09RestoredContosoWebSite.png
[DashboardOperationLogsLink]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
[ManagementServicesOperationLogsList]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
[DetailsButton]: ./media/web-sites-restore/12DetailsButton.png
[OperationDetails]: ./media/web-sites-restore/13OperationDetails.png
 
