<properties 
    pageTitle="Bruge gendannelse Manager til at løse problemer med shard kort over | Microsoft Azure" 
    description="Bruge klassen RecoveryManager til at løse problemer med shard kort" 
    services="sql-database" 
    documentationCenter=""  
    manager="jhubbard"
    authors="ddove"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/05/2016" 
    ms.author="ddove"/>

# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Brug af klassen RecoveryManager til at løse problemer med shard kort

Klassen [RecoveryManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.aspx) giver ADO.Net programmer mulighed for at lettere at finde og rette eventuelle uoverensstemmelser mellem globale shard kortet (GSM) og lokale shard kortet (LSM) i en delt database-miljøet. 

GSM og LSM at spore tilknytningen af hver database i et delt miljø. Nogle gange kan forekommer et sideskift mellem GSM og LSM. Brug i så fald klassen RecoveryManager til at registrere og reparere sideskift.

Klassen RecoveryManager er en del af [elastiske Database klientbibliotek](sql-database-elastic-database-client-library.md). 


![Shard kort][1]


Du kan finde ord definitioner, [elastiske databasen værktøjer ordliste](sql-database-elastic-scale-glossary.md). For at forstå, hvordan **ShardMapManager** bruges til at administrere data i en delt løsning, skal du se [Shard tilknytte administration](sql-database-elastic-scale-shard-map-management.md).


## <a name="why-use-the-recovery-manager"></a>Hvorfor bruge Styring af gendannelse?

I en delt database-miljø er der én lejer per database og mange databaser per server. Der kan også være mange servere i miljøet. Hver database er tilknyttet i shard kortet, så opkald kan også sendes til den korrekte server og database. Databaser, registreres i overensstemmelse med en **sharding nøgle**, og hver shard er tildelt et **område af nøgleværdier**. For eksempel kan en sharding nøgle repræsentere kundenavne fra "D" til "F" Tilknytning af alle shards (også kendt-databaser) og deres tilknytning områder er indeholdt i den **globale shard kort (GSM)**. Hver database indeholder også en oversigt over de områder, der er indeholdt i shard – dette kaldes **lokale shard tilknytte (LSM)**. Når en app opretter forbindelse til en shard, cachelagrede tilknytningen til appen til hurtig hentning. LSM bruges til at validere cachelagrede data. 

GSM og LSM kan blive synkroniseret af følgende årsager:

1. Sletning af en shard, hvis området ser ikke længere i brug eller omdøbe en shard. Slette et shard medfører en **tabte shard tilknytning**. Similary, en omdøbte database kan medføre en tabte shard tilknytning. Afhængigt af formålet med ændringen, shard kan være nødvendigt at blive fjernet eller shard placering skal opdateres. Hvis du vil gendanne en slettet database, skal du se [gendanne en database til et tidligere tidspunkt, gendanne en slettet database eller gendanne fra en data center afbrydelse](sql-database-troubleshoot-backup-and-restore.md).
2. En geografisk failover hændelse indtræffer. Hvis du vil fortsætte, skal en opdatere servernavn og databasenavn af shard kort manager i programmet på computeren og derefter opdatere shard tilknytningsdetaljerne for alle shards i et shard kort. I tilfælde af en geografisk-failover, skal være automatisk sådanne gendannelse logik i arbejdsprocessen failover. Automatisere løsninger gør det muligt for en frictionless administration til geografisk-kompatible databaser og undgår manuel human handlinger.
3. En shard eller ShardMapManager databasen gendannes til en tidligere punkt-tid.

Du kan finde flere oplysninger om Azure SQL Database elastiske Databaseværktøjer geografisk replikering og gendannelse, skal du se følgende: 

