<properties
    pageTitle="Indlejret trafik Manager profiler | Microsoft Azure"
    description="I denne artikel forklares funktionen 'Indlejret profiler' af Azure trafik Manager"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="nested-traffic-manager-profiles"></a>Indlejrede trafik Manager profiler

Trafik Manager indeholder en række trafik-routing metoder, så du kan styre, hvordan trafik Manager vælger hvilke slutpunkt skal modtage trafik fra hver slutbrugerens. Du kan finde yderligere oplysninger finder [trafik Manager trafik-routing metoder](traffic-manager-routing-methods.md).

Hver trafik Manager profil angiver en enkelt metode trafik-routing. Der er dog scenarier, der kræver mere avancerede trafik routing end ruten leveres af en enkelt trafik Manager profil. Du kan indlejre trafik Manager profiler for at kombinere fordelene ved mere end én trafik-routing metode. Indlejrede profiler giver dig mulighed at tilsidesætte trafik Manager standardfunktionsmåden til understøttelse af større og mere komplekse installationer som programmet.

Følgende eksempler viser, hvordan du bruger indlejrede trafik Manager profiler i forskellige scenarier.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Eksempel 1: Kombinere 'Ydeevne' og 'Vægtet' trafik routing

Antag, at du har installeret et program i følgende Azure områder: vest USA, vest Europe og Østasien. Du kan bruge trafik chefens 'Ydeevne' trafik-routing metode til at distribuere trafik til det område, der er tættest på brugeren.

![Enkelt trafik Manager profil][1]

Antag nu, du vil teste en opdatering til tjenesten før rullende mere alment. Du vil bruge metoden 'vægtet' trafik-routing til at dirigere en lille procentdel af trafik til test installationen. Du kan konfigurere test installationen sammen med den eksisterende produktionsmiljø i Vest Europa.

Du kan kombinere begge 'vægtet' og ' ydeevne trafik-routing i en enkelt profil. For at understøtte dette scenarie skal oprette du en trafik Manager-profil ved hjælp af de to Vest Europe slutpunkter og metoden trafik-routing 'Vægtet'. Derefter skal du tilføje denne 'underordnet' profil som et slutpunkt til profilen 'overordnet'. Den overordnede profil stadig bruger metoden trafik-routing ydeevne og indeholder de andre globale installationer som slutpunkter.

I følgende diagram vises i dette eksempel:

![Indlejrede trafik Manager profiler][2]

I denne konfiguration distribuerer trafik via den overordnede profil trafik på tværs af områder normalt. I Vest Europe distribuerer den indlejrede profil-trafik til fremstilling og test slutpunkterne efter de vægt, der er tildelt.

Når den overordnede profil benytter metoden 'Ydeevne' trafik-routing, skal hvert slutpunkt tildeles en placering. Er tildelt placeringen, når du konfigurerer slutpunktet. Vælg det Azure område, der er tættest på din installation. Azure områder er de placering værdier, der understøttes i Internet ventetid tabellen. Finde flere oplysninger, [trafik Manager 'Ydeevne' trafik-routing metode](traffic-manager-routing-methods.md#performance-traffic-routing-method).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Eksempel 2: Slutpunkt overvågning i indlejrede profiler

Trafik Manager overvåger aktivt tilstanden for hver tjenesteslutpunkt. Hvis et slutpunkt er beskadiget, dirigerer trafik Manager brugere at alternative slutpunkter for at bevare tilgængeligheden af din tjeneste. Denne funktionsmåde for slutpunkter overvågning og failover gælder for alle trafik-routing metoder. Du kan finde yderligere oplysninger finder [Trafik Manager slutpunkt overvågnings](traffic-manager-monitoring.md). Slutpunkt overvågning fungerer anderledes for indlejrede profiler. Med indlejrede profiler udføre overordnede profilen ikke tilstandskontrol på underordnede direkte. I stedet bruges tilstanden for underordnede profilens slutpunkter til at beregne den overordnede status for den underordnede profil. I denne tilstand oplysninger overføres op hierarkiet indlejrede profil. Den overordnede profil dette sammenlagt tilstand til at afgøre, om du vil direkte trafik til den underordnede profil. Se afsnittet [ofte stillede spørgsmål](#faq) i denne artikel for at få flere detaljer om sundhedsovervågning af indlejrede profiler.

Vende tilbage til det forrige eksempel, Antag produktionsmiljø i Vest Europa mislykkes. Som standard omdirigerer profilen 'underordnet' al trafik til test installationen. Hvis test installationen mislykkes, også skal bestemmer den overordnede profil, underordnede profilen ikke skal modtage trafik, fordi alle underordnede slutpunkter er beskadiget. Derefter distribuerer den overordnede profil trafik til de andre områder.

![Indlejrede profil failover (standard)][3]

Du kan muligvis tilfreds med denne aftale. Eller du kan blive bekymret, at al trafik for Vest Europe nu vil test installationen i stedet for en begrænset undersæt trafik. Uanset tilstanden for test-installation vil du skifte over til de andre områder, når produktionsmiljø i Vest Europa mislykkes. Hvis du vil aktivere denne failover, kan du angive parameteren 'MinChildEndpoints', når du konfigurerer profilen underordnede som et slutpunkt i den overordnede profil. Parameteren bestemmer det mindste antal tilgængelige slutpunkter i den underordnede profil. Standardværdien er '1'. I dette scenarie skal angive du værdien MinChildEndpoints til 2. Under denne grænse, den overordnede profil finder profilen hele det underordnede skal være tilgængelig og dirigerer trafik til de andre slutpunkter.

I følgende figur illustrerer denne konfiguration:

![Indlejret profil failover med 'MinChildEndpoints' = 2][4]

>[AZURE.NOTE]
>Metoden 'Priority' trafik-routing distribuerer al trafik til et enkelt slutpunkt. Dermed er der lille formål i en MinChildEndpoints indstilling end '1' efter profilen, underordnede.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Eksempel 3: Prioriteret failover områder i 'Ydeevne' trafik rute

Standardfunktionsmåden for metoden 'Ydeevne' trafik-routing er udviklet til at undgå uberettiget indlæsning af næste nærmeste slutpunkt og medfører en overlappende serie antal mislykkede forsøg. Når et slutpunkt mislykkes, er al trafik, der ville blevet ført til pågældende slutpunkt jævnt fordelt til andre slutpunkterne på tværs af alle områder.

!['Ydeevne' trafik routing med standard failover][5]

Antag, du vil have Vest Europe trafik sekundære Vest i USA, og kun at dirigere trafik til andre områder, når begge slutpunkter er ikke tilgængelig. Du kan oprette denne løsning ved hjælp af en underordnet profil med metoden trafik-routing 'Priority'.

!['Ydeevne' trafik routing med privilegerede failover][6]

Da Vest Europe slutpunktet har højere prioritet end Vest USA slutpunktet, sendes al trafik til vest Europe slutpunktet, når begge slutpunkter er online. Hvis Vest Europe mislykkes, dirigeres trafikken Vest i USA. Med den indlejrede profil ført trafik til østasiatisk kun, når både Vest Europa og Vest USA mislykkes.

Du kan gentage dette mønster for alle områder. Erstat alle tre slutpunkter i den overordnede profil med tre underordnede profiler, hver med en prioriteret failover sekvens.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Eksempel 4: Styre 'Ydeevne' trafik routing mellem flere slutpunkter i samme område

Antag, at den ydeevne, der er trafik-routing metode bruges i en profil, der har mere end et slutpunkt i et bestemt område. Som standard er trafik til området der er jævnt fordelt på tværs af alle tilgængelige slutpunkter i det pågældende område.

!['Ydeevne' trafik routing i område trafik fordeling (standard)][7]

I stedet for at tilføje flere slutpunkter i Vest Europa, er disse slutpunkter indgår i en separat underordnede profil. Underordnet profilen føjes til overordnet som det eneste slutpunkt i Vest Europa. Indstillingerne i den underordnede profil kan styre trafik fordeling med Vest Europe ved at aktivere prioriteret eller vægtet trafik routing i dette område.

!['Ydeevne' trafik routing til brugerdefineret i område trafik fordeling.][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Eksempel 5: Per slutpunkt overvågning indstillinger

Antag, at du bruger trafik Manager til at overføre problemfrit trafik fra en gammel lokale websted til en ny skybaseret version Azure som vært. Du vil bruge hjemmeside URI til at overvåge tilstand for ældre webstedet. Men til den nye skybaseret version, du implementerer en brugerdefineret overvågnings side (path ' / monitor.aspx'), der omfatter yderligere kontroller.

![Trafik Manager slutpunkt overvågning (standard)][9]

Indstillingerne for overvågning i en trafik Manager profil gælder for alle slutpunkter inden for en enkelt profil. Med indlejrede profiler bruger du en anden underordnede profil per websted til at definere forskellige indstillinger for overvågning.

![Trafik Manager slutpunkt overvågning med per slutpunkt indstillinger][10]

## <a name="faq"></a>OFTE STILLEDE SPØRGSMÅL

### <a name="how-do-i-configure-nested-profiles"></a>Hvordan kan jeg konfigurere indlejrede profiler?

Indlejrede trafik Manager profiler kan konfigureres ved hjælp af både ressourcestyring Azure og de klassisk Azure REST API'er, Azure PowerShell-cmdletter og på tværs af platforme Azure CLI-kommandoer. De er også understøttet via den nye Azure portal. De understøttes ikke i portalen klassisk.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Hvor mange lag af indlejre bruges trafik Manager understøtter?

Du kan indlejre profiler op til 10 niveauer. 'Løkker' er ikke tilladt.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Er det muligt at blande andre typer slutpunkt med indlejret underordnet profiler, i den samme trafik Manager-profil?

Ja. Der er ingen begrænsninger for, hvordan du kombinere slutpunkterne for forskellige typer i en profil.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Hvordan gælder fakturering modellen for indlejrede profiler?

Der er ikke negative priser virkningen af at bruge indlejrede profiler.

Trafik Manager fakturering har to komponenter: slutpunkt tilstandskontrol og millioner af DNS-forespørgsler

- Slutpunkt tilstandskontrol: der er gratis for en underordnet profil, når konfigureret som et slutpunkt i en overordnet profil. Overvågning af slutpunkterne i den underordnede profil faktureres i den sædvanlige måde.
- DNS-forespørgsler: hver forespørgsel tælles kun én gang. En forespørgsel til en overordnet profil, der returnerer et slutpunkt fra en underordnet profil tælles mod den overordnede profil.

Du kan finde detaljerede oplysninger, [priser trafik Manager-siden](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Er der en indflydelse på ydeevnen for indlejrede profiler?

Nej. Der er ingen indflydelse på ydeevnen påløbet, når du bruger indlejrede profiler.

Navneservere trafik Manager gennemgang internt hierarkiet profil, når behandling af hver DNS-forespørgsel. En DNS-forespørgsel til en overordnet profil kan modtage en DNS-svar til et slutpunkt fra en underordnet profil. En enkelt CNAME-post bruges, om du bruger en enkelt profil eller indlejret profiler. Der er ingen grund til at oprette en CNAME-post for hver profil i hierarkiet.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Hvordan beregne trafik Manager tilstanden for et indlejret slutpunkt i en overordnet profil?

Den overordnede profil udføre ikke tilstandskontrol på underordnede direkte. I stedet tilstanden for underordnede profilens slutpunkter bruges til at beregne den overordnede status for den underordnede profil. Disse oplysninger er blevet overført op hierarkiet indlejrede profil for at finde ud af tilstanden for det indlejrede slutpunkt. Den overordnede profil anvender denne aggregeret tilstand til at bestemme, om trafikken kan omdirigeres til underordnede.

Den følgende tabel beskrives funktionen af trafik Manager sundhed kontrollerer, om et indlejret slutpunkt.

|Underordnet profil skærm status|Overordnede slutpunkt skærm status|Noter|
|---|---|---|
|Deaktiveret. Underordnet profil er blevet deaktiveret.|Ikke længere|Tilstanden overordnede slutpunkt stoppes, ikke deaktiveret. Tilstanden deaktiveret er reserveret til angiver, at du har deaktiveret slutpunkt i den overordnede profil.|
|Nedsat. Mindst én underordnet profil slutpunkt er i tilstanden Degraded (forringet).| Online: antallet Online slutpunkter i den underordnede profil er mindst værdien af MinChildEndpoints.<BR>CheckingEndpoint: antallet Online plus CheckingEndpoint slutpunkter i den underordnede profil er mindst værdien af MinChildEndpoints.<BR>Nedsat: ellers.|Trafik dirigeres til status CheckingEndpoint slutpunkt. Hvis MinChildEndpoints er sat for højt, nedsat slutpunktet altid.|
|Online. Mindst én underordnet profil slutpunkt er onlinetilstand. Ingen slutpunkt er i tilstanden Degraded (forringet).|Se herover.||
|CheckingEndpoints. Mindst én underordnet profil slutpunkt er 'CheckingEndpoint'. Nogen slutpunkter er 'Online' eller 'Nedsat'|Det samme som ovenfor.||
|Inaktiv. Alle underordnede profil slutpunkter er deaktiveret eller stoppet eller denne profil har nogen slutpunkter.|Ikke længere||


## <a name="next-steps"></a>Næste trin

Få mere at vide mere om, [hvordan trafik Manager fungerer](traffic-manager-how-traffic-manager-works.md)

Lær, hvordan du [opretter en trafik Manager-profil](traffic-manager-manage-profiles.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png

