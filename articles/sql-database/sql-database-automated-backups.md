<properties
   pageTitle="SQL-Database sikkerhedskopier - automatisk, geografisk overflødige | Microsoft Azure" 
   description="SQL-Database opretter en lokal database sikkerhedskopi hver fem minutter og bruger Azure-læseadgang geografisk overflødige lagerplads (RA-GRS) til at give geografisk redundans automatisk. "
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/20/2016"
   ms.author="carlrab;barbkess"/>

<!------------------
This topic is annotated with TEMPLATE guidelines for FEATURE TOPICS.


Metadata guidelines

pageTitle
    60 characters or less. Includes name of the feature - primary benefit. Not the same as H1. Its 60 characters or fewer including all characters between the quotes and the Microsoft Azure site identifier.

description
    115-145 characters. Duplicate of the first sentence in the introduction. This is the abstract of the article that displays under the title when searching in Bing or Google. 

    Example: "SQL Database automatically creates a local database backup every few minutes and uses Azure read-access geo-redundant storage for geo-redundancy."
------------------>

<!----------------

TEMPLATE GUIDELINES for feature topics

The Feature Topic is a one-pager (ok, sometimes longer) that explains a capability of the product or service. It explains what the capability is and characteristics of the capability.  

It is a "learning" topic, not an action topic.

DO explain this:
    • Definition of the feature terminology.  i.e., What is a database backup?
    • Characteristics and capabilities of the feature. (How the feature works)
    • Common uses with links to overview topics that recommend when to use the feature.
    • Reference specifications (Limitations and Restrictions, Permissions, General Remarks, etc.)
    • Next Steps with links to related overviews, features, and tasks.

DON'T explain this:
    • How to steps for using the feature (Tasks)
    • How to solve business problems that incorporate the feature (Overviews)
------------------->

<!------------------
GUIDELINES for the H1 
    
    The H1 should answer the question "What is in this topic?" Write the H1 heading in conversational language and use search key words as much as possible. Since this is a learning topic, make sure the title indicates that and doesn't mislead people to think this will tell them how to do tasks.  
    
    To help people understand this is a learning topic and not an action topic, start the title with "Learn about ... "

    Heading must use an industry standard term. If your feature is a proprietary name like "Elastic database pools", use a synonym. For example:    "Learn about elastic database pools for multi-tenant databases". In this case multi-tenant database is the industry-standard term that will be an anchor for finding the topic.

-------------------->

# <a name="learn-about-sql-database-backups"></a>Få mere at vide om SQL-Database sikkerhedskopier

<!------------------
    GUIDELINES for introduction
    
    The introduction is 1-2 sentences.  It is optimized for search and sets proper expectations about what to expect in the article. It should contain the top key words that you are using throughout the article.The introduction should be brief and to the point of what the feature is, what it is used for, and what's in the article. 

    If the introduction is short enough, your article can pop to the top in Google Instant Answers.

    In this example:
    
 

Sentence #1 Explains what the article will cover, which is what the feature is or does. This is also the metadata description. 
    SQL Database automatically creates a local database backup every five minutes and uses Azure read-access geo-redundant storage (RA-GRS) to provide geo-redundancy. 

Sentence #2 Explains why I should care about this.  
    Database backups are an essential part of any business continuity and disaster recovery strategy because they protect your data from accidental corruption or deletion.

-------------------->

SQL-Database automatisk opretter en lokal database sikkerhedskopi alle par minutter og bruger Azure-læseadgang geografisk overflødige lagerplads til geografisk redundans. Sikkerhedskopier af databasen er en vigtig del af en hvilken som helst business løbende og genoprettelse efter genoprettelsesstrategi, fordi de beskytte dine data mod utilsigtet beskadigelse eller sletning. 

<!-- This image needs work, so not putting it in right now.

This diagram shows SQL Database running in the US East region. It creates a database backup every five minutes, which it stores locally to Azure Read Access Geo-redundant Storage (RA-GRS). Azure uses geo-replication to copy the database backups to a paired data center in the US West region.

![geo-restore](./media/sql-database-geo-restore/geo-restore-1.png)

-->

<!---------------
GUIDELINES for the first ## H2.

    The first ## describes what the feature encompasses and how it is used. It points to related task articles.
    
    For consistency, being the heading with "What is ... "
