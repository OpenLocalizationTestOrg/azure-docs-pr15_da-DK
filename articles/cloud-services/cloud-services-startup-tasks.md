<properties 
pageTitle="Køre Start opgaver i Azure Cloud Services | Microsoft Azure" 
description="Start opgaver hjælper med at forberede dit miljø til skyen tjeneste til din app. Dette lærer du, hvordan Start opgaver fungerer, og hvordan du kan gøre dem" 
services="cloud-services" 
documentationCenter="" 
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



# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Sådan konfigureres og køre Start opgaver til en skybaseret tjeneste

Du kan bruge Start opgaver til at udføre handlinger, før en rolle starter. Handlinger, som du vil udføre omfatter installation af en komponent, registrering af COM-komponenter, angive registreringsdatabasenøgler eller starte en længerevarende proces.

>[AZURE.NOTE] Start opgaver kan ikke anvendes til virtuelle maskiner, kun til skyen Service internettet og arbejder roller.

## <a name="how-startup-tasks-work"></a>Hvordan fungerer Start opgaver

Start opgaver er handlinger, der foretages, før dine roller begynder og er defineret i filen [ServiceDefinition.csdef] ved hjælp af [opgaveelement i elementet [Start] ] . Start opgaver er ofte batchfiler, men de kan også være console programmer eller batchfiler, der starter PowerShell-scripts.

Miljøvariabler videregive oplysninger til en Start-opgave, og lokale lager kan bruges til at overføre oplysninger af en Start-opgave. For eksempel en miljøvariablen kan angive stien til et program, du vil installere, og filer kan skrives til det lokale lager, der kan derefter læses senere af dine roller.

Start opgaven kan logge oplysninger og fejl til den mappe, der er angivet af **TEMP** -miljøvariablen. Under Start opgaven, miljøvariablen **TEMP** oversættes til *C:\\ressourcer\\temp\\[guid]. [ rolename]\\RoleTemp* directory, når der benyttes i skyen.

Start opgaver kan også udføres flere gange mellem genstarter. For eksempel Start opgaven køres, hver gang, genbruger rollen, og rolle genbruger indeholder muligvis ikke altid en genstart. Start opgaver, der skal skrives på en måde, som gør det muligt at køre flere gange uden problemer.

Start opgaver skal slutte med et **errorlevel** (eller afslutningskode) på nul til at starte at fuldføre. Hvis en Start opgave slutter med en forskellig fra nul **errorlevel**, starter ikke rollen.


## <a name="role-startup-order"></a>Rolle Start rækkefølge

I følgende afsnit beskrives rolle Start fremgangsmåden i Azure:

1. Forekomsten er markeret som **Start** og modtage ikke trafik.

2. Alle Start-opgaver udføres efter deres **taskType** -attributten.
    - **Enkel** opgaverne udføres synkront én ad gangen.
    - **Baggrunds** - og **forgrundsfarver** opgaverne er startet asynkront, parallel Start opgaven.  
       
    > [AZURE.WARNING] IIS er muligvis ikke fuldt konfigureret under Start opgave trin i startprocessen så rolle-specifikke data ikke muligvis tilgængelig. Start opgaver, der kræver rolle-specifikke data skal bruge [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx).

3. Rolle host processen startes, og der oprettes webstedet i IIS.

4. Metoden [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) kaldes.

5. Forekomsten er markeret som **klar** og trafik dirigeres til forekomsten.

6. Metoden [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) kaldes.


## <a name="example-of-a-startup-task"></a>Eksempel på en opgave til Start

Start opgaver er defineret i filen [ServiceDefinition.csdef] i elementet **opgave** . Attributten **kommandolinjen** angiver navnet på og parametrene for batchfil start eller console kommandoen, attributten **executionContext** angiver rettighedsniveauet på opgaven, Start, og attributten **taskType** angiver, hvordan opgaven skal udføres.

I dette eksempel er en miljøvariablen, **MyVersionNumber**, oprettet for opgaven, start og værdien "**1.0.0.0**".

**ServiceDefinition.csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

I følgende eksempel skriver batchfil **Startup.cmd** linjen "den aktuelle version er 1.0.0.0" til fil, StartupLog.txt i det bibliotek, der er angivet af TEMP-miljøvariablen. Den `EXIT /B 0` linje sikrer, at opgaven Start slutter med et **errorlevel** nul.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [AZURE.NOTE] I Visual Studio, skal egenskaben **Kopier til mappe, Output** til filen Start batchen skal angives til **Kopi altid** for at sikre, at filen Start batchen er installeret korrekt til dit projekt på Azure (**approot\\placering** for Web roller og **approot** for arbejder roller).

## <a name="description-of-task-attributes"></a>Beskrivelse af opgave attributter

Følgende beskriver attributterne for **opgaveelement i filen [ServiceDefinition.csdef] ** :

