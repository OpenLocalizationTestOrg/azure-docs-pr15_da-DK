<properties
    pageTitle="Oprette en Java WebApp i Azure App Service | Microsoft Azure"
    description="Dette selvstudium viser, hvordan du installere en Java WebApp til Azure App-tjenesten."
    services="app-service\web"
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="get-started-article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="create-a-java-web-app-in-azure-app-service"></a>Oprette en Java WebApp i Azure App Service

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Dette selvstudium viser, hvordan du opretter en Java [WebApp i Azure App Service] ved hjælp af [Azure-portalen]. Azure portalen har en grænseflade, du kan bruge til at administrere dine Azure ressourcer.

> [AZURE.NOTE] For at fuldføre dette selvstudium skal bruge du en Microsoft Azure-konto. Hvis du ikke har en konto, kan du [aktivere dit Visual Studio abonnement fordele] eller [Tilmeld dig en gratis prøveversion].
>
> Hvis du vil Introduktion til Azure App Service, før du tilmelder dig en Azure-konto, skal du gå til [Prøve App Service]. Der, skal kan du straks oprette en forbigående starter WebApp i App Service ingen kreditkort er påkrævet, og ingen forpligtelser.

## <a name="java-application-options"></a>Java programindstillinger

Der er flere måder, du kan konfigurere en Java-programmet i en App Service WebApp. 

