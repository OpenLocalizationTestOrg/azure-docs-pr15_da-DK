<properties
   pageTitle="Oprette en enkeltstående klynge med Azure VM'er, der kører Windows | Microsoft Azure"
   description="Lær at oprette og administrere en Azure Service strukturen klynge på Azure virtuelle maskiner, der kører Windows Server."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/05/2016"
   ms.author="dkshir;chackdan"/>



# <a name="create-a-three-node-standalone-service-fabric-cluster-with-azure-virtual-machines-running-windows-server"></a>Oprette en enkeltstående Service-strukturen klynge med tre noder med Azure virtuelle maskiner, der kører Windows Server

I denne artikel beskriver, hvordan du opretter en klynge på Windows-baseret Azure virtuelle maskiner (VM'er), ved hjælp af enkeltstående Service-strukturen installationsprogrammet til Windows Server. Dette er en særlig udgave af [oprette og administrere en klynge, der kører på Windows Server](service-fabric-cluster-creation-for-windows-server.md) hvor FOS er [Azure VM'er, der kører Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md), men du ikke opretter [en Azure skybaseret tjeneste strukturen klynge](service-fabric-cluster-creation-via-portal.md). Forskellen er, at enkeltstående Service-strukturen klynge oprettet af de følgende trin administreres helt af dig, mens Azure skybaseret tjeneste strukturen klynger administreres og opgraderet af provideren Service-strukturen ressource.


## <a name="steps-to-create-the-standalone-cluster"></a>Trin til at oprette enkeltstående klynge

1. Log på portalen Azure og oprette en ny Windows Server 2012 R2 Datacenter VM i en ressourcegruppe. Læse artiklen [oprette en Windows-VM i portalen Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md) få mere at vide.
2. Føje et par flere Windows Server 2012 R2 Datacenter FOS til samme ressourcegruppe. Sørg for, at hver af FOS har samme administratorens brugernavn og adgangskode, når du har oprettet. Én gang oprettede skal du se afsnittet alle tre FOS i det samme virtuelle netværk.
3. Oprette forbindelse til hver af FOS og slå Windows Firewall med [Server Manager, lokalserver dashboard](https://technet.microsoft.com/library/jj134147.aspx). Dette sikrer, at netværkstrafikken kan kommunikere mellem maskiner. Mens forbindelse til hver computer, kan du få IP-adresse ved at åbne en kommandoprompt og skrive `ipconfig`. Alternativt kan du se IP-adressen på hver enkelt computer ved at vælge virtuelt netværksressourcen for ressourcegruppen i portalen Azure.
4. Oprette forbindelse til en af FOS og teste, at du kan pinge de andre to FOS korrekt.
5. Oprette forbindelse til en af de FOS og [download pakken standalone Service-strukturen til Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) til en ny mappe på computeren og udtrække pakken.
6. Åbn filen *ClusterConfig.Unsecure.MultiMachine.json* i Notesblok og redigere hver node med tre IP-adresserne på computerne. Ændre klyngenavnet øverst, og Gem filen.  En delvis eksempel klynge Manifestets er vist nedenfor.

    ```
    {
        "name": "TestCluster",
        "clusterManifestVersion": "1.0.0",
        "apiVersion": "2015-01-01-alpha",
        "nodes": [
        {
            "nodeName": "vm0",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.5",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        },
        {
            "nodeName": "vm1",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.4",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc2/r0",
            "upgradeDomain": "UD1"
        },
        {
            "nodeName": "vm2",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.6",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc3/r0",
            "upgradeDomain": "UD2"
        }
    ],
    ```

7. Åbne et [PowerShell ISE vindue](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise). Gå til den mappe, hvor du har hentet Installationsprogrammet hentede enkeltstående og gemt klynge manifestfilen. Køre følgende PowerShell-kommando.

    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
    ```

8. Du bør se PowerShell køre og oprette forbindelse til hver computer og oprette en klynge. Når om et minut, kan du kontrollere, om klyngen er funktionsdygtige ved at oprette forbindelse til tjenesten strukturen Explorer på en af computerens IP-adresse fx ved hjælp af `http://10.7.0.5:19080/Explorer/index.html`. Da dette er en enkeltstående klynge, ved hjælp af Azure FOS for at gøre det sikkert, du vil installere [certifikater til Azure FOS](service-fabric-windows-cluster-x509-security.md) eller konfigurere en af maskiner som [Windows Server Active Directory (AD) controller til Windows-godkendelse](service-fabric-windows-cluster-windows-security.md), ligesom du ville gøre lokalt.


## <a name="next-steps"></a>Næste trin
- [Oprette enkeltstående Service-strukturen klynger på Windows Server eller Linux](service-fabric-deploy-anywhere.md)
- [Tilføje eller fjerne noder til en enkeltstående Service-strukturen klynge](service-fabric-cluster-windows-server-add-remove-nodes.md)
- [Konfigurationsindstillinger for enkeltstående Windows klynge](service-fabric-cluster-manifest.md)
- [Sikre en enkeltstående klynge på Windows ved hjælp af Windows-sikkerhed](service-fabric-windows-cluster-windows-security.md)
- [Sikre en enkeltstående klynge på Windows ved hjælp af X509 certifikater](service-fabric-windows-cluster-x509-security.md)
