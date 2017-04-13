## <a name="what-is-reverse-dns"></a>Hvad er modsat DNS?

Traditionelt DNS-poster aktivere en tilknytning fra et DNS-navn (såsom 'www.contoso.com') til en IP-adresse (såsom 64.4.6.100).  Modsat DNS gør oversættelsen af en IP-adresse (64.4.6.100) tilbage til et navn (www.contoso.com).

Modsat DNS-poster, der bruges i mange situationer. For eksempel bruges modsat DNS-poster bredt i bekæmpelse af uønsket e-mail ved at kontrollere afsenderen af en e-mail-meddelelse.  Modtagelse mailserveren hente modsat DNS-posten på serveren afsendende IP-adresse, og Bekræft Hvis, der er vært har tilladelse til at sende mail fra det oprindelige domæne. (Bemærk imidlertid pågældende [Azure beregne services understøtter ikke sende mails til eksterne domæner](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).)

## <a name="how-reverse-dns-works"></a>Hvordan modsat DNS fungerer

Modsat DNS-poster er hostet i speciel DNS zones, kaldet 'ARPA' zoner.  Disse zoner udgør et separat hierarki, DNS parallelt med hierarkiet normal vært domæner som 'contoso.com'.

For eksempel implementeres på DNS-post 'www.contoso.com' ved hjælp af en DNS-'A' post med navnet "www" i zone 'contoso.com'.  En A-post peger på den tilsvarende IP-adresse i dette tilfælde 64.4.6.100.  Omvendt opslag er implementeret separat, ved hjælp af en 'PTR' post med navnet '100' i zonen '6.4.64.in-addr.arpa' (Bemærk, at IP-adresser ændres i ARPA zoner.)  Denne PTR-registreringen, peger Hvis der er konfigureret korrekt, på navn 'www.contoso.com'.

Når en organisation er tildelt en IP-adresse for, skal anskaffe de også rettigheder til at administrere zonen tilsvarende ARPA. Zonerne ARPA svarer til de IP-adresseblokke, der bruges af Azure er hostet og administreres af Microsoft. Din Internetudbyder kan hoste zonen ARPA for din egen IP-adresser for dig, eller kan du muligvis være vært for zonen ARPA i en DNS-tjeneste efter eget valg, som Azure DNS.

>[AZURE.NOTE] Videresende DNS-opslag og omvendte DNS-opslag er implementeret i separate, parallelle DNS hierarkier. Omvendt opslag for 'www.contoso.com' er **ikke** hostes i zonen 'contoso.com' hellere de ligger i zonen ARPA for tilsvarende IP-adresse for.

Du kan finde flere oplysninger om modsat DNS, [DNS-opslag](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).

## <a name="azure-support-for-reverse-dns"></a>Azure understøttelse af modsat DNS

Azure understøtter to separate scenarier vedrørende Hvis du vil spejlvende DNS:

1. Vært for zonen ARPA svarer til din IP-adresse for.
2. Så du kan konfigurere modsat DNS-posten for den IP-adresse, der er tildelt til din Azure service.

For at understøtte tidligere, Azure DNS kan bruges til at hoste din ARPA zoner og administrere PTR-poster for hver DNS-opslag.  Processen med at oprette zonen ARPA, konfiguration af delegation og konfiguration af PTR-poster er den samme som for almindelige DNS zones.  Den eneste forskel er, delegation skal konfigureres via din Internetudbyder i stedet for din DNS-registrator, og kun PTR posttypen skal bruges.

For at understøtte disse, kan Azure du konfigurere omvendt opslag for de IP-adresser, der er allokeret til din tjeneste.  Denne omvendt opslag er konfigureret af Azure som en PTR-registreringen i zonen tilsvarende ARPA.  Disse ARPA zoner, der svarer til alle bruges af Azure, IP-adresseområder er hostet af Microsoft. **I resten af denne artikel beskrives dette scenario detaljeret.**
