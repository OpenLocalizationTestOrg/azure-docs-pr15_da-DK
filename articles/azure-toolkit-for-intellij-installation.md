<properties
    pageTitle="Installation af Azure værktøjerne til IntelliJ | Microsoft Azure"
    description="Lær at installere Azure-værktøjskassen til IntelliJ ide."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="installing-the-azure-toolkit-for-intellij"></a>Installation af Azure værktøjerne til IntelliJ

Azure værktøjerne til IntelliJ har skabeloner og funktionalitet, som gør det muligt at nemt oprette, udvikle, teste og installere Azure-programmer bruger udviklingsmiljø IntelliJ ide. Azure-værktøjskassen til IntelliJ er et Open Source-projekt, hvis kildekode findes under MIT-licensen fra projektwebsted på GitHub på følgende URL-adressen:

<https://github.com/Microsoft/Azure-Tools-for-Java>

Der er to metoder til at installere Azure-værktøjskassen til IntelliJ, fra dialogboksen Indstillinger og fra menuen Konfigurer på startskærmbilledet; begge metoder til installation bliver vist i følgende trin.

[AZURE.INCLUDE [azure-toolkit-for-IntelliJ-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="to-install-the-azure-toolkit-for-intellij-from-the-settings-dialog-box"></a>Installere Azure-værktøjskassen til IntelliJ fra i dialogboksen Indstillinger

1. Start IntelliJ ide.

1. Når IntelliJ ide åbnes, skal du klikke på **filer**, og derefter klikke på **Indstillinger**.

    ![Åbne dialogboksen IntelliJ ide indstillinger][01a]

1. Klik på **plug-ins**i dialogboksen Indstillinger, og klik derefter på **Gennemse typer lagre**.

    ![Dialogboksen til IntelliJ ide indstillinger][02a]

1. Skriv "Azure" i søgefeltet i dialogboksen **Gennemse typer lagre** . Fremhæv **Azure-værktøjskassen til IntelliJ**, og klik derefter på **Installer**.

    ![Søge efter Azure værktøjerne til IntelliJ][03]

    IntelliJ ide vises status for installationen i en dialogboks.

    ![Status for installationen][04]

1. Når installationen er fuldført, skal du klikke på **Genstart IntelliJ ide**.

    ![Genstart IntelliJ ide][05]

1. Klik på **OK** for at lukke dialogboksen Indstillinger.

    ![Lukke dialogboksen IntelliJ ide indstillinger][06]

1. Når du bliver bedt om at genstarte IntelliJ ide eller udsætte, kan du klikke på **Genstart**.

    ![Genstart IntelliJ ide][07]

## <a name="to-install-the-azure-toolkit-for-intellij-from-the-start-screen"></a>Installere Azure-værktøjskassen til IntelliJ fra startskærmen

1. Start IntelliJ ide.

1. Når IntelliJ ide startskærmen vises, skal du klikke på **Konfigurer**, og klik derefter på **plug-ins**.

    ![Installere IntelliJ ide plug-ins][01b]

1. Klik på **Gennemse typer lagre**i dialogboksen **plug-ins** .

    ![Gennemse IntelliJ ide-plug-in lagre][02b]

1. Skriv "Azure" i søgefeltet i dialogboksen **Gennemse typer lagre** . Fremhæv **Azure-værktøjskassen til IntelliJ**, og klik derefter på **Installer**.

    ![Søge efter Azure værktøjerne til IntelliJ][03]

    IntelliJ ide vises status for installationen i en dialogboks.

    ![Status for installationen][04]

1. Når installationen er fuldført, skal du klikke på **Genstart IntelliJ ide**.

    ![Genstart IntelliJ ide][05]

1. Når du bliver bedt om at genstarte IntelliJ ide eller udsætte, kan du klikke på **Genstart**.

    ![Genstart IntelliJ ide][07]

## <a name="see-also"></a>Se også

Du kan finde flere oplysninger om Azure programudviklingsværktøjer til Java IDEs følgende links:

- [Azure-værktøjskassen til Eklipse]
  - [Installation af Azure værktøjerne til Eklipse]
  - [Oprette en Hej verden WebApp til Azure i Eklipse]
  - [Hvad er nyt i Azure værktøjerne til Eklipse]
- [Azure-værktøjskassen til IntelliJ]
  - *Installation af Azure værktøjerne til IntelliJ (i denne artikel)*
  - [Oprette en Hej verden WebApp til Azure i IntelliJ]
  - [Hvad er nyt i Azure værktøjerne til IntelliJ]

Du kan finde flere oplysninger om brug af Azure med Java, [Azure Java Developer Center].

<!-- URL List -->

[Azure-værktøjskassen til Eklipse]: ./azure-toolkit-for-eclipse.md
[Azure-værktøjskassen til IntelliJ]: ./azure-toolkit-for-intellij.md
[Oprette en Hej verden WebApp til Azure i Eklipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Oprette en Hej verden WebApp til Azure i IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installation af Azure værktøjerne til Eklipse]: ./azure-toolkit-for-eclipse-installation.md
[Installing the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Hvad er nyt i Azure værktøjerne til Eklipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Hvad er nyt i Azure værktøjerne til IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/

<!-- IMG List -->

[01a]: ./media/azure-toolkit-for-intellij-installation/01-intellij-file-settings.png
[01b]: ./media/azure-toolkit-for-intellij-installation/01-intellij-configure-dropdown.png
[02a]: ./media/azure-toolkit-for-intellij-installation/02-intellij-settings-dialog.png
[02b]: ./media/azure-toolkit-for-intellij-installation/02-intellij-plugins-dialog.png
[03]: ./media/azure-toolkit-for-intellij-installation/03-intellij-browse-repositories.png
[04]: ./media/azure-toolkit-for-intellij-installation/04-install-progress.png
[05]: ./media/azure-toolkit-for-intellij-installation/05-restart-intellij.png
[06]: ./media/azure-toolkit-for-intellij-installation/06-intellij-settings-dialog.png
[07]: ./media/azure-toolkit-for-intellij-installation/07-restart-intellij.png
