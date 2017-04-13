<properties 
    pageTitle="Azure Mobile aftale Introduktionsvejledning med bedste fremgangsmåder"
    description="Få vejledning i Introduktion til Azure Mobile aftale og bedste praksis for onboarding" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="wesmc7777"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="10/04/2016"
    ms.author="wesmc;ricksal"/>

# <a name="azure-mobile-engagement---getting-started-guide-with-best-practices"></a>Azure Mobile aftale - Introduktion med bedste fremgangsmåder

## <a name="overview"></a>Oversigt

**Skærmbilledet mobile er et meget overfyldt mellemrum:** I 2013 fundet en undersøgelse den gennemsnitlige mobilenhed havde 27 programmer, der er installeret. Brugere har brugt typisk 30 timer månedsbasis på deres apps. De fleste af denne gang blev brugt på sociale netværk og gaming (omkring 20 timers). Ved 2014 havde Android marked omkring 1,5 million programmer for brugerne at vælge mellem. Apple store indeholdt omkring 1,2 millioner apps. Mobilapp stadig forøges som udviklere konkurrere i dette voksende marked. 

Den gennemsnitlige mobilbrugere vil installere og fjerne apps med høj frekvens afhængigt af ændre interesser og i app'en oplever. Det bliver vigtig vil vide mere end bare hvor mange brugere installere din app for at finde ud af en vellykket en app. Det er vigtigt for at vide hvordan nyttige din app er, og hvis pågældende brugen tendens ændres. Følgende spørgsmål bliver vigtige:

- Er dine brugere starten til at finde din app uninteresting eller forældede? 
- Hvor mange brugere har holdt op med at bruge din app overhovedet? 
- Der er i app'en køb populære opad eller nedad?
- Brugere ikke at fuldføre arbejdsgange på grund af problemer med den app eller manglende interesse? 
- Kan du beholde din app nyttige og relevante ved at trykke på nyt indhold til din bruger base? 
- Vil denne nyt indhold er det samme for alle brugere eller fokuseret på bruger målgrupper, der er baseret på funktionsmåde i din app? 
 
Svar på spørgsmål svarer til disse kan være en hjælp til at udvide liv og indtægter fra din app. De kan også hjælpe dig med at definere og bevare dit bruger-base. 

Medier relaterede apps ofte har nogle af de højeste opbevaring mellem brugere. En årsag til dette er de hele tiden leverer nyt indhold til brugere. Tidlig indførelsen af nyttige pushmeddelelser ført til et brugersegment ofte har en høj indvirkning på app opbevaring. 

Programmet Azure Mobile aftale er udviklet til at hjælpe dig med at udvide liv og opbevaring af din app ved at indsende en metode til at indsamle og analysere detaljerede oplysninger om brugen af din app. Det hjælper dig med at klassificere din base ifølge funktionsmåde bruger, og oprette fokus kampagner til at levere pushmeddelelser og i app'en meddelelser på identificerede bruger målgrupper. KPI'er (KPI) mål er hvordan aktive dine brugere med forskellige aspekter af din app. Azure Mobile aftale indeholder de metoder, du har brug for at finde ud af disse KPI'er. Det hjælper med at øge afkastet af din investering (Investering) ved at indsende den infrastruktur, du har brug for at øge aftale til din mobile-app. 

For at få mest muligt ud af Azure Mobile aftale, skal du starte med en veldesignet aftale plan. Din plan hjælper dig med at identificere de detaljerede data, skal du kunne segment skal tildeles din bruger base. Søgningen kan være baseret på funktionsmåde, og i app'en opstår. I rækkefølge for din plan kan lykkes, er det en god fremgangsmåde til at definere klart den KPI, vil måle målene for din app. Med Ryd indikatorer er defineret, kan du nemt integrere nødvendige logik i din app til at indsamle fint kornet data, som du vil bruge til at analysere og evaluerer KPI'er. Dette emne er en bedste praksis vejledning til at definere KPI'er, du vil bruge med din aftale plan. 


## <a name="step-1-define-your-kpis-to-fit-the-bet-model"></a>Trin 1: Definere KPI'er så det passer til modellen GULDKORN


