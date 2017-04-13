<properties
   pageTitle="Azure SQL Database internettet og Business Edition sunset ofte stillede spørgsmål om | Microsoft Azure"
   description="Find ud af, når Azure SQL Web og Business databaserne skal udgå og få mere at vide om funktioner og funktionalitet i de nye tjeneste niveauer."
   services="sql-database"
   documentationCenter="na"
   authors="stevestein"
   manager="jhubbard"
   editor="monicar" />
<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/08/2016"
   ms.author="sstein" />

# <a name="web-and-business-edition-sunset-faq"></a>Internettet og Business Edition sunset ofte stillede spørgsmål

Azure SQL Web og Business databaser er nu går på pension. De grundlæggende, Standard, Premium og elastiske lag Erstat retiring Web og Business databaserne.

For at hjælpe dig med at opgradere Web og Business databaser, anbefaler tjenesten SQL-Database relevante service niveau og ydeevne niveauet (priser niveau) for hver database, der er baseret på den historiske arbejdsbyrde.

**Til at få priser niveau anbefalinger:**

- [Opgradering til version 12 SQL-Database ved hjælp af portalen Azure](sql-database-upgrade-server-portal.md)
- [Opgradering til version 12 SQL-Database ved hjælp af PowerShell](sql-database-upgrade-server-powershell.md)
- [Ændre det priser niveau i en Web- eller Business-database](sql-database-service-tier-advisor.md)



## <a name="why-does-the-azure-portal-show-my-web-and-business-edition-databases-as-retired"></a>Hvorfor vises Mine Web og Business edition databaser som går på pension i portalen Azure?

Da Web og Business edition databaser ikke vil være tilgængelige efter September 2015, etiketter på portalen Web og Business databaser som går på pension. Udgåede etiketten indeholder også en påmindelse om, at nogen Web og Business databaser skal opgraderes til Standard, Basic eller Premium. Se [opgradere til version 12 Azure SQL-Database](sql-database-upgrade-server-portal.md)kan finde detaljerede oplysninger om opgradering eksisterende Web- eller Business-databaser til de nye tjeneste niveauer.

## <a name="which-new-service-tier-is-the-best-choice-to-upgrade-my-existing-web-or-business-database-to"></a>Hvilke nye webtjenesten er den bedste løsning at opgradere min eksisterende Web- eller Business-database til?

Valg af et passende ny tjeneste niveau og ydeevne niveau for den eksisterende Web- eller Business-database, afhænger af bestemte funktioner og ydeevne kravene til dit program.

Brug de priser niveau anbefalinger, der er beskrevet ovenfor, eller du kan finde detaljerede oplysninger til at hjælpe dig med at vælge en relevant nye webtjenesten, under [opgradering til version 12 Azure SQL-Database](sql-database-upgrade-server-portal.md).

## <a name="why-is-microsoft-introducing-new-service-tiers"></a>Hvorfor introducerer Microsoft nye service niveauer?

Baseret på kundefeedback introducerer Azure SQL-Database nye service niveauer for at hjælpe kunder med mere nemt understøtter relationsdatabase arbejdsmængder. De nye lag er designet til at levere forudsigelige ydeevne på tværs af en spektret af syv niveauer for nedtonet til aktiveret transaktions programmet behov. Desuden tilbyder de nye lag en række business løbende funktioner en stærkere oppetid SLA større database til færre penge og en forbedret fakturering oplevelse.

## <a name="where-can-i-learn-more-about-the-new-service-tiers"></a>Hvor kan jeg få mere at vide om de nye service lag?

Du kan finde detaljerede oplysninger om den nye tjeneste niveauer og ydeevne model [Service niveauer](sql-database-service-tiers.md). Detaljerede prisoplysninger for de nye tjeneste niveauer i [SQL-Database priser](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="what-features-or-functionality-will-not-be-available-in-basic-standard-and-premium"></a>Hvilke funktioner eller funktionalitet er ikke tilgængelige i Basic, Standard og Premium?

Funktionen oprette samlinger skal udgå med internettet og Business udgaver. Kunder, der skal skala fra deres database er kommentarruden i stedet bruge eller overføre til [elastiske Databaseværktøjer](sql-database-elastic-scale-get-started.md) til [Azure SQL-Database](sql-database-elastic-scale-get-started.md), som forenkler opbygge og administrere et program, der bruger sharding. Et .NET klientbibliotek kan programmer til at definere, hvordan dataene er tilknyttet på shards og omdirigerer OLTP anmodninger relevante databaser. En skabelon til Azure skybaseret tjeneste er inkluderet, du kan hoste i din egen Azure abonnement for at understøtte management handlinger, som omkonfigurerer, hvor data er distribueret mellem shards. Ud over [elastiske Databaseværktøjer](sql-database-elastic-scale-get-started.md)fortsætter Microsoft med at oprette og publicere [brugerdefinerede sharding mønstre og fremgangsmåder vejledning](https://msdn.microsoft.com/library/azure/dn764977.aspx) baseret på learnings fra deep kunde aftaler. Nye kunder, der skal have Skaler ud funktionalitet skal tjekke [elastiske Databaseværktøjer](sql-database-elastic-scale-get-started.md) og/eller kontakt Microsoft Support for at vurdere deres indstillinger.

Microsoft også ændre database kopi oplevelsen med Premium databaser. Tidligere premium database kvote var begrænset, Opret DATABASE... T-SQL oprettes som en kopi af i en database, suspenderet Premium uden reserverede ressourcer, der blev faktureret med samme hastighed som en Business-database. Kvote for premium er nu mere tilgængeligt, understøttes ikke længere suspenderet Premium. Database Kopier vil nu blive oprettet med de samme udgave og ydeevneniveau som kilde og vil blive faktureret i overensstemmelse hermed. Kunder kan vælge at nedgradere kopierede databaser til en anden tjeneste niveau eller ydeevne niveau at reducere deres omkostninger, hvis du ønsker. Eksisterende suspenderet Premium databaser konverteres til Business edition som en del af denne version. Det forventes, at indførelsen af [Punkt-In-Time gendanne](sql-database-recovery-using-backups.md#point-in-time-restore) vil reducere skal tage sikkerhedskopier af databaser.

## <a name="how-does-basic-standard-and-premium-improve-my-billing-experience"></a>Hvordan Basic, som Standard og Premium forbedre oplevelsen fakturering?

Grundlæggende, Standard, og Premium Azure SQL-databaser er faktureret ved timen, og du har mulighed for at skalere hver database, op eller ned. Du er faktureret med en fast rente, der er baseret på det højeste service niveau og ydeevne niveau du vælger til hver time. Desuden ydeevneniveauer (eksempel: Basic, S1 og P2) er opdelt i faktura til at gøre det nemmere at se antallet af dage/timer du påløbet i en enkelt måned for hvert ydeevneniveau med database. Web og Business databaser fortsætte med at blive faktureret ved hjælp af Database-enheder, der er baseret på databasestørrelse. Besøg den [SQL-Database priser side](https://azure.microsoft.com/pricing/details/sql-database/) for at få flere oplysninger om priser og forskelle mellem de nye tjeneste.


## <a name="see-also"></a>Se også

[Azure SQL-Database](https://azure.microsoft.com/documentation/services/sql-database/)

[Internettet og Business priser](https://azure.microsoft.com/pricing/details/sql-database/web-business/)

[Tjenesten niveauer](sql-database-service-tiers.md)

[Opgradere til version 12 Azure SQL-Database](sql-database-upgrade-server-portal.md)
