<properties
    pageTitle="Konfigurere trafik Manager routing metoder | Microsoft Azure"
    description="I denne artikel beskriver, hvordan du konfigurerer routing forskelligt i trafik Manager"
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
   ms.date="10/18/2016"
   ms.author="sewhee" />
<!-- repub for nofollow -->

# <a name="configure-traffic-manager-routing-methods"></a>Konfigurere trafik Manager routing metoder

Azure trafik Manager indeholder tre routing metoder, der bestemmer, hvordan trafik dirigeres til tilgængelig tjeneste slutpunkter. Metoden trafik-routing er anvendt på hver DNS-forespørgsel, der er modtaget for at afgøre, hvilke slutpunkt skal returneres i svaret DNS.

Der er tilgængelige i trafik Manager tre trafik routing metoder:

- **Prioritet:** Vælg 'Priority', når du vil bruge et primære tjenesteslutpunkt og give sikkerhedskopier i tilfælde af primært er ikke tilgængelig.
- **Vægtet:** Vælg 'vægtet' når du vil distribuere trafik på tværs af et sæt af slutpunkter, enten jævnt eller efter tykkelser, som du kan definere.
- **Ydeevne:** Vælg 'Ydeevne', når du har slutpunkter i forskellige geografiske steder, og du vil slutbrugere mulighed for at bruge "nærmeste" slutpunktet med hensyn til de laveste netværksventetid.

## <a name="configure-priority-routing-method"></a>Konfigurere prioritet routing metode

Uanset tilstanden websted indeholder Azure websteder allerede failover funktioner til websteder i et datacenter (også kendt som et område). Trafik Manager indeholder failover til websteder i forskellige datacentre.

Et almindeligt mønster for tjenesten failover er at sende trafik til en primær tjeneste og angive et sæt af identiske sikkerhedskopiering services for failover. Følgende fremgangsmåde beskriver, hvordan du konfigurerer denne prioriterede failover med Azure skytjenester og websteder:

