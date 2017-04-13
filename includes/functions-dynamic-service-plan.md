## <a name="dynamic-service-plan"></a>Dynamiske-serviceaftale

I de dynamiske Service planer, der dine funktionen apps skal tildeles en forekomst af funktionen app. Hvis det er nødvendigt flere forekomster tilføjes dynamisk.
Disse forekomster kan dække på tværs af flere dataressourcer, foretage mest ud af den tilgængelige Azure infrastruktur. Desuden kører din funktioner parallelt minimere den samlede tid, det er nødvendigt at behandle anmodninger. Kørselstid for hver funktion adderet i sekunder, og samlet af appen med funktionen. Med omkostninger indsatsbaseret med antallet af forekomster, deres hukommelsesstørrelse og samlet kørselstid målt i GB sekunder. Dette er en fremragende indstilling, hvis dine Beregn behov er forbigående eller din sag gange har tendens til at være meget kort, som du kan kun betale for Beregn ressourcer, når de rent faktisk er i brug.   

### <a name="memory-tier"></a>Hukommelse niveau

Afhængigt af din funktion skal du kan vælge mængden hukommelse, der er nødvendige for at køre dem i funktionen App (objektbeholder af funktioner).
Hukommelse størrelse indstillingerne varierer fra **128 MB til 1536 MB**. Den valgte hukommelsesstørrelse svarer til den arbejdssæt bruges til alle de funktioner, der er en del af din app til funktionen. Hvis din kode kræver mere hukommelse, end den valgte størrelse, lukkes funktionen app forekomsten på grund af manglende tilgængelig hukommelse.

### <a name="scaling"></a>Skalering

Azure funktioner platform evaluerer trafikbehov, baseret på de konfigurerede udløsere til at afgøre, hvornår skalere op eller ned. Granularitet skalering er funktionen app'en. Skalering betyder i dette tilfælde, at tilføje flere forekomster af en funktionen app. Omvendt som trafikken går ned, er funktionen app forekomster deaktiveret – til sidst skalering til nul når ingen programmer kører.  

### <a name="resource-consumption-and-billing"></a>Ressourceforbrug og fakturering

I dynamisk tilstand ressourceallokering er gjort anderledes end den standard App serviceaftale, derfor forbrug modellen er også er anderledes, giver mulighed for en "Betal per brug"-model. Forbrug rapporteres per funktionen app kun for tid, når der udføres kode.  
Det beregnes ved at gange den hukommelsesstørrelse (i GB) ved hjælp af den samlede mængde af tid (i sekunder) for alle de funktioner, der kører i pågældende funktionen app. Måleenheden for forbrug bliver **GB-s (Gigabyte sekunder)**.