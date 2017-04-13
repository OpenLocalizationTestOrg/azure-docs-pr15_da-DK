<properties
    pageTitle="SQL-Database på et lavere niveau klienter understøtter og IP-slutpunktet ændres i forbindelse med overvågning | Microsoft Azure"
    description="Få mere at vide om understøttelse af SQL-Database bagudkompatible klienter og IP-slutpunkt ændringer i forbindelse med overvågning."
    services="sql-database"
    documentationCenter=""
    authors="ronitr"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/10/2016"
    ms.author="ronitr"/>

# <a name="sql-database----downlevel-clients-support-and-ip-endpoint-changes-for-auditing"></a>SQL-Database - understøttelse af bagudkompatible klienter og IP-slutpunkt ændringer i forbindelse med overvågning


[Overvågning](sql-database-auditing-get-started.md) fungerer automatisk med SQL-klienter, der understøtter TDS omdirigering.


##<a id="subheading-1"></a>Understøttelse af bagudkompatible klienter

En klient, som anvender TDS 7.4 bør også understøtter omdirigering. Undtagelser til dette omfatter JDBC 4.0, funktionen omdirigering ikke understøttes fuldt ud og Tedious for Node.JS i hvilke omdirigering blev ikke implementeret.

"Ældre klienter", skal det vil sige hvilke support TDS version 7.3 og under - server fulde Domænenavn på forbindelsen streng ændres til:

Oprindelige server fulde Domænenavn i forbindelsesstrengen: <*servernavn*>. database.windows.net

Ændret server fulde Domænenavn i forbindelsesstrengen: <*servernavn*> .database. **sikker**. windows.net

En delvis liste over "Ældre klienter" omfatter:

- .NET 4.0 og nedenfor,
- ODBC-10.0 og nedenfor.
- JDBC (men JDBC understøtter TDS 7.4, funktionen TDS omdirigering er ikke understøttes fuldt ud)
- Tidskrævende (for Node.JS)

**Bemærkning:** Ovenstående serveren FDQN ændring kan være nyttige for også at anvende en SQL Server niveau overvågning politik uden behov for et trin i konfigurationen i hver database (midlertidige afhjælpning).

##<a id="subheading-2"></a>IP-slutpunktet ændres, når du aktiverer overvågning

Vær opmærksom på, når du aktiverer overvågning, ændres IP-slutpunktet af databasen. Hvis du har faste firewall-indstillinger, skal du opdatere disse firewall-indstillinger i overensstemmelse hermed.

Nyt database IP-slutpunkt, afhænger af databaseområdet:

| Databaseområde | Mulige IP-slutpunkter |
|----------|---------------|
| Kina nord  | 139.217.29.176, 139.217.28.254 |
| Kina øst  | 42.159.245.65, 42.159.246.245 |
| Australien øst  | 104.210.91.32, 40.126.244.159, 191.239.64.60, 40.126.255.94 |
| Australien Sydøst | 191.239.184.223, 40.127.85.81, 191.239.161.83, 40.127.81.130 |
| Brasilien syd  | 104.41.44.161, 104.41.62.230, 23.97.99.54, 104.41.59.191 |
| Centrale USA  | 104.43.255.70, 40.83.14.7, 23.99.128.244, 40.83.15.176 |
| Sydøstasien   | 23.99.125.133, 13.75.40.42, 23.97.71.138, 13.94.43.245 |
| Indtastning af østasiatiske USA 2 | 104.209.141.31, 104.208.238.177, 191.237.131.51, 104.208.235.50 |
| Indtastning af østasiatiske USA   | 23.96.107.223, 104.41.150.122, 23.96.38.170, 104.41.146.44 |
| Central Indien  | 104.211.98.219, 104.211.103.71 |
| Syd Indien   | 104.211.227.102, 104.211.225.157 |
| Vest Indien  | 104.211.161.152, 104.211.162.21 |
| Japan øst   | 104.41.179.1, 40.115.253.81, 23.102.64.207, 40.115.250.196 |
| Japan vest    | 104.214.140.140, 104.214.146.31, 191.233.32.34, 104.214.146.198 |
| Nord centrale USA  | 191.236.155.178, 23.96.192.130, 23.96.177.169, 23.96.193.231 |
| Nord Europe  | 104.41.209.221, 40.85.139.245, 137.116.251.66, 40.85.142.176 |
| Syd centrale USA  | 191.238.184.128, 40.84.190.84, 23.102.160.153, 40.84.186.66 |
| Sydøstasien  | 104.215.198.156, 13.76.252.200, 23.97.51.109, 13.76.252.113 |
| Vest Europe  | 104.40.230.120, 13.80.23.64, 137.117.171.161, 13.80.8.37, 104.47.167.215, 40.118.56.193, 104.40.176.73, 40.118.56.20 |
| Vest USA  | 191.236.123.146, 138.91.163.240, 168.62.194.148, 23.99.6.91 |
| Canada Central  | 13.88.248.106 |
| Canada øst  |  40.86.227.82 |
