<properties
   pageTitle="Mulighed for at teste: Service kommunikation | Microsoft Azure"
   description="Tjeneste til kommunikation er en kritiske integration punkt i et Service-strukturen til computeren. I denne artikel beskrives Designovervejelser og test teknikker."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/06/2016"
   ms.author="vturecek"/>

# <a name="service-fabric-testability-scenarios-service-communication"></a>Tjenesten strukturen mulighed for at teste scenarier: Service kommunikation

Microservices og service-rundt arkitektonisk typografier overflade naturligt i Azure Service struktur. I denne type fordelt arkitekturer, componentized microservice programmer er som regel består af flere tjenester, der har brug for at tale med hinanden. I selv nemmeste tilfælde har du normalt mindst en uden status webtjeneste og en med høj sikkerhed datatjeneste til lagerplads, skal bruge til at kommunikere.

Tjeneste til kommunikation er en kritiske integration punkt i et program, da hver tjeneste Fremviser en remote API til andre tjenester. Arbejde med en række API grænser, der involverer I/O generelt kræver nogle omtanke, med en god mængde test og validering.

Der er flere overveje at gøre, når disse service grænser wired sammen i et distribueret system:

 - *Transport protocol*. Vil du bruge HTTP for øget interoperabilitet eller en brugerdefineret binære protokol til maksimale antal gennemløb?
 - *Fejlhåndtering*. Hvordan permanente og midlertidige fejl skal håndteres? Hvad der sker, når en tjeneste flyttes til en anden node?
 - *Timeout og ventetid*. I modulopdelte programmer, hvordan hvert lag, tjenesten håndterer ventetid stablen igennem og til brugeren?

Om du bruger en af de indbyggede service kommunikationskomponenter, der leveres af tjenesten strukturen, eller du oprette dine egne, test interaktioner mellem dine tjenester er meget vigtigt at sikre fleksibilitet i dit program.

## <a name="prepare-for-services-to-move"></a>Forberede services til at flytte

Forekomster af tjenesten kan flytte rundt med tiden. Dette er tilfældet, når de er konfigureret med Indlæs måleredskaber for justering af brugerdefineret skræddersyet optimal ressource. Tjenesten strukturen flytter din forekomster af tjenesten til at maksimere deres tilgængelighed selv under opgraderinger, failover, skala ud og andre situationer, der forekommer i et distribueret system levetid.

Som services flytte rundt i klyngen, skal være forberedt dine kunder og andre tjenester til at håndtere to scenarier, når de tale til en tjeneste:

- Tjenesten forekomst eller partition replika er flyttet siden sidste gang, du har talt med den. Dette er en normal del af en tjeneste livscyklus, og det skal forventes, at sker under levetiden for dit program.
- Tjenesten forekomst eller partition replika er ved at flytte. Selvom der opstår meget hurtigt failover til en tjeneste fra en node til en anden i tjenesten struktur, kan der være en forsinkelse i tilgængelighed Hvis kommunikationskomponenten af din tjeneste er længe om at starte.

Håndtering af disse scenarier problemfrit er vigtigt for et jævne kører system. Hvis du vil gøre det, du være opmærksom på:

- Hver tjeneste, som kan være forbundet med har en *adresse* , som den lytter på (for eksempel HTTP eller WebSockets). Når en forekomst af tjenesten eller partition, flytter, ændres dens adresse slutpunkt. (Den flyttes til en anden node med en anden IP-adresse.) Hvis du bruger de indbyggede kommunikationskomponenter, håndterer de igen fortolkning af service-adresser for dig.
- Der kan være en midlertidig stigning i tjenesten ventetid som tjenesten forekomst starter op dens lytteren igen. Dette afhænger af, hvor hurtigt tjenesten åbnes lytteren, når forekomsten af flyttes.
- En hvilken som helst eksisterende forbindelser skal være lukkes og åbnes igen, når tjenesten åbnes på en ny node. En korrekt node lukning eller genstart giver tid til eksisterende forbindelser kan lukkes problemfrit.

### <a name="test-it-move-service-instances"></a>Tester den: flytte forekomster af tjenesten

Ved hjælp af tjenesten strukturen mulighed for at teste værktøjer, opretter du et scenarie med test for at teste disse situationer på forskellige måder:

1. Flytte en med høj sikkerhed tjeneste primære replika.

    Den primære kopi af en med høj sikkerhed service partition kan flyttes til en hvilken som helst antal årsager. Brug dette til at målrette den primære kopi af en bestemt partition at se, hvordan dine tjenester reagere på Flyt på en meget kontrolleret måde.

    ```powershell

    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService

    ```

2. Stoppe en node.

    Når en node er stoppet, flyttes Service-strukturen alle forekomster af tjenesten eller partitioner, der var på noden til en af de andre tilgængelige noder i klyngen. Brug dette til at afprøve en situation, hvor en node går tabt i din klynge og alle forekomster af tjenesten og replikaer på noden nødt til at flytte.

    Du kan forhindre en node ved hjælp af PowerShell **Stop ServiceFabricNode** cmdlet:

    ```powershell

    PS > Restart-ServiceFabricNode -NodeName Node_1

    ```

## <a name="maintain-service-availability"></a>Vedligeholde tjenesten kører

Som en platform er Service-strukturen udviklet til at sikre høj tilgængelighed for dine tjenester. Men i værste fald underliggende infrastruktur problemer stadig kan medføre utilgængelighed. Det er vigtigt at teste for disse scenarier for.

Med høj sikkerhed services Brug et quorum-baseret system til at gentage tilstanden for høj tilgængelighed. Det betyder, at en quorum replikaer skal være tilgængelig til at udføre skrivning. I sjældne tilfælde, som en omfattende hardware mislykket beslutningsdygtigt replikaer muligvis ikke tilgængelig. Du kan ikke udføre skrivehandlinger i disse tilfælde, men du vil stadig kunne udføre Læs handlinger.

### <a name="test-it-write-operation-unavailability"></a>Tester den: skrive handlingen utilgængelighed

Ved hjælp af værktøjerne mulighed for at teste i tjenesten strukturen, kan du indsætte en fejl, der Bæltematerialets quorum tab som en test. Men Sådan et scenario er sjældne, er det vigtigt, at klienter og tjenester, der er afhænger af en med høj sikkerhed tjeneste er parat til at håndtere situationer, hvor de ikke kan foretage skrive anmodninger til den. Det er også vigtigt, at tjenesten med høj sikkerhed selve er bekendt med denne mulighed og problemfrit kan kommunikere den til ringer.

Du kan forårsage quorum tab ved hjælp af PowerShell **Aktiver ServiceFabricPartitionQuorumLoss** cmdlet:

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

I dette eksempel skal vi konfigurere `QuorumLossMode` til `QuorumReplicas` til at angive, at vi vil forårsage quorum tab uden at foretage dig ned alle kopier. Denne måde, Læs handlinger er stadig muligt. For at teste et scenarie, hvor en hel partition er ikke tilgængelig, kan du angive denne parameter til `AllReplicas`.

## <a name="next-steps"></a>Næste trin

[Lær mere om mulighed for at teste handlinger](service-fabric-testability-actions.md)

[Lær mere om mulighed for at teste scenarier](service-fabric-testability-scenarios.md)
