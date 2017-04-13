<properties
   pageTitle="Få indsigt fra Azure Sikkerhedscenter data med Power BI | Microsoft Azure"
   description="Azure Security Center Power BI indhold pakken gør det nemt at finde sikkerhedsadvarsler, anbefalinger, angreb ressourcer og tendenserne, baseret på et datasæt, der er oprettet for dine rapportering."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="yurid"/>

# <a name="get-insights-from-azure-security-center-data-with-power-bi"></a>Få indsigt fra Azure Sikkerhedscenter data med Power BI
[Dashboard i Power BI](http://aka.ms/azure-security-center-power-bi) til Azure Sikkerhedscenter gør det muligt at visualisere, analysere og filtrere anbefalinger og sikkerhedsadvarsler overalt, herunder din mobilenhed. Bruge Power BI-dashboard til at finde tendenser og angreb mønstre - visning sikkerhedsadvarsler ressource eller kilde IP-adresse og uadresserede sikkerhedsrisici ved ressourcen eller alder. 

Du kan også indsamle Sikkerhedscenter anbefalinger og sikkerhedsadvarsler med andre data i interessante måder, for eksempel bruge data fra [Azure overvågningslogge](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) og [Azure SQL Database revision](https://powerbi.microsoft.com/blog/monitor-your-azure-sql-database-auditing-activity-with-power-bi/). Begge giver adgang til Power BI-Dashboards, og du kan også eksportere disse data til Excel til nem rapportering på tilstanden sikkerhed for ressourcerne skyen.

##<a name="using-azure-security-center-dashboard-to-access-power-bi"></a>Brug af Azure Sikkerhedscenter dashboard til at få adgang til Power BI
Du kan også bruge dashboardet Azure Security Center til at få adgang til Power BI-rapporter. Følg trinnene til at udføre denne opgave: 

1. Klik på knappen **Ekspresoversigt i Power BI** i dashboardet **Azure Sikkerhedscenter** .

    ![Oprette forbindelse til Azure Sikkerhedscenter ved hjælp af Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new10.png) 

2. Bladet **Gå på opdagelse i Power BI** åbner i højre side, som vist på følgende skærmbillede:

    ![Oprette forbindelse til Azure Sikkerhedscenter ved hjælp af Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new2.png)

3. Hvis du opretter Power BI-dashboardet for første gang, kan du vælge en af følgende indstillinger i bladet **Gå på opdagelse i Power BI** : 

    - **Sikkerhed indsigt dashboard**: Vælg denne indstilling, hvis du vil oprette et dashboard, der indeholder sikkerhedsstatus, tråde og registreringer. Denne indstilling er et mere almindeligt for DevOps rolle, der er ansvarlig for at analysere deres beskyttelsesstatus og fundet beskeder på tværs af abonnementer.
    - **Politik for administrationsdashboard**: Vælg denne indstilling, hvis du vil udforske politik for administration og aktivering.  Denne indstilling er et mere almindeligt for Central IT, der er mere fokuseret på styring. De kan bruge dette dashboard til at opnå synlighed og viden om sikkerhed politik overholdelse på tværs af organisationen.
    - Hvis du allerede har et dashboard i Power BI, kan du klikke på **Gå til dit aktuelle Power BI-dashboard**.

4. I dette eksempel skal du klikke på **sikkerhed indsigt dashboard** indstilling. Hvis dette er første gang, opretter du et Power BI-dashboard til Sikkerhedscenter du bliver bedt om at installere pakken med indhold. Klik på **få** knap i vinduet **indhold packs til Power BI** , som vist på følgende skærmbillede:

    ![Azure Security Center sikkerhed indsigt dashboard](./media/security-center-powerbi/security-center-powerbi-fig1-new3.png)

5. Vinduet **Opret forbindelse til Azure Security Center sikkerhed indsigt** vises. Kontrollér, at **godkendelsesmetoden** er **oAuth2** som vist nedenfor, og klik på knappen **Log på** .
    
    ![Godkendelse](./media/security-center-powerbi/security-center-powerbi-fig1-new4.png)

6. Du muligvis bedt om at godkende igen med dine Azure legitimationsoplysninger. Når godkendelse af dit dashboard oprettes. Når dashboardet er oprettet vist du en rapport med lignende struktur, som vist på følgende skærmbillede:

    ![Dashboard i Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new5.png)


> [AZURE.NOTE] En opdatering af rapporten er planlagt til at finde sted dagligt. I tilfælde af, at du oplever en fejl på denne opdatering, skal du læse [Potentielle opdatere problemer med Azure Security Center Power BI](https://blogs.msdn.microsoft.com/azuresecurity/2016/04/07/azure-security-center-power-bi-refresh-fails/), kan finde flere oplysninger om, hvordan du udfører fejlfinding af.

Her kan du se antallet af sikkerhedsadvarsler og anbefalinger og antallet af FOS, Azure SQL-databaser og netværksressourcer, der skal overvåges af Azure Sikkerhedscenter.

Et link til Azure Sikkerhedscenter omdirigerer til Azure-portalen. Diagrammerne gør det nemt at visualisere oplysninger om anbefalinger til sikkerhed og påmindelser, herunder:

- Ressource sikkerhed tilstand
- Ventende anbefalinger
- VM anbefalinger
- Beskeder over tid
- Angrebet ressourcer
- Angrebet IP'er

Bag hvert diagram er der flere indsigt. Vælg et felt for at se flere oplysninger. Feltet **Ressource sikkerhed tilstand** vises eksempelvis du få mere at vide om afventer anbefalinger af ressourcer som vist på følgende skærmbillede:

![Anbefalinger](./media/security-center-powerbi/security-center-powerbi-fig1-new6.png)

Hvis du klikker på en linje i dette diagram, går de andre grå ud af, og du fokus kun på det tilladelsesniveau, du har valgt. Klik på **Azure Sikkerhedscenter** under indstillingen **Dashboards** i venstre rude på denne side for at vende tilbage til dashboard.

> [AZURE.NOTE] Hvis du vil gerne tilpasse dine rapporter ved at tilføje ekstra felter eller ændre eksisterende visuelle elementer, kan du redigere rapporten. Læs [interakt.Skab med en rapport i redigeringsvisning i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-interact-with-a-report-in-editing-view/) kan finde flere oplysninger.

**Beskeder over tid, angreb ressourcer** og **Hacker IP'er** fliserne har lignende output, når du klikker på hver enkelt af filen. Dette sker, fordi rapporten samler oplysninger om alle disse tre variabler og kalder den **ressourcer under angreb** , som vist på følgende skærmbillede:

![Ressourcer under angreb](./media/security-center-powerbi/security-center-powerbi-fig1-new7.png)

På dette tidspunkt kan du også gemme en kopi af denne rapport, udskrive den eller publicere den på internettet ved hjælp af de tilgængelige indstillinger i menuen **filer** .

![Menuen Filer](./media/security-center-powerbi/security-center-powerbi-fig8.png)

## <a name="exploring-your-azure-security-center-data-with-power-bi-services"></a>Udforske dine Azure Sikkerhedscenter data med Power BI-tjenester

Oprette forbindelse til [Power BI-indhold Pack tjenester](https://msit.powerbi.com/groups/me/getdata/services) i Power BI og udføre følgende trin:

1. Du får vist to muligheder i vinduet **Indhold Pack til Power BI** som vist nedenfor.

    ![Indhold pack til Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new.png)

    >[AZURE.NOTE] Hvis allerede udført den første del af denne artikel vises kun én indstilling, som er Azure administration af sikkerhedspolitik Center.

2. I dette eksempel skal du klikke på **få** i feltet **Azure administration af sikkerhedspolitik Center** .

3. Sørg for at markere **oAuth2** under **Godkendelsesmetode** for rullelisten som vist nedenfor og klikke på **Log på** i vinduet **Opret forbindelse til Azure administration af sikkerhedspolitik Center** .

    ![Politik for administration af vinduet](./media/security-center-powerbi/security-center-powerbi-fig1-new8.png)

4. Du bliver omdirigeret til en godkendelse side, hvor du skal indtaste de legitimationsoplysninger, som du bruger til at oprette forbindelse til Azure Sikkerhedscenter. Når godkendelsesprocessen er fuldført, starter Power BI import af data for at oprette dine rapporter. I denne periode kan du få vist følgende meddelelse i højre hjørne i din browser:

    ![Oprette forbindelse til Azure Sikkerhedscenter ved hjælp af Power BI](./media/security-center-powerbi/security-center-powerbi-fig4.png)

    >[AZURE.NOTE] Det kan tage længere tid end normalt, især for scenarier, hvor du har flere abonnementer, når der oprettes dashboardet for første gang. 

5. Når processen er fuldført, indlæser dit Azure Security Center Power BI-dashboard med **Administration af** rapporten ligner den, der vises nedenfor:

    ![Politik for administration af dashboard](./media/security-center-powerbi/security-center-powerbi-fig1-new9.png)

## <a name="see-also"></a>Se også
I dette dokument, du har lært at bruge Power BI i Azure Sikkerhedscenter. Hvis du vil vide mere om Azure Sikkerhedscenter, skal du se følgende:

- [Azure Security Center planlægning og Operations Guide](security-center-planning-and-operations-guide.md) – få mere at vide om at planlægge Azure Sikkerhedscenter indføring.
- [Angive sikkerhedspolitikker i Azure Sikkerhedscenter](security-center-policies.md) – Lær, hvordan du kan konfigurere sikkerhedsindstillinger i Azure Security Center
- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md) – Lær at administrere og reagere på beskeder om sikkerhed
- [Azure Security Center ofte stillede spørgsmål](security-center-faq.md) – Find ofte stillede spørgsmål om ved hjælp af tjenesten
- [Azure sikkerhed Blog](http://blogs.msdn.com/b/azuresecurity/) – finde blogindlæg om Azure sikkerhed og overholdelse
