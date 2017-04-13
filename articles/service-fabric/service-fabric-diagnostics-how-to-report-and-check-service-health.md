<properties
   pageTitle="Rapportere og tjekke tilstand med Azure Service strukturen | Microsoft Azure"
   description="Lære at sende tilstandsrapporter fra din tjenestekode og Sådan kontrollerer du tilstanden for din tjeneste ved hjælp af værktøjerne for overvågning af sundhed, som indeholder et Azure Service-strukturen."
   services="service-fabric"
   documentationCenter=".net"
   authors="toddabel"
   manager="mfussell"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/06/2016"
   ms.author="toddabel"/>

# <a name="report-and-check-service-health"></a>Rapportere og se tjenestetilstand
Når dine tjenester støder på problemer, afhænger af din mulighed for at besvare og rette hændelser og udfald din mulighed for at finde problemerne hurtigt. Hvis du rapporterer problemer og fejl på Azure Service strukturen sundhed Manager fra din tjenestekode, kan du bruge standard sundhed overvågning værktøjer, som tjenesten strukturen for at kontrollere status for systemtilstand.

Der er to måder, kan du rapportere tilstand fra tjenesten:

- Brug [Partition](https://msdn.microsoft.com/library/system.fabric.istatefulservicepartition.aspx) eller [CodePackageActivationContext](https://msdn.microsoft.com/library/system.fabric.codepackageactivationcontext.aspx) objekter.  
Du kan bruge den `Partition` og `CodePackageActivationContext` objekter til at rapportere tilstanden for elementer, der er en del af den aktuelle kontekst. Kode, der kører som en del af en replika kan for eksempel rapportere sundhed kun på replikaen, den partition, der tilhører den og det program, det er en del af.

- Brug `FabricClient`.   
Du kan bruge `FabricClient` til rapporten tilstand fra tjenestekode, hvis klyngen ikke er [sikker](service-fabric-cluster-security.md) eller hvis tjenesten kører med administratortilladelser. Dette er ikke sand i de fleste reale scenarier. Med `FabricClient`, kan du rapportere tilstand på en hvilken som helst enhed, der er en del af klyngen. Ideelt set dog skal tjenestekode kun sende rapporter, der er relateret til sin egen tilstand.

I denne artikel vejledes du gennem et eksempel, at rapporter tilstand fra tjenesten koden. I eksempel vises også, hvordan de værktøjer, som indeholder et Service-strukturen kan bruges til at kontrollere status for systemtilstand. I denne artikel er beregnet på at være en hurtig introduktion til tilstanden overvågning funktionerne i tjenesten struktur. Du kan finde mere detaljerede oplysninger, kan du læse serie af dybdegående artikler om tilstand, der starter med linket i slutningen af denne artikel.

## <a name="prerequisites"></a>Forudsætninger
Du skal have installeret følgende:

   * Visual Studio 2015
   * Tjenesten strukturen SDK

## <a name="to-create-a-local-secure-dev-cluster"></a>Sådan oprettes en lokal sikker Udviklingscenter klynge
- Åbn PowerShell med administratortilladelser, og Kør følgende kommandoer.

![Kommandoer, der viser, hvordan du opretter en sikker Udviklingscenter klynge](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Installere et program og tjekke dens tilstand

1. Åbne Visual Studio som administrator.

2. Oprette et projekt ved hjælp af skabelonen **Med høj sikkerhed tjeneste** .

    ![Oprette et Service-strukturen program med med høj sikkerhed Service](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)

3. Tryk på **F5** for at køre programmet i fejlsikret tilstand. Programmet installeres til den lokale klynge.

4. Når programmet kører, skal du højreklikke på det lokale klynge Manager-ikonet i meddelelsesområdet, og vælg **Administrer lokale klynge** fra genvejsmenuen for at åbne Service-strukturen Stifinder.

    ![Åbn Service-strukturen Stifinder fra proceslinjen](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)

5. Programmet tilstanden skal vises som i dette billede. Programmet skal være sund uden fejl på nuværende tidspunkt.

    ![Sund programmet i tjenesten strukturen Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)

6. Du kan også kontrollere tilstanden ved hjælp af PowerShell. Du kan bruge ```Get-ServiceFabricApplicationHealth``` til at kontrollere et programs tilstand, og du kan bruge ```Get-ServiceFabricServiceHealth``` til at kontrollere en tjenestetilstand. Sundhed rapporten til det samme program i PowerShell er i dette billede.

    ![Sund programmet i PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Føje brugerdefineret sundhed begivenheder til din tjenestekode
Projektskabeloner Service-strukturen i Visual Studio indeholder eksempler på kode. Følgende trin viser, hvordan du kan rapportere brugerdefinerede sundhed begivenheder fra din tjenestekode. Disse rapporter vises automatisk i værktøjerne standard for systemtilstand overvågning, Service-strukturen indeholder som Service-strukturen Explorer, Azure portalen sundhed visning og PowerShell.

1. Åbner det program, du tidligere har oprettet i Visual Studio, eller Opret et nyt program ved hjælp af skabelonen **Med høj sikkerhed Service** Visual Studio.

2. Åbn filen Stateful1.cs, og find den `myDictionary.TryGetValueAsync` ringe på `RunAsync` metode. Du kan se, at denne metode returnerer en `result` , der indeholder den aktuelle værdi af tælleren, da den vigtige logik i dette program er at forhindre en optælling, der kører. Hvis dette er et reelt tal til computeren, og manglen resultat af repræsenteret en fejl, vil du vil markere pågældende begivenhed.

3. Tilføj følgende trin for at rapportere en tilstand hændelse, når manglen resultat af repræsenterer en fejl.

    en. Tilføje den `System.Fabric.Health` navneområde til filen Stateful1.cs.

    ```csharp
    using System.Fabric.Health;
    ```

    b. Føj følgende kode efter den `myDictionary.TryGetValueAsync` opkald

    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    Vi rapportere replika tilstand, fordi den rapporteres fra en med høj sikkerhed tjeneste. Den `HealthInformation` parameter gemmer oplysninger om problemet tilstand, der rapporteres.

    Hvis du har oprettet en uden status tjeneste, kan du bruge følgende kode

    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```

4. Hvis din tjeneste kører med administratortilladelser, eller hvis klyngen ikke er [sikker](service-fabric-cluster-security.md), kan du også bruge `FabricClient` for rapport sundhed som vist i følgende trin.  

    en. Oprette den `FabricClient` forekomst efter den `var myDictionary` definition.

    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```

    b. Føj følgende kode efter den `myDictionary.TryGetValueAsync` opkald.

    ```csharp
    if (!result.HasValue)
    {
       var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

5. Lad os simulere denne fejl, og se den vises i de værktøjer til overvågning af tilstand. For at efterligne fejlen, skrive en kommentar ud af den første linje i tilstanden rapportering kode, du tilføjede tidligere. Når du skrive en kommentar ud af den første linje, ser koden som i følgende eksempel.

    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
 Denne kode vil nu forespørgselsreglen denne tilstandsrapport, hver gang `RunAsync` udfører. Når du har foretaget ændringen, skal du trykke på **F5** for at køre programmet.

6. Når programmet kører, skal du åbne Service-strukturen Stifinder for at kontrollerer du tilstanden af programmet. Denne gang viser Service-strukturen Explorer, at programmet er beskadiget. Dette er på grund af den fejl, der blev rapporteret fra koden, vi har tilføjet tidligere.

    ![Beskadiget programmet i tjenesten strukturen Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)

7. Hvis du vælger den primære replika i trævisningen i tjenesten strukturen Explorer, vises, **Tilstanden** for angiver en fejl. Tjenesten strukturen Explorer viser også rapportoplysninger tilstand, der er blevet føjet til den `HealthInformation` parameter i koden. Du kan se de samme tilstandsrapporter i PowerShell og Azure portalen.

    ![Replika tilstand i tjenesten strukturen Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Denne rapport forbliver i tilstanden manager, indtil den erstattes af en anden rapport, eller indtil denne replika bliver slettet. Da vi ikke har angivet `TimeToLive` til denne tilstandsrapport i den `HealthInformation` objekt, rapporten udløber aldrig.

Vi anbefaler, at tilstand skal rapporteres på det mest detaljerede niveau, som i dette tilfælde er kopi. Du kan også rapportere tilstand på `Partition`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

For rapport sundhed på `Application`, `DeployedApplication`, og `DeployedServicePackage`, bruge `CodePackageActivationContext`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Næste trin
[Undersøgelse på Service-strukturen tilstand](service-fabric-health-introduction.md)
