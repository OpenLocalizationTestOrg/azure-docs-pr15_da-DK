<properties
   pageTitle="Azure datakatalog produktbemærkninger | Microsoft Azure"
   description="Produktbemærkninger til Azure datakatalog."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/21/2016"
   ms.author="maroche"/>

# <a name="azure-data-catalog-release-notes"></a>Azure datakatalog produktbemærkninger

## <a name="notes-for-the-november-20-2015-release-of-azure-data-catalog"></a>Noter til de November 20 2015 overgang Azure datakatalog

### <a name="opening-data-sources-in-power-bi-desktop"></a>Åbne datakilder i Power BI Desktop

Når du bruger indstillingen "Åbn i Power BI Desktop" fra portalen **Azure datakatalog** , brugere kan støde på en af to problemer i Power BI Desktop programmet:

- Der vises en dialogboks med titlen "Ikke til at åbne dokument"
- Programmet Power BI Desktop åbnes, men filen ser ud til at være tom

For hver situation kan problemet løses ved at downloade og installere den nyeste version af Power BI Desktop fra [PowerBI.com](https://powerbi.com).

## <a name="notes-for-the-november-13-2015-release-of-azure-data-catalog"></a>Noter til de November 13 2015 overgang Azure datakatalog

### <a name="registering-and-connecting-to-teradata"></a>Registrere og oprette forbindelse til Teradata

Når du opretter forbindelse til Teradata-datakilder brugere skal have installeret den korrekte Teradata ODBC-driver, der svarer til bittæthed (32-bit eller 64-bit) af softwaren bruges.

Og denne ADC Udgivelsesdato den seneste [Teradata ODBC-driver til windows (version 15.10)](http://downloads.teradata.com/download/connectivity/odbc-driver/windows) er kompatibel med Office 2013, men ikke har Office 2016.

## <a name="notes-for-the-july-13-2015-release-of-azure-data-catalog"></a>Noter til de juli 13 2015 overgang Azure datakatalog

### <a name="registering-and-connecting-to-oracle-database"></a>Registrere og oprette forbindelse til Oracle-Database

Når du opretter forbindelse til Oracle-Database datakilder brugere skal have installeret de korrekte Oracle-drivere, der svarer til bittæthed (32-bit eller 64-bit) af softwaren bruges.

-   Når der registreres Oracle datakilder på en computer med 32-bit Windows, 32-bit Oracle-drivere, der skal bruges
-   Når der registreres Oracle datakilder på en computer med 64-bit Windows, 64-bit Oracle-drivere, der skal bruges
-   Når du opretter forbindelse til Oracle-datakilder ved hjælp af Excel på en computer, der kører en 32-bit version af Microsoft Office, der herunder på 64-bit Windows 32-bit Oracle drivere skal bruges
-   Når du opretter forbindelse til Oracle-datakilder ved hjælp af Excel på en computer, der kører en 64-bit version af Microsoft Office, 64-bit Oracle-drivere, der skal bruges

### <a name="registering-and-connecting-to-sql-server-reporting-services"></a>Registrere og oprette forbindelse til SQL Server Reporting Services

Understøttelse af SQL Server Reporting Services (SSRS)-datakilder, der er i øjeblikket er begrænset til kun servere-tilstand. Understøttelse af SSRS i SharePoint-tilstand, føjes en nyere version.

### <a name="opening-data-assets-in-excel"></a>Åbne dataaktiver i Excel

Når du åbner dataaktiver i Microsoft Excel fra portalen **Azure datakatalog** , kan brugere bliver bedt om med en **Microsoft Excel-sikkerhedsmeddelelse** dialogboks. Dette er standard, forventede funktionsmåde, og brugerne kan vælge **Aktivér** at fortsætte.

Se [aktivere eller deaktivere sikkerhedsadvarsler om links og filer fra mistænkelige websteder](https://support.office.com/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE)kan finde flere oplysninger.

### <a name="proxy-and-policy-configuration-and-data-source-registration"></a>Konfiguration af proxy og politik og data fra kilden registrering

Brugere kan støde på en situation, hvor de kan logge på, til portalen Azure datakatalog, men når de forsøger at logge på værktøjet datakilde registrering de får en fejlmeddelelse, der forhindrer dem i at logge på.

Der er to mulige årsager til dette problem:

**Årsag 1: konfiguration af Active Directory Federation Services** Værktøjet til datakilde registrering bruger formulargodkendelse til at validere brugerlogon mod Active Directory. Til vellykket logon, skal være aktiveret formulargodkendelse i politikken globale godkendelse af en Active Directory-administrator.

I nogle situationer kan kan problemet fejl opstå, når brugeren er på virksomhedens netværk, eller kun, når brugeren opretter forbindelse fra uden for virksomhedens netværk. Den globale godkendelsespolitik giver godkendelsesmetoder til aktiveres separat til intranet og ekstranet forbindelser. Logon fejl kan opstå, hvis formulargodkendelse ikke er aktiveret for det netværk, hvor brugeren opretter forbindelse.

Du kan finde yderligere oplysninger finder [Konfigurere politikker for godkendelse](https://technet.microsoft.com/library/dn486781.aspx).

**Årsag 2: proxy netværkskonfiguration** Hvis virksomhedens netværk bruger en proxyserver, kan værktøjet til registrering muligvis ikke oprette forbindelse til Azure Active Directory via proxyen. Brugere kan sikre, at værktøjet til registrering ved at redigere værktøjets konfigurationsfil, tilføjes filen i dette afsnit:


      <system.net>
        <defaultProxy useDefaultCredentials="true" enabled="true">
          <proxy usesystemdefault="True"
                          proxyaddress="http://<your corporate network proxy url>"
                          bypassonlocal="True"/>
        </defaultProxy>
      </system.net>


Start værktøjet til registrering for at finde filen RegistrationTool.exe.config, og derefter åbne værktøjet Windows Jobliste. Højreklik på RegistrationTool.exe på fanen Detaljer i Jobliste og vælge Åbn filplacering i pop op-menuen.
