<properties 
    pageTitle="Azure WebJobs dokumentationsressourcer" 
    description="Anbefalede ressourcer for at lære at bruge Azure WebJobs og Azure WebJobs SDK." 
    services="app-service" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2016" 
    ms.author="tdykstra"/>

# <a name="azure-webjobs-documentation-resources"></a>Azure WebJobs dokumentationsressourcer

## <a name="overview"></a>Oversigt

Dette emne links til dokumentationsressourcer om at bruge Azure WebJobs og Azure WebJobs SDK. Azure WebJobs er en nem måde at køre scripts eller programmer som baggrundsprocesser i forbindelse med en [App Service WebApp, API app eller en mobilapp](../app-service/app-service-value-prop-what-is.md). Du kan overføre og køre en eksekverbar fil som som cmd, bat, exe (.NET), ps1, vi, php, Reg., js og glas. Disse programmer kører som WebJobs på en tidsplan (cron) eller løbende.

Formålet med [WebJobs SDK](websites-webjobs-resources.md) er at forenkle den kode, du skriver til almindelige opgaver, en WebJob kan udføre som billedbehandling, kø behandling, RSS-sammenlægning, vedligeholdelse og sende mails. WebJobs SDK indeholder indbyggede funktioner til at arbejde med Azure-lager og Service Bus til planlægning af opgaver og håndtering af fejl og mange andre almindelige scenarier. Desuden den er udviklet til at være extensible, og der er en [åbne kilde lager til extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview). [Azure fungerer](../azure-functions/functions-overview.md) (i øjeblikket preview) er baseret på en version af WebJobs SDK, som fungerer med C#-script, Node.js og andre sprog. 

Oprette, installation og administration af WebJobs er problemfri med integreret værktøj i Visual Studio. Du kan oprette WebJobs fra skabeloner, udgive og administrere (Kør/stop/skærm/fejlfinding) dem. 

WebJobs dashboard i portalen Azure indeholder effektive funktioner, der giver dig fuld kontrol over udførelse af WebJobs, herunder også muligheden for at aktivere bestemte funktioner i WebJobs. Dashboard viser også kørsel af funktionen og logføring af output. 

##<a name="getstarted"></a>Introduktion til WebJobs og WebJobs SDK

