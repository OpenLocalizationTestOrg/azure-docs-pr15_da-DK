<properties
   pageTitle="Opgradere en enkeltstående tjeneste strukturen klynge på Windows Server | Microsoft Azure"
   description="Opgradere Service-strukturen kode og/eller konfiguration, der kører en enkeltstående Service-strukturen klynge, herunder indstille klynge opdateringstilstand"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/10/2016"
   ms.author="chackdan"/>


# <a name="upgrade-your-standalone-service-fabric-cluster-on-windows-server"></a>Opgradere din enkeltstående Service-strukturen klynge på Windows Server

> [AZURE.SELECTOR]
- [Azure klynge](service-fabric-cluster-upgrade.md)
- [Enkeltstående klynge](service-fabric-cluster-upgrade-windows-server.md)

Design af upgradability er til en hvilken som helst moderne system, for at nå langsigtede succes af dit produkt. En tjeneste-strukturen klynge er en ressource, at du ejer. I denne artikel beskrives, hvordan du kan sikre dig, at klyngen altid kører understøttede versioner af service-strukturen kode og konfigurationer.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Styre versionerne struktur, der kører på din klynge

Du kan angive din klynge for at hente strukturen tjenesteopdateringerne, når Microsoft udgiver en ny version eller vælge at vælge en understøttede strukturen version, du vil din klynge skal være på. 

Du kan gøre dette ved at angive "fabricClusterAutoupgradeEnabled" klyngekonfigurationen til true eller false.


