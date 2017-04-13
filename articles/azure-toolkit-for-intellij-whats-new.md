<properties
    pageTitle="Hvad er nyt i Azure værktøjerne til IntelliJ | Microsoft Azure"
    description="Få mere at vide om de nyeste funktioner i Azure-værktøjskassen til IntelliJ."
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
    ms.date="08/26/2016" 
    ms.author="robmcm;asirveda;martinsawicki"/>

# <a name="whats-new-in-the-azure-toolkit-for-intellij"></a>Hvad er nyt i Azure værktøjerne til IntelliJ

## <a name="azure-toolkit-for-intellij-releases"></a>Azure-værktøjskassen til IntelliJ versioner

I denne artikel indeholder oplysninger om de forskellige versioner og seneste opdateringer til Azure-værktøjskassen til IntelliJ.

> [AZURE.NOTE] Der er også en Azure-værktøjskassen til Eklipse IDE. Du kan finde flere oplysninger [Azure-værktøjskassen til Eklipse].

### <a name="august-26-2016"></a>August 26 2016

Azure-værktøjskassen til IntelliJ - August 2016 version omfatter følgende forbedringer:

* **Brugerdefinerede JDK Salgsdistributioner**. Azure-værktøjskassen til IntelliJ understøtter nu angive og implementere en vilkårlig JDK version til din Azure WebApp objektbeholder:
  - Ud over de JDKs, der leveres af Azure, kan du også vælge fra et bredt udvalg af Zulu OpenJDK versioner gøres tilgængelige på Azure af Azul Systems.
  - Du kan også angive dine egne JDK fordeling, hvis du overfører som en ZIP-fil til kontoen lagerplads.
* **Forbedringer til Azure Stifindervisning**:
  - Understøttelse af virtuelt administration ved hjælp af Azures nye ressourcestyring model: Du kan listen, oprette og slette ressource manager-baserede virtuelle maskiner uden at forlade IDE-enheden.
  - Understøttelse af lagerplads blob kontoadministration ved hjælp af Azures Ressourcestyring, som er et supplement den eksisterende funktionalitet til administration af "klassisk" lagerplads konti.
* **Microsoft JDBC Driver 6.0 til SQL Server**. Denne opdatering indeholder den nyeste JDBC driver til Microsoft SQL Server (v6.0), som er nu medtaget som et bibliotek, kan du nemt føje til dine Java-projekter, og erstatte den gamle version.

### <a name="june-29-2016"></a>29 juni 2016

Azure-værktøjskassen til IntelliJ - juni 2016 version omfatter følgende forbedringer:

* **Java 8 krav**. Azure-værktøjskassen til IntelliJ kræver nu Java 8, selvom dette krav gælder kun for værktøjerne - programmerne kan fortsætte med at bruge alle versioner af Java, der understøttes af Azure.
* **Understøttelse af den seneste Java JDKs**. De seneste versioner af Java JDKs understøttes nu ved Azure-værktøjskassen til IntelliJ.
* **Understøttelse af Azure SDK v2.9.1**. Den seneste version af Azure SDK er nu den mindste foreløbig nødvendige til Azure-værktøjskassen til IntelliJ.
* **Integreret eksempler**. Azure-værktøjskassen til IntelliJ indeholder nu flere eksempler på programmer til at hjælpe udviklere med at komme i gang.
* **Integration af HDInsight værktøj**. Azure's HDInsight værktøjer er nu samlet med Azure-værktøjskassen til IntelliJ. Du kan finde yderligere oplysninger finder [HDInsight værktøjer plug-in til IntelliJ].
* **Remote fejlfinding af Java Webapps**. Azure-værktøjskassen til IntelliJ understøtter nu ekstern fejlfinding af Java webapps på Azure App Service.

### <a name="april-12-2016"></a>April 12 2016

Azure-værktøjskassen til IntelliJ - April 2016 version omfatter følgende forbedringer:

* **Understøttelse af Azure SDK v2.9.0**. Den seneste version af Azure SDK er nu den mindste foreløbig nødvendige til Azure-værktøjskassen til IntelliJ.
* **Diverse brugervenligheden, tilgængelighed og ydeevne forbedringer, der er relateret til Azure online support**. Et antal ydeevne optimeringer i hvordan værktøjerne kommunikerer med Azure resultatet i en hurtigere brugergrænseflade.
* **Mulighed for at slette en eksisterende webprogram objektbeholder i Azure fra i IntelliJ**. Azure-værktøjskassen til IntelliJ nu kan du slette en eksisterende Azure Web objektbeholder uden at forlade IntelliJ.

## <a name="see-also"></a>Se også ##

Du kan finde flere oplysninger om Azure programudviklingsværktøjer til Java IDEs følgende links:

- [Azure-værktøjskassen til Eklipse]
  - [Installation af Azure værktøjerne til Eklipse]
  - [Oprette en Hej verden WebApp til Azure i Eklipse]
  - [Hvad er nyt i Azure værktøjerne til Eklipse]
- [Azure-værktøjskassen til IntelliJ]
  - [Installation af Azure værktøjerne til IntelliJ]
  - [Oprette en Hej verden WebApp til Azure i IntelliJ]
  - *Hvad er nyt i Azure værktøjerne til IntelliJ (i denne artikel)*

Du kan finde flere oplysninger om brug af Azure med Java, [Azure Java Developer Center].

<!-- URL List -->

[Azure-værktøjskassen til Eklipse]: ./azure-toolkit-for-eclipse.md
[Azure-værktøjskassen til IntelliJ]: ./azure-toolkit-for-intellij.md
[Oprette en Hej verden WebApp til Azure i Eklipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Oprette en Hej verden WebApp til Azure i IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installation af Azure værktøjerne til Eklipse]: ./azure-toolkit-for-eclipse-installation.md
[Installation af Azure værktøjerne til IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Hvad er nyt i Azure værktøjerne til Eklipse]: ./azure-toolkit-for-eclipse-whats-new.md
[What's New in the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547

[HDInsight værktøjer plug-in til IntelliJ]: ./hdinsight/hdinsight-apache-spark-intellij-tool-plugin.md