----------------->

## <a name="what-is-a-sql-database-backup"></a>Hvad er en SQL-Database sikkerhedskopi?  

<!-- 
    Explains what a SQL Database backup is and answers an important question that people want to know.
-->

En SQL-Database sikkerhedskopi omfatter både lokale databasesikkerhedskopier og geografisk overflødige sikkerhedskopier. Disse sikkerhedskopier er oprettet automatisk og gratis. Du behøver ikke at gøre noget for at gøre dem sker.

<!----------------- 
    Explains first component of the backup feature
------------------>

Efter lokale sikkerhedskopier bruger SQL-Database SQL Server-teknologi til at oprette [fuld](https://msdn.microsoft.com/library/ms186289.aspx), [ændret](https://msdn.microsoft.com/library/ms175526.aspx )og [transaktionslog](https://msdn.microsoft.com/library/ms191429.aspx) sikkerhedskopier. Transaktions logsikkerhedskopier ske hver fem minutter, hvor du kan gøre en punkt-in-time gendannelse til den samme server, der er vært databasen. Når du gendanner en database, tal tjenesten ud af, hvilke fuld forskellen og transaktion log sikkerhedskopier skal gendannes.

<!--------------- 
    Explicit list of what to do with a local backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

Bruge en lokal database sikkerhedskopi til:

- Gendanne en database til et punkt-in-time inden for opbevaringsperioden. Du kan gendanne en database til et punkt-in-time med en sikkerhedskopi af databasen, gendanne en slettet database til den tid, det er blevet slettet, eller gendanne en database til en anden geografisk område. For at udføre en gendannelse, skal du se [gendanne en database fra en sikkerhedskopi af databasen](sql-database-recovery-using-backups.md).

- Kopiere en database til en SQL server i det samme eller forskellige område. Kopiér er en transaktion overensstemmelse med den aktuelle SQL-Database. Få vist [databasekopi](sql-database-copy.md)for at udføre en kopi.

- Arkivere en sikkerhedskopi af databasen ud over sikkerhedskopiering opbevaringsperioden. Til at udføre et arkiv, [eksportere en SQL-database til en BACPAC](sql-database-export.md) filen. Du kan derefter arkivere BACPAC til længerevarende opbevaring og gemme den ud over dit opbevaringsperiode. Eller brug BACPAC for at overføre en kopi af databasen til SQL Server, enten lokalt eller i en Azure virtual machine (VM).

<!----------------- 
    Explains first component of the backup feature
------------------>

Efter geografisk overflødige sikkerhedskopier bruger SQL-Database [Azure-lager gentagelse](../storage/storage-redundancy.md). SQL-Database gemmer lokale database sikkerhedskopifilerne med en konto med [Læseadgang geografisk overflødige lagerplads (RA-GRS)](../storage/storage-redundancy.md#read-access-geo-redundant-storage) . Azure kopieres sikkerhedskopifilerne til en [parvis datacenter](../best-practices-availability-paired-regions.md). 

<!--------------- 
    Explicit list of what to do with a geo-redundant backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

Bruge en geografisk overflødige sikkerhedskopi til:

- Gendanne en database til et andet geografisk område i tilfælde af, at du ikke kan få adgang til sikkerhedskopien af databasen fra din primære databaseområde. 

>[AZURE.NOTE] Azure-lager refererer ordet *Gentagelse* til at kopiere filer fra én placering til en anden. SQLS *databasereplikering* refererer til at holde flere sekundære databaser, der er synkroniseret med en primær database. 

<!----------------
    The next ## H2's discuss key characteristics of how the feature works. The title is in conversational language and asks the question that will be answered.
------------------->
## <a name="how-much-backup-storage-is-included-at-no-cost"></a>Hvor meget ekstra lagerplads er inkluderet i gratis?

SQL-Database indeholder op til 200% af den maksimale klargjorte database lagerplads som ekstra lagerplads gratis. Eksempelvis hvis du har en forekomst af Standard DB med en klargjort DB størrelse på 250 GB, har du 500 GB ekstra lagerplads gratis. Hvis databasen overstiger den medfølgende ekstra lagerplads, kan du vælge at reducere opbevaringsperioden ved at kontakte Azure Support. En anden mulighed er at betale for ekstra ekstra lagerplads, der faktureres til standardsats læseadgang geografisk overflødige lagerplads (RA-GRS). 

## <a name="how-often-do-backups-happen"></a>Hvor ofte sker sikkerhedskopier?

Efter lokale databasesikkerhedskopier ske komplette sikkerhedskopier ugentligt forskelle databasesikkerhedskopier udføres hver time og transaktionslog sikkerhedskopier sker alle fem minutter. Den første komplette sikkerhedskopi er planlagt, umiddelbart efter at der oprettes en database. Det som regel afsluttes inden for 30 minutter, men det kan tage længere tid, når databasen er af en betydeligt størrelse. Den første sikkerhedskopi kan for eksempel tage længere tid på en gendannede database eller en databasekopi. Efter den første komplette sikkerhedskopi planlagt automatisk alle yderligere sikkerhedskopier og administreres automatisk i baggrunden. Den nøjagtige tidsindstilling for fuld og [forskelle](https://msdn.microsoft.com/library/ms175526.aspx) databasesikkerhedskopier bestemmes som den afbalancerer overordnede arbejdsbelastningen. 

Efter geografisk overflødige sikkerhedskopier kopieres fuld og forskelle sikkerhedskopier ifølge Azure-lager gentagelse tidsplanen.

## <a name="how-long-do-you-keep-my-backups"></a>Hvor længe kan du placere min sikkerhedskopier?

Hver sikkerhedskopiering af SQL-Database har en opbevaringsperiode, der er baseret på det [webtjenesten](sql-database-service-tiers.md) af databasen. Opbevaringsperiode til en database i den:

<!------------------

    Using a list so the information is easy to find when scanning.
------------------->

- Grundlæggende webtjenesten er syv dage.
- Standard webtjenesten er 35 dage.
- Premium webtjenesten er 35 dage.


Hvis du nedgradere databasen fra Standard eller Premium service lag til grundlæggende, gemmes sikkerhedskopierne for syv dage. Alle eksisterende sikkerhedskopier, der er ældre end syv dage er ikke længere tilgængelige. 

Hvis du opgraderer din database fra den grundlæggende webtjenesten til Standard eller Premium, bevarer SQL-Database eksisterende sikkerhedskopier, indtil de er 35 dage gamle. Den bevarer ny sikkerhedskopier, efterhånden som de opstår i 35 dage.
 
Hvis du sletter en database, bevarer SQL-Database på sikkerhedskopier på samme måde i forbindelse med en onlinedatabase. Lad os antage, at du sletter en grundlæggende database, der har en opbevaringsperiode af syv dage. En sikkerhedskopi, der er fire dage gammel er gemt i tre flere dage.

>[AZURE.IMPORTANT]
    Hvis du sletter Azure SQL-server, der er vært SQL-databaser, slettes også alle databaser, der hører til serveren, og du kan ikke gendannes. Du kan ikke gendanne en slettet server.

<!-------------------
OPTIONAL section
## Best practices 
--------------------->

<!-------------------
OPTIONAL section
## General remarks
--------------------->

<!-------------------
OPTIONAL section
## Limitations and restrictions
--------------------->

<!-------------------
OPTIONAL section
## Metadata
--------------------->

<!-------------------
OPTIONAL section
## Performance
--------------------->

<!-------------------
OPTIONAL section
## Permissions
--------------------->

<!-------------------
OPTIONAL section
## Security
--------------------->

<!-------------------
GUIDELINES for Next Steps

    The last section is Next Steps. Give a next step that would be relevant to the customer after they have learned about the feature and the tasks associated with it.  Perhaps point them to one or two key scenarios that use this feature.

    You don't need to repeat links you have already given them.
--------------------->

## <a name="next-steps"></a>Næste trin

Sikkerhedskopier af databasen er en vigtig del af en hvilken som helst business løbende og genoprettelse efter genoprettelsesstrategi, fordi de beskytte dine data mod utilsigtet beskadigelse eller sletning. Se hvordan Webdatabase sikkerhedskopier i en bredere strategi, se [Oversigt over Business løbende](sql-database-business-continuity.md).