Korrekt definere KPI'er, kan det være en svært opgaven er afsluttet. Apps, der er udviklet til forskellige virksomheder har deres egne specifikke oplysninger og mål. Dette kan har tendens til at forveksle din tilgang. For at undgå dette, mål og KPI'er skal opdeles i tre primære kategorier: **Business**, **aftale**og **tekniske**. Dette er, hvad vi kalder **GULDKORN model**.

En god plan Generelt har målsætninger med de KPI'er, måler gunstige udfald i hver af de følgende kategorier af GULDKORN modellen.


#### <a name="business-kpis"></a>Business KPI'er

Business KPI'er skal være den nemmeste del til at oprette. Du defineret formentlig allerede disse i nogle formular når du planlagt din-mobilappen. Disse KPI'er Hjælp generelt måling indtægter og Investering til du app. Følgende liste indeholder nogle eksempel Business KPI'er, der kan hjælpe med at træffe du under definere dine indikatorer:

- Medier Business KPI'er
    - Antallet af reklamer klikkes på
    - Antal siden besøg per bruger
    - Antallet af aktuelle abonnementer
- Gaming Business KPI'er
    - Antallet af i app'en køb
    - Gennemsnitlig omsætning per bruger (ARPU)
    - Tid brugt hver enkelt session
    - Dage afspillet og aktuelle i game niveau
- E-handel Business KPI'er
    - Dage app bruges
    - Gennemsnitlig omsætning per bruger (ARPU)
    - Gennemsnitlig beløb i indkøbskurv betalingen
    - Produktkategori for de fleste visninger og køb
- Bank og forsikring Business KPI'er
    - Antallet af konti
    - Funktioner, der er aktiveret
    - Tilbud besøgt
    - Beskeder har klikket på eller aktiveret      



#### <a name="engagement-kpis"></a>Aftale KPI'er

En aftale KPI er en ydeevne indikator til at måle aftale dine brugere. Tendenser i dette område at finde ud af opbevaring af din app. Her er et par eksempel indikatorer for denne type KPI:

- Aktive brugere i de seneste 7 dage
- Inaktiv brugerantal for de seneste 7 dage
- Antallet af brugere, der ikke har brugt appen inden for 30 dage  

Nogle indlysende eksterne faktorer kan påvirke indikatorer i dette område. For eksempel kan du overveje en mobilenhed til at være med en bruger til enhver tid. Dette kan eller ikke kan være sande. En gaming app kan ofte have højere brugen omkring helligdage, når en spiller kan afspille flere mens ikke på arbejde eller ud på skole.   

Let at definere kan KPI'er i denne kategori hjælpe dig måler forholdet mellem din app og dine kunder.



#### <a name="technical-kpis"></a>Tekniske KPI'er

Indikatorer i denne kategori hjælpe dig med at fastslå, om din app opfører sig korrekt, hænger eller ned. Disse indikatorer kan måle tilstanden for din app og finde ud af brugervenligheden problemer, der kan forhindre brugere i at bruge appen. Oplysninger, der indsamles i denne kategori kan også indeholde ydeevneoplysninger, der er relevante for marketing teams. Dataene kan også være nyttig til fejlfinding af IT og support for teams at hjælpe med at identificere urapporteret fejl. 
 
Her er nogle eksempler på tekniske KPI'er:

- Oplysninger om ikke-afviklet eller håndteret undtagelse og antal 
- Tidsstemplet for sidste nedbrud
- Knappen sidste har klikket på eller sidste side besøgt
- Appen hukommelsesforbrug
- App ramme rente
- OS-version, der kører appen på
- App-version

Definere disse KPI'er for at måle app ydeevne og pinpoint potentielle fejl. Denne indikatorer måske være en hjælp til at reducere den tid, du har brug for til at levere en løsning til dine kunder. De kan også hjælpe dig med at definere et brugersegment, der er stødt på en bestemt problemer. Du kan bruge segmentering, der bruger til at oprette kampagner til levering af meddelelser om tilgængelige rettelser og potentielle salgsfremstød for at gendanne kundetilfredshed. 


#### <a name="playbook-exercise-1-create-your-kpi-dashboard"></a>Playbook opgave 1: Oprette dit KPI-dashboard

Når du definerer din strategi for marketing, skal din KPI'er præsentere en visning for hver af dine primære målsætninger. De skal være klart definerede datapunkter, der gør det muligt at indsamle vigtige oplysninger til overvågning af din app og funktionsmåden for slutbrugeren.

