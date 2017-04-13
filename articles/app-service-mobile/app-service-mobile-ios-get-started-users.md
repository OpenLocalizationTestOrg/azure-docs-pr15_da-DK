<properties
    pageTitle="Tilføje godkendelse på iOS med Azure Mobile-Apps"
    description="Lær at bruge Azure Mobile-Apps til at godkende brugere af din iOS-mailapp gennem en række forskellige identitetsudbydere, herunder AAD, Google, Facebook, Twitter og Microsoft."
    services="app-service\mobile"
    documentationCenter="ios"
    authors="ysxu"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="add-authentication-to-your-ios-app"></a>Føje godkendelse til din iOS-app

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

I dette selvstudium, kan du tilføje godkendelse til [iOS Hurtig start] projektet ved hjælp af en understøttede identitetsudbyder. Dette selvstudium er baseret på [iOS Hurtig start] -selvstudium, som du skal udføre først.

##<a name="register"></a>Registrere din app til godkendelse og konfigurere tjenesten App

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Begrænse tilladelser på godkendte brugere

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Tryk på **Kør** til at starte appen i Xcode. En undtagelse hæves, fordi app forsøger at få adgang til back-end som en ikke-godkendte bruger, men _TodoItem_ tabel nu kræver godkendelse.

##<a name="add-authentication"></a>Føje godkendelse til app

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]


<!-- URLs. -->

[iOS Hurtig start]: app-service-mobile-ios-get-started.md

[Azure portal]: https://portal.azure.com
