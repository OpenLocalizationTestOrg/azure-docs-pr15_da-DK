<properties
   pageTitle="Introduktion til Azure log integration | Microsoft Azure"
   description="Lær, hvordan du installerer tjenesten Azure log integration og integrere logfiler fra Azure-lager, Azure overvågningslogge og Azure Sikkerhedscenter beskeder."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2016"
   ms.author="TomSh"/>

# <a name="get-started-with-azure-log-integration-preview"></a>Introduktion til Azure log integration (Preview)

Azure log integration gør det muligt at integrere rå logfiler fra dine Azure ressourcer i til din lokale sikkerhedsoplysninger og begivenhed Management (SIEM). Denne integration giver et samlet dashboard for alle dine aktiver, lokalt eller i skyen, så du kan samle, koordinere, analysere og Giv besked om sikkerhed begivenheder, der er knyttet til dine programmer.

Dette selvstudium vejleder dig gennem hvordan du installerer Azure log integration og integrere logfiler fra Azure-lager, Azure overvågningslogge og Azure Sikkerhedscenter beskeder. Anslået tid at gennemføre selvstudiet er en time.

## <a name="prerequisites"></a>Forudsætninger

For at fuldføre dette selvstudium, skal du have følgende:

- En maskine (lokale eller i skyen) til at installere tjenesten Azure log integration. Denne computer skal køre en 64-bit Windows-Operativsystem med .net 4.5.1 installeret. Denne computer kaldes **Azlog Integrator**.
- Azure-abonnement. Hvis du ikke har en, kan du tilmelde dig en [gratis konto](https://azure.microsoft.com/free/).
- Azure diagnosticering aktiveret for din Azure virtuelle maskiner (VM'er). Aktivere diagnosticering til Cloud Services, skal du se [Aktivere Azure diagnosticering i Azure Cloud Services](../cloud-services/cloud-services-dotnet-diagnostics.md). Aktivere diagnosticering til en Azure VM, der kører Windows, skal du se [Bruge PowerShell til at aktivere Azure diagnosticering i et virtuelt kører Windows](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md).
- Forbindelse fra Azlog Integratoren til Azure-lager og til at godkende og Godkend Azure-abonnement.
- Agent for SIEM (for eksempel Splunk Universal domænes, HP ArcSight Windows begivenhed indsamler agent eller IBM QRadar WinCollect) skal være installeret på Azlog Integratoren for Azure VM loggene.

## <a name="deployment-considerations"></a>Overvejelser om installation

Du kan køre flere forekomster af Azlog Integratoren, hvis begivenheden lydstyrken er høj. Justering af belastning af Azure diagnosticering lagerplads konti til Windows *(WAD)* og antallet af abonnementer til at levere til forekomsterne skal være baseret på din kapacitet.

På en 8-processor (kerne) maskine, kan en enkelt forekomst af Azlog Integrator behandle om 24 millioner hændelser af hver dag (~1M/hour).

På en 4-processor (kerne) maskine, kan en enkelt forekomst af Azlog Integrator behandle om 1,5 million hændelser af hver dag (~62.5K/hour).

## <a name="install-azure-log-integration"></a>Installer Azure log integration

Hent [Azure logge integration](https://www.microsoft.com/download/details.aspx?id=53324).

Tjenesten Azure log integration indsamler telemetridata fra den maskine, som den er installeret.  Telemetridata, der indsamles er:

- Undtagelser, der opstår under udførelse af Azure logge integration
- Målepunkter om antallet af forespørgsler og hændelser behandlet
- Statistik om hvilke Azlog.exe kommandolinjeparametre bliver brugt

> [AZURE.NOTE] Du kan slå fra samling af telemetridata ved fravalg af denne indstilling.

## <a name="integrate-azure-vm-logs-from-your-azure-diagnostics-storage-accounts"></a>Integrere Azure VM logfiler fra Azure diagnosticering lagerplads konti

1. Markér de forudsætninger, der er nævnt ovenfor til at sikre, at kontoen WAD lagerplads indsamler logfiler før du fortsætter din Azure log integration. Ikke udføre følgende trin, hvis kontoen WAD lagerplads ikke er indsamling af logfiler.

2. Åbn kommandoprompten og **cd** til **c:\Program Files\Microsoft Azure Log Integration**.

3. Kør kommandoen

        azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>

      Erstat StorageAccountName med navnet på den Azure-lager-konto, der er konfigureret til at modtage diagnosticering begivenheder fra din VM.

        azlog source add azlogtest WAD azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==

      Hvis du vil have abonnement-id vises i XML, skal du føje abonnement-ID til det fulde navn:

        azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>

4. Vente 30-60 minutter (det kan tage op til en time) og derefter få vist de hændelser, der er trukket fra kontoen lagerplads. For at få vist, åbne **Logbog > Windows-logger > videresendt begivenheder** på Azlog Integratoren.

5. Sørg for, at den standard SIEM forbindelse, der er installeret på computeren er konfigureret til at vælge begivenheder fra mappen **Videresendt begivenheder** og pipe dem til din SIEM forekomst. Gennemse SIEM specifikt er konfigureret for at konfigurere og se loggene integrering.

## <a name="what-if-data-is-not-showing-up-in-the-forwarded-events-folder"></a>Hvad nu, hvis dataene ikke vises i mappen videresendt begivenheder?

Hvis efter en time data ikke vises i mappen **Videresendt begivenheder** derefter:

1. Markér maskinen, og Bekræft, at det kan få adgang til Azure. Prøv at åbne [Azure portal](http://portal.azure.com) fra browseren for at teste forbindelsen.

2. Kontrollér, at brugeren konto **azlog** har skrivetilladelse på mappen **users\azlog**.

3. Kontrollér, at kontoen lagerplads, der er tilføjet i kommandoen **azlog kilde tilføje** der vises, når du kører kommandoen **azlog kildelisten**.
4. Gå til **Logbog > Windows-logger > programmet** til at se, om der er fejl rapporteret fra Azure log integrationen.

Hvis du stadig ikke kan se hændelserne, derefter:

1. Hente [Microsoft Azure-lager Explorer](http://storageexplorer.com/).

2. Oprette forbindelse til kontoen lagerplads, der er tilføjet i kommandoen **azlog kilde tilføje**.

3. Gå til tabel **WADWindowsEventLogsTable** at se, om der er nogle data i Microsoft Azure-lager Stifinder. Hvis ikke, derefter diagnosticering i VM er ikke konfigureret korrekt.

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>Integrere Azure overvågningslogge og Sikkerhedscenter beskeder

1. Åbn kommandoprompten og **cd** til **c:\Program Files\Microsoft Azure Log Integration**.

2. Kør kommandoen

        azlog createazureid

      Denne kommando beder dig om dit Azure logon. Kommandoen opretter derefter en [Azure Active Directory Service hovedstolen](../active-directory/active-directory-application-objects.md) i de Azure AD-lejere, der hoster de Azure abonnementer, hvor den bruger logget på er Administrator, en samtidig Administrator eller en ejer. Kommandoen mislykkes, hvis den bruger logget på er kun en gæstebruger i Azure AD-lejer. Godkendelse til Azure sker via Azure Active Directory (AD).  Oprette en tjeneste hovedstolen for Azlog Integration opretter den Azure AD-identitet, får adgang til at læse fra Azure abonnementer.

3. Kør kommandoen

        azlog authorize <SubscriptionID>

      Dette tildeler læser access på dit abonnement til tjenesten vigtigste oprettede i trin 2. Hvis du ikke angiver en SubscriptionID, derefter den forsøger at tildele rollen vigtigste læser alle abonnementer, du har en hvilken som helst adgang til.

        azlog authorize 0ee9d577-9bc4-4a32-a4e8-c29981025328

      > [AZURE.NOTE] Du kan få vist advarsler, hvis du kører kommandoen **Godkend** umiddelbart efter kommandoen **createazureid** . Der er nogle ventetid mellem hvornår Azure AD-konto er oprettet, og når kontoen er tilgængelig til brug. Hvis du venter omkring 10 sekunder, når du har kørt kommandoen **createazureid** til at køre kommandoen **Godkend** , skal du ikke se disse advarsler.

4. Kontrollere for at bekræfte, at JSON overvågningslogfiler er der følgende mapper:

  - **c:\Users\azlog\AzureResourceManagerJson**
  - **c:\Users\azlog\AzureResourceManagerJsonLD**

5. Kontrollér følgende mapper for at bekræfte, at Sikkerhedscenter beskeder findes i dem:

  - **c:\Users\azlog\ AzureSecurityCenterJson**
  - **c:\Users\azlog\AzureSecurityCenterJsonLD**

6. Peg standard SIEM fil domænes forbindelsen til den relevante mappe til pipe dataene på forekomsten af SIEM. Du skal muligvis nogle felttilknytninger på baggrund af den SIEM produkt, du bruger.

Hvis du har spørgsmål om Azure Log Integration, bedes du sende en mail til [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Næste trin

I dette selvstudium, du har lært hvordan du installerer Azure log integration og integrere logfiler fra Azure-lager. Hvis du vil vide mere, skal du se følgende:

- [Microsoft Azure Log Integration for Azure loggene (Preview)](https://www.microsoft.com/download/details.aspx?id=53324) – Download Center for oplysninger om systemkrav og installationsanvisningerne på Azure log integration.
- [Introduktion til integration af Azure log](security-azure-log-integration-overview.md) – dette dokument introducerer dig til Azure log integration, dens vigtige funktioner, og hvordan det fungerer.
- [Trinnene til konfiguration af partner](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – dette blogindlæg viser, hvordan du konfigurerer Azure log integration til at arbejde med partnerløsninger Splunk, HP ArcSight og IBM QRadar.
- [Azure log Integration ofte stillede spørgsmål (FAQ)](security-azure-log-integration-faq.md) - denne ofte stillede spørgsmål finder du svar på spørgsmål om Azure log integration.
- [Integration Sikkerhedscenter beskeder med Azure logge Integration](../security-center/security-center-integrating-alerts-with-log-integration.md) – dette dokument viser, hvordan du synkronisere Sikkerhedscenter påmindelser, sammen med virtuelt sikkerhedshændelser, der indsamles via Azure diagnosticerings- og Azure overvågningslogge med din log analytics eller SIEM løsning.
- [Nye funktioner til Azure diagnosticering og Azure overvågningslogge](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – dette blogindlæg introducerer dig til Azure overvågningslogge og andre funktioner, der hjælper dig med at få indsigt i handlinger for ressourcerne Azure.
