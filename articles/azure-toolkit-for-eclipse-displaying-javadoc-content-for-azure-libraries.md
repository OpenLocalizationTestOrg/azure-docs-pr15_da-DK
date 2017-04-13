<properties
    pageTitle="Vise Javadoc indhold i Eklipse til Azure biblioteker pakken til Java"
    description="Sådan får du vist Javadoc indhold til Azure-biblioteker i Eklipse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh698319.aspx -->

# <a name="displaying-javadoc-content-in-eclipse-for-the-azure-libraries-package-for-java"></a>Vise Javadoc indhold i Eklipse til Azure biblioteker pakken til Java #

Javadoc indholdet til Azure-biblioteker til Java kan ses i dit miljø Eklipse ved at knytte Javadoc indhold til Azure-biblioteker til Java. Følgende trin viser, hvordan du bruge denne funktion i Eklipse.

Denne procedure antages det, du allerede har tilføjet biblioteket Azure til Java til din build sti.

## <a name="to-display-javadoc-content-in-eclipse-for-the-azure-libraries-for-java"></a>Vise Javadoc indhold i Eklipse for Azure-biblioteker til Java ##

* I Eklipses Projektstifinder du i afsnittet **Refereres til biblioteker** på dit projekt skal åbne genvejsmenuen for biblioteket Azure til Java JAR. For eksempel **microsoft-windowsazure-api-0.1.0.jar** (versionsnummeret kan være forskellige, afhængigt af hvilken version du har installeret).
* Klik på **Egenskaber**.
* Klik på **Javadoc placering**i dialogboksen **Egenskaber** i den venstre rude. Dialogboksen **Javadoc placering** vises.
* Du kan angive en **Javadoc URL-adresse**eller en **Javadoc i arkiv**.
    * Hvis du vil angive en **Javadoc URL-adresse**, du brug URL-adresser som **http://dl.windowsazure.com/javadoc** eller **http://dl.windowsazure.com/storage/javadoc**.
    * Hvis du vælger at bruge **Javadoc i arkiv**, kan du angive en ekstern fil eller en arbejdsområdefil.
    Foretag dit valg og gennemse/validere efter behov. I følgende eksempel knyttes Azure-biblioteker til Java med den tilsvarende Javadoc JAR, der er hentet lokalt til en mappe med navnet **c:\MyAzureJARs**.
    ![][ic553487]
* *Valgfrit*: Klik på **Valider**. Mulige problemer med Javadoc JAR kunne vises her.
* Klik på **OK**.

Når der er knyttet til biblioteket, skal vise Javadoc indholdet i din Eklipse IDE. Eksempelvis hvis `blob` er defineret af typen `CloudBlockBlob` i din kode, følgende er et eksempel på Javadoc indhold, der vises, når du skriver `blob.acquireLease` kode:

![][ic553488]

## <a name="see-also"></a>Se også ##

[Azure-værktøjskassen til Eklipse][]

[Oprette et Hej verden program til Azure i Eklipse][]

[Installation af Azure værktøjerne til Eklipse][] 

Du kan finde flere oplysninger om brug af Azure med Java, [Azure Java Developer Center][].

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure-værktøjskassen til Eklipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Oprette et Hej verden program til Azure i Eklipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installation af Azure værktøjerne til Eklipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic553487]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553487.png
[ic553488]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553488.png