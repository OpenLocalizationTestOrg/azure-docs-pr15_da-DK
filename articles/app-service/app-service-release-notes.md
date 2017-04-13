<properties 
   pageTitle="Produktbemærkninger til Azure SDK til .NET 2.5.1" 
   description="Produktbemærkninger til Azure SDK til .NET 2.5.1" 
   services="app-service" 
   documentationCenter=".net,nodejs,java" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/10/2016"
   ms.author="juliako"/>


# <a name="azure-sdk-for-net-251-release-notes"></a>Produktbemærkninger til Azure SDK til .NET 2.5.1

Dette dokument indeholder produktbemærkninger til Azure SDK til .NET 2.5.1 slip. 

##<a name="azure-sdk-for-net-251-release-notes"></a>Azure SDK til .NET 2.5.1 produktbemærkninger

Følgende er nye funktioner og opdateringer i Azure SDK til .NET 2.5.1.

- Ny features\scenarios, der er relateret til **Web værktøjer Extensions**. 

    - Azure websteder er blevet omdøbt til Azure App Service. Yderligere oplysninger finder [Azure App tjenesten og eksisterende Azure-tjenester](app-service-changes-existing-services.md).
    - Understøttelse af Azure API Apps (Preview) er blevet tilføjet, så kunderne kan publicere ASP.NET projekter som API Apps, og brug derefter Tilføj > Azure API App klient bevægelse i C# projekter for at generere kode, der er baseret på strukturen af de installerede API-App. 
    - Noden websteder i Server Explorer frarådes i stedet for noden Azure App Service, som indeholder understøttelse af ressource, gruppe-baserede gruppering af Azure API Apps, Mobile-Apps og Web Apps.
    - Understøttelse af Azure Mobile-Apps (Preview) er blevet tilføjet, så kunderne kan oprette nye projekter i Mobile-Apps, tilføje Mobile-Apps enheder, publicere projekterne og fra en fjernplacering fejlfinde programmer.
    - Tilføje > Azure API App klient bevægelse understøtter nu lokale Swagger JSON-filer, så Web API udviklere kan bruge fra tredjepart NuGets som Swashbuckle til at generere Swagger eller redigere den manuelt. Denne måde klient udviklere kan bruge funktionerne til oprettelse af kode til at bruge en hvilken som helst Swagger slutpunkt i C# projekter. 
    - Web App og API App publicering dialogbokse er blevet forbedret for at understøtte ressource gruppering Azure Portal konceptet, og markeringen/oprettelse af Azure grupper og App-tjenesten planer er repræsenteret i den nye Web App og API App klargøring dialogboks. 
    - Azure API App Server Explorer noder giver dig links til linket API Apps deep i portalen Azure samt andre funktioner som Log Streaming og ekstern fejlfinding.

    Kendte problemer og aktuelle begrænsninger i Azure SDK .NET 2.5.1 [denne](app-service-release-notes.md#known_issues_2_5_1) afsnittet nedenfor.


- Ny features\scenarios, der er relateret til **HDInsight værktøjer** i Visual Studio er aktiveret i denne udgave. 
    - Lokale validering af hive scripts. Klik på knappen Valider script på værktøjslinjen for at se, om der er fejl i dit script. 
    - Forbedret fejlfinding af Hive job. Du kan nu foretage fejlfinding Hive job ved at åbne garn logge i Visual Studio. Hvis dit program har problemer med ydeevnen, give ved at undersøge GARN logfiler nyttige oplysninger..
    - (Public Preview) Nøgleord automatisk-fuldførelse og IntelliSense understøttelse af Hive. For at du opretter Hive scripts, HDInsight Tools til Visual Studio tilføjet nøgleord automatisk-fuldførelse og IntelliSense understøttelse af Hive.
    - Storm support. Du kan nu bruge HDInsight Tools til Visual Studio til at udvikle Storm topologier/Spouts/bolte i C#. Du kan derefter sende udviklede topologien til en Storm klynge og får du vist topologi/bolt/tud status. Du kan bruge kunde logfiler og systemets logfiler til fejlfinding i forbindelse med din Storm topologier/bolte/Spouts. Du kan også bruge eksisterende JAVA aktiver i Storm på HDInsight.
    
    Se [Introduktion til brug af HDInsight Hadoop-værktøjer til Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)kan finde flere oplysninger.



##<a id="known_issues_2_5_1"></a>Azure SDK til .NET 2.5.1 kendte problemer og begrænsninger

- Azure API-Apps kan ses som installation destination for Mobile-Apps. Web Apps skal være kun destinationen for Mobile-Apps indtil en senere version. 
- Azure API App klargøring kan medføre succes, men mislykkes periodisk til at opdatere status i vinduet Azure App tjenestens aktivitet. Løsning er at kontrollere status for den nye Azure API App på portalen Azure. 
- Filer > nyt projekt > API App > F5 oplevelse resulterer i en HTTP-fejl, fordi der ikke er nogen default/index.html. Løsning er at gå manuelt til /api/values URL-adressen. 
- Server Explorer ikoner vises periodisk, flad. Genstart VS løser dette problem. 
- Hvis der genereres en undtagelse under Web App eller API App klargøringen (såsom overskredne kvotefejl eller dublerede Azure API App gateway-navn), vises fejlene nogle uformateret JSON-tekst. 
- Forbigående projektoprettelse problemer, når programmet indsigt er markeret på klokkeslættet for oprettelsen af project.
- Nogle gange kan den genererede Azure API App klient-kode mangler navneområder, de skal være manuelt inkluderet (eller automatisk importeret via Visual Studio signaler) for at samle kode. 
- Mobile-App projekter skal publiceres til webapps, men du skal vælge et websted, du har oprettet som et Mobile-App i portalen Azure, da Mobile-App projekter kræver en database. 
- Startside til Mobile-Apps bruger ordet "mobile service" i stedet for "mobilapps" 
- Mobile-App projektoprettelse kan tage til et minut til at oprette. 
- Under API App kommer klargøring (i nogle tilfælde) en fejl tilbage fra Azure API med besked om, at tilladelserne, der ikke kunne angives korrekt, mens API App er klargjort korrekt og er klar til brug. Du kan angive tilladelser ved hjælp af portalen Azure manuelt.
- Programmet indsigt understøttes ikke på API App-skabeloner og Mobile-App-skabeloner.
- API App projekter kan ikke bruges sammen med skytjeneste projekter.
- API App project-skabeloner er kun tilgængelige i C#.
- API App forbrug via genvejsmenuen "Tilføj Azure API App klient" understøttes kun i C#.

 
