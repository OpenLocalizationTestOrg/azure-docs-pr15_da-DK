<properties
    pageTitle="Azure diagnosticering versionshistorik"
    description="Gennemgang af ændringer i de forskellige versioner af Azure diagnosticering som afsendt med andre versioner af Microsoft Azure SDK'er."
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
    ms.date="02/12/2016"
    ms.author="robb"/>


# <a name="microsoft-azure-diagnostics-version-history"></a>Microsoft Azure diagnosticering versionshistorik

Ny bruger af Azure diagnosticering? Se [Oversigt over Azure diagnosticering](azure-diagnostics.md).

Hver version af Azure SDK leveres typisk med en ny version af Azure diagnosticering. Tabellen nedenfor beskrives de Azure SDK og Azure diagnosticering versioner, der er knyttet til SDK udgave.



Azure SDK-version | Azure diagnosticering version | Model
--- | --- | ---
1.x      | 1.0 | plug-in'en
2.0 til 2.4| 1.0 | "
2,5      | 1.2 | filtypenavn
2.6      | 1.3 | "
2.7      | 1.4 | "
2,8      | 1,5 | "


Den nyeste version er 1,5, der blev leveret med Azure SDK 2,8. Versionen af Azure diagnosticering lokalnummer, som leveres med SDK bruges kun til lokale emulator scenarier. Alle installerede programmer anvender automatisk den nyeste version, når der benyttes i Azure, uanset hvor version af SDK programmet er oprettet med. Men, medmindre du installerer den nyeste Azure SDK, skal du muligvis ikke alle de værktøjer, der hjælper dig med at bruge de nye funktioner.

Forskellige funktioner og ændringer, der er beskrevet nedenfor.

## <a name="azure-sdk-28"></a>Azure SDK 2,8
Azure SDK 2,8 føjet muligheden for at sende data til diagnosticering til [Programmet indsigt](./application-insights/app-insights-cloudservices.md) gør det nemmere at diagnosticere problemer på tværs af dit program samt niveauet system og infrastruktur.

## <a name="azure-26-diagnostics-changes"></a>Azure 2.6 diagnosticering ændringer

For Azure SDK 2.6 skybaseret tjeneste projekter i Visual Studio, der blev foretaget følgende ændringer. (Disse ændringer gælder også for senere versioner af Azure SDK.)

- Lokale emulatoren understøtter nu diagnosticering. Dette betyder, at du kan samle diagnosticering data og sikre, at dit program er oprette på rette spor, mens du udvikling og tester i Visual Studio. Forbindelsesstrengen `UseDevelopmentStorage=true` gør det muligt for diagnosticering dataindsamling, mens du kører projektet skybaseret tjeneste i Visual Studio ved hjælp af emulatoren Azure-lager. Alle diagnosticering data indsamles i kontoen (udvikling Storage) lagerplads.

- Diagnosticering lagerplads konto forbindelsesstrengen (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) er gemt igen i service-konfigurationsfil (.cscfg). I Azure SDK 2,5 blev diagnosticering lagerplads kontoen angivet i filen diagnostics.wadcfgx.

Der er nogle vigtige forskelle mellem hvordan forbindelsesstrengen lykkedes i Azure SDK 2.4 og tidligere, og hvordan det fungerer i Azure SDK 2.6 og nyere.

- I Azure SDK 2.4 og tidligere blev forbindelsesstrengen brugt som et runtime af diagnosticering plug-in'en til at få kontooplysninger lagerplads til at overføre logge til diagnosticering.

- I Azure SDK 2.6 og nyere bruges forbindelsesstrengen diagnosticering i Visual Studio til at konfigurere filtypenavnet diagnosticering med passende lager kontooplysninger under udgivelsen. Forbindelsesstrengen kan du angive forskellige lagerplads konti til anden tjeneste konfigurationer, Visual Studio vil bruge, når du udgiver. Da plug-in'en til diagnosticering er ikke længere tilgængelig (efter Azure SDK 2,5), kan filen .cscfg alene kan ikke aktivere filtypenavnet diagnosticering. Du skal aktivere udvidelsen separat via funktioner som Visual Studio eller PowerShell.

- For at forenkle processen med at konfigurere filtypenavnet diagnosticering med PowerShell indeholder pakke output fra Visual Studio også offentlige konfigurationen XML for filtypenavnet diagnosticering for hver rolle. Visual Studio anvender forbindelsesstreng diagnosticering til at udfylde kontooplysninger lagerplads findes i den offentlige konfiguration. Offentlige config-filer er oprettet i mappen filtypenavne og følge mønsteret PaaSDiagnostics. <RoleName>. PubConfig.xml. En hvilken som helst PowerShell baseret installationer kan bruge denne mønster tilknytte hver konfiguration til en rolle.

