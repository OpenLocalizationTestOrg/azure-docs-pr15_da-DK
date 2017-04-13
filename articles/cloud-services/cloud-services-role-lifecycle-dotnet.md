<properties 
pageTitle="Håndtere skybaseret tjeneste livscyklus hændelser | Microsoft Azure" 
description="Få mere at vide, hvordan livscyklus metoder til en skybaseret tjeneste rolle kan bruges i .NET" 
services="cloud-services" 
documentationCenter=".net" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="09/06/2016" 
ms.author="adegeo"/>

# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Tilpasse en Web eller arbejder rolle i .NET livscyklus

Når du opretter en kollega rolle, kan du udvide klassen [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) , som indeholder metoder for dig at tilsidesætte, som du kan besvare livscyklus begivenheder. For web roller er klassen valgfri, så du skal bruge den til at besvare livscyklus begivenheder.

## <a name="extend-the-roleentrypoint-class"></a>Udvide klassen RoleEntryPoint

Klassen [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) indeholder metoder, som kaldes ved Azure når den **starter**, **kører**eller **holder op med** en web eller arbejder rolle. Du kan eventuelt tilsidesætte disse metoder til at administrere rolle initialisering, rolle lukning serier eller udførelse af tråd af rollen. 

Når du udvider **RoleEntryPoint**, skal du være opmærksom på følgende funktionsmåder af metoder:

-   Metoderne [OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) og [OnStop](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx) returnerer en boolesk værdi, så det er muligt at returnere **Sand** fra disse metoder.

     Hvis din kode returnerer **Falsk**, afsluttes rolle processen pludselig, uden at køre en hvilken som helst lukning, du skal muligvis på plads. Generelt, skal du undgå at returnere **Falsk** fra metoden **OnStart** .
     
-   Alle ikke-opfanget undtagelse i en overbelastning af en **RoleEntryPoint** metode behandles som en ikke-afviklet undtagelse.

     Hvis der opstår en undtagelse i en af livscyklus metoder, Azure vil hæve [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) begivenheden, og derefter processen afsluttes. Når din rolle er blevet taget offline, genstartes ved Azure. Når der opstår en ikke-afviklet undtagelse, hændelsen [stoppe](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx) øges ikke, og metoden **OnStop** kaldes ikke.

Hvis din rolle starter ikke, eller er genbrug mellem initialisering, optaget, og stoppe stater, kan din kode skal udløses en ikke-afviklet undtagelse i en af livscyklus hændelser, hver gang rollen genstarter. I dette tilfælde skal bruge hændelsen [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) årsagen til undtagelsen og håndtere den korrekt. Din rolle kan også returnere fra metoden [køre](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) , som medfører, at rollen at genstarte. Du kan finde flere oplysninger om installation stater, [Almindelige problemer som årsag roller til Papirkurv](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [AZURE.NOTE] Hvis du bruger **Azure værktøjer til Microsoft Visual Studio** til at udvikle dit program, udvider projektskabeloner rolle automatisk klassen **RoleEntryPoint** for dig, i dialogboksen *WebRole.cs* og *WorkerRole.cs* filer.

## <a name="onstart-method"></a>OnStart metode

Metoden **OnStart** kaldes, når din rolle forekomst er online ved Azure. Mens OnStart koden udføres, forekomsten rolle er markeret som **optaget** og ingen ekstern trafik, blive omdirigeret til den ved justering af belastning. Du kan tilsidesætte denne metode til at udføre initialisering arbejdet, som implementere hændelseshandlere og starter [Azure diagnosticering](cloud-services-how-to-monitor.md).

Hvis **OnStart** returnerer **Sand**, forekomsten er initialiseret og Azure kalder metoden **RoleEntryPoint.Run** . Hvis **OnStart** returnerer **Falsk**, afbryder rollen med det samme, uden at udføre en hvilken som helst planlagte lukning serier.

Følgende kodeeksempel viser, hvordan at tilsidesætte metoden **OnStart** . Denne metode konfigurerer og starter en diagnosticering skærm, når den rolle forekomst starter og konfigurerer en overførsel af logføring data til en lagerplads konto:

```csharp
public override bool OnStart()
{
    var config = DiagnosticMonitor.GetDefaultInitialConfiguration();

    config.DiagnosticInfrastructureLogs.ScheduledTransferLogLevelFilter = LogLevel.Error;
    config.DiagnosticInfrastructureLogs.ScheduledTransferPeriod = TimeSpan.FromMinutes(5);

    DiagnosticMonitor.Start("DiagnosticsConnectionString", config);

    return true;
}
```

## <a name="onstop-method"></a>OnStop metode

Metoden **OnStop** kaldes, når en forekomst af rolle er taget offline ved Azure og før processen afsluttes. Du kan tilsidesætte denne metode til at ringe til kode, der kræves til din rolle forekomst til lukket korrekt.

> [AZURE.IMPORTANT] Kode, der kører i metoden **OnStop** har en begrænset tid til at udføre, når den kaldes årsager end en brugerinitieret lukning. Når denne tid der går, processen afsluttes, så skal du sikre dig, kode i feltet **OnStop** metode kan køre hurtigt eller tolererer ikke kører til afslutning. Metoden **OnStop** kaldes, efter hændelsen **stoppe** opløftes.


## <a name="run-method"></a>Køre metode

Du kan tilsidesætte metoden **køre** for at implementere en længerevarende tråd for din rolle forekomst.

Tilsidesætte metoden **køre** er ikke påkrævet. standardimplementeringen starter en tråd, der er i slumrer uendelig. Hvis du tilsidesætte metoden **køre** , skal din kode blokere på ubestemt tid. Hvis metoden **køre** returnerer, er rollen automatisk problemfrit genbruges; med andre ord Azure hæver hændelsen **stoppe** og kalder metoden **OnStop** , så din lukning serier kan blive udført, før rollen er gjort tilgængelig offline.


### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Implementere metoderne ASP.NET livscyklus for en web rolle

Du kan bruge ASP.NET livscyklus metoder, ud over dem, der leveres af klassen **RoleEntryPoint** til at administrere initialisering og lukning serier for en web rolle. Det kan være anvendeligt til kompatibilitet, hvis du konvertere et eksisterende ASP.NET-program til Azure. ASP.NET livscyklus metoder kaldes fra i **RoleEntryPoint** metoderne. Den **programmet\_starte** metode kaldes, når metoden **RoleEntryPoint.OnStart** afsluttes. Den **programmet\_slutningen** metode kaldes, før metoden **RoleEntryPoint.OnStop** kaldes.

## <a name="next-steps"></a>Næste trin
Lær, hvordan du [opretter en skybaseret tjeneste pakke](cloud-services-model-and-package.md).