Oprette en KPI-dashboard, der indeholder den under oplysninger

1.  Hvad er KPI'erne til appen?
2.  Hvilke datapunkter skal jeg bruge til at repræsentere hver enkelt KPI?
3.  Hvor findes disse data til mit program (det vil sige skærm, indstillinger, system...)?
4.  Kan jeg afspille en aftale sekvens for denne KPI?

Du kan bruge **KPI Builder** regnearket i vores [Media Playbook skabelon] [ Media Playbook link] eksempler og vejledning.



## <a name="step-2-your-engagement-program"></a>Trin 2: Din aftale Program


Et godt mobile aftale program skal betragtes som en vigtig komponent i din app. Et godt Velkommen program, der udføres for en bruger i de første dage af appforbrug bør omfatte absolut. Det som regel, skal have en meget positiv effekt på aftale og opbevaring af din app. Undersøgelser har vist, at fleste brugere stoppe med at bruge en app de første par dage efter installationen. Du vil bestræbe dig på at opfylder eller overstiger kunde forventningen fører interesse tidligt, mens brugeren er stadig fokuserede på din app. Kontrollér, at du præsenterer nøgleværdi og fordele af din app til dine kunder. 


![](./media/mobile-engagement-getting-started-best-practices/unsegmented-push-notifications.png)

Pushmeddelelser er den bedste metode til tidlig aftaler med brugere af mobile enheder. Stor omhu bør dog tages, når segmentering brugere til pushmeddelelser. Fordi når en bruger, der fungerer som de bliver tildelt spam eller uninteresting meddelelser, kan det have alvorlige påvirker. En bruger kan slette dit program aldrig til at returnere med få klik. Brugeren skal modtage meget tilpassede i app-værdi i stedet for generisk spam.

Når brugere er aktivt involveret, kan derefter aftale programmet hjælpe drive andre aspekter af appen.

For eksempel kan du konfigurere en kampagnens, der anmoder om dine aktive brugere til at vurdere din app. Da denne brugersegment er de mest aktive og har de fleste oplevelsen med dig programmet, forventer du at give den mest præcise bedømmelse. Når du har en høj app bedømmelse, kan det hjælpe drev op organiske overførslen af din app samt reducere din nye kunde acquisition omkostninger.



#### <a name="engagement-sequence"></a>Aftale sekvens


En global aftale Program indeholder forskellige aftale serier. Hver sekvens har til formål at nå flere målsætninger.


###### <a name="life-push-sequence"></a>Liv opslagsnål sekvens


Målene for en liv opslagsnål sekvens er forskellige, afhængigt af brugerens aftale med appen livscyklus. En bestemt bruger kan være nye, inaktiv eller meget aktive. På forskellige stadier i en aftale livscyklussen brugere kan drage fordel af din nyt indhold i form af tip eller links til dokumentation. 

For eksempel har brug for en ny bruger at få et overblik over til en app eller få glæde af en ny bruger god grund stil med følgende første gang de starter appen...

*"Gerne har du om bord! Husk at logge på få din 1st måned gratis!"*


###### <a name="behavioral-push-sequence"></a>Funktionsmæssige push-sekvens

Funktionsmæssige push-sekvens har til formål at øge brugen baseret på bruger funktionalitet, der indsamles for appen.  

For eksempel en meget aktive bruger af en opdigtet football app kan få glæde af der gik sammen med følgende opslagsnål meddelelsen...

*"John du er en alvorlige football vifte! Log på vores NFL sektion og vinder gratis adgang til SuperBowl!"*


###### <a name="alerting-push-sequence"></a>Advarer push-sekvens

Brugere vil sætte pris på relevante nyheder fokuseret på deres interesser. En besked om push-sekvens forbedrer aftale ved at sende beskeder, der er baseret på interesser en bruger har klart vises. Det kan skyldes eksplicitte, når en bruger vælger deres egne interesser i appen. Det kan også bestemmes implicit baseret på data, der indsamles under brugerinput med appen.

Brugeren af en E-handel app kan for eksempel regelmæssigt købe et bestemt mærke kaffe, som du har hentet med en business KPI. Følgende besked kan forbedre denne brugers aftale med programmet.
 