>[AZURE.NOTE] Sørg for at holde din klynge, der bruger en understøttet version af tjenesten struktur. Som, og når vi oplyse version af en ny version af tjenesten strukturen, markeres den tidligere version til slutningen af support efter et minimum af 60 dage fra denne dato. De nye versioner er offentliggjort [på service-strukturen teambloggen](https://blogs.msdn.microsoft.com/azureservicefabric/ ). Den nye version er tilgængelig til at vælge derefter. 


Du kan opgradere din klynge til den nye version kun, hvis du bruger en fremstilling-typografi node konfiguration, hvor der er allokeret hver tjeneste strukturen node på en separat fysiske eller virtuelt. Hvis du har en udvikling klynge, hvor der er mere end én tjeneste strukturen noder på en enkelt fysisk eller virtuel maskine, skal du nedbryde din klynge og indsætte det igen med den nye version.


Der findes to forskellige arbejdsprocesser til opgradering af din klynge til nyeste eller en tjeneste til strukturen version. En for klynger, der har forbindelse til at hente den nyeste version automatisk og den anden opgave til klynger, der er ingen forbindelse til at hente den nyeste version af tjenesten struktur.

### <a name="upgrade-the-clusters-with-connectivity-to-download-the-latest-code-and-configuration"></a>Opgradere klynger med forbindelse til at hente de seneste kode og konfiguration 

Følg disse trin for at opgradere din klynge til en understøttet version, hvis din klyngenoder har forbindelse til internettet til [http://download.microsoft.com](http://download.microsoft.com) 

For klynger, der har forbindelse til [http://download.microsoft.com](http://download.microsoft.com), søge vi jævnligt efter tilgængeligheden af service-strukturen versioner.


Når en ny tjeneste strukturen version er tilgængelig, hentes lokalt til klyngen pakken og klargjort til opgradering. Desuden for at informere kunden den nye version, markeres en eksplicit klynge sundhed advarsel ligner følgende:

"Den aktuelle klynge version [version #] som support stopper [dato].", når klyngen kører den nyeste version, advarslen forsvinder.


#### <a name="cluster-upgrade-workflow"></a>Klynge opgradering arbejdsproces.
 
Når du får vist klynge sundhed advarslen, skal du gøre følgende:

1. Oprette forbindelse til klyngen fra en hvilken som helst computer, der har administratoradgang til alle de computere, der er angivet som noder i klyngen. Den computer, der kører dette script på behøver ikke at være en del af klyngen

    ```powershell

    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3" 
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Hente en liste over service-strukturen versioner, du kan opgradere til

    ```powershell

    ###### Get the list of available service fabric versions 
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Du skal have en output nogenlunde sådan ud:

    ![få strukturen versioner][getfabversions]

3. Starte en klynge opgradering til en af de versioner, der er tilgængelige ved hjælp af [Start-ServiceFabricClusterUpgrade PowerShell cmd](https://msdn.microsoft.com/library/mt125872.aspx)

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    
    ```
Du kan overvåge forløbet af opgraderingen til Service-strukturen explorer eller ved at køre følgende kommando for power shell

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    If the cluster health policies are not met, the upgrade is rolled back. You can specify custom health policies at the time for the Start-ServiceFabricClusterUpgrade command refer to [this document](https://msdn.microsoft.com/library/mt125872.aspx) for details. 

Når du har løst de problemer, der udløste rollback, skal du starte opgraderingen igen ved at følge de samme trin som før.


### <a name="upgrade-the-clusters-with-uno-connectivityu-to-download-the-latest-code-and-configuration"></a>Opgradere klynger med <U>ingen forbindelse</u> til at hente de seneste kode og konfiguration

Følg disse trin for at opgradere din klynge til en understøttet version, hvis din klynge noder **ikke har** forbindelse til internettet til [http://download.microsoft.com](http://download.microsoft.com) 


>[AZURE.NOTE]Hvis du kører en klynge, der ikke er tilsluttet internettet, er du nødt til at overvåge service-strukturen teamblog for at få besked om en ny version. System **ikke** Placer klynge sundhed advarsel at advare dig af filen.  

1. Ændre din klynge konfiguration for at angive følgende egenskab til falsk.

        "fabricClusterAutoupgradeEnabled": false,

og starte en konfiguration opgradering. referere til [Start-ServiceFabricClusterUpgrade PS cmd](https://msdn.microsoft.com/library/mt125872.aspx) for detaljer om anvendelse. Den klynge manifest version er den version, du har i clusterConfig.JSON. Sørg for at opdatere den før du udsmidning fra konfiguration opgraderingen.

```powershell

    Start-ServiceFabricClusterUpgrade [-Config] [-ClusterConfigVersion] -FailureAction Rollback -Monitored 

```

#### <a name="cluster-upgrade-workflow"></a>Klynge opgradering arbejdsproces.
 


1. Hent den nyeste version af pakken fra [Opret service-strukturen klynge til windows server](service-fabric-cluster-creation-for-windows-server.md) -dokument 


1. Oprette forbindelse til klyngen fra en hvilken som helst computer, der har administratoradgang til alle de computere, der er angivet som noderne i klyngen. Den computer, der kører dette script på behøver ikke at være en del af klyngen 

    ```powershell

    ###### connect to the cluster
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3" 
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Kopiér den hentede pakke til klynge billede store.

    ```powershell

    ###### Get the list of available service fabric versions 
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"


    ```

2. Registrere den kopierede pakke 

    ```powershell

    ###### Get the list of available service fabric versions 
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file> 

    ###### Here is a filled out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab

     ```


3. Starte en klynge opgradering til en af de versioner, der er tilgængelig. 

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    
    ```
Du kan overvåge forløbet af opgraderingen til Service-strukturen explorer eller ved at køre følgende kommando for power shell

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    If the cluster health policies are not met, the upgrade is rolled back. You can specify custom health policies at the time for the start-serviceFabricClusterUpgrade command refer to [this document](https://msdn.microsoft.com/library/mt125872.aspx) for details. 

Når du har løst de problemer, der udløste rollback, skal du starte opgraderingen igen ved at følge de samme trin som før.



## <a name="next-steps"></a>Næste trin
- Lær at tilpasse nogle af de [strukturen klynge strukturen Tjenesteindstillinger](service-fabric-cluster-fabric-settings.md)
- Lær, hvordan du kan [tilpasse din klynge ind og ud](service-fabric-cluster-scale-up-down.md)
- Få mere at vide om [programmet opgraderinger](service-fabric-application-upgrade.md)

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
