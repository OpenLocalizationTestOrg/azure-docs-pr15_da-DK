<properties
    pageTitle="Aktivere ekstern fejlfinding med kontinuert levering | Microsoft Azure"
    description="Lær, hvordan du aktiverer remote fejlfinding, når du bruger kontinuert levering skal installeres til Azure"
    services="cloud-services"
    documentationCenter=".net"
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="enable-remote-debugging-when-using-continuous-delivery-to-publish-to-azure"></a>Aktivere ekstern fejlfinding, når du bruger kontinuert levering publicere til Azure

Du kan aktivere ekstern fejlfinding i Azure, til skytjenester eller virtuelle maskiner, når du bruger [fortløbende levering](cloud-services-dotnet-continuous-delivery.md) publicere til Azure ved at følge disse trin.

## <a name="enabling-remote-debugging-for-cloud-services"></a>Aktivere ekstern fejlfinding til skytjenester

1. På agent build konfigurere det indledende miljø til Azure som beskrevet i [Oprette kommandolinjeparametre til Azure](http://msdn.microsoft.com/library/hh535755.aspx).
2. Da remote fejlfinding runtime (msvsmon.exe) er påkrævet til pakken, skal du installere [Remote Tools til Visual Studio 2015](http://www.microsoft.com/en-us/download/details.aspx?id=48155) (eller [Remote værktøjer til Microsoft Visual Studio 2013 Update 5](https://www.microsoft.com/en-us/download/details.aspx?id=48156) Hvis du bruger Visual Studio 2013). Du kan kopiere de remote fejlfinding binære filer fra et system, der indeholder Visual Studio installeret som et alternativ.
3. Oprette et certifikat, som beskrevet i [Oversigt over certifikater til Azure Cloud Services](cloud-services-certs-create.md). Holde .pfx og RDP certifikat miniature, og Overfør certifikatet til destinationen skybaseret tjeneste.
4. Brug følgende indstillinger i kommandolinjen MSBuild til at opbygge og pakke med remote fejlfinding aktiveret. (Erstatte faktiske stier til dit system og project filer for de elementer, vinkel kantede parenteser).

        msbuild /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="<remote tools path>";RemoteDebuggerConnectorCertificateThumbprint="<thumbprint of the certificate added to the cloud service>";RemoteDebuggerConnectorVersion="2.7" "<path to your VS solution file>"

    `VSX64RemoteDebuggerPath`er stien til mappen, der indeholder msvsmon.exe i Remote Tools til Visual Studio.
    `RemoteDebuggerConnectorVersion`er Azure SDK versionen i skybaseret tjeneste. Det skal også svare til den version, der er installeret med Visual Studio.

5. Udgive til tjenesten target skyen ved hjælp af pakke og .cscfg filen, der er oprettet i ovenstående trin.
6. Importere certifikat (.pfx-fil) på den computer, der indeholder Visual Studio med Azure SDK til .NET er installeret. Sørg for at importere til den `CurrentUser\My` lager, ellers vedhæfte til fejlfinding i Visual Studio mislykkes.

## <a name="enabling-remote-debugging-for-virtual-machines"></a>Aktivere ekstern fejlfinding for virtuelle maskiner

1. Oprette en Azure virtuelt. Se [oprette en virtuel maskine, der kører Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md) eller [oprette og administrere Azure virtuelle maskiner i Visual Studio](../virtual-machines/virtual-machines-windows-classic-manage-visual-studio.md).
2. Få vist den virtuelle maskine dashboard for at få vist den virtuelle maskine **RDP certifikat MINIATURE** [Azure klassisk portal-side](http://go.microsoft.com/fwlink/p/?LinkID=269851). Denne værdi bruges til den `ServerThumbprint` værdi i konfigurationen af filtypenavn.
3. Oprette et klientcertifikat, som beskrevet i [Oversigt over certifikater til Azure Cloud Services](cloud-services-certs-create.md) (lade .pfx og RDP certifikat miniature).
4. Installere Azure Powershell (version 0.7.4 eller nyere) som beskrevet i [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).
5. Kør følgende script for at aktivere filtypenavnet RemoteDebug. Erstat stier og personlige oplysninger med dine egne, som din Abonnementsnavnet, navnet på tjenesten og miniature.

    >[AZURE.NOTE] Dette script er konfigureret til Visual Studio-2015. Hvis du bruger Visual Studio 2013, skal du ændre den `$referenceName` og `$extensionName` tildelinger nedenfor for at bruge `RemoteDebugVS2013` (i stedet for `RemoteDebugVS2015`).

    <pre>
   Tilføje AzureAccount

    Vælg-AzureSubscription "Min Microsoft abonnement"

    $vm = get-AzureVM - ServiceName "mytestvm1"-navn "mytestvm1"

    $endpoints = @( ,@{Name="RDConnVS2013"; PublicPort = 30400; PrivatePort = 30398} ,@{Name="RDFwdrVS2013"; PublicPort = 31400; PrivatePort = 31398})  

    foreach ($endpoint i $endpoints) {Tilføj AzureEndpoint - VM $vm-navngive $endpoint. Name - protokollen tcp - PublicPort $endpoint. PublicPort – Lokalport $endpoint. PrivatePort}

    $referenceName = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2015" $publisher = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug" $extensionName = "RemoteDebugVS2015" $version = "1.*" $publicConfiguration = "<PublicConfig>< Connector.Enabled > SAND < /Connector.Enabled ><ClientThumbprint>56D7D1B25B472268E332F7FC0C87286458BFB6B2</ClientThumbprint><ServerThumbprint>E7DCB00CB916C468CC3228261D6E4EE45C8ED3C6</ServerThumbprint><ConnectorPort>30398</ConnectorPort><ForwarderPort>31398</ForwarderPort></PublicConfig>"

    $vm | Angiv AzureVMExtension `
    -ReferenceName $referenceName ` 
    – Publisher $publisher `
    -ExtensionName $extensionName ` 
    – Version $version ' - PublicConfiguration $publicConfiguration

    foreach ($extension i $vm. VM. ResourceExtensionReferences) {Hvis (($extension. Referencenavn – eq $referenceName) `
    -and ($extension.Publisher -eq $publisher) ` 
    – og ($extension. Navngive - eq $extensionName) '- og ($extension. Version - eq $version)) {$extension. ResourceExtensionParameterValues [0]. Nøgle = 'config.txt' sideskift}}

    $vm | Opdater AzureVM </pre>

6. Importere certifikat (.pfx) til den computer, der har Visual Studio med Azure SDK til .NET er installeret.
