<properties
    pageTitle="Fejlfinding i forbindelse med Azure diagnosticering"
    description="Foretage fejlfinding af problemer, når du bruger Azure diagnosticering i Azure Cloud Services, virtuelle maskiner og "
    services="multiple"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="robb"/>


# <a name="azure-diagnostics-troubleshooting"></a>Azure diagnosticering fejlfinding
Fejlfinding i forbindelse med oplysninger, der er relevante for brug af Azure diagnosticering. Du kan finde flere oplysninger om Azure diagnosticering, [Azure diagnosticering oversigt](azure-diagnostics.md#cloud-services).

## <a name="azure-diagnostics-is-not-starting"></a>Azure diagnosticering starter ikke

Diagnosticering består af to komponenter: en gæst agent plug-in og overvågning agent. Du kan se logfilerne **DiagnosticsPluginLauncher.log** og **DiagnosticsPlugin.log** oplysninger om, hvorfor diagnosticering kan ikke startes.  
  
Logfiler for gæst agent plug-in findes i en skybaseret tjeneste rolle i: 

``` 
C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.6.3.0\ 
``` 

I en Azure virtuelt, er logfiler for gæst agent plug-in placeret i: 

``` 
C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.6.3.0\Logs\ 
``` 
 
Den sidste linje i logfilerne skal indeholde afslutningskoden.  

``` 
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0 
``` 

Plug-in'et returnerer følgende afslutningskoder:

Afslutningskode|Beskrivelse
---|---
0|Succes.
-1|Generel fejl.
-2.|Kan ikke indlæses filen rcf.<p>Dette er en intern fejl, der skal kun udføres, hvis starteren gæst agent plug-in aktiveres forkert, manuelt på VM.
-3|Kan ikke indlæses konfigurationsfil diagnosticering.<p><p>Løsning: Dette er resultatet af en konfigurationsfil, der ikke passerer validering af skema. Løsningen er at tilvejebringe et konfigurationsfil, der er i overholder med skemaet.
-4|En anden forekomst af overvågning agent diagnosticering bruger allerede mappen lokale ressourcer.<p><p>Løsning: Angiv en anden værdi for **LocalResourceDirectory**.
-6|Starteren gæst agent plug-in forsøgte at åbne diagnosticering med en ugyldig kommandolinjen.<p><p>Dette er en intern fejl, der skal kun udføres, hvis starteren gæst agent plug-in aktiveres forkert, manuelt på VM.
-10|Plug-in'en til diagnosticering forladt med en ikke-afviklet undtagelse.
-11|Gæst agent kunne ikke oprette processen ansvarlig for at åbne og overvågning overvågning agent.<p><p>Løsning: Kontrollér, at tilstrækkelige systemressourcer er tilgængelige for Start nye processer.<p>
-101|Ugyldige argumenter, når du ringer til diagnosticering plug-in'et.<p><p>Dette er en intern fejl, der skal kun udføres, hvis starteren gæst agent plug-in aktiveres forkert, manuelt på VM.
-102|Processen plug-in'en kan ikke initialiseret sig selv.<p><p>Løsning: Kontrollér, at tilstrækkelige systemressourcer er tilgængelige for Start nye processer.
-103|Processen plug-in'en kan ikke initialiseret sig selv. Det er specifikt kan ikke oprette objektet logføring.<p><p>Løsning: Kontrollér, at tilstrækkelige systemressourcer er tilgængelige for Start nye processer.
-104|Kan ikke indlæses filen rcf leveret af gæst agent.<p><p>Dette er en intern fejl, der skal kun udføres, hvis starteren gæst agent plug-in aktiveres forkert, manuelt på VM.
-105|Diagnosticerings-plug-in'en kan ikke åbne konfigurationsfil diagnosticering.<p><p>Dette er en intern fejl, der skal kun udføres, hvis plug-in'en til diagnosticering aktiveres forkert, manuelt på VM.
-106|Kan ikke læse konfigurationsfil diagnosticering.<p><p>Løsning: Dette er resultatet af en konfigurationsfil, der ikke passerer validering af skema. Så er løsningen at tilvejebringe et konfigurationsfil, der er i overholder med skemaet. Du kan finde den XML, der er leveret til filtypenavnet diagnosticering i mappen *%SystemDrive%\WindowsAzure\Config* på VM. Åbn den relevante XML-fil og søge i **Microsoft.Azure.Diagnostics**og derefter i feltet **xmlCfg** . Dataene er base64-kodet, så du skal [afkode den](http://www.bing.com/search?q=base64+decoder) at se den XML, der er blevet indlæst af diagnosticering.<p>
-107|Ressource directory passet til overvågning agent er ugyldig.<p><p>Dette er en intern fejl, der skal kun udføres, hvis overvågning agent aktiveres forkert, manuelt på VM.</p>
-108    |Der kan ikke konvertere konfigurationsfil diagnosticering til overvågning agent-konfigurationsfil.<p><p>Dette er en intern fejl, der skal kun udføres, hvis plug-in'en til diagnosticering startes manuelt med en ugyldig konfigurationsfil.
-110|Generelt diagnosticering konfigurationsfejl.<p><p>Dette er en intern fejl, der skal kun udføres, hvis plug-in'en til diagnosticering startes manuelt med en ugyldig konfigurationsfil.
-111|Kan ikke startes overvågning agent.<p><p>Løsning: Kontrollér, at tilstrækkelige systemressourcer er tilgængelige.
-112|Generel fejl


## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Diagnosticering Data er ikke logget til Azure-lager
Azure diagnosticering gemmer alle data i Azure-lager.

Den mest almindelige årsag til mangler begivenhed data er forkert defineret lagerplads kontooplysninger.

Løsning: Ret din konfigurationsfil til diagnosticering og geninstallere diagnosticering.
Hvis problemet fortsætter, efter at geninstallere filtypenavnet diagnosticering, og du kan være nødvendigt at foretage fejlfinding af yderligere ved at kigge på en hvilken som helst overvågning agent fejl. Før begivenheden data er overført til kontoen lagerplads gemmes i LocalResourceDirectory.

Til skyen Service rolle er LocalResourceDirectory:

    C:\Resources\Directory\<CloudServiceDeploymentID>.<RoleName>.DiagnosticStore\WAD<DiagnosticsMajorandMinorVersion>\Tables

For virtuelle maskiner er LocalResourceDirectory:

    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD<DiagnosticsMajorandMinorVersion>\Tables

Hvis der er ingen filer i mappen LocalResourceDirectory, er den overvågning agent kunne ikke startes. Dette skyldes typisk en ugyldig konfigurationsfil en begivenhed, der skal rapporteres i CommandExecution.log.

Hvis den overvågning Agent korrekt indsamle begivenhed data vises .tsf filer for hver enkelt hændelse, der er defineret i din konfigurationsfil. Agent for overvågning logger dens fejl i filen MaEventTable.tsf. Du kan bruge programmet tabel2csv konvertere filen .tsf til et komma adskilt values(.csv) fil for at undersøge indholdet af denne fil:

På en skybaseret tjeneste rolle:

    %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

*% Systemdrev %* på en skybaseret tjeneste rolle er typisk D:

På en virtuel maskine:

    C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

Ovenstående kommandoer genererer den log fil *maeventtable.csv*, som du kan åbne og undersøge til fejlmeddelelser.    


## <a name="diagnostics-data-tables-not-found"></a>Diagnosticering data tabeller, der blev ikke fundet
Tabellerne i Azure lagerplads holder Azure diagnosticering data er navngivet ved hjælp af nedenstående kode:

        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;

Her er et eksempel:

        <EtwEventSourceProviderConfiguration provider=”prov1”>
          <Event id=”1” />
          <Event id=”2” eventDestination=”dest1” />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider=”prov2”>
          <DefaultEvents eventDestination=”dest2” />
        </EtwEventSourceProviderConfiguration>

Der genereres 4 tabeller:

Begivenhed|Tabelnavn
---|---
Provider = "prov1" &lt;begivenhed id = "1" /&gt;|WADEvent + MD5("prov1") + "1"
Provider = "prov1" &lt;begivenhed id = "2" eventDestination = "dest1" /&gt;|WADdest1
Provider = "prov1" &lt;DefaultEvents /&gt;|WADDefault+MD5("prov1")
Provider = "prov2" &lt;DefaultEvents eventDestination = "dest2" /&gt;|WADdest2
