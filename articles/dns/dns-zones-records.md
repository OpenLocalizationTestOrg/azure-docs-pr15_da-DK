<properties 
   pageTitle="DNS-zoner og poster | Microsoft Azure" 
   description="Oversigt over understøttelse af vært DNS-zoner og poster i Microsoft Azure DNS." 
   services="dns" 
   documentationCenter="na" 
   authors="jtuliani" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="10/17/2016"
   ms.author="jtuliani"/>

# <a name="dns-zones-and-records"></a>DNS-zoner og poster

Denne side beskrives det, de vigtigste begreber af domæner, DNS-zoner og DNS-poster og postsæt og hvordan de understøttes i Azure DNS.

## <a name="domain-names"></a>Domænenavne
 
Domain Name System er et hierarki af domæner. Hierarkiet starter fra ' roddomænet ', hvis navn er blot**.**.  Komme topdomæner, såsom 'com', 'net', 'Organisationsdiagram', 'Storbritannien' eller 'jp' under dette.  Under disse er andet niveau domæner, som 'org.uk' eller 'co.jp'. Domæner i hierarkiet DNS distribueres globalt, hostet af DNS-navneservere over hele verden.

En domæneregistrator er en organisation, der gør det muligt at købe et domænenavn, som 'contoso.com'.  Købe et domænenavn, får du ret til at styre DNS hierarkiet under navnet, for eksempel gør det muligt at dirigere navn 'www.contoso.com' til virksomhedens websted. Registrator kan være vært for domænet i sin egen navneservere på dine vegne, eller du kan også angive alternative navneservere.

Azure DNS indeholder en globalt fordelt, høj tilgængelighed name server-infrastruktur, som du kan bruge til at være vært for dit domæne. Du kan administrere dine DNS-poster med de samme legitimationsoplysninger, API'er, værktøjer, fakturering og support ved at placere dine domæner i Azure DNS, som dine andre Azure tjenester.

Azure DNS understøtter i øjeblikket ikke køb af domænenavne. Hvis du vil købe et domænenavn, skal du bruge en tredjeparts domæneregistrator. Registrator vil typisk tager et mindre årlige gebyr. Domænerne kan derefter bruges i Azure DNS for administration af DNS-poster. Du kan finde oplysninger i [stedfortræder et domæne til Azure DNS](dns-domain-delegation.md) .

## <a name="dns-zones"></a>DNS-zoner 

En DNS-zone bruges til at være vært for DNS-posterne for et bestemt domæne. Hvis du vil starte vært for dit domæne i Azure DNS, skal du oprette en DNS-zone for det pågældende domænenavn. Hver DNS-posten for dit domæne der oprettes i denne DNS-zone. 

For eksempel kan domæne 'contoso.com' indeholde flere DNS-poster, som 'mail.contoso.com' (for en e-mail-server) og 'www.contoso.com' (for et websted).

Når du opretter en DNS-zone i Azure DNS, skal navnet på zonen være entydig i ressourcegruppen. Det samme zonenavn kan genbruges i en anden ressourcegruppe eller et andet Azure-abonnement. Hvor flere zoner deler det samme navn, tildeles hver forekomst andet navn serveradresser. Kun ét sæt adresser kan konfigureres med domæneregistratoren.

>[AZURE.NOTE] Du behøver ikke at ejer et domænenavn til at oprette en DNS-zone med pågældende domænenavn i Azure DNS. Men, behøver du ejer domænet for at konfigurere Azure DNS-navneservere, som de korrekte navneservere for domænenavnet med domæneregistratoren.

Du kan finde yderligere oplysninger finder [stedfortræder et domæne til Azure DNS](dns-domain-delegation.md).

## <a name="dns-records"></a>DNS-poster

### <a name="record-types"></a>Posttyper

Hver DNS-post, har et navn og en type. Poster er organiseret i forskellige typer ifølge de data, de indeholder. Den mest udbredte type er en 'A' post, der knytter et navn til en IPv4-adresse. En anden almindelige type er en 'MX' post, der knytter et navn til en e-mail-server.

Azure DNS understøtter alle almindelige DNS-posttyper: A, AAAA, CNAME, MX, NS, PTR, SOA, SRV og TXT.

