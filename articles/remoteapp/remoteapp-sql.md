<properties
   pageTitle="SQL Azure med Azure RemoteApp | Microsoft Azure"
   description="Få mere at vide, hvordan du bruger SQL Azure med Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="ericorman"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# <a name="sql-azure-with-azure-remoteapp"></a>SQL Azure med Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Ofte, når kunder vælger til at hoste deres Windows-programmer i skyen med Azure RemoteApp vil de også overføre deres data som SQL-servere til skyen for en hel skyen installation. Dette giver mulighed for hele skyen hostet løsning, der kan åbnes når som helst af enhver enhed hvor som helst med Azure RemoteApp. Nedenfor finder du links og referencer sammen med vejledning til at hjælpe dig med denne proces.  

## <a name="migrate-your-sql-data"></a>Overføre dine SQL-data

Start med at [overføre en SQL Server-database til Azure SQL-Database](../sql-database/sql-database-cloud-migrate.md). 

## <a name="configure-azure-remoteapp"></a>Konfigurere Azure RemoteApp
Være vært for din Windows-program i Azure RemoteApp. Nedenfor vises en meget høj grad trinvise:

1.     Opret [Azure RemoteApp skabelon VM](remoteapp-imageoptions.md). 
2.     Installere det nødvendige program på VM.
3.     Konfigurere programmet, så den opretter forbindelse til den SQL-database, og Bekræft, at den fungerer.
4.     Sysprep og lukning VM. Tag dette som et billede til brug sammen med Azure. **Note:** Du skal sikre, at programmet er i stand til at bevare forbindelsesoplysninger DB gennem sysprep processen. Hvis programmet er i stand til at bevare forbindelsesoplysningerne DB, vil du måske gerne deltage leverandør af programmet til at kontrollere, hvordan vi kan angive forbindelsesstrengen.
5.     Importere det brugerdefinerede billede i biblioteket Azure RemoteApp at vælge den korrekte geografiske placering, som din SQL Azure-installation er placeret. 
6.     Installere en RemoteApp samling i den samme datacenter som din SQL Azure-installation, ved hjælp af skabelonen ovenfor og publicere programmet. Implementere Azure RemoteApp i den samme datacenter som din SQL Azure installation hjælper med at sikre, at de hurtigste forbindelseshastigheder og reducere ventetid. 

## <a name="app-and-sql-configuration-considerations"></a>App og SQL konfiguration overvejelser:
Der er et par punkter du bør overveje, når du bruger SQL Azure med RemoteApp:

Lær, [hvordan du konfigurerer en Azure SQL database-firewallen](../sql-database/sql-database-firewall-configure.md). Et uddrag fra stater artikel "i første omgang alle adgang til din Azure SQL-databaseserver er blokeret af firewallen. For at begynde at bruge din Azure SQL-databaseserver, skal du gå til portalen klassisk og angive én eller flere serverniveau firewallregler, som giver adgang til din server Azure SQL-Database. Brug firewallreglerne til at angive, hvilke IP-adresseområder fra internettet er tilladt, og hvorvidt Azure-programmer kan forsøge at oprette forbindelse til din Azure SQL-databaseserver."

Også, når en computer, der forsøger at oprette forbindelse til din databaseserver fra internettet, firewallen kontrollerer oprindelige IP-adressen for din anmodning mod det samlede sæt af serverniveau og (hvis det er nødvendigt) database niveau firewallregler. "Hvis IP-adressen for din anmodning er placeret i et af de områder, der er angivet i serverniveau firewallreglerne, forbindelsen er tildelt til Azure SQL-databaseserver." Derfor skal vi kan gøre brug af IP-områder og ikke blot individuelle kilde-IP-adresser.

Følg de trinvise instruktioner i [Sådan: konfigurere firewall-indstillinger på SQL-Database ved hjælp af portalen Azure](../sql-database/sql-database-configure-firewall-settings.md) til at angive IP-området. Når du konfigurerer SQL firewallregler, skal du angive IP-området i det undernet, der er angivet til samlingen Azure RemoteApp. Dette skulle give ARA-servere at oprette forbindelse til den SQL-database, selvom de har dynamisk-tildeles IP-adresser.

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med
Hvis oplevelsen ved at bruge en klientprogrammet hostes i Azure RemoteApp, der opretter forbindelse til en SQL-database hvor hostes på Azure eller lokalt er langsomt der kan være et par mulige årsager hvorfor.  

- Netværksventetid fra enheden til Azure er høj. Flytte til den bedste og hurtigste netværksforbindelse, kan du opnår den bedste ydeevne. Bruge [azurespeed.com](http://azurespeed.com/) som et generelt værktøj til at teste din enheder ventetid til Azure datacenter.  
- Klient-app hostet i Azure RemoteApp er belastet. Vælge en anden fakturering plan som Premium fakturering, vil det forbedre ydeevnen. En anden hemmeligheden er til at overvåge de ressourcer, der bruger dit program: udføre en ctrl-alt-end-sekvens som Start skærmbilledet SAS, Vælg Jobliste og overhold ressource anvendelsen for din app under en aktiv session.
- SQL server er belastet eller er optimeret ikke. Følg SQL vejledning til fejlfinding. 

