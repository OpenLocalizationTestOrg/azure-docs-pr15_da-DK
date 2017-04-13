<properties
    pageTitle="Spore strømmen i et Cloud Services-program med Azure diagnosticering | Microsoft Azure"
    description="Føje sporing af meddelelser til Azure programmet til hjælp til fejlfinding, måle ydeevne, overvågning, analyse og mere."
    services="cloud-services"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="02/20/2016"
    ms.author="robb"/>



# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Spore strømmen af et Cloud Services-program med Azure diagnosticering

Sporing af er en metode til at overvåge udførelse af dit program, mens den kører. Du kan bruge [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx), [System.Diagnostics.Debug](https://msdn.microsoft.com/library/system.diagnostics.debug.aspx)og [System.Diagnostics.TraceSource](https://msdn.microsoft.com/library/system.diagnostics.tracesource.aspx) klasser til at registrere oplysninger om fejl og udførelse af programmet i logfiler, tekstfiler eller andre enheder til efterfølgende analyse. Du kan finde flere oplysninger om sporing Se [sporing og instrumentering programmer](https://msdn.microsoft.com/library/zs6s4h68.aspx).


## <a name="use-trace-statements-and-trace-switches"></a>Brug sporing sætninger og sporing parametre

Implementere sporing i dit Cloud Services-program ved at tilføje [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) af programmet konfigurationen og foretage opkald til System.Diagnostics.Trace eller System.Diagnostics.Debug i din programkode. Brug konfiguration fil *app.config* for arbejder roller og *web.config* for web roller. Når du opretter en ny hostet tjeneste ved hjælp af Visual Studio-skabelon, Azure diagnosticering føjes automatisk til projektet, og DiagnosticMonitorTraceListener føjes til den relevante konfigurationsfil for de roller, du tilføjer.

Finde oplysninger om indsættelse af sporing sætninger [Sådan: tilføje sporing sætninger til programkode](https://msdn.microsoft.com/library/zd83saa2.aspx).

Ved at placere [Sporing parametre](https://msdn.microsoft.com/library/3at424ac.aspx) i din kode, kan du styre, om sporing forekommer, og hvordan omfattende, det er. Her kan du overvåge status for dit program i et produktionsmiljø. Dette er især vigtigt i et forretningsprogram, der bruger flere komponenter, der kører på flere computere. Du kan finde flere oplysninger, se [Sådan: konfigurere sporing parametre](https://msdn.microsoft.com/library/t06xyy08.aspx).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Konfigurere lytteren sporing i et Azure-program

Sporing, fejlfinding og TraceSource, skal du konfigurere "lyttere" til at indsamle og registrere de meddelelser, der sendes. Lyttere indsamling, gemme og dirigere sporing af meddelelser. De direkte sporing output til en passende destination, som en log, vinduet eller tekstfil. Azure diagnosticering bruger klassen [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) .

Før du udfører følgende procedure, skal du har initialiseret den Azure diagnosticering skærm. For at gøre dette, under [Aktivere Diagnostics i Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Bemærk, at hvis du bruger de skabeloner, der leveres af Visual Studio, konfigurationen af lytteren føjes automatisk for dig.


### <a name="add-a-trace-listener"></a>Tilføje en sporing lytter

1. Åbn filen web.config eller app.config for din rolle.
2. Tilføj følgende kode til filen. Ændre egenskaben Version for at bruge versionsnummeret for samlingen du refererer til. Samling versionen ændrer ikke nødvendigvis med hver Azure SDK-version, medmindre der er opdateringer til den.

    ```
    <system.diagnostics>
        <trace>
            <listeners>
                <add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
                  Microsoft.WindowsAzure.Diagnostics,
                  Version=2.8.0.0,
                  Culture=neutral,
                  PublicKeyToken=31bf3856ad364e35"
                  name="AzureDiagnostics">
                  <filter type="" />
                </add>
            </listeners>
        </trace>
    </system.diagnostics>
    ```
    >[AZURE.IMPORTANT] Sørg for, at du har en projektreference til samlingen Microsoft.WindowsAzure.Diagnostics. Opdater versionsnummeret i XML-ovenfor for at svare til versionen af den refererede Microsoft.WindowsAzure.Diagnostics samling.

3. Gem filen config.

Du kan finde flere oplysninger om lyttere, [Spor lyttere](https://msdn.microsoft.com/library/4y5y10s7.aspx).

Når du har fuldført trinnene for at tilføje lytteren, kan du tilføje sporing sætninger i din kode.


### <a name="to-add-trace-statement-to-your-code"></a>Tilføje sporing sætning i din kode

1. Åbn en kildefil for dit program. For eksempel på <RoleName>.cs fil, som arbejder rollen eller web rolle.
2. Tilføj følgende ved hjælp af sætningen, hvis den ikke allerede er tilføjet:
    ```
        using System.Diagnostics;
    ```
3. Tilføj spor sætninger, hvor du vil indsamle oplysninger om tilstanden for dit program. Du kan bruge en række forskellige metoder til at formatere output fra sætningen sporing. Kan finde flere oplysninger under [Sådan: tilføje sporing sætninger til programkode](https://msdn.microsoft.com/library/zd83saa2.aspx).
4. Gem filen.
