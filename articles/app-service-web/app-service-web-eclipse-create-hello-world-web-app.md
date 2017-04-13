<properties 
    pageTitle="Oprette en Hej verden WebApp til Azure i Eklipse | Microsoft Azure" 
    description="Dette selvstudium viser, hvordan du bruger Azure-værktøjet til Eklipse til at oprette en Hej verden Web App til Azure." 
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
    ms.date="08/26/2016" 
    ms.author="robmcm"/>

# <a name="create-a-hello-world-web-app-for-azure-in-eclipse"></a>Oprette en Hej verden WebApp til Azure i Eklipse

Dette selvstudium viser, hvordan du opretter og installerer et grundlæggende Hej verden program til Azure som en Web App ved hjælp af [Azure-værktøjskassen til Eklipse]. Eksempel på en grundlæggende JSP vises for enkel, men meget lignende trin der ville være relevante for en Java servlet omfang er bekymrede for Azure-installation.

Når du har fuldført dette selvstudium, ser dit program svarende til følgende illustration, når du får det vist i en webbrowser:

![Eksempel på Hej verden app][01]
 
## <a name="prerequisites"></a>Forudsætninger

* En Java udvikler Kit (JDK), v 1,8 eller nyere.
* Eklipse IDE for Java EE udviklere, Luna eller nyere. Dette kan hentes fra <http://www.eclipse.org/downloads/>.
* En fordeling af en Java-baseret webserver eller programserver som Apache Tomcat eller Jetty.
* Et Azure abonnement, som kan hentes fra <https://azure.microsoft.com/free/> eller <http://azure.microsoft.com/pricing/purchase-options/>.
* Azure værktøjskassen til Eklipse. Du kan finde flere oplysninger, kan du se [installere Azure-værktøjskassen til Eklipse].

## <a name="to-create-a-hello-world-application"></a>Sådan oprettes et Hej verden program

Først skal begynder vi til at oprette et Java-projekt.

1. Start Eklipse, og klik på menuen **filer**, klik på **Ny**, og klik derefter på **Dynamiske webprojekt**. (Hvis du ikke kan se **Dynamisk webprojekt** er angivet som en tilgængelig project, når du klikker på **fil** og **Ny**, gør derefter følgende: Klik på **filer**, klik på **Ny**, klik på **projekt...**, udvide **Web**, klik på **Dynamisk webprojekt**og klikke på **Næste**.)

1. Navngive projektet **MyWebApp**henblik på dette selvstudium. Skærmen vises som følger:

    ![Oprette et nyt dynamiske Web-projekt][02]

1. Klik på **Udfør**.

1. Udvid **MyWebApp**i Eklipses Projektstifinder visning. Højreklik på **WebContent**, klik på **Ny**, og klik derefter på **JSP fil**.

1. Bevare den overordnede mappe som **MyWebApp/WebContent**i dialogboksen **Ny JSP-fil** , navn fil **index.jsp**, og klik derefter på **Næste**.

1. Vælg **Ny JSP fil (html)**med henblik på af dette selvstudium i dialogboksen **Vælg JSP skabelon** , og klik derefter på **Udfør**.

1. Tilføj, når filen index.jsp åbner i Eklipse i tekst skal vises dynamisk **Hej verden!** inden for den nuværende `<body>` element. Din opdaterede `<body>` indhold skal ligne eksemplet nedenfor:

    `<body><b><% out.println("Hello World!"); %></b></body>` 

1. Gem index.jsp.

## <a name="to-deploy-your-application-to-an-azure-web-app-container"></a>Installere dit program til en Azure Web App-objektbeholder

Der er flere måder, som du kan installere et Java webprogram til Azure. I dette selvstudium beskrives en af de nemmeste: dit program, som skal installeres i en Azure Web App-objektbeholder – ingen speciel projekttype eller andre værktøjer, som du skal bruge. JDK og web objektbeholder softwaren leveres for dig af Azure, så det er ikke nødvendigt at overføre dine egne; Du skal blot din Java Web App. Derfor tager publiceringsprocessen for dit program sekunder, ikke minutter.

1. Højreklik på **MyWebApp**i Eklipses Projektstifinder.

