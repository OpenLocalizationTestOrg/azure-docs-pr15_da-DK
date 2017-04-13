Du kan oprette flere tjenester inden for et abonnement, hver enkelt klargjort på et bestemt niveau, begrænset af antallet tjenester, der er tilladt på hvert niveau. For eksempel kan du oprette op til 12 services grundlæggende lag og en anden 12 services S1 lag inden for samme abonnement. Se [Vælg en anden SKU eller niveau til Azure søgning](../articles/search/search-sku-tier.md)kan finde flere oplysninger om niveauer.

Største begrænsninger kan hæves på anmodning. Hvis du har brug for flere tjenester i det samme abonnement, skal du kontakte Azure Support.

Ressource|Gratis|Grundlæggende|S1|S2|S3 |S3 HD <sup>1</sup>
---|---|---|---|----|---|----
Maksimale tjenester |1 |12 |12  |6 |6 |6 
Maksimale skala i sø <sup>2</sup>|I/t, <sup>3</sup>|3 sø <sup>4</sup> |36 SØ|36 SØ|36 SØ|12 sø, 3 sø <sup>5</sup>

<sup>1</sup> S3 HD understøtter ikke [indeks](../articles/search/search-indexer-overview.md) på nuværende tidspunkt. 

<sup>2</sup> Søg enheder (sø) er fakturerbar enheder hver tjeneste, tildeles som en *replika* eller en *partition*. Du skal bruge begge ressourcer til opbevaring, indeksering og forespørgslerne. Få flere oplysninger om gyldige kombinationer, forbliver under de maksimale grænser under [skala ressource niveauer for forespørgsel og indeks arbejdsmængder](../articles/search/search-capacity-planning.md). 

<sup>3</sup> Free er baseret på delte ressourcer, der bruges af flere abonnenter. Denne lag er der ingen dedikeret ressourcer for en enkelt abonnement. Derfor er maksimale skala markeret som ikke tilgængelige.

<sup>4</sup> basic har en fast partition. Denne lag bruges flere SUs til tildeling af yderligere replikaer til øget forespørgsel arbejdsmængder.

<sup>5</sup> S3 HD har en anderledes tildeling struktur med hensyn til tilladte kombinationer. For kopier, kan du have op til 12. Til partitioner er det maksimale antal 3.




