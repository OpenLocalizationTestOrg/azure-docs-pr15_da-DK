<properties
    pageTitle="Konfiguration af vurdering løsning i Log Analytics | Microsoft Azure"
    description="Konfiguration vurdering løsningen i Log Analytics giver dig detaljerede oplysninger om den aktuelle tilstand for dit System Center Operations Manager server-infrastruktur, når du bruger Operations Manager supportmedarbejdere en Operations Manager management gruppe."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="configuration-assessment-solution-in-log-analytics"></a>Konfiguration af vurdering løsning i Log Analytics

Konfiguration vurdering løsningen i Log Analytics hjælper dig med at finde potentielle server konfigurationsproblemer via beskeder og viden anbefalinger.

Denne løsning kræver System Center Operations Manager. Konfiguration af vurdering er ikke tilgængelig, hvis du kun bruger direkte tilsluttet supportmedarbejdere.

Få vist oplysninger i konfiguration vurdering løsning kræver Silverlight-plug-in til din browser.

>[AZURE.NOTE] Starten 5 juli 2016 konfiguration vurdering løsningen kan ikke længere føjes til Log Analytics arbejdsområder og denne løsning er ikke længere tilgængelig for eksisterende brugere efter August 1 2016. For kunder, der bruger denne løsning til SQL Server eller Active Directory, anbefaler vi du i stedet bruge [SQL Server vurdering](log-analytics-sql-assessment.md), [Active Directory vurdering](log-analytics-ad-assessment.md) og [Active Directory gentagelse Status](log-analytics-ad-replication-status.md) løsninger. For kunder, der bruger konfiguration vurdering til Windows, Hyper-V og System Center Virtual Machine Manager, vi anbefaler, du bruger samlingen begivenhed og registrering af ændringer funktioner til at få en holistisk visning af problemer, inden dine miljøer.

![Konfiguration af vurdering felt](./media/log-analytics-configuration-assessment/oms-config-assess-tile.png)

Af konfigurationsdata er indsamlet fra overvåget servere og derefter sendes til tjenesten OMS i skyen for behandling. Logik anvendes på modtager data og skytjenesten registrerer dataene. Behandlede data for serverne, der vises for følgende områder:

- **Beskeder:** Viser de konfiguration beslægtede, proaktiv påmindelser, der er oprettet for dine overvåget servere. Disse er udviklet af regler, der er oprettet af Microsoft Customer og organisering af Support (CSS) med bedste fremgangsmåder fra feltet.
- **Knowledge anbefalinger:** Viser i Microsoft Knowledge Base-artikler, der er anbefalet til arbejdsbelastninger, som findes i din infrastruktur disse foreslås automatisk baseret på din konfiguration via brugen af machine learning.
- **Servere og arbejdsbelastninger analysere:** Viser de servere og arbejdsbelastninger, der skal overvåges ved OMS.

![Konfiguration af vurdering dashboard](./media/log-analytics-configuration-assessment/oms-config-assess-dash01.png)

### <a name="technologies-you-can-analyze-with-configuration-assessment"></a>Du kan analysere med konfiguration vurdering teknologier

OMS konfiguration vurdering analyserer de følgende arbejdsmængder:

- Windows Server 2012 og Microsoft Hyper-V Server 2012
- Windows Server 2008 og Windows Server 2008 R2, herunder:
    - Active Directory
    - Hyper-V host
    - Generelle operativsystem
- SQL Server 2008 eller nyere
    - SQL Server-Database Engine
- Microsoft SharePoint 2010
- Microsoft Exchange Server 2010 og Microsoft Exchange Server 2013
- Microsoft Lync Server 2013 og Lync Server 2010
- System Center 2012 SP1 – Virtual Machine Manager

Til SQL Server understøttes følgende 32-bit og 64-bit versioner af analysen af:

- SQL Server 2016 - alle versioner
- SQL Server 2014 - alle versioner
- SQL Server 2008 og 2008 R2 - alle versioner

SQL Server-Database Engine analysere på alle understøttede udgaver. Desuden understøttes 32-bit-versionen af SQL Server, når du kører i WOW64 implementeringen.

## <a name="installing-and-configuring-the-solution"></a>Installation og konfiguration af løsningen
Brug følgende oplysninger til at installere og konfigurere løsningen.

- Operations Manager er påkrævet for løsningen konfiguration vurdering.
- Du skal have en Operations Manager agent på alle computere, hvor du vil vurdere dens konfiguration.
- Føje konfiguration vurdering løsningen til arbejdsområdet OMS ved hjælp af processen beskrevet i [tilføje Log Analytics løsninger fra løsningsgalleriet](log-analytics-add-solutions.md).  Der er ingen yderligere konfiguration, der er påkrævet.

