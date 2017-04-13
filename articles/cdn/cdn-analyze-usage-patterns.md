<properties
    pageTitle="Analysere Azure CDN brugsmønstre | Microsoft Azure"
    description="Du kan få vist brugsmønstre for din CDN ved hjælp af følgende rapporter: båndbredde Data overføres søgeresultater, cachen statusser, cachen påløber forholdet, og IPV4/IPV6 Data overføres."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>

# <a name="analyze-azure-cdn-usage-patterns"></a>Analysere Azure CDN brugsmønstre

[AZURE.INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Du kan få vist brugsmønstre for din CDN ved hjælp af følgende rapporter:

- Båndbredde
- Data, der overføres
- Besøg
- Statusser, som cache
- Bredde-forhold-cachen
- IPv4/IPv6-Data, der overføres

## <a name="accessing-advanced-http-reports"></a>Få adgang til avancerede HTTP-rapporter

1. Klik på knappen **Administrer** fra bladet CDN profil.

    ![Knappen Administrer CDN profil blade](./media/cdn-reports/cdn-manage-btn.png)

    Portalen CDN management åbnes.

2. Peg på fanen **analyser** og derefter holde markøren over den **Grundlæggende rapporter** pop op.  Klik på den ønskede rapport i menuen.

    ![LEVERANDØR(er) management portal - menuen Core rapporter](./media/cdn-reports/cdn-core-reports.png)


## <a name="bandwidth"></a>Båndbredde

Rapporten båndbredde består af en graf og data tabel, der angiver båndbredden for HTTP og HTTPS over en bestemt tidsperiode. Du kan få vist båndbredden på tværs af alle CDN vises eller en bestemt POP. Dette giver dig mulighed at se trafik spidser og fordeling på tværs af CDN vises i Mbps.

- Vælg alle kant noder til at se trafik fra alle noder eller vælge en bestemt område/node på rullelisten.
- Vælg datointerval for at få vist data for i dag/denne uge/denne måned, osv., eller Angiv brugerdefinerede datoer og derefter klikke på "OK" for at sikre, at dit valg opdateres.
- Du kan eksportere og hente data ved at klikke på excel-ark ikonet ud for "gå".

Rapporten opdateres hver 5 minutter.

![Båndbredde rapport](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Data, der overføres

Denne rapport består af en graf og data tabel, der angiver trafik brugen for HTTP og HTTPS over en bestemt tidsperiode. Du kan få vist trafik brugen på tværs af alle CDN vises eller en bestemt POP. Dette giver dig mulighed at se trafik spidser og fordeling på tværs af CDN vises i GB.

- Vælg alle kant noder til at se trafik fra alle noter, eller Vælg et bestemt område/knude på rullelisten.
- Vælg datointerval for at få vist data for i dag/denne uge/denne måned, osv., eller Angiv brugerdefinerede datoer og derefter klikke på "OK" for at sikre, at dit valg opdateres.
- Du kan eksportere og hente data ved at klikke på excel-ark ikonet ud for "gå".

Rapporten opdateres hver 5 minutter.

![Data overføres rapport](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Besøg (statuskoder)

Denne rapport beskrives fordelingen af anmodning om status-koder til indholdet. Alle anmodninger om indhold genererer en HTTP-statuskode. Statuskoden beskrives, hvordan kanten popper håndteres anmodningen. For eksempel angiver 2xx status-koder, anmodningen blev korrekt served til en klient, mens en 4xx statuskode angiver, at der opstod en fejl. Du kan finde flere oplysninger om HTTP-statuskode, [status-koder](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

- Vælg datointerval for at få vist data for i dag/denne uge/denne måned, osv., eller Angiv brugerdefinerede datoer og derefter klikke på "OK" for at sikre, at dit valg opdateres.
- Du kan eksportere og hente data ved at klikke på excel-regnearket findes ud for "gå".

![Besøg rapport](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Statusser, som cache

Denne rapport beskrives fordelingen af i cachen og udgivelsescachen for klientanmodning. Da den hurtigste ydeevne stammer fra cachen, kan du optimere data levering hastigheder ved at minimere udgivelsescachen og udløbet i cachen. Udgivelsescachen kan blive reduceret, ved at konfigurere din origin server for at undgå at tildele "ikke-cache" svar sidehoveder, ved at undgå forespørgselsstreng cachelagring undtagen, hvor det er absolut nødvendigt og ved at undgå ikke kan cachelagres Svarkoder. Udløbet cache søgeresultater kan undgås ved at gøre et aktiv er Maks alder så længe som muligt at minimere antallet af anmodninger om til den oprindelige server.

![Cache statusser rapport](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Statusser, som primære cache omfatter:

- TCP_HIT: Served fra kant. Objektet er i cachen og ikke var overskredet dens Maks-alder.
- TCP_MISS: Served fra Origin. Objektet er ikke i cachen og svaret var tilbage til origin.
- TCP_EXPIRED _MISS: Served fra origin efter fornyelse med origin. Objektet er i cachen, men havde overskredet dens Maks-alder. En fornyelse med origin udløste objektet cache er erstattet med et nyt svar fra origin.
- TCP_EXPIRED _HIT: Served fra kant efter fornyelse med origin. Objektet er i cachen, men havde overskredet dens Maks-alder. En fornyelse med den oprindelige server udløste objektet cache der uændrede.

- Vælg datointerval for at få vist data for i dag/denne uge/denne måned, osv., eller Angiv brugerdefinerede datoer og derefter klikke på "OK" for at sikre, at dit valg opdateres.
- Du kan eksportere og hente data ved at klikke på excel-ark ikonet ud for "gå".

### <a name="full-list-of-cache-statuses"></a>Komplet liste over cache statusser

- TCP_HIT – denne status rapporteres, når en anmodning om leveres direkte fra POP til klienten. Et aktiv leveres direkte fra en POP, når den er cachelagret på POP tættest på klienten og den har et gyldigt time to live eller TTL. TTL bestemmes af følgende svar overskrifterne:

    - Cache-styring: s-maxage
    - Cache-styring: Maks-alder
    - Udløber

- TCP_MISS – denne status angiver, at en cachelagret version på den ønskede aktiv ikke blev fundet på POP tættest på klienten. Bliver bedt om aktivet fra en server til origin eller en origin skjold server. Hvis den oprindelige server eller origin skjold serveren returnerer et aktiv, vil det served til klienten og cachelagret på både klienten og edge server. Ellers kan en ikke-200 statuskode (f.eks., 403 forbudt, 404 ikke fundet, osv.) returneres.

- TCP_EXPIRED _HIT – denne status rapporteres, når en anmodning, der rettet et aktiv med et udløbet TTL, som når aktivets Maks-alder er udløbet, blev leveres direkte fra POP til klienten.

    Anmodning om en udløbet typisk resulterer i en anmodning om fornyelse til den oprindelige server. Hvis en TCP_EXPIRED _HIT skal udføres, skal skal den oprindelige server angive, at en nyere version for aktivet ikke findes. Denne type situation opdateres typisk aktivet Cache-Control og udløber sidehoveder.

- TCP_EXPIRED _MISS – denne status rapporteres, når der kommer en nyere version af et udløbet cachelagrede aktiv fra POP til klienten. Dette sker, når TTL på cachelagrede anlægsaktiver er udløbet (f.eks., udløbet Maks alder) og den oprindelige server returnerer en nyere version af aktivet. Denne nye version af aktivet der skal vises til klienten i stedet for den cachelagrede version. Det kan desuden cachelagres på edge server og klienten.

- CONFIG_NOCACHE – denne status angiver, at en kunde-specifikke konfiguration på vores kant POP forhindret aktivet i cachelagres.

- Ingen - denne status angiver, at cachen indhold friskhed Tjek ikke blev udført.

- TCP_ CLIENT_REFRESH _MISS – denne status rapporteres, når en HTTP-klient (f.eks., browser) fremtvinger en kant POP til at hente en ny version af et forældede aktiv fra den oprindelige server.

    Som standard forhindre vores servere en HTTP-klient i at tvinge vores edge-servere at hente en ny version af aktivet fra den oprindelige server.

- TCP_ PARTIAL_HIT – denne status rapporteres, når en byte områdeanmodningen resulterer i en stor succes for en delvist cachelagrede aktiv. Området anmodede byte leveres med det samme fra POP til klienten.

- UNCACHEABLE – denne status rapporteres, når et aktiv Cache-Control og udløber sidehoveder angiver, at det ikke skal cachelagres på en POP eller af HTTP-klienten. Disse typer anmodninger der leveres fra den oprindelige server

## <a name="cache-hit-ratio"></a>Bredde-forhold-cachen

Denne rapport angiver procentdelen af cachelagrede anmodninger, der blev fungeret direkte fra cachen.

Rapporten indeholder følgende oplysninger:

- Det ønskede indhold er cachelagret på tættest på anmoderen POP.
- Anmodningen blev leveres direkte fra kanten af vores netværk.
- Anmodningen ikke kræver fornyelse med den oprindelige server.

Rapporten indeholder ikke:

- Anmodninger, der ikke på grund af land filtreringsindstillinger.
- Anmodninger om aktiver, hvis brevhoveder angiver, at de ikke skal gemmes i cachen. For eksempel Cache-Control: private og Cache-Control: ingen-cache eller Pragma: ingen-cache sidehoveder forhindrer et aktiv i cachelagres.
- Byte område anmodninger om delvist cachelagret indhold.

Formlen er: (TCP_ BESØG / (TCP_ BESØG + TCP_MISS)) * 100

- Vælg datointerval for at få vist data for i dag/denne uge/denne måned, osv., eller Angiv brugerdefinerede datoer og derefter klikke på "OK" for at sikre, at dit valg opdateres.
- Du kan eksportere og hente data ved at klikke på excel-ark ikonet ud for "gå".


![Bredde-forhold rapport-cachen](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>IPv4/IPv6-Data, der overføres

Denne rapport viser trafik brugen fordelingen i IPV4 vs IPV6.

![IPv4/IPv6-Data, der overføres](./media/cdn-reports/cdn-ipv4-ipv6.png)

- Vælg datointerval for at få vist data for i dag/denne uge/denne måned, osv., eller Angiv brugerdefinerede datoer.
- Klik på "OK" for at sikre, at dit valg opdateres.


## <a name="considerations"></a>Overvejelser i forbindelse med

Rapporter kan kun oprettes inden for de sidste 18 måneder.
