<properties
    pageTitle="Bedste fremgangsmåder til Azure skærm Autoskalering. | Microsoft Azure"
    description="Få mere at vide principper for at bruge effektivt Autoskalering i Azure skærm."
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="ashwink"/>

# <a name="best-practices-for-azure-monitor-autoscaling"></a>Bedste fremgangsmåder til Azure skærm Autoskalering

I følgende afsnit i dette dokument hjælper dig med at forstå de bedste fremgangsmåder for Autoskalering i Azure. Når du har gennemgået disse oplysninger, skal du være bedre kan du bruge effektivt Autoskalering i infrastrukturen Azure.

## <a name="autoscale-concepts"></a>Autoskalering begreber

- En ressource kan tildeles kun *én* Autoskalering indstilling
- En Autoskalering indstilling kan have et eller flere profiler og hver profil kan have en eller flere Autoskalering regler.
- En Autoskalering indstilling skaleres forekomster vandret, hvilket er *ud* ved at øge forekomster og *i* ved at reducere antallet af forekomster.
 En Autoskalering indstilling har en maksimum, minimum og standardværdien for forekomster.
- Et Autoskalering job læser altid den tilknyttede metrikværdi skalere efter kontrol af, om det har krydses konfigurerede grænsen for skala ud eller skala i. Du kan få vist en liste over målepunkter pågældende Autoskalering kan skalere med på [Azure skærm Autoskalering almindelige målepunkter](insights-autoscale-common-metrics.md).
- Alle tærskler beregnes på en forekomst niveau. For eksempel "skala ved 1 forekomst når Gennemsnitlig CPU > 80%, når forekomst Tæl er 2", betyder skala ud, når den gennemsnitlige CPU på tværs af alle forekomster er større end 80%.
- Du modtage altid beskeder om via mail. Nærmere betegnet kan modtager ejeren, bidragyder og læsere på ressourcen, target mail. Du modtager også altid en *gendannelse* mail, når Autoskalering genopretter fra en fejl og starter fungerer normalt.
- Du kan vælge på for at få en vellykket skala handling besked via mail og webhooks.

## <a name="autoscale-best-practices"></a>Autoskalering bedste fremgangsmåder

Brug følgende anbefalede fremgangsmåder, når du bruger Autoskalering.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Sikre, at de største og mindste værdier er forskellige og har en tilstrækkelig margenen mellem dem.
Hvis du har en indstilling, der har minimum = 2, maksimale = 2, og aktuelt forekomst antallet er 2, kan opstå, intet skala. Bevar en tilstrækkelig margenen mellem største og mindste forekomst tæller, som er begge inklusive. Autoskalering altid skal skalere mellem disse begrænsninger.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>Manuel skalering nulstilles ved Autoskalering min og max
Hvis du opdaterer manuelt forekomst antallet til en værdi over eller under maksimum, skaleres Autoskalering program automatisk tilbage til minimum (hvis den er under) eller det maksimale antal (hvis ovenfor). For eksempel kan du angive området mellem 3 og 6. Hvis du har en aktiv forekomst, skaleres Autoskalering program til 3 forekomster under dets næste kørsel. På samme måde, det vil skala på 8 forekomster tilbage til 6 på den næste kørsel.  Manuel skalering er meget midlertidig, medmindre du nulstille Autoskalering reglerne.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Brug altid en kombination af skala ud og skalering i regel, som udfører en Forøg eller Formindsk
Hvis du bruger kun én del af kombinationen, er Autoskalering skala på, enkelt ud eller på, indtil maksimum eller minimum, nået.

### <a name="do-not-switch-between-the-azure-portal-and-the-azure-classic-portal-when-managing-autoscale"></a>Undlad at skifte mellem Azure portalen og Azure klassisk portalen under ledelse af Autoskalering
Bruge Azure-portalen (portal.azure.com) til at oprette og administrere indstillinger for Autoskalering for Skytjenester og App-tjenester (online). Brug PoSH, CLI eller REST-API til at oprette og administrere Autoskalering indstilling for virtuelt skala sæt. Undlad at skifte mellem Azure klassisk portalen (manage.windowsazure.com) og portalen Azure (portal.azure.com) under ledelse af Autoskalering konfigurationer. Azure klassisk portalen og dens underliggende back end-har begrænsninger. Flytte til portalen Azure til at administrere Autoskalering ved hjælp af en grafisk brugergrænseflade. Indstillingerne er at bruge Autoskalering PowerShell, CLI eller REST-API (via Azure ressource Explorer).

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Vælg den relevante statistikken for din diagnosticering metrisk
På diagnosticering målepunkter, kan du vælge mellem *gennemsnit*, *Minimum*, *maksimale* og *Samlet* som en metrikværdi skalere ved. De mest almindelige statistikken er *middel*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Vælg tærsklerne omhyggeligt for alle metriske typer
Vi anbefaler at omhyggeligt vælge forskellige tærskler skala ud og skala baseret på praktiske situationer.

