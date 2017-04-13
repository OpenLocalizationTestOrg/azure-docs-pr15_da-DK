En DNS-zone bruges til at være vært for DNS-posterne for et bestemt domæne. Hvis du vil starte vært for dit domæne, skal du oprette en DNS-zone. Alle DNS-post, der er oprettet for et bestemt domæne bliver i en DNS-zone for domænet. 

Domænet "contoso.com" kan for eksempel indeholder et antal DNS-poster, som "mail.contoso.com" (for en e-mail-server) og "www.contoso.com" (for et websted). 


## <a name="names"></a>Om DNS-zone navne
 
- Navnet på zonen skal være entydig i ressourcegruppen, og zonen må ikke findes allerede. Ellers mislykkes handlingen.

- Det samme zonenavn kan genbruges i en anden ressourcegruppe eller et andet Azure-abonnement. 

- Hvor flere zoner deler det samme navn, der skal tildeles hver forekomst server-adresser for andet navn, og kun én forekomst kan uddelegeres fra det overordnede domæne. Du kan finde yderligere oplysninger finder [stedfortræder et domæne til Azure DNS](../articles/dns/dns-domain-delegation.md).