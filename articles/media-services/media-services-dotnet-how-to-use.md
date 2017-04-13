<properties 
    pageTitle="Sådan konfigureres Computer til udvikling af Media Services med .NET" 
    description="Få mere at vide om forudsætninger for Media Services ved hjælp af Media Services SDK til .NET. Også se, hvordan du opretter en Visual Studio-app." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/24/2016"  
    ms.author="juliako"/>

#<a name="media-services-development-with-net"></a>Udvikling af Media Services med .NET

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Dette emne beskrives, hvordan du starter udviklingsprogrammer Media Services ved hjælp af .NET.

Biblioteket **Azure Media Services.NET SDK** gør det muligt at programmere mod Media Services ved hjælp af .NET. Hvis du vil gøre det endnu nemmere at udvikle med .NET, leveres biblioteket **Azure Media Services .NET SDK filtypenavne** . Dette bibliotek indeholder et sæt af lokalnummer metoder og hjælpefunktioner, der kan forenkle din .NET-kode. Begge biblioteker er tilgængelige via **NuGet** og **GitHub**.


##<a name="prerequisites"></a>Forudsætninger

-   En Media Services-konto i et nyt eller eksisterende Azure abonnement. Se emnet, [hvordan du opretter en Media Services-konto](media-services-portal-create-account.md).
-   Operativsystemer: Windows 10, Windows 7, Windows 2008 R2 eller Windows 8.
-   .NET framework 4.5.
-    Visual Studio 2015, Visual Studio 2013, Visual Studio 2012 eller Visual Studio 2010 SP1 (professionel, Premium, Ultimate eller Express).


##<a name="create-and-configure-a-visual-studio-project"></a>Oprette og konfigurere et Visual Studio-projekt

Dette afsnit viser, hvordan du opretter et projekt i Visual Studio og implementerer Media Services udvikling.  I dette tilfælde projektet er en C# Windows console-program, men de samme konfigurationstrin, der er vist her gælder for andre typer projekter, kan du oprette for Media Services-programmer (for eksempel et Windows Forms-program eller en ASP.NET-webprogram).

Dette afsnit viser, hvordan du bruger **NuGet** til at tilføje Media Services .NET SDK og andre afhængige biblioteker.

Du kan også få de seneste Media Services .NET SDK bit fra GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) og [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), oprette løsningen og tilføje referencer til klientprojektet. Bemærk, at alle de nødvendige afhængigheder få hentede og udpakkede automatisk.

1. Oprette et nyt C# Console-program i Visual Studio 2010 SP1 eller nyere eller-versioner. Angiv det **navn**, **placering**og **løsningsnavn**, og klik derefter på OK.

2. Oprette løsningen.

2. Brug **NuGet** til at installere, og Tilføj **Azure Media Services .NET SDK udvidelser**. Installationen af denne pakke, også installerer **Media Services.NET SDK** og tilføjer alle andre nødvendige afhængigheder.

    Sørg for, at du har den nyeste version af NuGet, der er installeret. Du kan finde flere oplysninger og en installationsvejledning [NuGet](http://nuget.codeplex.com/).

2. Højreklik på navnet på projektet i Solution Explorer, og vælg Administrer NuGet pakker...

    Dialogboksen Administrer NuGet pakker vises.

3. Søg efter Azure MediaServices udvidelser, Vælg Azure Media Services .NET SDK filtypenavne, og klik derefter på knappen Installer i galleriet Online.

    Projektet er ændret, og referencer til Media Services .NET SDK-udvidelser, Media Services .NET SDK og andre afhængige samlinger der tilføjes.

4. Hvis du vil fremhæve et renere udviklingsmiljø Overvej at aktivere NuGet pakke gendanne. Du kan finde flere oplysninger, se [NuGet pakke gendanne "](http://docs.nuget.org/consume/package-restore).

3. Tilføje en henvisning til **system.Configuration i stor** samling. Denne samling indeholder system.Configuration i stor. **ConfigurationManager** klasse, der bruges til at få adgang til af konfigurationsfiler (for eksempel App.config).

    Hvis du vil tilføje referencer ved hjælp af dialogboksen Administrer referencer, skal du højreklikke på projektnavnet i Solution Explorer. Vælg derefter Tilføj og referencer.

    Dialogboksen Administrer referencer vises.

4. Under .NET framework assemblies, find og vælg samlingen system.Configuration i stor og trykke på OK.
5. Åbn filen App.config (Tilføj filen til dit projekt, hvis meddelelsen ikke blev tilføjet som standard) og tilføje en *appSettings* sektion til filen.     
Angiv værdier for din Azure Media Services navn og konto kontonøgle, som vist i følgende eksempel.

    Gå til Azure-portalen for at finde navnet på og nøgle værdier, og vælg din konto. Vinduet indstillinger vises til højre. Vælg taster i vinduet indstillinger. At klikke på ikonet ud for hver tekstboks kopieres til Udklipsholder.


        <configuration>
        ...
            <appSettings>
              <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
              <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
            </appSettings>

        </configuration>

6. Overskriv eksisterende **ved hjælp af** sætningerne i starten af filen Program.cs med følgende kode.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;

På dette tidspunkt er du klar til at udvikle et Media Services-program.    


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
