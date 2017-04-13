<properties
    pageTitle="Kontinuert installation til Azure App Service | Microsoft Azure"
    description="Lær, hvordan du aktiverer fortløbende installation til Azure App-tjenesten."
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
    ms.date="10/28/2016"
    ms.author="dariagrigoriu"/>
    
# <a name="continuous-deployment-to-azure-app-service"></a>Kontinuert installation til Azure App-tjenesten

Dette selvstudium viser, hvordan du konfigurerer en fortløbende installation arbejdsgangen for din [Azure App Service] -app. App Service integration med BitBucket, GitHub og Visual Studio Team Services (VSTS) gør det muligt for en fortløbende installation arbejdsproces, hvor Azure trækker i de seneste opdateringer fra dit projekt, der er publiceret på en af disse tjenester. Kontinuert installation er en god indstilling for de projekter, hvor flere og hyppige bidrag integreres.

## <a name="overview"></a>Aktivere fortløbende installation

Aktivere fortløbende installation 

1. Udgive din app-indhold til lager, der skal bruges til fortløbende installation.  
    Se [oprette en repo (GitHub)], [Opret en repo (BitBucket)]og [komme i gang med VSTS]kan finde flere oplysninger om at publicere dit projekt til disse tjenester.

2. Klik på i din app-menuen blade i [Azure-portalen], **APPINSTALLATION > installationsindstillinger**. Klik på **Vælg kilde**og derefter vælge installation kilden.  

    ![](./media/app-service-continuous-deployment/cd_options.png)
    
    > [AZURE.NOTE] Hvis du vil konfigurere en VSTS konto til App Service installation skal du se dette [selvstudium](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).
    
3. Fuldfør arbejdsprocessen for godkendelse. 

4. Vælg project og gren til at udrulle fra i bladet **installation kilde** . Når du er færdig, skal du klikke på **OK**.
  
    ![](./media/app-service-continuous-deployment/github_option.png)

    > [AZURE.NOTE] Når du aktiverer fortløbende-installation med GitHub eller BitBucket, vises både offentlige og private projekter.

    App Service opretter en tilknytning til den valgte lager, trækker i filerne fra den angivne gren og vedligeholder en kopi af din lager til din App Service-app. Når du konfigurerer VSTS fortløbende installation fra Azure-portalen, integrationen bruger App Service [Kudu installation program](https://github.com/projectkudu/kudu/wiki), som allerede automatisere build og installation opgaver med hver `git push`. Du behøver ikke at konfigurere separat fortløbende installation i VSTS. Når denne proces er fuldført, vises bladet **installationsindstillinger** app en aktiv installation, der angiver installationen er fuldført.

5. Klik på **URL-adressen** øverst på den app blade i Azure-portalen for at bekræfte appen er installeret. 

6. Skubbe en ændring i lageret for at bekræfte, at bliver til virkelighed fortløbende installation fra lager efter eget valg. Din app bør opdatere afspejler ændringerne, umiddelbart efter opslagsnål til lagringsstedet er fuldført. Du kan kontrollere, at det har trukket i Opdater i bladet **installationsindstillinger** af din app.

## <a name="VSsolution"></a>Kontinuert installation af en Visual Studio-løsning 

Skubbe en løsning i Visual Studio til Azure App-tjenesten er lige så nemt som at trykke på en enkelt index.html fil. Installationsprocessen App Service strømliner alle detaljer, herunder gendannelse NuGet afhængigheder og opbygning af de binære filer programmet på computeren. Du kan følge kilde kontrolelement bedste praksis for vedligeholde kode kun i din ciffer lager, og fortæl App Service installation tager sig af resten.

Trinnene for at placere din løsning i Visual Studio til App-tjenesten er de samme som i [forrige afsnit](#overview), hvis du konfigurerer din løsning og lager på følgende måde:

-   Brug indstillingen Visual Studio kilde kontrolelementet til at oprette en `.gitignore` , når billedet nedenfor eller manuelt tilføje en `.gitignore` fil i dit lager rod med indhold, der ligner denne [.gitignore eksempel](https://github.com/github/gitignore/blob/master/VisualStudio.gitignore). 

    ![](./media/app-service-continuous-deployment/VS_source_control.png)
 
-   Tilføje hele løsningen mappetræ til dit lager, med filen .sln på rod-lager.

Når du har oprettet dit lager som beskrevet og konfigureret din app i Azure til fortløbende udgivelse fra en af de online ciffer typer lagre, kan du udvikle dine ASP.NET-program lokalt i Visual Studio og installere løbende din kode blot ved at trykke på dine ændringer til din online ciffer lager.

## <a name="disableCD"></a>Deaktivere fortløbende installation

Sådan deaktiveres kontinuerlig installation 

1. Klik på i din app-menuen blade i [Azure-portalen], **APPINSTALLATION > installationsindstillinger**. Klik derefter på **Afbryd forbindelse** i bladet **installationsindstillinger** .

    ![](./media/app-service-continuous-deployment/cd_disconnect.png)    

2. Når du besvarer **Ja** til bekræftelsesmeddelelsen, kan du vende tilbage til din app blade og klikke på **APPINSTALLATION > installationsindstillinger** Hvis du vil konfigurere publicering fra en anden kilde.

## <a name="additional-resources"></a>Yderligere ressourcer

* [Sådan Undersøg almindelige problemer med kontinuert installation](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Sådan bruger du PowerShell til Azure]
* [Hvordan du kan bruge værktøjerne Azure kommandolinjeparametre til Mac og Linux]
* [Ciffer dokumentation]
* [Project Kudu](https://github.com/projectkudu/kudu/wiki)

>[AZURE.NOTE] Hvis du vil komme i gang med Azure App Service før tilmelding til en Azure-konto, skal du gå til [Prøve App Service](http://go.microsoft.com/fwlink/?LinkId=523751), hvor du straks kan oprette en forbigående starter WebApp i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.

[Azure App Service]: https://azure.microsoft.com/en-us/documentation/articles/app-service-changes-existing-services/ 
[Azure-portalen]: https://portal.azure.com
[VSTS Portal]: https://www.visualstudio.com/en-us/products/visual-studio-team-services-vs.aspx
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Sådan bruger du PowerShell til Azure]: ../articles/powershell-install-configure.md
[Hvordan du kan bruge værktøjerne Azure kommandolinjeparametre til Mac og Linux]: ../articles/xplat-cli-install.md
[Ciffer dokumentation]: http://git-scm.com/documentation

[Oprette en repo (GitHub)]: https://help.github.com/articles/create-a-repo
[Oprette en repo (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[Komme i gang med VSTS]: https://www.visualstudio.com/get-started/overview-of-get-started-tasks-vs
[Continuous delivery to Azure using Visual Studio Team Services]: ../articles/cloud-services/cloud-services-continuous-delivery-use-vso.md
