<properties
   pageTitle="Azure Service-strukturen på Linux | Microsoft Azure"
   description="Tjenesten strukturen klynger understøtter Linux og Java, hvilket betyder, at du vil kunne installere og host Service-strukturen programmer, der er skrevet i Java og C# på Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="SubramaR"/>

# <a name="service-fabric-on-linux"></a>Tjenesten strukturen på Linux

Eksempel på Service-strukturen på Linux gør det muligt at opbygge, installere og administrere meget tilgængelig, meget SVG-programmer på Linux samme måde som i Windows. Tjenesten strukturen-strukturer (pålidelig Services og pålidelig aktører) er tilgængelige i Java på Linux ud over C# (.NET Core).  Du kan også oprette [Gæst eksekverbare services](service-fabric-deploy-existing-app.md) med en hvilken som helst sprog eller framework. Preview understøtter desuden orchestrating Docker beholdere. Docker beholdere kan køre gæst eksekverbare filer eller oprindelige Service strukturerede services, hvor tjenesten strukturen strukturer.

Tjenesten struktur på Linux er svarer til tjenesten strukturen i Windows (med undtagelse af OS specifikke oplysninger og programmering understøttelse af sprog). Dermed, anvender de fleste af vores [eksisterende dokumentation](http://aka.ms/servicefabricdocs) hjælpe dig med at blive fortrolig med teknologien.

> [AZURE.VIDEO service-fabric-linux-preview]

## <a name="supported-operating-systems-and-programming-languages"></a>Understøttede operativsystemer og programmeringssprog

Begrænset preview understøtter oprettelse af én boks udvikling klynger ud over flere maskiner klynger i Azure kører Ubuntu Server 16.04. Preview understøtter pålidelige aktører og pålidelig uden status Services strukturer i Java og C# ud over gæst eksekverbare filer og orchestrating Docker beholdere.  

>[AZURE.NOTE] Pålidelige samlinger understøttes ikke i Linux endnu. Til at skille sig alene klynger ikke understøttes – enten kun én boks og Azure Linux flere maskiner klynger understøttes i eksemplet.

## <a name="supported-tooling"></a>Understøttede værktøj

Preview understøtter interaktion med klynge via Azure CLI. Integration med Eklipse og Yeoman leveres til Java-udviklere med Eklipse understøttes på Linux og OSX. Alle OSX-integration bruger en Linux VM struktur via vagrant. Integration med Yeoman er angivet til at generere programskabeloner for C# udviklere.

## <a name="next-steps"></a>Næste trin


1. Bliv fortrolig med [Pålidelig aktører](service-fabric-reliable-actors-introduction.md) og [Pålidelig Services](service-fabric-reliable-services-introduction.md) programming strukturer.

2. [Forberede din udviklingsmiljø på Linux](service-fabric-get-started-linux.md)

3. [Forberede din udviklingsmiljø på OSX](service-fabric-get-started-mac.md)

4. [Oprette dit første Service-strukturen Java-program på Linux](service-fabric-create-your-first-linux-application-with-java.md)
