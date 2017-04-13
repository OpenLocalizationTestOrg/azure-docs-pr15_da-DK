<properties
    pageTitle="Optimere dit miljø med løsningen Service-strukturen i Log Analytics | Microsoft Azure"
    description="Du kan bruge tjenesten strukturen løsningen til at vurdere risikoen og tilstand for tjenesten strukturen programmer, micro-tjenester, noder og klynger."
    services="log-analytics"
    documentationCenter=""
    authors="niniikhena"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="nini"/>



# <a name="service-fabric-solution-in-log-analytics"></a>Tjenesten strukturen løsning i Log Analytics

> [AZURE.SELECTOR]
- [Ressourcestyring](log-analytics-service-fabric-azure-resource-manager.md)
- [PowerShell](log-analytics-service-fabric.md)

I denne artikel beskrives, hvordan du bruger løsningen Service-strukturen i Log Analytics til at identificere og foretage fejlfinding af problemer på tværs af din Service-strukturen klynge.

Tjenesten strukturen løsningen bruger Azure diagnosticering data fra din Service-strukturen FOS, ved at indsamle disse data fra Azure WAD tabellerne. Log Analytics læser derefter tjenesten strukturen framework hændelser, herunder **Pålidelige tjenestehændelser**, **Agent begivenheder**, **Funktionsdygtige begivenheder**og **brugerdefineret ETW begivenheder**. Du kan få til at få vist væsentlige problemer og relevante begivenheder i dit miljø, Service-strukturen med dashboardet løsning.

For at komme i gang med løsningen, skal du oprette forbindelse din tjeneste strukturen klynge til en Log Analytics-arbejdsområde. Her er tre scenarier skal du tænke på:

1. Hvis du ikke har installeret din tjeneste strukturen klynge, kan du følge trinnene i ***Implementer en tjeneste strukturen klynge forbindelse til et arbejdsområde Log Analytics*** installere en ny klynge, så den er konfigureret til rapport for at Log analyser.

2. Hvis du har brug at indsamle tællere i ydeevne fra din værtsnationer for at bruge andre OMS løsninger som sikkerhed på din Service-strukturen klynge, skal du følge trinnene i ***Implementer en tjeneste-strukturen klynge forbindelse til et arbejdsområde OMS med VM lokalnummer, der er installeret.***

3. Hvis du allerede har installeret din klynge Service-strukturen og vil forbinde den til Log Analytics, skal du følge trinnene i ***at føje en eksisterende konto lagerplads til Log Analytics.***


##<a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace"></a>Installere en Service-strukturen klynge, der er tilsluttet et Log Analytics-arbejdsområde.
Denne skabelon gør følgende:


1. Installerer en Azure Service strukturen klynge allerede har forbindelse til et Log Analytics-arbejdsområde. Du har mulighed for at oprette et nyt arbejdsområde under installation af skabelonen, eller skriv navnet på et eksisterende Log Analytics-arbejdsområde.
2. Føjer kontoen diagnosticering lagerplads til arbejdsområdet Log analyser.
3. Gør det muligt for løsningen Service-strukturen i arbejdsområdet Log analyser.

