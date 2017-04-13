#### <a name="create-an-a-record-set-with-single-record"></a>Oprette en A-post, der er konfigureret med en enkelt post

Brug til at oprette et postsæt `azure network dns record-set create`. Angive ressourcegruppen, zonenavn, post angive relative navn, posttype og tid til live (TTL).

    azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300

Når du har oprettet A post sæt, skal du tilføje IPv4-adressen til den post, der er konfigureret med `azure network dns record-set add-record`.

    azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1

#### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Oprette en AAAA-post, der er konfigureret med en enkelt post

    azure network dns record-set create myresourcegroup contoso.com "test-aaaa" AAAA --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-aaaa" AAAA -b "2607:f8b0:4009:1803::1005"

#### <a name="create-a-cname-record-set-with-a-single-record"></a>Oprette en CNAME-post, der er konfigureret med en enkelt post

CNAME-poster giver kun én enkelt strengværdi.


    azure network dns record-set create -g myresourcegroup contoso.com  "test-cname" CNAME --ttl 300

    azure network dns record-set add-record  myresourcegroup contoso.com  test-cname CNAME -c "www.contoso.com"


#### <a name="create-an-mx-record-set-with-a-single-record"></a>Oprette en MX-post, der er konfigureret med en enkelt post

I dette eksempel skal vi bruge postsæt navnet "@" til at oprette MX-posten i zone af toppen (i dette tilfælde "contoso.com"). Dette er fælles for MX-poster.

    azure network dns record-set create myresourcegroup contoso.com  "@"  MX --ttl 300

    azure network dns record-set add-record -g myresourcegroup contoso.com  "@" MX -e "mail.contoso.com" -f 5


#### <a name="create-an-ns-record-set-with-a-single-record"></a>Oprette en NS-post, der er konfigureret med en enkelt post

    azure network dns record-set create myresourcegroup contoso.com test-ns  NS --ttl 300

    azure network dns record-set add-record myresourcegroup  contoso.com  "test-ns" NS -d "ns1.contoso.com"

#### <a name="create-a-ptr-record-set-with-a-single-record"></a>Oprette en PTR-registreringen, der er konfigureret med en enkelt post  
I dette tilfælde ' min-arpa-zone.com' repræsenterer zonen ARPA, der repræsenterer din IP-område.  Hver PTR-registreringen, der er angivet i denne zone svarer til en IP-adresse i denne IP-område.    

    azure network dns record-set add-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"   

#### <a name="create-an-srv-record-set-with-a-single-record"></a>Oprette en SRV-post, der er konfigureret med en enkelt post

Hvis du opretter en SRV-post i roden af zonen, kan du angive "_service" og "_protocol" i navnet på posten. Det er ikke nødvendigt at medtage "@" i navnet på posten.


    azure network dns record-set create myresourcegroup contoso.com "_sip._tls" SRV --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 - w 5 -o 8080 -u "sip.contoso.com"

#### <a name="create-a-txt-record-set-with-single-record"></a>Oprette en TXT-post, der er konfigureret med en enkelt post

    azure network dns record-set create myresourcegroup contoso.com "test-TXT" TXT --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-txt" TXT -x "this is a TXT record"
