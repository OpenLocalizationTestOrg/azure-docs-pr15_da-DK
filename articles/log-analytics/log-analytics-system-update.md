<properties
    pageTitle="System opdatering vurdering løsning i Log Analytics | Microsoft Azure"
    description="Du kan bruge løsningen systemopdateringer i Log Analytics kan hjælpe dig med at anvende manglende opdateringer til servere i infrastrukturen."
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
    ms.date="08/11/2016"
    ms.author="banders"/>

# <a name="system-update-assessment-solution-in-log-analytics"></a>System opdatering vurdering løsning i Log Analytics

Du kan bruge løsningen systemopdateringer i Log Analytics kan hjælpe dig med at anvende manglende opdateringer til servere i infrastrukturen. Når du har installeret løsningen, kan du få vist de opdateringer, der mangler fra serverne overvåget ved hjælp af feltet **System opdatering vurdering** på siden **Oversigt** i OMS.

Hvis manglende opdateringer findes, vises detaljer på dashboardet til **opdateringer** . Du kan bruge dashboardet for **opdateringer** til at arbejde med manglende opdateringer og udvikle en plan for at anvende dem på servere, der har brug for dem.

## <a name="installing-and-configuring-the-solution"></a>Installation og konfiguration af løsningen
Brug følgende oplysninger til at installere og konfigurere løsningen.

- Føje System opdatering vurdering løsningen til arbejdsområdet OMS ved hjælp af processen beskrevet i [tilføje Log Analytics løsninger fra løsningsgalleriet](log-analytics-add-solutions.md).  Der er ingen yderligere konfiguration, der er påkrævet.

## <a name="system-update-data-collection-details"></a>Detaljer om system Opdater data af websteder

System opdatering vurdering indsamler metadata og stat data ved hjælp af supportmedarbejdere, som du har aktiveret.

Følgende tabel viser data samling metoder og andre oplysninger om, hvordan data indsamles for systemet opdatering vurdering.

| platform | Direkte Agent | SCOM agent | Azure-lager | SCOM påkrævet? | SCOM agent data, der sendes via administration af gruppe | Frekvens af websteder |
|---|---|---|---|---|---|---|
|Windows|![Ja](./media/log-analytics-system-update/oms-bullet-green.png)|![Ja](./media/log-analytics-system-update/oms-bullet-green.png)|![Nej](./media/log-analytics-system-update/oms-bullet-red.png)|            ![Nej](./media/log-analytics-system-update/oms-bullet-red.png)|![Ja](./media/log-analytics-system-update/oms-bullet-green.png)| Mindst 2 gange i dag og 15 minutter, når du har installeret en opdatering|

I følgende tabel vises eksempler på datatyper, der indsamles via System opdatering vurdering:

|**Datatype**|**Felter**|
|---|---|
|Metadata|BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IP-adresse, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP-adresse, NetbiosDomainName, LogicalProcessors, DNSName, vist navn, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime|
|Stat|StateChangeEventId, StateId, NewHealthState, OldHealthState, kontekst, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified|


### <a name="to-work-with-updates"></a>Til at fungere sammen med opdateringer

1. Klik på feltet **System opdatering vurdering** på siden **Oversigt** .  
    ![System opdatering vurdering felt](./media/log-analytics-system-update/sys-update-tile.png)
2. Få vist opdatering kategorierne på dashboardet til **opdateringer** .  
    ![Opdateringer dashboard](./media/log-analytics-system-update/sys-updates02.png)
3. Rul til højre på siden for at få vist bladet **Kritisk/sikkerhedsopdateringer til Windows** og derefter klikke på **Sikkerhedsopdateringer**under **klassifikation**.  
    ![Sikkerhedsopdateringer](./media/log-analytics-system-update/sys-updates03.png)
4. På siden Log Søg vises forskellige oplysninger om de vigtige opdateringer, der blev fundet manglende fra servere i infrastrukturen. Klik på **listen** for at få vist detaljerede oplysninger om opdateringerne.  
    ![Søgeresultater - liste](./media/log-analytics-system-update/sys-updates04.png)
5. På siden Log Søg vises detaljerede oplysninger om hver opdatering. Klik på **Vis** for at få vist den tilsvarende artikel på webstedet Microsoft Support ud for tal KBID.  
    ![Log søgeresultater - visning KB](./media/log-analytics-system-update/sys-updates05.png)
6. Webbrowseren åbnes websiden Microsoft Support i forbindelse med opdateringen i en ny fane. Få vist oplysninger om opdateringen, der mangler.  
    ![Microsoft Support-webside](./media/log-analytics-system-update/sys-updates06.png)
7. Bruger med oplysninger, du har fundet, kan du oprette en plan for manuelt at anvende den manglende opdatering, eller du kan fortsætte med at følge de resterende trin for automatisk at anvende opdateringen.
8. Hvis du vil anvende den manglende opdatering automatisk, gå tilbage til dashboardet **opdateringer** , og klik derefter på **Klik her for at planlægge en opdatering, der kører**under **Opdatere kører**.  
    ![Opdater kører - planlagte fanen](./media/log-analytics-system-update/sys-updates07.png)
9. Klik på **Tilføj** for at oprette en ny opdatering Kør på siden **Opdatere kører** under fanen **planlagt** .  
    ![Planlagt fanen - Tilføj](./media/log-analytics-system-update/sys-updates08.png)
10. Tilføje individuelle computere eller computergrupper på siden **Ny opdatere Kør** , Skriv et navn til opdateringen Kør, definere en tidsplan, og klik derefter på **Gem**.  
    ![Ny opdatering Kør](./media/log-analytics-system-update/sys-updates09.png)
11. Fanen **planlagt** på **Opdatere kører** siden viser den nye opdatering kører, som du har planlagt.  
    ![Planlagte fanen](./media/log-analytics-system-update/sys-updates10.png)
12. Når du begynder at køre opdateringen, får du vist oplysningerne for den på fanen **kører** .  
    ![Igangværende fanen](./media/log-analytics-system-update/sys-updates11.png)
13. Når opdateringen køre er fuldført, vises fanen **afsluttet** status.
14. Hvis der er anvendt opdateringer fra den kører i bladet **Kritisk/sikkerhedsopdateringer til Windows** update ser du, at antallet af opdateringer er reduceret.  
    ![Blade kritisk/sikkerhedsopdateringer til Windows - opdatering Tæl reduceret](./media/log-analytics-system-update/sys-updates12.png)



## <a name="next-steps"></a>Næste trin

- [Logfiler over Søg](log-analytics-log-searches.md) til at få vist detaljerede system opdateringsdata.
