<properties
   pageTitle="Programmet livscyklus i tjenesten strukturen | Microsoft Azure"
   description="I denne artikel beskrives udvikling, implementering, test, opgradering, vedligeholde og fjerne tjenesten strukturen programmer."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>


<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>


# <a name="service-fabric-application-lifecycle"></a>Tjenesten strukturen programmet livscyklus
Som med andre platforme, et program på Azure Service-strukturen normalt går gennem følgende faser: design, udvikling, test, installation, opgradering, vedligeholdelse og fjernelse. Tjenesten strukturen indeholder førsteklasses support til skyen programmer, fra udvikling ved implementering, daglig administration og vedligeholdelse til eventuel udfase komplette program livscyklus. Tjenesten modellen gør det muligt for flere forskellige roller til at deltage i programmet livscyklus uafhængigt af hinanden. Denne artikel indeholder en oversigt over API'erne, og hvordan de bruges af de forskellige roller i hele faserne i tjenesten strukturen programmet livscyklus.

## <a name="service-model-roles"></a>Tjenesten model roller
Tjenesten model roller er:

- **Tjenesten udvikler**: udvikler modul og generisk tjenester, der kan igen forventede og bruges i flere programmer af samme type eller forskellige typer. For eksempel kan en kø tjeneste bruges til at oprette en ticketing (helpdesk) eller et e-handel program (Indkøbsvogn).

- **Programmet udvikler**: opretter programmer ved at integrere en samling af tjenester til at opfylde bestemte specifikke krav eller scenarier. For eksempel en e-handel websted kan integrere "JSON uden status Front-End-tjenesten," "Auktion med høj sikkerhed tjeneste" og "Kø med høj sikkerhed Service" til at opbygge en auctioning løsning.

- **Programmet administrator**: foretager beslutninger programkonfiguration (udfylde konfiguration Skabelonparametre), installation (tilknytning til tilgængelige ressourcer) og kvaliteten af tjenesten. For eksempel beslutter en programadministrator landestandard (på engelsk for USA) eller japansk til Japan, for eksempel af programmet. Et andet udløst program kan have forskellige indstillinger.

- **Operatoren**: installerer programmer, der er baseret på programkonfigurationen og krav, der er angivet af administratoren af programmet. For eksempel en operator bestemmelser og installerer programmet og sikrer, at den kører i Azure. Operatorer overvåge programoplysninger tilstand og ydeevne og vedligeholde fysisk infrastrukturen efter behov.


## <a name="develop"></a>Udvikle
1. En *tjeneste udvikler* udvikler forskellige typer tjenester ved hjælp af [Pålidelig aktører](service-fabric-reliable-actors-introduction.md) eller [Pålidelige tjenester](service-fabric-reliable-services-introduction.md) programming modellen.
2. En *tjeneste udvikler* beskrives med en erklæring tjenestetyperne udviklede i en tjenestemanifestfilen bestående af en eller flere kode, konfiguration og data pakker.
3. Et *program udvikler* opbygger derefter et program med forskellige typer.
4. Et *program udvikler* beskrives med en erklæring programtype i et progammanifest ved at referere til tjenesten manifester for de enkelte tjenester og korrekt tilsidesætte og anvende parametre i forskellige indstillinger for konfiguration og installation af de enkelte tjenester.

Se [Introduktion til pålidelig aktører](service-fabric-reliable-actors-get-started.md) og [komme i gang med pålidelig Services](service-fabric-reliable-services-quick-start.md) eksempler.

## <a name="deploy"></a>Installere
1. En *administrator af programmet* tilpasser programtype til et bestemt program skal installeres til en tjeneste-strukturen klynge ved at angive de ønskede parametre i elementet **ApplicationType** i programmanifestet.

