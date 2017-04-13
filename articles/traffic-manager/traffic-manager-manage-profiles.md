<properties
    pageTitle="Administrere Azure trafik Manager profiler | Microsoft Azure"
    description="I denne artikel hjælper dig med at oprette, deaktivere, aktivere, slette og få vist historikken for en Azure trafik Manager profil."
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="manage-an-azure-traffic-manager-profile"></a>Administrere en Azure trafik Manager-profil

Trafik Manager profiler Brug trafik-routing metoder til at styre fordelingen af trafikken til skytjenester eller slutpunkter for websted. I denne artikel beskrives det, hvordan du kan oprette og administrere disse profiler.

## <a name="create-a-traffic-manager-profile-using-quick-create"></a>Oprette en trafik Manager profil ved hjælp af Hurtig oprettelse

Du kan hurtigt oprette en trafik Manager profil ved hjælp af Hurtig oprettelse i portalen Azure klassisk. Hurtig Opret kan du oprette profiler med grundlæggende konfigurationsindstillinger. Men du kan ikke bruge Hurtig oprettelse for indstillinger som sæt af slutpunkter (skytjenester og websteder), failover ordren metoden failover trafik routing eller overvågningsindstillinger. Når du har oprettet din profil, kan du konfigurere disse indstillinger i portalen Azure klassisk. Trafik Manager understøtter op til 200 slutpunkter for hver profil. De fleste brugsscenarier kræver dog kun nogle få af slutpunkter.

### <a name="to-create-a-traffic-manager-profile"></a>Oprette en trafik Manager-profil

