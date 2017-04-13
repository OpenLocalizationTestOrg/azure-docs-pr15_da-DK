**Virtuelt diske: per begrænsninger for konto**

Ressource|Standardgrænse
---|---
Samlet diskkapacitet per konto|35 TB
Samlet snapshot kapacitet per konto|10 TB
Maks båndbredde per konto (vandindtrængen + udgangspunkt<sup>1</sup>)|< = 50 Gbps

<sup>1</sup> *Vandindtrængen* refererer til alle data (anmodninger) bliver sendt til en lagerplads konto. *Udgangspunkt* refererer til alle data (svar) modtages fra en lagerplads-konto.

**Virtuelt diske: per disk begrænsninger**

Premium lagerplads Disk Type | P10 | R20 = | P30
---|---|---|---
Diskstørrelse | 128 giB | 512 giB | 1024 giB (1 TB)
Max IOPS per disk | 500 | 2300 | 5000
Maks overførselshastighed per disk | 100 MB sekundet | 150 MB sekundet | 200 MB sekundet
Maksimalt antal diske per lagerplads konto | 280 | 70 | 35

**Virtuelt diske: per VM begrænsninger**

Ressource|Standardgrænse
---|---
Maks IOP'ER Per VM|80.000 IOP'ER med GS5 VM<sup>1</sup>
Maks overførselshastighed per VM|2000 MB/s med GS5 VM<sup>1</sup>

<sup>1</sup> Se i [VM størrelse](../articles/virtual-machines/virtual-machines-linux-sizes.md) for begrænsninger på andre VM størrelser. 
