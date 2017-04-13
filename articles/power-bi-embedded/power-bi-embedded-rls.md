<properties
   pageTitle="Række-sikkerhed på brugerniveau med Power BI integreret"
   description="Oplysninger om række-sikkerhed på brugerniveau med Power BI integreret"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="row-level-security-with-power-bi-embedded"></a>Række sikkerhed med Power BI integreret

Række sikkerhed (RLS) kan bruges til at begrænse brugeradgang til bestemte data i en rapport eller et datasæt, giver mulighed for flere forskellige brugere at bruge den samme rapport, mens alle se forskellige data. Power BI integrerede understøtter nu datasæt, der er konfigureret med RLS.

![](media\power-bi-embedded-rls\pbi-embedded-rls-flow-1.png)

For at udnytte RLS, er det vigtigt du forstår tre primære begreber; Brugere, roller og regler. Lad os se nærmere på de enkelte:

**Brugere** – dette er de faktiske slutbrugere visning af rapporter. I Power BI integrerede identificeres brugere af egenskaben username i en App-Token.

**Roller** – brugere tilhører roller. En rolle er en beholder for regler og kan navngives noget som "Salgschef" eller "Sælger". I Power BI integrerede identificeres brugere af egenskaben roller i et App-Token.

**Regler** – roller har regler, og disse regler er de faktiske filtre, der skal anvendes på dataene. Det kan være så enkelt som "land = USA" eller noget mere dynamisk.

### <a name="example"></a>Eksempel

I resten af denne artikel vil vi giver dig et eksempel på redigering RLS og forbrug, i et integreret program. Dette eksempel bruger [Analysis Detail](http://go.microsoft.com/fwlink/?LinkID=780547) PBIX filen.

![](media\power-bi-embedded-rls\pbi-embedded-rls-scenario-2.png)

Vores detailsalg analyse eksempel viser salg for alle butikker i en bestemt detailsalg kæde. Uden RLS, uanset hvilken distrikt manager logger på og visninger, rapporten, får de vist de samme data. Ledelsen har registreret hver distrikt manager får kun vist salg for butikker, de styrer, og for at gøre dette, kan vi bruge RLS.

RLS er oprettet i Power BI Desktop. Når datasæt og rapport åbnes, kan vi skifte til diagrammet visning for at se skemaet:

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-3.png)

Her er nogle ting at lægge mærke med dette skema:

-   Alle målinger, som **Samlet salg**, gemmes i tabellen **Sales** fakultet.
-   Der er fire yderligere relaterede dimensionstabeller: **element**, **klokkeslæt**, **Store**og **distrikt**.
-   Pile på relationslinjerne angiver, hvordan filtre kan flyde fra én tabel til en anden. Eksempelvis hvis filteret er placeret på **tid [dato]**, filtrere i det aktuelle skema det kun ned værdier i tabellen **Sales** . Ingen andre tabeller vil ikke påvirkes af dette filter, da alle pilene i relationslinjerne peger på tabellen Salg og ikke til stede.
-   Tabellen **distrikt** angiver, at der er leder for hver distrikt:

    ![](media\power-bi-embedded-rls\pbi-embedded-rls-district-table-4.png)

Baseret på dette skema, hvis vi anvende et filter på kolonnen **Distrikt Manager** i tabellen distrikt, og hvis, der filtrerer matcher den bruger, der åbner rapporten, vil også filtrere filteret ned tabellerne **Store** og **Salg** kun viser data til den bestemt distrikt manager.

Her er, hvordan:

1.  Klik på fanen modellering **Administrere roller**.  
![](media\power-bi-embedded-rls\pbi-embedded-rls-modeling-tab-5.png)

2.  Oprette en ny rolle kaldet **Manager**.  
![](media\power-bi-embedded-rls\pbi-embedded-rls-manager-role-6.png)

3.  Angiv følgende DAX-udtryk i tabellen **distrikt** : **[distrikt leder] = USERNAME()**  
![](media\power-bi-embedded-rls\pbi-embedded-rls-manager-role-7.png)

4.  Klik på **Vis som roller**for at sikre at reglerne, der arbejder på fanen **Modeling** , og angiv derefter følgende:  
![](media\power-bi-embedded-rls\pbi-embedded-rls-view-as-roles-8.png)

    Rapporterne, der viser nu data, som om du er logget på som **Andrew glidende gennemsnit**.

Anvendelse af filter, filtrerer som vi har her, ned alle poster i tabellerne **distrikt**, **Store**og **Salg** . Dog på grund af filter retning på relationerne mellem **Salg** og det **klokkeslæt**, vil **Salg** og **element**, og **element** og **tid** tabeller ikke blive filtreret ned.

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-9.png)

Der kan være ok til dette krav, men hvis vi ikke vil have ledere til at få vist elementer, de ikke har en hvilken som helst salg, kan vi aktivere tovejs krydsfiltrering for relationen og dataflow sikkerhedsfilteret i begge retninger. Dette kan gøres ved at redigere relationen mellem **Salg** og **element**, således:

![](media\power-bi-embedded-rls\pbi-embedded-rls-edit-relationship-10.png)

Nu, filtre kan også flyde fra tabellen Sales til tabellen **element** :

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-11.png)

**Bemærk!** Hvis du bruger DirectQuery-tilstand til dine data, skal du aktivere tovejs tværs filtrering ved at vælge disse to indstillinger:

1.  **Filen** -> **muligheder og indstillinger** -> **Funktionerne Preview** -> **Aktivér på tværs af filtrering i begge retninger for DirectQuery**.
2.  **Filen** -> **muligheder og indstillinger** -> **DirectQuery** -> **Tillad ubegrænset måling i DirectQuery-tilstand**.


Hvis du vil vide mere om tovejs krydsfiltrering, kan du hente [tovejs krydsfiltrering i SQL Server Analysis Services 2016 og Power BI Desktop](http://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional cross-filtering in Analysis Services 2016 and Power BI.docx) hvidbog.

Dette ombrydes op alt arbejde, der skal udføres i Power BI Desktop, men der er en mere arbejde, der skal udføres for at gøre RLS regler vi definerede arbejde i Power BI integreret. Brugere godkendte og autoriserede af dit program og App tokens bruges til at give denne brugeradgang til en bestemt Power BI integreret rapport. Power BI integrerede har ikke nogen bestemte oplysninger, som brugeren er. For RLS til at fungere sammen, skal du overføre nogle yderligere kontekst som en del af din app token:
-   **brugernavn** (valgfrit) – bruges sammen med RLS dette er en streng, der kan bruges til at hjælpe med at identificere brugeren, når du anvender RLS regler. Se Brug af række integreret sikkerhed med Power BI
-   **roller** – en streng, der indeholder rollerne vælge når anvende sikkerhed på brugerniveau række regler. Hvis der passerer mere end én rolle, skal de overføres som en strengmatrix.

Hvis egenskaben username findes, skal du også overføre mindst én værdi i roller.

Fuld app tokenet ser nogenlunde således ud:

![](media\power-bi-embedded-rls\pbi-embedded-rls-app-token-string-12.png)

Nu med alle elementerne sammen, vil når en person logger ind i vores program tilladelse til at få vist denne rapport, de kun kunne se de data, de har tilladelse til at se, som defineret af vores række-sikkerhed på brugerniveau.

![](media\power-bi-embedded-rls\pbi-embedded-rls-dashboard-13.png)

## <a name="see-also"></a>Se også
[Række-sikkerhed på brugerniveau (RLS) med Power](https://powerbi.microsoft.com/en-us/documentation/powerbi-admin-rls/)
