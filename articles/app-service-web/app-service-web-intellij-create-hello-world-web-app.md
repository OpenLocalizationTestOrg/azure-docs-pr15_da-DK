<properties 
    pageTitle="Oprette en Hej verden WebApp til Azure i IntelliJ | Microsoft Azure" 
    description="Dette selvstudium viser, hvordan du bruger Azure-værktøjet til IntelliJ til at oprette en Hej verden Web App til Azure." 
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
    ms.date="08/11/2016" 
    ms.author="asirveda;robmcm"/>

# <a name="create-a-hello-world-web-app-for-azure-in-intellij"></a>Oprette en Hej verden WebApp til Azure i IntelliJ

Dette selvstudium viser, hvordan du opretter og installerer et grundlæggende Hej verden program til Azure som en Web App ved hjælp af [Azure-værktøjskassen til IntelliJ]. Eksempel på en grundlæggende JSP vises for enkel, men meget lignende trin der ville være relevante for en Java servlet omfang er bekymrede for Azure-installation.

Når du har fuldført dette selvstudium, ser dit program svarende til følgende illustration, når du får det vist i en webbrowser:

![][01]
 
## <a name="prerequisites"></a>Forudsætninger

* En Java udvikler Kit (JDK), v 1,8 eller nyere.
* IntelliJ ide Ultimate Edition. Dette kan hentes fra <https://www.jetbrains.com/idea/download/index.html>.
* En fordeling af en Java-baseret webserver eller programserver som Apache Tomcat eller Jetty.
* Et Azure abonnement, som kan hentes fra <https://azure.microsoft.com/free/> eller <http://azure.microsoft.com/pricing/purchase-options/>.
* Azure værktøjskassen til IntelliJ. Du kan finde flere oplysninger, kan du se [installere Azure-værktøjskassen til IntelliJ].

## <a name="to-create-a-hello-world-application"></a>Sådan oprettes et Hej verden program

Først skal begynder vi til at oprette et Java-projekt.

1. Start IntelliJ, og klik på **filer**og derefter **Ny**i menuen, og klik derefter på **projekt**.

   ![][02]

1. Vælg **Java**, derefter **Webprogram**, og klik derefter på **Næste**i dialogboksen nyt projekt.

   ![][03a]

   Hvis du bliver bedt om at fortsætte med ingen SDK, der er tildelt, skal du klikke på **Ja**.

   ![][03b]

1. Navngiv projektet **Java-Web-App-på-Azure**henblik på dette selvstudium, og klik derefter på **Udfør**.

   ![][04]

1. Udvid **Java-Web-App-på-Azure**, i Intellijs Projektstifinder visning, og derefter udvide **web**, og dobbeltklik derefter på **index.jsp**.

   ![][05]

1. Når index.jsp filen åbnes i IntelliJ, skal du tilføje tekst til dynamisk visning **Hej verden!** inden for den nuværende `<body>` element. Din opdaterede `<body>` indhold skal ligne eksemplet nedenfor:

   `<body><b><% out.println("Hello World!"); %></b></body>` 

1. Gem index.jsp.

## <a name="to-deploy-your-application-to-an-azure-web-app-container"></a>Installere dit program til en Azure Web App-objektbeholder

Der er flere måder, som du kan installere et Java webprogram til Azure. I dette selvstudium beskrives en af de nemmeste: dit program, som skal installeres i en Azure Web App-objektbeholder – ingen speciel projekttype eller andre værktøjer, som du skal bruge. JDK og web objektbeholder softwaren leveres for dig af Azure, så det er ikke nødvendigt at overføre dine egne; Du skal blot din Java Web App. Derfor tager publiceringsprocessen for dit program sekunder, ikke minutter.

1. Højreklik på **Java-Web-App-på-Azure** projektet i Intellijs Projektstifinder. Når der vises i genvejsmenuen, Vælg **Azure**, og klik derefter på **Udgiv som Azure Web App. …**

   ![][06]