**kommandolinje** - angiver kommandolinjen for opgaven, start:

- Kommandoen, med valgfrit kommandolinjeparametre, som begynder Start opgaven.
- Dette er ofte filnavnet på en cmd. eller .bat batchfil.
- Opgaven er i forhold til AppRoot\\placering mappe til installationen. Miljøvariabler udvides ikke ved fastlæggelse af stien og af opgaven. Hvis udvidelse er påkrævet, kan du oprette et lille cmd. script, der kalder Start opgaven.
- Kan være et console program eller en batch-fil, der starter en [PowerShell-script](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**executionContext** - angiver rettighedsniveauet for opgaven, Start. Rettighedsniveauet kan være begrænset eller udvidede:

- **begrænset**  
Start opgaven kører med de samme tilladelser som rollen. Når attributten **executionContext** for elementet [Runtime] er også **begrænset**, anvendes brugerrettigheder.

- **udvidede**  
Start opgaven kører med administratorrettigheder. Dette giver mulighed Start opgaver for at installere programmer, foretage ændringer i IIS konfigurationen, skal du udføre ændringer i registreringsdatabasen og andre niveau administratoropgaver, uden at øge rollen selve rettigheder.  

> [AZURE.NOTE] Rettighedsniveauet for en opgave Start behøver ikke at være den samme som rollen sig selv.

**taskType** – angiver, hvordan en Start opgave udføres.

- **enkel**  
Opgaver er udført synkront, en ad gangen, i rækkefølgen, angivet i filen [ServiceDefinition.csdef] . Når en **simpel** Start opgave slutter med et **errorlevel** nul, udføres den næste **simple** Start opgave. Hvis der er ingen flere **simple** Start opgaver skal udføres, startes rollen sig selv.   

    > [AZURE.NOTE] Hvis opgaven **enkel** slutter med en forskellig fra nul **errorlevel**, blokeres forekomsten. Efterfølgende **enkle** Start opgaver og rollen som sig selv kan ikke starte.

    For at sikre, at din batchfil slutter med et **errorlevel** nul, udføre kommandoen `EXIT /B 0` i slutningen af batchen fil processen.

- **baggrund**  
Opgaver er udført asynkront, parallelt med starten af rollen.

- **forgrunden**  
Opgaver er udført asynkront, parallelt med starten af rollen. Vigtige forskellen mellem en **forgrunden** og en **baggrund** opgave er, at en opgave i **forgrunden** forhindrer rollen fra genbrug eller lukker ned, indtil opgaven er afsluttet. **Baggrundsopgaverne** har ikke denne begrænsning.

## <a name="environment-variables"></a>Miljøvariabler

Miljøvariabler er en måde at overføre oplysninger til en Start-opgave. Du kan for eksempel anbringe stien til en blob, der indeholder et program, der skal installeres, eller portnumre, vil bruge din rolle eller indstillinger for at kontrolfunktioner på Start opgaven.

Der findes to typer af miljøvariabler for Start opgaver. statisk miljøvariabler og miljøvariabler, der er baseret på medlemmer af klassen [RoleEnvironment] . Begge er i afsnittet [miljø] i filen [ServiceDefinition.csdef] , og begge Brug [Variable] elementet, sammen med **navnet** attributten.

Statisk miljøvariabler bruger attributten **værdi** af [Variable] elementet. Ovenstående eksempel opretter miljøvariablen **MyVersionNumber** som har en statisk værdi af "**1.0.0.0**". Et andet eksempel kunne være at oprette en **StagingOrProduction** miljøvariablen, kan du manuelt angive til værdierne for "**arrangere**" eller "**fremstilling**" til at udføre forskellige Start handlinger, der er baseret på værdien af miljøvariablen **StagingOrProduction** .

Miljøvariabler baseret på medlemmer af klassen RoleEnvironment skal ikke bruge attributten **værdi** af [Variable] elementet. I stedet [RoleInstanceValue] underordnet element med attributværdien **XPath** bruges til at oprette en miljøvariablen, der er baseret på et bestemt medlem af klassen [RoleEnvironment] . Værdier for attributten **XPath** at få adgang til forskellige [RoleEnvironment] værdier findes [her](cloud-services-role-config-xpath.md).



For eksempel bruge følgende [variabel] og [RoleInstanceValue] elementer for at oprette en miljøvariablen, der er "**Sand**", når forekomsten kører i Beregn emulator og "**false**", når der benyttes i skyen:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
    
            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->
    
            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>
    
        </Environment>
    </Task>
</Startup>
```

## <a name="next-steps"></a>Næste trin
Lær, hvordan du udfører nogle [almindelige Start opgaver](cloud-services-startup-tasks-common.md) med skybaseret tjeneste.

[Pakke](cloud-services-model-and-package.md) skybaseret tjeneste.  


[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Opgave]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Start]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Miljø]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variabel]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx