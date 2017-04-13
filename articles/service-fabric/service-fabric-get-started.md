<properties
   pageTitle="Konfigurere din udviklingsmiljø | Microsoft Azure"
   description="Installere runtime, SDK og værktøjer, og oprette en lokal udvikling klynge. Når installationen er fuldført, vil du være klar til at opbygge programmer."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/26/2016"
   ms.author="ryanwi"/>

# <a name="prepare-your-development-environment"></a>Forberede din udviklingsmiljø

> [AZURE.SELECTOR]
-[Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [ALLE OSX](service-fabric-get-started-mac.md)

 Oprette og køre [Azure Service strukturen programmer] [ 1] på computeren udvikling installere runtime, SDK og værktøjer. Du skal også aktivere udførelse af Windows PowerShell-scripts, der er inkluderet i SDK.

## <a name="prerequisites"></a>Forudsætninger
### <a name="supported-operating-system-versions"></a>Understøttet operativsystemversioner
Følgende versioner af operativsystemet understøttes for udvikling:

- Windows 7
- Windows 8/Windows 8.1
- Windows Server 2012 R2
- Windows 10

>[AZURE.NOTE] Windows 7 indeholder kun Windows PowerShell 2.0 som standard. Tjenesten strukturen PowerShell-cmdlet'er kræver PowerShell 3.0 eller nyere. Du kan [hente Windows PowerShell 5.0] [ powershell5-download] fra Microsoft Download Center.

## <a name="install-the-runtime-sdk-and-tools"></a>Installere runtime, SDK og værktøjer

Web Platform Installer indeholder to konfigurationer for tjenesten strukturen udvikling:

- [Installere Service-strukturen runtime, SDK og værktøjer til Visual Studio-2015 (kræver Visual Studio 2015 opdatering 2 eller nyere)][full-bundle-vs2015]
- [Installere Service-strukturen runtime og SDK kun (ingen Visual Studio funktioner)][core-sdk]

## <a name="enable-powershell-script-execution"></a>Aktivere udførelse af PowerShell-script

Tjenesten strukturen bruger Windows PowerShell-scripts til oprettelse af en lokal udvikling klynge og installerer programmer fra Visual Studio. Som standard blokerer Windows disse scripts i at køre. Hvis du vil aktivere dem, skal du ændre din PowerShell udførelse af politik. Åbne PowerShell som administrator, og Skriv følgende kommando:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="next-steps"></a>Næste trin
Nu hvor du er færdig med at konfigurere dit udviklingsmiljø, start opbygning og køre apps.

- [Opret dit første Service-strukturen-program i Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
- [Lær, hvordan du installerer og Administrer programmer på din lokale klynge](service-fabric-get-started-with-a-local-cluster.md)
- [Få mere at vide om programmering modellerne: pålidelig tjenester og pålidelig aktører](service-fabric-choose-framework.md)
- [Se kodeeksempler Service-strukturen på GitHub](https://aka.ms/servicefabricsamples)
- [Visualisere din klynge ved hjælp af tjenesten strukturen Stifinder](service-fabric-visualizing-your-cluster.md)
- [Følg Service-strukturen læringssti for at få en introduktion til platformen](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)

[1]: http://azure.microsoft.com/en-us/campaigns/service-fabric/ "Tjenesten strukturen kampagnens side"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "ELLER-RC"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "Eller-2015 WebPI link"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI link"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI link"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395