1. Hvis du ikke allerede er logget på Azure fra IntelliJ, bliver du bedt om at logge på din Azure-konto:

   ![][07]

   Bemærk: Hvis du har flere Azure konti, nogle af instruktionerne under processen med at logge vises muligvis flere gange, selvom de ser ud til at være den samme. Når dette sker, skal du fortsætte med at følge vejledningen logge.

1. Når du har logget på din Azure-konto, vises dialogboksen **Administrer abonnementer** for en liste over de abonnementer, som er knyttet til dine legitimationsoplysninger. Hvis der er flere abonnementer, og du vil arbejde med et angivet delsæt af dem, du kan også fjerne markeringen af dem du vil bruge. Klik på **Luk**, når du har valgt dine abonnementer.

   ![][08]

1. Når dialogboksen **Installer til Azure Web App objektbeholder** vises, viser den nogen Web App-objektbeholdere, du tidligere har oprettet. Hvis du ikke har oprettet nogen objektbeholdere, vil listen være tom.   

   ![][09]

1. Hvis du ikke har oprettet en Azure Web App-objektbeholder før, eller hvis du vil publicere dit program til en ny objektbeholder, kan du bruge følgende trin. Ellers skal du vælge en eksisterende Web App-objektbeholder og gå videre til trin 6 nedenfor.

  1. Klik på**+**

        ![][10]

  1. Dialogboksen **Ny Web App-objektbeholder** vises, hvor der skal bruges til de næste flere trin.

        ![][11]

  1. Angiv en **DNS-navn** til din Web App-objektbeholder. Dette udgør blad DNS-navnet på host URL-adressen for dit program i Azure. Bemærk: Navnet skal være tilgængeligt, og de svarer til Azure online krav til navngivning.

  1. Vælg den nødvendige software for dit program i rullemenuen **Web objektbeholder** .

        I øjeblikket, kan du vælge fra Tomcat 8, Tomcat 7 eller Jetty 9. En seneste fordeling af den valgte software leveres af Azure, og den kan køre på en seneste fordeling af JDK 8 er oprettet af Oracle og leveres af Azure.

  1. Vælg det abonnement, du vil bruge til denne installation i rullemenuen **abonnement** .

  1. Vælg gruppen ressource, som du vil knytte din online i rullemenuen **Ressourcegruppe** .

        Bemærk: Azure ressourcegrupper gør det muligt at gruppere relaterede ressourcer sammen, så, for eksempel de kan slettes sammen.

        Du kan vælge en eksisterende ressourcegruppe (Hvis du har en) og Spring til trin g nedenfor, eller du kan bruge disse trin for at oprette en ny ressourcegruppe følgende:

      * Klik på **Ny...**

      * Dialogboksen **Ny ressourcegruppe** vises:

            ![][12]

      * I den tekstfeltet **navn** skal angive et navn til din nye ressourcegruppe.

      * I den det **område** i rullemenuen, Vælg de relevante Azure data center placering til dine ressourcegruppe.

      * Klik på **OK**.

  1. Rullemenuen **App Service planlægge** viser tjenesteplanerne app, der er knyttet til den ressourcegruppe, du har valgt.

        Bemærk: Planlægge en App-tjenesten angiver oplysninger som placeringen af din online, kan priser niveauet og Beregn forekomst størrelse. En enkelt App Service Plan kan bruges til flere Web Apps, hvilket er årsagen til det vedligeholdes separat fra en bestemt Web App-installation.

        Du kan vælge en eksisterende App Service planlægge (Hvis du har en) og Spring til trin h nedenfor, eller du kan bruge disse trin for at oprette en ny App Service planlægge følgende:

      * Klik på **Ny...**

      * Dialogboksen **Ny App Service planlægge** vises:

            ![][13]

      * I den tekstfeltet **navn** skal angive et navn til din nye App Service planlægge.

      * I på den **placering** i rullemenuen, Vælg de relevante Azure data center placering til planen.

      * I den rullemenuen **Priser niveau** Vælg den relevante priser for plan. Du kan vælge **Free**til testformål.

      * I den forekomst rullelisten menuen **Størrelse** , Vælg den relevante forekomst størrelse for plan. Du kan vælge **Small**til testformål.

  1. Når du har gennemført alle ovenstående trin, skal dialogboksen Ny Web App-objektbeholder ligne den følgende illustration:

        ![][14]

  1. Klik på **OK** for at afslutte oprettelsen af din nye Web App-objektbeholder.

        Vent et par sekunder til listen over Web App-beholdere skal opdateres, og din nyoprettede web app-objektbeholder skulle nu være markerede på listen.

1. Du er nu klar til at udføre den indledende installation af din online til Azure; Klik på **OK** for at installere Java-program til objektbeholderen markerede Web App.

    ![][15]

    Bemærk: Som standard programmet installeres som en undermappe til application server. Hvis du vil have den installeres som rodprogram, Markér afkrydsningsfeltet **Implementer roden** før du klikker på **OK**.

1. Derefter skal du se visningen **Azure aktivitetslog** , hvilket indikerer, installation status for din online.

    ![][16]

    Processen med at udrulle din online til Azure fører kun et par sekunder at fuldføre. Når dit program klar til, vises der et link med navnet **Published** i kolonnen **Status** . Når du klikker på linket, kommer du til startsiden for din udløst online, eller du kan bruge trinnene i følgende afsnit til at gå til din online.

## <a name="browsing-to-your-web-app-on-azure"></a>Gå til din online på Azure

Du kan bruge **Azure** Stifindervisning til brows til din online på Azure.

Hvis **Azure** Stifindervisning ikke allerede er åben, kan du åbne den ved at klikke på derefter menuen **Vis** i IntelliJ, og derefter klikke på **Værktøjet Windows**og klik derefter på **Service Stifinder**. Hvis du tidligere har ikke har logget på, bliver du bedt om at gøre det.

Når visningen **Azure Explorer** er åben, brug skal du følge disse trin for at stoppe din online: 

1. Udvid noden **Azure** .

1. Udvid noden **Web Apps** . 

1. Højreklik på den ønskede Web App.

1. Når genvejsmenuen vises, skal du klikke på **Åbn i Browser**.

    ![][17]

## <a name="updating-your-web-app"></a>Opdatere din online

Opdatere en eksisterende bruger Azure Web App er en hurtig og nem proces, og du har to muligheder for at opdatere:

* Du kan opdatere installationen af en eksisterende Java Web App.
* Du kan udgive en ekstra Java-program til objektbeholderen samme Web App.

I begge tilfælde, processen er identiske og tager kun et par sekunder:

1. Højreklik på det Java-program, du vil opdatere eller tilføje til en eksisterende Web App objektbeholder i Projektstifinder IntelliJ.

1. Når der vises i genvejsmenuen skal du vælge **Azure** og klik derefter på **Udgiv som Azure Web App. …**

1. Da du har allerede er logget tidligere, vises en liste over dine eksisterende Web App-beholdere. Vælg det, du vil publicere eller publicere din Java-program til, og klik på **OK**.

Et par sekunder senere visningen **Azure aktivitetslog** , vises din opdaterede installation som **Published** , og du vil kunne bekræfte dit opdaterede program i en webbrowser.

## <a name="starting-or-stopping-an-existing-web-app"></a>Starte eller stoppe en eksisterende WebApp

Hvis du vil starte eller stoppe en eksisterende Azure Web App-objektbeholder (herunder alle de installerede Java-programmer i den), kan du bruge **Azure** Stifindervisning.

Hvis **Azure** Stifindervisning ikke allerede er åben, kan du åbne den ved at klikke på derefter menuen **Vis** i IntelliJ, og derefter klikke på **Værktøjet Windows**og klik derefter på **Service Stifinder**. Hvis du tidligere har ikke har logget på, bliver du bedt om at gøre det.

Når visningen **Azure Explorer** er åben, brug skal du følge disse trin for at starte eller stoppe din online: 

1. Udvid noden **Azure** .