*"Høj Wes, vil en af dine foretrukne mærker kaffe være til salg 25% fra den første uge i September 2015. Vi vil gerne du som en kunde og ønsker at sikre, at du var opmærksom."*

###### <a name="rentention-push-sequence"></a>Rentention opslagsnål sekvens

Denne fremgangsmåde har til formål at bevare brugere ved hjælp af en gentagne opslagsnål meddelelse kampagner til at øge en almindelig vane engagerende med programmet. Dette kan hjælpe med at øge app opbevaring, hvis brugeren har interaktionerne. 

Brugeren af en sports relaterede app modtager muligvis følgende opslagsnål meddelelsen ugentlig baseret på brugerens foretrukne grupper:

*"Med mulighed at vinde 200 point, gå stemme om New York Yankees vinder deres game, denne uge mod Toronto blå Jays!"*


#### <a name="the-3w-approach"></a>3W tilgangen

Aktiverede forskellige opslagsnål serier hjælper dig med at kommunikere med slutbrugere. Men, skal du stadig bruge 3W metode til at tilpasse dine meddelelser. 3W tilgangen henvender sig til hvem, hvad og hvornår til hver meddelelse. Hvis du tydeligt opfylder disse tre spørgsmål skal du beskeder korrekt fokuseret for aftale.

![](./media/mobile-engagement-getting-started-best-practices/who-what-when.png)



###### <a name="who-the-user-segment-that-will-receive-messages"></a>Hvem: Brugeren målgruppe, der skal modtage de meddelelser

Trykke på beskeder til brugerne skal betragtes en meget følsomme kommunikationskanal. Kontrollér, at de meddelelser, du har til formål at sende til et brugersegment godt er fastsat til brugersegment interesser. En forkert distribueret besked har sandsynligvis have en negativ indflydelse på en bruger. De kan undersøge det spam, hvilket medfører din app, fjernes. 

Brug en kombination af bestemte tekniske og funktionsmæssige kriterier, når du definerer bruger målgrupper, der skal modtage meddelelser. Et simpelt eksempel, der definerer et brugersegment skyldes svarende til følgende sætning:

"Alle brugere, der startes på en mobil-programmet til først gang 3 dage siden, og har besøgt siden login to gange uden faktisk at færdiggøre en login".
 
Denne sætning hjælper med at identificere de data, du har brug for at indsamle for at understøtte et bestemt scenarie.


###### <a name="what-the-message-that-you-will-send"></a>Hvad: Den meddelelse, du vil sende

**Tone**

Brug i dine aftaler en tone, der passer til dine for brugerne segmenteret. Dette er helt klart en god måde at holde kontakten med dine slutbrugere og fremme en brugers interesse i din app. 

**Omdirigering**

En meddelelse om opslagsnål kan bruges til mere end åbning af programmet. Hvis meddelelsen om opgaven giver en kontekst som broadcast nyheder eller produkt forfremmet, denne meddelelse kan deep link direkte til det rette indhold i programmet. For at understøtte denne, skal du oprette en URL-skema at lade programmet administrere omdirigering. Når du arbejder på din aftale serier, er dette et vigtigt trin, der ikke skal glemt.

Omdirigering kan også administreres for andre systemer. For eksempel med en handling URL-adresse er det muligt at omdirigere slutbrugere til mange andre systemer, herunder følgende:

- Et websted
- En postkasse med allerede konfigureret mail
- Et felt til SMS
- Tjenesten til et opkald
- Direkte til programlageret til vurdering af programmet. 

Dette giver mange muligheder for at deltage slutbrugere og opretter automatisk regler for at forbedre ydeevnen.


**Formatere/indhold**

Forskellige typer og opslagsnål meddelelse formater:

