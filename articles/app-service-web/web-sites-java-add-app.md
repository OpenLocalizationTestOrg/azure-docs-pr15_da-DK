<properties 
    pageTitle="Tilføje en Java-program til Azure App Service Web Apps" 
    description="Dette selvstudium viser, hvordan du tilføje en side eller et program tilladelse til at din forekomst af Azure App Service Web Apps, der er allerede konfigureret til at bruge Java." 
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
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="add-a-java-application-to-azure-app-service-web-apps"></a>Tilføje en Java-program til Azure App Service Web Apps

Når du har initialiseret din Java online i [Azure App-tjeneste][] , som beskrevet i [oprette en Java WebApp i Azure App-tjeneste](web-sites-java-get-started.md), kan du overføre dit program ved at placere din KRIG i mappen **WebApp** .

Navigation stien til mappen **WebApp** varierer afhængigt af hvordan du kan konfigurere din Web Apps-forekomst.

- Hvis du har oprettet din online ved hjælp af Azure Marketplace, er stien til mappen **WebApp** i formularen **d:\home\site\wwwroot\bin\application\_server\webapps**, hvor **programmet\_server** er navnet på programserveren gælder for din online-forekomst. 
- Hvis du har oprettet din online ved hjælp af Brugergrænsefladen for Azure konfiguration, er stien til mappen **WebApp** i formularen **d:\home\site\wwwroot\webapps**. 

Bemærk, som du kan bruge versionsstyring til at overføre programmet eller websider, herunder [kontinuerlig integrationsmuligheder](app-service-continuous-deployment.md). FTP er også en mulighed for at overføre dine programmer eller websider.

Bemærkning til Tomcat Onlines: Når du har overført KRIG filen til mappen **WebApp** , programserver Tomcat registrerer, at du har tilføjet den, og automatisk indlæser den. Bemærk, at hvis du kopierer filer (foruden KRIG filer) til rodmappen, application server skal genstartes, før filerne, der bruges. Automatisk indlæsning funktionaliteten for Tomcat Java webapps kører på Azure er baseret på en ny KRIG fil føjes, eller nye filer eller mapper, der er føjet til mappen **WebApp** . 

## <a name="next-steps"></a>Næste trin

Du kan finde yderligere oplysninger finder [Java Developer Center](/develop/java/).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- External Links -->
[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