* [Introduktion til Azure WebJobs](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
* [Azure WebJobs er telefonbog, og du skal begynde at bruge dem lige nu!](http://www.troyhunt.com/2015/01/azure-webjobs-are-awesome-and-you.html) (Blogindlæg af Troy Hunt.)
* [Azure WebJobs funktioner](/blog/2014/10/22/webjobs-goes-into-full-production/)
* [Hvad er WebJobs SDK](websites-dotnet-webjobs-sdk.md)
* [Baggrund job vejledning ved Microsoft mønstre og fremgangsmåder](/documentation/articles/best-practices-background-jobs/)
* [Præsentation af 1.1.0 RTM af Microsoft Azure WebJobs SDK](/blog/azure-webjobs-sdk-1-1-0-rtm/)
* [Introduktion til Azure WebJobs SDK](websites-dotnet-webjobs-sdk-get-started.md)
* [Sådan bruger du Azure kø lagerplads med WebJobs SDK](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)
* [Sådan bruger du Azure blob-lager med WebJobs SDK](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
* [Sådan bruger du Azure-tabellager med WebJobs SDK](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)
* [Sådan bruger du Azure Service Bus med WebJobs SDK](websites-dotnet-webjobs-sdk-service-bus.md)
* [Sådan bruges WebHooks med WebJobs SDK, med eksempler på GitHub, IFTTT og HTTP](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/WebHooks-Walkthrough)
* [Azure WebJobs SDK oversigtsvejledning (PDF-overførsel)](http://go.microsoft.com/fwlink/?LinkID=524028&clcid=0x409)
* [WebJobs indstillinger dokumentation i GitHub](https://github.com/projectkudu/kudu/wiki/Web-jobs).
* Videoer
    * [WebJobs og WebJobs SDK](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
    * [Azure WebJobs video serie på kanal 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)
    * [Introduktion til WebJobs værktøjer til Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
    * [WebJobs værktøj og ekstern fejlfinding](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)
    * [Azure WebJobs Update ved hjælp af Pranav Rastogi - udvidelsesmuligheder af i version 1.1](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-183-Azure-WebJobs-Update-with-Pranav-Rastogi)

Se også i de følgende afsnit på [Implementering af WebJobs](#deploy) og [tester og foretage fejlfinding af WebJobs](#debug).

##<a name="deploy"></a>Implementere WebJobs

* [Sådan installerer Azure WebJobs ved hjælp af Visual Studio](websites-dotnet-deploy-webjobs.md)
* [Sådan installeres WebJobs ved hjælp af portalen Azure](web-sites-create-web-jobs.md)
* [Aktivering kommandolinjeparametre eller fortløbende leveringen af Azure WebJobs](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [Ciffer anvender en .NET console-app for Azure ved hjælp af WebJobs](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/)
* [Installation af en F # WebJob til Azure](http://blogs.msdn.com/b/dave_crooks_dev_blog/archive/2015/02/18/deploying-f-web-job-to-azure.aspx)
* [Installation af brugerdefineret services som Azure Webjobs](http://withouttheloop.com/articles/2015-06-23-deploying-custom-services-as-azure-webjobs/)
* Videoer
    * [Introduktion til WebJobs værktøjer til Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
    * [WebJobs værktøj og ekstern fejlfinding](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="schedule"></a>Planlægge WebJobs

* [Den Azure WebJob dialogboksen Tilføj](websites-dotnet-deploy-webjobs.md#configure)
* [Oprette en planlagt WebJob i portalen Azure](web-sites-create-web-jobs.md#CreateScheduled)
* [Tilslutte en scheduler kørsel til en WebJob](http://blog.davidebbo.com/2015/05/scheduled-webjob.html)
* [Planlægge Azure WebJobs med cron udtryk](http://blog.amitapple.com/post/2015/06/scheduling-azure-webjobs/)
* [Planlægning individuelle WebJob funktioner ved hjælp af WebJobs SDK TimerTrigger](websites-dotnet-webjobs-sdk.md#schedule)

##<a name="debug"></a>Test og fejlfinding WebJobs

* [Ny udvikler og fejlfinding funktioner til Azure WebJobs i Visual Studio](http://blogs.msdn.com/b/webdev/archive/2014/11/12/new-developer-and-debugging-features-for-azure-webjobs-in-visual-studio.aspx)
* [Få vist WebJobs Dashboard](websites-dotnet-webjobs-sdk-get-started.md#view-the-webjobs-sdk-dashboard)
* [Hvordan du kan skrive logfiler ved hjælp af WebJobs SDK og få dem vist i dashboardet](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)
* [Remote fejlfinding WebJobs](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebugwj)
* [Hvem der har skrevet, blob?](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [Vært interaktive kode i skyen](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [Tilføje sporing til Azure WebJobs](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [Overvåge, diagnosticere og fejlfinding i forbindelse med Microsoft Azure-lager](../storage/storage-monitoring-diagnosing-troubleshooting.md)
* Videoer
    * [WebJobs værktøj og ekstern fejlfinding](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="scale"></a>Skalering WebJobs

* [Skalering dit webprogram med Azure websteder](http://msdn.microsoft.com/magazine/dn786914.aspx)
* [Azure App Service: planlagde arkitektur Massive skala klar For Business Webapps](https://channel9.msdn.com/Events/Build/2014/3-626). Omslag skalering af webapps med WebJobs, herunder WebJobs SDK.
* Videoer
    * [Skalering af WebJobs](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)

##<a name="additional"></a>Yderligere WebJobs ressourcer

* [Azure WebJobs GA blogindlæg af Magnus Mårtensson](http://magnusmartensson.com/azure-webjobs-ga)
* [Kører Powershell Web job på Azure App Service](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [Få besked, når din Azure udløses WebJobs er fuldført](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [Enkel Web App sikkerhedskopi opbevaringspolitik med WebJobs](https://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [Azure Webapps og Cloud Services langsomt på første anmodning](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/). Viser, hvordan du bruger WebJobs til at efterligne funktionen AlwaysOn, der er kun tilgængelig for det Standard priser niveau.
* [WebJobs lukning](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl). For WebJobs SDK lukning Se [lukning](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful).)
* [Automatisere sikkerhedskopier med Azure WebJobs & AzCopy](http://markjbrown.com/azure-webjobs-azcopy/)
* Videoer
    * [Azure WebJobs videoer efter Magnus Mårtensson](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
    * [Azure WebJobs video serie på kanal 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="additionalsdk"></a>Yderligere WebJobs SDK ressourcer

* [Produktbemærkninger til WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes)
* [WebJobs SDK-kildekode](https://github.com/Azure/azure-webjobs-sdk)
* [WebJobs SDK filtypenavne kildekode](https://github.com/Azure/azure-webjobs-sdk-extensions), med [detaljeret vejledning til udvidelsesmuligheder af modellen](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview).  
* [WebJobs SDK Script-kildekode](https://github.com/Azure/azure-webjobs-sdk-script/) (bruges til [Azure funktioner](../azure-functions/functions-overview.md))
* [WebJob til at overføre FREB filer til Azure-lager ved hjælp af WebJobs SDK](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [Vært Azure webjobs uden for Azure, hostet med logføring fordelene fra en Azure webjob](http://bypassion.dk/?p=510)
* [Opbygge et værktøj til Import af Data med Azure WebJobs](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* [Oversigt over Azure funktioner](../azure-functions/functions-overview.md)
* Videoer
    * [Azure WebJobs video serie på kanal 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="samples"></a>Eksempel på WebJob programmer

* [Eksempler på programmer leveres af WebJobs teamet på GitHub](https://github.com/azure/azure-webjobs-sdk-samples)
* [Enkel Azure Web App med WebJobs Backend ved hjælp af WebJobs SDK](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77). Demonstrerer anvendelse af planlagte og hændelsesstyret WebJobs. Kan du se blogindlægget [genopbygge den ved hjælp af Azure WebJobs SDK SiteMonitR](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs).

##<a name="blogs"></a>Blogge

* [Azure-blog](/blog)
* [Amit Apples blog](http://blog.amitapple.com/). Fokuserer på WebJobs (ikke SDK).
* [Magnus Mårtensson blog](http://magnusmartensson.com/)

##<a name="gethelp"></a>Få hjælp til WebJobs

* [StackOverflow for WebJobs](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [StackOverflow for WebJobs SDK](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [StackOverflow til Azure-funktioner](http://stackoverflow.com/questions/tagged/azure-functions)
* [Azure og ASP.NET-forum](http://forums.asp.net/1247.aspx)
* [Azure-App-tjenesten Web Apps-forum](http://social.msdn.microsoft.com/Forums/azure/home?forum=windowsazurewebsitespreview)
* [Azure Web Apps bruger tale-websted](https://feedback.azure.com/forums/169385-websites/)
* [Twitter](http://twitter.com/). Brug mærke #AzureWebJobs.
* [Rapportere en WebJobs fejl eller en problemliste](https://github.com/projectkudu/kudu/wiki/Reporting-WebJobs-issues)

