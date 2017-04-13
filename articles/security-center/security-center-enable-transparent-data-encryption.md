<properties
   pageTitle="Aktivere kryptering af gennemsigtig Data i Azure Sikkerhedscenter | Microsoft Azure"
   description="Dette dokument viser, hvordan du implementere Azure Sikkerhedscenter anbefaling **Aktivere gennemsigtig kryptering af Data**."
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

# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>Aktivere kryptering af gennemsigtig Data i Azure Sikkerhedscenter

Azure Sikkerhedscenter anbefaler, at du aktiverer gennemsigtig Data kryptering (TDE) på SQL-databaser, hvis TDE ikke allerede er aktiveret. TDE beskytter dine data og hjælper dig med at opfylde overholdelseskrav ved at kryptere din database, tilknyttede sikkerhedskopier og transaktionslogfiler på resten, uden at kræve ændringer i dit program. Du kan se mere under [Gennemsigtig kryptering af Data med Azure SQL-Database](https://msdn.microsoft.com/library/dn948096).

Denne anbefaling gælder til Azure SQL-tjenesten. omfatter ikke SQL, der kører på din virtuelle computere.

> [AZURE.NOTE] Dette dokument introducerer tjenesten ved hjælp af en eksempel-installation.  Dette er ikke en trinvis vejledning.

## <a name="implement-the-recommendation"></a>Implementere anbefalingen

1. Vælg **Aktivér gennemsigtig kryptering af Data**i bladet **anbefalinger** .
![Aktivere gennemsigtig datakryptering][1]

2. Dette åbner bladet **Aktivere kryptering af gennemsigtig Data på SQL-databaser** . Vælg en SQL-database til at aktivere TDE på.
![Vælg SQL DB Sådan aktiveres TDE på][2]
3. Vælg **på** under kryptering af Data på bladet **kryptering af gennemsigtig data** , og vælg **Gem** i det øverste bladet på båndet.
![Slå TDE][3]

  Når TDE er aktiveret på den valgte SQL-database, ændres **kryptering status** til **krypteret**.    

  ![Kryptering status][4]

## <a name="see-also"></a>Se også

I denne artikel viste dig, hvordan at implementere Sikkerhedscenter anbefaling "Aktivere gennemsigtig kryptering af Data". Hvis du vil vide mere om SQL TDE skal du se følgende:

- [Kryptering af gennemsigtig Data med Azure SQL-Database](https://msdn.microsoft.com/library/dn948096)
- [Komme i gang med gennemsigtig Data kryptering (TDE)](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

Hvis du vil vide mere om Sikkerhedscenter, skal du se følgende:

- [Angive sikkerhedspolitikker i Azure Sikkerhedscenter](security-center-policies.md) – Lær, hvordan du konfigurere sikkerhedspolitikker for dine Azure abonnementer og grupper.
- [Administrere sikkerhedsanbefalinger i Azure Sikkerhedscenter](security-center-recommendations.md) – Lær, hvordan anbefalinger hjælpe dig med at beskytte dine Azure ressourcer.
- [Sikkerhed sundhed overvågning i Azure Sikkerhedscenter](security-center-monitoring.md) – Lær, hvordan du overvåge tilstanden for dine Azure ressourcer.
- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md) – Lær at administrere og reagere på beskeder om sikkerhed.
- [Overvågnings partnerløsninger med Azure Security Center](security-center-partner-solutions.md) – Lær, hvordan du overvåge systemtilstand status for din partnerløsninger.
- [Azure Security Center ofte stillede spørgsmål](security-center-faq.md) – Find ofte stillede spørgsmål om ved hjælp af tjenesten.
- [Azure sikkerhed blog](http://blogs.msdn.com/b/azuresecurity/) – få de seneste nyheder og Azure sikkerhed og oplysninger.

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png
