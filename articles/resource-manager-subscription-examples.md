<properties
   pageTitle="Scenarier for og eksempler på abonnement styring | Microsoft Azure"
   description="Indeholder eksempler på, hvordan du kan implementere Azure abonnement styring for almindelige scenarier."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="rdendtler"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/05/2016"
   ms.author="rodend;karlku;tomfitz"/>

# <a name="examples-of-implementing-azure-enterprise-scaffold"></a>Eksempler på implementere Azure enterprise scaffold

Dette emne indeholder eksempler på, hvordan en virksomhed kan implementere anbefalinger til en [Azure enterprise scaffold](resource-manager-subscription-governance.md). Et fiktivt firma med navnet Contoso bruges til at illustrere bedste fremgangsmåder til almindelige scenarier. 

## <a name="background"></a>Baggrund

Contoso er en global virksomhed, der indeholder levering kæde løsninger til kunder i alt fra en "Software som en tjeneste" model til en pakket model installeres lokalt.  De udvikle software over hele verden med betydeligt udvikling centrum i Indien, USA og Canada. 

ISV del af firmaet er inddelt i flere uafhængige afdelinger, der administrerer produkter i en betydeligt virksomhed. Hver afdeling har sin egen udviklere, produktchefer og arkitekter. 

Afdeling Enterprise teknologi (ETS) indeholder centraliserede IT-kapacitet og administrerer flere datacentre hvor forretningsenheder vært for deres programmer. Sammen med administration af datacentre ETS organisationen indeholder og administrerer centralt samarbejde (såsom mail og websteder) og netværk/telefoni tjenester. De også administrere med kundekontakt arbejdsmængder for mindre afdelinger, der ikke har funktionsdygtige personale. 

De følgende personas bruges i dette emne:

- Dave er ETS Azure-administratoren.
- Bent er Contosos direktør af udvikling i levering kæde afdeling. 

Contoso behov at oprette en line of business-app og en med kundekontakt app. Det har besluttet at køre apps på Azure. Dave læser emnet [præskriptive abonnement styring](resource-manager-subscription-governance.md) og er nu klar til at implementere anbefalingerne. 

## <a name="scenario-1-line-of-business-application"></a>Scenarie 1: line of business program

Contoso opbygning af en kilde kode administrationssystem (BitBucket) der skal bruges i udviklere over hele verden.  Programmet bruger infrastruktur som en tjeneste (IaaS) til at være vært og består af-Webserverne og en databaseserver. Udviklere adgang til servere i deres development-miljøer, men de skal have ikke adgang til serverne i Azure. Contoso ETS ønsker at tillade programmet ejeren og team til at administrere programmet. Programmet er kun tilgængelig, mens på Contosos virksomhedens netværk. Dave skal konfigurere abonnementet for dette program. Abonnementet er også vært andre elementer, der vedrører software i fremtiden.  

### <a name="naming-standards--resource-groups"></a>Navngive standarder og ressourcegrupper

Dave opretter et abonnement for at understøtte udviklerværktøjer, der er almindelige på tværs af alle afdelinger. Hun skal oprette et meningsfuldt navn for grupperne abonnement og ressource (for programmet og netværkene). Hun opretter følgende abonnement og ressource grupper:

| Element | Navn | Beskrivelse |
| ---- | ---- | ----------- |
| Abonnement | Contoso ETS DeveloperTools fremstilling | Understøtter almindelige udviklerværktøjer |
| Ressourcegruppe | rgBitBucket | Indeholder programmet webserver og databaseserver |
| Ressourcegruppe | rgCoreNetworks | Indeholder virtuelle netværk og websted til gateway-forbindelse |


### <a name="role-based-access-control"></a>Rollebaseret adgangskontrol

Når du har oprettet sin abonnement, vil Dave at sikre, at de relevante teams og ejere af programmet kan få adgang til deres ressourcer. Dave genkender, grupperne har forskellige krav. Han anvender de grupper, der er blevet synkroniseret fra Contosos lokale Active Directory (AD) til Azure Active Directory og giver det rette niveau af adgang til teams. 