1. **Meddelelser** : gør det muligt at sende reklamer meddelelser til brugere på forskellige tid (af appen i app eller når).
2. **Afstemninger** : aktiveret at indsamle oplysninger fra slutbrugere ved at stille dem spørgsmål. Disse svar er derefter tilgængelige, når du opretter kriterier til destinationen slutbrugere.
3. **Data skubber** : gør det muligt at sende et binært tal eller base64 datafil for at opdatere appen. Oplysningerne i en data opslagsnål sendes til dit program til at tilpasse brugernes oplevelse i din app. Programmet skal være udviklet til at understøtte dataene i et enkelt tryk data.
4. **Felter (kun Windows Phone)** : Du kan bruge den Microsoft Push meddelelse Service (MPNS) til at sende oprindelige Push-meddelelse med XML-Data (understøttede siden SDK version 0.9.0. Den endelige overførsel for felter må ikke overstige 32 KB.). Meddelelsen vises direkte i din tavle-felt.
5. **Webvisning** : en webvisning er en pop op-med webindhold. Denne pop op-vindue vises, når slutbrugeren har klikket på opslagsnål meddelelsen. En webvisning kan du have flere interaktion med slutbrugeren.
 
>[AZURE.NOTE] Sørg for, at det indhold, du sender som pushmeddelelser i overensstemmelse med de respektive platform (iOS, Android, Windows) retningslinjer for udvikling af apps og sende pushmeddelelser.

 


###### <a name="when-the-timing-of-your-campaign"></a>Hvornår: Tidsindstilling for din kampagnens

Hvornår er det bedste tidspunkt at aktivere et kampagnens udløser pushmeddelelser? Skal det være manuel eller automatisk? Skal det tilbagevendende? Det er vigtigt at involvere brugerne med de bedste resultater ved fastlæggelse af det rette tidspunkt eller frekvens. For hver aftale sekvens og scenarie, skal du angive, hvornår er det bedste tidspunkt at sende pushmeddelelser. Her er nogle mulige eksempler:

![](./media/mobile-engagement-getting-started-best-practices/campaign-timing-examples.png)

Hvis du sender mange beskeder dagligt, skal du udføre alvorlige overvejelser, at brugerne kan opfatter din kommunikation som spam. 

Azure Mobile aftale er der to metoder til at undgå din kommunikation, der opfattes som spam. Først skal bruge fin korn segmentering til at sikre, at du ikke målrette de samme brugere. Desuden indeholder Azure Mobile aftale en funktion, "kvote". Denne funktion kan begrænse meddelelser, der er sendt til en campaign. For eksempel vil angive en standard-kvote til 5 om ugen sikre, at en bruger, der er inkluderet som en del af kampagnens brugersegment modtager ikke mere end 5 beskeder for denne uge.





#### <a name="playbook-exercise-2-create-your-engagement-program"></a>Playbook opgave 2: Opret dit aftale program

Bruge mere tid summering af dine mål og definerer de kampagner, du forventer at afspille ved hjælp af bestemte serier. Kontrollér, at du anvende 3W tilgangen på meddelelserne i din kampagner. 

Bruge **Aftale Program** regnearket i [Media Playbook skabelon] [ Media Playbook link] eksempler og vejledning.


## <a name="step-3-app-integration"></a>Trin 3: App-Integration


#### <a name="create-a-tag-plan"></a>Oprette en plan for mærke

Hvis du vil integrere Azure Mobile aftale i din app skal du oprette en plan for mærke. Mærke-plan er hjørnestenene af projektet. Den definerer relationen mellem marketing specifikationer, arbejde strømmen af programmet, og den reelle mærke data, der indsamles i appen for at måle KPI'er. Det angiver, hvilke analytics du vil kunne se på portalen. Du kan også definere bruger segmenter, og send fokus pushmeddelelser til at deltage dine slutbrugere. Når du har mærke defineret, tilføje koden for at integrere den i din app er enkelt ved hjælp af Azure Mobile aftale SDK.

Et mærke plan skal ikke Markér alt i et program. Det skal kun indeholde mærke data, som er en del af din strategi for mobile aftale. Dette er sandsynligvis forskelligartet mellem programmer. [Medier Playbook skabelon] [ Media Playbook link] leveres af Azure Mobile aftale hjælper med at opbygge en mærke plan med en given metode. Bruge **Mærke Plan** regnearket som en vejledning til at lave din mærke plan.

Når du definerer en mærke sektion i regnearket, være meget specifik. Dette er meget vigtigt for at undgå forvirring. Detaljeret hver forventet scenarie, hvor hvert mærke skal sendes. Indeholde navnet på den aktivitet, hvor hvert mærke er integreret. Dette bør alle medtages i **Informative** del af regnearket. Mærke plan regneark skal være primære referencen til test bekræftelse. 

