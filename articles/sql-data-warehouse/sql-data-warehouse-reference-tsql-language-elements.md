<properties
   pageTitle="SQL Data Warehouse Transact-SQL sprogelementer | Microsoft Azure"
   description="Liste over links til oplysninger om indhold til de Transact-SQL sprogelementer, der bruges til SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/08/2016"
   ms.author="barbkess;sonyama;kevin"/>

# <a name="language-elements"></a>Sprogelementer

## <a name="core-elements"></a>Grundlæggende elementer

- [Syntaksen for parametre](https://msdn.microsoft.com/library/ms177563.aspx)
- [reglerne for navngivning af objekt](https://msdn.microsoft.com/library/ms175874.aspx)
- [reserverede nøgleord](https://msdn.microsoft.com/library/ms189822.aspx)
- [sorteringer](https://msdn.microsoft.com/library/ff848763.aspx)
- [kommentarer](https://msdn.microsoft.com/library/ms181627.aspx)
- [konstanter](https://msdn.microsoft.com/library/ms179899.aspx)
- [datatyper](https://msdn.microsoft.com/library/ms187752.aspx)
- [UDFØRE](https://msdn.microsoft.com/library/ms188332.aspx)
- [udtryk](https://msdn.microsoft.com/library/ms190286.aspx)
- [AFBRYDE](https://msdn.microsoft.com/library/ms173730.aspx)
- [IDENTITET egenskaben løsning](https://msdn.microsoft.com/library/ms186775.aspx)
- [UDSKRIV](https://msdn.microsoft.com/library/ms176047.aspx)
- [BRUG](https://msdn.microsoft.com/library/ms188366.aspx)

## <a name="batches-control-of-flow-and-variables"></a>Batches, af Kontrolflow og variabler

- [STARTE... AFSLUT](https://msdn.microsoft.com/library/ms190487.aspx)
- [SIDESKIFT](https://msdn.microsoft.com/library/ms181271.aspx)
- [ERKLÆRE@local_variable](https://msdn.microsoft.com/library/ms188927.aspx)
- [HVIS... MERE](https://msdn.microsoft.com/library/ms182717.aspx)
- [RAISERROR](https://msdn.microsoft.com/library/ms178592.aspx)
- [SET@local_variable](https://msdn.microsoft.com/library/ms189484.aspx)
- [UDLØS](https://msdn.microsoft.com/library/ee677615.aspx)
- [PRØV... FANGE](https://msdn.microsoft.com/library/ms175976.aspx)
- [STYKKE TID](https://msdn.microsoft.com/library/ms178642.aspx)

## <a name="operators"></a>Operatorer
- [+ (Tilføj)](https://msdn.microsoft.com/library/ms178565.aspx)
- [+ (Streng sammenkædning)](https://msdn.microsoft.com/library/ms177561.aspx)
- [-(Negativ)](https://msdn.microsoft.com/library/ms189480.aspx)
- [-(Træk)](https://msdn.microsoft.com/library/ms189518.aspx)
- [* (Gange)](https://msdn.microsoft.com/library/ms176019.aspx)
- [/ (Divider)](https://msdn.microsoft.com/library/ms175009.aspx)
- [Modulus](https://msdn.microsoft.com/library/ms190279.aspx)

## <a name="wildcard-characters-to-match"></a>Jokertegn tegn til at matche
- [= (Lig med)](https://msdn.microsoft.com/library/ms175118.aspx)
- [> (Større end)](https://msdn.microsoft.com/library/ms178590.aspx)
- [< (Mindre end)](https://msdn.microsoft.com/library/ms179873.aspx)
- [> = (lyder godt end eller lig med)](https://msdn.microsoft.com/library/ms181567.aspx)
- [< = (mindre end eller lig med)](https://msdn.microsoft.com/library/ms174978.aspx)
- [<> (Ikke lig med)](https://msdn.microsoft.com/library/ms176020.aspx)
- [! = (Ikke lig med)](https://msdn.microsoft.com/library/ms190296.aspx)
- [OG](https://msdn.microsoft.com/library/ms188372.aspx)
- [MELLEM](https://msdn.microsoft.com/library/ms187922.aspx)
- [DER FINDES](https://msdn.microsoft.com/library/ms188336.aspx)
- [I](https://msdn.microsoft.com/library/ms177682.aspx)
- [[IKKE] ER NULL](https://msdn.microsoft.com/library/ms188795.aspx)
- [SYNES GODT OM](https://msdn.microsoft.com/library/ms179859.aspx)
- [IKKE](https://msdn.microsoft.com/library/ms189455.aspx)
- [ELLER](https://msdn.microsoft.com/library/ms188361.aspx)

### <a name="bitwise-operators"></a>Bitvis operatorer

- [& (Bitbaseret og)](https://msdn.microsoft.com/library/ms174965.aspx)
- [| (Bitbaseret eller)](https://msdn.microsoft.com/library/ms186714.aspx)
- [^ (Bitbaseret eksklusivt eller)](https://msdn.microsoft.com/library/ms190277.aspx)
- [~ (Bitvis ikke)](https://msdn.microsoft.com/library/ms173468.aspx)
- [^ = (Bitvis eksklusiv eller er lig med)](https://msdn.microsoft.com/library/cc627413.aspx)
- [| = (' Bitbaseret eller ' er lig med)](https://msdn.microsoft.com/library/cc627409.aspx)
- [& = (bitbaseret og er lig med)](https://msdn.microsoft.com/library/cc627427.aspx)

## <a name="functions"></a>Funktioner

- [@@DATEFIRST](https://msdn.microsoft.com/library/ms187766.aspx)
- [@@ERROR](https://msdn.microsoft.com/library/ms188790.aspx)
- [@@LANGUAGE](https://msdn.microsoft.com/library/ms177557.aspx)
- [@@SPID](https://msdn.microsoft.com/library/ms189535.aspx)
- [@@TRANCOUNT](https://msdn.microsoft.com/library/ms187967.aspx)
- [@@VERSION](https://msdn.microsoft.com/library/ms177512.aspx)
- [ABS](https://msdn.microsoft.com/library/ms189800.aspx)
- [ARCCOS](https://msdn.microsoft.com/library/ms178627.aspx)
- [ASCII](https://msdn.microsoft.com/library/ms177545.aspx)
- [ARCSIN](https://msdn.microsoft.com/library/ms181581.aspx)
- [ARCTAN](https://msdn.microsoft.com/library/ms181746.aspx)
- [ATN2](https://msdn.microsoft.com/library/ms173854.aspx)
- [BINARY_CHECKSUM](https://msdn.microsoft.com/library/ms173784.aspx)
- [STORE OG SMÅ BOGSTAVER](https://msdn.microsoft.com/library/ms181765.aspx)
- [CAST og KONVERTÉR](https://msdn.microsoft.com/library/ms187928.aspx)
- [LOFT](https://msdn.microsoft.com/library/ms189818.aspx)
- [TEGN](https://msdn.microsoft.com/library/ms187323.aspx)
- [STRENGFUNKTIONERS](https://msdn.microsoft.com/library/ms186323.aspx)
- [KONTROLSUM](https://msdn.microsoft.com/library/ms189788.aspx)
- [SAMLING](https://msdn.microsoft.com/library/ms190349.aspx)
- [COL_NAME](https://msdn.microsoft.com/library/ms174974.aspx)
- [COLLATIONPROPERTY](https://msdn.microsoft.com/library/ms190305.aspx)
- [SAMMENKÆD](https://msdn.microsoft.com/library/hh231515.aspx)
- [COS](https://msdn.microsoft.com/library/ms188919.aspx)
- [COT](https://msdn.microsoft.com/library/ms188921.aspx)
- [TÆL](https://msdn.microsoft.com/library/ms175997.aspx)
- [COUNT_BIG](https://msdn.microsoft.com/library/ms190317.aspx)
- [CUME_DIST](https://msdn.microsoft.com/library/hh231078.aspx)
- [CURRENT_TIMESTAMP](https://msdn.microsoft.com/library/ms188751.aspx)
- [AKTUEL_BRUGER](https://msdn.microsoft.com/library/ms176050.aspx)
- [DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx)
- [DATALENGTH](https://msdn.microsoft.com/library/ms173486.aspx)
- [DATEADD](https://msdn.microsoft.com/library/ms186819.aspx)
- [DATEDIFF](https://msdn.microsoft.com/library/ms189794.aspx)
- [DATEFROMPARTS](https://msdn.microsoft.com/library/hh213228.aspx)
- [DATENAME](https://msdn.microsoft.com/library/ms174395.aspx)
- [DATEPART](https://msdn.microsoft.com/library/ms174420.aspx)
- [DATETIME2FROMPARTS](https://msdn.microsoft.com/library/hh213312.aspx)
- [DATETIMEFROMPARTS](https://msdn.microsoft.com/library/hh213233.aspx)
- [DATETIMEOFFSETFROMPARTS](https://msdn.microsoft.com/library/hh231077.aspx)
- [DAG](https://msdn.microsoft.com/library/ms176052.aspx)
- [DB_ID](https://msdn.microsoft.com/library/ms186274.aspx)
- [DB_NAME](https://msdn.microsoft.com/library/ms189753.aspx)
- [GRADER](https://msdn.microsoft.com/library/ms178566.aspx)
- [DENSE_RANK](https://msdn.microsoft.com/library/ms173825.aspx)
- [FORSKELLEN](https://msdn.microsoft.com/library/ms188753.aspx)
- [SLUT.PÅ.MÅNED](https://msdn.microsoft.com/library/hh213020.aspx)
- [ERROR_MESSAGE](https://msdn.microsoft.com/library/ms190358.aspx)
- [ERROR_NUMBER](https://msdn.microsoft.com/library/ms175069.aspx)
- [ERROR_PROCEDURE](https://msdn.microsoft.com/library/ms188398.aspx)
- [ERROR_SEVERITY](https://msdn.microsoft.com/library/ms178567.aspx)
- [ERROR_STATE](https://msdn.microsoft.com/library/ms180031.aspx)
- [EXP](https://msdn.microsoft.com/library/ms179857.aspx)
- [FIRST_VALUE](https://msdn.microsoft.com/library/hh213018.aspx)
- [FLOOR](https://msdn.microsoft.com/library/ms178531.aspx)
- [GETDATE](https://msdn.microsoft.com/library/ms188383.aspx)
- [GETUTCDATE](https://msdn.microsoft.com/library/ms178635.aspx)
- [HAS_DBACCESS](https://msdn.microsoft.com/library/ms187718.aspx)
- [HASHBYTES](https://msdn.microsoft.com/library/ms174415.aspx)
- [INDEXPROPERTY](https://msdn.microsoft.com/library/ms187729.aspx)
- [ISDATE](https://msdn.microsoft.com/library/ms187347.aspx)
- [ISNULL](https://msdn.microsoft.com/library/ms184325.aspx)
- [ISNUMERIC](https://msdn.microsoft.com/library/ms186272.aspx)
- [MELLEMLIGGENDE](https://msdn.microsoft.com/library/hh231256.aspx)
- [LAST_VALUE](https://msdn.microsoft.com/library/hh231517.aspx)
- [KUNDEEMNE](https://msdn.microsoft.com/library/hh213125.aspx)
- [VENSTRE](https://msdn.microsoft.com/library/ms177601.aspx)
- [LÆNGDE](https://msdn.microsoft.com/library/ms190329.aspx)
- [LOG](https://msdn.microsoft.com/library/ms190319.aspx)
- [LOG10](https://msdn.microsoft.com/library/ms175121.aspx)
- [NEDERSTE](https://msdn.microsoft.com/library/ms174400.aspx)
- [LTRIM](https://msdn.microsoft.com/library/ms177827.aspx)
- [MAKS](https://msdn.microsoft.com/library/ms187751.aspx)
- [MIN](https://msdn.microsoft.com/library/ms179916.aspx)
- [MÅNED](https://msdn.microsoft.com/library/ms187813.aspx)
- [NCHAR](https://msdn.microsoft.com/library/ms182673.aspx)
- [NTILE](https://msdn.microsoft.com/library/ms175126.aspx)
- [NULLIF](https://msdn.microsoft.com/library/ms177562.aspx)
- [OBJECT_ID](https://msdn.microsoft.com/library/ms190328.aspx)
- [OBJEKTNAVN](https://msdn.microsoft.com/library/ms186301.aspx)
- [OBJECTPROPERTY](https://msdn.microsoft.com/library/ms176105.aspx)
- [OIBJECTPROPERTYEX](https://msdn.microsoft.com/library/ms188390.aspx)
- [ODBCS skalarfunktioner](https://msdn.microsoft.com/library/bb630290.aspx)
- [OVER delsætning](https://msdn.microsoft.com/library/ms189461.aspx)
- [PARSENAME](https://msdn.microsoft.com/library/ms188006.aspx)
- [PATINDEX](https://msdn.microsoft.com/library/ms188395.aspx)
- [PERCENTILE_CONT](https://msdn.microsoft.com/library/hh231473.aspx)
- [PERCENTILE_DISC](https://msdn.microsoft.com/library/hh231327.aspx)
- [PERCENT_RANK](https://msdn.microsoft.com/library/hh213573.aspx)
- [PI](https://msdn.microsoft.com/library/ms189512.aspx)
- [POWER](https://msdn.microsoft.com/library/ms174276.aspx)
- [QUOTENAME](https://msdn.microsoft.com/library/ms176114.aspx)
- [RADIANER](https://msdn.microsoft.com/library/ms189742.aspx)
- [SLUMP](https://msdn.microsoft.com/library/ms177610.aspx)
- [RANG](https://msdn.microsoft.com/library/ms176102.aspx)
- [ERSTAT](https://msdn.microsoft.com/library/ms186862.aspx)
- [GENTAGE](https://msdn.microsoft.com/library/ms174383.aspx)
- [OMVENDT](https://msdn.microsoft.com/library/ms180040.aspx)
- [HØJRE](https://msdn.microsoft.com/library/ms177532.aspx)
- [AFRUNDE](https://msdn.microsoft.com/library/ms175003.aspx)
- [RÆKKENUMMER](https://msdn.microsoft.com/library/ms186734.aspx)
- [RTRIM](https://msdn.microsoft.com/library/ms178660.aspx)
- [SCHEMA_ID](https://msdn.microsoft.com/library/ms188797.aspx)
- [SCHEMA_NAME](https://msdn.microsoft.com/library/ms175068.aspx)
- [SERVERPROPERTY](https://msdn.microsoft.com/library/ms174396.aspx)
- [SESSION_USER](https://msdn.microsoft.com/library/ms177587.aspx)
- [LOG](https://msdn.microsoft.com/library/ms188420.aspx)
- [SIN](https://msdn.microsoft.com/library/ms188377.aspx)
- [SMALLDATETIMEFROMPARTS](https://msdn.microsoft.com/library/hh213396.aspx)
- [SOUNDEX](https://msdn.microsoft.com/library/ms187384.aspx)
- [MELLEMRUM](https://msdn.microsoft.com/library/ms187950.aspx)
- [SQL_VARIANT_PROPERTY](https://msdn.microsoft.com/library/ms178550.aspx)
- [KVROD](https://msdn.microsoft.com/library/ms176108.aspx)
- [FIRKANT](https://msdn.microsoft.com/library/ms173569.aspx)
- [STATS_DATE](https://msdn.microsoft.com/library/ms190330.aspx)
- [STDAFV](https://msdn.microsoft.com/library/ms190474.aspx)
- [STDAFVP](https://msdn.microsoft.com/library/ms176080.aspx)
- [STR](https://msdn.microsoft.com/library/ms189527.aspx)
- [TING](https://msdn.microsoft.com/library/ms188043.aspx)
- [UNDERSTRENG](https://msdn.microsoft.com/library/ms187748.aspx)
- [SUM](https://msdn.microsoft.com/library/ms187810.aspx)
- [SUSER_SNAME](https://msdn.microsoft.com/library/ms174427.aspx)
- [SWITCHOFFSET](https://msdn.microsoft.com/library/bb677244.aspx)
- [SYSDATETIME](https://msdn.microsoft.com/library/bb630353.aspx)
- [SYSDATETIMEOFFSET](https://msdn.microsoft.com/library/bb677334.aspx)
- [SYSTEM_USER](https://msdn.microsoft.com/library/ms179930.aspx)
- [SYSUTCDATETIME](https://msdn.microsoft.com/library/bb630387.aspx)
- [TAN](https://msdn.microsoft.com/library/ms190338.aspx)
- [TERTIARY_WEIGHTS](https://msdn.microsoft.com/library/ms186881.aspx)
- [TIMEFROMPARTS](https://msdn.microsoft.com/library/hh213398.aspx)
- [TODATETIMEOFFSET](https://msdn.microsoft.com/library/bb630335.aspx)
- [TYPE_ID](https://msdn.microsoft.com/library/ms181628.aspx)
- [TYPE_NAME](https://msdn.microsoft.com/library/ms189750.aspx)
- [TYPEPROPERTY](https://msdn.microsoft.com/library/ms188419.aspx)
- [UNICODE](https://msdn.microsoft.com/library/ms180059.aspx)
- [ØVERSTE](https://msdn.microsoft.com/library/ms180055.aspx)
- [BRUGER](https://msdn.microsoft.com/library/ms186738.aspx)
- [BRUGERNAVN](https://msdn.microsoft.com/library/ms188014.aspx)
- [VARIANS](https://msdn.microsoft.com/library/ms186290.aspx)
- [FUNKTIONEN VARIANSP](https://msdn.microsoft.com/library/ms188735.aspx)
- [ÅR](https://msdn.microsoft.com/library/ms186313.aspx)
- [XACT_STATE](https://msdn.microsoft.com/library/ms189797.aspx)

## <a name="transactions"></a>Transaktioner

- [transaktioner](https://msdn.microsoft.com/library/mt204031.aspx)

## <a name="diagnostic-sessions"></a>Diagnose-sessioner

- [OPRETTE DIAGNOSTICERINGSSESSION](https://msdn.microsoft.com/library/mt204029.aspx)

## <a name="procedures"></a>Procedurer

- [sp_addrolemember](https://msdn.microsoft.com/library/ms187750.aspx)
- [sp_columns](https://msdn.microsoft.com/library/ms176077.aspx)
- [sp_configure](https://msdn.microsoft.com/library/ms188787.aspx)
- [sp_datatype_info_90](https://msdn.microsoft.com/library/mt204014.aspx)
- [sp_droprolemember](https://msdn.microsoft.com/library/ms188369.aspx)
- [sp_execute](https://msdn.microsoft.com/library/ff848746.aspx)
- [sp_executesql](https://msdn.microsoft.com/library/ms188001.aspx)
- [sp_fkeys](https://msdn.microsoft.com/library/ms175090.aspx)
- [sp_pdw_add_network_credentials](https://msdn.microsoft.com/library/mt204011.aspx)
- [sp_pdw_database_encryption](https://msdn.microsoft.com/library/mt219360.aspx)
- [sp_pdw_database_encryption_regenerate_system_keys](https://msdn.microsoft.com/library/mt204033.aspx)
- [sp_pdw_log_user_data_masking](https://msdn.microsoft.com/library/mt204023.aspx)
- [sp_pdw_remove_network_credentials](https://msdn.microsoft.com/library/mt204038.aspx)
- [sp_pkeys](https://msdn.microsoft.com/library/ms189813.aspx)
- [sp_prepare](https://msdn.microsoft.com/library/ff848808.aspx)
- [sp_spaceused](https://msdn.microsoft.com/library/ms188776.aspx)
- [sp_special_columns_100](https://msdn.microsoft.com/library/mt204025.aspx)
- [sp_sproc_columns](https://msdn.microsoft.com/library/ms182705.aspx)
- [sp_statistics](https://msdn.microsoft.com/library/ms173842.aspx)
- [sp_tables](https://msdn.microsoft.com/library/ms186250.aspx)
- [sp_unprepare](https://msdn.microsoft.com/library/ff848735.aspx)



## <a name="set-statements"></a>Angiv sætninger

- [ANGIV ANSI_DEFAULTS](https://msdn.microsoft.com/library/ms188340.aspx)
- [ANGIV ANSI_NULL_DFLT_OFF](https://msdn.microsoft.com/library/ms187356.aspx)
- [ANGIV ANSI_NULL_DFLT_ON](https://msdn.microsoft.com/library/ms187375.aspx)
- [SÆT BRUGES ANSI_NULLS](https://msdn.microsoft.com/library/ms188048.aspx)
- [ANGIV ANSI_PADDING-INDSTILLINGER](https://msdn.microsoft.com/library/ms187403.aspx)
- [ANGIV ANSI_WARNINGS](https://msdn.microsoft.com/library/ms190368.aspx)
- [ANGIV ARITHABORT](https://msdn.microsoft.com/library/ms190306.aspx)
- [ANGIV ARITHIGNORE](https://msdn.microsoft.com/library/ms184341.aspx)
- [ANGIV CONCAT_NULL_YIELDS_NULL](https://msdn.microsoft.com/library/ms176056.aspx)
- [ANGIV DATEFIRST](https://msdn.microsoft.com/library/ms181598.aspx)
- [ANGIV DATOFORMAT](https://msdn.microsoft.com/library/ms189491.aspx)
- [ANGIV FMTONLY](https://msdn.microsoft.com/library/ms173839.aspx)
- [ANGIV IMPLICIT_TRANSACITONS](https://msdn.microsoft.com/library/ms187807.aspx)
- [ANGIV LOCK_TIMEOUT](https://msdn.microsoft.com/library/ms189470.aspx)
- [ANGIV NUMBERIC_ROUNDABORT](https://msdn.microsoft.com/library/ms188791.aspx)
- [ANGIV QUOTED_IDENTIFIER](https://msdn.microsoft.com/library/ms174393.aspx)
- [ANGIV ROWCOUNT](https://msdn.microsoft.com/library/ms188774.aspx)
- [ANGIV TEXTSIZE](https://msdn.microsoft.com/library/ms186238.aspx)
- [ANGIV TRANSAKTION ISOLATIONSNIVEAU](https://msdn.microsoft.com/library/ms173763.aspx)
- [ANGIV XACT_ABORT](https://msdn.microsoft.com/library/ms188792.aspx)


## <a name="next-steps"></a>Næste trin
Du kan finde flere oplysninger, [SQL Data Warehouse Referenceoversigt][].

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse Referenceoversigt]: sql-data-warehouse-overview-reference.md

<!--MSDN references-->
