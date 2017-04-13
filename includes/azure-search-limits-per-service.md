Lagerplads er begrænset af diskplads eller af en grænse på det *maksimale antal* af indeks eller dokumenter, afhængigt af hvad der kommer først. 

Ressource|Gratis|Grundlæggende|S1|S2|S3 |S3 HD
---|---|---|---|----|---|----
Serviceniveauaftale (SLA)|Ingen <sup>1</sup> |Ja |Ja  |Ja |Ja |Ja
Lagerplads per partition|50 MB |2 GB|25 GB|100 GB|200 GB|200 GB
Partitioner hver tjeneste|I/T.|1|12|12|12|3
Partitionsstørrelse|I/T.|2 GB|25 GB|100 GB|200 GB |200 GB
Replikaer|I/T.|1|12|12|12|12
Maksimale indeks|3|5|50|200|200|1000 per partition eller 3000 hver tjeneste
Maksimale dokumenter|10.000|1 million|15 millioner per partition eller 180 millioner hver tjeneste |60 millioner per partition eller 720 millioner hver tjeneste |120 millioner per partition eller 1.4 milliarder hver tjeneste|1 million per indeks eller 200 millioner per partition |
Anslået forespørgsler sekundet (QPS)|I/T.|~ 3 / replika|~ 15 per replika|~ 60 per replika|~ 60 per replika|> 60 per replika

<sup>1</sup> gratis og Preview lagerenheder ikke kommer med serviceaftaler (SLA). SLA gennemtvinges, når en anden SKU bliver alment tilgængelig.