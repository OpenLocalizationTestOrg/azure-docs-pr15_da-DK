<properties
    pageTitle="WebJobs i Azure App-tjeneste"
    description="Lær, hvordan du opretter WebJobs for at køre baggrund test og interagere med tjenester som lager og Service Bus og oprette planlagte opgaver."
    services="app-service"
    documentationCenter=""
    authors="christopheranderson"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/10/2015"
    ms.author="chrande"/>

# <a name="using-webjobs-in-azure-app-service"></a>Brug af WebJobs i Azure App Service

Denne artikel indeholder links til dokumentationsressourcer om at bruge Azure WebJobs og Azure WebJobs SDK. Azure WebJobs giver en nem måde at køre scripts eller programmer som baggrundsprocesser på [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714). Du kan overføre og køre en eksekverbar fil som som cmd, bat, exe (.NET), ps1, vi, php, Reg., js og glas. Disse programmer kører som WebJobs på en tidsplan (cron) eller løbende.

WebJobs SDK gør det nemmere at bruge Azure-lager. WebJobs SDK har en binding og udløser system, der fungerer sammen med Microsoft Azure-lager blob, køer og tabeller samt Bus servicekøer.

Oprette, installation og administration af WebJobs er problemfri med integreret værktøj i Visual Studio. Du kan oprette WebJobs fra skabeloner, udgive og administrere (Kør/stop/skærm/fejlfinding) dem.

WebJobs dashboard i portalen Azure indeholder effektive funktioner, der giver dig fuld kontrol over udførelse af WebJobs, herunder også muligheden for at aktivere bestemte funktioner i WebJobs. Dashboard viser også kørsel af funktionen og logføring af output.

[AZURE.INCLUDE [app-service-blueprint-webjobs](../../includes/app-service-blueprint-webjobs.md)]
