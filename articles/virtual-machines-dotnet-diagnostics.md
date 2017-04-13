<properties
    pageTitle="Sådan bruges Azure diagnosticering i virtuelle maskiner | Microsoft Azure"
    description="Brug af Azure diagnosticering til at indsamle data fra Azure virtuelle maskiner til fejlfinding, måle ydeevne, overvågning, analyse og mere."
    services="virtual-machines"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="virtual-machines"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="02/20/2016"
    ms.author="robb"/>



# <a name="enabling-diagnostics-in-azure-virtual-machines"></a>Aktivere diagnosticering på Azure virtuelle computere

Se [Oversigt over Azure diagnosticering](azure-diagnostics.md) som baggrund på Azure diagnosticering.

## <a name="how-to-enable-diagnostics-in-a-virtual-machine"></a>Sådan aktiveres diagnosticering i en virtuel maskine

Denne gennemgang beskriver, hvordan du installerer fra en fjernplacering diagnosticering til en Azure virtuelt fra en computer, udvikling. Også lærer du at implementere et program, der kører på den Azure virtuelt og udsender telemetridata ved hjælp af .NET [EventSource klasse][]. Azure diagnosticering bruges til at indsamle telemetri og gemme den på en Azure-lager-konto.

### <a name="pre-requisites"></a>Forudsætninger
Denne gennemgang antages, du har et abonnement til Azure og bruger Visual Studio 2013 med Azure SDK. Hvis du ikke har et Azure-abonnement, kan du tilmelde dig [Gratis prøveversion][]. Sørg for at [installere og konfigurere Azure PowerShell version 0.8.7 eller nyere][].

### <a name="step-1-create-a-virtual-machine"></a>Trin 1: Oprette en virtuel maskine
1.  Start Visual Studio 2013 på computeren udvikling.
2.  I Visual Studio **Server Explorer** udvide **Azure**, skal du højreklikke på **virtuelle maskiner** og vælg derefter **Opret virtuelt**.
3.  Vælg abonnementet Azure i dialogboksen **Vælg et abonnement** , og klik på **Næste**.
4.  Vælg **Windows Server 2012 R2 Datacenter, November 2014** i dialogboksen **Vælg et virtuelt billede** , og klik på **Næste**.
5.  Angiv det virtuelle computernavn til "wadexample" i **Virtuelt grundlæggende indstillinger**. Angiv dit administratorbrugernavn og din adgangskode, og klik på **Næste**.
6.  Oprette en ny skybaseret tjeneste med navnet "wadexampleVM" i dialogboksen **Skyen Tjenesteindstillinger** . Oprette en ny lagerplads konto med navnet "wadexample", og klik på **Næste**.
7.  Klik på **Opret**.

### <a name="step-2-create-your-application"></a>Trin 2: Oprette dit program
1.  Start Visual Studio 2013 på computeren udvikling.
2.  Oprette en ny visuel C# Console program, der er beregnet til .NET Framework 4.5. Navngive projektet "WadExampleVM".
    ![CloudServices_diag_new_project](./media/virtual-machines-dotnet-diagnostics/NewProject.png)
3.  Erstat indholdet af Program.cs med følgende kode. Klassen **SampleEventSourceWriter** implementerer fire logføring metoder: **SendEnums**, **MessageMethod**, **SetOther** og **HighFreq**. Den første parameter til metoden WriteEvent definerer ID for den pågældende begivenhed. Metoden Run implementerer en uendelig løkke, der ringer op til hver af de logføring metoder, der er implementeret i klassen **SampleEventSourceWriter** hvert 10.

        using System;
        using System.Diagnostics;
        using System.Diagnostics.Tracing;
        using System.Threading;

        namespace WadExampleVM
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

        class Program
        {
        static void Main(string[] args)
        {
            Trace.TraceInformation("My application entry point called");

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
        }
        }


4.  Gem filen, og vælg **Opbygge løsning** fra menuen **Build** til at opbygge din kode.


