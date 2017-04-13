I følgende tabel beskrives hver af den overordnede kvoter, begrænsninger, standarder og throttles i Azure Scheduler.

|Ressource|Grænse beskrivelse|
|---|---|
|**Jobbet størrelse**|Den maksimale job størrelse er 16K. Hvis en læg eller en programrettelse resulterer i et job, der er større end disse begrænsninger, returneres en 400 forkert anmodning statuskode.|
|**Anmode om URL-adressen størrelse**|Maksimumstørrelsen for URL-anmodningen er 2048 tegn.|
|**Sammenlæg sidehoved størrelse**|Maksimale Sammenlæg sidehoved størrelse er 4096 tegn.|
|**Sidehoved Tæl**|Største sidehoved antallet er 50 sidehoveder.|
|**Brødteksten størrelse**|Maksimale brødtekst størrelse er 8192 tegn.|
|**Gentagelse omfanget**|Maksimale gentagelse omfanget er 18 måneder.|
|**Tid til at starttidspunktet**|Maksimum "tid at starttidspunktet" er 18 måneder.|
|**En oversigt over**|Maksimale svar brødtekst, der er gemt i en oversigt over er 2048 byte.|
|**Frekvens**|Kvoten for maks frekvens standard er 1 time i en gratis job af websteder og 1 minut i en standard job af websteder. Den maksimale hyppighed kan konfigureres på en sag af websteder skal være mindre end det maksimale antal. Alle job i samlingen job er begrænset værdien er angivet på samlingen job. Hvis du forsøger at oprette et job med en højere frekvens end den maksimale hyppighed på samlingen jobbet mislykkes anmodning med en 409 konflikt statuskode.|
|**Job**|Kvoten standard Maks job er 5 job i en gratis job af websteder og 50 sager i en standard job af websteder. Det maksimale antal sager kan konfigureres på en sag af websteder. Alle job i samlingen job er begrænset værdien er angivet på samlingen job. Hvis du forsøger at oprette flere job end kvoten maksimale job, mislykkes anmodningen med en 409 konflikt statuskode.|
|**Opbevaring af udskriftsjob historik**|En oversigt over bevares for op til 2 måneder eller op til de sidste 1000 udførelser.|
|**Færdige og fejl jobopbevaring**|Fuldførte og fejl job bevares i 60 dage.|
|**Timeout**|Der findes en statisk (ikke kan konfigureres) anmodning om timeout på 60 sekunder for HTTP-handlinger. I længere tid, der kører handlinger, skal du benytte HTTP asynkron protokoller; for eksempel returnerer en 202 med det samme, men fortsætte med at arbejde i baggrunden.|
