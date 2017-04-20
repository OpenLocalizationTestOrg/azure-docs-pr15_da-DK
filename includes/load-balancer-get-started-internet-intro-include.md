En Azure belastning er belastningsjustering Layer 4 (TCP, UDP). Belastning giver høj tilgængelighed ved at distribuere indgående trafik mellem sunde tjenesteforekomster i cloud services eller virtuelle maskiner i et sæt med load balancer. Azure belastning kan også give disse tjenester på flere porte, flere IP-adresser eller begge dele.

Du kan konfigurere belastningsjustering til:

* Indlæse saldi indgående internettrafik til virtuelle maskiner (VM'er). Vi henviser til en belastning i denne situation som en [belastning på internettet](../articles/load-balancer/load-balancer-internet-overview.md).
* Load balance trafik mellem FOS i et virtuelt netværk (VNet), mellem FOS i cloud-tjenester eller mellem lokale computere og FOS i et virtuelt netværk på tværs af lokaler. Vi henviser til en belastning i denne situation som en [intern belastning (ILB)](../articles/load-balancer/load-balancer-internal-overview.md).
* Til ekstern trafik til en bestemt forekomst af VM.
