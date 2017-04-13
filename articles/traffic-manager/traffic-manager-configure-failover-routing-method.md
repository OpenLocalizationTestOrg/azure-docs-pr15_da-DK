<properties
   pageTitle="Konfigurere trafik Manager failover trafik routing metode | Microsoft Azure"
   description="Denne artikel kan hjælpe dig med at konfigurere failover trafik routing metode i trafik Manager"
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="sewhee" />
<!-- repub for nofollow -->

# <a name="configure-failover-routing-method"></a>Konfigurere Failover routing metode

Ofte vil en organisation at levere pålidelighed for dens tjenester. Det gør dette ved at indsende sikkerhedskopiering services, i tilfælde af deres primære tjeneste går ned. Et almindeligt mønster for tjenesten failover er at give et sæt af identiske tjenester og sende trafik til en primær tjeneste, og samtidig bevare en konfigurerede liste over en eller flere ekstra tjenester. Du kan konfigurere denne type sikkerhedskopiering med Azure skytjenester og websteder ved at følge nedenstående fremgangsmåde.

Bemærk, at Azure websteder allerede indeholder failover trafik routing metode funktionalitet til websteder i et datacenter (også kendt som et område), uanset tilstanden websted. Trafik Manager kan du angive failover trafik routing metode til websteder i forskellige datacentre.

## <a name="to-configure-failover-traffic-routing-method"></a>Sådan konfigureres failover trafik routing metode:

1. Klik på ikonet **Trafik Manager** for at åbne ruden trafik Manager i Azure klassisk portalen, i venstre rude. Hvis du endnu ikke har oprettet din trafik Manager profil, kan du se [Administrere trafik Manager profiler](traffic-manager-manage-profiles.md) for trin til at oprette en grundlæggende trafik Manager profil.
2. Find den trafik Manager-profil, der indeholder de indstillinger, du vil ændre i ruden trafik Manager i portalen Azure klassisk, og klik derefter på pilen til højre for profilnavnet. Derved åbnes indstillingssiden for profilen.
3. På din profilside, klik på **slutpunkter** øverst på siden, og Kontrollér, at den begge cloud services og websteder (slutpunkter), du vil medtage i din konfiguration er til stede. Du kan finde trin til at tilføje eller fjerne slutpunkter, [Administrere slutpunkter i trafik Manager](traffic-manager-endpoints.md).
4. Klik på din profilside, **Konfigurer** øverst for at åbne konfigurationssiden.
5. For **trafik routing metode indstillinger**skal du kontrollere at dirigere trafik metoden er **Failover**. Hvis det ikke er, skal du klikke på **Failover** på rullelisten.
6. Justere failover rækkefølge for slutpunkterne **Failover prioriterede liste**. Når du vælger metoden **Failover** trafik routing, har rækkefølgen af de markerede slutpunkter betydning. Det primære slutpunkt vises øverst. Brug pil op og pil ned for at ændre rækkefølgen, efter behov. Du kan finde oplysninger om, hvordan du angive failover prioritet ved hjælp af Windows PowerShell [Sæt AzureTrafficManagerProfile](http://go.microsoft.com/fwlink/p/?LinkId=400880).
7. Kontrollér, at de **Indstillinger for overvågning** er konfigureret korrekt. Overvågning sikrer, at slutpunkter, som er offline ikke sendes trafik. For at overvåge slutpunkter, skal du angive en sti og et filnavn. Bemærk, at en skråstreg "/" er en gyldig værdi for den relative sti og indebærer, at filen er i rodmappe (standard). Du kan finde flere oplysninger om overvågning [Trafik Manager overvågnings](traffic-manager-monitoring.md).
8. Når du har fuldført dine ændringer i konfigurationen, skal du klikke på **Gem** nederst på siden.
9. Du kan teste ændringerne i konfigurationen. Du kan få flere oplysninger i [Test trafik Manager indstillinger](traffic-manager-testing-settings.md) .
10. Når profilen trafik Manager er konfiguration og arbejde, skal du redigere DNS-post på din autoritative DNS-server til at pege virksomhedens domænenavn på domænenavnet trafik Manager. Du kan finde flere oplysninger om, hvordan du gør dette [punkt et firmadomæne Internet til et trafik Manager domæne](traffic-manager-point-internet-domain.md).

## <a name="next-steps"></a>Næste trin

[Peg et trafik Manager domæne et firmadomæne Internet](traffic-manager-point-internet-domain.md)

[Trafik Manager routing metoder](traffic-manager-routing-methods.md)

[Konfigurere round robin routing metode](traffic-manager-configure-round-robin-routing-method.md)

[Konfigurere ydeevne routing metode](traffic-manager-configure-performance-routing-method.md)

[Fejlfinding i forbindelse med trafik Manager nedsat tilstand](traffic-manager-troubleshooting-degraded.md)

[Trafik Manager - Deaktiver, aktiver eller slette en profil](disable-enable-or-delete-a-profile.md)

[Trafik Manager - deaktivere eller aktivere et slutpunkt](disable-or-enable-an-endpoint.md)