1. Klik på ikonet **Trafik Manager** for at åbne ruden trafik Manager i Azure klassisk portalen, i venstre rude.
2. Find den trafik Manager-profil, der indeholder de indstillinger, du vil ændre, i ruden trafik Manager i portalen Azure klassisk. For at åbne indstillingssiden skal du klikke på pilen til højre for profilnavnet.
3. På din profilside skal du klikke på **slutpunkter** øverst på siden. Kontrollér, at både skytjenester og websteder, du vil medtage i din konfiguration er til stede.
4. Klik på **Konfigurer** øverst for at åbne konfigurationssiden.
5. For **trafik routing metode indstillinger**skal du kontrollere at dirigere trafik metoden er **Failover**. Hvis det ikke er, skal du klikke på **Failover** på rullelisten.
6. Justere failover rækkefølge for slutpunkterne **Failover prioriterede liste**. Når du vælger metoden **Failover** trafik routing, har rækkefølgen af de markerede slutpunkter betydning. Det primære slutpunkt vises øverst. Brug pil op og pil ned for at ændre rækkefølgen, efter behov. Du kan finde oplysninger om, hvordan du angive failover prioritet ved hjælp af Windows PowerShell [Sæt AzureTrafficManagerProfile](http://go.microsoft.com/fwlink/p/?LinkId=400880).
7. Kontrollér, at de **Indstillinger for overvågning** er konfigureret korrekt. Overvågning sikrer, at slutpunkter, som er offline ikke sendes trafik. Hvis du vil overvåge slutpunkter, skal du angive en sti og et filnavn. A skråstreg "/" er en gyldig værdi for den relative sti og indebærer, at filen er i rodmappe (standard).
8. Når du har fuldført dine ændringer i konfigurationen, skal du klikke på **Gem** nederst på siden.
9. Du kan teste ændringerne i konfigurationen.
10. Når profilen trafik Manager fungerer, skal du redigere DNS-post på din autoritative DNS-server til at pege virksomhedens domænenavn på domænenavnet trafik Manager.

## <a name="configure-weighted-routing-method"></a>Konfigurere vægtet routing metode

Et almindelige trafikmønster routing metode er at give et sæt af identiske sidste, som omfatter skytjenester og websteder, og sende trafik til hvert navn i en round robin-måde. Følgende trin skitserer, hvordan du konfigurerer denne type trafik routing metode.

>[AZURE.NOTE] Azure websteder indeholder allerede round robin-justering af belastning funktionalitet til websteder i et datacenter (også kendt som et område). Trafik Manager kan du angive round robin-trafik routing metode til websteder i forskellige datacentre.

1. Klik på ikonet **Trafik Manager** for at åbne ruden trafik Manager i Azure klassisk portalen, i venstre rude.
2. Find den trafik Manager-profil, der indeholder de indstillinger, du vil ændre, i ruden trafik Manager. For at åbne indstillingssiden skal du klikke på pilen til højre for profilnavnet.
3. Klik på **slutpunkter** øverst på siden, og Kontroller, at de service slutpunkter, du vil medtage i din konfiguration findes på siden for din profil.
4. Klik på din profilside, **Konfigurer** øverst for at åbne konfigurationssiden.
5. For **trafik routing metode indstillinger**, du kontrollere, at dirigere trafik metoden er **Round Robin**. Hvis det ikke er, skal du klikke på **Round Robin** på rullelisten.
6. Kontrollér, at de **Indstillinger for overvågning** er konfigureret korrekt. Overvågning sikrer, at slutpunkter, som er offline ikke sendes trafik. Hvis du vil overvåge slutpunkter, skal du angive en sti og et filnavn. A skråstreg "/" er en gyldig værdi for den relative sti og indebærer, at filen er i rodmappe (standard).
7. Når du har fuldført dine ændringer i konfigurationen, skal du klikke på **Gem** nederst på siden.
8. Du kan teste ændringerne i konfigurationen.
9. Når profilen trafik Manager fungerer, skal du redigere DNS-post på din autoritative DNS-server til at pege virksomhedens domænenavn på domænenavnet trafik Manager.

## <a name="configure-performance-traffic-routing-method"></a>Konfigurere ydeevne trafik routing metode

Metoden ydeevne trafik routing gør det muligt at dirigere trafik til slutpunktet med den laveste forsinkelse fra kundens netværk. Datacenteret med den laveste ventetid er typisk tættest i geografiske afstand. Denne trafik routing metode kan ikke højde for realtid ændringer i netværkskonfiguration eller indlæse.

1. Klik på ikonet **Trafik Manager** for at åbne ruden trafik Manager i Azure klassisk portalen, i venstre rude.
2. Find den trafik Manager-profil, der indeholder de indstillinger, du vil ændre, i ruden trafik Manager. For at åbne indstillingssiden skal du klikke på pilen til højre for profilnavnet.
3. Klik på **slutpunkter** øverst på siden, og Kontroller, at de service slutpunkter, du vil medtage i din konfiguration findes på siden for din profil.
4. Klik på **Konfigurer** øverst for at åbne konfigurationssiden på siden for din profil.
5. For **trafik routing metode indstillinger**, du kontrollere, at dirigere trafik metoden er * *ydeevne*. Hvis det ikke er, skal du klikke på * *ydeevne** på rullelisten.
6. Kontrollér, at de **Indstillinger for overvågning** er konfigureret korrekt. Overvågning sikrer, at slutpunkter, som er offline ikke sendes trafik. Hvis du vil overvåge slutpunkter, skal du angive en sti og et filnavn. A skråstreg "/" er en gyldig værdi for den relative sti og forudsætter, at filen er i rodmappe (standard).
7. Når du har fuldført dine ændringer i konfigurationen, skal du klikke på **Gem** nederst på siden.
8. Du kan teste ændringerne i konfigurationen.
9. Når profilen trafik Manager fungerer, skal du redigere DNS-post på din autoritative DNS-server til at pege virksomhedens domænenavn på domænenavnet trafik Manager.

## <a name="next-steps"></a>Næste trin

* [Administrere trafik Manager profiler](traffic-manager-manage-profiles.md)
* [Trafik Manager routing metoder](traffic-manager-routing-methods.md)
* [Test trafik Manager indstillingerne](traffic-manager-testing-settings.md)
* [Peg et trafik Manager domæne et firmadomæne Internet](traffic-manager-point-internet-domain.md)
* [Administrere trafik Manager slutpunkter](traffic-manager-manage-endpoints.md)
* [Fejlfinding i forbindelse med trafik Manager nedsat tilstand](traffic-manager-troubleshooting-degraded.md)