### <a name="record-names"></a>Optage navne

I Azure DNS angives poster ved hjælp af relative navne. Et *fuldt kvalificeret* domænenavn (fulde Domænenavn) indeholder navnet på zone, mens et *relativt* navn ikke. For eksempel giver det relative post navn "www" i zonen 'contoso.com' navnet på fuldt kvalificerede posten 'www.contoso.com'.

En *nedre* post er en DNS-post på rod (eller *toppen*) af en DNS-zone. For eksempel i DNS-zonen 'contoso.com' har en nedre post også det fulde navn 'contoso.com' (dette kaldes også et *nøgen* domæne).  Ved konferencen, det relative navn '@' bruges til at oprette nedre poster.

### <a name="record-sets"></a>Postsæt
Du vil oprette mere end én DNS-post med et givent navn og type. Lad os antage, at webstedet 'www.contoso.com' er placeret på to forskellige IP-adresser. Webstedet kræver to forskellige poster, en for hver IP-adresse. Dette er et eksempel på et postsæt:

    www.contoso.com.        3600    IN  A   134.170.185.46
    www.contoso.com.        3600    IN  A   134.170.188.221

Azure DNS håndterer DNS-poster ved hjælp af *post angiver*. Et postsæt (også kendt som en *ressource* postsæt) er en samling af DNS-poster i en zone, der har samme navn og er af samme type. De fleste postsæt indeholder en enkelt post, men eksempler som denne, hvor et postsæt indeholder mere end én post, er ikke usædvanlige.

For eksempel Antag, at du allerede har oprettet en A-post "www." i zonen 'contoso.com', der peger til IP-adresse '134.170.185.46' (den første post ovenfor).  Hvis du vil oprette den anden post du vil tilføje denne post til den eksisterende postsæt i stedet for oprette en ny postsæt.

Posttyperne SOA og CNAME er undtagelser. DNS-standarder tillade ikke flere poster med det samme navn for disse filtyper, og som derfor disse postsæt kun kan indeholde en enkelt post.

### <a name="time-to-live"></a>Time to live

Tid til live eller TTL, du angiver, hvor lang tid hver post cachelagres af klienter før forespørges igen. I eksemplet ovenfor er TTL 3600 sekunder eller 1 time.

Azure DNS er TTL angivet for i postsæt ikke for hver post, så den samme værdi bruges for alle poster i pågældende postsæt.  Du kan angive en hvilken som helst TTL-værdi mellem 1 og 2.147.483.647 sekunder.

### <a name="wildcard-records"></a>Jokertegn poster