## <a name="configuration-assessment-data-collection-details"></a>Oplysninger om konfiguration vurdering data af websteder

Konfiguration af vurdering indsamler konfigurationsdata, metadata og tilstand data ved hjælp af supportmedarbejdere, som du har aktiveret.

Den følgende tabel viser data samling metoder og andre oplysninger om, hvordan data indsamles for konfiguration vurdering.

| platform | Direkte Agent | SCOM agent | Azure-lager | SCOM påkrævet? | SCOM agent data, der sendes via administration af gruppe | Frekvens af websteder |
|---|---|---|---|---|---|---|
|Windows|![Nej](./media/log-analytics-configuration-assessment/oms-bullet-red.png)|![Ja](./media/log-analytics-configuration-assessment/oms-bullet-green.png)|![Nej](./media/log-analytics-configuration-assessment/oms-bullet-red.png)|            ![Ja](./media/log-analytics-configuration-assessment/oms-bullet-green.png)|![Ja](./media/log-analytics-configuration-assessment/oms-bullet-green.png)| to gange om dagen|

I følgende tabel vises eksempler på datatyper, der indsamles via konfiguration vurdering:

|**Datatype**|**Felter**|
|---|---|
|Konfiguration|Kunde-id AgentID enheds-id, ManagedTypeID, ManagedTypePropertyID, og Aktuelværdi, ChangeDate|
|Metadata|BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IP-adresse, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP-adresse, NetbiosDomainName, LogicalProcessors, DNSName, vist navn, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime|
|Stat|StateChangeEventId, StateId, NewHealthState, OldHealthState, kontekst, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified|

## <a name="configuration-assessment-alerts"></a>Konfiguration af vurdering beskeder
Du kan få vist og administrere beskeder fra konfiguration vurdering med siden beskeder. Beskeder fortæller dig, det problem, der blev registreret, årsagen og hvordan du kan løse problemet. De indeholder også oplysninger om indstillinger for søgekonfiguration i dit miljø, der kan medføre problemer med ydeevnen.

![få vist beskeder](./media/log-analytics-configuration-assessment/oms-config-assess-alerts01.png)

>[AZURE.NOTE] Konfiguration vurdering beskeder er anderledes end de andre påmindelser i Log analyser. Få vist beskeder kræver en Silverlight-plug-in til din browser.

Når du vælger et element eller en kategori af elementer på siden beskeder, får du vist en liste over servere eller arbejdsmængder med beskeder, der gælder for hvert element.

![liste over påmindelser](./media/log-analytics-configuration-assessment/oms-config-assess-alerts-view-config.png)

Hver besked indeholder et link til en artikel i Microsoft Knowledge Base. Disse artikler indeholder flere oplysninger om beskeden.

>[AZURE.TIP] Som standard er det maksimale antal beskeder vises 2.000. For at få vist flere beskeder, skal du klikke på meddelelseslinjen over listen med beskeder.

Du kan klikke på et element på listen for at få vist KB-artiklen, der kan hjælpe dig med at løse problemet, som genererede beskeden årsagen.

![KB-artikel](./media/log-analytics-configuration-assessment/oms-config-assess-alerts-details-kb.png)

Du kan administrere regler for påmindelser for at ignorere specifikke regler eller en klasser af regler.

![administrere regler for påmindelser](./media/log-analytics-configuration-assessment/oms-config-assess-alert-rules.png)

## <a name="knowledge-recommendations"></a>Knowledge anbefalinger
Når du får vist knowledge anbefalinger, er du præsenteret log søgeresultater med en oversigt over Microsoft KB-artikel anbefales til arbejdsmængder og computere, som indeholder flere oplysninger om beskeden.

![søgeresultater for knowledge anbefalinger](./media/log-analytics-configuration-assessment/oms-config-assess-knowledge-recommendations.png)

## <a name="servers-and-workloads-analyzed"></a>Servere og arbejdsbelastninger analysere
Når du får vist knowledge anbefalinger, er du præsenteret log søgeresultater med en oversigt over alle de servere og arbejdsbelastninger, som er blevet konstateret til OMS fra Operations Manager.

![Servere og arbejdsmængder](./media/log-analytics-configuration-assessment/oms-config-assess-servers-workloads.png)

## <a name="next-steps"></a>Næste trin

- Brug [Log søgninger i Log Analytics](log-analytics-log-searches.md) til at få vist oplysninger om konfigurationen vurdering data.
