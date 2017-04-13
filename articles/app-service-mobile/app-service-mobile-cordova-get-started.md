<properties
    pageTitle="Oprette en Cordova app på Azure App Service Mobilapps | Microsoft Azure"
    description="Følge dette selvstudium for at komme i gang med at bruge Azure-mobilappen en back-end for Apache Cordova udvikling"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""
    tags=""
    keywords="cordova, javascript, mobilklient" />

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

#<a name="create-an-apache-cordova-app"></a>Oprette en Apache Cordova app

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Oversigt

Dette selvstudium viser, hvordan du føjer en skybaseret back end-tjeneste til en Apache Cordova mobilapp ved hjælp af en Azure-mobilappen backend-version.  Du skal oprette en ny mobilapp back-end såvel en enkel _Opgaveliste listen_ Apache Cordova app, der gemmer app-data i Azure.

Fuldfør dette selvstudium er en betingelse for alle andre Apache Cordova selvstudier om brug af funktionen Mobile-Apps i Azure App-tjeneste.

## <a name="prerequisites"></a>Forudsætninger

For at fuldføre dette selvstudium skal bruge du følgende:

* En PC med [Visual Studio Community 2015] eller nyere.
* [Visual Studio-værktøjer til Apache Cordova].
* En [aktiv Azure-konto](https://azure.microsoft.com/pricing/free-trial/).

Du kan også tilsidesætte Visual Studio og bruge kommandolinjen Apache Cordova direkte.  Dette er nyttigt, når du fuldfører selvstudiet på en Mac-computer.  Kompilering Apache Cordova klientprogrammer ved hjælp af kommandolinjen er ikke omfattet af dette selvstudium.

## <a name="create-a-new-azure-mobile-app-backend"></a>Oprette en ny Azure-mobilappen back-end

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

[Se en video, der viser lignende trin](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-1-Create-an-Azure-Mobile-App)

## <a name="configure-the-server-project"></a>Konfigurere server projektet

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Downloade og køre Apache Cordova appen

[AZURE.INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

## <a name="next-steps"></a>Næste trin

Nu hvor du har fuldført dette selvstudium i Hurtig start, skal du gå videre til en af følgende selvstudier:

* [Tilføje godkendelse] til din Apache Cordova app.
* [Tilføje Pushmeddelelser] til din Apache Cordova app.

Lær mere om vigtige begreber med Azure App Service.

* [Godkendelse]
* [Push-beskeder]

Lær at bruge SDK'er.

* [Apache Cordova SDK]
* [ASP.NET Server SDK]
* [Node.js Server SDK]

<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio-værktøjer til Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Tilføje godkendelse]: app-service-mobile-cordova-get-started-users.md
[Tilføje Pushmeddelelser]: app-service-mobile-cordova-get-started-push.md
[Godkendelse]: app-service-mobile-auth.md
[Push-beskeder]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
