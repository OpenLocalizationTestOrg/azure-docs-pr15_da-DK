<properties
   pageTitle="Implementering af Ressourcestyring og klassisk | Microsoft Azure"
   description="I denne artikel beskrives forskellene mellem implementeringsmodel ressourcestyring og klassiske (eller Service Management) implementeringsmodel."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/27/2016"
   ms.author="tomfitz"/>

# <a name="azure-resource-manager-vs-classic-deployment-understand-deployment-models-and-the-state-of-your-resources"></a>Azure ressourcestyring kontra klassisk installation: forstå installation-modeller og tilstanden for dine ressourcer

I dette emne, du få mere at vide om Azure ressourcestyring og klassisk installation modeller, tilstanden for dine ressourcer, og hvorfor ressourcerne blev installeret med ene eller den anden. Ressourcestyring og klassisk installation modeller repræsenterer to forskellige måder at implementere og administrere dine Azure løsninger. Du kan arbejde med dem via to forskellige API-sæt, og de installerede ressourcer kan indeholde vigtige forskelle. De to modeller er ikke helt kompatible med hinanden. Dette emne beskrives disse forskelle.

For at forenkle installation og administration af ressourcer, anbefales det, at du bruger Ressourcestyring for alle nye ressourcer. Microsoft anbefaler Hvis det er muligt, at du Geninstaller eksisterende ressourcer gennem ressourcestyring.

Hvis du ikke kender til Ressourcestyring, kan du først Gennemse den terminologi, der er defineret i [Azure ressourcestyring oversigt](azure-resource-manager/resource-group-overview.md).

## <a name="history-of-the-deployment-models"></a>Oversigt over installation modellerne

Azure angivet oprindeligt kun klassisk implementeringsmodel. I denne model fandtes hver ressource uafhængigt Der opstod ingen måde at gruppere relaterede ressourcer. I stedet skulle du manuelt registrere de ressourcer, der er foretaget af løsning eller program, og Husk at administrere dem i en koordineret tilgang. Du skal installere en løsning, skulle du enten oprette hver ressource individuelt via klassisk administrationsportalen eller oprette et script, der installeres alle ressourcerne i den rigtige rækkefølge. Hvis du vil slette en løsning, skulle du slette hver ressource enkeltvis. Du kan ikke nemt kan anvende og opdatere politik for adgangskontrol til relaterede ressourcer. Til sidst skal du kunne ikke anvende mærker til ressourcer til at markere dem med ord, der hjælper dig med at overvåge dine ressourcer og administrere fakturering.

I 2014 introduceret Azure Ressourcestyring, som tilføjes som repræsenterer en ressourcegruppe. En ressourcegruppe er en beholder for de ressourcer, der deler en fælles livscyklus. Ressourcestyring implementeringsmodel giver adskillige fordele:

- Du kan installere, administrere og overvåge alle tjenester til din løsning som en gruppe, i stedet for håndtering af disse tjenester enkeltvis.
- Du kan gange installere din løsning i hele dets livscyklus og har tillid til dine ressourcer er installeret på en konsistent tilstand.
- Du kan anvende kontrol af adgang til alle ressourcer i din ressourcegruppe, og disse politikker anvendes automatisk, når nye ressourcer føjes til ressourcegruppen.
- Du kan anvende mærker til ressourcer til logisk organisere alle ressourcerne i dit abonnement.
- Du kan bruge JavaScript Object Notation (JSON) til at definere infrastrukturen for din løsning. Filen JSON kaldes en ressourcestyring skabelon.
- Du kan definere afhængigheder mellem ressourcer, så de er implementeret i den rigtige rækkefølge.

Når ressourceleder, der blev tilføjet, er alle de ressourcer tilbagevirkende føjet til standardgrupper ressource. Hvis du opretter en ressource gennem klassisk installation nu, oprettes der automatisk ressourcen inden for en ressource standardgruppe for den pågældende tjeneste, selvom du ikke angav ressourcegruppen på installation. Dog betyder eksisterende lige inden for en ressourcegruppe ikke, at ressourcen, der er blevet konverteret til Ressourcestyring modellen. Vi vil se på hvordan hver tjeneste håndterer to installation modellerne i næste afsnit. 

## <a name="understand-support-for-the-models"></a>Forstå understøttelse af modellerne 

