<properties
    pageTitle="Eksempel infrastruktur gennemgang | Microsoft Azure"
    description="Få mere at vide om de vigtigste design og implementering retningslinjer for installation af en eksempel-infrastruktur i Azure."
    documentationCenter=""
    services="virtual-machines-linux"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="example-azure-infrastructure-walkthrough"></a>Eksempel Azure infrastruktur gennemgang

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

I denne artikel indeholder en gennemgang opbygge en eksempel programmet infrastruktur. Vi Detaljeformater designe en infrastruktur for en enkelt onlinebutik, som samler alle retningslinjer og beslutninger omkring navngivningskonventioner, tilgængelighed sæt, virtuelle netværk og Indlæs balancere, og faktisk implementering af din virtuelle maskiner (VM'er).


## <a name="example-workload"></a>Eksempel arbejdsbelastning

Adventure Works skifter ønsker at opbygge en onlinebutik programmet i Azure, der består af:

- To nginx servere, der kører klienten front end i ét web lag
- To nginx servere behandling af data og ordrer i et program lag
- To MongoDB servere del af en delt klynge til lagring af produktdata og ordre i en database lag
- To Active Directory-domænecontrollere til kundekonti og leverandører i en godkendelse lag
- Alle serverne, der er placeret i to undernet:
    - en front-end-undernet til webserverne 
    - et back end-undernet for application servere, MongoDB klynge og domænecontrollere

![Diagram over forskellige niveauer for programmet infrastruktur](./media/virtual-machines-common-infrastructure-service-guidelines/example-tiers.png)

Indgående secure webtrafik skal Indlæs-afstemmes mellem webserverne som kunder gå online store. Rækkefølge behandling trafik i form af HTTP anmodninger fra internettet servere skal Indlæs-afstemmes mellem application servere. Desuden skal infrastrukturen være udviklet til høj tilgængelighed.

Den resulterende design skal inkorporere:

- Et Azure-abonnement og konto
- En enkelt ressourcegruppe
- Lagerplads konti
- Et virtuelt netværk med to undernet
- Tilgængelighed indstiller for FOS med en lignende rolle
- Virtuelle maskiner

Alle ovenstående skal du følge disse navngivningskonventioner:

- Works skifter bruger på eventyr **[IT arbejdsbelastningen]-[placering]-[Azure ressource]** som et præfiks
    - I dette eksempel skal du "**azos**" (Azure Online Store) er IT arbejdsbelastningen navnet og "**Brug**" (af USA 2) er placeringen
- Lagerplads konti Brug adventureazosusesa**[beskrivelse]**
    - 'adventure' blev føjet til præfikset, du vil angive entydige, og lagerplads kontonavne understøtter ikke brugen af bindestreger.
- Virtuelt Network bruges AZOS-Brug-VN**[antal]**
- Tilgængelighed sæt bruger azos-bruger-som-**[rolle]**
- Virtuelt i navne bruges der azos-bruger-vm -**[vmname]**


## <a name="azure-subscriptions-and-accounts"></a>Azure-abonnementer og -konti

Adventure Works skifter bruger deres Enterprise-abonnement, med navnet Adventure Works Enterprise-abonnement, for at angive fakturering for denne IT arbejdsbyrde.


## <a name="storage-accounts"></a>Lagerplads konti

Adventure Works skifter fastsat, at de skal bruges to lagerplads konti:

- **adventureazosusesawebapp** til standard opbevaring af webserverne, programmet-servere og domænecontrollere og deres datadisce.
- **adventureazosusesadbclust** til Premium opbevaring af MongoDB delt klyngeservere og deres datadisce.


## <a name="virtual-network-and-subnets"></a>Virtuelt netværk og undernet

Fordi det virtuelle netværk ikke behøver igangværende forbindelse til Adventure arbejde skifter lokale netværk, besluttet de i et virtuelt netværk kun skyen.

De oprettet et kun skyen virtuelt netværk med følgende indstillinger ved hjælp af portalen Azure:

- Navn: AZOS-Brug-VN01
- Placering: Af US 2
- Virtuelt netværk adresseområde: 10.0.0.0/8
- Første undernet:
    - Navn: front end
    - Adresse mellemrum: 10.0.1.0/24
- Anden undernet:
    - Navn: backend-version
    - Adresse mellemrum: 10.0.2.0/24


## <a name="availability-sets"></a>Tilgængelighed sæt

Hvis du vil bevare høj tilgængelighed af alle fire dele af deres onlinebutik, besluttet Adventure Works skifter på fire tilgængelighed sæt:

- **azos brug som web** web-servere
- **azos brug som app** til application servere
- **azos brug som db** for servere i den MongoDB delt klynge
- **azos brug som dc** ved domæne enheder


## <a name="virtual-machines"></a>Virtuelle maskiner

Adventure Works skifter besluttet på følgende navne til deres Azure FOS:

- **azos-Brug-vm-web01** for den første webserver
- **azos-Brug-vm-web02** for den anden webserver
- **azos-Brug-vm-app01** for den første programserver
- **azos-Brug-vm-app02** for den anden programserver
- **azos-Brug-vm-db01** for den første MongoDB server i klyngen
- **azos-Brug-vm-db02** for den anden MongoDB server i klyngen
- **azos-Brug-vm-dc01** til første domænecontrolleren
- **azos-Brug-vm-dc02** til anden domænecontrolleren

Her er den resulterende konfiguration.

![Endelige programmet infrastruktur implementeret i Azure](./media/virtual-machines-common-infrastructure-service-guidelines/example-config.png)

Denne konfiguration omfatter:

- Et kun skyen virtuelt netværk med to undernet (front end og back end)
- To lagerplads konti
- Fire tilgængelighed sæt, en for hvert niveau af online store
- Virtuelle maskiner til de fire lag
- En ekstern belastning afstemmes sæt for HTTPS-baserede webtrafik fra internettet til webserverne
- En intern belastning afstemmes sæt for ikke-krypteret webtrafik fra webserverne til application servere
- En enkelt ressourcegruppe


## <a name="next-steps"></a>Næste trin

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 