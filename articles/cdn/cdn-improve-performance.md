<properties
    pageTitle="Forbedre ydeevnen ved at komprimere filer i Azure CDN | Microsoft Azure"
    description="Lær at forbedre overførselshastigheden fil og øger siden Indlæs ydeevne ved at komprimere dine filer i Azure CDN."
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

# <a name="improve-performance-by-compressing-files"></a>Forbedre ydeevnen ved at komprimere filer

Komprimering er en enkel og effektiv metode til at forbedre overførselshastigheden fil og Forøg siden Indlæs ydeevne ved at reducere filstørrelsen, før den er sendt fra serveren. Den reducerer båndbredde omkostninger og giver en hurtigere brugeroplevelse for dine brugere.

Der er to måder at aktivere komprimering:

- Du kan aktivere komprimering på origin server, hvor store og små bogstaver på CDN skal passerer gennem de komprimerede filer og levere komprimerede filer til kunder, der anmoder om dem.
- Du kan aktivere komprimering direkte på CDN edge-servere, hvor store og små bogstaver på CDN vil komprimere filerne og fungere til slutbrugere, selvom de ikke komprimeres af den oprindelige server.

> [AZURE.IMPORTANT] Ændringer i konfigurationen CDN gå et stykke tid at overføre via netværket.  For <b>Azure CDN fra Akamai</b> profiler fuldfører overførsel normalt i under et minut.  For <b>Azure CDN fra Verizon</b> profiler, kan du som regel se ændringerne anvende inden for 90 minutter.  Hvis dette er første gang, du har konfigureret komprimering for din CDN slutpunkt, bør du overveje, venter på 1-2 timer er vigtigt komprimeringen indstillinger har overføres til popper før du foretager fejlfinding

## <a name="enabling-compression"></a>Aktivere komprimering

> [AZURE.NOTE] Standard- og Premium CDN niveauer Giv samme komprimering funktionalitet, men brugergrænsefladen varierer.  Se [Oversigt over Azure CDN](cdn-overview.md), du kan finde flere oplysninger om forskellene mellem Standard- og Premium CDN niveauer.

### <a name="standard-tier"></a>Standard niveau

> [AZURE.NOTE] Dette afsnit gælder for **Azure CDN Standard fra Verizon** og **Azure CDN standardversionen fra Akamai** profiler.

1. Klik på det CDN slutpunkt, du vil administrere bladet CDN profil.

    ![LEVERANDØR(er) profil blade slutpunkter](./media/cdn-file-compression/cdn-endpoints.png)

    Bladet CDN slutpunkt åbnes.

2. Klik på knappen **Konfigurer** .

    ![Knappen Administrer CDN profil blade](./media/cdn-file-compression/cdn-config-btn.png)

    Bladet CDN konfiguration åbnes.

3. Slå **komprimering**.

    ![LEVERANDØR(er) komprimeringsindstillinger](./media/cdn-file-compression/cdn-compress-standard.png)

4. Brug standardposttyperne, eller redigere listen ved at fjerne eller tilføje filtyper.
    
    > [AZURE.TIP] Mulige, mens anbefales det ikke at anvende komprimering på komprimeret formater, som ZIP, MP3, MP4, JPG osv.
    
5. Når du har foretaget dine ændringer, skal du klikke på knappen **Gem** .

### <a name="premium-tier"></a>Premium niveau

> [AZURE.NOTE] Dette afsnit gælder for **Azure CDN Premium fra Verizon** profiler.

1. Klik på knappen **Administrer** fra bladet CDN profil.

    ![Knappen Administrer CDN profil blade](./media/cdn-file-compression/cdn-manage-btn.png)

    Portalen CDN management åbnes.

2. Holde markøren over fanen **HTTP store** , og derefter holde markøren over **Cacheindstillinger** pop op.  Klik på **komprimering**.

    Komprimeringsindstillinger vises.

    ![Komprimering](./media/cdn-file-compression/cdn-compress-files.png)

