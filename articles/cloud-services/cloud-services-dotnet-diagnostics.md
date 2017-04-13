<properties
    pageTitle="Sådan bruges Azure diagnosticering (.NET) med Cloud Services | Microsoft Azure"
    description="Bruge Azure diagnosticering til at indsamle data fra Azure cloud Services for fejlfinding, måle ydeevne, overvågning, analyse og mere."
    services="cloud-services"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="01/25/2016"
    ms.author="robb"/>



# <a name="enabling-azure-diagnostics-in-azure-cloud-services"></a>Aktivere Azure diagnosticering i Azure Cloud Services

Se [Oversigt over Azure diagnosticering](../azure-diagnostics.md) som baggrund på Azure diagnosticering.


## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>Sådan aktiveres diagnosticering i en kollega rolle

Denne gennemgang beskriver, hvordan at implementere en Azure arbejder rolle, der udsender telemetridata ved hjælp af .NET EventSource klassen. Azure diagnosticering bruges til at indsamle dataene, telemetri og gemme den på en Azure-lager-konto. Når du opretter en kollega rolle aktiverer Visual Studio automatisk diagnosticering 1.0 som en del af løsningen i Azure SDK'er til .NET 2.4 og tidligere. Følgende fremgangsmåde beskriver processen til oprettelse af rollen arbejder, deaktivere diagnosticering 1.0 fra løsningen, og implementering diagnosticering 1.2 eller 1.3 til din kollega rolle.

### <a name="pre-requisites"></a>Forudsætninger
I denne artiklen antages, du har et abonnement til Azure og bruger Visual Studio 2013 med Azure SDK. Hvis du ikke har et Azure-abonnement, kan du tilmelde dig [Gratis prøveversion][]. Sørg for at [installere og konfigurere Azure PowerShell version 0.8.7 eller nyere][].

### <a name="step-1-create-a-worker-role"></a>Trin 1: Oprette en kollega rolle
1.  Start **Visual Studio-2013**.
2.  Oprette et projekt med **Azure skybaseret tjeneste** fra skabelonen **skyen** pågældende destinationer .NET Framework 4.5.  Navngiv projektet "WadExample", og klik på Ok.
3.  Vælg **Arbejder rolle** , og klik på Ok. Projektet oprettes.
4.  I **Solution Explorer**skal du dobbeltklikke på filen **WorkerRole1** egenskaber.
5.  Fanen Fjern markeringen af **Aktivér diagnosticering** for at deaktivere diagnosticering 1.0 (Azure SDK 2.4 og eariler) i sektionen **konfiguration** .
6.  Opbygge din løsning for at bekræfte, at du har ingen fejl.

### <a name="step-2-instrument-your-code"></a>Trin 2: Instrumentere din kode
Erstat indholdet af WorkerRole.cs med følgende kode. Klassen SampleEventSourceWriter, nedarvet fra [EventSource klasse][]implementerer fire logføring metoder: **SendEnums**, **MessageMethod**, **SetOther** og **HighFreq**. Den første parameter til metoden **WriteEvent** definerer ID for den pågældende begivenhed. Metoden Run implementerer en uendelig løkke, der ringer op til hver af de logføring metoder, der er implementeret i klassen **SampleEventSourceWriter** hvert 10.

    using Microsoft.WindowsAzure.ServiceRuntime;
    using System;
    using System.Diagnostics;
    using System.Diagnostics.Tracing;
    using System.Net;
    using System.Threading;

    namespace WorkerRole1
    {
    sealed class SampleEventSourceWriter : EventSource
    {
        public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    }

    enum MyColor
    {
        Red,
        Blue,
        Green
    }

    [Flags]
    enum MyFlags
    {
        Flag1 = 1,
        Flag2 = 2,
        Flag3 = 4
    }

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }
        }

        public override bool OnStart()
        {
            // Set the maximum number of concurrent connections
            ServicePointManager.DefaultConnectionLimit = 12;

            // For information on handling configuration changes
            // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
    }


