<properties
   pageTitle="Aktivere overvågning på SQL-servere i Azure Sikkerhedscenter | Microsoft Azure"
   description="Dette dokument viser, hvordan du implementerer Azure Sikkerhedscenter anbefalingen **aktivere overvågning på SQL-servere**."
   services="security-center"
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
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# <a name="enable-auditing-on-sql-servers-in-azure-security-center"></a>Aktivere overvågning på SQL-servere i Azure Sikkerhedscenter

Azure Sikkerhedscenter anbefaler, at du slår overvågning for alle databaser på Azure SQL-servere, hvis overvågning ikke allerede er aktiveret. Overvågning kan hjælpe dig med at bevare overholdelse af lovgivningen, forstå databaseaktivitet og få indsigt i uoverensstemmelser og afvigelser, der kan angive business problemstillinger eller mulig overtrædelse af sikkerhed.

Du kan konfigurere indstillinger for registrering af truslen og mails for at få vist sikkerhedsadvarsler, når du har aktiveret overvågning. Truslen registrering registrerer uoverensstemmende databaseaktiviteter, der angiver mulige trusler til databasen. Dette gør det muligt at registrere og svare på potentielle trusler, efterhånden som de opstår.

Denne anbefaling gælder til Azure SQL-tjenesten. Det omfatter ikke SQL Server, der kører på din virtuelle maskiner i Azure infrastrukturtjenester (Azure IaaS).

> [AZURE.NOTE] Dette dokument introducerer tjenesten ved hjælp af en eksempel-installation.  Dette er ikke en trinvis vejledning.

## <a name="implement-the-recommendation"></a>Implementere anbefalingen

1. Vælg **Aktivér overvågning på SQL-servere**i bladet **anbefalinger** .  Dette åbner bladet **Aktivere overvågning på SQL-servere** .
![Aktivere overvågning på SQL-servere][1]

2. Vælg en SQL server for at aktivere overvågning på. Dette åbner bladet **Indstillinger for overvågning** .
![Indstillinger for overvågning][2]
3. Vælg **på** under **overvågning**på bladet **Indstillinger for overvågning** .
![Slå indstillinger for overvågning][3]

4. Følg trinnene i [Introduktion til SQL database overvågning](../sql-database/sql-database-auditing-get-started.md) for at konfigurere lagerplads, hvor din overvågningslogge skal gemmes. Det abonnement lagerplads konto for indsamling af data er lagerplads standardkontoen.

5. Følg trinnene i [Introduktion til SQL Database truslen registrering](../sql-database/sql-database-threat-detection-get-started.md) til at aktivere og konfigurere truslen registrering og konfigurere listen over mails, der skal modtage sikkerhedsadvarsler ved konstatering af uoverensstemmende aktiviteter.

## <a name="see-also"></a>Se også

I denne artikel viste dig, hvordan at implementere Sikkerhedscenter anbefaling "Aktivere overvågning på SQL-servere". Hvis du vil vide mere om sikring af SQL-database skal du se følgende:

- [Sikring af SQL-Database](../sql-database/sql-database-security.md)

Hvis du vil vide mere om Sikkerhedscenter, skal du se følgende:

- [Angive sikkerhedspolitikker i Azure Sikkerhedscenter](security-center-policies.md) – Lær, hvordan du konfigurere sikkerhedspolitikker for dine Azure abonnementer og grupper.
- [Administrere sikkerhedsanbefalinger i Azure Sikkerhedscenter](security-center-recommendations.md) – Lær, hvordan anbefalinger hjælpe dig med at beskytte dine Azure ressourcer.
- [Sikkerhed sundhed overvågning i Azure Sikkerhedscenter](security-center-monitoring.md) – Lær, hvordan du overvåge tilstanden for dine Azure ressourcer.
- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md) – Lær at administrere og reagere på beskeder om sikkerhed.
- [Overvågnings partnerløsninger med Azure Security Center](security-center-partner-solutions.md) – Lær, hvordan du overvåge systemtilstand status for din partnerløsninger.
- [Azure Security Center ofte stillede spørgsmål](security-center-faq.md) – Find ofte stillede spørgsmål om ved hjælp af tjenesten.
- [Azure sikkerhed blog](http://blogs.msdn.com/b/azuresecurity/) – få de seneste nyheder og Azure sikkerhed og oplysninger.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-server/enable-auditing-on-sql-servers.png
[2]:./media/security-center-enable-auditing-on-sql-server/enable-auditing.png
[3]: ./media/security-center-enable-auditing-on-sql-server/auditing-settings-blade.png