1. Vælg **Azure**i genvejsmenuen, og derefter klikke på **Udgiv som Azure Web App. …**

    ![Udgive som Azure WebApp][03]
   
    Mens projektet web application er markeret i Projektstifinder, kan du også klikke på knappen **Publicer** rullemenuen på værktøjslinjen og vælge **Publicer som Azure Web App** derfra:
   
    ![Udgive som Azure WebApp][14]
   
1. Hvis du ikke allerede er logget på Azure fra Eklipse, bliver du bedt om at logge på din Azure-konto:

    ![Azure dialogboksen logon][04]
   
    Hvis du har flere Azure konti, nogle af instruktionerne under processen med at logge vises muligvis flere gange, selvom de ser ud til at være den samme. Når dette sker, skal du fortsætte med at følge vejledningen logge.

1. Når du har logget på din Azure-konto, vises dialogboksen **Administrer abonnementer** for en liste over de abonnementer, som er knyttet til dine legitimationsoplysninger. Hvis der er flere abonnementer, og du vil arbejde med et angivet delsæt af dem, du kan også fjerne markeringen af dem du vil bruge. Klik på **Luk**, når du har valgt dine abonnementer.

    ![Administrere dialogboksen abonnementer][05]
   
1. Når dialogboksen **Installer til Azure Web App objektbeholder** vises, viser den nogen Web App-objektbeholdere, du tidligere har oprettet. Hvis du ikke har oprettet nogen objektbeholdere, vil listen være tom.

    ![Installere på Azure Web App objektbeholder dialogboksen][06]
   
1. Hvis du ikke har oprettet en Azure Web App-objektbeholder før, eller hvis du vil publicere dit program til en ny objektbeholder, kan du bruge følgende trin. Ellers skal du vælge en eksisterende Web App-objektbeholder og gå videre til trin 7 nedenfor.

    1. Klik på **Ny...**

        ![Installere på Azure Web App objektbeholder dialogboksen][15]

    1. Dialogboksen **Ny Web App-objektbeholder** vises:

        ![Dialogboksen Ny Web App objektbeholder][07a]

    1. Angiv en **DNS-navn** til din Web App-objektbeholder. Dette udgør blad DNS-navnet på host URL-adressen for dit program i Azure. (Bemærk, at navnet skal være tilgængeligt, og de svarer til Azure online krav til navngivning).

    1. Vælg den nødvendige software for dit program i rullemenuen **Web objektbeholder** .

        I øjeblikket, kan du vælge fra Tomcat 8, Tomcat 7 eller Jetty 9. En seneste fordeling af den valgte software leveres af Azure, og den kan køre på en seneste fordeling af JDK 8 er oprettet af Oracle og leveres af Azure.

    1. Vælg det abonnement, du vil bruge til denne installation i rullemenuen **abonnement** .

    1. Vælg gruppen ressource, som du vil knytte din online i rullemenuen **Ressourcegruppe** . (Azure ressourcegrupper gør det muligt at gruppere relaterede ressourcer sammen, så, for eksempel de kan slettes sammen.)

        Du kan vælge en eksisterende ressourcegruppe (Hvis du har en) og Spring til trin g nedenfor, eller du kan bruge disse trin for at oprette en ny ressourcegruppe følgende:

        * Klik på **Ny...**

        * Dialogboksen **Ny ressourcegruppe** vises:

            ![Dialogboksen ny ressourcegruppe][08]

        * I den tekstfeltet **navn** skal angive et navn til din nye ressourcegruppe.

        * I den det **område** i rullemenuen, Vælg de relevante Azure data center placering til dine ressourcegruppe.

        * VALGFRIT: Som standard en seneste fordeling af Java 8 installeres ved Azure automatisk til din web app-objektbeholder som din JVM. Du kan dog angive en anden version og fordelingen af JVM, hvis din online kræver det. Hvis du vil angive JDK for din online, klik på fanen **JDK** , og vælg en af følgende indstillinger:

            * **Implementer standard JDK, som Azure Web Apps-tjenesten**: denne indstilling, vil installere en seneste fordeling af Java 8.

            * **Implementer en 3 part JDK, der er tilgængelige på Azure**: denne indstilling kan du vælge fra listen over JDKs, der leveres af Microsoft Azure.

            * **Implementer mit eget JDK fra denne downloadplaceringen**: denne indstilling kan du angive dine egne JDK fordeling, som skal pakkes som en ZIP-fil og overføres til et offentligt tilgængeligt downloadplaceringen eller en Azure-lager-konto, du har adgang til.

            ![Dialogboksen Ny Web App objektbeholder][07b]

    * Klik på **OK**.

    1. Rullemenuen **App Service planlægge** viser tjenesteplanerne app, der er knyttet til den ressourcegruppe, du har valgt. (App Tjenesteplanerne angive oplysninger som placeringen af din online, kan priser niveauet og Beregn forekomst størrelse. En enkelt App Service Plan kan bruges til flere Web Apps, hvilket er årsagen til det vedligeholdes separat fra en bestemt Web App-installation.)

        Du kan vælge en eksisterende App Service planlægge (Hvis du har en) og Spring til trin h nedenfor, eller du kan bruge disse trin for at oprette en ny App Service planlægge følgende:

        * Klik på **Ny...**

        * Dialogboksen **Ny App Service planlægge** vises:

            ![Dialogboksen for ny App Service planlægge][09]

        * I den tekstfeltet **navn** skal angive et navn til din nye App Service planlægge.

        * I på den **placering** i rullemenuen, Vælg de relevante Azure data center placering til planen.

        * I den rullemenuen **Priser niveau** Vælg den relevante priser for plan. Du kan vælge **Free**til testformål.

        * I den forekomst rullelisten menuen **Størrelse** , Vælg den relevante forekomst størrelse for plan. Du kan vælge **Small**til testformål.

    1. Når du har gennemført alle ovenstående trin, skal dialogboksen Ny Web App-objektbeholder ligne den følgende illustration:

        ![Dialogboksen Ny Web App objektbeholder][10]

    1. Klik på **OK** for at afslutte oprettelsen af din nye Web App-objektbeholder.

        Vent et par sekunder til listen over Web App-beholdere skal opdateres, og din nyoprettede web app-objektbeholder skulle nu være markerede på listen.