1. **Installer din skytjenester og websteder produktionsmiljø.** Du kan finde flere oplysninger om skytjenester, [Skytjenester](http://go.microsoft.com/fwlink/p/?LinkId=314074). Du kan finde flere oplysninger om websteder, [websteder](http://go.microsoft.com/fwlink/p/?LinkId=393327).

2. **Log på portalen Azure klassisk.** Klik på **Ny** på nederst til venstre i portalen, skal du klikke på **netværk Services > trafik Manager**, og klik derefter på **Hurtig oprettelse** for at begynde at konfigurere din profil.
3. **Konfigurere præfikset DNS.** Giv din trafik manager profil en entydig DNS præfiks navn. Du kan angive præfikset for et trafik Manager domænenavn.
4. **Vælg abonnementet.** Vælg det relevante Azure abonnement. Hver profil er knyttet til et enkelt abonnement. Hvis du kun har ét abonnement, vises denne indstilling ikke.
5. **Vælg den dirigere trafik metode.** Vælg metoden trafik routing i **trafik routing politik**. Du kan finde flere oplysninger om trafik routing metoder, [om trafik Manager trafik routing metoder](traffic-manager-routing-methods.md).
6. **Skal du klikke på "Opret" for at oprette profilen**. Når profil konfigurationen er fuldført, kan du finde din profil i ruden trafik Manager i portalen Azure klassisk.
7. **Konfigurere slutpunkter, overvågning og yderligere indstillinger i portalen Azure klassisk.** Grundlæggende indstillinger for konfigurerer brug af Hurtig oprettelse kun. Det er nødvendigt at konfigurere yderligere indstillinger som på listen over slutpunkter og slutpunkt failover rækkefølge.


## <a name="disable-enable-or-delete-a-profile"></a>Deaktivere, aktivere eller slette en profil

Du kan deaktivere en eksisterende profil, så denne trafik Manager ikke refererer anmodninger til konfigurerede slutpunkterne. Når du deaktiverer en trafik Manager profil, profilen, og de oplysninger, der er indeholdt i profilen, stadig intakte, og kan redigeres i grænsefladen trafik Manager.  Henvisninger CV, når du genaktiverer profilen. Når du opretter en trafik Manager profil på portalen Azure klassisk, er det automatisk aktiveret. Hvis du vælger en profil ikke længere er nødvendig, kan du slette den.

### <a name="to-disable-a-profile"></a>Deaktivere en profil

1. Hvis du bruger et brugerdefineret domænenavn, kan du ændre CNAME-posten på din Internet DNS-server, så den ikke længere peger på din trafik Manager profil.
2. Trafik holder op med at blive ført til slutpunkterne gennem profilindstillinger trafik Manager.
3. Vælg den profil, du vil deaktivere. På siden trafik Manager skal du fremhæve profilen ved at klikke på kolonnen ud for profilnavnet. Bemærk, at klikke på navnet på profilen eller på pilen ud for navnet åbnes indstillingssiden for profilen.
4. Når du har valgt profilen, skal du klikke på **Deaktiver** nederst på siden.

### <a name="to-enable-a-profile"></a>Aktivere en profil

1. Vælg den profil, du vil deaktivere. På siden trafik Manager skal du fremhæve profilen ved at klikke på kolonnen ud for profilnavnet. Bemærk, at klikke på navnet på profilen eller på pilen ud for navnet åbnes indstillingssiden for profilen.
2. Når du har valgt profilen, skal du klikke på **Aktivér** nederst på siden.
3. Hvis du bruger et brugerdefineret domænenavn, kan du oprette en ressource CNAME-post på din Internet DNS-server til at pege på domænenavnet for din trafik Manager profil.
4. Trafik er ført til slutpunkterne igen.

### <a name="to-delete-a-profile"></a>Slette en profil

1. Sikre, at ressource DNS-post på din Internet DNS-server ikke længere bruger en CNAME-ressourcepost, der peger på domænenavnet for din trafik Manager profil.
2. Vælg den profil, du vil deaktivere. På siden trafik Manager skal du fremhæve profilen ved at klikke på kolonnen ud for profilnavnet. Bemærk, at klikke på navnet på profilen eller på pilen ud for navnet åbnes indstillingssiden for profilen.
3. Når du har valgt profilen, skal du klikke på **Slet** nederst på siden.

## <a name="view-traffic-manager-profile-change-history"></a>Vis trafik Manager profil ændringsoversigten

Du kan få vist ændringsoversigten for din trafik Manager profil i Azure klassisk portalen i Management Services.

### <a name="to-view-your-traffic-manager-change-history"></a>For at få vist chefen trafik ændringsoversigten

1. Klik på **Management Services**i venstre rude af Azure klassisk portal.
2. Klik på **Handling logge**på siden Management Services.
3. På siden handlingen logge, kan du filtrere for at få vist en ændringsoversigt for din trafik Manager profil. Når du har valgt indstillingerne for filtrering, skal du klikke på markeringen for at få vist resultaterne.

   - Vælg dit abonnement og tidsinterval for at få vist ændringerne for alle profiler, og vælg derefter **Trafik Manager** fra genvejsmenuen **Type** .
   - Skriv navnet på profilen i feltet **Navn** på tjeneste for at filtrere efter profilnavn, eller Vælg den i genvejsmenuen.
   - Vælg rækken med den ændring, du vil have vist for at få vist oplysninger om hver enkelt ændring, og klik derefter på **Detaljer** i bunden af siden. Du kan få vist XML-repræsentationen af objektet API, der blev oprettet eller opdateret som en del af handlingen i vinduet **Oplysninger om handling** .

## <a name="next-steps"></a>Næste trin

- [Tilføje et slutpunkt](traffic-manager-endpoints.md)
- [Konfigurere failover routing metode](traffic-manager-configure-failover-routing-method.md)
- [Konfigurere round robin routing metode](traffic-manager-configure-round-robin-routing-method.md)
- [Konfigurere ydeevne routing metode](traffic-manager-configure-performance-routing-method.md)
- [Peg et trafik Manager domænenavn et firmadomæne Internet](traffic-manager-point-internet-domain.md)
- [Fejlfinding i forbindelse med trafik Manager nedsat tilstand](traffic-manager-troubleshooting-degraded.md)