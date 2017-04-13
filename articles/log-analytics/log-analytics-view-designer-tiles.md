<properties
    pageTitle="Log Analytics visning Forespørgselsdesigner feltet Reference | Microsoft Azure"
    description="Vis Designer i Log Analytics gør det muligt at oprette brugerdefinerede visninger i konsollen OMS, der indeholder forskellige visualiseringer af data i OMS-lager. Denne artikel indeholder en reference af indstillingerne for hver af de felter, der er tilgængelige til brug i dine brugerdefinerede visninger."
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
    ms.date="09/27/2016"
    ms.author="bwren"/>

# <a name="log-analytics-view-designer-tile-reference"></a>Log Analytics visning Forespørgselsdesigner feltet Reference
Vis Designer i Log Analytics gør det muligt at oprette brugerdefinerede visninger i konsollen OMS, der indeholder forskellige visualiseringer af data i OMS-lager. Denne artikel indeholder en reference af indstillingerne for hver af de felter, der er tilgængelige til brug i dine brugerdefinerede visninger.

Andre artikler tilgængelige til visning Designer er:

- [Vis Designer](log-analytics-view-designer.md) - oversigt over de Designer visninger og procedurerne for at oprette og redigere brugerdefinerede visninger.
- [Visualisering del referencen](log-analytics-view-designer-parts.md) - referencen til indstillingerne for hver af de felter, der er tilgængelige til brug i dine brugerdefinerede visninger. 


I følgende tabel vises de forskellige typer felter, der er tilgængelige i visningen Designer.  I nedenstående afsnit beskrives hver felttype i detaljer og deres egenskaber.