Når du beslutter, hvilken installation der skal bruges til dine ressourcer, er der tre scenarier, der skal være opmærksom på:

1. Tjenesten understøtter ressourcestyring og indeholder kun en enkelt type.
2. Tjenesten understøtter Ressourcestyring, men indeholder to typer - én for ressourcestyring og én til klassisk. Dette scenarie gælder kun for virtuelle maskiner, lager konti og virtuelle netværk.
3. Tjenesten understøtter ikke ressourcestyring.

For at se, om en tjeneste understøtter Ressourcestyring, se [ressourcestyring understøttet udbydere](resource-manager-supported-services.md).

Hvis du vil bruge tjenesten ikke understøtter Ressourcestyring, skal du fortsætte med at bruge klassisk installation.

Hvis tjenesten understøtter ressourcestyring og **er ikke** en virtuel maskine, lagerplads konto eller virtuelt netværk, kan du bruge ressourcestyring uden en hvilken som helst komplikationer.

Til virtuelle maskiner, lager konti og virtuelle netværk, hvis ressourcen blev oprettet via klassisk installation, skal du fortsætte med til at betjene på den gennem klassisk handlinger. Hvis den virtuelle maskine, lagerplads konto eller virtuelt netværk blev oprettet via ressourcestyring installation, skal du fortsætte med at bruge ressourcestyring handlinger. Denne skelnen kan få overskuelig, når dit abonnement indeholder en blanding af ressourcer, der er oprettet ved hjælp af Ressourcestyring og klassisk installation. Denne kombination af ressourcer kan oprette uventede resultater, fordi ressourcerne, der ikke understøtter de samme handlinger.

I nogle tilfælde kan en ressourcestyring kommando kan hente oplysninger om en ressource, der er oprettet via klassisk installation eller kan udføre en administrative opgaver såsom at flytte en klassisk ressource til en anden ressourcegruppe. Men disse tilfælde skal ikke give det indtryk, at typen understøtter ressourcestyring handlinger. Lad os antage, at du har en ressourcegruppe, der indeholder en virtuel maskine, der er oprettet med klassisk installation. Hvis du køre følgende kommando for ressourcestyring PowerShell:

    Get-AzureRmResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines

Returnerer den den virtuelle maskine:
    
    Name              : ExampleClassicVM
    ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
    ResourceName      : ExampleClassicVM
    ResourceType      : Microsoft.ClassicCompute/virtualMachines
    ResourceGroupName : ExampleGroup
    Location          : westus
    SubscriptionId    : {guid}

Ressourcestyring cmdlet **Get-AzureRmVM** returnerer dog kun virtuelle maskiner installeres via ressourcestyring. Følgende kommando returnerer ikke den virtuelle maskine, der er oprettet via klassisk installation.

    Get-AzureRmVM -ResourceGroupName ExampleGroup

Kun ressourcer, der er oprettet ved hjælp af Ressourcestyring support mærker. Du kan ikke anvende mærker til klassisk ressourcer.

## <a name="resource-manager-characteristics"></a>Ressourcestyring egenskaber

For at hjælpe dig med at forstå de to modeller, Lad os gennemgå ressourcestyring egenskaber:

