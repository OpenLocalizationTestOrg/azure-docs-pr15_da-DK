<properties
   pageTitle="Kommandolinjen build til Azure | Microsoft Azure"
   description="Kommandolinjen build til Azure"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="command-line-build-for-azure"></a>Kommandolinjen Build til Azure

## <a name="overview"></a>Oversigt

Du kan oprette en pakke til Azure-installation ved at køre MSBuild ved en kommandoprompt. Du kan konfigurere og definere builds til fejlfinding, midlertidige og producere, ud over at automatisere nogle af processen build.


## <a name="microsoft-build-engine-msbuild"></a>Microsoft Build program (MSBuild)

Ved hjælp af Microsoft opbygge Engine (MSBuild), kan du oprette produkter i build øvelse miljøer, hvor Visual Studio ikke er installeret. MSBuild bruger en XML-format for project-filer, der er extensible og fuldt understøttet af Microsoft. I dette filformat, du kan beskrive hvad elementer skal være bygget til en eller flere platforme og konfigurationer.

Du kan også køre MSBuild kommandoprompten, og dette emne beskrives, hvordan du håndterer det pågældende. Ved at angive egenskaber ved en kommandoprompt, kan du oprette specifikke konfigurationer af et projekt. På samme måde, kan du også definere målene kommandoen MSBuild vil oprette. Du kan finde flere oplysninger om kommandolinjeparametre og MSBuild [MSBuild kommandolinjereference](https://msdn.microsoft.com/library/ms164311.aspx).

## <a name="installation"></a>Installation

Som følgende procedure beskriver, skal du installere software og værktøjer på build-serveren, før du kan oprette en Azure-pakke ved hjælp af MSBuild:

1. Installer .NET Framework 4 eller nyere, som indeholder MSBuild.

1. Installer [Azure redigering værktøjer](http://go.microsoft.com/fwlink/?LinkId=394615) (se efter MicrosoftAzureAuthoringTools x64.msi eller MicrosoftAzureAuthoringTools x86.msi.

1. Installer [Azure biblioteker til .NET](http://go.microsoft.com/fwlink/?LinkId=394616) (se efter MicrosoftAzureLibsForNet x64.msi eller MicrosoftAzureLibs x86.msi.

1. Kopier filen Microsoft.WebApplication.targets fra en Visual Studio-installation på en anden computer.

    Filen er placeret i mappen C:\Program Files (x86) \MSBuild\Microsoft\Visual Studio\v12.0\WebApplications (v11.0 til Visual Studio 2012), og skal du kopiere den til den samme mappe på build-serveren.

1. Installer [Azure Tools til Visual Studio](http://go.microsoft.com/fwlink/?LinkId=394616).

    Se efter WindowsAzureTools.vs120.exe til at oprette Visual Studio 2013 projekter.

## <a name="msbuild-parameters"></a>MSBuild parametre

Den nemmeste måde at oprette en pakke er at køre MSBuild med den `/t:Publish` indstilling. Som standard denne kommando opretter en mappe i forhold til rodmappen for projektet, som ProjectDir\bin\Configuration\app.publish\. når du opretter et Azure projekt, du opretter to filer, pakkefilen selve og den tilhørende konfigurationsfil:

- Project.cspkg

- ServiceConfiguration.TargetProfile.cscfg

Som standard hver Azure project indeholder en tjeneste-konfigurationsfil til lokal (fejlfinding) opbygger og et andet til skyen (midlertidige eller fremstilling) builds, men du kan tilføje eller fjerne tjenestekonfiguration filer efter behov. Når du opretter en pakke i Visual Studio, bliver du spurgt hvilken tjeneste konfigurationsfil medtage sammen med pakken. Når du opretter en pakke ved hjælp af MSBuild, medtages den lokale tjenestekonfiguration fil som standard. Hvis du vil medtage en anden tjenestekonfiguration fil, skal du angive den `TargetProfile` egenskab for kommandoen MSBuild (`MSBuild /t:Publish /p:TargetProfile=ProfileName`).

Hvis du vil bruge en alternativ mappe til de lagrede pakke og af konfigurationsfiler, kan du angive stien ved hjælp af den `/p:PublishDir=Directory\` indstilling, herunder efterstillede omvendt skråstreg separatoren.

## <a name="deployment"></a>Installation

Når pakken er oprettet, kan du installere det i Azure. Et selvstudium, der har demonstrerer denne proces, skal du se webstedet Azure. Du kan finde oplysninger om, hvordan du automatisere processen [Fortløbende levering for Cloud Services i Azure](./cloud-services/cloud-services-dotnet-continuous-delivery.md).
