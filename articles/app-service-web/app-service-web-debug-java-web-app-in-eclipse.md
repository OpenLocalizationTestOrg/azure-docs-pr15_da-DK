<properties 
    pageTitle="Fejlfinding af en Java WebApp på Azure i Eklipse | Microsoft Azure" 
    description="Dette selvstudium viser, hvordan du bruger Azure-værktøjet til Eklipse til at foretage fejlfinding af en Java Web App, der kører på Azure." 
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

# <a name="debug-a-java-web-app-on-azure-in-eclipse"></a>Fejlfinding af en Java WebApp på Azure i Eklipse

Dette selvstudium viser, hvordan du udfører fejlfinding en Java Web App, der kører på Azure ved hjælp af [Azure-værktøjskassen til Eklipse]. Af hensyn til enkel, du vil bruge et eksempel på grundlæggende Java Server side (JSP) til dette selvstudium, men trinnene ville være tilsvarende for en Java servlet, når du foretager fejlfinding på Azure.

Når du har fuldført dette selvstudium, ser dit program svarende til følgende illustration, når du foretager fejlfinding i Eklipse:

![][01]
 
## <a name="prerequisites"></a>Forudsætninger

* En Java udvikler Kit (JDK), v 1,8 eller nyere.
* Eklipse IDE for Java EE udviklere småt eller nyere. Dette kan hentes fra <http://www.eclipse.org/downloads/>.
* En fordeling af en Java-baseret webserver eller programserver som Apache Tomcat eller Jetty.
* Et Azure abonnement, som kan hentes fra <https://azure.microsoft.com/en-us/free/> eller <http://azure.microsoft.com/pricing/purchase-options/>.
* Azure værktøjskassen til Eklipse. Du kan finde flere oplysninger, kan du se [installere Azure-værktøjskassen til Eklipse].
* Et dynamisk webprojekt oprettet og implementeret til Azure App Service; Se [oprette en Hej verden Web App til Azure i Eklipse]f.eks.

## <a name="to-debug-a-java-web-app-on-azure"></a>Foretage fejlfinding af en Java WebApp på Azure

For at fuldføre disse trin i dette afsnit, du kan bruge en eksisterende dynamisk webprojekt, du allerede har installeret som en Java Web App på Azure, du henter et [Eksempel dynamisk Web Project] og følge trinnene i [oprette en Hej verden Web App til Azure i Eklipse] til at udrulle på Azure. 

1. Åbn Eklipse.

1. Konfigurere timeout for ekstern fejlfinding:

    1. Klik på menuen **Windows** i Eklipse, og klik derefter på **Indstillinger**.
    1. Udvid noden **Java** og derefter vælge **fejlfinding**.
    1. Konfigurere indstillinger for både **fejlfindingen timeout (ms)** og **Start timeout (ms)** til `120000`.

        ![][02]

    1. Klik på **OK** for at lukke dialogboksen **Indstillinger** .

1. Højreklik på det dynamiske Web-projekt, du har installeret til Azure Eklipses Projektstifinder, i visningen. Når der vises i genvejsmenuen, Markér **Fejlfinding af som**, og klik derefter på **Azure Web App**.

    ![][03]

1. Hvis dette er første gang du foretager fejlfinding af projektet dynamisk Web, åbnes dialogboksen **Foretage fejlfinding af konfigurationer** ; Du kan acceptere standardværdierne som er angivet af værktøjerne under fanen **Opret forbindelse** . På fanen **kilde** skal du klikke på **Tilføj**, og klik derefter **Java project**, Vælg **Dynamiske Web-projekt**, og klik derefter på **OK**. Når du har udført disse trin, skal du klikke på **fejlfinding**.

    ![][04]

1. Når du bliver bedt om at **aktivere ekstern fejlfinding i remote online nu?**, skal du klikke på **OK**.

1. Når du bliver bedt om **din online er nu klar til ekstern fejlfinding**, skal du klikke på **OK**.

    ![][05]

1. Når dialogboksen **Foretage fejlfinding af konfigurationer** vises igen, skal du klikke på **fejlfinding**.

1. En Windows-kommandoprompt eller Unix shell åbnes og forberede nødvendige forbindelse til fejlfinding. skal du vente, indtil der oprettes forbindelse til din remote Java online, før du fortsætter. Hvis du bruger Windows, vil det se ud i følgende illustration.

    ![][06]

1. Indsætte et linjeskift punkt i siden JSP, og derefter åbne URL-adressen til dit Java Web App i en browser:

    1. Åbn **Azure Explorer** i Eklipse.
    1. Gå til **Web Apps** og Webapp'en Java, du vil foretage fejlfinding.
    1. Højreklik på den Web App, og klik på **Åbn i Browser**.
    1. Eklipse træder nu i fejlsikret tilstand.

## <a name="next-steps"></a>Næste trin

Du kan finde flere oplysninger om brug af Azure med Java, [Azure Java Developer Center].

Du kan finde flere oplysninger om oprettelse af Azure Web Apps [Web Apps oversigt].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Azure-værktøjskassen til Eklipse]: ../azure-toolkit-for-eclipse.md
[Installation af Azure værktøjerne til Eklipse]: ../azure-toolkit-for-eclipse-installation.md
[Oprette en Hej verden WebApp til Azure i Eklipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[Eksempel dynamiske Web-projekt]: http://go.microsoft.com/fwlink/?LinkId=817337

[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[Oversigt over Web Apps]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-eclipse/01-debug-java-web-app-in-eclipse.png
[02]: ./media/app-service-web-debug-java-web-app-in-eclipse/02-configure-eclipse-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-eclipse/03-debug-as.png
[04]: ./media/app-service-web-debug-java-web-app-in-eclipse/04-debug-configurations.png
[05]: ./media/app-service-web-debug-java-web-app-in-eclipse/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-eclipse/06-windows-command-prompt-connection-successful-to-remote.png
