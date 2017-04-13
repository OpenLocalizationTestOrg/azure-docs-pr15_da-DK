<properties
   pageTitle="Introduktion til SQL Data Warehouse truslen registrering"
   description="Hvordan du kan komme i gang med truslen registrering"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/24/2016"
   ms.author="lodipalm;sonyama;barbkess"/>


# <a name="get-started-with-threat-detection"></a>Komme i gang med truslen registrering

> [AZURE.SELECTOR]
- [Overvågning](sql-data-warehouse-auditing-overview.md)
- [Truslen registrering](sql-data-warehouse-security-threat-detection.md)

## <a name="overview"></a>Oversigt

Truslen registrering registrerer uoverensstemmende databaseaktiviteter, der angiver mulige trusler til databasen. Truslen registrering i Vis udskrift og understøttes for SQL Data Warehouse.

Truslen registrering indeholder et nyt lag sikkerhed, som gør det muligt for kunder til at registrere og svare på potentielle trusler, efterhånden som de opstår ved at angive sikkerhedsadvarsler under uoverensstemmende aktiviteter. Brugere kan udforske mistænkelige begivenheder ved hjælp af [Azure SQL Data Warehouse overvågning](sql-data-warehouse-auditing-overview.md) til at bestemme, hvis de skyldes et forsøg på at få adgang til, bryde eller udnytte data i et datalager.
Truslen registrering gør det nemt at adresse potentielle trusler til et datalager uden at skulle være en ekspert sikkerhed eller administrere avancerede sikkerhedsindstillinger overvågning systemer.

For eksempel registrerer truslen registrering visse uoverensstemmende databaseaktiviteter, der angiver mulige SQL-indsættelse forsøg. SQL-indsættelse er et af de almindelige Web application sikkerhedsproblemer på internettet, bruges til at angreb datadrevne programmer. Hackere drage fordel af programmet svagheder skal tilføjes skadelig SQL-sætninger i programmet indtastningsfelter til overtrædelse eller ændring af data i databasen.


## <a name="set-up-threat-detection-for-your-database"></a>Konfigurere truslen registrering for din database

1. Start på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).

2. Gå til bladet konfiguration af SQL Data Warehouse du vil overvåge. Vælg **overvågning og registrering af truslen**i bladet indstillinger.

    ![Navigationsruden][1]

3. Gå **til** overvågning, som vises indstillingerne truslen registrering i bladet **overvågning og registrering af truslen** konfiguration.

    ![Navigationsruden][2]

4. Slå registrering af **til** truslen.

5. Konfigurere listen over mails, der skal modtage sikkerhedsadvarsler på identifikation af uoverensstemmende data warehouse aktiviteter.

6. Klik på **Gem** i bladet **overvågning og truslen registrering** konfiguration til at gemme den nye eller opdaterede overvågning og trussel registrering politik.

    ![Navigationsruden][3]


## <a name="explore-anomalous-data-warehouse-activities-upon-detection-of-a-suspicious-event"></a>Udforske uoverensstemmende data warehouse aktiviteter ved konstatering af en mistænkelige begivenhed

1. Du får besked via mail på registrering af uoverensstemmende databaseaktiviteter. <br/>
E-mailen give oplysninger om hændelsen mistænkelige sikkerhed, herunder art uoverensstemmende aktiviteter, databasenavnet, servernavn og tidspunkt for begivenheden. Derudover kan give oplysninger om mulige årsager og anbefalede handlinger til at undersøge og reducere potentielle truslen til databasen.<br/>

    ![Navigationsruden][4]

2. Klik på linket **Azure SQL overvågning Log** , hvilket Start Azure klassisk portalen og vise de relevante overvågning poster omkring tidspunktet for hændelsen mistænkelige i mailen.

    ![Navigationsruden][5]

3. Klik på overvågningsindstillinger posterne for at få vist flere detaljer om mistænkelige databaseaktiviteter som SQL-sætning, manglende årsag og klienten IP-adresse.

    ![Navigationsruden][6]

4. Klik på **Åbn i Excel** for at åbne en forudkonfigureret i bladet overvågning poster excel-skabelon til at importere og køre bedre analyse af overvågningsloggen omkring tidspunktet for hændelsen mistænkelige.<br/>
**Note:** I Excel 2010 eller nyere, der kræves for Power-forespørgsel og indstillingen **Hurtig kombination**

    ![Navigationsruden][7]

5. For at konfigurere indstillingen **Hurtig kombination** - i båndfanen **POWER-forespørgsel** skal du vælge **Indstillinger** til at vise dialogboksen Indstillinger for. Vælge afsnittet beskyttelse af personlige oplysninger og vælge den anden mulighed - 'Ignorer niveauer for beskyttelse af personlige oplysninger og potentielt forbedre ydeevnen':

    ![Navigationsruden][8]

6. For at indlæse SQL overvågningslogge skal du sikre, at parametrene i indstillingerne under fanen er indstillet korrekt, og vælg derefter 'Data' båndet og klikke på knappen 'Opdater alle'.

    ![Navigationsruden][9]

7. Resultaterne vises i arket **SQL overvågningslogge** som gør det muligt at køre tillader en mere dybdegående analyse af de uoverensstemmende aktiviteter, der blev fundet og afhjælpe virkningen af hændelsen sikkerhed i programmet.


<!--Image references-->
[1]: ./media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-data-warehouse-security-threat-detection/4_td_email.png
[5]: ./media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png
[6]: ./media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png
[7]: ./media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png
[8]: ./media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png
[9]: ./media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png
