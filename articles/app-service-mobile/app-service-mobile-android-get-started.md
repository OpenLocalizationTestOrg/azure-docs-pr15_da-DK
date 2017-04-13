<properties
    pageTitle="Oprette en Android-app på Azure App Service Mobilapps | Microsoft Azure"
    description="Følge dette selvstudium for at komme i gang med at bruge Azure-mobilappen en back-end for Android udvikling"
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

#<a name="create-an-android-app"></a>Oprette en Android-app

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Oversigt

Dette selvstudium viser, hvordan du tilføjer en skybaseret back end-tjeneste til en Android-mobilappen med en Azure-mobilappen backend-version.  Du skal oprette en ny mobilapp back-end såvel en enkel _Opgaveliste listen_ Android-appen, der gemmer app-data i Azure.

Fuldfør dette selvstudium er en betingelse for alle andre Android selvstudier om brug af funktionen Mobile-Apps i Azure App-tjeneste.

## <a name="prerequisites"></a>Forudsætninger

For at fuldføre dette selvstudium skal bruge du følgende:

* [Android-udviklerværktøjer](https://developer.android.com/sdk/index.html), som indeholder det integrerede udviklingsmiljø Android Studio, og den seneste Android platform.
* Azure Mobile Android SDK, som automatisk refereres til som en del af det Hurtig start-projekt, du har hentet.
* En [aktiv Azure-konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-new-azure-mobile-app-backend"></a>Oprette en ny Azure-mobilappen back-end

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="configure-the-server-project"></a>Konfigurere server projektet

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-android-app"></a>Downloade og køre Android-appen

[AZURE.INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