* [Oversigt: Skyen business løbende og database nedbrud med SQL-Database](sql-database-business-continuity.md) 
* [Komme i gang med elastiske Databaseværktøjer](sql-database-elastic-scale-get-started.md)  
* [Administration af ShardMap](sql-database-elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>Henter RecoveryManager fra en ShardMapManager 

Det første trin er at oprette en forekomst af RecoveryManager. [GetRecoveryManager metode](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager.aspx) returnerer gendannelse manager til den aktuelle forekomst af [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) . For at løse eventuelle uoverensstemmelser i shard kortet, skal du først hente RecoveryManager til bestemt shard kortet. 

    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager(); 

I dette eksempel er RecoveryManager initialiseret fra ShardMapManager. Den ShardMapManager, der indeholder en ShardMap er også allerede initialiseret. 

Da denne programkode manipulerer shard kortet selve, skal de legitimationsoplysninger, der bruges i metoden factory (i eksemplet ovenfor smmConnectionString) legitimationsoplysninger, der har læse / skrive-tilladelser på den GSM database, der refereres til af forbindelsesstrengen. Disse legitimationsoplysninger er typisk forskellige fra de legitimationsoplysninger, der bruges til at åbne forbindelser til distribution af data-afhængige. Du kan finde yderligere oplysninger [ved hjælp af legitimationsoplysninger i elastiske database-klienten](sql-database-elastic-scale-manage-credentials.md).

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>Fjerne en shard fra ShardMap, når en shard er blevet slettet

Den [DetachShard metode](https://msdn.microsoft.com/library/azure/dn842083.aspx) afbryder forbindelsen til den angivne shard fra shard kortet og sletter tilknytninger, der er knyttet til shard.  

* Placeringsparameteren er sted, shard, servernavn og databasenavn af shard blive fjernet. 
* Parameteren shardMapName er shard kort navn. Denne indstilling er kun nødvendigt, når flere shard kort administreres af samme shard kort manager. Valgfrit. 

**Vigtigt**: bruge denne teknik, kun hvis du er sikker på, at området for opdaterede tilknytningen er tom. Disse metoder kontrollerer ikke data til det område, der skal flyttes, så det er bedst at medtage Kontroller i din kode.

I dette eksempel fjerner shards fra shard kortet. 

    rm.DetachShard(s.Location, customerMap); 

Kortet shard placering i GSM før sletning af shard. Da shard er blevet slettet, forudsættes det var bevidst og sharding vigtige området er ikke længere i brug. Hvis dette ikke er tilfældet, kan du udføre punkt i tid Gendan. til at gendanne shard fra en tidligere punkt i gang. (I så fald se afsnittet nedenfor til at registrere shard uoverensstemmelser.) For at gendanne skal du se [gendanne en database til et tidligere tidspunkt, gendanne en slettet database eller gendanne fra en data center afbrydelse](sql-database-troubleshoot-backup-and-restore.md).

Da forudsættes database sletningen er bevidst, er handlingen endelige administrative Diskoprydning til at slette posten, der skal shard i shard kort manager. Dette forhindrer programmet i at skrive oplysninger til et område der ikke forventes ved et uheld.

## <a name="to-detect-mapping-differences"></a>Til at registrere tilknytning forskelle 

Den [DetectMappingDifferences metode](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences.aspx) vælger og returnerer en af de shard kort (enten lokalt eller global) som kilde for sandheden og afstemmer tilknytninger på begge shard kort (GSM og LSM).

    rm.DetectMappingDifferences(location, shardMapName);

* *Placering* angiver det servernavn og databasenavn. 
* Parameteren *shardMapName* er shard kort navn. Denne indstilling er kun påkrævet, hvis flere shard kort administreres af samme shard kort manager. Valgfrit. 

## <a name="to-resolve-mapping-differences"></a>Til at løse tilknytning forskelle

Den [ResolveMappingDifferences metode](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences.aspx) markerer en af de shard kort (enten lokalt eller global) som kilde for sandheden og afstemmer tilknytninger på begge shard kort (GSM og LSM).

    ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   
* Parameteren *RecoveryToken* optælles forskelle i sektionen tilknytninger mellem GSM og LSM for den specifikke shard. 

* [MappingDifferenceResolution optælling, der](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution.aspx) bruges til at angive metoden til løsning af forskellen mellem shard tilknytningerne. 
* **MappingDifferenceResolution.KeepShardMapping** anbefales i tilfælde af, at LSM indeholder nøjagtige tilknytningen og derfor tilknytning i shard skal bruges. Det er typisk tilfældet i tilfælde af en failover: shard nu er placeret på en ny server. Da shard skal først fjernes fra GSM (ved hjælp af metoden RecoveryManager.DetachShard), findes en tilknytning ikke længere på GSM. Derfor på LSM skal bruges til at genoprette shard tilknytning.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Vedhæfte en shard til ShardMap, når en shard er gendannet 

Den [AttachShard metode](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard.aspx) vedhæfter den angivne shard shard kortet. Det derefter registrerer uoverensstemmelser shard kort og opdaterer tilknytningerne så det svarer til shard på det pågældende shard gendannelsen. Det antages, at databasen også omdøbes afspejler det oprindelige databasenavn (før når shard blev gendannet), siden punkt på tidspunktet for gendannelse standarder til en ny database, der er føjet til tidsstemplet. 

    rm.AttachShard(location, shardMapName) 

* Parameteren *placering* er det servernavn og databasenavn, af shard tilknyttes. 

* Parameteren *shardMapName* er shard kort navn. Denne indstilling er kun nødvendigt, når flere shard kort administreres af samme shard kort manager. Valgfrit. 

I dette eksempel føjer en shard til shard kortet som for nylig er blevet genoprettet fra en tidligere punkt-tid. Shard (nemlig tilknytning til shard i LSM) er blevet gendannet, og det er derfor potentielt ikke stemmer overens med posten shard i GSM. Shard blev gendannet og omdøbt til det oprindelige navn på databasen uden for denne eksempelkode. Da den blev gendannet, forudsættes det tilknytning i LSM er den-tilknytning, der er tillid til. 

    rm.AttachShard(s.Location, customerMap); 
    var gs = rm.DetectMappingDifferences(s.Location); 
      foreach (RecoveryToken g in gs) 
       { 
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping); 
       } 

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>Opdaterer shard placeringer efter en geografisk-failover (Gendan) af shards

I tilfælde af en geografisk failover sekundær databasen foretages skrive handicapvenlige og bliver den nye primære database. Navnet på serveren, og potentielt database (afhængigt af din konfiguration), kan være forskellige fra den oprindelige primære. Derfor skal tilknytning posterne til shard i GSM og LSM rettes. På samme måde, hvis databasen er gendannet til et andet navn eller placering eller til et tidligere tidspunkt, dette kan medføre uoverensstemmelser shard kort. Shard kort Manager håndterer fordelingen af Åbn forbindelser til den korrekte database. Fordeling baseret på dataene i shard kortet og værdien af nøglen sharding, som er mål for anmodning om programmets. Efter en geografisk failover skal disse oplysninger opdateres med nøjagtige servernavn, databasenavnet og shard tilknytningen af gendannet databasen. 

## <a name="best-practices"></a>Bedste fremgangsmåder

Geografisk failover og gendannelse er handlinger, der typisk administreres af en sky administrator af programmet bevidst benytte en af Azure SQL-databaser business løbende funktioner. Planlægning af Business løbende kræver processer, procedurer og foranstaltninger for at sikre, at business handlinger kan fortsætte uden forstyrrelser. De tilgængelige metoder baseret som en del af klassen RecoveryManager skal bruges i denne arbejdsgang til at sikre GSM og LSM holdes opdaterede på den handling, gendannelse. Der er 5 grundlæggende trin til korrekt at sikre GSM og LSM afspejler de nøjagtige oplysninger efter en failover begivenhed. Programkode til at udføre disse trin kan integreres i eksisterende værktøjer og arbejdsprocessen. 

1. Hent RecoveryManager fra ShardMapManager. 
2. Fjerne det gamle shard fra shard kortet.
3. Vedhæfte den nye shard til shard kortet, herunder den nye shard placering.
4. Registrere uoverensstemmelser i tilknytningen mellem GSM og LSM. 
5. Løse forskelle mellem GSM og LSM, have tillid til LSM. 

I dette eksempel udfører følgende trin:
1. Fjerner shards fra Shard kortet som afspejler shard placeringer før begivenheden failover.
2. Vedhæfter shards Shard kortet afspejler de nye shard placeringer (parameteren "Configuration.SecondaryServer" er det nye servernavn, men det samme navn på databasen).
3. Henter gendannelse tokens efter registrering af tilknytning forskelle mellem GSM og LSM for hver shard. 
4. Løser uoverensstemmelser ved at have tillid til tilknytning fra LSM af hver shard. 

    varians shards = smm. GetShards();  foreach (shard s i shards) {Hvis (s.Location.Server == Configuration.PrimaryServer) {ShardLocation slNew = nye ShardLocation (Configuration.SecondaryServer, s.Location.Database) 
        
          rm.DetachShard(s.Location); 
        
          rm.AttachShard(slNew); 
        
          var gs = rm.DetectMappingDifferences(slNew); 
    
          foreach (RecoveryToken g in gs) 
            { 
               rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping); 
            } 
        } 
    } 



[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
[1]: ./media/sql-database-elastic-database-recovery-manager/recovery-manager.png
 
