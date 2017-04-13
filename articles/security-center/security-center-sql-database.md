<properties
   pageTitle="Azure Sikkerhedscenter og Azure SQL-Database service | Microsoft Azure"
   description="I denne artikel beskrives, hvordan Sikkerhedscenter kan hjælpe dig med at sikre dine databaser i Azure SQL-Database."
   services="sql-database"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-and-azure-sql-database-service"></a>Azure Sikkerhedscenter og Azure SQL-Database

[Azure Sikkerhedscenter](https://azure.microsoft.com/documentation/services/security-center/) hjælper dig med at forhindre, at registrere og besvare trusler. Det giver integreret sikkerhed overvågning og politik for administration på tværs af dine abonnementer på Azure, hjælper med at registrere trusler, der ellers går sig og fungerer med et omfattende netværk af sikkerhedsløsninger.

I denne artikel beskrives, hvordan Sikkerhedscenter kan hjælpe dig med at sikre dine databaser i Azure SQL-Database.

## <a name="why-use-security-center"></a>Hvorfor bruge Sikkerhedscenter?

Sikkerhedscenter hjælper dig med at beskytte data i SQL-Database ved at indsende indsigt i sikkerheden for dine servere og databaser. Med Sikkerhedscenter kan du:

- Definer politikker for SQL-Database kryptering og overvågning.
- Overvåge sikkerheden for SQL-Database ressourcer på tværs af alle dine abonnementer.
- Hurtigt identificere og ret sikkerhedsproblemer med.
- Integrere beskeder fra [Azure SQL-Database truslen registrering](../sql-database/sql-database-threat-detection-get-started.md).

Ud over at beskytte dine SQL-Database ressourcer, giver Sikkerhedscenter også sikkerhed overvågning og administration til Azure virtuelle maskiner, Cloud Services, App Services, virtuelle netværk og meget mere. Lær mere om Sikkerhedscenter [her](security-center-intro.md).

## <a name="prerequisites"></a>Forudsætninger

For at komme i gang med Sikkerhedscenter, skal du have et abonnement på Microsoft Azure. Det gratis niveau i Sikkerhedscenter er aktiveret til dit abonnement. Se [Security Center priser](https://azure.microsoft.com/pricing/details/security-center/)kan finde flere oplysninger om Security Center gratis og Standard niveauer.

Sikkerhedscenter understøtter rollebaseret adgang. Hvis du vil vide mere om rollebaseret adgangskontrol (RBAC) i Azure, skal du se [Azure Active Directory rollebaseret adgangskontrol](../active-directory/role-based-access-control-configure.md). Security Center ofte stillede spørgsmål om indeholder oplysninger om, [hvordan tilladelser skal håndteres i Sikkerhedscenter](security-center-faq.md#how-are-permissions-handled-in-azure-security-center).

## <a name="access-security-center"></a>Access Sikkerhedscenter

Du kan få adgang til Sikkerhedscenter fra [Azure-portalen](https://azure.microsoft.com/features/azure-portal/). [Log på portalen](https://portal.azure.com/) , og vælg den **indstilling for Sikkerhedscenter**.

![Sikkerhedscenter indstilling][1]

Bladet **Sikkerhedscenter** åbnes.
![Sikkerhedscenter blade][2]

## <a name="set-security-policy"></a>Angive sikkerhedspolitik

En sikkerhedspolitik definerer de kontrolelementer, der er anbefalet til ressourcer inden for den angivne abonnement eller ressourcegruppe. I Sikkerhedscenter definerer du politikker for dine abonnementer eller ressourcegrupper afhængigt af din virksomheds sikkerhedsbehov og typer programmer eller følsomheden for dataene i hvert abonnement.

Du kan angive en politik til at vise anbefalinger til SQL overvågning og gennemsigtig data SQL-kryptering (TDE).

- Når du slår **SQL overvågning og registrering af truslen**, anbefaler Sikkerhedscenter, overvågning af adgang til Azure-databasen være aktiveret for overholdelse af regler, avancerede registrering og undersøgelse formål.
- Når du slår **SQL gennemsigtig datakryptering**, Sikkerhedscenter anbefaler være, at kryptering på resten aktiveret til Azure SQL-Database, tilknyttede sikkerhedskopier og transaktionslogfiler.

For at angive en sikkerhedspolitik skal du vælge feltet **politik** på bladet Sikkerhedscenter. Vælg det abonnement, som du vil aktivere sikkerhedspolitikken på bladet **sikkerhedspolitik** . Vælg **politik for beskyttelse mod** og **aktivere de sikkerhedsanbefalinger om, som du vil bruge på dette abonnement** .
![Sikkerhedspolitik][3]

Se [konfigurere sikkerhedspolitikker](security-center-policies.md)for at få mere for at vide.

## <a name="manage-security-recommendation"></a>Administrere sikkerhed anbefaling

Sikkerhedscenter analyserer med jævne mellemrum tilstanden sikkerhed for ressourcerne Azure. Når Sikkerhedscenter identificerer potentielle sikkerhedsrisici, opretter anbefalinger. Anbefalinger fører dig gennem processen med at konfigurere nødvendige kontrolelementer.

Når du har angivet en sikkerhedspolitik, analyserer Sikkerhedscenter sikkerhed tilstanden for dine ressourcer til at identificere potentielle svagheder. Anbefalinger vises i et tabelformat, hvor hver linje repræsenterer en bestemt anbefaling. Brug den følgende tabel som en reference til at hjælpe dig med at forstå de tilgængelige anbefalinger til Azure SQL-Database, og hvad hver anbefaling ikke, hvis du har anvendt den. Hvis du vælger en anbefaling, fører dig til en artikel, der forklarer, hvordan du implementerer anbefalingen i Sikkerhedscenter.

| Anbefaling | Beskrivelse |
| ----- | ----- |
| [Aktivere registrering af overvågning og truslen på SQL-servere](security-center-enable-auditing-on-sql-servers.md) | Anbefaler, at du slår overvågning og truslen registrering for SQL-Database-servere. (Kun SQL Database-tjeneste. Omfatter ikke Microsoft SQL Server, der kører på din virtuelle maskiner.) |
| [Aktivere registrering af overvågning og truslen på SQL-databaser](security-center-enable-auditing-on-sql-databases.md) | Anbefaler, at du slår overvågning og truslen registrering for SQL-Database databaser. (Kun SQL Database-tjeneste. Omfatter ikke Microsoft SQL Server, der kører på din virtuelle maskiner.) |
| [Aktivere gennemsigtig datakryptering](security-center-enable-transparent-data-encryption.md) | Anbefaler, at du aktiverer kryptering for SQL-databaser. (SQL-Database kun service.) |

For at se anbefalinger til dine Azure ressourcer skal du vælge feltet **anbefalinger** på bladet Sikkerhedscenter. Vælg en anbefaling kunne se oplysninger om bladet **anbefalinger** . I dette eksempel, Vælg Lad os **aktivere overvågning og registrering af truslen på SQL-servere**.

![Anbefalinger][4]

Som vist nedenfor, viser Sikkerhedscenter de SQL-servere, hvor overvågning og truslen registrering ikke er aktiveret. Når du har aktiveret overvågning, kan du konfigurere indstillinger for registrering af truslen og e-mail-indstillinger for at få vist sikkerhedsadvarsler. Truslen registrering giver dig besked, når programmet registrerer uoverensstemmende databaseaktiviteter, som angiver mulige trusler til databasen. Beskeder vises i dashboardet for Sikkerhedscenter.
![Overvågning og truslen registrering][5]

Følg trinnene i [Introduktion til SQL Database truslen registrering](../sql-database/sql-database-threat-detection-get-started.md) til at aktivere og konfigurere truslen registrering og konfigurere listen over mails, der skal modtage sikkerhedsadvarsler ved konstatering af uoverensstemmende aktiviteter.

Hvis du vil vide mere om anbefalinger, kan du se [administrere sikkerhedsanbefalinger](security-center-recommendations.md).

## <a name="monitor-security-health"></a>Overvåge sikkerhed tilstand

Når du aktiverer [sikkerhedspolitikker](security-center-policies.md) for ressourcer, der er et abonnement, analysere Sikkerhedscenter sikkerheden for dine ressourcer til at identificere potentielle svagheder.  Du kan se tilstanden sikkerhed for ressourcerne i feltet **ressource sikkerhed tilstand** . Når du klikker på **Data** i feltet **ressource sikkerhed tilstand** , åbnes bladet **Dataressourcer** med SQL anbefalinger til problemer som overvågning og gennemsigtig kryptering ikke er aktiveret. Der er også anbefalinger til generelle tilstanden af databasen.
![Ressource sikkerhed tilstand][6]

Hvis du vil vide mere, se [sikkerhed sundhed overvåge](security-center-monitoring.md).

## <a name="manage-and-respond-to-security-alerts"></a>Administrere og reagere på beskeder om sikkerhed

Sikkerhedscenter automatisk indsamler, analyserer og integrerer logdata fra [Azure SQL truslen registrering](../sql-database/sql-database-threat-detection-get-started.md)samt andre Azure ressourcer til at registrere reelle trusler og reducere forkerte forekomster. Der vises en liste over prioriterede sikkerhedsadvarsler i Sikkerhedscenter sammen med de oplysninger, du har brug for for hurtigt at undersøge problemet og anbefalinger til, hvordan du afhjælpning et angreb.

For at se beskeder skal du vælge feltet **sikkerhedsadvarsler** på bladet Sikkerhedscenter. Vælg en besked til at få mere at vide om de hændelser, som udløste den besked, og hvad, hvis et trin, du skal gennemføre for at afhjælpning et angreb på bladet **sikkerhedsadvarsler** . Lad os Vælg **potentielle SQL-indsættelse**i dette eksempel.
![Sikkerhedsadvarsler][7]

Som vist nedenfor, Security Center indeholder flere oplysninger, der giver indblik i hvad der udløste beskeden, target ressourcen, når det er relevant kildens IP-adresse og anbefalinger om, hvordan du afhjælpning.
![Potentielle SQL-indsættelse][8]

For at få mere for at vide, skal du se [administrere og besvare sikkerhedsadvarsler](security-center-managing-and-responding-alerts.md).

## <a name="next-steps"></a>Næste trin

- [Ofte stillede spørgsmål om sikkerhed Center](security-center-faq.md) – Find ofte stillede spørgsmål om ved hjælp af tjenesten.
- [Sikkerhedscenter planlægning og drift vejledning](security-center-planning-and-operations-guide.md) - Følg et sæt trin og opgaver til at optimere din brug af Sikkerhedscenter baseret på din organisations sikkerhedskrav og skyen management model.
- [Security Center datasikkerhed](security-center-data-security.md) – Lær, hvordan Sikkerhedscenter indsamler og behandler data om dine Azure ressourcer, herunder oplysninger om konfiguration, metadata, hændelseslogfiler, nedbrud lagring af filer og meget mere.
- [Håndtering af sikkerhedshændelser](security-center-incident.md) - Lær at bruge beskeder om sikkerhedsfunktioner i Sikkerhedscenter hjælper dig med håndtering af sikkerhedshændelser.

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png