Vi *anbefaler ikke* Autoskalering indstillinger Sådan eksemplerne nedenfor med den samme eller minder meget om tærskelværdi for ud af, og i betingelser:

- Øge forekomster af 1 tælle hvornår antal tråde < = 600
- Mindske forekomster med 1 tælle hvornår antal tråde > = 600

Lad os se på et eksempel på, hvad kan føre til funktioner, der kan virke forvirrende. Cosider følgende sekvens.

1. Antag, der er 2 forekomster til at begynde med og derefter det gennemsnitlige antal tråde hver forekomst vokser til 625.
2. Autoskalering skaleres ud af, at tilføje en forekomst af 3.
3. Dernæst skal forudsætter, at den gennemsnitlige tråd optælling på tværs af forekomst falder til 575.
4. Før skalering, bliver Autoskalering forsøg at anslå det endelige tilstand, hvis den skaleret i. For eksempel 575 x 3 (aktuelle forekomst antal) = 1,725 / 2 (endelige antallet af forekomster når skaleret) = 862.5 tråde. Det betyder Autoskalering skulle med det samme skala ud igen selv efter at den skaleret i, hvis den gennemsnitlige tråd antal forbliver uændret eller endda falder kun en smule. Men hvis det skaleret op igen, hele processen vil gentage, fører til en uendelig løkke.
5. For at undgå problemet (kaldes "flapping"), autoskalering ikke indskrænke overhovedet. I stedet, der springer og reevaluates betingelsen igen, næste gang tjenestens job udfører. Dette kan forveksle mange personer, fordi Autoskalering ville ser ud til at arbejde, når den gennemsnitlige tråd optælling blev 575.

Skøn under en skala på er beregnet til at undgå "flappy" situationer. Husk, når du vælger de samme tærskler for skala ud og i, skal du beholde denne funktionsmåde.

Vi anbefaler at vælge en passende margen mellem skala ud og i tærskler. Som et eksempel, bør du overveje følgende bedre regel kombination.

- Øge forekomster af 1 tælle hvornår CPU % > = 80
- Mindske forekomster med 1 tælle hvornår CPU % < = 60

I dette tilfælde  

1. Forudsætter, der er 2 forekomster skal starte med.
2. Hvis den gennemsnitlige CPU % på tværs af forekomster går til 80, skaleres Autoskalering ud af, at tilføje en tredje forekomst.
3. Nu forudsætter, at CPU % over tid falder til 60.
4. Autoskalerings skala i regel beregner den endelige status, hvis det var til skala i. For eksempel 60 x 3 (aktuelle forekomst antal) = 180 / 2 (endelige antallet af forekomster når skaleret) = 90. Så Autoskalering ikke skala på fordi det er nødvendigt at skala igen med det samme ud. I stedet springer den skalering.
5. Den næste gang Autoskalering kontrollerer, CPU'EN fortsætter med at gå til 50. Estimerer igen - 50 x 3 forekomst = 150 / 2 forekomster = 75, som er under skala fra grænsen på 80, så det skaleres korrekt i på forekomster af 2.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Overvejelser i forbindelse med skalering tærskelværdi på speciel målepunkter
 På speciel målepunkter som lagerplads eller Service Bus kø længde metrisk er grænsen det gennemsnitlige antal meddelelser, der er tilgængelige i aktuelle antallet af forekomster. Vælg omhyggeligt Vælg grænseværdi for denne metrikværdi.

Lad os illustrere den med et eksempel for at sikre, at du forstår funktionsmåden bedre.

- Øge forekomster med 1 Tæl, når lagerplads kø meddelelse Tæl > = 50
- Mindske forekomster med 1 Tæl, når lagerplads kø meddelelse Tæl < = 10

Overvej følgende rækkefølge:

1. Der er 2 lagerplads kø forekomster.
2. Holde kommer meddelelser, og når du gennemser lagerplads køen, det samlede antal læser 50. Du kan gå ud fra pågældende Autoskalering skal starte en skala fra handling. Bemærk imidlertid, at det er stadig 50/2 = 25 meddelelser hver forekomst. Så opstår skala fra ikke. Til den første skala ud til at opstå, skal det samlede antal meddelelser i køen lagerplads 100.
3. Dernæst skal forudsætter, at det samlede antal meddelelser når 100.
4. En 3 lagerplads kø forekomst tilføjes på grund af en skala fra handling.  Handlingen næste skala op-der ikke sker, indtil det samlede antal meddelelser i køen når 150, fordi 150/3 = 50.
5. Antallet af beskeder i køen bliver nu mindre. Med 3 forekomster, den første skala i handling sker der, når de samlede meddelelser i alle køer tilføje op til 30, fordi 30/3 = 10 meddelelser hver forekomst, som er i grænseværdi for skala i.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Overvejelser i forbindelse med skalering, når flere profiler er konfigureret i en Autoskalering indstilling

