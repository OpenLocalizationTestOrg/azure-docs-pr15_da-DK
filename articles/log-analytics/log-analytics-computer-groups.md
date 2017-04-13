<properties
    pageTitle="Computergrupper i Log Analytics logge søgninger | Microsoft Azure"
    description="Computergrupper i Log Analytics giver dig mulighed for omfang log søgninger med et bestemt sæt af computere.  I denne artikel beskrives de forskellige metoder, du kan bruge til at oprette grupper af computere og hvordan du kan bruge dem i en log søgning."
    services="log-analytics"
    documentationCenter=""
    authors="bwren"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="bwren"/>

# <a name="computer-groups-in-log-analytics-log-searches"></a>Computergrupper i Log Analytics logge søgninger
Computergrupper i Log Analytics muligt omfang [logge søgninger](log-analytics-log-searches.md) med et bestemt sæt af computere.  Hver gruppe er udfyldt med computere, enten ved hjælp af en forespørgsel, som du definerer eller ved at importere grupper fra forskellige kilder.  Når gruppen medtages i en log søgning, er resultatet er begrænset til poster, der opfylder computerne i gruppen.

## <a name="creating-a-computer-group"></a>Oprette en computergruppe
Du kan oprette en computergruppe i Log analyser ved hjælp af metoderne i den følgende tabel.  Få at vide om hver enkelt metode er angivet i afsnittene herunder. 

| Metode | Beskrivelse |
|:---|:---|
| Log søgning       | Oprette en logfil søgning, der returnerer en liste over computere og gemme resultaterne som en computergruppe for. |
| Log Søg API   | Brug Log Søg API til at oprette en computergruppe ud fra resultaterne af en log søgning fra et program. |
| Active Directory | Automatisk at scanne gruppemedlemskabet for en hvilken som helst agentcomputere, der er medlemmer af en Active Directory-domæne og oprette en gruppe i Log Analytics for hver sikkerhedsgruppe.
| WSUS              | Scanne WSUS-servere eller klienter til målretning af grupper og oprette en gruppe i Log Analytics for hver automatisk. |


### <a name="log-search"></a>Log søgning

Computergrupper, der er oprettet ud fra en Log søgning indeholder alle de computere, der returneres af en søgeforespørgsel, som du definerer.  Denne forespørgsel køres, hver gang gruppen computer bruges, så afspejles eventuelle ændringer siden gruppen blev oprettet.

Brug følgende fremgangsmåde til at oprette en computergruppe fra en log søgning.

1. [Oprette en logfil søgning](log-analytics-log-searches.md) , der returnerer en liste over computere.  Feltet Søg skal returnere et entydigt sæt computere ved hjælp af noget som **Særskilte Computer** eller **måling count() ved Computer** i forespørgslen.  
2. Klik på knappen **Gem** øverst på skærmen.
3. Vælg **Ja** til **Gem denne forespørgsel som en computergruppe for:**.
4. Skriv et **navn** og en **kategori** til gruppen.  Hvis der allerede findes en søgning med samme navn og kategori, derefter du vil blive bedt om at overskrive den.  Du kan have flere søgninger med samme navn i forskellige kategorier. 

Følgende er eksempel søgninger, som du kan gemme som en computergruppe for.

    Computer="Computer1" OR Computer="Computer2" | distinct Computer 
    Computer=*srv* | measure count() by Computer

### <a name="log-search-api"></a>Logge af søge-API

Computergrupper, der er oprettet med Log Søg API er den samme som søgninger, der er oprettet med en Log søgning.

