<properties 
   pageTitle="Administrere Azure automatisering data | Microsoft Azure"
   description="I denne artikel indeholder flere emner til administration af et Azure automatisering miljø.  I øjeblikket omfatter dataopbevaring og sikkerhedskopiere Azure automatisering nedbrud i Azure Automation."
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/02/2016"
   ms.author="bwren;sngun" />

# <a name="managing-azure-automation-data"></a>Administrere Azure automatisering data

I denne artikel indeholder flere emner til administration af et Azure automatisering miljø.

## <a name="data-retention"></a>Dataopbevaring

Når du sletter en ressource i Azure automatisering, bevares den i 90 dage til overvågning før der fjernes permanent.  Du kan ikke se eller bruge ressourcen i dette tidsrum.  Denne politik gælder også for ressourcer, der tilhører en automation-konto, der er blevet slettet.

Azure automatisering slettes automatisk og fjerner permanent job, der er ældre end 90 dage.

Den følgende tabel opsummerer opbevaringspolitikken for forskellige ressourcer.

|Data|Politik|
|:---|:---|
|Konti|Fjernes permanent 90 dage efter kontoen, der er blevet slettet af en bruger.|
|Aktiver|Fjernes permanent 90 dage efter aktivet er blevet slettet af en bruger eller 90 dage efter den konto, der indeholder aktivet er blevet slettet af en bruger.|
|Moduler|Fjernes permanent 90 dage efter modulet, der er blevet slettet af en bruger eller 90 dage efter den konto, der indeholder modulet er blevet slettet af en bruger.|
|Runbooks|Fjernes permanent 90 dage efter ressourcen, der er blevet slettet af en bruger eller 90 dage efter den konto, der indeholder ressourcen er blevet slettet af en bruger.|
|Job|Slettede og permanent fjernede 90 dage efter de sidste bliver ændret. Det kan skyldes når jobbet er fuldført, er ikke længere eller afbrydes midlertidigt.|
|Node konfigurationer/MOF-filer| Gamle node konfiguration fjernes permanent 90 dage, når der oprettes en ny node konfiguration.|
|DTK noder| Fjernes permanent 90 dage efter noden er fjernet fra automatisering konto ved hjælp af Azure portal eller [Fjerner registreringen af AzureRMAutomationDscNode](https://msdn.microsoft.com/library/mt603500.aspx) -cmdlet i Windows PowerShell. Noder fjernes også permanent 90 dage efter den konto, der indeholder noden er blevet slettet af en bruger. |
|Node rapporter| Fjernes permanent 90 dage, når der oprettes en ny rapport for denne node|

Opbevaringspolitikken gælder for alle brugere og i øjeblikket ikke kan tilpasses.

## <a name="backing-up-azure-automation"></a>Sikkerhedskopiere Azure automatisering

Når du sletter en automatisering konto i Microsoft Azure, slettes alle objekter i kontoen herunder runbooks, moduler, konfigurationer, indstillinger, job og aktiver. Objekterne, der kan ikke gendannes, når kontoen er blevet slettet.  Du kan bruge følgende oplysninger til at sikkerhedskopiere indholdet af din konto med automation inden det slettes. 

### <a name="runbooks"></a>Runbooks

Du kan eksportere dine runbooks til scriptfiler ved hjælp af portalen Azure Management eller Cmdletten [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx) i Windows PowerShell.  Disse scriptfiler kan importeres til en anden konto med automation, som beskrevet i [oprette eller importere en Runbook](https://msdn.microsoft.com/library/dn643637.aspx).


### <a name="integration-modules"></a>Af integrationsmoduler

Du kan ikke eksportere integrationsmoduler fra Azure automatisering.  Du skal sikre dig, at de er tilgængelige uden for den automatiske konto.

### <a name="assets"></a>Aktiver

Du kan ikke eksportere [Aktiver](https://msdn.microsoft.com/library/dn939988.aspx) fra Azure automatisering.  Ved hjælp af portalen Azure Management, skal du notere detaljerne for variabler, legitimationsoplysninger, certifikater, forbindelser og tidsplaner.  Derefter skal du manuelt oprette en hvilken som helst aktiver, der anvendes af runbooks, du importerer til en anden automatisering.

Du kan bruge [Azure-cmdletter](https://msdn.microsoft.com/library/dn690262.aspx) for at hente oplysninger om ikke-krypteret aktiver og enten gemme dem til fremtidig brug eller oprette tilsvarende aktiver i en anden konto med automation.

Du kan hente værdien for krypterede variabler eller adgangskodefeltet af legitimationsoplysninger ved hjælp af cmdletter.  Hvis du ikke kender disse værdier, kan du hente dem fra en runbook ved hjælp af [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) og [Få AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx) aktiviteter.

Du kan ikke eksportere certifikater fra Azure automatisering.  Du skal sikre dig, at der er tilgængelige uden for Azure en hvilken som helst certifikater.

### <a name="dsc-configurations"></a>DTK konfigurationer

Du kan eksportere dine konfigurationer til scriptfiler ved hjælp af portalen Azure Management eller Cmdletten [Eksportér AzureRmAutomationDscConfiguration](https://msdn.microsoft.com/library/mt603485.aspx) i Windows PowerShell. Disse konfigurationer kan importeres og benyttes i en anden konto med automation.


##<a name="geo-replication-in-azure-automation"></a>Geografisk-gentagelse i Azure Automation

Geografisk-gentagelse, standard i Azure automatisering konti sikkerhedskopierer kontodata til et andet geografisk område til redundans. Du kan vælge et primære område, når du konfigurerer din konto, og derefter en sekundær område er tildelt til den automatisk. Den sekundære data kopieret fra det primære region, opdateres løbende i tilfælde af tab af data.  

Følgende tabel viser de tilgængelige primære og sekundære område-par.

|Primære            |Sekundær
| ---------------   |----------------
|Syd centrale USA   |Nord centrale USA
|USA Øst 2          |Centrale USA
|Vest Europe        |Nord Europe
|Syd Østasien    |Sydøstasien
|Japan øst         |Japan vest

I sandsynlighed begivenheden, en primær områdedata går tabt, forsøger Microsoft at gendanne den. Hvis den primære data ikke kan gendannes, derefter geografisk failover er udført, og de pågældende kunder får besked om det via deres abonnement.