I en Autoskalering indstilling, kan du vælge en standardprofil, der anvendes altid uden en hvilken som helst afhængighed af tidsplan eller et klokkeslæt, eller du kan vælge en tilbagevendende profil eller en profil for et bestemt tidsrum med en dato- og klokkeslætsintervallet.

Når Autoskalering service behandler dem, kontrollerer den altid i følgende rækkefølge:

1. Fast dato-profil
2. Tilbagevendende profil
3. Standardprofil ("altid")

Hvis en profil betingelser er opfyldt, kontrollerer Autoskalering ikke betingelsen næste profil under den. Autoskalering behandler kun én profil ad gangen. Det betyder, at hvis du vil også medtage en behandling betingelse fra en lavere niveau profil, skal du medtage disse regler samt i den aktuelle profil.

Lad os gennemgå dette ved hjælp af et eksempel:

Billedet nedenfor viser en Autoskalering indstilling med en standardprofil af mindste forekomster = 2- og maksimumværdier forekomster = 10. I dette eksempel regler er konfigureret til at skala-ud, når antallet meddelelser i køen er større end 10 skala-in og når antallet meddelelser i køen er mindre end 3. Så nu kan ressourcen skalere mellem 2 og 10 forekomster.

Desuden er der en tilbagevendende profil for mandag. Det er angivet efter mindste forekomster = 2- og maksimumværdier forekomster = 12. Det betyder om mandagen, den første gang Autoskalering kontrollerer, om problemet, hvis forekomst antallet er 2, det skaleres til det nye minimum af 3. Så længe Autoskalering fortsætter med at finde denne profil betingelse matchet (mandag), det kun behandler CPU-baserede skala-ud/i reglerne konfigureret til denne profil. På nuværende tidspunkt kontrollerer det ikke for kø længde. Hvis du vil også betingelsen kø længden kontrolleres, skal du imidlertid medtage disse regler fra standardprofilen samt i din mandag profil.

På samme måde, når Autoskalering skifter tilbage til standardprofilen, skal den kontrollerer først, hvis de mindste og største betingelser er opfyldt. Hvis antallet af forekomster på tidspunktet er 12, skaleres det i til 10, den maksimalt tilladte for standardprofilen.

![Autoskalering indstillinger](./media/insights-autoscale-best-practices/insights-autoscale-best-practices.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Overvejelser i forbindelse med skalering, når flere regler, der er konfigureret i en profil
Der er tilfælde, hvor du kan være nødvendigt at angive flere regler i en profil. Den følgende regelsæt Autoskalering bruges ved brug af tjenesterne, når flere regler er angivet.

Autoskalering kører på *Skaler ud*, hvis en regel er opfyldt.
På *skala i*kræver Autoskalering alle regler, der skal opfyldes.

For at illustrere, forudsætter, at du har følgende 4 Autoskalering regler:

- Hvis CPU < 30%, skala i af 1
- Hvis hukommelse < 50%, skala i af 1
- Hvis CPU > 75%, skala ud af 1
- Hvis hukommelse > 75%, skala ud af 1

Klik derefter opstår på Følg:

- Hvis CPU er 76% og hukommelse er 50% skal vi skala ud.
- Hvis CPU er 50% og hukommelse er 76% vi skala ud.

På den anden side, hvis CPU er 25% og hukommelse er 51% Autoskalering betyder **ikke** skala på. Med henblik på skala i, CPU skal være 29% og hukommelse 49%.

### <a name="always-select-a-safe-default-instance-count"></a>Vælg altid et sikkert standard forekomst antal
Standard forekomst antallet er vigtigt Autoskalering skaleres din tjeneste til, der tæller, når målepunkter ikke er tilgængelige. Derfor vælge en standard forekomst optælling, der er tillid til din arbejdsbelastninger.

### <a name="configure-autoscale-notifications"></a>Konfigurere beskeder til Autoskalering
Autoskalering underretter administratorer og bidragydere ved en ressource via mail, hvis et af følgende betingelser opstår:

- Autoskalering tjeneste til at udføre en handling.
- Målepunkter er ikke tilgængelige for Autoskalering tjeneste til at tage et skala beslutning.
- Målepunkter er tilgængelig (gendannelse) igen for at sikre en skala beslutning.
Foruden betingelserne ovenfor, kan du konfigurere mail eller webhook meddelelser til at få besked til vellykket skala handlinger.