### <a name="step-3-deploy-your-worker-role"></a>Trin 3: Installere din kollega rolle
1.  Installer din kollega rolle til Azure fra i Visual Studio ved at vælge **WadExample** projektet i Solution Explorer derefter **Publicer** på menuen **Build** .
2.  Vælg dit abonnement.
3.  Vælg **Opret ny …**i dialogboksen **Microsoft Azure publiceringsindstillinger** .
4.  Angiv et **navn** (eksempelvis "WadExample") i dialogboksen **Opret skybaseret tjeneste og lagerplads konto** , og vælg et område eller en forbindelse gruppe.
5.  Angiv **miljø** til **midlertidige**.
6.  Ændre andre **Indstillinger** efter behov, og klik på **Publicer**.
7.  Når installationen er færdig, Bekræft i portalen Azure klassisk, skybaseret tjeneste er i tilstanden **kører** .

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>Trin 4: Oprette din konfigurationsfil til diagnosticering og installere filtypenavnet
1.  Hente offentlige konfiguration fil skemadefinitionen ved at udføre følgende PowerShell-kommando:
2.
        (Get-AzureServiceAvailableExtension - ExtensionName 'PaaSDiagnostics' - ProviderNamespace 'Microsoft.Azure.Diagnostics'). PublicConfigurationSchema | Tilknytte fil-kodning utf8 - FilePath 'WadConfig.xsd'

2.  Føje en XML-fil til projektet **WorkerRole1** ved at højreklikke på **WorkerRole1** projektet og vælge **Tilføj** -> **Nyt element...**  ->  **Visual C# elementer** -> **Data** -> **XML-fil**. Navngiv filen "WadExample.xml".

    ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)

3.  Knytte WadConfig.xsd med konfigurationsfilen. Kontrollér, at vinduet WadExample.xml editor er det aktive vindue. Tryk på **F4** for at åbne vinduet **Egenskaber** . Klik på egenskaben **ved hjælp af skemaer** i vinduet **Egenskaber** . Klik på **...** i egenskaben **ved hjælp af skemaer** . Klik på **Tilføj...** knappen, og gå til det sted, hvor du gemte XSD-filen, og vælg filen WadConfig.xsd. Klik på **OK**.
4.  Erstatte indholdet af konfigurationsfil WadExample.xml med følgende XML, og Gem filen. Denne konfigurationsfil definerer et par tællere i ydeevne til at indsamle: én for CPU-forbrug og én til hukommelsesbrug. Derefter definerer konfigurationen de fire hændelser, der svarer til metoderne i klassen SampleEventSourceWriter.

```
        <?xml version="1.0" encoding="utf-8"?>
        <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
            <WadCfg>
                <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
                <PerformanceCounters scheduledTransferPeriod="PT1M">
                    <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
                    <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
                    </PerformanceCounters>
                    <EtwProviders>
                        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
                            <Event id="1" eventDestination="EnumsTable"/>
                            <Event id="2" eventDestination="MessageTable"/>
                            <Event id="3" eventDestination="SetOtherTable"/>
                            <Event id="4" eventDestination="HighFreqTable"/>
                            <DefaultEvents eventDestination="DefaultTable" />
                        </EtwEventSourceProviderConfiguration>
                    </EtwProviders>
                </DiagnosticMonitorConfiguration>
            </WadCfg>
        </PublicConfig>
```

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>Trin 5: Installere diagnosticering på din kollega rolle
PowerShell-cmdletter til administration af diagnosticering på en web eller arbejder rolle er: Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension og fjern AzureServiceDiagnosticsExtension.

1.  Åbn Azure PowerShell.
2.  Udføre scriptet for at installere diagnosticering på din kollega rolle (erstatte *StorageAccountKey* med kontonøgle lagerplads for kontoen wadexample lagerplads):

```
    $storage_name = "wadexample"
    $key = "<StorageAccountKey>"
    $config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
    $service_name="wadexample"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>Trin 6: Kig på din telemetridata
Naviger til kontoen wadexample lagerplads i Visual Studio **Server Explorer** . Efter skyen har tjenesten kørt omkring 5 minutter, skal du se afsnittet tabellerne **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** og **WADSetOtherTable**. Dobbeltklik på en af tabellerne for at få vist den telemetri, der indsamles.
    ![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)


## <a name="configuration-file-schema"></a>Konfiguration af fil skema

Konfigurationsfil diagnosticering definerer værdier, der bruges til at initialiseret diagnosticering konfigurationsindstillinger, når diagnosticering agent starter. Se de [seneste skemareference](https://msdn.microsoft.com/library/azure/mt634524.aspx) til gyldige værdier og eksempler.

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

Hvis du har problemer, se [Fejlfinding i forbindelse med Azure diagnosticering](../azure-diagnostics-troubleshooting.md) hjælp til almindelige problemer.

## <a name="next-steps"></a>Næste trin
[Se en liste over virtuelt relateret Azure diagnosticering artikler](azure-diagnostics.md#cloud-services) til at ændre de data, du indsamler, foretage fejlfinding af problemer eller få mere at vide om diagnosticering Generelt.


[EventSource klasse]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Gratis prøveversion]: http://azure.microsoft.com/pricing/free-trial/
[Installere og konfigurere Azure PowerShell version 0.8.7 eller nyere]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