I sektionen **Data til at indsamle** skal udviklingsteamet finde de typer, navne, værdier og ekstra oplysninger nøgle/værdi-par, der kræves for hver kode, der vil blive integreret i programmet på computeren.

Vi anbefaler, at gennemgå den mærke plan med alle grupper, der er knyttet til projektet. Foretag nødvendige rettelser, og Bekræft, at alt er klart for marketing og udvikling teams.

**Erklæring af arbejdet** regnearket kan bruges til at Hjælp-vejledning alle involveret i projektet.


#### <a name="data-types"></a>Datatyper

Dette er almindelige typer dataunderstøttelse ved Azure Mobile aftale.

###### <a name="devices-and-users"></a>Enheder og brugere

Azure Mobile aftale identificerer brugere ved at generere et entydigt id for hver enhed. Dette id kaldes enheds-id (eller deviceid). Den er oprettet i en sådan måde, at alle programmer, der kører på den samme enhed dele den samme enheds-id.

###### <a name="sessions-and-activities"></a>Sessioner og aktiviteter

En session er én forekomst af den app, som køres af en bruger. Sessionen strækker sig over gang brugeren starter appen, indtil det afbrydes.

En aktivitet er en logisk gruppering af en række ting appen kan gøre i en session. Det er normalt en bestemt skærm i appen, men det kan være noget defineret af logikken af programmet. Som minimum skal du mærke hver skærmen eller aktivitet for din app. Dette gør det muligt at forstå bruger-stien.


###### <a name="events"></a>Begivenheder

Hændelser, der bruges til at rapportere brugerinput med appen. De kan være Chat handlinger, som dele indhold eller ved at starte en video. Mærkning af hændelser, der giver dig datasamlinger, der viser, hvordan brugerne arbejder med appen. 

###### <a name="jobs"></a>Job

Job bruges til at rapportere handlinger, der har en varighed. Nogle eksempler omfatter:

- Udførelse af API-kald
- Vis klokkeslættet for reklamer
- Baggrund opgaver varighed 
- Køb proces varighed
- Få vist en video


###### <a name="errors"></a>Fejl

Fejl, der bruges til at rapportere problemer, der er fundet af appen. For eksempel forkerte brugerhandlinger, eller API opkald mislykkede.

###### <a name="application-information"></a>Følgende oplysninger om

Programoplysninger (App-Info) bruges til at mærke data, der er relateret til en brugers oplevelse med et program. Det er oprettet af en brugers interaktion med programmet. 

For en given app-info-nøglen Azure Mobile aftale kun holder styr på den sidste værdi (ingen oversigt). App-info viser status for din app eller dine slutbrugere. For eksempel status log ind, eller en brugers foretrukne produktgruppe.

###### <a name="crash-data"></a>Går ned i data

Ned data automatisk der indsamles via Mobile aftale SDK rapporter programfejl ikke behandlet af programmet. For eksempel en ikke-afviklet undtagelse, der opstår.


###### <a name="extra-data"></a>Ekstra data

Hændelser, fejl, aktiviteter og job kan forbedres med parametre. Dette er ekstra-oplysninger om en udvikler kan give som bestemte data fra programmet. Dette er vigtigt til at definere detaljerede segmentering. 

Værdien af en "artikel" mærke, for eksempel kan du til segmentet slutbrugere baseret på hvem der har set dette bestemte udstyr. Men, der muligvis ikke nok. Det kan være bedre, hvis samme "artikel" mærket desuden ekstra-oplysninger som "news_category" i en aktivitet. Det kan være nyttige til at bestemme dynamisk foretrukne kategorierne til brugeren. 

Ekstra oplysninger rapporteres som et tasten/værdi-par. I eksemplet for medier programmet ville ekstra oplysninger til "news_category" være værdien for den pågældende kategori. "Sports", "økonomi" eller "politik".





#### <a name="tag-and-sdk-integration"></a>Mærke- og SDK-integration 

Følg i dokumentationen til [Aftale SDK Integration](mobile-engagement-windows-store-integrate-engagement.md) på Azure websted for trinvise instruktioner til integrering af Azure Mobile aftale SDK i din app. Vælg din target platform linkene øverst på siden.

