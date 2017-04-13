<properties
    pageTitle="Test trafik Manager indstillingerne | Microsoft Azure"
    description="I denne artikel kan hjælpe dig test trafik Manager indstillinger"
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

# <a name="test-your-traffic-manager-settings"></a>Test dine trafik Manager-indstillinger

For at teste trafik Manager indstillingerne, skal du have flere klienter, på forskellige placeringer, hvor du kan køre test. Derefter sætter slutpunkterne i din trafik Manager profil ned én ad gangen.

* Angiv DNS TTL-værdien lav, så ændringer overføres hurtigt (for eksempel, 30 sekunder).
* Se IP-adresser for Azure skyen tjenester og websteder i den profil, du tester.
* Bruge værktøjer, som du kan oversætte et DNS-navn til en IP-adresse og vise, som omhandler.

Du tjekker for at se, at DNS-navnene løse til IP-adresserne på slutpunkterne i din profil. Navnene skal løse i overensstemmelse med metoden trafik routing defineret i profilen trafik Manager. Du kan bruge værktøjerne som **nslookup** eller **grave** til at fortolke DNS-navne.

I følgende eksempler hjælpe dig med at teste din trafik Manager profil.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Kontrollere trafik Manager profil ved hjælp af nslookup og ipconfig i Windows

1. Åbn en kommando eller et Windows PowerShell-prompt som administrator.
2. Skriv `ipconfig /flushdns` til Rens DNS resolver cache.
3. Skriv `nslookup <your Traffic Manager domain name>`. For eksempel kontrollerer følgende kommando domænenavn med præfiks *myapp.contoso*

        nslookup myapp.contoso.trafficmanager.net

    Et typisk resultat vises følgende oplysninger:

    * DNS-navn og IP-adressen på DNS-server, der åbnes for at løse dette trafik Manager domænenavn.
    * Trafik Manager domænenavn, du indtastede på kommandolinjen, efter "nslookup" og den IP-adresse, hvor trafikken Manager domænet løser. Den anden IP-adresse er det vigtige, at kontrollere. Det skal svare til en offentlig virtuelle IP-adresse (VIP) adresse for et af skytjenester eller websteder i profilen trafik Manager du tester.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>Hvordan du tester metoden failover trafik routing

1. Du kan lade alle slutpunkter op.
2. Ved hjælp af en enkelt klient, anmode om DNS-opløsning for virksomhedens domænenavn ved hjælp af nslookup eller en lignende utility.
3. Kontrollere, at løst IP-adressen, svarer det primære slutpunkt.
4. Sænkes din primære slutpunkt eller fjerne filen overvågning, så denne trafik Manager mener, at programmet er nede.
5. Vent på DNS-Time-to-Live (TTL) for trafik Manager profilen samt en yderligere to minutter. Eksempelvis hvis din DNS TTL er 300 sekunder (5 minutter), skal du vente syv minutter.
6. Rens din DNS-klient cache og anmodning om DNS-opløsning ved hjælp af nslookup. I Windows, kan du rens din DNS-cache med kommandoen ipconfig/flushdns.
7. Sørg for, løst IP-adressen svarer til din sekundære slutpunkt.
8. Gentag processen, hvilket ned hvert slutpunkt igen. Kontrollér, at DNS returnerer IP-adressen på det næste slutpunkt på listen. Når alle slutpunkter er ned, skal du hente IP-adressen på det primære slutpunkt igen.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>Hvordan du tester metoden vægtet trafik routing

1. Du kan lade alle slutpunkter op.
2. Ved hjælp af en enkelt klient, anmode om DNS-opløsning for virksomhedens domænenavn ved hjælp af nslookup eller en lignende utility.
3. Sørg for, svarer løst IP-adressen til et af slutpunkterne.
4. Rens cachen i din DNS-klienten og gentage trin 2 og 3 for hvert slutpunkt. Du bør se forskellige IP-adresser, der returneres for hver af slutpunkterne.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>Hvordan du tester metoden ydeevne trafik routing

Hvis du vil teste effektivt en ydeevne trafik routing metode, skal du have klienter, der er placeret i forskellige dele af hele verden. Du kan oprette klienter i forskellige Azure områder, der kan bruges til at teste dine tjenester. Hvis du har et globalt netværk, kan du få fjernadgang logge på klienter i andre dele af verden og køre test derfra.

Er du kan også der gratis webbaseret DNS-opslag og grave tjenester, der er tilgængelige. Nogle af disse værktøjer giver dig mulighed for at se DNS-navneoversættelse fra forskellige steder over hele verden. Foretage en søgning på "DNS-opslag" eksempler. Tredjepartstjenester som Gomez eller Keynote kan bruges til at bekræfte, at dine profiler distribuerer trafik som forventet.

## <a name="next-steps"></a>Næste trin

* [Om trafik Manager trafik routing metoder](traffic-manager-routing-methods.md)
* [Overvejelser i forbindelse med trafik Manager ydeevne](traffic-manager-performance-considerations.md)
* [Fejlfinding i forbindelse med trafik Manager nedsat tilstand](traffic-manager-troubleshooting-degraded.md)




