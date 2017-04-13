<properties
    pageTitle="Gemme og få vist diagnosticering dataene i Azure lager | Microsoft Azure"
    description="Hente Azure diagnosticering data til Azure-lager og få den vist"
    services="cloud-services"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor="tysonn" />
<tags
    ms.service="cloud-services"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/01/2016"
    ms.author="robb" />

# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Gemme og få vist diagnosticering data i Azure-lager

Diagnosticering data gemmes ikke permanent, medmindre du overføre den til Microsoft Azure-lager emulatoren eller til Azure-lager. Én gang i lagring, den kan vises med en af flere tilgængelige værktøjer.

## <a name="specify-a-storage-account"></a>Angiv en konto, lagerplads

Du angiver lagerplads-konto, du vil bruge i filen ServiceConfiguration.cscfg. Kontooplysninger er defineret som en forbindelsesstreng i en indstilling for søgekonfiguration. I følgende eksempel viser den standard forbindelsesstreng, der er oprettet for et nyt projekt skybaseret tjeneste i Visual Studio:


```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Du kan ændre denne forbindelsesstreng for at give kontooplysninger for en Azure-lager-konto.

Afhængigt af typen diagnosticering data, der indsamles, bruger Azure diagnosticering tjenesten Blob eller tjenesten tabel. I følgende tabel vises de datakilder, som er bevaret og deres format.

|Datakilde|Lagerplads format|
|---|---|
|Azure logfiler|Tabel|
|IIS 7.0 logfiler|BLOB|
|Azure diagnosticering infrastruktur logfiler|Tabel|
|Mislykkedes anmode om spore logfiler|BLOB|
|Windows-hændelseslogge|Tabel|
|Tællere i ydeevne|Tabel|
|Crashdumps|BLOB|
|Brugerdefineret fejllogge|BLOB|

## <a name="transfer-diagnostic-data"></a>Overføre diagnosticering data

For SDK 2,5 og nyere kan ske anmodningen til at overføre diagnosticering data via konfigurationsfilen. Du kan overføre diagnosticering data med planlagte intervaller som angivet i konfigurationen.

SDK 2.4 og tidligere kan du anmode om at overføre diagnosticering data via konfigurationsfilen som fra et program. Den programmeringsmæssige tilgang kan du også gøre efter behov overførsler.


>[AZURE.IMPORTANT] Når du overfører diagnosticering data til en Azure-lager-konto, påløber der omkostninger for de ressourcer, lagerplads, der bruger dataene diagnosticering.

## <a name="store-diagnostic-data"></a>Diagnosticering lagerdata

Logdata er gemt i Blob eller tabel storage med følgende navne:

**Tabeller**

- **WadLogsTable** - logfiler, der er skrevet i kode ved hjælp af lytteren sporing.

- **WADDiagnosticInfrastructureLogsTable** - diagnosticering skærm og konfiguration ændres.

- **WADDirectoriesTable** – kataloger, som den diagnosticering skærm overvåger.  Dette omfatter IIS-logfiler, IIS mislykkedes anmodning om logfiler og brugerdefinerede mapper.  Placeringen af logfilen blob er angivet i feltet objektbeholder og navnet på blob i feltet RelativePath.  Feltet AbsolutePath angiver placeringen af og navnet på fil, som fandtes på en Azure virtuel maskine.

- **WADPerformanceCountersTable** – tællere i ydeevne.

- **WADWindowsEventLogsTable** – Windows-hændelseslogge.

**BLOB**

- **wad-kontrolelement-objektbeholder** – indeholder (kun for SDK 2.4 og tidligere) de filer, XML-konfiguration, der styrer Azure diagnosticering.

- **wad-iis-failedreqlogfiles** – indeholder oplysninger fra IIS mislykkedes anmode om logfiler.

- **wad-iis-logfiles** – indeholder oplysninger om IIS logfiler.

- **"brugerdefinerede"** – en brugerdefineret objektbeholder, der er baseret på konfiguration af mapper, der overvåges af den diagnosticering skærm.  Navnet på denne blob objektbeholder angives i WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Værktøjer til at få vist diagnosticering data
Flere værktøjer, der er tilgængelige til at få vist dataene, når det er overført til lagerplads. Eksempel:

- Server Explorer i Visual Studio - Hvis du har installeret de Azure-værktøjer til Microsoft Visual Studio, kan du bruge noden Azure-lager i Server Explorer til at få vist skrivebeskyttet blob og tabeldata fra kontiene Azure-lager. Du kan få vist data fra din lokale lager emulator konto og også fra lagerplads konti du har oprettet til Azure. Få mere at vide under [gennemsyn og administrere lagerplads ressourcer med Server Explorer](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md).

- [Microsoft Azure lagerplads Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) er en enkeltstående app, der gør det muligt at arbejde ubesværet med Azure-lager data på Windows, OSX og Linux.

- [Azure Management Studio](http://www.cerebrata.com/products/azure-management-studio/introduction) omfatter Azure diagnosticering Manager, som gør det muligt at få vist, hente og administrere diagnosticering oplysninger der indsamles via programmer, der kører på Azure.


## <a name="next-steps"></a>Næste trin

[Spore strømmen i et Cloud Services til computeren med Azure diagnosticering](cloud-services-dotnet-diagnostics-trace-flow.md)
