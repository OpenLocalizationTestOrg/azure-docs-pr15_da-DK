<properties 
   pageTitle="Fejlfinding Azure Cloud Services | Microsoft Azure"
   description="Fejlfinding Azure Cloud Services"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="debugging-cloud-services"></a>Fejlfinding-skytjenester

Du kan bruge forskellige metoder til fejlfinding af et Azure program ved hjælp af værktøjerne Azure til Microsoft Visual Studio og Azure SDK:

- Du kan foretage fejlfinding af en Azure-program fra Visual Studio når du udvikler, samme måde som alle Visual C# eller Visual Basic-programmer. Du kan finde flere oplysninger, se [fejlfinding af din skytjeneste på din lokale computer](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer).

- Du kan bruge Azure diagnosticering til at logge detaljerede oplysninger fra kode, der kører i roller, uanset om rollerne, der kører i udviklingsmiljøet eller i Azure. Yderligere oplysninger finder du under [indsamling logføring data ved hjælp af Azure diagnosticering](http://go.microsoft.com/fwlink/p/?LinkId=400450).

- Hvis du bruger Visual Studio Enterprise til at skrive roller, der er rettet mod .NET Framework 4 eller .NET Framework 4.5, kan du aktivere IntelliTrace på det tidspunkt, at du installerer en Azure skybaseret tjeneste fra Visual Studio. IntelliTrace indeholder en logfil, som du kan bruge med Visual Studio til at foretage fejlfinding af dit program, som om den kørte i Azure. Du kan finde flere oplysninger, se [fejlfinding af en publiceret skybaseret tjeneste med IntelliTrace og Visual Studio]( http://go.microsoft.com/fwlink/p/?LinkId=623016).

- Du kan aktivere ekstern fejlfinding på din skytjenester på det tidspunkt, når du installerer skytjenesten fra Visual Studio. Hvis du vælger at aktivere ekstern fejlfinding for en installation, er remote fejlfinding services installeret på de virtuelle maskiner, der kører hver rolle forekomst. Disse tjenester, som msvsmon.exe, ikke påvirke ydeevnen eller medfører ekstra omkostninger. Du kan finde flere oplysninger, se [fejlfinding af en skybaseret tjeneste i Azure](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure).



