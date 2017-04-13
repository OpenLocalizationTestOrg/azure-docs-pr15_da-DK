<properties
   pageTitle="Beskytte Azure SQL-tjenesten i Azure Sikkerhedscenter | Microsoft Azure"
   description="Dette dokument adresser anbefalinger i Azure Security Center, der hjælper dig med at beskytte SQL Azure service og holde i overensstemmelse med sikkerhedspolitikker."
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
   ms.date="08/04/2016"
   ms.author="terrylan"/>

# <a name="protecting-azure-sql-service-in-azure-security-center"></a>Beskytte Azure SQL-tjenesten i Azure Security Center

Azure Sikkerhedscenter analyserer tilstanden sikkerhed for ressourcerne Azure. Når Sikkerhedscenter identificerer potentielle sikkerhedsrisici, opretter anbefalinger, der fører dig gennem processen med at konfigurere nødvendige kontrolelementer.  Anvende anbefalinger til Azure ressourcetyper: virtuelle maskiner (VM'er), netværk, SQL og programmer.

I denne artikel omhandler anbefalinger, der gælder for SQL Azure service.  Azure SQL-tjenesten anbefalinger center rundt om aktivering af overvågning for Azure SQL-servere og databaser og aktivere kryptering for SQL-databaser.  Brug tabellen nedenfor som en reference til at hjælpe dig med at forstå de tilgængelige SQL-tjeneste anbefalinger, og hvad hver enkelt skal gøre, hvis du har anvendt den.

## <a name="available-sql-service-recommendations"></a>Tilgængelige SQL-tjenesten anbefalinger

|Anbefaling|Beskrivelse|
|-----|-----|
|[Aktivere overvågning af SQL-server](security-center-enable-auditing-on-sql-servers.md)|Anbefaler, at du slår overvågning for Azure SQL-servere (SQL Azure service kun; ikke omfatter SQL, der kører på din virtuelle maskiner).|
|[Aktivere overvågning af SQL-database](security-center-enable-auditing-on-sql-databases.md)|Anbefaler, at du slår overvågning for Azure SQL-databaser (SQL Azure service kun; ikke omfatter SQL, der kører på din virtuelle maskiner).|
|[Aktivere gennemsigtig kryptering af Data på SQL-databaser](security-center-enable-transparent-data-encryption.md)|Anbefaler, at du aktiverer kryptering for SQL-databaser (kun SQL Azure service).|

## <a name="see-also"></a>Se også

Hvis du vil vide mere om anbefalinger, der gælder for andre Azure ressourcetyper, skal du se følgende:

- [Beskytte din virtuelle maskiner i Azure Sikkerhedscenter](security-center-virtual-machine-recommendations.md)
- [Beskytte dine programmer i Azure Sikkerhedscenter](security-center-application-recommendations.md)
- [Beskytte dit netværk i Azure Security Center](security-center-network-recommendations.md)

Hvis du vil vide mere om Sikkerhedscenter, skal du se følgende:

- [Angive sikkerhedspolitikker i Azure Sikkerhedscenter](security-center-policies.md) – Lær, hvordan du konfigurere sikkerhedspolitikker for dine Azure abonnementer og grupper.
- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md) – Lær at administrere og reagere på beskeder om sikkerhed.
- [Azure Security Center ofte stillede spørgsmål](security-center-faq.md) – Find ofte stillede spørgsmål om ved hjælp af tjenesten.
