<properties
   pageTitle="Konfigurere din udviklingsmiljø på Linux | Microsoft Azure"
   description="Installere runtime- og SDK og oprette en lokal udvikling klynge på Linux. Når installationen er fuldført, vil du være klar til at opbygge programmer."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="seanmck"/>

# <a name="prepare-your-development-environment-on-linux"></a>Forberede din udviklingsmiljø på Linux


> [AZURE.SELECTOR]
-[Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [ALLE OSX](service-fabric-get-started-mac.md)

 For at installere og køre [Azure Service strukturen programmer](service-fabric-application-model.md) på computeren Linux udvikling, skal du installere de runtime og almindelige SDK. Du kan også installere valgfri SDK'er til Java og .NET Core.

## <a name="prerequisites"></a>Forudsætninger
### <a name="supported-operating-system-versions"></a>Understøttet operativsystemversioner
Følgende versioner af operativsystemet understøttes for udvikling:

- Ubuntu 16.04 (Xenial Xerus)

## <a name="update-your-apt-sources"></a>Opdatere dine Hovedgaden kilder

Hvis du vil installere SDK og den tilknyttede runtime-pakken via Hovedgaden Hent, skal du først opdatere dine Hovedgaden kilder.

1. Åbn en terminal.
2. Føje Service-strukturen repo til listen over datakilder.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Føje den nye GPG nøgle til din Hovedgaden nøglering.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    ```

4. Opdatere din pakke lister, der er baseret på de tilføjede lagre.

    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-sdk"></a>Installere og konfigurere SDK

Når dine kilder er opdateret, kan du installere SDK.

1. Installere Service-strukturen SDK-pakken. Du bliver bedt om at bekræfte installationen og for at acceptere en licensaftale.

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```

2. Kør scriptet SDK konfiguration.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

## <a name="set-up-the-azure-cross-platform-cli"></a>Konfigurere den Azure på tværs af platforme CLI

[Azure på tværs af platforme CLI] [ azure-xplat-cli-github] indeholder kommandoer til arbejde med tjenesten strukturen enheder, herunder klynger og programmer. Det er baseret på Node.js så [Sørg for, at du har installeret Node] [ install-node] inden du fortsætter med vejledningen nedenfor.

1. Klone github repo til din computer og udvikling.

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```

2. Skifte til den klonede repo og installere den CLI afhængigheder ved hjælp af Node pakke Manager (npm).

    ```bash
    cd azure-xplat-cli
    npm install
    ```

3. Oprette en symlink fra mappen placering/azure for klonede repo til /usr/bin/azure, så det er føjet til din sti og kommandoer er tilgængelige fra en anden mappe.

    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```

4. Endelig giver mulighed for automatisk fuldførelse Service-strukturen kommandoer.

    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

## <a name="set-up-a-local-cluster"></a>Konfigurere en lokal klynge

Hvis alt er installeret, skal du kunne starte en lokal klynge.

1. Kør scriptet klynge konfiguration.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

2. Åbn en webbrowser og gå til http://localhost:19080/Explorer. Hvis klyngen er startet, bør du undersøge Service-strukturen Explorer dashboard.

    ![Tjenesten strukturen Explorer på Linux][sfx-linux]

Du kan på dette tidspunkt til at implementere færdigbyggede Service-strukturen-programpakker eller nye baseret på gæst objektbeholdere eller gæst eksekverbare filer. For at oprette nye tjenester ved hjælp af Java eller .NET Core SDK'er, skal du benytte følgende valgfri konfiguration nedenfor.

## <a name="install-the-java-sdk-and-eclipse-neon-plugin-optional"></a>Installere Java SDK og Eklipse Neon plug-in'et (valgfrit)

Java-SDK indeholder de biblioteker og skabeloner, der kræves for at opbygge Service strukturerede services ved hjælp af Java.

1. Installere Java SDK-pakken.

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```

2. Kør scriptet SDK konfiguration.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

Du kan installere plug-in'en til Eklipse til Service-strukturen fra Eklipse Neon IDE.

1. Eklipse, Kontrollér, at du har Buildship version 1.0.17 eller nyere installeret. Du kan kontrollere versionerne af installerede komponenter ved at vælge **Hjælp > installationsoplysninger**. Du kan opdatere Buildship ved hjælp af vejledningen [her][buildship-update].

2. For at installere plug-in'en til tjenesten strukturen skal du vælge **Hjælp > installere ny Software...**

3. I tekstfeltet "Arbejde med" angive: http://dl.windowsazure.com/eclipse/servicefabric

4. Klik på Tilføj.

    ![Eklipse plug-in][sf-eclipse-plugin]

5. Vælg Service-strukturen plug-in'en, og klik på Næste.

6. Fortsæt gennem installationen, og accepter slutbrugerlicensaftalen.

## <a name="install-the-net-core-sdk-optional"></a>Installere .NET Core SDK (valgfrit)

.NET Core SDK indeholder de biblioteker og skabeloner, der kræves for at opbygge Service strukturerede services Hjælp på tværs af platforme .NET Core.

1. Installere .NET Core SDK-pakken.

    ```bash
    sudo apt-get install servicefabricsdkcsharp
    ```

2. Kør scriptet SDK konfiguration.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
    ```

## <a name="next-steps"></a>Næste trin

- [Oprette dit første Java-program på Linux](service-fabric-create-your-first-linux-application-with-java.md)

- [Forberede din udviklingsmiljø på OSX](service-fabric-get-started-mac.md)


<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