2. En *operator* uploader pakken programmet til klynge billede store ved hjælp af den [ **CopyApplicationPackage** metode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx) eller [ **Kopiér ServiceFabricApplicationPackage** cmdlet](https://msdn.microsoft.com/library/azure/mt125905.aspx). Programpakken indeholder programmanifestet og samling af servicepakker. Tjenesten strukturen installerer programmer fra den programpakke, der er gemt i store billede, som kan være et Azure blob-lager eller systemtjenesten Service-strukturen.

3. *Operatoren* klargør derefter programtype i target klynge i programpakken overførte ved hjælp af [ **ProvisionApplicationAsync** metode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx), [ **Registrer ServiceFabricApplicationType** cmdlet](https://msdn.microsoft.com/library/azure/mt125958.aspx)eller [ **klargøring et program** RESTEN handling](https://msdn.microsoft.com/library/azure/dn707672.aspx).

4. Efter klargøring af programmet, starter en *operator* programmet med de angivne *programmet administrator* ved hjælp af den [ **CreateApplicationAsync** metode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync.aspx), [ **Ny ServiceFabricApplication** cmdlet](https://msdn.microsoft.com/library/azure/mt125913.aspx)eller [ **Opret program** RESTEN handlingen](https://msdn.microsoft.com/library/azure/dn707676.aspx)parametre.

5. Når programmet er blevet installeret, bruges en *operator* [ **CreateServiceAsync** metode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.createserviceasync.aspx), [ **Ny ServiceFabricService** cmdlet](https://msdn.microsoft.com/library/azure/mt125874.aspx)eller [ **Oprette Service** RESTEN handling](https://msdn.microsoft.com/library/azure/dn707657.aspx) til at oprette nye forekomster af tjenesten for det program, der er baseret på tilgængelig tjeneste.

6. Programmet kører nu i tjenesten strukturen klynge.

Du kan finde eksempler i [Implementer et program](service-fabric-deploy-remove-applications.md) .

## <a name="test"></a>Test
1. Når du anvender for den lokale udvikling klynge eller en test klynge, kører en *tjeneste udvikler* scenariet indbyggede failover test ved hjælp af [**FailoverTestScenarioParameters**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenarioparameters.aspx) og [**FailoverTestScenario**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenario.aspx) klasser eller [ **Aktiver ServiceFabricFailoverTestScenario** cmdlet](https://msdn.microsoft.com/library/azure/mt644783.aspx). Scenariet failover test kører en bestemt tjeneste via vigtige overgange og failover at sikre, at det er stadig tilgængelig og fungerer.

2. *Tjenesten udvikler* kører derefter indbyggede kaos test scenarie med [**ChaosTestScenarioParameters**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenarioparameters.aspx) og [**ChaosTestScenario**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenario.aspx) klasser eller [ **Aktiver ServiceFabricChaosTestScenario** cmdlet](https://msdn.microsoft.com/library/azure/mt644774.aspx). Scenariet kaos test Bæltematerialets tilfældigt flere node, kode pakke og replika fejl i klyngen.

3. *Tjenesten udvikler* [test - tjenester kommunikation](service-fabric-testability-scenarios-service-communication.md) ved redigering test scenarier, som flytter primære replikaer omkring klyngen.

Du kan få flere oplysninger i [Introduktion til tjenesten fejl analyse](service-fabric-testability-overview.md) .

## <a name="upgrade"></a>Opgradere
1. En *tjeneste udvikler* opdateres de enkelte tjenester af programmet instantieret og/eller løser fejl og giver en ny version af tjenestemanifestet.

2. Et *program udvikler* tilsidesætter og parameterizes indstillingerne konfiguration og installation af ensartet tjenesterne og giver en ny version af programmanifestet. Programmet udvikler derefter inkorporerer de nye versioner af tjenesten manifester til programmet og giver en ny version af programtype i en opdateret programpakke.

3. En *administrator af programmet* inkorporerer den nye version af typen programmet til destinationsprogrammet ved at opdatere de relevante parametre.

5. En *operator* uploader den opdaterede programpakke til klynge billede store ved hjælp af den [ **CopyApplicationPackage** metode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx) eller [ **Kopiér ServiceFabricApplicationPackage** cmdlet](https://msdn.microsoft.com/library/azure/mt125905.aspx). Programpakken indeholder programmanifestet og samling af servicepakker.

6. En *operator* danner den nye version af programmet i target klynge ved hjælp af den [ **ProvisionApplicationAsync** metode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx), [ **Registrer ServiceFabricApplicationType** cmdlet](https://msdn.microsoft.com/library/azure/mt125958.aspx)eller [ **klargøring et program** RESTEN handling](https://msdn.microsoft.com/library/azure/dn707672.aspx).

7. En *operator* opgraderer destinationsprogrammet til den nye version, der bruger den [ **UpgradeApplicationAsync** metode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.upgradeapplicationasync.aspx), [ **Start-ServiceFabricApplicationUpgrade** cmdlet](https://msdn.microsoft.com/library/azure/mt125975.aspx)eller [ **opgradere et program** RESTEN handling](https://msdn.microsoft.com/library/azure/dn707633.aspx).

8. En *operator* kontrollerer status for opgradering, der bruger den [ **GetApplicationUpgradeProgressAsync** metode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.getapplicationupgradeprogressasync.aspx), [ **Get-ServiceFabricApplicationUpgrade** cmdlet](https://msdn.microsoft.com/library/azure/mt125988.aspx)eller [ **Få programmet opgradere opgavefremdrift** RESTEN handling](https://msdn.microsoft.com/library/azure/dn707631.aspx).

9. Hvis det er nødvendigt, *operator* ændrer og genanvender parametrene for den aktuelle program opgradering, der bruger den [ **UpdateApplicationUpgradeAsync** metode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.updateapplicationupgradeasync.aspx), [ **Opdater ServiceFabricApplicationUpgrade** cmdlet](https://msdn.microsoft.com/library/azure/mt126030.aspx)eller [ **Opdater programmet opgradere** RESTEN handling](https://msdn.microsoft.com/library/azure/mt628489.aspx).

10. Hvis det er nødvendigt, er *operatoren* annullerer den aktuelle program opgradering, der bruger den [ **RollbackApplicationUpgradeAsync** metode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.rollbackapplicationupgradeasync.aspx), [ **Start-ServiceFabricApplicationRollback** cmdlet](https://msdn.microsoft.com/library/azure/mt125833.aspx)eller [ **Rollback programmet opgradere** RESTEN handling](https://msdn.microsoft.com/library/azure/mt628494.aspx).

11. Tjenesten strukturen opgraderer destinationsprogrammet kører i klyngen uden at miste tilgængeligheden af en af dens enkelte tjenester.

Se [programmet opgradere selvstudium](service-fabric-application-upgrade-tutorial.md) eksempler.

## <a name="maintain"></a>Vedligeholde
1. Tjenesten strukturen grænseflader til opgradering af operativsystemet og rettelser, med Azure infrastrukturen at garantere tilgængeligheden af alle programmer, der kører i klyngen.

2. Til opgraderinger og at den tjeneste strukturen platform-opdateringer opgraderer Service-strukturen selve uden at miste tilgængeligheden af en af de programmer, der kører på klyngen.

3. En *administrator af programmet* godkender tilføjelse eller fjernelse af knuder fra en klynge efter analyse af historiske kapacitet anvendelsen data og forventede fremtidige behov.

4. En *operator* tilføjer og fjerner noder, der er angivet af *programmet administrator*.

5. Når nye noder føjes til, eller eksisterende noder fjernes fra klyngen, Service-strukturen automatisk indlæsning-saldi de programmer, der kører på tværs af alle noder i klynge for at opnå optimal ydeevne.

## <a name="remove"></a>Fjerne
1. En *operator* kan slette en bestemt forekomst af en aktiv tjeneste i klyngen uden at fjerne hele programmet ved hjælp af den [ **DeleteServiceAsync** metode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync.aspx), [ **Fjern ServiceFabricService** cmdlet](https://msdn.microsoft.com/library/azure/mt126033.aspx)eller [ **Slette tjenesten** RESTEN handling](https://msdn.microsoft.com/library/azure/dn707687.aspx).  

2. En *operator* kan også slette en forekomst af programmet og alle dens tjenester, der bruger den [ **DeleteApplicationAsync** metode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync.aspx), [ **Fjern ServiceFabricApplication** cmdlet](https://msdn.microsoft.com/library/azure/mt125914.aspx)eller [ **Slette programmet** RESTEN handling](https://msdn.microsoft.com/library/azure/dn707651.aspx).

3. Når de programmer og tjenester er stoppet, kan *operatoren* klargøring for den programtype, der bruger den [ **UnprovisionApplicationAsync** metode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync.aspx), [ **Fjerner registreringen af ServiceFabricApplicationType** cmdlet](https://msdn.microsoft.com/library/azure/mt125885.aspx)eller [ **fjernelsen et program** RESTEN handling](https://msdn.microsoft.com/library/azure/dn707671.aspx). Tilhørende programpool programtypen fjerner ikke programpakken fra ImageStore. Du skal fjerne programpakken manuelt.

4. En *operator* fjerner programpakken fra ImageStore ved hjælp af den [ **RemoveApplicationPackage** metode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage.aspx) eller [ **Fjern ServiceFabricApplicationPackage** cmdlet](https://msdn.microsoft.com/library/azure/mt163532.aspx).

Du kan finde eksempler i [Implementer et program](service-fabric-deploy-remove-applications.md) .

## <a name="next-steps"></a>Næste trin

Du kan finde flere oplysninger om udvikling, se test og administration af tjenesten strukturen programmer og tjenester:

- [Pålidelige aktører](service-fabric-reliable-actors-introduction.md)
- [Pålidelige tjenester](service-fabric-reliable-services-introduction.md)
- [Installere et program](service-fabric-deploy-remove-applications.md)
- [Opgradering af programmet](service-fabric-application-upgrade.md)
- [Mulighed for at teste oversigt](service-fabric-testability-overview.md)
- [REST-baseret program livscyklus eksempel](service-fabric-rest-based-application-lifecycle-sample.md)