### <a name="step-3-deploy-your-application"></a>Trin 3: Installere dit program
1.  Højreklik på **WadExampleVM** projektet i **Solution Explorer** , og vælg **Åbn mappe i Stifinder**.
2.  Gå til mappen *bin\Debug* og kopiere alle filer (WadExampleVM.*)
3.  Højreklik på den virtuelle maskine, og vælg **Opret forbindelse ved hjælp af Fjernskrivebord**i **Server Explorer** .
4.  Oprette en mappe med navnet WadExampleVM og indsætte dit program filer til mappen, når forbindelse til VM.
5.  Start app'en WadExampleVM.exe. Der vises et tomt console-vindue.

### <a name="step-4-create-your-diagnostics-configuration-and-install-the-extension"></a>Trin 4: Oprette konfigurationen af diagnosticering og installere filtypenavnet
1.  Hente offentlige konfiguration fil skemadefinitionen til computeren udvikling ved at udføre følgende PowerShell-kommando:

        (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'

2.  Åbn en ny XML-fil i Visual Studio, enten i et projekt, som du allerede har åbnet eller i en Visual Studio forekomst med nogen åbne projekter. I Visual Studio, skal du vælge **Tilføj** -> **Nyt element...**  ->  **Visual C# elementer** -> **Data** -> **XML-fil**. Navngiv filen "WadExample.xml"
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

### <a name="step-5-remotely-install-diagnostics-on-your-azure-virtual-machine"></a>Trin 5: Installere fra en fjernplacering diagnosticering på din Azure virtuelle maskine
PowerShell-cmdletter til administration af diagnosticering på en VM er: Set-AzureVMDiagnosticsExtension, Get-AzureVMDiagnosticsExtension og fjern AzureVMDiagnosticsExtension.

1.  Åbn Azure PowerShell på computeren udvikler.
2.  Udføre scriptet for at installere fra en fjernplacering diagnosticering på din VM (Erstat *StorageAccountKey* med kontonøgle lagerplads for kontoen wadexamplevm lagerplads):

        $storage_name = "wadexamplevm"
        $key = "<StorageAccountKey>"
        $config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExampleVM\WadExampleVM\WadExample.xml"
        $service_name="wadexamplevm"
        $vm_name="WadExample"
        $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
        $VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
        $VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
        $VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM


### <a name="step-6-look-at-your-telemetry-data"></a>Trin 6: Kig på din telemetridata
Naviger til kontoen wadexample lagerplads i Visual Studio **Server Explorer** . Efter VM har kørt omkring 5 minutter skal du se tabellerne, **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** og **WADSetOtherTable**. Dobbeltklik på en af tabellerne for at få vist den telemetri, der indsamles.

![CloudServices_diag_wadexamplevm_tables](./media/virtual-machines-dotnet-diagnostics/WadExampleVMTables.png)

## <a name="configuration-file-schema"></a>Konfiguration af fil skema

Konfigurationsfil diagnosticering definerer værdier, der bruges til at initialiseret diagnosticering konfigurationsindstillinger, når diagnosticering agent starter. Se de [seneste skemareference](https://msdn.microsoft.com/library/azure/mt634524.aspx) til gyldige værdier og eksempler.

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

Du kan få flere oplysninger i [Foretage fejlfinding af Azure diagnosticering](azure-diagnostics-troubleshooting.md) .


## <a name="next-steps"></a>Næste trin
[Se en liste over virtuelt relateret Azure diagnosticering artikler](azure-diagnostics.md#virtual-machines-using-azure-diagnostics) til at ændre de data, du indsamler, foretage fejlfinding af problemer eller få mere at vide om diagnosticering Generelt.


[EventSource klasse]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Gratis prøveversion]: http://azure.microsoft.com/pricing/free-trial/
[Installere og konfigurere Azure PowerShell version 0.8.7 eller nyere]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
