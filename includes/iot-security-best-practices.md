# <a name="internet-of-things-security-best-practices"></a>Sikkerhed bedste fremgangsmåder for Internet af ting

Infrastruktur kræver en fuldkommen sikkerhed i dybde-strategi for at sikre en Internet af ting (IoT). Denne strategi kræver, at sikre data i skyen, beskytte dataintegriteten undervejs over det offentlige internet og klargøre sikkert enheder. Hvert lag opbygger større garanti for sikkerhed i den overordnede infrastruktur.

## <a name="secure-an-iot-infrastructure"></a>Sikre en IoT-infrastruktur

Denne strategi for sikkerhed i dybde kan udvikles og udført med aktiv deltagelse af forskellige spillere, der er involveret i produktion, udvikling og implementering af IoT enheder og infrastruktur. Følgende er en overordnet beskrivelse af disse afspillere.  

- **IoT hardware producenten/integrator**: disse er typisk producenter af IoT hardware er installeret, ændres, montering af hardware fra forskellige producenter eller leverandører, der leverer hardware til en installation med IoT fremstilles eller integreret med andre leverandører.
- **IoT løsningsudvikleren**: udvikling af en IoT løsning gøres typisk af en løsningsudvikleren. Denne udvikler kan en del af et internt team eller Workflowsystemet (SI), der har specialiseret sig i denne aktivitet. Løsningsudvikleren IoT kan udvikle forskellige komponenter af IoT-løsning fra bunden, integrere forskellige off-the-shelf eller open source-komponenter eller vedtage forudkonfigurerede løsninger med mindre tilpasning.
- **Deployer til løsning af IoT**: efter en IoT løsning udvikles, der skal anvendes i feltet. Dette omfatter installation af hardware, sammenkobling af enheder og implementering af løsninger, i hardwareenheder eller skyen.
- **IoT løsning operator**: efter IoT-løsningen er installeret, det kræver langsigtet drift, overvågning, opgraderinger og vedligeholdelse. Dette kan gøres ved en in-house team, der omfatter oplysninger teknologiske eksperter, hardware operationer og vedligeholdelse af grupper og domæne-specialister, som overvåger overordnede IoT infrastruktur korrekte funktionsmåde.

I de følgende afsnit indeholder bedste fremgangsmåder for hver af disse spillere til at hjælpe med at udvikle, implementere og drive en sikker infrastruktur til IoT.

## <a name="iot-hardware-manufacturerintegrator"></a>IoT hardware producenten/integrator

Følgende er de bedste fremgangsmåder for IoT hardwareproducenter og hardware ændres.

- **Omfang hardware til minimumskrav**: hardwaredesign bør omfatte de mindste funktioner, der kræves til drift af hardwaren, og intet andet. Et eksempel er at medtage USB-porte, hvis det er nødvendigt til drift af enheden. Disse yderligere funktioner, Åbn enheden for uønskede angrebsvektorer, bør undgås.
- **Foretage hardware pille bevis**: bygge i mekanismer til registrering af fysiske indgreb som åbning på forsiden af enheden, eller at fjerne en del af enheden. Disse pille signaler kan være en del af datastrømmen overført til skyen, som kan advare ledere af disse hændelser.
- **Opbygge omkring sikker hardware**: tillader, at hvis forbrug, opbygge sikkerhedsfunktioner som sikker og krypteret opbevaring eller starte funktionalitet, der er baseret på Trusted Platform Module (TPM). Disse funktioner gør enheder mere sikre og beskytte den overordnede IoT-infrastruktur.
- **Foretage sikker opgraderinger**: firmwareopgraderinger levetid af enheden er uundgåelige. Opbygning af enheder med sikker stier til opgraderinger og kryptografiske sikkerhed af firmwareversioner tillader enheden at blive sikker under og efter opgraderinger.

## <a name="iot-solution-developer"></a>IoT løsningsudvikleren

Følgende er de bedste fremgangsmåder for udviklere af IoT løsninger:

- **Følg sikker software development metode**: udvikling af sikker software kræver, at tro, at jorden op om sikkerhed fra starten af projektet helt til dens gennemførelse, test og implementering. Valg af platforme, sprog og værktøjer er påvirket med denne metode. Microsoft Security Development Lifecycle giver en trinvis fremgangsmåde til at oprette sikker software.
- **Vælg open source - software med omhu**: Open source-software giver mulighed for at hurtigt at udvikle løsninger. Når du vælger open source-software, skal du overveje aktivitetsniveau for hver komponent, open source-Fællesskabet. En aktiv Fællesskabet sikrer software understøttes, og at problemer er opdaget og rettet. Du kan også understøttes muligvis ikke en sløre og inaktive open source-software og registreres problemer sandsynligvis ikke.
- **Integrere med omhu**: der findes mange software sikkerhedsmangler på grænsen af biblioteker og API'er. Funktioner, der ikke er nødvendige for den aktuelle installation kan stadig være tilgængelige via et API-lag. For at sikre den overordnede sikkerhed, skal du kontrollere alle grænseflader af komponenter, der er integreret til sikkerhedsmangler.      

