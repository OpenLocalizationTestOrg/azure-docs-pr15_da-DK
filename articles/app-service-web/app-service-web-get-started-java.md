<properties 
    pageTitle="Installer din første Java WebApp til Azure i fem minutter | Microsoft Azure" 
    description="Få mere at vide, hvor nemt det er at køre webapps i App Service ved at installere en eksempel-app. Start hurtigt at gøre reelle udvikling, og få vist resultater med det samme." 
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""
/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/13/2016" 
    ms.author="cephalin"
/>
    
# <a name="deploy-your-first-java-web-app-to-azure-in-five-minutes"></a>Installer din første Java WebApp til Azure i fem minutter

Dette selvstudium hjælper dig med at installere en simpel Java WebApp til [Azure App-tjenesten](../app-service/app-service-value-prop-what-is.md).
Du kan bruge App Service til at oprette webapps, [mobilapp tilbage ender](/documentation/learning-paths/appservice-mobileapps/)og [API apps](../app-service-api/app-service-api-apps-why-best-platform.md).

Du skal: 

- Oprette en web-app i Azure App Service.
- Installere en stikprøve Java-app.
- Se din kode, der kører direkte i fremstilling.

## <a name="prerequisites"></a>Forudsætninger

- Få en FTP-/ FTPS-klient, som [FileZilla](https://filezilla-project.org/).
- Få en Microsoft Azure-konto. Hvis du ikke har en konto, kan du [tilmelde dig en gratis prøveversion](/pricing/free-trial/?WT.mc_id=A261C142F) eller [aktivere dine Visual Studio abonnement fordele](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Du kan [Prøve App Service](http://go.microsoft.com/fwlink/?LinkId=523751) uden en Azure-konto. Oprette en starter app og lege med det til op til en time – ingen kreditkort er påkrævet, ingen forpligtelser.

<a name="create"></a>
## <a name="create-a-web-app"></a>Oprette en WebApp

1. Log på [Azure-portalen](https://portal.azure.com) med din Azure-konto.

2. Klik på **Ny**fra menuen til venstre, > **Web + Mobile** > **Web App**.

    ![](./media/app-service-web-get-started-languages/create-web-app-portal.png)

3. Brug følgende indstillinger for din nye app i bladet app oprettelse af:

    - **App-navn**: Skriv et entydigt navn.
    - **Ressourcegruppe**: Vælg **Opret ny** og navngive ressourcegruppen.
    - **App Service plan/placering**: Klik på den for at konfigurere, og klik derefter på **Opret ny** for at angive det navn, placering og priser niveau i App-serviceaftale. Velkommen til at bruge den **gratis** priser niveau.

    Når du er færdig, skal din app oprettelse af blade se sådan ud:

    ![](./media/app-service-web-get-started-languages/create-web-app-settings.png)

3. Klik på **Opret** nederst. Du kan klikke på **ikonet for øverst for at se status** .

    ![](./media/app-service-web-get-started-languages/create-web-app-started.png)

4. Når installationen er fuldført, skal du se denne meddelelse. Klik på meddelelsen for at åbne din installation blade.

    ![](./media/app-service-web-get-started-languages/create-web-app-finished.png)

5. Klik på **ressourcen** linket for at åbne din nye online blade i bladet **installation lykkedes** .

    ![](./media/app-service-web-get-started-languages/create-web-app-resource.png)

## <a name="deploy-a-java-app-to-your-web-app"></a>Installere en Java-app til din online

Nu, Lad os installere en Java-app til Azure ved hjælp af FTPS.

5. Rul ned til **Programindstillinger** eller søge efter den i bladet web app og derefter klikke på den. 

    ![](./media/app-service-web-get-started-languages/set-java-application-settings.png)

6. Vælg **Java 8** **Java version**, og klik på **Gem**.

    ![](./media/app-service-web-get-started-languages/set-java-application-settings.png)

    Når du får vist meddelelsen **opdateret indstillinger for web app**skal du gå til http://*&lt;Programnavn >*. azurewebsites.net at se standard JSP servlet i aktion.

7. Rul ned til **installation legitimationsoplysninger** eller søge efter det tilbage i bladet web app og derefter klikke på den.

8. Angiv dine legitimationsoplysninger installation, og klik på **Gem**.

7. Klik på **Oversigt**tilbage i bladet web app. Ud for **FTP/installation brugernavn** og **FTPS hostname**, skal du klikke på knappen **Kopiér** for at kopiere disse værdier.

    ![](./media/app-service-web-get-started-languages/get-ftp-url.png)

    Nu er du klar til at udrulle din Java app med FTPS.

8. Log på din Azure online FTP-server ved hjælp af de værdier, du har kopieret i det sidste trin i kunden FTP-/ FTPS. Brug den installation adgangskode, du oprettede tidligere.

    Følgende skærmbillede viser, at logge på ved hjælp af FileZilla.

    ![](./media/app-service-web-get-started-languages/filezilla-login.png)

    Du kan se sikkerhedsadvarsler om ukendte SSL-certifikat fra Azure. Fortsæt, og fortsæt.

9. Klik på [dette link](https://github.com/Azure-Samples/app-service-web-java-get-started/raw/master/webapps/ROOT.war) for at hente KRIG filen til din lokale computer.

9. Gå til **/site/wwwroot/webapps** i det eksterne websted i kunden FTP-/ FTPS, og træk den hentede fil KRIG på din lokale computer til remote mappen.

    ![](./media/app-service-web-get-started-languages/transfer-war-file.png)

    Klik på **OK** for at tilsidesætte filen i Azure.

    >[AZURE.NOTE] I overensstemmelse med Tomcats standardfunktionsmåden, filnavn **ROOT.war** i /site/wwwroot/webapps giver dig online rod (http://*&lt;Programnavn >*. azurewebsites.net), og et filnavn ** * &lt;anyname >*.war** giver dig en navngivet WebApp (http://*&lt;Programnavn >*.azurewebsites.net/*&lt;anyname >*).

Det var det! Java-appen er nu aktiveret direkte i Azure. I din browser skal du gå til http://*&lt;Programnavn >*. azurewebsites.net til at se det i aktion. 

## <a name="make-updates-to-your-app"></a>Foretage opdateringer til din app

Når du har brug at sikre en opdatering, kun overføre den nye KRIG-fil til den samme remote mappe med kunden FTP-/ FTPS.

## <a name="next-steps"></a>Næste trin

[Opret en Java web-app fra en skabelon i Azure Marketplace](web-sites-java-get-started.md#marketplace). Du kan få din egen fuldt ud justerbart Tomcat objektbeholder og få den velkendte Manager UI. 

Foretage fejlfinding af din Azure online, direkte i [IntelliJ](app-service-web-debug-java-web-app-in-intellij.md) eller [Eklipse](app-service-web-debug-java-web-app-in-eclipse.md).

Eller du kan få mere ud af din første online. Eksempel:

- Prøv [andre måder at implementere kode til Azure](../app-service-web/web-sites-deploy.md). 
- Tage dine Azure-app til et højere niveau. Godkende dine brugere. Skala det baseret på behov. Konfigurere påmindelser nogle ydeevne. Alle med nogle få klik. Se [tilføje funktioner til dit første WebApp](app-service-web-get-started-2.md).