1. Du er nu klar til at udføre den indledende installation af din online til Azure:

    ![Installere på Azure Web App objektbeholder dialogboksen][11]

    Klik på **OK** for at installere Java-program til objektbeholderen markerede Web App.

    Som standard installeres dit program som en undermappe til application server. Hvis du vil have den installeres som rodprogram, Markér afkrydsningsfeltet **Implementer roden** før du klikker på **OK**.

1. Derefter skal du se visningen **Azure aktivitetslog** , hvilket indikerer, installation status for din online.

    ![Azure aktivitetslog][12]

    Processen med at udrulle din online til Azure fører kun et par sekunder at fuldføre. Når dit program klar til, vises der et link med navnet **Published** i kolonnen **Status** . Når du klikker på linket, som den fører til startsiden for din udløst online.

## <a name="updating-your-web-app"></a>Opdatere din online

Opdatere en eksisterende bruger Azure Web App er en hurtig og nem proces, og du har to muligheder for at opdatere:

* Du kan opdatere installationen af en eksisterende Java Web App.
* Du kan udgive en ekstra Java-program til objektbeholderen samme Web App.

I begge tilfælde, processen er identiske og tager kun et par sekunder:

1. Højreklik på det Java-program, du vil opdatere eller tilføje til en eksisterende Web App objektbeholder i Projektstifinder Eklipse.

1. Når der vises i genvejsmenuen skal du vælge **Azure** og klik derefter på **Udgiv som Azure Web App. …**

1. Da du har allerede er logget tidligere, vises en liste over dine eksisterende Web App-beholdere. Vælg det, du vil publicere eller publicere din Java-program til, og klik på **OK**.

Et par sekunder senere visningen **Azure aktivitetslog** , vises din opdaterede installation som **Published** , og du vil kunne bekræfte dit opdaterede program i en webbrowser.

## <a name="stopping-an-existing-web-app"></a>Stoppe en eksisterende WebApp

Stoppe en eksisterende Azure Web App-objektbeholder (herunder alle de installerede Java-programmer i den), du kan bruge **Azure** Stifindervisning.