1. Oprette en app, og derefter konfigurere **Programindstillinger**.

    App-tjenesten giver flere Tomcat og Jetty versioner standardkonfiguration. Hvis det program, du vil vært for fungerer sammen med en af de indbyggede versioner, denne metode til at konfigurere en web objektbeholder er den nemmeste og er perfekt, når alt, du vil gøre, er overføre en krig fil til en web-objektbeholder. For denne metode kan du oprette en app på portalen Azure, og gå derefter til bladet **Programindstillinger** for din app til at vælge din version af Java sammen med den ønskede Java web beholder. Når du bruger denne metode køres både Java og din web objektbeholder fra programfiler. De andre metoder læg objektbeholderen web og potentielt JVM i din diskplads. Når du bruger denne model, kan du ikke har adgang til at redigere filer i denne webdel af typen af filsystem. Det betyder, at du ikke kan gøre ting som konfigurere filen *server.xml* eller Placer biblioteksfiler i mappen */lib* . Kan finde flere oplysninger i den [oprette og konfigurere en Java WebApp](#appsettings) afsnittet senere i dette selvstudium.
    
2. Bruge en skabelon fra Azure Marketplace.

    Azure Marketplace indeholder skabeloner, automatisk at oprette og konfigurere Java webapps med Tomcat eller Jetty web beholdere. Web beholdere, skabelonerne opretter kan konfigureres. Du kan finde flere oplysninger i afsnittet [Brug en Java skabelon fra Azure Marketplace](#marketplace) i dette selvstudium.
  
3. Oprette en app og derefter kopiere manuelt og redigere konfigurationsfiler 

    Du muligvis vil være vært for en brugerdefineret Java-program, der ikke implementerer i en af de web beholdere, der leveres af App Service. Eksempel:
    
    * Java programmet kræver en version af Tomcat eller Jetty, der ikke understøttes af App Service direkte eller angivet i galleriet.
    * Java-program tager HTTP-anmodninger og implementerer ikke som en KRIG til en eksisterende web beholder.
    * Du vil konfigurere objektbeholderen web fra bunden dig selv. 
    * Du vil bruge en version af Java, der ikke understøttes i App-tjeneste og vil overføre det selv.

    I tilfælde som disse, kan du oprette en ved hjælp af portalen Azure-app, og giv dernæst de relevante runtime-filer manuelt. I dette tilfælde filerne, der skal tælles mod din kvoter for lagerplads plads for din App Service-plan. Yderligere oplysninger finder du se [overføre en brugerdefineret Java WebApp til Azure].

## <a name="portal"></a>Oprette og konfigurere en Java WebApp

I dette afsnit viser, hvordan du opretter en WebApp og konfigurere det til Java ved hjælp af bladet **Programindstillinger** af portalen.

1. Log på [Azure-portalen].

2. Klik på **Ny > Web + Mobile > Web App**.

    ![Ny WebApp][newwebapp]

4. Angiv et navn til WebApp i feltet **WebApp** .

    Dette navn skal være entydige i azurewebsites.net domæne, fordi URL-adressen til WebApp bliver {name}. azurewebsites.net. Hvis det navn, du angiver ikke er entydige, vises et rødt udråbstegn i tekstfeltet.

5. Vælg en **Ressourcegruppe** eller Opret en ny.

    Finde flere oplysninger om ressourcegrupper, [ved hjælp af portalen Azure for at administrere dine Azure ressourcer].

6. Vælg en **App Service plan/placering** eller Opret en ny.

    Se [Oversigt over Azure App Service planer], du kan finde flere oplysninger om App Service planer.

7. Klik på **Opret**.

    ![Oprette WebApp][newwebapp2]
 
8. Når webappen er blevet oprettet, skal du klikke på **Web Apps > {din online}**.
 
    ![Vælg WebApp][selectwebapp]

9. Klik på **Indstillinger**i bladet **WebApp** .

10. Klik på **Indstillinger for et webprogram**.

11. Vælg den ønskede **Java version**. 

12. Vælg den ønskede **Java underordnet version**. Hvis du vælger **nyeste**, bruge din app den nyeste underordnede version, som fås i App-tjeneste til den pågældende Java overordnet version. Det **nyeste** element er entydige for Java-apps, der er oprettet ud fra **Programindstillinger**. Hvis du opretter din Java-app fra galleriet, har du til at administrere dine egne objektbeholder og JVM ændringer. 

12. Vælg den ønskede **Web objektbeholder**. Hvis du vælger en objektbeholder navn, der starter med **nyeste**, holdes din app til den nyeste version af den web objektbeholder overordnet version, som fås i App-tjeneste. 

    ![Web objektbeholder versioner][versions]

13. Klik på **Gem**.

    I et øjeblik, så din online bliver Java-baserede og konfigureret til at bruge objektbeholderen web, du har valgt.

14. Klik på **Web apps > {din nye online}**.

15. Klik på **URL-adressen** til at finde frem til det nye websted.

    Websiden bekræfter, at du har oprettet en Java-baserede web-app.

## <a name="marketplace"></a>Brug en skabelon på Java fra Azure Marketplace

Dette afsnit viser, hvordan du bruger Azure Marketplace til at oprette en Java WebApp. Den samme generelle strøm kan også bruges til at oprette en Java-baserede mobile eller API app. 

1. Log på [Azure-portalen]

2. Klik på **Ny > Marketplace**.

    ![Ny Marketplace][newmarketplace]

3. Klik på **Web + Mobile**.

    Du skal muligvis rulle til venstre for at få vist bladet **Marketplace** , hvor du kan vælge **Web + Mobile**.

4. Skriv navnet på en Java application server, som **Apache Tomcat** eller **Jetty**, i søgefeltet, og tryk derefter på Enter.

5. Klik på programserver Java i søgeresultaterne.

    ![Web Mobile Jetty][webmobilejetty]

6. Klik på **Opret**i den første **Apache Tomcat** eller **Jetty** blade.

    ![Modtageanlæg portalen Blade][jettyblade]

7. Angiv et navn til web app i feltet **WebApp** i den næste **Apache Tomcat** eller **Jetty** blade.

    Dette navn skal være entydige i azurewebsites.net domæne, fordi URL-adressen til WebApp bliver {name}. azurewebsites.net. Hvis det navn, du angiver ikke er entydige, vises et rødt udråbstegn i tekstfeltet.

8. Vælg en **Ressourcegruppe** eller Opret en ny.

    Finde flere oplysninger om ressourcegrupper, [ved hjælp af portalen Azure for at administrere dine Azure ressourcer].

9. Vælg en **App Service plan/placering** eller Opret en ny.

    Se [Oversigt over Azure App Service planer], du kan finde flere oplysninger om App Service planer.

10. Klik på **Opret**.

    ![Oprette modtageanlæg Portal][jettyportalcreate2]

    I et øjeblik, typisk mindre end et minut afsluttes Azure oprettelse af nye WebApp.

11. Klik på **Web apps > {din nye online}**.

12. Klik på **URL-adressen** til at finde frem til det nye websted.

    ![Modtageanlæg URL-adresse][jettyurl]

    Tomcat leveres med et standardsæt af sider. Hvis du vælger Tomcat, skal du se en side, der ligner følgende eksempel.

    ![Online ved hjælp af Apache Tomcat][tomcat]

    Hvis du vælger Jetty, skal du se en side, der ligner følgende eksempel. Jetty har ikke et standardsæt side, så den samme JSP, der bruges til et tomt websted Java genbruges her.

    ![Online ved hjælp af Jetty][jetty]

Nu hvor du har oprettet WebApp med en app beholder, i afsnittet [Næste trin](#next-steps) for at få oplysninger om, hvordan du overføre dit program til WebApp.

## <a name="next-steps"></a>Næste trin

På dette tidspunkt, har du en Java application server, der kører i din online i Azure App-tjeneste. Du skal installere din egen programkode web App, se [tilføje et program eller webside til din Java WebApp].

Du kan finde flere oplysninger om udviklingsprogrammer Java i Azure, [Java Developer Center].

<!-- URL List -->

[Føje et program eller en webside til din Java WebApp]: ./web-sites-java-add-app.md
[Azure App Service planer oversigt]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[Azure-portalen]: https://portal.azure.com/
[aktivere dine Visual Studio abonnement fordele]: http://go.microsoft.com/fwlink/?LinkId=623901
[tilmelde dig en gratis prøveversion]: http://go.microsoft.com/fwlink/?LinkId=623901
[Prøv App Service]: http://go.microsoft.com/fwlink/?LinkId=523751
[WebApp i Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Java Developer Center]: /develop/java/
[Ved hjælp af portalen Azure til at administrere dine Azure ressourcer]: ../azure-portal/resource-group-portal.md
[Overføre en brugerdefineret Java WebApp til Azure]: ./web-sites-java-custom-upload.md

<!-- IMG List -->

[newwebapp]: ./media/web-sites-java-get-started/newwebapp.png
[newwebapp2]: ./media/web-sites-java-get-started/newwebapp2.png
[selectwebapp]: ./media/web-sites-java-get-started/selectwebapp.png
[versions]: ./media/web-sites-java-get-started/versions.png
[newmarketplace]: ./media/web-sites-java-get-started/newmarketplace.png
[webmobilejetty]: ./media/web-sites-java-get-started/webmobilejetty.png
[jettyblade]: ./media/web-sites-java-get-started/jettyblade.png
[jettyportalcreate2]: ./media/web-sites-java-get-started/jettyportalcreate2.png
[jettyurl]: ./media/web-sites-java-get-started/jettyurl.png
[tomcat]: ./media/web-sites-java-get-started/tomcat.png
[jetty]: ./media/web-sites-java-get-started/jetty.png