[![Installere på Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-oms%2F%2Fazuredeploy.json)


Når du vælger knappen Implementer ovenfor, modtager du på Azure-portalen med parametre for dig at redigere. Sørg for at oprette en ny ressourcegruppe, hvis du indtaster et nyt Log Analytics arbejdsområdenavn: ![Service-strukturen](./media/log-analytics-service-fabric/2.png)

![Tjenesten struktur](./media/log-analytics-service-fabric/3.png)

Acceptere juridiske betingelser, og tryk "Oprette" for at starte installationen. Når installationen er fuldført, skal du se det nye arbejdsområde- og klynge, der er oprettet, og WADServiceFabric * begivenhed, WADWindowsEventLogs og WADETWEvent tabeller, der er tilføjet:

![Tjenesten struktur](./media/log-analytics-service-fabric/4.png)

##<a name="deploy-a-service-fabric-cluster-connected-to-an-oms-workspace-with-vm-extension-installed"></a>Installere en tjeneste-strukturen klynge, der er tilsluttet et OMS arbejdsområde med VM lokalnummer, der er installeret.
Denne skabelon gør følgende:

1. Installerer en Azure Service strukturen klynge allerede har forbindelse til et Log Analytics-arbejdsområde. Du kan oprette et nyt arbejdsområde eller bruge en eksisterende.
2. Føjer kontiene diagnosticering lagerplads til arbejdsområdet Log analyser.
3. Gør det muligt for løsningen Service-strukturen i arbejdsområdet Log analyser.
4. Installerer filtypenavnet MMA agent i hver VM målestoksforhold, der er angivet i din tjeneste strukturen klynge. Med den MMA agent er installeret, er du kan se ydeevne målepunkter om din noder.


[![Installere på Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-vmss-oms%2F%2Fazuredeploy.json)


Indtast de nødvendige parametre, følge de samme trin herover, og starte en installation. Igen skal du se nyt arbejdsområde, klynge og WAD tabeller alle oprettede:

![Tjenesten struktur](./media/log-analytics-service-fabric/5.png)

###<a name="viewing-performance-data"></a>Visning af ydelsesdata

Sådan får du vist performance Data fra din noder:
</br>
- Start arbejdsområdet Log Analytics fra Azure-portalen.

![Tjenesten struktur](./media/log-analytics-service-fabric/6.png)

- Gå til indstillinger i venstre rude, og vælg Data >> tællere i Windows ydeevne >> "Tilføj de valgte performance tællere": ![Service-strukturen](./media/log-analytics-service-fabric/7.png)

- I Log søgning skal du bruge følgende forespørgsler til delve til vigtige mål om din noder:
</br>

    en. Sammenlign den gennemsnitlige CPU-forbrug på tværs af alle noder i den seneste én time for at se, hvilke knuder har problemer med, og på hvilket tidsinterval en node havde en samling:

    ``` Type=Perf ObjectName=Processor CounterName="% Processor Time"|measure avg(CounterValue) by Computer Interval 1HOUR. ```

    ![Tjenesten struktur](./media/log-analytics-service-fabric/10.png)


    b. Få vist lignende kurvediagrammer for tilgængelig hukommelse på hver node med denne forespørgsel:

    ```Type=Perf ObjectName=Memory CounterName="Available MBytes Memory" | measure avg(CounterValue) by Computer Interval 1HOUR.```

    Brug denne forespørgsel for at få vist en oversigt over alle dine noder, viser nøjagtige gennemsnitsværdien for MB til rådighed for hver enkelt node:

    ```Type=Perf (ObjectName=Memory) (CounterName="Available MBytes") | measure avg(CounterValue) by Computer ```

    ![Tjenesten struktur](./media/log-analytics-service-fabric/11.png)


    c. I den sag, du vil analysere ned i en bestemt node ved at undersøge hver time gennemsnit, minimum, største og 75 fraktil CPU-brug, er du mulighed for at gøre dette ved hjælp af denne forespørgsel (erstatte Computer felt):

    ```Type=Perf CounterName="% Processor Time" InstanceName=_Total Computer="BaconDC01.BaconLand.com"| measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR```

    ![Tjenesten struktur](./media/log-analytics-service-fabric/12.png)

    Læs mere om ydeevnen for statistik i Log Analytics [her]. (https://blogs.technet.microsoft.com/msoms/tag/metrics/)


##<a name="adding-an-existing-storage-account-to-log-analytics"></a>Føje en eksisterende konto lagerplads til Log Analytics

Denne skabelon føjer blot kontiene eksisterende lagerplads til et nyt eller eksisterende Log Analytics-arbejdsområde.
</br>

[![Installere på Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Foms-existing-storage-account%2Fazuredeploy.json)

>[AZURE.NOTE] I at vælge en ressourcegruppe, hvis du arbejder med et eksisterende Log Analytics-arbejdsområde, vælge "Brug eksisterende", og Søg efter ressourcegruppen, der indeholder OMS arbejdsområdet. Oprette en ny én Hvis Ellers.
![Tjenesten struktur](./media/log-analytics-service-fabric/8.png)

Når denne skabelon er blevet installeret, vil du kunne se kontoen lagerplads har forbindelse til arbejdsområdet Log analyser. I denne forekomst, jeg har tilføjet en mere lagerplads konto til Exchange-arbejdsområdet, jeg har oprettet ovenfor.
![Tjenesten struktur](./media/log-analytics-service-fabric/9.png)

## <a name="view-service-fabric-events"></a>Få vist Service-strukturen hændelser

Når installationer er fuldført, og løsningen Service-strukturen er blevet aktiveret i arbejdsområdet skal du vælge feltet **Service-strukturen** i portalen Log Analytics til at starte tjenesten strukturen dashboard. Dashboardet indeholder kolonnerne, i den følgende tabel. Hver kolonne, som viser de øverste ti hændelser efter antal, der stemmer overens med den kolonne kriterier for det angivne tidsinterval. Du kan køre en log søgning, der indeholder hele listen ved at klikke på **se alle** højre nederst i hver kolonne eller ved at klikke på kolonneoverskriften.

| **Tjenesten strukturen begivenhed** | **Beskrivelse** |
| --- | --- |
| Væsentlige problemer | En visning af problemer som RunAsyncFailures RunAsynCancellations og Node filterlister. |
| Funktionsdygtige begivenheder | Væsentlige funktionsdygtige begivenheder som programmet opgradering og installationer. |
| Pålidelig Service begivenheder | Væsentlige pålidelig service begivenheder sådanne Runasyncinvocations. |
| Agent begivenheder | Væsentlige Agent hændelser, der genereres af din micro-tjenester, som undtagelser udløst af en agent metode, Agent aktiveringer og deactivations osv. |
| Programhændelser | Alle brugerdefinerede ETW hændelser, der genereres af dine programmer. |

![Tjenesten strukturen dashboard](./media/log-analytics-service-fabric/sf3.png)

![Tjenesten strukturen dashboard](./media/log-analytics-service-fabric/sf4.png)


Følgende tabel viser data samling metoder og andre oplysninger om, hvordan data indsamles for tjenesten struktur.

| platform | Direkte Agent | SCOM agent | Azure-lager | SCOM påkrævet? | SCOM agent data, der sendes via administration af gruppe | Frekvens af websteder |
|---|---|---|---|---|---|---|
|Windows|![Nej](./media/log-analytics-malware/oms-bullet-red.png)|![Nej](./media/log-analytics-malware/oms-bullet-red.png)| ![Ja](./media/log-analytics-malware/oms-bullet-green.png)|            ![Nej](./media/log-analytics-malware/oms-bullet-red.png)|![Nej](./media/log-analytics-malware/oms-bullet-red.png)|10 minutter |


>[AZURE.NOTE] Du kan ændre omfanget af disse begivenheder i tjenesten strukturen løsningen ved at klikke på **Data ud fra de seneste 7 dage** øverst på dashboardet. Du kan også vise hændelser, der opstår i de seneste 7 dage, 1 dag eller seks timer. Eller du kan vælge **brugerdefineret** til at angive et brugerdefineret datointerval.


## <a name="next-steps"></a>Næste trin

- Brug [Log søgninger i Log Analytics](log-analytics-log-searches.md) til at få vist detaljerede data for tjenesten strukturen begivenhed.