1. Udvid noden **Web Apps** . 

1. Højreklik på den ønskede Web App.

1. Klik på **Start** eller **Stop**, når der vises i genvejsmenuen. Bemærk, at menuindstillinger kontekst-aware, så du kan kun standse en igangværende WebApp eller starte en WebApp, som ikke kører i øjeblikket.

    ![][18]

## <a name="next-steps"></a>Næste trin

Du kan finde flere oplysninger om Azure programudviklingsværktøjer til Java IDEs følgende links:

- [Azure-værktøjskassen til Eklipse]
  - [Installation af Azure værktøjerne til Eklipse]
  - [Oprette en Hej verden WebApp til Azure i Eklipse]
  - [Hvad er nyt i Azure værktøjerne til Eklipse]
- [Azure-værktøjskassen til IntelliJ]
  - [Installation af Azure værktøjerne til IntelliJ]
  - *Oprette en Hej verden WebApp til Azure i IntelliJ (i denne artikel)*
  - [Hvad er nyt i Azure værktøjerne til IntelliJ]

Du kan finde flere oplysninger om brug af Azure med Java, [Azure Java Developer Center].

Du kan finde flere oplysninger om oprettelse af Azure Web Apps [Web Apps oversigt].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure-værktøjskassen til Eklipse]: ../azure-toolkit-for-eclipse.md
[Azure-værktøjskassen til IntelliJ]: ../azure-toolkit-for-intellij.md
[Oprette en Hej verden WebApp til Azure i Eklipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[Create a Hello World Web App for Azure in IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[Installation af Azure værktøjerne til Eklipse]: ../azure-toolkit-for-eclipse-installation.md
[Installation af Azure værktøjerne til IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Hvad er nyt i Azure værktøjerne til Eklipse]: ../azure-toolkit-for-eclipse-whats-new.md
[Hvad er nyt i Azure værktøjerne til IntelliJ]: ../azure-toolkit-for-intellij-whats-new.md

[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[Oversigt over Web Apps]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-intellij-create-hello-world-web-app/01-Web-Page.png
[02]: ./media/app-service-web-intellij-create-hello-world-web-app/02-File-New-Project.png
[03a]: ./media/app-service-web-intellij-create-hello-world-web-app/03-New-Project-Dialog.png
[03b]: ./media/app-service-web-intellij-create-hello-world-web-app/03-No-SDK-Specified.png
[04]: ./media/app-service-web-intellij-create-hello-world-web-app/04-New-Project-Dialog.png
[05]: ./media/app-service-web-intellij-create-hello-world-web-app/05-Open-Index-Page.png
[06]: ./media/app-service-web-intellij-create-hello-world-web-app/06-Azure-Publish-Context-Menu.png
[07]: ./media/app-service-web-intellij-create-hello-world-web-app/07-Azure-Log-In-Dialog.png
[08]: ./media/app-service-web-intellij-create-hello-world-web-app/08-Manage-Subscriptions.png
[09]: ./media/app-service-web-intellij-create-hello-world-web-app/09-App-Containers.png
[10]: ./media/app-service-web-intellij-create-hello-world-web-app/10-Add-App-Container.png
[11]: ./media/app-service-web-intellij-create-hello-world-web-app/11-New-App-Container.png
[12]: ./media/app-service-web-intellij-create-hello-world-web-app/12-New-Resource-Group.png
[13]: ./media/app-service-web-intellij-create-hello-world-web-app/13-New-App-Service-Plan.png
[14]: ./media/app-service-web-intellij-create-hello-world-web-app/14-New-App-Container.png
[15]: ./media/app-service-web-intellij-create-hello-world-web-app/15-Deploy-To-Azure.png
[16]: ./media/app-service-web-intellij-create-hello-world-web-app/16-Progress-Indicator.png
[17]: ./media/app-service-web-intellij-create-hello-world-web-app/17-Browse-Web-App.png
[18]: ./media/app-service-web-intellij-create-hello-world-web-app/18-Stop-Web-App.png