Få mere at vide om oprettelse af en computergruppe ved hjælp af Log Søg API se [computergrupper i Log Analytics logger search REST-API](log-analytics-log-search-api.md#computer-groups).

### <a name="active-directory"></a>Active Directory

Når du konfigurerer Log Analytics importere Active Directory gruppemedlemskaber, vil det analysere gruppemedlemskabet for en hvilken som helst domæne tilsluttet computere med OMS agent.  Der oprettes en computergruppe i Log Analytics for hver sikkerhedsgruppe i Active Directory, og alle computere er føjet til grupperne computer, der svarer til de sikkerhedsgrupper, de er medlem af.  Medlem af denne gruppe opdateres løbende hver 4 timer.  

Du konfigurerer Log analyser for at importere Active Directory-sikkerhedsgrupper fra menuen **Computergrupper** i Log Analytics- **Indstillinger**.  Vælg **automatisering** og derefter **importere Active Directory gruppemedlemskaber fra computere**.  Der er ingen yderligere konfiguration, der er påkrævet.

![Computergrupper fra Active Directory](media/log-analytics-computer-groups/configure-activedirectory.png)

Når grupper er blevet importeret, vises i menuen, hvor mange computere med gruppemedlemskab fundet og antallet af grupper, der er importeret.  Du kan klikke på en af disse links til at returnere **ComputerGroup** poster med disse oplysninger.

### <a name="windows-server-update-service"></a>Windows Server Update-tjenesten

Når du konfigurerer Log Analytics importere WSUS gruppemedlemskaber, vil det analysere målretning gruppemedlemskabet for en hvilken som helst computere med OMS agent.  Hvis du bruger klientsiden har målretning af enhver computer, der er forbundet med OMS og er en del af en hvilken som helst WSUS målretning af grupper dens gruppemedlemskab, der er importeret til Log analyser. Hvis du bruger serversiden skal målretning af OMS agent være installeret på WSUS-serveren for at medlemsoplysninger gruppe der skal importeres til OMS.  Medlem af denne gruppe opdateres løbende hver 4 timer. 

Du konfigurerer Log analyser for at importere Active Directory-sikkerhedsgrupper fra menuen **Computergrupper** i Log Analytics- **Indstillinger**.  Vælg **Active Directory** og derefter **importere Active Directory gruppemedlemskaber fra computere**.  Der er ingen yderligere konfiguration, der er påkrævet.

![Computergrupper fra Active Directory](media/log-analytics-computer-groups/configure-wsus.png)

Når grupper er blevet importeret, vises menuen antallet af computere med gruppemedlemskab fundet og antallet af grupper, der er importeret.  Du kan klikke på en af disse links til at returnere **ComputerGroup** poster med disse oplysninger.

## <a name="managing-computer-groups"></a>Administration af grupper af computere

Du kan få vist grupper af computere, der er oprettet ud fra en log søgning eller søgning Log API fra menuen **Computergrupper** i Log Analytics- **Indstillinger**.  Klik på **x** i kolonnen **fjerne** slette gruppen computer.  Klik på ikonet **Vis medlemmer** til en gruppe for at køre gruppens log søgning, der returnerer medlemmerne. 

![Gemte computergrupper](media/log-analytics-computer-groups/configure-saved.png)

Hvis du vil ændre gruppen, skal du oprette en ny gruppe med samme **kategori** og **navn** at overskrive den oprindelige gruppe.

## <a name="using-a-computer-group-in-a-log-search"></a>Brug af en computergruppe i en log søgning
Du kan bruge følgende syntaks til at referere til en computergruppe i en log søgning.  Angive **kategorien** er valgfrit, og kun påkrævet, hvis du har computergrupper med samme navn i forskellige kategorier. 

    $ComputerGroups[Category: Name]

Når du kører en søgning, løses først medlemmerne af en hvilken som helst computergrupper, der er inkluderet i feltet Søg.  Hvis gruppen er baseret på en log søgning, er, at søgningen køre for at returnere medlemmerne af gruppen før du udfører på øverste niveau log søgning.

Computergrupper bruges typisk med **delsætningen in i feltet log Søg som i følgende eksempel** .

    Type=UpdateSummary Computer IN $ComputerGroups[My Computer Group]

## <a name="computer-group-records"></a>Computer gruppeposter

Der oprettes en post i OMS lager for hver computer gruppemedlemskab, der er oprettet ud fra Active Directory eller WSUS.  Disse poster har en type af **ComputerGroup** og har egenskaberne i den følgende tabel.  Poster er ikke oprettet for grupper af computere baseret på log søgninger.

| Egenskaben | Beskrivelse |
|:--|:--|
| Type                | *ComputerGroup* |
| SourceSystem        | *SourceSystem*  |
| Computer            | Navnet på computeren, medlem. |
| Gruppe               | Navnet på gruppen. |
| GroupFullName       | Hele stien til den gruppe, herunder kilde- og kildenavn.
| GroupSource         | Datakilde gruppen blev indsamles fra. <br><br>Active Directory<br>WSUS<br>WSUSClientTargeting |
| GroupSourceName     | Navnet på den kilde, grupperne er indsamlet fra.  Dette er navnet på det domæne for Active Directory. |
| ManagementGroupName | Navnet på gruppen management for SCOM supportmedarbejdere.  I forbindelse med andre supportmedarbejdere er AOI -\<arbejdsområde-ID\> |
| TimeGenerated       | Dato og klokkeslæt for gruppen computer blev oprettet eller opdateret. |



## <a name="next-steps"></a>Næste trin

- Få mere at vide om [log søgninger](log-analytics-log-searches.md) til at analysere de data, der indsamles fra datakilder og løsninger.  