Azure DNS understøtter [jokertegn poster](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Jokertegn poster returneres som svar på en forespørgsel med en tilsvarende navn (medmindre der er passer bedre fra et ikke-jokertegn postsæt). Jokertegn postsæt understøttes for alle posttyper undtagen NS og SOA.  

Hvis du vil oprette et jokertegn postsæt, Brug navnet på postsæt '\*'. Alternativt kan du også bruge et navn med "\*'som dets længst til venstre etiket, for eksempel'\*.foo'.

### <a name="cname-records"></a>CNAME-poster

CNAME-post sæt fungerer ikke sammen med andre poster sæt med samme navn. For eksempel kan du oprette en CNAME-post, der er konfigureret med det relative navn "www." og en A-post med det relative navn "www." på samme tid.

Fordi dennes zone nedre (navn = ‘@’) indeholder altid NS og SOA optage sæt, der er oprettet, når zonen blev oprettet, du kan ikke oprette en CNAME-post, der er angivet i zone af toppen.

Disse begrænsninger opstår fra DNS-standarder og kan ikke begrænsninger i forbindelse med Azure DNS.

### <a name="ns-records"></a>NS-poster

En NS postsæt der oprettes automatisk i toppen af hver zone (navn = ‘@’), og slettes automatisk, når zonen slettes (det kan ikke slettes separat).  Du kan ændre TTL på denne postsæt, men du kan ikke redigere de poster, der er konfigureret til at referere til Azure DNS-navneservere, der er tildelt på forhånd.

Du kan oprette og slette andre NS-poster i zonen, ikke i zone af toppen.  Du kan konfigurere underordnede zoner (se [Delegating underdomæner i Azure DNS](dns-domain-delegation.md#delegating-sub-domains-in-azure-dns)).

### <a name="soa-records"></a>SOA poster

Et SOA postsæt der oprettes automatisk i toppen af hver zone (navn = ‘@’), og slettes automatisk, når zonen slettes.  SOA poster kan ikke oprettes eller slettes separat. 

Du kan redigere alle egenskaber for posten SOA med undtagelse af egenskaben 'host', som er konfigureret til at referere til den primære navn servernavn fra Azure DNS på forhånd.

### <a name="spf-records"></a>SPF-poster

Afsenderen Policy Framework (SPF) poster bruges til at angive, hvilke mailservere har tilladelse til at sende mail på vegne af en given domænenavn.  Korrekt konfiguration af SPF-poster er vigtigt at forhindre modtagere af markere din mail som "uønsket".

De DNS-RFC'er oprindeligt blev introduceret en ny 'SPF' posttype til dette scenario. For at understøtte ældre navneservere skal tilladt de også brug af typen TXT-post til at angive SPF-poster.  Denne tvetydighed ført til forvirring, som blev løst med [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1).  Dette angivet, bør kun oprettes med posttypen TXT SPF-poster, og forældet posttypen SPF. 

**SPF-poster, der understøttes af Azure DNS og skal oprettes med posttypen TXT.** Forældede SPF record type understøttes ikke. Når [import af en DNS zone fil](dns-import-export.md), en SPF-poster ved hjælp af typen SPF-post, konverteres til posttypen TXT. 

### <a name="srv-records"></a>SRV-poster

[SRV-poster](https://en.wikipedia.org/wiki/SRV_record) anvendes af forskellige tjenester til at angive serverplaceringer. Når du angiver en SRV-post i Azure DNS:

- *Tjenesten* og *protokoller* skal være angivet som en del af navnet på postsæt præfikset understregningstegn.  For eksempel '\_sip. \_tcp.name'.  For en post i toppen zone, der er ingen grund til at angive '@' i navnet på posten blot bruge tjenesten og protokol, f.eks. '\_sip. \_tcp'.
- *Prioritet*, *vægt*, *port*og *mål* er angivet som parametre for hver post i postsættet. 

## <a name="tags-and-metadata"></a>Mærker og metadata

### <a name="tags"></a>Mærker
Mærker er en liste over navne værdipar og bruges ved Azure ressourcestyring til etiket ressourcer.  Azure ressourcestyring bruger mærker til at aktivere filtrerede visninger af din Azure faktura, og gør det også muligt at angive en politik, som er nødvendige mærker. Du kan finde flere oplysninger om mærker, [ved hjælp af mærker til at organisere dine Azure ressourcer](../resource-group-using-tags.md).

Azure DNS understøtter brug Azure ressourcestyring koder på DNS-zone ressourcer.  Mærker understøttes på DNS-post sæt ikke.

### <a name="metadata"></a>Metadata

Som et alternativ til mærker postsæt understøtter Azure DNS anmærkninger post sæt ved hjælp af 'metadata'.  Ligner mærker, metadata gør det muligt at knytte navn / værdi-par til hver postsæt.  Det kan være nyttigt, for eksempel til post formålet med hver postsæt.  I modsætning til mærker, metadata kan ikke bruges til at levere en filtreret visning af din Azure faktura og kan ikke angives i en politik til Azure ressourcestyring.

## <a name="limits"></a>Begrænsninger

Følgende begrænsninger for standard gælder, når ved hjælp af Azure DNS:

[AZURE.INCLUDE [dns-limits](../../includes/dns-limits.md)] 

## <a name="next-steps"></a>Næste trin

- Hvis du vil begynde at bruge Azure DNS, se, hvordan du [opretter en DNS-zone](dns-getstarted-create-dnszone-portal.md) og [oprette DNS-poster](dns-getstarted-create-recordset-portal.md).
- For at overføre en eksisterende DNS-zone kan få mere at vide om at [importere og DNS zone file](dns-import-export.md).
