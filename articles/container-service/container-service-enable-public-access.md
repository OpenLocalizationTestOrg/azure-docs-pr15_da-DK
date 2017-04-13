<properties
   pageTitle="Aktivere offentlig adgang til en ACS app | Microsoft Azure"
   description="Sådan aktiveres offentlig adgang til en Azure objektbeholder tjenesten."
   services="container-service"
   documentationCenter=""
   authors="Thraka"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, objektbeholdere, Micro-tjenester, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/26/2016"
   ms.author="timlt"/>

# <a name="enable-public-access-to-an-azure-container-service-application"></a>Aktivere offentlig adgang til en Azure objektbeholder-tjenesteprogram

En hvilken som helst DC/OS beholder i ACS [offentlige agent puljen](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) der automatisk vises på internettet. Som standard, portene **80**, **443**, **8080** åbnes og enhver (offentlige) beholder lytter på disse porte er tilgængelige. I denne artikel beskrives, hvordan du åbner flere porte til dine programmer i Azure objektbeholder tjenesten.

## <a name="open-a-port-portal"></a>Åbne en port (portal) 

Først skal vi brug at åbne den port, vi vil.

1. Log på portalen.
2. Find den ressourcegruppe, du har installeret Azure objektbeholder tjenesten til.
3. Vælg den agent justering af belastning (som hedder ligner **XXXX-agent-kg-XXXX**).

    ![Azure objektbeholder tjenesten justering af belastning](media/container-service-dcos-agents/agent-load-balancer.png)

4. Klik på **sonder** og derefter **tilføje**.

    ![Azure objektbeholder tjenesten justering af belastning sonder](media/container-service-dcos-agents/add-probe.png)

5. Udfyld formularen efterprøvning af af, og klik på **OK**.

  	| Felt | Beskrivelse |
  	| ----- | ----------- |
  	| Navn  | Et beskrivende navn på efterprøvning af af. |
  	| Port  | Porten på objektbeholder til at teste. |
  	| Sti  | (Når i HTTP-tilstand) Relative websted stien til forespørgsler. HTTPS understøttes ikke. |
  	| Interval | Mængden af tid mellem efterprøvning af af forsøger i sekunder. |
  	| Beskadiget grænseværdi | Antal på hinanden følgende efterprøvning af af forsøger før du beslutter, beskadiget objektbeholderen. | 
    

6. Tilbage i egenskaberne for agent justering af belastning, skal du klikke på **indlæse justering af belastning regler** , og klik derefter på **Tilføj**.

    ![Azure objektbeholder tjenesten Indlæs belastningsjusteringstjenesten regler](media/container-service-dcos-agents/add-balancer-rule.png)

7. Udfyld formularen belastning belastningsjusteringstjenesten, og klik på **OK**.

  	| Felt | Beskrivelse |
  	| ----- | ----------- |
  	| Navn  | Et beskrivende navn på justering af belastning. |
  	| Port  | Den offentlige indgående port. |
  	| Back end-port | Den intern offentlige port på objektbeholder til at dirigere trafik til den. |
  	| Back end-puljen | Objektbeholdere i denne pulje vil være destinationen for denne justering af belastning. |
  	| Sende forespørgsler | Bruges til at bestemme, om en destination i **back end-puljen** efterprøvning af af er i orden. |
  	| Session brugerdata | Bestemmer, hvordan trafik fra en klient skal håndteres i løbet af sessionen.<br><br>**Ingen**: efterfølgende anmodninger fra den samme klient kan håndteres af enhver beholder.<br>**Klient IP-adresse**: efterfølgende anmodninger fra den samme klient IP-adresse er håndteret af samme beholder.<br>**IP-klienten og protokol**: efterfølgende anmodninger fra den samme klient IP-protokollen kombination og håndteres af samme beholder. |
  	| Inaktiv timeout | (Kun TCP) Åbn tid til at bevare en TCP/HTTP-klient i minutter, uden at *heller* meddelelser. |

## <a name="add-a-security-rule-portal"></a>Tilføj en Sikkerhedsregel for (portal)

Dernæst skal vi brug at føje en sikkerhedsregel, som dirigerer trafik fra vores åbnet port via firewallen.

1. Log på portalen.
2. Find den ressourcegruppe, du har installeret Azure objektbeholder tjenesten til.
3. Vælg den **offentlige** agent netværk sikkerhedsgruppe (som hedder ligner **XXXX-agent-offentlige-nsg-XXXX**).

    ![Azure objektbeholder tjenesten netværk sikkerhedsgruppe](media/container-service-dcos-agents/agent-nsg.png)

4. Vælg **indgående sikkerhedsregler for** , og klik derefter på **Tilføj**.

    ![Azure objektbeholder tjenesten netværk sikkerhed gruppe regler](media/container-service-dcos-agents/add-firewall-rule.png)

5. Udfyld firewallreglen Tillad dine offentlige port, og klik på **OK**.

  	| Felt | Beskrivelse |
  	| ----- | ----------- |
  	| Navn  | Et beskrivende navn til firewallreglen. |
  	| Prioritet | Prioritet rang for reglen. Jo lavere det nummer jo højere prioritet. |
  	| Kilde | Begrænse den indgående IP-adresseområder for at tillades eller afvises af denne regel. Bruge **en hvilken som helst** ikke angive en begrænsning. |
  	| Tjenesten | Vælg et sæt af foruddefinerede tjenester denne regel er til. Ellers skal du bruge **brugerdefineret** til at oprette din egen. |
  	| Protokol | Begrænse trafik baseret på **TCP** eller **UDP**. Bruge **en hvilken som helst** ikke angive en begrænsning. |
  	| Portområde | Når **tjenesten** er **brugerdefineret**, du angiver række porte, der påvirker denne regel. Du kan bruge en enkelt port, som **80**eller et område som **1024 1500**. |
  	| Handling | Tillade eller nægte trafik, der opfylder kriterierne. |

## <a name="next-steps"></a>Næste trin

Få mere at vide om forskellen mellem [offentlige og private DC/OS supportmedarbejdere](container-service-dcos-agents.md).

Læs flere oplysninger om [Administration af din DC/OS beholdere](container-service-mesos-marathon-ui.md).