## <a name="iot-solution-deployer"></a>IoT løsning deployer

Bedste praksis for IoT løsning kun er følgende:

- **Installation af hardware sikkert**: IoT installationer kan kræve hardware installeres på usikker placeringer, f.eks offentlige rum eller fungerer landestandarder. I sådanne situationer skal sikre, at hardware-installation er sikret mod manipulation i det omfang. Hvis USB- eller andre porte er tilgængelige i hardwaren, kan du sikre dig, at de er dækket sikkert. Mange angrebsvektorer kan bruge disse som genveje.
- **Sikre godkendelsesnøgler**: under installationen kræver enheds-id'er og tilknyttede godkendelsesnøgler, der er genereret af tjenesten cloud hver enhed. Beskytte disse nøgler fysisk selv efter installationen. En kompromitteret nøgle kan bruges af en skadelig enhed til som en eksisterende enhed.

## <a name="iot-solution-operator"></a>IoT løsning operator

Følgende er de bedste fremgangsmåder for IoT løsning operatorer:

- **Holde systemet opdateret**: sikre, at enheden operativsystemer og alle enhedsdrivere opgraderes til de nyeste versioner. Hvis du slår Automatiske opdateringer i Windows-10 (IoT eller andre varenumre), holder Microsoft det opdateret, at skabe et sikkert operativsystem for IoT enheder. Andre operativsystemer (f.eks Linux), holde sikre opdateret med til at, at de også er beskyttet mod skadelige angreb.
- **Beskyt mod skadelig aktivitet**: Hvis operativsystemet tillader det, kan du installere de seneste funktioner af antivirus- og Antimalwareprogram på hver enhed operativsystemet. Dette kan hjælpe med at afhjælpe de fleste eksterne trusler. Du kan beskytte de fleste moderne operativsystemer mod trusler ved at træffe passende foranstaltninger.
- **Overvåg ofte**: overvågning IoT infrastruktur til sikkerhedsrelaterede problemer er nøglen, når du reagerer på sikringsrelaterede hændelser. De fleste operativsystemer indeholder indbyggede hændelseslogføring, ofte bør revideres for at sikre, at der opstod ingen brud på sikkerheden. Tilladelserne kan sendes som en separat fjernmåling stream til sky service hvor den kan analyseres.
- **Fysisk beskyttelse af IoT-infrastruktur**: de værste angreb mod IoT infrastruktur er startet ved hjælp af fysisk adgang til enheder. Der er et vigtigt praksis til beskyttelse mod skadelig brug af USB-porte og andre fysisk adgang. Én nøgle til uncovering overtrædelser, der kan være opstået logger af fysisk adgang, f.eks USB-port. Igen, kan Windows 10 (IoT og andre lagervarer) detaljeret logføring af disse hændelser.
- **Beskyt sky legitimationsoplysninger**: Cloud-legitimationsoplysninger, der bruges til konfiguration og drift af en installation af IoT er muligvis den nemmeste måde at få adgang og svække et system af IoT. Beskytte legitimationsoplysninger ved at ændre adgangskoden ofte, og undlade at bruge disse legitimationsoplysninger på offentlige computere.

Funktioner for forskellige enheder, IoT varierer meget. Nogle enheder muligvis computere, der kører almindelige operativsystemer til stationære computere og nogle enheder kører muligvis meget lav vægt operativsystemer. Sikkerhed bedste fremgangsmåder beskrives kan tidligere gælde for disse enheder i varierende grader. Hvis angivet, bør flere sikkerheds- og bedste fremgangsmåder fra producenterne af disse enheder følges.

Nogle ældre og begrænsede enheder kan ikke have er udviklet specielt til implementering af IoT. Disse enheder muligvis ikke har mulighed for at kryptere data, oprette forbindelse til internettet eller tilbyde avanceret overvågning. I disse tilfælde kan en moderne og sikker gateway aggregere data fra ældre enheder og give den sikkerhed, der kræves til at forbinde disse enheder via internettet. Feltet gateways kan levere sikker godkendelse, forhandling af krypterede sessioner, modtagelse af kommandoer fra skyen og mange andre sikkerhedsfunktioner.
