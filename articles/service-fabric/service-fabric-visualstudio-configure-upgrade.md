<properties
   pageTitle="Konfigurere opgradering af en-strukturen tjenesteprogram | Microsoft Azure"
   description="Lær, hvordan du kan konfigurere indstillinger for opgradering af en-strukturen tjenesteprogram ved hjælp af Microsoft Visual Studio."
   services="service-fabric"
   documentationCenter="na"
   authors="cawaMS"
   manager="paulyuk"
   editor="tglee" />
<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="07/29/2016"
   ms.author="cawa" />

# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Konfigurere opgradering af en-strukturen tjenesteprogram i Visual Studio

Visual Studio tools til Azure Service strukturen yde opgradering support for at publicere til lokale eller en fjerndatabase klynger. Der er to fordele ved at opgradere dit program til en nyere version i stedet for at erstatte program under test og fejlfinding:

- Programmet data gå ikke tabt under opgraderingen.
- Tilgængelighed, forbliver høj, så der ikke er en hvilken som helst tjenesten blev afbrudt under opgraderingen, hvis der er tilstrækkelig forekomster af tjenesten udbrede opgradering domæner.

Test kan kun køres mod et program, mens det er ved at blive opgraderet.

## <a name="parameters-needed-to-upgrade"></a>Parametre, der er behov for at opgradere

Du kan vælge mellem to typer installation: normalt eller opgradering. En almindelig installation, slettes eventuelle tidligere installationsoplysninger og data på klynge, mens en opgradering installation bevarer den. Når du opgraderer en-strukturen tjenesteprogram i Visual Studio, skal du angive programmets opgradering parametre og tilstand kontrollere politikker. Programmet opgradering parametre hjælpe dig med at styre opgraderingen, mens sundhed Kontrollér politikker bestemmer, om opgraderingen blev gennemført. Se [Service-strukturen programmet opgraderingen: opgradere parametre](service-fabric-application-upgrade-parameters.md) få mere at vide.

Der er tre måder at opgradere: *overvågede*, *UnmonitoredAuto*og *UnmonitoredManual*.

  - En overvågede opgradering automatisere opgraderingen og tilstandskontrollen for programmet på computeren.

  - En UnmonitoredAuto opgradering automatisere opgraderingen, men springer tilstandskontrol programmet.

  - Når du gør en UnmonitoredManual opgradering, skal du manuelt opgradere hvert opgradering domæne.

Hver opgradering tilstand kræver forskellige typer parametre. Se [programmet opgradere parametre](service-fabric-application-upgrade-parameters.md) til at få mere at vide om de forskellige opgradering indstillinger.

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Opgradere en-strukturen tjenesteprogram i Visual Studio

Hvis du bruger værktøjerne Visual Studio Service-strukturen opgradere et Service-strukturen program, kan du angive en Publicer processen for at være en opgradering i stedet for en almindelig installation ved at markere afkrydsningsfeltet **opgradere programmet** .

### <a name="to-configure-the-upgrade-parameters"></a>Konfigurere parametrene opgradering

1. Klik på knappen **Indstillinger** ud for afkrydsningsfeltet. Dialogboksen **Rediger opgradere parametre** vises. Dialogboksen **Rediger opgradere parametre** understøtter overvågede, UnmonitoredAuto og UnmonitoredManual opgradering funktionsmåder.

2. Vælg den opgradering tilstand, som du vil bruge, og udfyld derefter gitteret parameter.

    Hver parameter har standardværdier. Valgfri parameter *DefaultServiceTypeHealthPolicy* tager en hash tabelinput. Her er et eksempel på hash tabel input format for *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* er en anden valgfri parameter, der fører en hash tabelinput i følgende format:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Her er en virkelige liv eksempel:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```

3. Hvis du vælger UnmonitoredManual opgradering tilstand, skal du starte et PowerShell console for at fortsætte, og Udfør opgraderingsprocessen manuelt. Refererer til [Service-strukturen programmet opgraderingen: Avancerede emner](service-fabric-application-upgrade-advanced.md) til at få mere at vide hvordan manuel opgraderingen fungerer.

## <a name="upgrade-an-application-by-using-powershell"></a>Opgradere et program ved hjælp af PowerShell

Du kan bruge PowerShell-cmdlet'er til at opgradere en-strukturen tjenesteprogram. Se [Service-strukturen programmet opgradere selvstudium](service-fabric-application-upgrade-tutorial.md) og [Start-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/mt125975.aspx) kan finde detaljerede oplysninger.

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Angive en politik for systemtilstand afkrydsningsfelt i manifestfilen til programmet

Hver tjeneste i en tjeneste-strukturen programmet kan have sin egen sundhed politikparametre, der tilsidesætter standardværdierne. Du kan angive følgende parameterværdier i programmet manifestfilen.

I følgende eksempel viser, hvordan du anvender en entydig sundhed politik for hver tjeneste i programmanifestet.

```
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## <a name="next-steps"></a>Næste trin
Du kan finde flere oplysninger om installation af et program, [Implementer et eksisterende program i Azure Service struktur](service-fabric-deploy-existing-app.md).
