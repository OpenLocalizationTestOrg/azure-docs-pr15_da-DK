<properties
   pageTitle="Ved hjælp af Azure Sikkerhedscenter til et hændelsen svar | Microsoft Azure"
   description="Dette dokument forklares det, hvordan du bruger Azure Sikkerhedscenter for en hændelsen svar scenarie."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/20/2016"
   ms.author="yurid"/>

# <a name="using-azure-security-center-for-an-incident-response"></a>Ved hjælp af Azure Sikkerhedscenter til et hændelsen svar
Mange organisationer Lær at imødekomme sikkerhedsproblemer kun efter lide et angreb. For at reducere omkostninger og skade, er det vigtigt, at du har et hændelsen svar planlægge på plads, før et angreb finder sted. Du kan bruge Azure Sikkerhedscenter i forskellige faser af et hændelsen svar.

## <a name="incident-response-planning"></a>Planlægning af hændelsen svar

En effektiv plan afhænger af tre kernefunktioner: Der kunne beskytte, registrerer og besvare trusler. Beskyttelse er om forhindre hændelser, registrering er om at identificere trusler tidligt og svaret, som er om evicting hackeren og gendanne systemer for at reducere indvirkningen ved en overtrædelse.

I denne artikel vil bruge sikkerhed hændelsen svar faserne fra artiklen [Microsoft Azure Security Response i skyen](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678) , som vist i følgende diagram:

![Hændelsen svar livscyklus](./media/security-center-incident-response/security-center-incident-response-fig1.png)

Du kan bruge Sikkerhedscenter under Find, vurdering og Diagnosticer faser. Her er eksempler på, hvordan Sikkerhedscenter kan være nyttige under de tre første hændelse svar faser:

- **Find**: Gennemgå den første angivelse af en begivenhed undersøgelse.
    - Eksempel: Gennemse indledende bekræftelsen, en høj prioritet-sikkerhedsmeddelelsen er opløftet i dashboardet for Sikkerhedscenter.
- **Vurdering**: udføre den indledende vurdering for at få flere oplysninger om mistænkelig aktivitet.
    - Eksempel: få flere oplysninger om sikkerhedsadvarslen.
- **Diagnosticer**: forestå en tekniske undersøgelse og identificere beholdning, afhjælpning og løsning strategier.
    - Eksempel: skal du benytte følgende afhjælpning fremgår af Sikkerhedscenter, bestemt sikkerhedsmeddelelsen.

Dette scenario, der følger efter viser, hvordan du udnytte Sikkerhedscenter i Find, vurdering og Diagnosticer/svar faserne af en sikkerhedshændelse. I Sikkerhedscenter er [sikkerhedshændelse](security-center-incident.md) en sammenlægning af alle beskeder for en ressource, som Juster med [afbryde kæde](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/) mønstre. Hændelser vises i feltet [sikkerhedsadvarsler](security-center-managing-and-responding-alerts.md) og blade. En hændelse vises på listen med relaterede beskeder, så du kan få flere oplysninger om hver forekomst. Sikkerhedscenter viser også enkeltstående sikkerhedsadvarsler, der kan også bruges til at spore en mistænkelig aktivitet.

## <a name="scenario"></a>Scenarie

Contoso overflyttet senest nogle af deres lokale ressourcer til Azure, herunder nogle virtuelt baserede line of business arbejdsmængder og SQL-databaser. Contosos Core Computer sikkerhed hændelse svar Team (CSIRT) har, et problem ved at undersøge sikkerhedsproblemer på grund af sikkerhed intelligence ikke blive integreret med deres aktuelle hændelsen svar værktøjer. Denne funktion ikke integration introducerer et problem under fasen Find (for mange forkerte forekomster), og under vurdering og Diagnosticer faserne. Som en del af denne overførsel de besluttede dig for at tilmelde sig til Sikkerhedscenter til at hjælpe dem med at løse problemet.

Den første fase af denne overførsel færdig efter de onboarded alle de ressourcer og adresseret alle sikkerhedsanbefalinger fra Sikkerhedscenter. Contoso CSIRT er knudepunkt til håndtering af computer sikkerhedshændelser. Gruppen består af en gruppe af personer med ansvarsområder for håndtering af enhver sikkerhedshændelse. Gruppemedlemmerne har klart definerede opgaver til at sikre, at ingen område i svar er tilbage uafdækkede.

Til dette scenarie skal vi fokus for roller i de følgende personas, der er en del af Contoso CSIRT:

![Hændelsen svar livscyklus](./media/security-center-incident-response/security-center-incident-response-fig2.png)

Judy er i sikkerhedshandlinger. Hendes Ansvarsområderne omfatter:

- Overvåge og reagere på trusler rundt.
- Videresender til skyen arbejdsbelastningen ejeren eller sikkerhed analyse efter behov.

