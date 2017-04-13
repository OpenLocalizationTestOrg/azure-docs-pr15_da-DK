<properties
   pageTitle="Konfigurere trafik Manager round robin trafik routing metode | Microsoft Azure"
   description="Denne artikel kan hjælpe dig med at konfigurere round robin justering af belastning for trafik Manager slutpunkterne."
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

# <a name="configure-round-robin-routing-method"></a>Konfigurer Round Robin routing metode

Et almindelige trafikmønster routing metode er at give et sæt af identiske sidste, som omfatter skytjenester og websteder, og sende trafik til hvert navn i en round robin-måde. Nedenstående trin skitserer, hvordan du konfigurerer trafik Manager for at udføre denne type trafik routing metode. Du kan finde flere oplysninger om de forskellige trafik routing metoder, [om trafik Manager trafik routing metoder](traffic-manager-routing-methods.md).

>[AZURE.NOTE] Azure websteder indeholder allerede round robin-justering af belastning funktionalitet til websteder i et datacenter (også kendt som et område). Trafik Manager kan du angive round robin-trafik routing metode til websteder i forskellige datacentre.

## <a name="routing-traffic-equally-round-robin-across-a-set-of-endpoints"></a>Sende trafik lige (round robin) på tværs af et sæt af slutpunkter:

1. Klik på ikonet **Trafik Manager** for at åbne ruden trafik Manager i Azure klassisk portalen, i venstre rude. Hvis du endnu ikke har oprettet din trafik Manager profil, kan du se [Administrere trafik Manager profiler](traffic-manager-manage-profiles.md) for trin til at oprette en grundlæggende trafik Manager profil.
2. Find den trafik Manager-profil, der indeholder de indstillinger, du vil ændre i Azure klassisk portalen, i ruden trafik Manager, og klik derefter på pilen til højre for profilnavnet. Derved åbnes indstillingssiden for profilen.
3. Klik på **slutpunkter** øverst på siden, og Kontroller, at de service slutpunkter, du vil medtage i din konfiguration findes på siden for din profil. Du kan finde trin til at tilføje eller fjerne slutpunkter, [Administrere slutpunkter i trafik Manager](traffic-manager-endpoints.md).
4. Klik på din profilside, **Konfigurer** øverst for at åbne konfigurationssiden.
5. For **trafik routing metode indstillinger**, du kontrollere, at dirigere trafik metoden er **Round Robin**. Hvis det ikke er, skal du klikke på **Round Robin** på rullelisten.
6. Kontrollér, at de **Indstillinger for overvågning** er konfigureret korrekt. Overvågning sikrer, at slutpunkter, som er offline ikke sendes trafik. For at overvåge slutpunkter, skal du angive en sti og et filnavn. Bemærk, at en skråstreg "/" er en gyldig værdi for den relative sti og indebærer, at filen er i rodmappe (standard). Finde flere oplysninger om overvågning af, [Om trafik Manager overvågning](traffic-manager-monitoring.md).
7. Når du har fuldført dine ændringer i konfigurationen, skal du klikke på **Gem** nederst på siden.
8. Du kan teste ændringerne i konfigurationen. Se [Test trafik Manager indstillinger](traffic-manager-testing-settings.md)kan finde flere oplysninger.
9. Når profilen trafik Manager er konfiguration og arbejde, skal du redigere DNS-post på din autoritative DNS-server til at pege virksomhedens domænenavn på domænenavnet trafik Manager. Du kan finde flere oplysninger om, hvordan du gør dette [punkt et Firmadomæne Internet til et trafik Manager domæne](traffic-manager-point-internet-domain.md).

## <a name="next-steps"></a>Næste trin


[Peg et trafik Manager domæne et firmadomæne Internet](traffic-manager-point-internet-domain.md)

[Trafik Manager routing metoder](traffic-manager-routing-methods.md)

[Konfigurere failover routing metode](traffic-manager-configure-failover-routing-method.md)

[Konfigurere ydeevne routing metode](traffic-manager-configure-performance-routing-method.md)

[Fejlfinding i forbindelse med trafik Manager nedsat tilstand](traffic-manager-troubleshooting-degraded.md)

[Trafik Manager - Deaktiver, aktiver eller slette en profil](disable-enable-or-delete-a-profile.md)

[Trafik Manager - deaktivere eller aktivere et slutpunkt](disable-or-enable-an-endpoint.md)

