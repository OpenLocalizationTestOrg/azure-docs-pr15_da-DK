<properties 
   pageTitle="Sådan overføres fra forbindelse grupper til et internationale virtuelt netværk (VNet)"
   description="Lær at overføre fra forbindelse grupper til internationale vnets"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="how-to-migrate-from-affinity-groups-to-a-regional-virtual-network-vnet"></a>Sådan overføres fra forbindelse grupper til et internationale virtuelt netværk (VNet)

Du kan bruge en forbindelse gruppe til at sikre, at ressourcer, der er oprettet i samme forbindelse gruppen fysisk er på servere, der er tæt på hinanden, så disse ressourcer til at kommunikere hurtigere. I tidligere var forbindelse grupper et krav til at oprette virtuelle netværk (VNets). På tidspunkt, kan tjenesten netværk manager, administreres VNets kun arbejde i et sæt af fysiske servere eller enhed for tidsskala. Arkitektonisk forbedringer har øget omfanget af netværk management til et område.

Som et resultat af disse arkitektonisk forbedringer forbindelse grupper er ikke længere anbefales eller kræves til virtuelle netværk. Brug af forbindelsen mellem processorer grupper for VNets erstattes af områder. VNets, der er knyttet til områder kaldes internationale VNets.

Derudover anbefaler vi, at du ikke bruger forbindelse grupper Generelt. Ud over kravet VNet blev forbindelse grupper også vigtigt, at du kan bruge til at sikre, at ressourcer, som Beregn og lagerplads, er placeret tæt på hinanden. Dog med den aktuelle Azure netværksarkitektur er kravene placeringen ikke længere det er nødvendigt. Se [forbindelse grupper og FOS](#Affinity-groups-and-VMs) for de par resterende særlige tilfælde, hvor du vil muligvis bruge en forbindelse gruppe.

## <a name="creating-and-migrating-to-regional-vnets"></a>Oprette og overføre til internationale VNets

Gå frem, når du opretter nye VNets, bruge *område*. Du får vist som en valgmulighed på portalen administration. Bemærk, at i konfigurationsfil netværk dette viser som *placering*.

>[AZURE.IMPORTANT] Selvom det er stadig teknisk muligt at oprette et virtuelt netværk, der er knyttet til en gruppe af forbindelsen mellem processorer, er der ingen gode grunde til at gøre dette. Mange nye funktioner, som netværk sikkerhedsgrupper er kun tilgængelige, når du bruger en internationale VNet og er ikke tilgængelige for virtuelle netværk, der er knyttet til forbindelsen mellem processorer grupper.

### <a name="about-vnets-currently-associated-with-affinity-groups"></a>Om VNets i øjeblikket er knyttet til forbindelsen mellem processorer grupper

VNets, der er knyttet til forbindelsen mellem processorer grupper i øjeblikket er aktiveret for overførsel til internationale VNets. For at overføre til en internationale VNet, skal du følge disse trin:

1. Eksportere konfigurationsfil netværk. Du kan bruge PowerShell eller Management-portalen. Du kan finde oplysninger ved hjælp af portalen Management, [Konfigurer din VNet ved hjælp af et netværk konfigurationsfil](virtual-networks-using-network-configuration-file.md).

1. Redigere dit netværk konfigurationsfil, erstatter de gamle værdier med de nye værdier. 

    > [AZURE.NOTE] **Placering** er det område, du har angivet for gruppen forbindelse, der er knyttet til din VNet. Eksempelvis hvis din VNet er knyttet til en gruppe af forbindelsen mellem processorer, der er placeret i Vest USA, når du overfører, skal din placering pege på Vest USA. 
    
    Redigere efterfølgende linjer i dit netværk konfigurationsfil erstatter værdierne med dine egne: 

    **Gamle værdi:** \<VirtualNetworkSitename = "VNetUSWest" AffinityGroup = "VNetDemoAG"\> 

    **Ny værdi:** \<VirtualNetworkSitename = "VNetUSWest" placering = "Vest US"\>

1. Gemme dine ændringer og [importere](virtual-networks-using-network-configuration-file.md) netværkskonfigurationen til Azure.

>[AZURE.NOTE] Denne overførsel forårsager ikke en hvilken som helst nedetid til dine tjenester.

## <a name="affinity-groups-and-vms"></a>Forbindelse grupper og FOS

Som tidligere nævnt kan forbindelse grupper ikke længere generelt anbefales til FOS. Kun, når et sæt af FOS skal have den absolutte lavest netværksventetid mellem FOS, skal du bruge en forbindelse gruppe. Ved at placere FOS i en forbindelse gruppe, placeres FOS alle i den samme enhed, Beregn klynge eller skala.

Det er vigtigt at være opmærksom på, ved hjælp af en forbindelse gruppe kan have to, muligvis negative konsekvenser:

- Sættet af VM størrelser vil være begrænset til sæt af VM størrelser tilbydes Beregn skala måleenhed.

- Der findes en større sandsynlighed for at tildele en ny VM. Dette sker, når enheden bestemt målestoksforhold for gruppen forbindelse er uden for kapacitet.

### <a name="what-to-do-if-you-have-a-vm-in-an-affinity-group"></a>Hvad du skal gøre, hvis du har en VM i en gruppe af forbindelse

VM'er, der aktuelt findes i en gruppe af forbindelsen mellem processorer behøver ikke at blive fjernet fra gruppen forbindelse.

Når en VM er installeret, installeres den til en enkelt skala enhed. Forbindelse grupper kan begrænse sæt af tilgængelige VM størrelser til en ny VM-installation, men eventuelle eksisterende VM, der er implementeret er allerede begrænset til de tilgængelige i den enhed for tidsskala VM installeres VM størrelser. På grund af dette har Hvis du fjerner en VM fra gruppen forbindelse ingen virkning.
 
