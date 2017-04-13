<properties
   pageTitle="Opgradering af programmet: Avancerede emner | Microsoft Azure"
   description="Denne artikel omhandler nogle avancerede emner, der vedrører opgradering af en tjeneste-strukturen programmet."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>

# <a name="service-fabric-application-upgrade-advanced-topics"></a>Tjenesten strukturen programmet opgraderingen: Avancerede emner

## <a name="adding-or-removing-services-during-an-application-upgrade"></a>Tilføje eller fjerne tjenester under en programmet opgradering

Om en ny tjeneste er føjet til et program, der er allerede installeret, og publicerede som en opgradering, tilføjes den nye tjeneste i det installerede program.  Sådan opgraderes påvirker ikke nogen af de tjenester, der var allerede er en del af programmet. Dog skal være startet en forekomst af den tjeneste, der blev føjet til den nye tjeneste skal være aktiv (ved hjælp af den `New-ServiceFabricService` cmdlet).

Services kan også fjernes fra et program som en del af en opgradering. Men alle aktuelle forekomster af tjenesten til-be-slettet skal stoppes, før du fortsætter med opgraderingen (ved hjælp af den `Remove-ServiceFabricService` cmdlet). 

## <a name="manual-upgrade-mode"></a>Manuel opgradering tilstand

> [AZURE.NOTE]  Den uovervågede manuelle tilstand skal betragtes kun for en mislykket eller afbrudt opgradering. Overvåget tilstand er det anbefalede opgradering tilstand for tjenesten strukturen programmer.

Azure Service-strukturen indeholder flere opgradering tilstande for at understøtte udvikling og fremstilling klynger. Installationsindstillinger valgt kan være forskellige for forskellige miljøer.

Overvåget rullende program opgraderingen er den mest almindelige opgradering til brug i fremstilling. Når politikken opgradering er angivet, sikrer Service-strukturen, at programmet sund før opgraderingen skrider frem.

 Administratoren af programmet kan bruge manuel rullende program opgradering tilstanden skal have fuld kontrol over opgradering status gennem de forskellige opgradering domæner. Denne tilstand er nyttig, når en tilpasset eller komplekse sundhed evaluering politik er påkrævet, eller der sker en ikke-traditionelt opgradering (for eksempel programmet er allerede i tab af data).

Endelig er automatiseret rullende program opgraderingen nyttige til udvikling eller test miljøer for at angive en fast gentagelse cyklus under service udviklingen.

## <a name="change-to-manual-upgrade-mode"></a>Ændre til manuel opgradering tilstand
**Manuel**– stoppe programmet opgraderingen på den aktuelle UD og ændre tilstanden opgradering til uovervågede manuelt. Administratoren skal manuelt opkald **MoveNextApplicationUpgradeDomainAsync** for at fortsætte med opgraderingen eller udløse en rollback ved at påbegynde en ny opgradering. Når opgraderingen træder i tilstanden manuel, forbliver den i tilstanden manuel, indtil en ny opgradering startes. Kommandoen **GetApplicationUpgradeProgressAsync** returnerer STRUKTUREN\_programmet\_opgradere\_tilstand\_rullende\_fremad\_AFVENTER.

## <a name="upgrade-with-a-diff-package"></a>Opgradere med en diff pakke

En tjeneste-strukturen programmet kan opgraderes ved klargøring med en fuld, selvstændig programpakke. Et program kan også opgraderes ved hjælp af en diff-pakke, der indeholder kun de opdaterede-programfiler, opdaterede programmanifestet og service manifestfilerne.

En komplet programpakke indeholder alle de filer, der er nødvendige for at starte og køre en tjeneste-strukturen programmet. En diff pakken indeholder kun de filer, der ændres mellem den sidste klargøring og den aktuelle opgradering, samt fuld programmanifestet og tjenesten manifestet filer. En reference i progammanifest eller tjenestemanifestet, der ikke kan findes i layoutet build er søger efter i store billede.

Fuld programpakker der kræves til den første installation af et program til klyngen. Efterfølgende opdateringer kan være enten en fuld programpakke eller en diff pakke.

Lejligheder ved hjælp af en pakke diff ville være et godt valg:

* En diff pakke er foretrukne, når du har en stor programpakke, der refererer til flere service manifestfiler og/eller flere kode pakker, config pakker eller datapakker.

* En diff pakke er foretrukne, når du har et installation system, som genererer layoutet build direkte fra din build ansøgningsprocedure. I dette tilfælde, selvom koden er ændret, få en anden kontrolsum nyligt indbygget assemblies. Ved hjælp af en komplet programpakke kræver du opdaterer versionen på alle kode pakker. Ved hjælp af en diff pakke, giver du kun de filer, ændret og manifestfilerne hvor versionen er ændret.

Når et program opgraderes ved hjælp af Visual Studio, udgives pakken diff automatisk. Hvis du vil oprette en diff pakke manuelt, programmanifestet og service-manifester skal opdateres, men kun de ændrede pakker skal medtages i den endelige programpakke. 

For eksempel Lad os starte med følgende program (version tal fastsatte nemmere at forstå):

```text
app1        1.0.0
  service1  1.0.0
    code    1.0.0
    config  1.0.0
  service2  1.0.0
    code    1.0.0
    config  1.0.0
```

Nu, Lad os antage, du vil opdatere kun kode pakken af onsiteservice1 ved hjælp af en diff pakke via PowerShell. Nu har opdateret programmet følgende mappestrukturen:

```text
app1        2.0.0      <-- new version
  service1  2.0.0      <-- new version
    code    2.0.0      <-- new version
    config  1.0.0
  service2  1.0.0
    code    1.0.0
    config  1.0.0
```

I dette tilfælde skal opdatere du programmanifestet 2.0.0 og tjenestemanifestet for onsiteservice1 til at afspejle den kode pakke opdatering. Mappen til din programpakke ville have følgende struktur:

```text
app1/
  service1/
    code/
```

## <a name="next-steps"></a>Næste trin

[Opgradering af dit program ved hjælp af Visual Studio](service-fabric-application-upgrade-tutorial.md) vejleder dig gennem programmet opgraderes ved hjælp af Visual Studio.

[Opgradering af dit program ved hjælp af Powershell](service-fabric-application-upgrade-tutorial-powershell.md) vejleder dig gennem programmet opgraderes ved hjælp af PowerShell.

Styre, hvordan dit program opgraderer ved hjælp af [Opgradere parametre](service-fabric-application-upgrade-parameters.md).

Gøre dit program opgraderinger kompatibel ved at lære, hvordan du bruger [Dataserialisering](service-fabric-application-upgrade-data-serialization.md).

Rette almindelige fejl i programmet opgraderinger ved at referere til trinnene i [Foretage fejlfinding af programmet opgraderinger](service-fabric-application-upgrade-troubleshooting.md).
 
