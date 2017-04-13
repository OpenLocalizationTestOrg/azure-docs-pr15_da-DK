<properties
   pageTitle="Introduktion til installation og opgradering apps på din lokale klynge | Microsoft Azure"
   description="Konfigurere en lokal Service-strukturen klynge, implementere et eksisterende program til den og derefter opgradere dette program."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2016"
   ms.author="ryanwi;mikhegn"/>

# <a name="get-started-with-deploying-and-upgrading-applications-on-your-local-cluster"></a>Introduktion til installation og opgradering programmer på din lokale klynge
Azure Service-strukturen SDK indeholder en fuldstændig lokale udviklingsmiljø, som du kan bruge til at komme hurtigt i gang med implementering og administration af programmer på en lokal klynge. I denne artikel, du opretter en lokal klynge, installere et eksisterende program til den og derefter opgradere programmet igen for at en ny version, alle fra Windows PowerShell.

> [AZURE.NOTE] I denne artikel antages det, at du allerede [konfigurere dit udviklingsmiljø](service-fabric-get-started.md).

## <a name="create-a-local-cluster"></a>Oprette en lokal klynge
En tjeneste-strukturen klynge repræsenterer et sæt hardwareressourcer, som du kan installere programmer. Typisk består en klynge af overalt fra fem til mange tusindvis af computere. Men Service-strukturen SDK omfatter en klynge konfiguration, som kan køre på en enkelt maskine.

Det er vigtigt at forstå, at den lokale klynge Service-strukturen ikke er en emulator eller simulator. Den kører den samme platform kode, der findes på flere maskiner klynger. Den eneste forskel er, at der køres platform processer, der er normalt udbrede fem maskiner på én maskine.

SDK er der to metoder til at konfigurere en lokal klynge: et Windows PowerShell-script og lokale klynge Manager system bakke app. I dette selvstudium bruger vi PowerShell-script.

> [AZURE.NOTE] Hvis du allerede har oprettet en lokal klynge ved at installere et program i Visual Studio, kan du springe dette afsnit.


1. Start et nyt vindue i PowerShell som administrator.

2. Du kan køre scriptet klynge konfiguration fra mappen SDK:

    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
    ```

    Konfiguration af klynge tager et øjeblik. Når installationen er fuldført, skal du se afsnittet output, der svarer til:

    ![Klynge konfiguration output][cluster-setup-success]

    Du er nu klar til at prøve at implementere et program til din klynge.

## <a name="deploy-an-application"></a>Installere et program
Tjenesten strukturen SDK indeholder et bredt udvalg af strukturer og udvikler værktøjer til oprettelse af programmer. Hvis du er interesseret i at vide, hvordan du opretter programmer i Visual Studio, skal du se [oprette dit første Service-strukturen program i Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).

I dette selvstudium skal vi bruge en eksisterende Northwind (kaldet WordCount), så vi kan fokusere på management aspekter af platformen – herunder installation, overvågning og opgradering af.


1. Start et nyt vindue i PowerShell som administrator.

2. Importere Service-strukturen SDK PowerShell-modulet.

    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```

3. Opret en mappe for at gemme det program, du downloader og installerer, som C:\ServiceFabric.

    ```powershell
    mkdir c:\ServiceFabric\
    cd c:\ServiceFabric\
    ```