Vi anbefaler, at oprette projekter til to apps indbygget oven på Azure Mobile aftale. Én for udvikling og test midlertidige og et til fremstilling midlertidige. Din IT-teamet kan derefter opgradere fra test midlertidige til fremstilling når brugeren accept test er gået igennem.



#### <a name="user-acceptance-testing-uat"></a>Bruger accept test (UAT)

Bruger accept test (UAT) opnås ved at sikre, at alt fungerer efter hensigten. Arbejdsgange kan udføres og indsamle alle de påkrævede data baseret på din mærke plan:
 
- Oplysninger om mærkning skal være på plads ifølge dokumenterede AZME begreber
- Alle ønskede oplysninger der indsamles (herunder ekstra oplysninger og værdi App oplysninger)
- Terminologi stemmer overens med ifølge jelayout mærke planlægge
- Der er ingen dubletter mærker, der er sendt

Test grundigt alle typerne meddelelser funktionsmåde, du har integreret i din app

- Meddelelser, afstemninger, Data skubber af app og i app
- Tekst/Web-visninger
- Badge opdatering, kategorier



#### <a name="setup"></a>Konfiguration

Konfiguration af Azure Mobile aftale er meget simpel. Den dokumentation, der er relateret til brugergrænsefladen findes på webstedet Azure Mobile aftale, [hvordan du navigerer til brugergrænsefladen](mobile-engagement-user-interface-home.md).

Det anbefales, at du starter ved at konfigurere højre roller og medlemskab til brugerne af projektet. Dette hjælper dig med at administrere adgang til platformen for alle brugere. Din rolle kan omfatte følgende:

- Administratorer
- Udviklere
- Brugerne kan 

Bagefter:
- Registrere din deviceID teste på din egen enhed.
- Gå til indstillingerne for din konto, og Konfigurer tidszonen have diagrammer og meddelelse leveringstidspunktet angive for din tidszone.
- Gå til indstillingerne for dit program og registrere de "App-oplysninger" du skal målet slutbrugerlicensaftale lige ved hånden.

Du kan finde flere oplysninger om, hvordan du kører din første opslagsnål meddelelse kampagnens gennemgå, [hvordan du Introduktion, bruge og administrere skubber til Henvend dig til dine slutbrugere](mobile-engagement-how-tos.md).



## <a name="conclusion"></a>Konklusion


Aftale programmer er gentaget, og du bør løbende forbedre din egen, som du eksperimentere med hvad der fungerer bedst for din app. 

Først, mens du udvikle oplevelse med aftale strategier ikke forsøger at opbygge en strategi for hele global aftale. Tage en trinvis fremgangsmåde identificere KPI'er og hvordan du kan udnytte dem. Aftale strategi skal være entydige for hver app.

Når du har udviklet nogle oplevelse kan du overveje at tilføje følgende til programmerne aftale:

- Sporing: Du får fat på brugere, og du definere sandsynligvis dataindsamling kilder. Azure mobile aftale kan sammenkædes med dataindsamling kilder. Det kan du overvåge ydeevne for hver datakilde. Disse oplysninger er interessante for maksimere investeringen acquisition. 

- A / B test: Dette er en vigtig del af aftale program. Hver app har sin egen specifikke oplysninger. Med A / B test, kan du forbedre din aftale program.

- Geografisk placering: Dette er en stor mulighed for mærker. Du kan få fat på det rigtige sted tidspunkt sammen takket være denne funktion. Vi anbefaler, at bekræfte, at du har indsamlet nok slutbrugerlicensaftale funktionsmåde data før du begynder at bruge geografisk placering.

- Data opslagsnål: Data push er en usynlige opslagsnål. Data opslagsnål giver mulighed for tilpasning af dit program, der er baseret på slutbrugerlicensaftale funktionalitet. Eksempelvis hvis et brugersegment hører ofte høj tech produkter, kan ejeren af app sende en data opslagsnål, vil tilpasse hendes startside med høj tech indhold.



## <a name="next-steps"></a>Næste trin

- [Opret en Azure Mobile aftale konto](mobile-engagement-create.md).
- Besøg [Definer din strategi for Mobile aftale](mobile-engagement-define-your-mobile-engagement-strategy.md) for at lære mere om at definere din strategi for Mobile aftale.



  

<!--Image references-->


<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks
