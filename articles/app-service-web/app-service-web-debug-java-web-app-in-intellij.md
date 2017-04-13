<properties 
    pageTitle="Fejlfinding af en Java WebApp på Azure i IntelliJ | Microsoft Azure" 
    description="Dette selvstudium viser, hvordan du bruger Azure-værktøjet til IntelliJ til at foretage fejlfinding af en Java Web App, der kører på Azure." 
    services="app-service\web" 
    documentationCenter="java" 
    authors="selvasingh" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="09/20/2016" 
    ms.author="asirveda;robmcm"/>

# <a name="debug-a-java-web-app-on-azure-in-intellij"></a>Fejlfinding af en Java WebApp på Azure i IntelliJ

Dette selvstudium viser, hvordan du udfører fejlfinding en Java Web App, der kører på Azure ved hjælp af [Azure-værktøjskassen til IntelliJ]. Af hensyn til enkel, du vil bruge et eksempel på grundlæggende Java Server side (JSP) til dette selvstudium, men trinnene ville være tilsvarende for en Java servlet, når du foretager fejlfinding på Azure.

Når du har fuldført dette selvstudium, ser dit program svarende til følgende illustration, når du foretager fejlfinding i IntelliJ:

![][01]
 
## <a name="prerequisites"></a>Forudsætninger

* En Java udvikler Kit (JDK), v 1,8 eller nyere.
* IntelliJ ide Ultimate Edition. Dette kan hentes fra <https://www.jetbrains.com/idea/download/index.html>.
* En fordeling af en Java-baseret webserver eller programserver som Apache Tomcat eller Jetty.
* Et Azure abonnement, som kan hentes fra <https://azure.microsoft.com/en-us/free/> eller <http://azure.microsoft.com/pricing/purchase-options/>.
* Azure værktøjskassen til IntelliJ. Du kan finde flere oplysninger, kan du se [installere Azure-værktøjskassen til IntelliJ].
* Et dynamisk webprojekt oprettet og implementeret til Azure App Service; for eksempel se [oprette en Hej verden Web App til Azure i IntelliJ].

## <a name="to-debug-a-java-web-app-on-azure"></a>Foretage fejlfinding af en Java WebApp på Azure

For at fuldføre disse trin i dette afsnit, du kan bruge en eksisterende dynamisk webprojekt, du allerede har installeret som en Java Web App på Azure, du henter et [Eksempel dynamisk Web Project] og følge trinnene i [oprette en Hej verden Web App til Azure i IntelliJ] til at udrulle på Azure. 

1. Åbne projektet Java Web App, du har installeret til Azure i IntelliJ.

1. Klik på menuen **Kør** , og klik derefter på **Rediger konfigurationer**.

    ![][02]

1. Når åbner dialogboksen **Kør/fejlfinding konfigurationer** : 

    1. Vælg **Azure WebApp**.
    1. Klik på **+** at tilføje en ny konfiguration.
    1. Angiv et **navn** til konfigurationen.
    1. Acceptere de resterende standardværdier, der er foreslået af Azure-værktøjskassen, og klik derefter på **OK**.

        ![][03]

1. Vælg den Azure online fejlfinding konfiguration, som du lige har oprettet i øverste højre hjørne af menuen, og klik på **fejlfinding**

    ![][04]

1. Når du bliver bedt om at **aktivere ekstern fejlfinding i remote online nu?**, skal du klikke på **OK**.

1. Når du bliver bedt om **din online er nu klar til ekstern fejlfinding**, skal du klikke på **OK**.

    ![][05]

1. Vælg den Azure online fejlfinding konfiguration, du lige har oprettet i øverste højre hjørne af menuen, og klik på **fejlfinding**.

1. En Windows-kommandoprompt eller Unix shell åbnes og forberede nødvendige forbindelse til fejlfinding. skal du vente, indtil der oprettes forbindelse til din remote Java online, før du fortsætter. Hvis du bruger Windows, ser det sådan i følgende illustration:

    ![][06]

1. Indsætte et linjeskift punkt i siden JSP, og derefter åbne URL-adressen til dit Java Web App i en browser:

    1. Åbn **Azure Explorer** i IntelliJ.
    1. Gå til **Web Apps** og Webapp'en Java, du vil foretage fejlfinding.
    1. Højreklik på den Web App, og klik på **Åbn i Browser**.
    1. IntelliJ træder nu i fejlsikret tilstand.

## <a name="next-steps"></a>Næste trin

Du kan finde flere oplysninger om brug af Azure med Java, [Azure Java Developer Center].

Du kan finde flere oplysninger om oprettelse af Azure Web Apps [Web Apps oversigt].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Azure-værktøjskassen til IntelliJ]: ../azure-toolkit-for-intellij.md
[Installation af Azure værktøjerne til IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Oprette en Hej verden WebApp til Azure i IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[Eksempel dynamiske Web-projekt]: http://go.microsoft.com/fwlink/?LinkId=817337

[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[Oversigt over Web Apps]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-intellij/01-debug-java-web-app-in-intellij.png
[02]: ./media/app-service-web-debug-java-web-app-in-intellij/02-configure-intellij-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-intellij/03-debug-configuration.png
[04]: ./media/app-service-web-debug-java-web-app-in-intellij/04-select-debug.png
[05]: ./media/app-service-web-debug-java-web-app-in-intellij/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-intellij/06-windows-command-prompt-connection-successful-to-remote.png