4. [Hente programmet WordCount](http://aka.ms/servicefabric-wordcountapp) til placeringen, du har oprettet.  Bemærk: Microsoft Edge-browseren gemmer filen med filtypenavnet *.zip* .  Skift filtypen til *.sfpkg*.

5. Oprette forbindelse til den lokale klynge:

    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```

6. Oprette et nyt program ved hjælp af kommandoen af i SDK-installation med et navn og en sti til pakken programmet på computeren.

    ```powershell  
  Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```

    Hvis alle går godt, skal du se afsnittet output ud som følger:

    ![Installere et program til den lokale klynge][deploy-app-to-local-cluster]

7. Start browseren for at få vist programmet i handling, og gå til [http://localhost:8081/wordcount/index.html](http://localhost:8081/wordcount/index.html). Du burde se:

    ![Installerede program brugergrænseflade][deployed-app-ui]

    Programmet WordCount er meget simpel. Den indeholder klientsiden JavaScript-kode for at generere tilfældigt fem tegn "ord", som er videresendes derefter til programmet via ASP.NET Web API. En med høj sikkerhed tjeneste registrerer antallet af ord, tælles. De er opdelt baseret på det første tegn i ordet. Du kan finde koden til appen WordCount i den [første gang prøver](https://azure.microsoft.com/documentation/samples/service-fabric-dotnet-getting-started/).

    Det program, vi installeret indeholder fire partitioner. Så ord, der begynder med A til G er gemt i den første partition, ord, der begynder med H gennem N er gemt i den anden partition, og så videre.

## <a name="view-application-details-and-status"></a>Se detaljer om programmet og status
Nu hvor vi har installeret programmet, Lad os se på nogle af app-oplysningerne i PowerShell.

1. Alle installerede programmer i klyngen-forespørgsel:

    ```powershell
    Get-ServiceFabricApplication
    ```

    Hvis resultatet, at du kun har installeret appen WordCount, kan du se noget lignende:

    ![Forespørgsel alle installerede programmer i PowerShell][ps-getsfapp]

2. Gå til næste niveau ved forespørgsel om sæt af tjenester, der er inkluderet i programmet WordCount.

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Liste over tjenester til programmet i PowerShell][ps-getsfsvc]

    Programmet består af to services – web front end og tjenesten med høj sikkerhed, der administrerer ordene.

3. Til sidst skal du se nærmere på listen over partitioner for WordCountService:

    ```powershell
    Get-ServiceFabricPartition 'fabric:/WordCount/WordCountService'
    ```

    ![Få vist service partitionerne i PowerShell][ps-getsfpartitions]

    Række kommandoer, som du har brugt, som alle Service-strukturen PowerShell-kommandoer er tilgængelige for en hvilken som helst klynge, du kan oprette forbindelse til lokale eller en fjerndatabase.

    Du kan bruge værktøjet webbaseret tjeneste strukturen Explorer for et mere visuelt at interagere med klyngen, ved at navigere til [http://localhost:19080/Explorer](http://localhost:19080/Explorer) i browseren.

    ![Se detaljer om programmet i tjenesten strukturen Explorer][sfx-service-overview]

    > [AZURE.NOTE] Hvis du vil vide mere om tjenesten strukturen Explorer skal du se [visualisere din klynge med tjenesten strukturen Stifinder](service-fabric-visualizing-your-cluster.md).

## <a name="upgrade-an-application"></a>Opgradere et program
Tjenesten strukturen indeholder ingen nedetid opgraderinger ved at overvåge tilstanden for programmet, som den ruller på tværs af klyngen. Lad os udføre en enkel opgradering af programmet WordCount.

Den nye version af programmet nu tæller kun de ord, der starter med en vokal. Som opgraderingen løber, kan vi se to ændringer i programmets funktionsmåde. Først skal skal den rente, hvormed antallet vokser langsomt, da færre ord tælles. Andet, da den første partition har to korte og lange vokallyde (A og E), og alle andre partitioner kun indeholde én hver, dens Tæl skal til sidst begynder at outpace de andre.

1. [Hent pakken WordCount v2](http://aka.ms/servicefabric-wordcountappv2) til den samme placering, hvor du har hentet v1-pakke.

2. Gå tilbage til PowerShell-vinduet, og brug af SDK opgradering kommando til at registrere den nye version i klyngen. Begynder at opgradere strukturen: / WordCount programmet.

    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```

    Du bør se output i PowerShell, der ser ud som følger som opgraderingen begynder.

    ![Opgradere opgavefremdrift i PowerShell][ps-appupgradeprogress]

3. Når opgraderingen er du fortsætter, kan det være nemmere at overvåge dens status fra Service-strukturen Stifinder. Start et browservindue, og gå til [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Udvid **programmer** i træet i venstre side og derefter vælge **WordCount**, og til sidst **strukturen: / WordCount**. Fanen essentials, får du vist status for opgradering, som det skrider frem til den klynge opgradering domæner.

    ![Opgradere opgavefremdrift i tjenesten strukturen Explorer][sfx-upgradeprogress]

    Som opgraderingen skrider frem gennem hver domæne, udføres tilstandskontrol for at sikre, at programmet fungerer korrekt.

4. Hvis du tidligere køre forespørgslen igen til sæt af tjenester i strukturen: / WordCount programmet, Bemærk, at versionen af WordCountService ændres, men versionen af WordCountWebService blev ikke:

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Forespørgsel programtjenester efter opgradering][ps-getsfsvc-postupgrade]

    Det fremhæver Sådan Service-strukturen håndteres programmet opgraderinger. Den rører kun det sæt af tjenesterne (eller kode/konfiguration pakker i disse tjenester), der har ændret, hvilket gør procesfarver af opgraderingen hurtigere og mere pålidelig.

5. Til sidst skal du gå tilbage til browseren til at overholde funktionsmåden for den nye version af programmet. Som forventet, antallet skrider frem langsommere, og den første partition afsluttes med lidt mere lydstyrken.

    ![Få vist den nye version af programmet i browseren][deployed-app-ui-v2]

## <a name="cleaning-up"></a>Rydde op

Før afslutning, er det vigtigt at huske, at den lokale klynge er et reelt tal. Programmer fortsætte med at køre i baggrunden, indtil du fjerner dem.  Afhængigt af hvilke af dine apps, kan en igangværende app optager betydelige ressourcer på din computer. Har du forskellige valgmuligheder til at administrere programmer og klyngen:

1. Hvis du vil fjerne et enkelt program og alle dens data, skal du køre følgende:

    ```powershell
    Unpublish-ServiceFabricApplication -ApplicationName "fabric:/WordCount"
    ```

    Eller slette programmet fra menuen **Handlinger** i tjenesten strukturen Explorer eller kontekstmenu i programmet listevisningen i den venstre rude.

    ![Slet et program er Service-strukturen Explorer][sfe-delete-application]

2. Efter sletning af et program fra klyngen, kan du fjerne registreringen af version 1.0.0 og 2.0.0 af typen WordCount programmet. Sletningen fjerner programpakker, herunder kode og konfiguration, fra den klynge billede store.

    ```powershell
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 2.0.0
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0
    ```

    Eller i tjenesten strukturen Explorer skal du vælge **Fjernelsen Type** for programmet.

3. For at lukke klyngen, men bevare programmet data og sporinger, skal du klikke på **Stop lokale klynge** i system bakke app.

4. Hvis du vil slette klyngen helt, skal du klikke på **Fjern lokale klynge** i system bakke app. Denne indstilling resulterer i en anden langsom installation næste gang du trykker på F5 i Visual Studio. Fjerne den lokale klynge kun, hvis du ikke vil bruge den til stykke tid, eller hvis du har brug at frigøre ressourcer.

## <a name="1-node-and-5-node-cluster-mode"></a>1 node og 5 Node klynge tilstand

Når du arbejder med den lokale klynge du udvikler programmer, du ofte finde dig i forbindelse med hurtig gentagelser skrive kode, fejlfinding, ændring af kode, fejlfinding osv. For at optimere denne proces, kan køre den lokale klynge i to tilstande: 1 Node eller 5 Node. Begge klynge tilstande har deres fordele.
5 node klynge tilstand gør det muligt at arbejde med en reelle klynge. Du kan teste failover scenarier, arbejde med flere forekomster og kopier af dine tjenester.
1 node klynge tilstand er optimeret til at gøre hurtig installation og registrering af tjenesterne, kan hjælpe dig med hurtigt at validere kode ved hjælp af tjenesten strukturen runtime.

Både 1 Node klynge tilstand og 5 Node klynge tilstand er ikke en emulator eller simulator. Den kører den samme platform kode, der findes på flere maskiner klynger.

> [AZURE.NOTE] Denne funktion findes i SDK version 5.2 og derover.

For at ændre klyngetilstanden til en 1 Node klynge skal du enten bruge Service strukturen lokale klynge Manager eller bruge PowerShell på følgende måde:

1. Start et nyt vindue i PowerShell som administrator.

2. Du kan køre scriptet klynge konfiguration fra mappen SDK:

    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster
    ```

    Konfiguration af klynge tager et øjeblik. Når installationen er fuldført, skal du se afsnittet output, der svarer til:
    
    ![Klynge konfiguration output][cluster-setup-success-1-node]

Hvis du bruger Service strukturen lokale klynge Manager:

![Skift klynge tilstand][switch-cluster-mode]

> [AZURE.WARNING] Når du ændrer klynge tilstand, den aktuelle klynge fjernes fra dit system, og der oprettes en ny klynge. De data, skal du har gemt i klynge, slettes, når du ændrer klynge tilstand.

## <a name="next-steps"></a>Næste trin
- Nu, hvor du har installeret og opgraderet nogle færdigbyggede programmer, kan du [prøve at bygge din egen i Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).
- Alle de handlinger, der er udført på den lokale klynge i denne artikel kan udføres på en [Azure klynge](service-fabric-cluster-creation-via-portal.md) samt.
- Opgraderingen, som vi udføres i denne artikel blev grundlæggende. Se den [opgradere dokumentation](service-fabric-application-upgrade.md) til få mere at vide om power og fleksibilitet af tjenesten strukturen opgraderinger.

<!-- Images -->

[cluster-setup-success]: ./media/service-fabric-get-started-with-a-local-cluster/LocalClusterSetup.png
[extracted-app-package]: ./media/service-fabric-get-started-with-a-local-cluster/ExtractedAppPackage.png
[deploy-app-to-local-cluster]: ./media/service-fabric-get-started-with-a-local-cluster/DeployAppToLocalCluster.png
[deployed-app-ui]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-v1.png
[deployed-app-ui-v2]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-PostUpgrade.png
[sfx-app-instance]: ./media/service-fabric-get-started-with-a-local-cluster/SfxAppInstance.png
[sfx-two-app-instances-different-partitions]: ./media/service-fabric-get-started-with-a-local-cluster/SfxTwoAppInstances-DifferentPartitionCount.png
[ps-getsfapp]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFApp.png
[ps-getsfsvc]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc.png
[ps-getsfpartitions]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFPartitions.png
[ps-appupgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/PS-AppUpgradeProgress.png
[ps-getsfsvc-postupgrade]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc-PostUpgrade.png
[sfx-upgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/SfxUpgradeOverview.png
[sfx-service-overview]: ./media/service-fabric-get-started-with-a-local-cluster/sfx-service-overview.png
[sfe-delete-application]: ./media/service-fabric-get-started-with-a-local-cluster/sfe-delete-application.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[switch-cluster-mode]: ./media/service-fabric-get-started-with-a-local-cluster/switch-cluster-mode.png