Dave tildeler for abonnementet følgende roller: 

| Rolle | Tildelt til | Beskrivelse |
| ---- | ----------- | ----------- |
| [Ejer](./active-directory/role-based-access-built-in-roles.md#owner) | Administrerede ID fra Contosos AD | Dette ID styres med bare i gang (JIT) adgang via Contosos identitet administrationsværktøjer og sikrer, at abonnement ejer access fuldt overvåget. |
| [Security Manager](./active-directory/role-based-access-built-in-roles.md#security-manager) | Sikkerhed og risikoen management afdeling | Denne rolle kan brugerne se på Sikkerhedscenter Azure og status for ressourcerne. |
| [Netværk bidragyder](./active-directory/role-based-access-built-in-roles.md##network-contributor) | Netværksteam | Denne rolle kan Contosos netværksteam til at administrere til websted VPN og den virtuelle netværk. |
| *Brugerdefineret rolle* | Programmet ejer | Dave opretter en rolle, der giver mulighed for at ændre ressourcer i ressourcegruppen. Du kan finde flere oplysninger, se [Brugerdefineret roller i Azure RBAC](./active-directory/role-based-access-control-custom-roles.md) |

### <a name="policies"></a>Politikker

Dave har følgende krav til administration af ressourcer i abonnement:

- Da værktøjerne understøtter udviklere over hele verden, vil han ikke Bloker brugere fra at oprettelsen af ressourcer i en hvilken som helst område. Han skal vide, hvor ressourcer, der er oprettet. 
- Hun er pågældende med omkostninger. Derfor ønsker hun at forhindre, at programmet ejere opretter unødvendigt dyr virtuelle computere.  
- Han vil mærke hver ressource med ejeren af business enhed og programmet, fordi dette program tjener udviklere i mange afdelinger. Ved hjælp af disse mærker, kan ETS faktureres de relevante teams.

Hun opretter følgende [ressourcestyring politikker](resource-manager-policy.md): 

| Felt | Effekt | Beskrivelse |
| ----- | ------ | ----------- |
| placering | overvågningslog | Overvåge oprettelse af ressourcer i et område |
| type | nægte | Nægte oprettelse af G-serien virtuelle maskiner |
| mærker | nægte | Kræv programmet ejer mærke |
| mærker | nægte | Kræv omkostninger center mærke |
| mærker | tilføje | Føje kodenavn **afdelingen** og tag værdi **ETS** til alle de ressourcer |


### <a name="resource-tags"></a>Ressource-mærker

Dave forstår, at hun skal have bestemte oplysninger på fakturaen til at identificere bæreren til BitBucket gennemførelse. Desuden vil Dave gerne kende alle de ressourcer, der ETS ejer. 

Han føjer følgende [mærker](resource-group-using-tags.md) til grupper og ressourcer. 
 
| Kodenavn | Tag værdi |
| -------- | --------- |
| ApplicationOwner | Navnet på den person, der administrerer dette program. |
| CostCenter | Den gruppe, der betaler for det Azure forbrug bærer. |
| Afdelingen | **ETS** (den afdeling, der er knyttet til abonnementet) |

### <a name="core-network"></a>Core netværk

Contoso ETS oplysninger sikkerhed og risikoen management teamet gennemser Daves foreslåede plan til at flytte programmet til Azure. De vil sikre dig, at programmet ikke er være tilgængelig på internettet.  Dave har også udvikler apps, der i fremtiden vil blive flyttet til Azure. Disse apps kræver offentlige grænseflader.  Hvis du vil opfylder kravene, indeholder han både interne og eksterne netværk virtual og sikkerhedsgruppe netværk til at begrænse adgangen.

Hun opretter følgende ressourcer:

| Ressourcetype | Navn | Beskrivelse |
| ------------- | ---- | ----------- |
| Virtuelt netværk | vnInternal | Bruges med programmet BitBucket og er forbundet via ExpressRoute til Contosos virksomhedens netværk.  Et undernet (sbBitBucket) indeholder programmet med et bestemt IP-adresseområde. |
| Virtuelt netværk | vnExternal | Tilgængelig for fremtidige programmer, der kræver offentligt slutpunkter. |
| Netværk sikkerhedsgruppe | nsgBitBucket | Sikrer, at minimeres angrebsoverfladen af denne arbejdsbelastningen ved at tillade forbindelser kun på port 443 for undernettet hvor programmet findes (sbBitBucket). |

### <a name="resource-locks"></a>Ressourcelåse 

Dave genkender, at forbindelsen fra Contosos firmanetværk til den interne virtuelle netværk skal være beskyttet fra en hvilken som helst wayward script eller utilsigtet sletning. 

Hun opretter følgende [ressource Lås](resource-group-lock-resources.md):

| Låsningstype | Ressource | Beskrivelse |
| --------- | -------- | ----------- |
| **CanNotDelete** | vnInternal | Brugerne kan ikke slette virtuelt netværk eller undernet, men forhindrer ikke tilføjelse af nye undernet. |

### <a name="azure-automation"></a>Azure-automatisering 

Dave har ikke noget til at automatisere for dette program. Selvom hun har oprettet en Azure automatiske konto, bruge ikke han først den. 

### <a name="azure-security-center"></a>Azure Security Center 

Styring af Contoso IT skal til hurtigt at identificere og håndtere trusler. De vil også at forstå, hvilke problemer kan findes.  

For at overholde disse krav, Dave aktiverer [Azure Sikkerhedscenter](./security-center/security-center-intro.md), og giver adgang til rollen Security Manager. 

## <a name="scenario-2-customer-facing-app"></a>Scenarie 2: med kundekontakt app

Business førerskab i levering kæde afdeling har identificeret forskellige muligheder for at øge aftale med Contosos kunder ved hjælp af en kortet. Alices team skal oprette dette program og beslutter, at Azure øger deres mulighed for at imødekomme forretningsmæssige behov. Bent fungerer med Dave fra ETS til at konfigurere to abonnementer til at udvikle og drift dette program.

### <a name="azure-subscriptions"></a>Azure-abonnementer 

Dave logger Azure Enterprise Portal og får vist, afdelingen levering kæde allerede findes.  Dog som dette projekt er det første udviklingsprojekt for levering kæde teamet i Azure, genkender Dave behovet for en ny konto til Alices udviklingsteam.  Hun opretter kontoen "R & D" for teamet og giver adgang til Bent. Bent logger på via portalen konto og opretter to abonnementer: et til at indeholde udvikling serverne og et til at holde fremstilling servere.  Hun følger de tidligere eksisterende naming standarderne, når du opretter følgende abonnementer: 

| Brug af abonnement | Navn |
| ---------------- | ---- |
| Udvikling | SupplyChain ResearchDevelopment LoyaltyCard udvikling |
| Fremstilling | SupplyChain handlinger LoyaltyCard fremstilling |

### <a name="policies"></a>Politikker

Dave og Bent diskutere programmet og identificere, at dette program kun fungerer kunder i Nordamerika område.  Bent og teamet du planlægger at bruge Azures program servicemiljø og Azure SQL til at oprette programmet. De kan skal bruge til at oprette virtuelle maskiner under udviklingen.  Bent ønsker at sikre, at hendes udviklere har de ressourcer, de skal bruge til at udforske og undersøge problemer uden at trække i ETS. 

**Udvikling abonnement**opretter de politikken følgende:

| Felt | Effekt | Beskrivelse |
| ----- | ------ | ----------- |
| placering | overvågningslog | Overvåg oprettelse af ressourcer i en hvilken som helst område. |

De begrænser ikke typen sku en bruger kan oprette i udvikling, og de kræver ikke mærker for en ressourcegrupper eller ressourcer.

**Fremstilling abonnement**opretter de følgende politikker:

| Felt | Effekt | Beskrivelse |
| ----- | ------ | ----------- |
| placering | nægte | Nægte oprettelse af en hvilken som helst ressourcer uden for USA datacentre. |
| mærker | nægte | Kræv programmet ejer mærke |
| mærker | nægte | Kræv afdeling mærke. | 
| mærker | tilføje | Føje mærker til hver ressourcegruppe, der angiver produktionsmiljø. |

De begrænser ikke typen sku en bruger kan oprette i fremstilling.

### <a name="resource-tags"></a>Ressource-mærker 

Dave forstår, at hun skal have specifikke oplysninger til at identificere de korrekte business grupper for fakturerings- og ejerskab. Han definerer ressource mærker for grupper og ressourcer. 
 
Kodenavn | Tag værdi |
| -------- | --------- |
| ApplicationOwner | Navnet på den person, der administrerer dette program. |
| Afdeling | Den gruppe, der betaler for det Azure forbrug bærer. |
| EnvironmentType | **Fremstilling** (Selvom abonnementet omfatter **fremstilling** i navnet, herunder dette mærke gør det muligt for nem identifikation når de kigger på ressourcer på portalen eller på faktura.) |

### <a name="core-networks"></a>Core netværk

Contoso ETS oplysninger sikkerhed og risikoen management teamet gennemser Daves foreslåede plan til at flytte programmet til Azure. De vil sikre dig, at programmet kortet er korrekt isolerede og beskyttet i et DMZ-netværk.  For at opfylde dette krav, oprette Dave og Bent en ekstern virtuelt netværk og sikkerhedsgruppe netværk til at isolere programmet kortet fra Contoso virksomhedens netværk.  

**Udvikling abonnement**opretter de:

| Ressourcetype | Navn | Beskrivelse |
| ------------- | ---- | ----------- |
| Virtuelt netværk | vnInternal | Fungerer udviklingsmiljø Contoso kortet og er forbundet via ExpressRoute til Contosos virksomhedens netværk. |

**Fremstilling abonnement**opretter de:

| Ressourcetype | Navn | Beskrivelse |
| ------------- | ---- | ----------- |
| Virtuelt netværk | vnExternal | Vært for kortet programmet og er ikke forbundet direkte til Contosos ExpressRoute. Kode rykkes via deres kildekode system direkte til PaaS tjenesterne. |
| Netværk sikkerhedsgruppe | nsgBitBucket | Sikrer, at angrebsoverfladen af denne arbejdsbelastningen minimeres ved kun at tillade indgående kommunikation på TCP 443.  Contoso også undersøger ved hjælp af en Firewall til webprogrammer til yderligere beskyttelse. |  

### <a name="resource-locks"></a>Ressourcelåse 

Dave og Bent giver og beslutter dig for at tilføje ressourcelåse på nogle af de vigtigste ressourcer i miljø for at forhindre utilsigtet sletning under en fejlbehæftede kode opslagsnål. 

De oprette følgende låsen:

| Låsningstype | Ressource | Beskrivelse |
| --------- | -------- | ----------- |
| **CanNotDelete** | vnExternal | For at forhindre folk i at slette det virtuelle netværk eller undernet. Låsen forhindrer ikke tilføjelse af nye undernet. |

### <a name="azure-automation"></a>Azure-automatisering 

Bent og udviklingsteamet har omfattende runbooks til at administrere miljø til dette program. Runbooks Tillad tilføjelse/sletning af noder til programmet og andre DevOps opgaver. 

Hvis du vil bruge disse runbooks, muliggør de [automatisering](./automation/automation-intro.md).

### <a name="azure-security-center"></a>Azure Security Center 

Styring af Contoso IT skal til hurtigt at identificere og håndtere trusler. De vil også at forstå, hvilke problemer kan findes.  

For at overholde disse krav, aktiverer Dave Azure Sikkerhedscenter. Han sikrer, at Sikkerhedscenter Azure overvåger ressourcerne, og giver adgang til DevOps og sikkerhed teams. 

## <a name="next-steps"></a>Næste trin

- Se [bedste fremgangsmåder til oprettelse af Azure ressourcestyring skabeloner](resource-manager-template-best-practices.md)for at få mere for at vide om oprettelse af Ressourcestyring skabeloner.

*[Karl Kuhnhausen](https://github.com/karlkuhnhausen) bidraget til dette emne.*