3. Aktivere komprimering ved at klikke på alternativknappen **Komprimering aktiveret** .  Angiv MIME-typer, du vil komprimere som en kommasepareret-liste (uden mellemrum) i tekstfeltet **Filtyper** .
        
    > [AZURE.TIP] Mulige, mens anbefales det ikke at anvende komprimering på komprimeret formater, som ZIP, MP3, MP4, JPG osv. 

4. Når du har foretaget dine ændringer, skal du klikke på knappen **Opdater** .


## <a name="compression-rules"></a>Regler for komprimering

Disse tabeller beskriver Azure CDN komprimering funktionsmåde for hver scenarie.

> [AZURE.IMPORTANT] Til **Azure CDN fra Verizon** (Standard og Premium) komprimeres kun berettiget filer.  For at være berettiget til komprimering, skal en fil:
>
> - Være større end 128 byte.
> - Være mindre end 1 MB.
> 
> Alle filer er berettiget til komprimering til **Azure CDN fra Akamai**.
>
> For alle Azure CDN produkter, skal en fil være en MIME-type, der er blevet [konfigureret til komprimering](#enabling-compression).
>
> **Azure CDN fra Verizon** profiler (Standard og Premium) understøtter **gzip**, **deflate**eller **bzip2** kodning.  **Azure CDN fra Akamai** profiler understøtter kun **gzip** -kodning.
>
> **Azure CDN fra Akamai** slutpunkter anmode altid **gzip** kodet filer origin, uanset klientens anmodning.

### <a name="compression-disabled-or-file-is-ineligible-for-compression"></a>Komprimering deaktiveret eller en fil er uberettiget til komprimering

|Klienten anmodede format (via Acceptér-kodning sidehoved)|Cachelagrede filformat|LEVERANDØR(er) svar til klienten|Noter|
|----------------|-----------|------------|-----|
|Komprimeret|Komprimeret|Komprimeret|   |
|Komprimeret|Dekomprimerede|Dekomprimerede|    | 
|Komprimeret|Ikke cachelagret|Komprimeret eller ikke-komprimeret format|Afhænger af origin svar|
|Dekomprimerede|Komprimeret|Dekomprimerede|    |
|Dekomprimerede|Dekomprimerede|Dekomprimerede|    |   
|Dekomprimerede|Ikke cachelagret|Dekomprimerede|     |

### <a name="compression-enabled-and-file-is-eligible-for-compression"></a>Komprimering aktiveret og fil er berettiget til komprimering

|Klienten anmodede format (via Acceptér-kodning sidehoved)|Cachelagrede filformat|LEVERANDØR(er) svar til klienten|Noter|
|----------------|-----------|------------|-----|
|Komprimeret|Komprimeret|Komprimeret|LEVERANDØR(er) transcodes mellem understøttede formater|
|Komprimeret|Dekomprimerede|Komprimeret|LEVERANDØR(er) udfører komprimering|
|Komprimeret|Ikke cachelagret|Komprimeret|LEVERANDØR(er) udfører komprimering, hvis origin returnerer dekomprimerede.  **Azure CDN fra Verizon** overfører dekomprimerede filen på den første anmodning og derefter Komprimer og filen efterfølgende anmodninger om at gemme i cachen.  Filer med `Cache-Control: no-cache` sidehoved, aldrig komprimeres. 
|Dekomprimerede|Komprimeret|Dekomprimerede|LEVERANDØR(er) udfører dekomprimering|
|Dekomprimerede|Dekomprimerede|Dekomprimerede|     |  
|Dekomprimerede|Ikke cachelagret|Dekomprimerede|     |

## <a name="media-services-cdn-compression"></a>Media Services CDN komprimering

Af Media Services CDN aktiveret streaming slutpunkter, komprimering er aktiveret som standard for de følgende indholdstyper: vnd.ms-program-sstr + xml, application/dash+xml,application/vnd.apple.mpegurl, programmet/f4m + xml. Du kan ikke aktivere/deaktivere komprimering for de nævnte typer ved hjælp af portalen Azure.  

## <a name="see-also"></a>Se også
- [Fejlfinding i forbindelse med CDN komprimering](cdn-troubleshoot-compression.md)    
