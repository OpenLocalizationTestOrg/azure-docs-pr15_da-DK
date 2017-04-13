<properties 
   pageTitle="Fejlfinding af U-SQL-job | Microsoft Azure" 
   description="Få mere at vide, hvordan du udfører fejlfinding U-SQL mislykkedes knudepunkt ved hjælp af Visual Studio. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/02/2016"
   ms.author="jgao"/>



#<a name="debug-c-code-in-u-sql-for-data-lake-analytics-jobs"></a>Fejlfinding af C#-kode i U-SQL for Data sø Analytics sager 

Lær, hvordan du bruger Azure Data sø Visual Studio-værktøjer til at foretage fejlfinding af mislykkede U-SQL-job på grund af fejl i brugerkode. 

Værktøjet Visual Studio giver mulighed at hente kompilerede kode og det er nødvendigt knudepunkt data fra klynge til at spore og fejlfinde mislykkede job.

Stor datasystemer indeholder som regel udvidelsesmuligheder af model gennem sprog som Java, C#, Python osv. Mange disse systemer giver begrænset runtime fejlfindingsoplysninger, der gør det svært at foretage fejlfinding af kørselsfejl i brugerdefineret kode. De seneste Visual Studio-værktøjer leveres med en funktion, der hedder "Fejlfinding af mislykkedes knudepunkt". Bruger denne funktion, kan du hente runtime-data fra Azure til lokale arbejdsstation så du kan foretage fejlfinding af mislykkede brugerdefinerede C#-kode med samme runtime og nøjagtige indtastede data fra skyen.  Når problemerne, rettes, kan du igen køre den reviderede kode i Azure fra værktøjerne.

Se [fejlfinding af din brugerdefinerede kode i Azure Data sø Analytics](https://mix.office.com/watch/1bt17ibztohcb)for en video præsentation af denne funktion.

>[AZURE.NOTE] Visual Studio kan hænger eller går ned, hvis du ikke har de følgende to vinduer opgraderinger: [Microsoft Visual C++ 2015 Redistributable opdatering 2](https://www.microsoft.com/download/details.aspx?id=51682), [Universal C Runtime til Windows](https://www.microsoft.com/download/details.aspx?id=50410&wa=wsignin1.0).


##<a name="prerequisites"></a>Forudsætninger
-   Har gennemgået artiklen [Introduktion](data-lake-analytics-data-lake-tools-get-started.md) .

## <a name="create-and-configure-debug-projects"></a>Oprette og konfigurere fejlfinding projekter

Når du åbner en mislykkede job i Data sø Visual Studio værktøj, får du en besked. Oplysninger om detaljerede fejl vises i fanen fejl og den gule beskeder om bjælke øverst i vinduet. 

![Azure Data sø Analytics U-SQL fejlfinding visual studio download knudepunkt](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

**Hente knudepunkt og opretter en løsning, fejlfinding**

1.  Åbn en mislykkede U-SQL-job i Visual Studio.
2.  Klik på **Hent** for at hente alle de nødvendige ressourcer og input streams. Klik på **Prøv igen** , hvis overførslen mislykkedes.
3.  Klik på **Åbn** , når overførslen er færdig, for at oprette en lokal fejlfinding projekt. Der oprettes en ny Visual Studio-løsning kaldet **VertexDebug** med et tomt projekt kaldet **LocalVertexHost** .

Hvis brugerdefinerede operatorer bruges i U-SQL-koden bag (Script.usql.cs), skal du oprette en klasse bibliotek C#-projekt med brugerdefinerede operatorer kode og medtage projektet i løsningen VertexDebug.

Hvis du har registreret dll-assemblies til Data sø Analytics-databasen, skal du føje kildekode af enheder til VertexDebug løsningen.
 
Hvis du har oprettet en separat C# klassebibliotek til U-SQL-koden og registrerede dll-assemblies til Data sø Analytics-databasen, skal du føje C# kildeprojektet-enheder til VertexDebug løsningen.

I visse tilfælde kan du bruge brugerdefinerede operatorer i U-SQL-koden bag (Script.usql.cs)-fil i den oprindelige løsning. Hvis du vil gøre det arbejde, skal du oprette et C#-bibliotek, der indeholder kildekoden og ændre navnet på samling til den, der er registreret i klyngen. Du kan få den samling navn, der er registreret i klyngen ved at markere scriptet, har du kører i klyngen. Du kan gøre det ved at åbne U-SQL jobbet og klikke på "script" i panelet job. 

**Konfigurere løsningen**

1.  Højreklik på det C#-projekt, du lige har oprettet fra Solution explorer, og klik derefter på **Egenskaber**.
2.  Angiv stien Output som LocalVertexHost projekt arbejder mappesti. Du kan få LocalVertexHost project arbejdsmappe sti via LocalVertexHost egenskaber.
3.  Opbygge dit C#-projekt for at sætte filen .pdb i projektets LocalVertexHost arbejdsmappe, eller du kan kopiere filen .pdb til denne mappe manuelt.
4.  Kontrollér almindelige sprog Runtime undtagelser i **Undtagelse indstillinger**:

![Azure Data sø Analytics U-SQL fejlfinding visual studio indstilling](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)
 
##<a name="debug-the-job"></a>Fejlfinding af jobbet

Når du har oprettet en fejlfinding løsning ved at hente knudepunktet og har konfigureret miljøet, kan du begynde fejlfinding U-SQL-koden.

1.  Fra Solution Explorer, skal du højreklikke på det **LocalVertexHost** projekt, du lige har oprettet, peg på **fejlfinding**og derefter klikke på **Start ny forekomst**. LocalVertexHost skal være angivet som projektets start. Du kan få vist følgende meddelelse for første gang, kan du ignorere. Det kan tage op til et minut til at åbne skærmbilledet fejlfinding.
 
    ![Azure Data sø Analytics U-SQL fejlfinding visual studio advarsel](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

4.  Brug Visual Studio baseret fejlfinding oplevelse (se, variabler, osv.) til at foretage fejlfinding af problemet. 
5.  Når du har fundet et problem, ret koden, og derefter genopbygge projektet C# før du tester den igen, indtil alle problemer er løst. Efter fejlfinding er blevet fuldført, outputvinduet, der viser følgende meddelelse 

        The Program ‘LocalVertexHost.exe’ has exited with code 0 (0x0).
 
##<a name="resubmit-the-job"></a>Indsend igen jobbet

Når du har fuldført fejlfinding U-SQL-koden, kan du sende det mislykkede job igen.

1. Registrere nye dll-assemblies til databasen ADLA.

    1.  Udvid noden **databaser** fra Server Explorer/skyen Explorer i Data sø Visual Studio værktøj 
    2.  Højreklik på Assemblies til Register assemblies. 
    3.  Registrere din nye dll-assemblies til ADLA-databasen.
 
2.  Eller kopiere din C#-kode til script.usql.cs – C#-kode bag fil.
3.  Indsend igen tingene.

##<a name="next-steps"></a>Næste trin

- [Selvstudium: Introduktion til Azure Data sø Analytics U-SQL-sprog](data-lake-analytics-u-sql-get-started.md)
- [Selvstudium: udvikle U-SQL-scripts ved hjælp af Data sø Tools til Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Udvikle U-SQL brugerdefinerede operatorer til Azure Data sø Analytics-job](data-lake-analytics-u-sql-develop-user-defined-operators.md)

