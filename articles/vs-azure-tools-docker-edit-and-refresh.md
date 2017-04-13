<properties
   pageTitle="Fejlfinding-apps i en lokal Docker objektbeholder | Microsoft Azure"
   description="Lær at ændre en app, der kører på en lokal Docker objektbeholder, opdatere objektbeholderen via Rediger, og Opdater og angive fejlfinding pausepunkter"
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="07/22/2016"
   ms.author="mlearned" />

# <a name="debugging-apps-in-a-local-docker-container"></a>Fejlfinding-apps i en lokal Docker objektbeholder

## <a name="overview"></a>Oversigt
Visual Studio Tools til Docker giver en ensartet måde at udvikle i en og validere dit program lokalt i en Linux Docker beholder.
Du behøver ikke at genstarte objektbeholderen, hver gang du foretager en kode, ændre.
I denne artikel vil illustrere Sådan bruges funktionen "Redigere og opdatere" til at starte en ASP.NET Core Web app i en lokal Docker beholder, Foretag de ønskede ændringer og derefter opdatere browseren for at se ændringerne.
Det kan du også se Sådan indstilles pausepunkter til fejlfinding.

> [AZURE.NOTE] Understøttelse af Windows objektbeholder kommer i en senere version

## <a name="prerequisites"></a>Forudsætninger
Følgende værktøjer skal være installeret.

- [Visual Studio 2015 opdatering 2](https://go.microsoft.com/fwlink/?LinkId=691978)
- Installer [Visual Studio 2015 opdatering 3](https://go.microsoft.com/fwlink/?LinkId=691129)
- [Microsoft ASP.NET Core 1.0 SDK](https://go.microsoft.com/fwlink/?LinkID=809122)

Hvis du vil køre Docker beholdere lokalt, skal du bruge en lokal docker klient.
Du kan bruge den frigivne [Docker værktøjskassen](https://www.docker.com/products/overview#/docker_toolbox) som kræver Hyper-V til at være deaktiveret, eller du kan bruge [Docker for Windows Beta](https://beta.docker.com) som bruger Hyper-V og kræver Windows 10.

Hvis bruger Docker værktøjskasse, skal du [konfigurere Docker klienten](./vs-azure-tools-docker-setup.md)

## <a name="1-create-a-web-app"></a>1. oprette en WebApp

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. Tilføj Docker support

[AZURE.INCLUDE [Add docker support](../includes/vs-azure-tools-docker-add-docker-support.md)]


## <a name="3-edit-your-code-and-refresh"></a>3. redigere din kode og opdatering

Du kan hurtigt navigere ændringer, kan du starte dit program i en objektbeholder, og fortsætte med at foretage ændringer, se dem, som du ville gøre med IIS Express.

1. Konfiguration løsning `Debug` , og tryk på ** &lt;CTRL + F5 >** til at opbygge dit docker billede og køre den lokalt.

    Når billedet objektbeholder er bygget og kører i en Docker objektbeholder, Start Visual Studio Web app i din standardbrowser.
    Hvis du bruger Microsoft Edge-browseren eller ellers har fejl, se [fejlfinding af](vs-azure-tools-docker-troubleshooting-docker-errors.md) afsnittet.

1. Gå til siden om, som er, hvor vi skal foretage vores ændringer.

1. Gå tilbage til Visual Studio og åbne `Views\Home\About.cshtml`.

1. Tilføj følgende HTML-indhold til slutningen af filen, og Gem dine ændringer.

    ```
    <h1>Hello from a Docker Container!</h1>
    ```

1.  Få vist outputvinduet, når den nyeste version af .NET er fuldført, og du kan se disse linjer, skifte tilbage til din browser, og Opdater siden om.

    ```
    Now listening on: http://*:80
    Application started. Press Ctrl+C to shut down
    ```

1.  Dine ændringer, der er anvendt!

## <a name="4-debug-with-breakpoints"></a>4. foretages fejlfinding med pausepunkter

Ofte, skal ændringer yderligere inspektion, udnytte funktionerne fejlfinding af Visual Studio.

1.  Gå tilbage til Visual Studio og åbne`Controllers\HomeController.cs`

1.  Erstat indholdet af metoden About() med følgende:

    ```
    string message = "Your application description page from wthin a Container";
    ViewData["Message"] = message;
    ````

1.  Angive et pausepunkt til venstre for den `string message`... linje.

1.  Tryk på ** &lt;F5 >** at starte fejlfindingen.

1.  Gå til siden om at ramme din pausepunkt.

1.  Skifte til Visual Studio til at få vist pausepunktet, og Undersøg værdien af meddelelse.

    ![][2]

##<a name="summary"></a>Oversigt

Med [Visual Studio 2015 Tools til Docker](https://aka.ms/DockerToolsForVS), kan du få produktivitet arbejde lokalt, med fremstilling realisme af udvikling i en Docker objektbeholder.

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

[Fejlfinding i forbindelse med Visual Studio Docker udvikling](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## <a name="more-about-docker-with-visual-studio-windows-and-azure"></a>Mere om Docker med Visual Studio, Windows og Azure

- [Docker Tools til Visual Studio](http://aka.ms/dockertoolsforvs) - udvikling af din .NET Core kode i en objektbeholder
- [Docker Tools til Visual Studio Team Services](http://aka.ms/dockertoolsforvsts) - opbygge og anvende docker objektbeholdere
- [Docker Tools til Visual Studio-kode](http://aka.ms/dockertoolsforvscode) - sprogtjenester til redigering af docker filer med flere e2e scenarier, der kommer
- [Oplysninger om Windows objektbeholder](http://aka.ms/containers)- Windows Server og nanosystemer serveroplysninger
- [Azure objektbeholder tjenesten](https://azure.microsoft.com/services/container-service/) - [Azure objektbeholder tjenesten indhold](http://aka.ms/AzureContainerService)
-    Flere eksempler på arbejde med Docker, under [arbejde med Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) fra [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 forbinde [demo](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Du kan finde flere Hurtig start-guider fra HealthClinic.biz videoen, [Azure udvikler værktøjer Hurtig start-guider](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).

## <a name="various-docker-tools"></a>Forskellige Docker værktøjer

[Nogle gode docker værktøjer (Steve Lasker blog)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## <a name="good-articles"></a>Gode artikler

[Introduktion til Microservices fra NGINX](https://www.nginx.com/blog/introduction-to-microservices/)

## <a name="presentations"></a>Præsentationer

- [Steve Lasker: VS Live Las Vegas 2016 - Docker e2e](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
- [Introduktion til ASP.NET Core @ opbygge 2016 - sted, hvor du på Demo](https://channel9.msdn.com/Events/Build/2016/B810)
- [Udvikling af .NET apps i beholdere, kanal 9](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png
