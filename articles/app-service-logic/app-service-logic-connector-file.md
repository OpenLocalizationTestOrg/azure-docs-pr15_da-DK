<properties
    pageTitle="Ved hjælp af filen connector i logik apps | Microsoft Azure App Service"
    description="Hvordan du kan oprette og konfigurere fil forbindelse eller API app og bruge det i en logik app i Azure App Service"
    authors="rajeshramabathiran"
    manager="erikre"
    editor=""
    services="logic-apps"
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="rajram"/>

# <a name="get-started-with-the-file-connector-and-add-it-to-your-logic-app"></a>Komme i gang med filen forbindelsen og tildele den til din logik app
>[AZURE.NOTE] Denne version i denne artikel gælder for logik apps 2014-12-01-skema prøveversionen.

Oprette forbindelse til et filsystem til at overføre, kan du hente og meget mere til dine filer på en værtsmaskinen. Logik apps kan udløse baseret på en række forskellige datakilder og tilbud forbindelser for at hente og behandle data. Du kan føje fil-forbindelse til virksomhedens arbejdsgange og processer data som en del af denne arbejdsproces i en logik app. 

Fil forbindelsen bruger Forbindelsesstyring Hybrid for hybride connectivity til host-filsystemet.

## <a name="creating-a-file-connector-for-your-logic-app"></a>Oprette en fil forbindelse til din logik app ##
Hvis du vil bruge fil forbindelsen, skal du først oprette en forekomst af filen forbindelse API app. Dette kan gøres på følgende måde:

1.  Åbn den Azure Marketplace ved hjælp af plustegnet (+) ny indstilling i venstre side af portalen Azure.
2.  Søge efter "fil forbindelse".
3.  Markér **Filen forbindelse (preview)** i søgeresultaterne.
4.  Vælg knappen **Opret**
5.  Konfigurere fil forbindelsen på følgende måde:  
![][1]

    - **Navn** - Giv et navn til fil-forbindelsen
    - **Pakkeindstillinger**
        - **Rodmappen** - angive stien til rodmappen på computeren host. F.eks. D:\FileConnectorTest
        - **Tjenesten Bus forbindelsesstreng** - giver en tjeneste Bus forbindelsesstreng. Sørg for, at navneområdet service bus er af typen Standard og ikke grundlæggende til at tillade brug af tjenesten Bus relæer.  Tjenesten Bus Relay bruges til at oprette forbindelse til Hybrid Forbindelsesstyring.
    - **App-serviceaftale** - vælge eller oprette en App-serviceaftale
    - **Priser niveau** - Vælg et priser trin til connector
    - **Ressourcegruppe** - vælge eller oprette en ressourcegruppe, hvor forbindelsen skal være medlem
    - **Abonnement** - Vælg et abonnement, du vil denne forbindelse skal have oprettet i
    - **Placering** – Vælg den geografiske placering, hvor du vil have forbindelsen skal installeres

4. Klik på Opret. Der oprettes en ny fil-forbindelse

## <a name="configure-hybrid-connection-manager"></a>Konfigurere Hybrid Forbindelsesstyring ##
Når forekomsten API App er oprettet, skal du gå til dens dashboard.  Dette kan gøres ved at klikke på Gennemse > API Apps > Vælg fil forbindelsen API App.  Herfra skal Forbindelsesstyring Hybrid konfigureres.
Finde flere oplysninger om konfiguration og Forbindelsesstyring Hybrid skal lukkes [med Forbindelsesstyring Hybrid].

## <a name="using-the-file-connector-in-your-logic-app"></a>Brug af filen forbindelsen i din logik app ##
Når din API app er oprettet, kan du nu bruge forbindelsen fil som en handling til din logik app. For at gøre dette, skal du:

1.  Oprette en ny logik app, og vælg den samme ressourcegruppe som har fil forbindelsen. Følg instruktionerne til at [oprette en ny logik app].

2.  Åbn "Udløsere og handlinger" oprettet logik App til at åbne logik apps Designer og konfigurere dit rutediagram.

3.  Fil forbindelsen skal vises i afsnittet "API Apps i denne ressourcegruppe" i galleriet på højre side.

4.  Du kan slippe filer forbindelse API app i editoren ved at klikke på "filer forbindelsen". fil forbindelse Fremviser én udløser og 4 handlinger:  
![][5]

6.  Hver enkelt af disse Fremviser bestemte egenskaber. Billedet nedenfor viser egenskaberne for udløseren og få fil handling:  
![][6]

7. Når disse er konfigureret, kan udløser og handling bruges i dit rutediagram. På samme måde, kan andre handlinger konfigureres samt.

> [AZURE.NOTE] Fil udløser, slettes filen, når den er blevet læst fra mappen.

## <a name="file-connector-rest-apis"></a>Arkivere forbindelse REST API'er ##
De REST API'er, der vises af forbindelsen kan bruges til at bruge forbindelsen uden for en logik app. Du kan få vist denne API definitioner ved hjælp af Gennemse -> Api App -> fil forbindelse. Klik nu på objektiv til API Definition under afsnittet Oversigt til at få vist alle de API'er, der vises af denne forbindelse:  
![][7]

Oplysninger om API'erne kan findes på [fil forbindelse API definition].

## <a name="do-more-with-your-connector"></a>Få mere ud af forbindelsen
Nu hvor forbindelsen er oprettet, kan du tilføje den til en arbejdsprocesser for virksomheder ved hjælp af en logik app. Se [Hvad er logik apps?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Hvis du vil Introduktion til Azure logik apps før tilmelding til en Azure-konto, skal du gå til [prøve logik app](https://tryappservice.azure.com/?appservice=logic), hvor du straks kan oprette en forbigående starter logik app i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.

Få vist Swagger REST-API reference på [forbindelser og API Apps Reference](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Du kan også gennemse ydeevne statistik og kontrollere sikkerhed til forbindelsen. Se [administrere og overvåge dine indbyggede API Apps og forbindelse](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[Oprette en ny logik-app]: app-service-logic-create-a-logic-app.md
[Fil forbindelse API definition]: https://msdn.microsoft.com/library/dn936296.aspx
[Ved hjælp af Forbindelsesstyring Hybrid]: app-service-logic-hybrid-connection-manager.md