| Side om side | Beskrivelse |
|:--|:--|
| [Tal](#number-tile) | Enkelt tal, der viser antallet af poster fra en forespørgsel. |
| [To tal](#two-numbers-tile) | To enkelt tal, der viser antallet af poster fra to forskellige forespørgsler. |
| [Krans](#donut-tile) | Krans diagram baseret på en forespørgsel med en oversigt over værdi i midten. |
| [Kurvediagram og billedforklaring](#line-chart-amp-callout-tile) | Kurvediagram baseret på en forespørgsel og en billedforklaring med en oversigt over værdi. |
| [Kurvediagram](#line-chart-tile) | Kurvediagram baseret på en forespørgsel. |
| [To tidslinjer](#two-timelines-tile) | Søjlediagram med to serier hver baseret på en separat forespørgsel. |



## <a name="number-tile"></a>Tal felt

Feltet **tal** viser et enkelt tal, der viser antallet af poster fra en log forespørgsel og en etiket.

![Tal felt](media/log-analytics-view-designer/tile-number.png)

| Indstilling | Beskrivelse |
|:--|:--|
| Navn        | Tekst, der skal vises øverst i feltet. |
| Beskrivelse | Tekst, der skal vises under feltet navn.    |
| **Side om side** |
| FORKLARING | Tekst, der vises under værdien. |
| Forespørgsel | Forespørgsel til at køre.  Antallet af antallet poster returneres i forespørgslen vises. |
| **Avanceret** |  **> Dataflow-godkendelse** |
| Aktiveret | Vælg, hvis dataflow-bekræftelse skal være aktiveret for feltet.  Dette giver en alternativ meddelelse, hvis dataene ikke er tilgængelig for feltet.  Dette er typisk bruges til at angive en meddelelse i midlertidige perioden, når visningen er installeret og data kommer tilgængelige. |
| Forespørgsel | Forespørgsel for at køre for at kontrollere, om dataene er tilgængelig for visningen.  Hvis forespørgslen ikke returnerer nogen resultater, vises der en meddelelse i stedet for værdien fra den primære forespørgsel. |
| Meddelelse | Meddelelse for at vise Hvis dataflow-bekræftelse forespørgslen returnerer ingen data.  Hvis du giver ingen meddelelse, vises *Udføre vurdering* . |
| **Tidsinterval** |
| Varighed | Varighed fra dags dato skal bruges til et tidsinterval i forespørgslen.  Eksempelvis hvis **7 dage** er angivet, er derefter forespørgslen begrænset til poster, der er oprettet ud fra 7 dage siden til dags dato. |
| Afslut data forskydning | Valgfri forskydning fra den aktuelle data til brug i et tidsinterval på den primære forespørgsel.  Eksempelvis hvis **-1 dag** bruges til **slutningen dato forskydning** og **7 dage** for **varighed**, er derefter forespørgslen begrænset til poster, der er oprettet ud fra 8 dage siden til i går. |

## <a name="two-numbers-tile"></a>To tal side om side

Feltet **To tal** viser to tal, der viser antallet af poster fra to forskellige log forespørgsler og en etiket for hver.

![To tal side om side](media/log-analytics-view-designer/tile-two-numbers.png)

| Indstilling | Beskrivelse |
|:--|:--|
| Navn        | Tekst, der skal vises øverst i feltet. |
| Beskrivelse | Tekst, der skal vises under feltet navn.    |
| **Første felt** |
| FORKLARING | Tekst, der vises under værdien. |
| Forespørgsel | Forespørgsel til at køre.  Antallet af antallet poster returneres i forespørgslen vises. |
| **Andet felt** |
| FORKLARING | Tekst, der vises under værdien. |
| Forespørgsel | Forespørgsel til at køre.  Antallet af antallet poster returneres i forespørgslen vises. |
| **Avanceret** | **> Dataflow-godkendelse** |
| Aktiveret | Vælg, hvis dataflow-bekræftelse skal være aktiveret for feltet.  Dette giver en alternativ meddelelse, hvis dataene ikke er tilgængelig for feltet.  Dette er typisk bruges til at angive en meddelelse i midlertidige perioden, når visningen er installeret og data kommer tilgængelige. |
| Forespørgsel | Forespørgsel for at køre for at kontrollere, om dataene er tilgængelig for visningen.  Hvis forespørgslen ikke returnerer nogen resultater, vises der en meddelelse i stedet for værdien fra den primære forespørgsel. |
| Meddelelse | Meddelelse for at vise Hvis dataflow-bekræftelse forespørgslen returnerer ingen data.  Hvis du giver ingen meddelelse, vises *Udføre vurdering* . |
| **Tidsinterval** |
| Varighed | Varighed fra dags dato skal bruges til et tidsinterval i forespørgslen.  Eksempelvis hvis **7 dage** er angivet, er derefter forespørgslen begrænset til poster, der er oprettet ud fra 7 dage siden til dags dato. |
| Afslut data forskydning | Valgfri forskydning fra den aktuelle data til brug i et tidsinterval på den primære forespørgsel.  Eksempelvis hvis **-1 dag** bruges til **slutningen dato forskydning** og **7 dage** for **varighed**, er derefter forespørgslen begrænset til poster, der er oprettet ud fra 8 dage siden til i går. |

## <a name="donut-tile"></a>Krans felt

Feltet **kranse** viser et enkelt tal, der er opsummeret fra en værdikolonne i en log forespørgsel.  Krans viser grafisk resultaterne af de øverste tre poster.

![Krans felt](media/log-analytics-view-designer/tile-donut.png)

| Indstilling | Beskrivelse |
|:--|:--|
| Navn        | Tekst, der skal vises øverst i feltet. |
| Beskrivelse | Tekst, der skal vises under feltet navn.    |
| **Krans** |
| Forespørgsel | Forespørgslen køres til krans.  Den første egenskab skal være en tekstværdi og den anden egenskab en numerisk værdi.  Dette er typisk en forespørgsel, der bruger nøgleordet **mål** til at opsummere resultater. |
| **Krans** | **> Center** |
| Tekst | Tekst, der vises under værdien i krans. |
| Handling | Handlingen, der skal udføres på egenskaben value til at opsummere til en enkelt værdi.<br><br>-Sum: Addere værdierne af alle poster med egenskabsværdien.<br>-Procentdel: Procentdel af de summerede værdier fra poster med den egenskabsværdi, der sammenlignes med de summerede værdier for alle poster. |
| Resultatværdier, der bruges i center operation | Du kan også klikke på plustegnet for at tilføje en eller flere værdier.  Resultaterne af forespørgslen begrænses til poster med de egenskabsværdier, du angiver.  Hvis der er tilføjet nogen værdier, end alle poster, der er medtaget i forespørgslen. |
| **Krans** | **> Flere indstillinger** |
| Farver | Farven, der skal vises for hver af de tre øverste egenskaber.  Hvis du vil angive alternative farver til specifikke egenskabsværdier, skal du bruge avancerede tilknytning af farve. |
| Avancerede farveskema | Viser en farve til specifikke egenskabsværdier.  Hvis den angivne værdi er i den øverste tre, vises den alternative farve i stedet for den standardfarve.  Hvis egenskaben ikke er i den øverste tre, derefter vises farven ikke. |
| **Avanceret** | **> Dataflow-godkendelse** |
| Aktiveret | Vælg, hvis dataflow-bekræftelse skal være aktiveret for feltet.  Dette giver en alternativ meddelelse, hvis dataene ikke er tilgængelig for feltet.  Dette er typisk bruges til at angive en meddelelse i midlertidige perioden, når visningen er installeret og data kommer tilgængelige. |
| Forespørgsel | Forespørgsel for at køre for at kontrollere, om dataene er tilgængelig for visningen.  Hvis forespørgslen ikke returnerer nogen resultater, vises der en meddelelse i stedet for værdien fra den primære forespørgsel. |
| Meddelelse | Meddelelse for at vise Hvis dataflow-bekræftelse forespørgslen returnerer ingen data.  Hvis du giver ingen meddelelse, vises *Udføre vurdering* . |
| **Tidsinterval** |
| Varighed | Varighed fra dags dato skal bruges til et tidsinterval i forespørgslen.  Eksempelvis hvis **7 dage** er angivet, er derefter forespørgslen begrænset til poster, der er oprettet ud fra 7 dage siden til dags dato. |
| Afslut data forskydning | Valgfri forskydning fra den aktuelle data til brug i et tidsinterval på den primære forespørgsel.  Eksempelvis hvis **-1 dag** bruges til **slutningen dato forskydning** og **7 dage** for **varighed**, er derefter forespørgslen begrænset til poster, der er oprettet ud fra 8 dage siden til i går. |

## <a name="line-chart-tile"></a>Feltet kurvediagram

Feltet **kurvediagram** viser et kurvediagram med flere serier fra en forespørgsel, log over tid.  

![Kurvediagram og billedforklaring felt](media/log-analytics-view-designer/tile-line-chart.png)

| Indstilling | Beskrivelse |
|:--|:--|
| Navn        | Tekst, der skal vises øverst i feltet. |
| Beskrivelse | Tekst, der skal vises under feltet navn.    |
| **Kurvediagram** |  
| Forespørgsel | Forespørgsel til at køre til kurvediagram.  Den første egenskab skal være en tekstværdi og den anden egenskab en numerisk værdi.  Dette er typisk en forespørgsel, der bruger nøgleordet **mål** til at opsummere resultater.  Hvis forespørgslen bruger nøgleordet **interval** brug x-aksen i diagrammet dette tidsinterval.  Hvis forespørgslen ikke inkluderer nøgleordet **interval** bruges hver time intervaller til x-aksen. |
| **Kurvediagram** | **> Y-aksen** |
| Brug logaritmisk skala | Vælg for at bruge en logaritmisk skala for y-aksen. |
| Enheder | Angive enhederne for de værdier, der returneres af forespørgslen.  Disse oplysninger bruges til at vise etiketter på det diagram, der angiver værdityperne og eventuelt til konvertering af værdierne.  **Enhedstype** angiver, hvilken kategori af enheden, der definerer de **Aktuelle enhedstype** værdier, der er tilgængelige.  Hvis du vælger en værdi i **konvertere til** konverteres de numeriske værdier fra den **Aktuelle enhed** type til typen **konvertere til** . |
| Brugerdefineret etiket | Tekst, der vises for Y-aksen ud for etiketten for enhedstypen.  Hvis ingen etiket ikke er angivet, vises kun enhedstype. |
| **Avanceret** | **> Dataflow-godkendelse** |
| Aktiveret | Vælg, hvis dataflow-bekræftelse skal være aktiveret for feltet.  Dette giver en alternativ meddelelse, hvis dataene ikke er tilgængelig for feltet.  Dette er typisk bruges til at angive en meddelelse i midlertidige perioden, når visningen er installeret og data kommer tilgængelige. |
| Forespørgsel | Forespørgsel for at køre for at kontrollere, om dataene er tilgængelig for visningen.  Hvis forespørgslen ikke returnerer nogen resultater, vises der en meddelelse i stedet for værdien fra den primære forespørgsel. |
| Meddelelse | Meddelelse for at vise Hvis dataflow-bekræftelse forespørgslen returnerer ingen data.  Hvis du giver ingen meddelelse, vises *Udføre vurdering* . |
| **Tidsinterval** |
| Varighed | Varighed fra dags dato skal bruges til et tidsinterval i forespørgslen.  Eksempelvis hvis **7 dage** er angivet, er derefter forespørgslen begrænset til poster, der er oprettet ud fra 7 dage siden til dags dato. |
| Afslut data forskydning | Valgfri forskydning fra den aktuelle data til brug i et tidsinterval på den primære forespørgsel.  Eksempelvis hvis **-1 dag** bruges til **slutningen dato forskydning** og **7 dage** for **varighed**, er derefter forespørgslen begrænset til poster, der er oprettet ud fra 8 dage siden til i går. |


## <a name="line-chart--callout-tile"></a>Stregfelt diagram og billedforklaring

Feltet **kurvediagram og billedforklaring** viser et kurvediagram med flere serier fra en forespørgsel, log over tid og en billedforklaring med en værdi.  

![Kurvediagram og billedforklaring felt](media/log-analytics-view-designer/tile-line-chart-callout.png)

| Indstilling | Beskrivelse |
|:--|:--|
| Navn        | Tekst, der skal vises øverst i feltet. |
| Beskrivelse | Tekst, der skal vises under feltet navn.    |
| **Kurvediagram** |  
| Forespørgsel | Forespørgsel til at køre til kurvediagram.  Den første egenskab skal være en tekstværdi og den anden egenskab en numerisk værdi.  Dette er typisk en forespørgsel, der bruger nøgleordet **mål** til at opsummere resultater.  Hvis forespørgslen bruger nøgleordet **interval** brug x-aksen i diagrammet dette tidsinterval.  Hvis forespørgslen ikke inkluderer nøgleordet **interval** bruges hver time intervaller til x-aksen. |
| **Kurvediagram** | **> Billedforklaring** |
| Billedforklaring | Titeltekst skal vises over billedforklaring værdi. |
| Serienavn | Egenskabsværdi for serien skal bruges til værdien billedforklaring.  Hvis ingen serie ikke er angivet, bruges alle poster fra forespørgslen. |
| Handling | Handlingen, der skal udføres på egenskaben value til at opsummere til en enkelt værdi for billedforklaringen.<br>-Gennemsnit: Gennemsnittet af værdien fra alle poster.<br><br>-Antal: Antallet af alle poster returneres i forespørgslen.<br>-Sidste eksempel: Værdi fra det sidste interval, der er inkluderet i diagrammet.<br>-Maks: Maksimumværdi fra de intervaller, der er inkluderet i diagrammet.<br>-Min: Minimumværdi fra de intervaller, der er inkluderet i diagrammet.<br>-Sum: Summen af værdien fra alle poster. |
| **Kurvediagram** | **> Y-aksen** |
| Brug logaritmisk skala | Vælg for at bruge en logaritmisk skala for y-aksen. |
| Enheder | Angive enhederne for de værdier, der returneres af forespørgslen.  Disse oplysninger bruges til at vise etiketter på det diagram, der angiver værdityperne og eventuelt til konvertering af værdierne.  **Enhedstype** angiver, hvilken kategori af enheden, der definerer de **Aktuelle enhedstype** værdier, der er tilgængelige.  Hvis du vælger en værdi i **konvertere til** konverteres de numeriske værdier fra den **Aktuelle enhed** type til typen **konvertere til** . |
| Brugerdefineret etiket | Tekst, der vises for Y-aksen ud for etiketten for enhedstypen.  Hvis ingen etiket ikke er angivet, vises kun enhedstype. |
| **Avanceret** | **> Dataflow-godkendelse** |
| Aktiveret | Vælg, hvis dataflow-bekræftelse skal være aktiveret for feltet.  Dette giver en alternativ meddelelse, hvis dataene ikke er tilgængelig for feltet.  Dette er typisk bruges til at angive en meddelelse i midlertidige perioden, når visningen er installeret og data kommer tilgængelige. |
| Forespørgsel | Forespørgsel for at køre for at kontrollere, om dataene er tilgængelig for visningen.  Hvis forespørgslen ikke returnerer nogen resultater, vises der en meddelelse i stedet for værdien fra den primære forespørgsel. |
| Meddelelse | Meddelelse for at vise Hvis dataflow-bekræftelse forespørgslen returnerer ingen data.  Hvis du giver ingen meddelelse, vises *Udføre vurdering* . |
| **Tidsinterval** |
| Varighed | Varighed fra dags dato skal bruges til et tidsinterval i forespørgslen.  Eksempelvis hvis **7 dage** er angivet, er derefter forespørgslen begrænset til poster, der er oprettet ud fra 7 dage siden til dags dato. |
| Afslut data forskydning | Valgfri forskydning fra den aktuelle data til brug i et tidsinterval på den primære forespørgsel.  Eksempelvis hvis **-1 dag** bruges til **slutningen dato forskydning** og **7 dage** for **varighed**, er derefter forespørgslen begrænset til poster, der er oprettet ud fra 8 dage siden til i går. |

## <a name="two-timelines-tile"></a>To tidslinjer felt

Feltet **to tidslinjer** viser resultaterne af to log forespørgsler over tid som liggende søjlediagrammer.  En billedforklaring vises for hver serie.  

![To tidslinjer felt](media/log-analytics-view-designer/tile-two-timelines.png)

| Indstilling | Beskrivelse |
|:--|:--|
| Navn        | Tekst, der skal vises øverst i feltet. |
| Beskrivelse | Tekst, der skal vises under feltet navn.    |
| Første diagram   
| FORKLARING | Tekst, der vises under boblen for den første serie.
| Farve | Farve skal bruges til kolonnerne i den første serie.
| Diagram forespørgsel | Forespørgslen køres til den første serie.  Antallet af antallet poster over hvert tidsinterval repræsenteres af kolonnerne diagram.
| Handling | Handlingen, der skal udføres på egenskaben value til at opsummere til en enkelt værdi for billedforklaringen.<br><br>-Gennemsnit: Gennemsnittet af værdien fra alle poster.<br>-Antal: Antallet af alle poster returneres i forespørgslen.<br>-Sidste eksempel: Værdi fra det sidste interval, der er inkluderet i diagrammet.<br>-Maks: Maksimumværdi fra de intervaller, der er inkluderet i diagrammet.
| **Det andet diagram** |
| FORKLARING | Tekst, der vises under boblen for den anden række.
| Farve | Farve skal bruges til kolonnerne i anden række.
| Diagram forespørgsel | Forespørgslen køres til den anden række.  Antallet af antallet poster over hvert tidsinterval repræsenteres af kolonnerne diagram.
| Handling | Handlingen, der skal udføres på egenskaben value til at opsummere til en enkelt værdi for billedforklaringen.<br><br>-Gennemsnittet: Gennemsnittet af værdien fra alle poster.<br>-Antal: Antallet af alle poster returneres i forespørgslen.<br>-Sidste eksempel: Værdi fra det sidste interval, der er inkluderet i diagrammet.<br>-Maks: Maksimumværdi fra de intervaller, der er inkluderet i diagrammet. |
| **Avanceret** | **> Dataflow-godkendelse** |
| Aktiveret | Vælg, hvis dataflow-bekræftelse skal være aktiveret for feltet.  Dette giver en alternativ meddelelse, hvis dataene ikke er tilgængelig for feltet.  Dette er typisk bruges til at angive en meddelelse i midlertidige perioden, når visningen er installeret og data kommer tilgængelige. |
| Forespørgsel | Forespørgsel for at køre for at kontrollere, om dataene er tilgængelig for visningen.  Hvis forespørgslen ikke returnerer nogen resultater, vises der en meddelelse i stedet for værdien fra den primære forespørgsel. |
| Meddelelse | Meddelelse for at vise Hvis dataflow-bekræftelse forespørgslen returnerer ingen data.  Hvis du giver ingen meddelelse, vises *Udføre vurdering* . |
| **Tidsinterval** |
| Varighed | Varighed fra dags dato skal bruges til et tidsinterval i forespørgslen.  Eksempelvis hvis **7 dage** er angivet, er derefter forespørgslen begrænset til poster, der er oprettet ud fra 7 dage siden til dags dato. |
| Afslut data forskydning | Valgfri forskydning fra den aktuelle data til brug i et tidsinterval på den primære forespørgsel.  Eksempelvis hvis **-1 dag** bruges til **slutningen dato forskydning** og **7 dage** for **varighed**, er derefter forespørgslen begrænset til poster, der er oprettet ud fra 8 dage siden til i går. |

## <a name="next-steps"></a>Næste trin

- Få mere at vide om [log søgninger](log-analytics-log-searches.md) til at understøtte forespørgsler i fliser.
- Tilføj [Visualisering webdele](log-analytics-view-designer-parts.md) på den brugerdefinerede visning.