Hvis **Azure** Stifindervisning ikke allerede er åben, kan du åbne den ved at klikke på derefter menuen **vindue** i Eklipse, og derefter klikke på **Åbn visning**, og klik derefter **andre …**og derefter **Azure**og klik derefter på **Azure Stifinder**. Hvis du tidligere har ikke har logget på, bliver du bedt om at gøre det.

Når visningen **Azure Explorer** er åben, brug skal du følge disse trin for at stoppe din online: 

1. Udvid noden **Azure** .

1. Udvid noden **Web Apps** . 

1. Højreklik på den ønskede Web App.

1. Klik på **Stop**, når der vises i genvejsmenuen.

    ![Stoppe en eksisterende WebApp][13]

## <a name="next-steps"></a>Næste trin

Du kan finde flere oplysninger om Azure programudviklingsværktøjer til Java IDEs følgende links:

- [Azure-værktøjskassen til Eklipse]
  - [Installation af Azure værktøjerne til Eklipse]
  - *Oprette en Hej verden WebApp til Azure i Eklipse (i denne artikel)*
  - [Hvad er nyt i Azure værktøjerne til Eklipse]
- [Azure-værktøjskassen til IntelliJ]
  - [Installation af Azure værktøjerne til IntelliJ]
  - [Oprette en Hej verden WebApp til Azure i IntelliJ]
  - [Hvad er nyt i Azure værktøjerne til IntelliJ]

Du kan finde flere oplysninger om brug af Azure med Java, [Azure Java Developer Center].

Du kan finde flere oplysninger om oprettelse af Azure Web Apps [Web Apps oversigt].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure-værktøjskassen til Eklipse]: ../azure-toolkit-for-eclipse.md
[Azure-værktøjskassen til IntelliJ]: ../azure-toolkit-for-intellij.md
[Create a Hello World Web App for Azure in Eclipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[Oprette en Hej verden WebApp til Azure i IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[Installation af Azure værktøjerne til Eklipse]: ../azure-toolkit-for-eclipse-installation.md
[Installation af Azure værktøjerne til IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Hvad er nyt i Azure værktøjerne til Eklipse]: ../azure-toolkit-for-eclipse-whats-new.md
[Hvad er nyt i Azure værktøjerne til IntelliJ]: ../azure-toolkit-for-intellij-whats-new.md

[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[Oversigt over Web Apps]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-eclipse-create-hello-world-web-app/01-Web-Page.png
[02]: ./media/app-service-web-eclipse-create-hello-world-web-app/02-Dynamic-Web-Project.png
[03]: ./media/app-service-web-eclipse-create-hello-world-web-app/03-Context-Menu.png
[04]: ./media/app-service-web-eclipse-create-hello-world-web-app/04-Log-In-Dialog.png
[05]: ./media/app-service-web-eclipse-create-hello-world-web-app/05-Manage-Subscriptions-Dialog.png
[06]: ./media/app-service-web-eclipse-create-hello-world-web-app/06-Deploy-To-Azure-Web-Container.png
[07a]: ./media/app-service-web-eclipse-create-hello-world-web-app/07a-New-Web-App-Container-Dialog.png
[07b]: ./media/app-service-web-eclipse-create-hello-world-web-app/07b-New-Web-App-Container-Dialog.png
[08]: ./media/app-service-web-eclipse-create-hello-world-web-app/08-New-Resource-Group-Dialog.png
[09]: ./media/app-service-web-eclipse-create-hello-world-web-app/09-New-Service-Plan-Dialog.png
[10]: ./media/app-service-web-eclipse-create-hello-world-web-app/10-Completed-Web-App-Container-Dialog.png
[11]: ./media/app-service-web-eclipse-create-hello-world-web-app/11-Completed-Deploy-Dialog.png
[12]: ./media/app-service-web-eclipse-create-hello-world-web-app/12-Activity-Log-View.png
[13]: ./media/app-service-web-eclipse-create-hello-world-web-app/13-Azure-Explorer-Web-App.png
[14]: ./media/app-service-web-eclipse-create-hello-world-web-app/14-publishDropdownButton.png
[15]: ./media/app-service-web-eclipse-create-hello-world-web-app/15-New-Azure-Web-Container.png
