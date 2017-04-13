Ressource|Standardgrænse
---|---
Antallet af lagerplads konti per abonnement|200<sup>1</sup>
TB hver lagerplads konto|500 TB
Maksimalt antal blob beholdere, blob, filshares, tabeller, køer, enheder eller meddelelser om lagerplads konto|Kun grænsen er 500 TB konto lagerkapacitet
Maks størrelsen på en enkelt blob objektbeholder, tabel eller kø|500 TB
Maksimal antal blokke i en blok blob eller føje blob|50000
Maksimal størrelse af en blok i en blok blob eller føje blob|4 MB
Maksimal størrelse på en blok blob eller føje blob|50000 x 4 GB, som hukommelse 195 
Maks størrelsen på en side blob |1 TB
Maks størrelsen på en tabel-enhed|1 MB
Det maksimale antal egenskaber i en tabel-enhed|252
Maksimal størrelse på en meddelelse en kø|64 KB
Maks størrelsen på et filshare|5 TB
Maksimal størrelse for en fil i et filshare|1 TB
Det maksimale antal filer i et filshare|Kun grænsen er det pågældende filshare 5 TB samlede kapacitet
Maks 8 KB IOP'ER hver del|1000
Det maksimale antal filer i et filshare|Kun grænsen er det pågældende filshare 5 TB samlede kapacitet
Maksimalt antal blob beholdere, blob, filshares, tabeller, køer, enheder eller meddelelser om lagerplads konto|Kun grænsen er 500 TB konto lagerkapacitet
Maksimalt antal lagrede access politikker per objektbeholder, filshare, tabel eller kø|5
Samlet anmode om i (Hvis 1KB Objektstørrelse) hver lagerplads konto|Op til 20.000 IOP'ER objekter sekundet eller meddelelser sekundet
Overførsel af mål for enkelt blob|Op til 60 MB til hver anden eller op til 500 anmodninger sekundet
Overførsel af mål for enkelt kø (1 KB meddelelser)|Op til 2000-meddelelser sekundet
Overførsel af mål for enkelt tabel partition (1 KB enheder)|Op til 2000 enheder sekundet
Overførsel af mål for enkelt filshare|Op til 60 MB sekundet
Maks vandindtrængen<sup>2</sup> per lagerplads konto (OS områder)|10 Gbps Hvis GRS/ZRS<sup>3</sup> aktiveret, 20 Gbps for LRS
Maks udgangspunkt<sup>2</sup> per lagerplads konto (OS områder)|20 Gbps Hvis RA-GRS/GRS/ZRS<sup>3</sup> aktiveret, 30 Gbps for LRS
Maks vandindtrængen<sup>2</sup> per lagerplads konto (europæisk og asiatiske områder)|5 Gbps Hvis GRS/ZRS<sup>3</sup> aktiveret, 10 Gbps for LRS
Maks udgangspunkt<sup>2</sup> per lagerplads konto (europæisk og asiatiske områder)|10 Gbps Hvis RA-GRS/GRS/ZRS<sup>3</sup> aktiveret, 15 Gbps for LRS

<sup>1</sup> Dette omfatter både Standard og Premium lagerplads konti. Hvis du har brug for mere end 200 lagerplads konti, skal du en anmodning om via [Azure Support](https://azure.microsoft.com/support/faq/). Azure-lager teamet vil gennemgå business store og små bogstaver og kan godkende op til 250 lagerplads konti. 

<sup>2</sup> *Vandindtrængen* refererer til alle data (anmodninger) bliver sendt til en lagerplads konto. *Udgangspunkt* refererer til alle data (svar) modtages fra en lagerplads-konto.  

<sup>3</sup> Azure indstillinger for lagring af replikering omfatter:

- **RA-GRS**: læseadgang geografisk overflødige lagerplads. Hvis RA-GRS er aktiveret, er udgangspunkt mål for den sekundære placering identiske til rettighederne for den primære placering.
- **GRS**: geografisk overflødige lagerplads. 
- **ZRS**: Zone-overflødige lagerplads. Kun tilgængelig for Bloker BLOB. 
- **LRS**: lokalt overflødige lagerplads. 