Søren er en sikkerhed analyse, og dennes ansvarsområder omfatter:

- Undersøger angreb.
- Afhjælper beskeder.
- Arbejde med arbejdsbyrde ejere, du vil finde ud af, og anvende afhjælpninger.

Judy og Søren har forskellige ansvarsområder, som du kan se, og de skal arbejde sammen for at dele oplysninger om Sikkerhedscenter.

## <a name="recommended-solution"></a>Anbefalet løsning

Da Judy og Søren har forskellige roller, skal de bruge forskellige områder af Security Center til at hente relevante oplysninger til deres daglige aktiviteter. Judy anvender **sikkerhedsadvarsler** som en del af sin daglige overvågning.

![Sikkerhedsadvarsler](./media/security-center-incident-response/security-center-incident-response-fig3.png)

Judy anvender sikkerhedsadvarsler under Find og vurdering faserne. Når Judy afsluttes den indledende vurdering, kan hun eskalere problemet til Søren, hvis yderligere undersøgelse er påkrævet. På dette tidspunkt bruger Søren de oplysninger, der har fået af Sikkerhedscenter, nogle gange sammen med andre datakilder, du flytter til Diagnosticer fase.


## <a name="how-to-implement-this-solution"></a>Hvordan du kan implementere denne løsning

Hvis du vil se, hvordan du kan bruge Azure Sikkerhedscenter i et scenarie med hændelsen svar, skal vi følge Judys trin i Find og vurdering faserne, og Læs derefter gør Søren til at diagnosticere problemet.

### <a name="detect-and-assess-incident-response-stages"></a>Registrere og vurdere hændelsen svar faser

Judy logget på portalen Azure og virker i konsollen Sikkerhedscenter. Som en del af sin daglige overvågning aktiviteter gang hun gennemgå sikkerhedsadvarsler med høj prioritet ved at benytte følgende fremgangsmåde:

1. Klik på feltet **sikkerhedsadvarsler** og få adgang til bladet **sikkerhedsadvarsler** .
    ![Sikkerhed beskeder om blade](./media/security-center-incident-response/security-center-incident-response-fig4.png)

    > [AZURE.NOTE] Til dette scenarie skal der Judy skal udføre en vurdering på skadelig SQL aktivitet beskeden, som det ses i den foregående figur.
2. Klik på beskeden med **skadelig SQL aktivitet** og gennemse angrebet ressourcerne i bladet **skadelig SQL aktivitet** :  ![hændelse detaljer](./media/security-center-incident-response/security-center-incident-response-fig5.png)

    I denne blade Judy kan tage noter med hensyn til de angrebet ressourcer, hvor mange gange dette angreb er der sket med, og hvornår det blev registreret.
3. Klik på den **angreb ressource** for at få mere at vide om dette angreb.

Når du har læst beskrivelsen, er Judy overbevist om, at det ikke en falsk positiv og, hun skal du eskalere problemet til Søren.

### <a name="diagnose-incident-response-stage"></a>Diagnosticere hændelsen svar fase

Søren modtager kassen fra Judy og starter gennemgå trinnene afhjælpning, Sikkerhedscenter foreslås.

![Hændelsen svar livscyklus](./media/security-center-incident-response/security-center-incident-response-fig6.png)

### <a name="additional-resources"></a>Yderligere ressourcer

Hændelsen svar teamet kan også drage fordel af [Security Center Power BI](security-center-powerbi.md) muligheden for at se forskellige typer rapporter. Disse rapporter kan hjælpe dem under yderligere undersøgelse for at visualisere, analysere og filtrere anbefalinger og sikkerhedsadvarsler. Virksomheder, der bruger deres sikkerhedsoplysninger og begivenhed management (SIEM)-løsning under processen undersøgelsen, kan de også [integrere Sikkerhedscenter med deres løsning](security-center-integrating-alerts-with-log-integration.md). Du kan også integrere Azure overvågningslogge og VM (virtual machine) sikkerhed begivenheder ved hjælp af [Azure logfiler integration værktøjet](https://blogs.msdn.microsoft.com/azuresecurity/2016/07/21/microsoft-azure-log-integration-preview/). Hvis du vil undersøge et angreb, kan du bruge disse oplysninger sammen med de oplysninger, der indeholder Sikkerhedscenter.


## <a name="conclusion"></a>Konklusion

Samle et team, før der opstår en hændelse, er meget vigtigt, at din organisation og vil positivt påvirke, hvordan hændelser håndteres. De rette værktøjer til at overvåge ressourcer, der kan være dette team til at udføre nøjagtige trin til afhjælpning af en sikkerhedshændelse. Sikkerhedscenter [registrering funktioner](security-center-detection-capabilities.md) kan hjælpe IT til hurtigt at imødekomme sikkerhedsproblemer og ret sikkerhedsproblemer med.