- Oprettet via [Azure-portalen](https://portal.azure.com/).

     ![Azure-portalen](./media/resource-manager-deployment-model/portal.png)

     Beregn, lager og netværk ressourcer, har du mulighed for at bruge enten ressourcestyring eller klassisk installation. Vælg **Ressourcestyring**.

     ![Ressourcestyring installation](./media/resource-manager-deployment-model/select-resource-manager.png)

- Oprettet med ressourcestyring versionen af Azure PowerShell-cmdlet'er. Disse kommandoer har formatet *Verber AzureRmNoun*.

        New-AzureRmResourceGroupDeployment

- Oprettet via [Azure ressourcestyring REST-API](https://msdn.microsoft.com/library/azure/dn790568.aspx) til RESTEN handlinger.

- Oprettet via Azure CLI-kommandoer, der kører i tilstanden **arm** .

        azure config mode arm
        azure group deployment create 

- Ressourcetypen omfatter ikke **(klassisk)** i navnet. Følgende billede viser typen som **lager konto**.

    ![WebApp](./media/resource-manager-deployment-model/resource-manager-type.png)

## <a name="classic-deployment-characteristics"></a>Klassisk installation egenskaber

Du kan også kende den klassiske implementeringsmodel som modellen, Service Management.

Ressourcer, der er oprettet i den klassiske implementeringsmodel dele følgende egenskaber:

- Oprettet via [Klassisk portal](https://manage.windowsazure.com)

     ![Klassisk portal](./media/resource-manager-deployment-model/classic-portal.png)

     Eller på Azure-portalen, og du angive **Klassisk** installation (for Beregn, Storage og netværk).

     ![Klassisk installation](./media/resource-manager-deployment-model/select-classic.png)

- Oprettet via Service Management-version af Azure PowerShell-cmdlet'er. Disse navne på kommandoer har formatet *Verber AzureNoun*.

        New-AzureVM 

- Oprettet via [Service Management REST-API](https://msdn.microsoft.com/library/azure/ee460799.aspx) til RESTEN handlinger.
- Oprettet via Azure CLI-kommandoer køre i **asm** tilstand.

        azure config mode asm
        azure vm create 

- Ressourcetypen omfatter **(klassisk)** i navnet. Følgende billede viser typen som **lager konto (klassisk)**.

    ![Klassisk type](./media/resource-manager-deployment-model/classic-type.png)

Du kan bruge portalen Azure til at administrere ressourcer, der er oprettet via klassisk implementering.

## <a name="changes-for-compute-network-and-storage"></a>Ændringer for Beregn-, Netværks- og lagerplads

I det følgende diagram viser Beregn-, Netværks- og lagerplads ressourcer, der er installeret gennem ressourcestyring.

![Ressourcestyring arkitektur](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)

Bemærk følgende relationerne mellem ressourcerne:

- Alle ressourcerne, der findes i en ressourcegruppe.
- Den virtuelle maskine, afhænger af en bestemt lagerplads-konto, der er defineret i Storage ressource provider til at gemme dens diske i blob-lager (påkrævet).
- Den virtuelle maskine refererer til en bestemt NIC, der er defineret i ressource udbyderen af det netværk (påkrævet) og en tilgængelighed, angive defineret i provideren Beregn ressource (valgfrit).
- NIC refererer til den virtuelle maskine tildelt IP-adresse (påkrævet), undernet i det virtuelle netværk til den virtuelle maskine (påkrævet), og til en netværk sikkerhedsgruppe (valgfrit).
- Undernet i et virtuelt netværk referencer sikkerhedsgruppe netværk (valgfrit).
- Indlæse belastningsjusteringstjenesten forekomst refererer til back end-puljen af IP-adresser, som indeholder NIC af en virtuel maskine (valgfrit) og refererer til en Indlæs belastningsjusteringstjenesten offentlig eller privat IP-adresse (valgfrit).

Her er komponenterne og deres relationer til klassisk installation:

![Klassisk arkitektur](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)

Klassisk løsningen som vært for en virtuel maskine omfatter:

- En påkrævet skybaseret tjeneste, der fungerer som en objektbeholder til at være vært virtuelle maskiner (Beregn). Virtuelle maskiner der automatisk følger med et netværkskort (NIC) og har fået tildelt en IP-adresse ved Azure. Desuden indeholder skytjenesten en ekstern Indlæs belastningsjusteringstjenesten forekomst, en offentlig IP-adresse og standard slutpunkter til at tillade remote Fjernskrivebord og PowerShell bliver trafik til Windows-baserede virtuelle maskiner og Secure Shell (SSH) trafik for Linux-baserede virtuelle computere.
- En påkrævet lagerplads-konto, der gemmer de virtuelle harddiske for en virtuel maskine, herunder operativsystemet, midlertidige og yderligere datadisce (storage).
- Et valgfrit virtuelt netværk, der fungerer som en yderligere beholder, hvori du kan oprette en undernetværket struktur og angive det undernet, hvor den virtuelle maskine er placeret (network).

I følgende tabel beskrives ændringer i Beregn-, Netværks- og lagerplads ressource udbydere arbejde:

 Element | Klassisk | Ressourcestyring
 ---|---|---
| Skybaseret tjeneste til virtuelle maskiner |  Skybaseret tjeneste er en objektbeholder til lagring af de virtuelle maskiner, kræves tilgængelighed fra platform og justering af belastning. | Skybaseret tjeneste er ikke længere et objekt, der kræves for at oprette en virtuel maskine, ved hjælp af den nye model. |
| Virtuelt netværk | Et virtuelt netværk er valgfrit for den virtuelle maskine. Hvis medtages, kan det virtuelle netværk ikke installeres med ressourcestyring. | Virtuelt kræver et virtuelt netværk, der er installeret med ressourcestyring. |
| Lagerplads konti | Den virtuelle maskine kræver en lagerplads-konto, der gemmer de virtuelle harddiske for operativsystem, midlertidige og yderligere datadisce. | Den virtuelle maskine kræver en lagerplads konto til at gemme dens diske i blob-lager. |
| Tilgængelighed sæt | Tilgængelighed til platformen blev angivet ved at konfigurere den samme "AvailabilitySetName" på de virtuelle computere. Det maksimale antal fejl domæner var 2. | Tilgængelighed er en ressource, der vises af Microsoft.Compute udbyder. Virtuelle maskiner, der kræver høj tilgængelighed skal medtages i det tilgængelighed sæt. Det maksimale antal fejl domæner er nu 3. |
| Forbindelse grupper | Forbindelse grupper er påkrævet for at oprette virtuelle netværk. Dog med Introduktion af internationale virtuelle netværk, der er ikke påkrævet længere. |For at forenkle, findes som repræsenterer forbindelsen mellem processorer grupper ikke i API'er vises via Azure ressourcestyring. |
| Justering af belastning    | Oprettelse af en skybaseret tjeneste indeholder et implicit justering af belastning til de virtuelle maskiner, der er installeret. | Den justering af belastning er en ressource, der vises af provideren Microsoft.Network. Grænsefladen primære netværk for de virtuelle maskiner, der skal være Indlæs afstemmes, skal være henviser til justering af belastning. Indlæse balancere kan være interne eller eksterne. En forekomst af belastning belastningsjusteringstjenesten refererer til back end-puljen af IP-adresser, som indeholder NIC af en virtuel maskine (valgfrit) og refererer til en Indlæs belastningsjusteringstjenesten offentlig eller privat IP-adresse (valgfrit). [Få mere at vide.](../articles/resource-groups-networking.md) |
|Virtuelle IP-adresse | Cloud Services får en standard-VIP (virtuelle IP-adresse), når en VM er føjet til en skybaseret tjeneste. Den virtuelle IP-adresse er den adresse, der er knyttet til implicit justering af belastning.    | Offentlige IP-adresse er en ressource, der vises af Microsoft.Network-udbyder. Offentlige IP-adresse kan være statisk (reserveret) eller dynamisk. Dynamisk offentlige IP'er kan tildeles til en justering af belastning. Offentlige IP'er kan være fastgjort ved hjælp af sikkerhedsgrupper. |
|Reserveret IP-adresse|   Du kan reservere en IP-adresse i Azure og knytte den til en skybaseret tjeneste til at sikre, at den IP-adresse er sticky.   | Offentlige IP-adresse kan oprettes i "Statisk" tilstand, og det giver adgang til samme egenskab som en "reserverede IP-adresse". Statisk offentlige IP'er kan kun tildeles til en belastningsjustering lige nu. |
|Offentlige IP-adresse (PIP) per VM | Offentlige IP-adresser kan også være knyttet til en VM direkte. | Offentlige IP-adresse er en ressource, der vises af Microsoft.Network-udbyder. Offentlige IP-adresse kan være statisk (reserveret) eller dynamisk. Kun dynamiske offentlige IP'er kan dog tildeles til en netværksgrænseflade at få en offentlige IP-adresse per VM lige nu. |
|Slutpunkter| Indtast slutpunkter bruges til at være konfigureret på en virtuel maskine være åbne op connectivity for bestemte porte. En af de almindelige måder at oprette forbindelse til virtuelle maskiner færdig ved at konfigurere input slutpunkter. | Indgående NAT regler kan konfigureres på Indlæs balancere at opnå den samme egenskab for aktivering af slutpunkterne på bestemte porte til at oprette forbindelse til FOS. |
|DNS-navn| En skybaseret tjeneste ville få et implicit globalt entydige DNS-navn. For eksempel: `mycoffeeshop.cloudapp.net`. | DNS-navne er valgfrie parametre, der kan angives på en offentlig IP-adresse ressource. Det fulde Domænenavn er i følgende format - `<domainlabel>.<region>.cloudapp.azure.com`. |
|Netværksgrænseflader | Primære og sekundære netværkskort og dens egenskaber er defineret som netværkskonfiguration af en virtuel maskine. | Netværkskort er en ressource, der vises af Microsoft.Network udbyder. Livscyklus for grænsefladen netværket ikke er bundet til et virtuelt. Den refererer til den virtuelle maskine tildelt IP-adresse (påkrævet), undernet i det virtuelle netværk til den virtuelle maskine (påkrævet), og til en netværk sikkerhedsgruppe (valgfrit). |

For at få mere for at vide om at oprette forbindelse virtuelle netværk fra forskellige installation modeller, kan du se [forbinde virtuelle netværk fra forskellige installation modeller på portalen](./vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="migrate-from-classic-to-resource-manager"></a>Overføre fra klassisk til Ressourcestyring

Hvis du er klar til at overføre dine ressourcer fra klassisk installation til Ressourcestyring installation, skal du se:

1. [Tekniske undersøgelse på platform understøttes overførsel fra klassisk til Azure ressourcestyring](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
2. [Platform understøttes overførsel af IaaS ressourcer fra klassisk til Azure ressourcestyring](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
3. [Overføre IaaS ressourcer fra klassisk til Azure ressourcestyring ved hjælp af Azure PowerShell](./virtual-machines/virtual-machines-windows-ps-migration-classic-resource-manager.md)
4. [Overføre IaaS ressourcer fra klassisk til Azure ressourcestyring ved hjælp af Azure CLI](./virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md)

## <a name="frequently-asked-questions"></a>Ofte stillede spørgsmål

**Kan jeg oprette en virtuel maskine, der bruger Azure ressourcestyring til at implementere i et virtuelt netværk eller lagerplads konto, der er oprettet ved hjælp af klassisk installation?**

Dette understøttes ikke. Du kan ikke bruge Azure ressourcestyring til at installere en virtuel maskine i et virtuelt netværk, der blev oprettet ved hjælp af klassisk installation.

**Kan jeg oprette en virtuel maskine, ved hjælp af Ressourcestyring Azure fra en bruger billede, der blev oprettet ved hjælp af Azure Service Management API'er?**

Dette understøttes ikke. Du kan dog kopiere Virtuelle filer fra en lagerplads-konto, der blev oprettet ved hjælp af Service Management API'er og føje dem til en ny konto, der er oprettet via Azure ressourcestyring.

**Hvad er påvirkningen af kvoten for mit abonnement?**

Kvoter for virtuelle maskiner, virtuelle netværk og lagerplads konti, der er oprettet via Azure ressourcestyring er adskilt fra andre kvoter. Hvert abonnement bliver kvoter for at oprette de ressourcer, ved hjælp af de nye API'er. Du kan læse mere om de ekstra kvoter [her](../articles/azure-subscription-service-limits.md).

**Kan jeg fortsætte med at bruge mit automatiseret scripts til klargøring af virtuelle maskiner, virtuelt netværk og storage-konti via ressourcestyring API'er?**

Alle automatisering og scripts, som du tidligere har opbygget fortsætte med at arbejde til de eksisterende virtuelle maskiner, virtuelt Network oprettes under Azure Service Management-tilstand. Scriptene har dog skal opdateres, hvis du vil bruge det nye skema til oprettelse af de samme ressourcer via ressourcestyring tilstand.

**Hvor kan jeg finde eksempler på Azure ressourcestyring skabeloner?**

Et omfattende sæt startskabeloner kan findes på [Azure ressourcestyring Hurtig start skabeloner](https://azure.microsoft.com/documentation/templates/).

## <a name="next-steps"></a>Næste trin

- Hvis du vil gennemgå oprettelse af skabelon, der definerer en virtuel maskine, se lagerplads konto og virtuelt netværk og [ressourcestyring skabelon gennemgang](resource-manager-template-walkthrough.md).
- For at se kommandoerne til implementering af en skabelon skal du se [Implementer et program med Azure ressourcestyring skabelon](resource-group-template-deploy.md).
