<properties
    pageTitle="Aktivere Session forbindelse med Azure-værktøjskassen til Eklipse"
    description="Få mere at vide, hvordan du aktiverer session forbindelse med Azure-værktøjskassen til Eklipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690950.aspx -->

# <a name="enable-session-affinity"></a>Aktivere Session forbindelse #

I Azure-værktøjskassen til Eklipse, kan du aktivere HTTP-session forbindelse eller "sticky sessioner", til dine roller. Følgende billede viser dialogboksen bruges til at aktivere funktionen session forbindelse egenskaberne **Belastning** :

![][ic719492]

## <a name="to-enable-session-affinity-for-your-role"></a>Aktivere session forbindelse til din rolle ##

1. Højreklik på rollen i Eklipses Projektstifinder, skal du klikke på **Azure**, og klik derefter på **Belastning**.
1. I dialogboksen **Egenskaber for WorkerRole1 belastning** :
    1. Kontrollere **Aktiver HTTP session forbindelse (sticky sessioner) for denne rolle.**
    1. Vælg et input slutpunkt skal bruges, for eksempel **http (offentlige: 80, privat: 8080)**for **Input slutpunkt til brug**. Dit program skal bruge dette slutpunkt som dens HTTP slutpunkt. Du kan aktivere flere slutpunkter for din rolle, men du kan vælge kun én af dem til at understøtte sticky sessioner.
    1. Genopbygge dit program.

Når aktiveret, hvis du har mere end én rolle forekomst, fortsat HTTP-anmodninger kommer fra en bestemt klient behandles af den samme rolle forekomst.

Eklipse værktøjerne aktiverer dette ved at installere en speciel IIS-modul kaldet program anmode om Routing (ARR) i hver af din rolle forekomster. ARR omdirigerer HTTP-anmodninger til den relevante rolle forekomst. Værktøjerne omkonfigurerer automatisk det valgte slutpunkt, så den indgående HTTP-trafik først dirigeres til ARR softwaren. Værktøjerne også opretter et nyt interne slutpunkt, som Java serveren er konfigureret til at lytte til. Det er det slutpunkt, der bruges af ARR til at omdirigere HTTP-trafik til den relevante rolle forekomst. Denne måde hver rolle forekomst i flere forekomster installationen fungerer som en omvendt proxy for alle andre forekomster, så sticky sessioner.

## <a name="notes-about-session-affinity"></a>Noter om session forbindelse ##

* Session forbindelse fungerer ikke i Beregn emulatoren. Indstillingerne kan anvendes i Beregn emulatoren uden at forstyrre din build proces eller beregne udførelse af emulator, men selve funktionen fungerer ikke i Beregn emulatoren.
* Aktivere session forbindelse resulterer i et større inden for den diskplads optager af din installation i Azure, som yderligere software skal hentes og installeres i din rolle forekomster, når din tjeneste startes i Azure skyen.
* Tid at starte hver rolle tager længere tid.
* Et internt slutpunkt skal fungere som et trafik rerouter som nævnt ovenfor, vil være added.ss

Et eksempel på, hvordan du bevare sessionsdata, når session forbindelse er aktiveret, se, [hvordan du bevare Session Data med Session forbindelse][].

## <a name="see-also"></a>Se også ##

[Azure-værktøjskassen til Eklipse][]

[Oprette et Hej verden program til Azure i Eklipse][]

[Installation af Azure værktøjerne til Eklipse][] 

[Sådan vedligeholdes sessionsdata med Session forbindelse][]

Du kan finde flere oplysninger om brug af Azure med Java, [Azure Java Developer Center][].

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure-værktøjskassen til Eklipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Oprette et Hej verden program til Azure i Eklipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Sådan vedligeholdes sessionsdata med Session forbindelse]: http://go.microsoft.com/fwlink/?LinkID=699539
[Installation af Azure værktøjerne til Eklipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719492]: ./media/azure-toolkit-for-eclipse-enable-session-affinity/ic719492.png
