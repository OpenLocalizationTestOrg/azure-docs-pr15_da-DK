<properties
    pageTitle="Installation af Azure værktøjerne til Eklipse | Microsoft Azure"
    description="Få mere at vide, hvordan du installerer Azure-værktøjskassen til Eklipse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->

# <a name="installing-the-azure-toolkit-for-eclipse"></a>Installation af Azure værktøjerne til Eklipse

Azure værktøjerne til Eklipse har skabeloner og funktionalitet, som gør det muligt at nemt oprette, udvikle, teste og installere Azure-programmer bruger udviklingsmiljø Eklipse. Azure-værktøjskassen til Eklipse er et Open Source-projekt, hvis kildekode findes under MIT-licensen fra projektwebsted på GitHub på følgende URL-adressen:

<https://github.com/Microsoft/Azure-Tools-for-Java>

Følgende trin viser, hvordan du installerer Azure-værktøjskassen til Eklipse.

[AZURE.INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="to-install-the-azure-toolkit-for-eclipse"></a>Installere Azure-værktøjskassen til Eklipse

1. Start Eklipse.

1. Når Eklipse åbnes, skal du i menuen **Hjælp** og derefter klikke på **Installer ny Software**, som vist i nedenstående illustration.

    ![Installation af Azure værktøjerne til Eklipse][01]

1. Skriv **http://dl.microsoft.com/eclipse** efterfulgt af tasten **Enter** i dialogboksen **Tilgængelige programmer** , i feltet **arbejde med** tekst.

1. Markér **Azure-værktøjskassen til Eklipse**i ruden **navn** , og fjern markeringen i **alle Opdater websteder under Kontakt Installer for at finde software, der kræves**. Skærmen skal se ud som følger:

    ![Installation af Azure værktøjerne til Eklipse][02]

1. Hvis du udvider **Azure-værktøjskassen til Eklipse**, får du vist følgende elementer:

    * **Programmet indsigt plug-in til Java**: denne komponent kan du bruge Azures telemetri logføring og analysis services for dine programmer og serverforekomster.
    * **Azure Access kontrolelement Services Filter**: denne komponent yder support til godkendelse af programmet brugere med Azure ACS, aktivere enkeltlogon scenarier- og externalizing identitet logik fra programmet.
    * **Azure almindelige plug-in**: denne komponent giver den nødvendige af andre værktøjskassen komponenter almindelige funktionalitet.
    * **Azure Explorer til Eklipse**: denne komponent giver den nødvendige af andre værktøjskassen komponenter almindelige funktionalitet.
    * **Azure-plug-in til Eklipse med Java**: denne komponent yder support til udvikling af projekter, som at opbygge, teste og installere Java-programmer til Microsoft Azure skyen i Eklipse og via kommandolinjen.
    * **Azure Web Apps-plug-in med Java**: denne komponent understøtter til implementering af Java webprogrammer til Microsoft Azure Web App beholdere.
    * **Microsoft JDBC Driver 4.2 til SQL Server**: denne komponent giver JDBC API til SQL Server og Microsoft Azure SQL-Database til Java Platform Enterprise Edition 8.
    * **Pakke til Apache Qpid klientbiblioteker for JMS**: denne komponent giver komponenten JMS klient fra Apache Qpid projektet til at gøre det muligt at bruge AMQP messaging i Microsoft Azure.
    * **Pakke til Microsoft Azure-biblioteker til Java**: denne komponent giver API'er for at få adgang til Microsoft Azure-tjenester, som lager, service bus, service runtime osv.

1. Klik på **Næste**. (Hvis du oplever usædvanlige forsinkelser, når du installerer værktøjerne, skal sikre dig, **Kontakt alle Opdater websteder under installere for at finde software, der kræves** er markeret.)

1. Klik på **Næste**i dialogboksen **Installere oplysninger** .

    ![Gennemse oplysninger om Installation][03]

1. Gennemse vilkårene for license-aftaler i dialogboksen **Gennemse licenser** . Hvis du accepterer vilkårene for license-aftaler, skal du klikke på **jeg accepterer vilkårene i license-aftaler** , og klik derefter på **Udfør**. (De resterende trin forudsætter, du accepterer vilkårene for licensaftaler. Hvis du ikke accepterer vilkårene for license-aftaler, Afslut installationsprocessen.)

    ![Gennemse licenser][04]

    Eklipse vil hente og installere de påkrævede pakker.

    ![Status for installationen][05]

1. Klik på **Ja**, hvis du bliver bedt om at genstarte Eklipse for at fuldføre installationen.

    ![Genstart Prompt][06]

## <a name="see-also"></a>Se også

Du kan finde flere oplysninger om Azure programudviklingsværktøjer til Java IDEs følgende links:

- [Azure-værktøjskassen til Eklipse]
  - *Installation af Azure værktøjerne til Eklipse (i denne artikel)*
  - [Oprette en Hej verden WebApp til Azure i Eklipse]
  - [Hvad er nyt i Azure værktøjerne til Eklipse]
- [Azure-værktøjskassen til IntelliJ]
  - [Installation af Azure værktøjerne til IntelliJ]
  - [Oprette en Hej verden WebApp til Azure i IntelliJ]
  - [Hvad er nyt i Azure værktøjerne til IntelliJ]

Du kan finde flere oplysninger om brug af Azure med Java, [Azure Java Developer Center].

<!-- URL List -->

[Azure-værktøjskassen til Eklipse]: ./azure-toolkit-for-eclipse.md
[Azure-værktøjskassen til IntelliJ]: ./azure-toolkit-for-intellij.md
[Oprette en Hej verden WebApp til Azure i Eklipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Oprette en Hej verden WebApp til Azure i IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installing the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Installation af Azure værktøjerne til IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Hvad er nyt i Azure værktøjerne til Eklipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Hvad er nyt i Azure værktøjerne til IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/

<!-- IMG List -->

[01]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-01.png
[02]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-02.png
[03]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-03.png
[04]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-04.png
[05]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-05.png
[06]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-06.png

