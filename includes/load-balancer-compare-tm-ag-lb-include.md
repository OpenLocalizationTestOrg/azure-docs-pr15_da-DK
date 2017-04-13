## <a name="load-balancer-differences"></a>Indlæse belastningsjusteringstjenesten forskelle

Der er forskellige muligheder for at distribuere netværkstrafik ved hjælp af Microsoft Azure. Disse indstillinger fungerer forskelligt fra hinanden, har du en anden funktion angive og understøtter forskellige scenarier. De kan hver bruges alene eller kombinerer dataene.

- **Azure belastningsjustering** fungerer på transport lag (lag 4 OSI netværk reference stablede). Den indeholder netværk niveau fordelingen af trafikken på tværs af forekomster af et program, der kører i den samme Azure datacenter.

- **Application Gateway** arbejder på programlag (lag 7 OSI netværk reference stablede). Det fungerer som en omvendt proxy-tjeneste, klienten forbindelsen afbrydes og videresende anmodninger til back end-slutpunkter.

- **Trafik Manager** fungerer på niveauet for DNS.  DNS-svar bruges til at dirigere slutbrugerlicensaftale trafik til globalt fordelt slutpunkter. Klienter derefter opretter forbindelse til disse slutpunkter direkte.

Den følgende tabel opsummerer de funktioner, som hver tjeneste:

| Tjenesten | Azure justering af belastning | Application Gateway | Trafik Manager |
|---|---|---|---|
|Teknologi| Transportniveau (lag 4) | Programmet niveau (lag 7) | DNS-niveau |
| Programmet protokoller, der understøttes | En hvilken som helst | HTTP- og HTTPS |  En hvilken som helst (HTTP ark er påkrævet til slutpunkt overvågning) |
| Slutpunkter | Azure FOS og Cloud Services rolle forekomster | En hvilken som helst Azure interne IP-adresse eller offentlige internet IP-adresse | Azure FOS, Cloud Services, Azure Web Apps og eksterne slutpunkter |
| Understøttelse af Vnet | Kan bruges til begge Internet modstående og interne (Vnet)-programmer | Kan bruges til begge Internet modstående og interne (Vnet)-programmer |    Understøtter kun internettet programmer |
Slutpunkt overvågning | Understøttes via sonder | Understøttes via sonder | Understøttes via HTTP/HTTPS Hent | 

Azure justering af belastning og Application Gateway route netværk trafik til slutpunkter, men de har forskellige brugsscenarier til hvilken trafik til at håndtere. Tabellen nedenfor hjælper med at forstå forskellen mellem to Indlæs balancere:

| Type | Azure justering af belastning | Application Gateway |
|---|---|---|
| Protokoller | UDP/TCP | HTTP / HTTPS |
| IP-reservation | Understøttes | Ikke understøttet | 
| Indlæse justering af belastning tilstand | 5-tuple(source IP, source port, destination IP, destination port, protocol type) | Round Robin<br>Distribution baseret på URL-adresse | 
| Tilstand af belastning (kilde-IP / sticky sessioner) |  2-tupel (IP-kilde og destination IP), 3-tupel (kilde-IP, IP-destination og port). Kan skalere op eller ned baseret på antallet virtuelle maskiner | Cookie-baseret forbindelse<br>Distribution baseret på URL-adresse |
| Sundhed sonder | Standard: efterprøvning af af interval - 15 sekunder. Fjernet: 2 fortløbende mislykkede forsøg. Understøtter brugerdefinerede sonder | Inaktiv efterprøvning af af interval 30 sekunder. Tegnes efter 5 fortløbende direkte trafik fejl eller en enkelt efterprøvning af af fejl i inaktiv tilstand. Understøtter brugerdefinerede sonder | 
| Overføre SSL | Ikke understøttet | Understøttes | 
  