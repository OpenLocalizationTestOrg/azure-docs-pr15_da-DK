<properties
   pageTitle="Fejlfinding Docker klienten på Windows ved hjælp af Visual Studio | Microsoft Azure"
   description="Foretage fejlfinding af problemer, der kan opstå, når du bruger Visual Studio til at oprette og anvende Onlines til Docker på Windows ved hjælp af Visual Studio."
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="allclark" />

# <a name="troubleshooting-visual-studio-docker-development"></a>Fejlfinding i forbindelse med Visual Studio Docker udvikling

Når du arbejder med Visual Studio Tools til Docker Preview, kan du støde på nogle problemer på grund af hvilke preview.
Følgende er nogle almindelige problemer og løsninger.


## <a name="unable-to-validate-volume-mapping"></a>Kan ikke valideres lydstyrken tilknytning
Lydstyrken tilknytning er påkrævet til at dele kildekode og binære filer i dit program med mappen app i objektbeholderen.  Bestemte lydstyrken tilknytninger befinder sig i docker compose.dev.debug.yml og docker compose.dev.release.yml filer. Som filer er ændret på computeren host, afspejler beholderne disse ændringer i en lignende mappestrukturen.

Åbn **Indstillinger …** fra Docker til Windows "moby" proceslinjeikonet for at aktivere lydstyrken tilknytning, og derefter vælge fanen **Delt drev** .  Sørg for, at det bogstav, vært for dit projekt samt bogstavet på drev, hvor % BRUGERPROFIL % er placeret deles ved at markere dem og derefter klikke på **Anvend**.

Hvis du vil teste Hvis lydstyrken tilknytning fungerer, når drev, der er delt, Genopbyg og F5 fra i Visual Studio eller Prøv følgende fra en kommando Spørg:

*I en Windows-kommandoprompt*

*[Note: Dette forudsætter, at mappen brugere er placeret på drevet, "C" og, er blevet delt.  Opdatere som det er nødvendigt, hvis du har delt et andet drev]*
```
docker run -it -v /c/Users/Public:/wormhole busybox
```

*I beholderen Linux*

```
/ # ls
```

Du bør se en mappe fra mappen brugere/offentlige.
Hvis nogen filer vises, og mappen /c/Users/Public ikke er tom, vil lydstyrken tilknytning er ikke konfigureret korrekt. 

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

Ændre til mappen ormehul til at se indholdet af den `/c/Users/Public` directory:

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

**Note:** *Når du arbejder med Linux VM'er, der skelnes mellem filsystemet objektbeholder.*

##<a name="build--prepareforbuild-task-failed-unexpectedly"></a>Build: "PrepareForBuild" opgave mislykkedes uventet.

Microsoft.DotNet.Docker.CommandLine.ClientException: Der opstod en fejl forsøger at oprette forbindelse:

Kontroller værten standard docker kører. Åbn en kommandoprompt og udføre:

```
docker info
```

Hvis dette returnerer fejlen derefter forsøge at starte af **Docker til Windows** -programmet.  Hvis den på computeren kører derefter ikonet **moby** i bakken skal være synlige. Højreklik på ikonet bakke og åbne **Indstillinger**.  Klik på **Nulstil** under fanen og derefter **Genstart Docker …**.

##<a name="manually-upgrading-from-version-031-to-040"></a>Manuelt opgradering fra version 0.31 til 0,40


1. Lav sikkerhedskopier af projektet
1. Slet følgende filer i projektet:

    ```
      Dockerfile
      Dockerfile.debug
      DockerTask.ps1
      docker-compose-yml
      docker-compose.debug.yml
      .dockerignore
      Properties\Docker.props
      Properties\Docker.targets
    ```

1. Luk løsningen, og fjern følgende linjer fra filen .xproj:

    ```
      <DockerToolsMinVersion>0.xx</DockerToolsMinVersion>
      <Import Project="Properties\Docker.props" />
      <Import Project="Properties\Docker.targets" />
    ```

1. Genåbn løsningen
1. Fjern følgende linjer fra filen Properties\launchSettings.json:

    ```
      "Docker": {
        "executablePath": "%WINDIR%\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
        "commandLineArgs": "-ExecutionPolicy RemoteSigned .\\DockerTask.ps1 -Run -Environment $(Configuration) -Machine '$(DockerMachineName)'"
      }
    ```

1. Fjerne de følgende filer, der er relateret til Docker fra project.json i publishOptions:

    ```
    "publishOptions": {
      "include": [
        ...
        "docker-compose.yml",
        "docker-compose.debug.yml",
        "Dockerfile.debug",
        "Dockerfile",
        ".dockerignore"
      ]
    },
    ```

1. Fjerne den tidligere version og installere Docker værktøjer 0,40, og klik derefter på **Tilføj Docker Support->** igen i kontekstmenuen for ASP.Net Core Web eller Console-program. Dette vil tilføje de nye påkrævet Docker elementer tilbage til projektet. 

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>En fejldialogboks opstår under forsøg på at **tilføje Docker Support->** eller foretage fejlfinding (F5) et ASP.NET Core-program i en objektbeholder

Vi har nogle gange set afinstallation og installerer filtypenavne, Visual Studio MEF (administrerede udvidelsesmuligheder af Framework) cache kan beskadiget. Når dette sker, kan det medføre forskellige fejl dialogbokse, når du tilføjer Docker Support og/eller forsøger at køre eller foretage fejlfinding (F5) ASP.NET Core-program. Udføre følgende trin for at slette og genoprette MEF cachen som en midlertidig løsning.

1. Luk alle forekomster af Visual Studio
1. Åbn %USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\
1. Slet følgende mapper
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. Åbne Visual Studio
1. Forsøg at dette scenario igen 
