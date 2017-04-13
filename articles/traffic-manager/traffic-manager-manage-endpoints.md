<properties
    pageTitle="Administrere slutpunkter i Azure trafik Manager | Microsoft Azure"
    description="I denne artikel kan du tilføje, fjerne, aktivere og deaktivere slutpunkter fra Azure trafik Manager."
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="add-disable-enable-or-delete-endpoints"></a>Tilføje, deaktivere, aktivere eller slette slutpunkter

Funktionen Web Apps i Azure Service App indeholder allerede failover og round robin-trafik routing funktionalitet til websteder i et datacenter, uanset tilstanden websted. Azure trafik Manager kan du angive failover og round robin-trafik routing til websteder og cloud services i forskellige datacentre. Det første trin, der er nødvendige for at give denne funktionalitet er at tilføje skybaseret tjeneste eller websted slutpunktet til trafik Manager.

>[AZURE.NOTE]  I denne artikel forklares, hvordan du bruger klassisk portalen. Azure klassisk portalen understøtter kun oprettelse og tildeling af skytjenester og webapps som slutpunkter. Den nye [Azure portal](https://portal.azure.com) er den foretrukne brugergrænseflade.

Du kan også deaktivere individuelle slutpunkter, som er en del af en trafik Manager profil. Deaktivere et slutpunkt forlader det som en del af profilen, men profilen, der fungerer som om slutpunktet ikke medtages i den. Denne handling er nyttigt til midlertidigt at fjerne et slutpunkt, der er i vedligeholdelsestilstand, eller der genaktiveres. Når slutpunktet er oppe at køre igen, kan det være aktiveret.

>[AZURE.NOTE] Deaktivere et slutpunkt har ingen betydning for med dens installation tilstand i Azure. En sund slutpunkt forbliver op og kunne modtage trafik selvom deaktiveret i trafik Manager. Desuden kan påvirker deaktivere ark i en profil ikke dens status i en anden profil.

## <a name="to-add-a-cloud-service-or-website-endpoint"></a>Tilføje en skybaseret tjeneste eller websted slutpunkt

1. Find den profil, trafik Manager, der indeholder indstillingerne slutpunkt, som du vil ændre, i ruden trafik Manager i portalen Azure klassisk. For at åbne indstillingssiden skal du klikke på pilen til højre for profilnavnet.
2. Øverst på siden skal du klikke på **slutpunkter** for at få vist de slutpunkter, der allerede findes en del af konfigurationen.
3. Klik på **Tilføj** for at få adgang til siden **Tilføj Service slutpunkter** nederst på siden. Siden viser skytjenester under **Service slutpunkter**som standard.
4. Vælg skytjenesterne til skytjenester, på listen for at tilføje dem som slutpunkter for denne profil. Rydde navnet på tjenesten skyen, fjernes den fra listen over slutpunkter.
5. Klik på rullelisten **Tjenestetype** for websteder, og vælg derefter **Online**.
6. Vælg webstederne på listen for at tilføje dem som slutpunkter for denne profil. Rydde webstedets navn, fjerner den fra listen over slutpunkter. Du kan vælge kun én websted per Azure datacenter (også kendt som et område). Når du markerer det første websted, bliver de andre websteder i det samme datacenter utilgængelige for markerede. Bemærk også, at kun Standard websteder er angivet.
7. Når du vælger slutpunkterne for denne profil, skal du klikke på markeringen nederst til højre til at gemme ændringerne.

>[AZURE.NOTE] Når du tilføjer eller fjerner et slutpunkt fra en profil ved hjælp af metoden *Failover* trafik routing, listen failover prioritet kan ikke være sorteret på de ønskede måde. Du kan justere rækkefølgen af listen Failover prioritet på siden konfiguration. Se [konfigurere Failover trafik routing](traffic-manager-configure-failover-routing-method.md)kan finde flere oplysninger.

## <a name="to-disable-an-endpoint"></a>Sådan deaktiverer du et slutpunkt

1. Find den profil, trafik Manager, der indeholder indstillingerne slutpunkt, som du vil ændre, i ruden trafik Manager i portalen Azure klassisk. For at åbne indstillingssiden skal du klikke på pilen til højre for profilnavnet.
2. Øverst på siden skal du klikke på **slutpunkter** for at få vist de slutpunkter, der er inkluderet i din konfiguration.
3. Klik på det slutpunkt, som du vil deaktivere, og klik derefter på **Deaktiver** nederst på siden.
4. Klienter fortsætte med at sende trafik til slutpunktet for varigheden af Time-to-Live (TTL). Du kan ændre TTL på siden konfiguration af trafik Manager profilen.

## <a name="to-enable-an-endpoint"></a>Aktivere et slutpunkt

1. Find den profil, trafik Manager, der indeholder indstillingerne slutpunkt, som du vil ændre, i ruden trafik Manager i portalen Azure klassisk. For at åbne indstillingssiden skal du klikke på pilen til højre for profilnavnet.
2. Øverst på siden skal du klikke på **slutpunkter** for at få vist de slutpunkter, der er inkluderet i din konfiguration.
3. Klik på det slutpunkt, som du vil aktivere, og klik derefter på **Aktivér** nederst på siden.
4. Klienter bliver bedt om at aktiveret slutpunktet, bestemmes af profilen.

## <a name="to-delete-a-cloud-service-or-website-endpoint"></a>Slette en skybaseret tjeneste eller websted slutpunkt

1. Find den profil, trafik Manager, der indeholder indstillingerne slutpunkt, som du vil ændre, i ruden trafik Manager i portalen Azure klassisk. For at åbne indstillingssiden skal du klikke på pilen til højre for profilnavnet.
2. Øverst på siden skal du klikke på **slutpunkter** for at få vist de slutpunkter, der allerede findes en del af konfigurationen.
3. Klik på navnet på det slutpunkt, som du vil slette fra profilen, på siden slutpunkter.
4. Klik på **Slet**nederst på siden.

## <a name="next-steps"></a>Næste trin

* [Administrere trafik Manager profiler](traffic-manager-manage-profiles.md)
* [Konfigurere routing metoder](traffic-manager-configure-routing-method.md)
* [Fejlfinding i forbindelse med trafik Manager nedsat tilstand](traffic-manager-troubleshooting-degraded.md)
* [Overvejelser i forbindelse med trafik Manager ydeevne](traffic-manager-performance-considerations.md)
* [Handlinger på trafik Manager (REST-API Reference)](http://go.microsoft.com/fwlink/p/?LinkID=313584)
