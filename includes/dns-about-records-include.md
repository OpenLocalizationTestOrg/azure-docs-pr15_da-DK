## <a name="about-records"></a>Om poster

Hver DNS-post, har et navn og en type. Poster er organiseret i forskellige typer ifølge de data, de indeholder. Den mest udbredte type er en "En" post, der knytter et navn til en IPv4-adresse. En anden type er et "MX"-poster, der knytter et navn til en e-mail-server.

Azure DNS understøtter alle almindelige DNS-posttyper, herunder A, AAAA, CNAME, MX, NS, PTR, SOA, SRV og TXT. Bemærk, at:
- SOA postsæt oprettes automatisk med hver zone, de kan ikke oprettes separat.
- SPF-poster skal oprettes ved hjælp af posttypen TXT. Du kan finde yderligere oplysninger finder [denne side](http://tools.ietf.org/html/rfc7208#section-3.1).

I Azure DNS angives poster ved hjælp af relative navne. Et "fuldt kvalificeret" domænenavn (fulde Domænenavn) indeholder navnet på zone, mens et "relativt" navn ikke. For eksempel giver det relative post navn "www" i zonen "contoso.com" fuldstændige post navn www.contoso.com.

## <a name="about-record-sets"></a>Om postsæt

Du vil oprette mere end én DNS-post med et givent navn og type. Lad os antage, at webstedet "www.contoso.com" er placeret på to forskellige IP-adresser. Webstedet kræver to forskellige poster, en for hver IP-adresse. Dette er et eksempel på et postsæt:

    www.contoso.com.        3600    IN  A   134.170.185.46
    www.contoso.com.        3600    IN  A   134.170.188.221

Azure DNS håndterer DNS-poster ved hjælp af postsæt. Et postsæt er en samling af DNS-poster i en zone, der har samme navn og er af samme type. De fleste postsæt indeholder en enkelt post, men eksempler som denne, hvor et postsæt indeholder mere end én post, er ikke usædvanlige.

SOA og CNAME-poster der er undtagelser. DNS-standarder tillade ikke flere poster med det samme navn for disse filtyper.

Tid til live eller TTL, du angiver, hvor lang tid hver post cachelagres ved klienter før forespørges igen. I dette eksempel er TTL 3.600 sekunder eller 1 time. TTL er angivet for postsæt ikke for hver post, så den samme værdi bruges for alle poster i pågældende postsæt.

#### <a name="wildcard-record-sets"></a>Jokertegn post sæt

Azure DNS understøtter [jokertegn poster](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Disse der returneres af en forespørgsel med en tilsvarende navn (medmindre der er passer bedre fra et ikke-jokertegn postsæt). Jokertegn postsæt understøttes for alle posttyper undtagen NS og SOA.  

Hvis du vil oprette et jokertegn postsæt, Brug navnet på postsæt "\*". Eller brug et navn med navnet "\*", for eksempel"\*.foo".

#### <a name="cname-record-sets"></a>CNAME-post sæt

CNAME-post sæt fungerer ikke sammen med andre poster sæt med samme navn. For eksempel kan du oprette en CNAME-post, der er konfigureret med det relative navn "www" og en A-post med det relative navn "www" på samme tid. Fordi dennes zone nedre (navn = ‘@’) indeholder altid NS og SOA optage sæt, der er oprettet, når zonen blev oprettet, du kan ikke oprette en CNAME-post, der er angivet i zone af toppen. Disse begrænsninger opstår fra DNS-standarder og ikke begrænsninger i forbindelse med Azure DNS.