- Forbindelsesstrengen i filen .cscfg bruges også i portalen Azure til at få adgang til diagnosticering dataene, så den kan vises på fanen **overvågnings** . Forbindelsesstrengen skal bruges til at konfigurere tjenesten for at få vist detaljeret overvågningsdata på portalen.

### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Overføre projekter til Azure SDK 2.6 og nyere

Når du overfører fra Azure SDK 2,5 til Azure SDK 2.6 eller nyere, hvis du har en diagnosticering lagerplads konto, der er angivet i filen .wadcfgx, derefter forbliver det der. For at kunne udnytte mulighed for at bruge forskellige lagerplads konti til forskellige lagerplads konfigurationer, har du manuelt føje forbindelsesstrengen til projektet. Hvis du flytter et projekt fra Azure SDK 2.4 eller tidligere til Azure SDK 2.6, bevares diagnosticering forbindelsesstrenge. Vær opmærksom på ændringer i hvordan strenge er behandlet i Azure SDK 2.6 som angivet i forrige afsnit.

### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Hvordan Visual Studio afgør kontoen diagnosticering lagerplads

- Hvis en diagnosticering forbindelsesstreng er angivet i filen .cscfg, bruges Visual Studio til at konfigurere filtypenavnet diagnosticering, når du udgiver, og når der genereres offentlige konfiguration XML-filer under emballagen.

- Hvis strengen ikke diagnosticering forbindelse er angivet i filen .cscfg, derefter går Visual Studio tilbage til ved hjælp af kontoen lagerplads, der er angivet i filen .wadcfgx til at konfigurere filtypenavnet diagnosticering, når publicere og genereres offentlige konfiguration XML-filer, når emballagen.

- Diagnosticering forbindelsesstrengen i filen .cscfg tilsidesætter kontoen lagerplads i filen .wadcfgx. Hvis en diagnosticering forbindelsesstreng er angivet i filen .cscfg, derefter Visual Studio bruger, og ignorerer lagerplads kontoen i .wadcfgx.

### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>Hvad betyder "Opdater udvikling lagerplads forbindelse strengene..." afkrydsningsfeltet gøre?

Afkrydsningsfeltet for **opdatering udvikling lagerplads forbindelsesstrenge til diagnosticering og cachelagring med Microsoft Azure lagerplads kontolegitimationsoplysninger, når du udgiver til Microsoft Azure** giver dig en nem måde at opdatere en hvilken som helst udvikling lagerplads konto forbindelsesstrenge med den angivne under udgivelsen Azure-lager-konto.

Antag f.eks., du markerer dette afkrydsningsfelt og diagnosticering forbindelsesstrengen angiver `UseDevelopmentStorage=true`. Når du publicerer projektet til Azure, opdateres Visual Studio automatisk diagnosticering forbindelsesstrengen med kontoen lagerplads du har angivet i guiden Udgiv. Men hvis en reelle lagerplads konto er angivet som forbindelsesstrengen diagnosticering, så kontoen bruges i stedet.

## <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Diagnosticering funktionalitet forskellene mellem Azure SDK 2.4 og tidligere og Azure SDK 2,5 og nyere

Hvis du opgraderer projektet fra Azure SDK 2.4 til Azure SDK 2,5 eller nyere, skal du huske indeholde følgende diagnosticering funktionalitet forskelle.

- **Konfiguration af API'er frarådes** – programmeringsmæssig konfigurationen af diagnosticering er tilgængelig i Azure SDK 2.4 eller tidligere versioner, men frarådes i Azure SDK 2,5 og nyere. Hvis konfigurationen af diagnosticering der aktuelt er angivet i kode, skal du konfigurere disse indstillinger fra bunden i overførte projektet for at diagnosticering for at fortsætte med at arbejde. Konfigurationsfil diagnosticering til Azure SDK 2.4 er diagnostics.wadcfg og diagnostics.wadcfgx for Azure SDK 2,5 og nyere.

- **Diagnosticering til skyen tjenesteprogrammer kan kun konfigureres på niveauet rolle ikke på niveauet for forekomst.**

- **Hver gang du installerer din app konfigurationen af diagnosticering er opdateret** – dette kan medføre problemer med fungerer ensartet, hvis du ændrer konfigurationen af diagnosticering fra Server Explorer og geninstaller derefter din app.

- **I Azure SDK 2,5 og nyere, nedbrud gemmer er konfigureret i konfigurationsfil diagnosticering ikke i kode** – Hvis du har Crashdumps, der er konfigureret i kode, har du manuelt skifte konfigurationen fra kode til konfigurationsfil, da Crashdumps ikke overføres under overførslen til Azure SDK 2.6.
