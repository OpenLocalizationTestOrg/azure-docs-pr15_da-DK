<properties
   pageTitle="Hvad er et netværk listen ACL (Access Control)?"
   description="Få mere at vide om ACLs"
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

# <a name="what-is-an-endpoint-access-control-list-acls"></a>Hvad er et slutpunkt Access Control List (ACLs)?

Et slutpunkt listen ACL (Access Control) er en tilgængelig for din installation af Azure sikkerhedsforbedring. En ACL giver mulighed for at tillade eller nægte trafik for et virtuelt slutpunkt enkeltvis. Filterfunktion denne pakken indeholder en ekstra sikkerhedslag. Du kan angive netværk ACLs for slutpunkter kun. Du kan ikke angive en ACL for et virtuelt netværk eller et bestemt undernet, der er indeholdt i et virtuelt netværk.

> [AZURE.IMPORTANT] Det anbefales at bruge netværk sikkerhedsgrupper (NSGs) i stedet for ACLs, når det er muligt. Du kan få mere at vide om NSGs, [Hvad er en sikkerhedsgruppe netværk?](virtual-networks-nsg.md).

ACLs kan konfigureres ved hjælp af PowerShell eller Management-portalen. For at konfigurere et netværk ACL ved hjælp af PowerShell skal du se [Administrere adgangskontrollister (ACLs) for slutpunkter ved hjælp af PowerShell](virtual-networks-acl-powershell.md). For at konfigurere et netværk ACL ved hjælp af portalen administration skal du se, [hvordan du angive slutpunkter til en virtuel maskine](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).

Ved hjælp af netværk ACLs, kan du gøre følgende:

- Tillade eller nægte indgående trafik baseret på remote undernet IPv4-adresseområde til et virtuelt input slutpunkt enkeltvis.

- Blacklist IP-adresser

- Opret flere regler per virtuelt slutpunkt

- Angive op til 50 ACL regler per virtuelt slutpunkt

- Brug regel ordning at sikre, at det korrekte sæt af regler anvendes på en given virtuelt slutpunkt (laveste til højeste)

- Angiv en ACL for et bestemt remote undernet IPv4-adresse.

## <a name="how-acls-work"></a>Hvordan fungerer ACLs

En ACL er et objekt, der indeholder en liste over regler. Når du opretter en ACL og anvende den på et virtuelt slutpunkt, foregår pakkefiltrering på noden vært for din VM. Det betyder trafik fra eksterne IP-adresser er filtreret efter noden host for tilsvarende ACL regler i stedet for på din VM. Dette forhindrer, at din VM bruger værdifulde CPU-skifter på filtrering af pakker.

Når der oprettes en virtuel maskine, indsættes standard ACL sted at blokere al indgående trafik. Men hvis der oprettes et slutpunkt for (port 3389), derefter standard ACL ændres, så alle indgående trafik til det pågældende slutpunkt. Indgående trafik fra en hvilken som helst remote undernet derefter har tilladelse til at pågældende slutpunkt og ingen firewall klargøring er påkrævet. Alle andre porte blokeres for indgående trafik, medmindre der oprettes slutpunkter for disse porte. Udgående trafik er tilladt som standard.

**Standard ACL eksempeltabel**

| **Regel nr.** | **Remote undernet** | **Første eller sidste ark** | **Tillad/Afvis** |
|--------|---------------|----------|-------------|
| 100    | 0.0.0.0/0     | 3389     | Tillade      |

## <a name="permit-and-deny"></a>Tillade og nægte

Du kan selektivt tillade eller nægte netværkstrafik for et virtuelt input slutpunkt ved at oprette regler, der angiver "tillade" eller "nægte". Det er vigtigt at være opmærksom på, som standard, når der oprettes et slutpunkt, al trafik er tilladt til slutpunktet. Derfor er det vigtigt at forstå, hvordan du opretter tilladelse/nægte regler og sætte dem i den rigtige rækkefølge, hvis du vil have detaljerede kontrol over den netværkstrafik, som du vælger at tillade når virtuelt slutpunkt.

Punkter du bør overveje:

1. **Ingen ACL-** Når der oprettes et slutpunkt, som standard tilladelse vi alle for slutpunktet.

1. **Tillade-** Når du tilføjer en eller flere "tillade" områder, afviser du alle andre områder som standard. Kun pakker fra det tilladte IP-område kan kommunikere med det virtuelle maskine slutpunkt.

1. **Nægte-** Når du tilføjer en eller flere "nægte" områder, du giver tilladelse til andre områder af trafik som standard.

1. **Kombination af tillade og nægte-** Du kan bruge en kombination af "tillade" og "nægte", når du vil skærer ud af en bestemt IP-interval tilladt eller nægtet.

## <a name="rules-and-rule-precedence"></a>Regler og rangorden af regler for

Netværk ACLs kan konfigureres på bestemte virtuelt slutpunkter. For eksempel kan du angive et netværk ACL til et RDP slutpunkt, der er oprettet på en virtuel maskine, hvilke låse ned adgang for visse IP-adresser. Tabellen nedenfor viser en metode til at give adgang til offentlige virtuelle IP'er (VIPs) i et bestemt område til at tillade adgang til RDP. Alle andre remote IP'er afvises. Vi følger en *laveste prioriteres* regel rækkefølge.

### <a name="multiple-rules"></a>Flere regler

I eksemplet nedenfor, hvis du vil give adgang til RDP slutpunktet kun fra to offentlige IPv4-adresseområder (65.0.0.0/8 og 159.0.0.0/8), kan du opnå dette ved at angive to *tillade* regler. I dette tilfælde, da RDP oprettes som standard for en virtuel maskine, kan du låse ned adgang til RDP porten baseret på et remote undernet. Eksemplet herunder viser en metode til at give adgang til offentlige virtuelle IP'er (VIPs) i et bestemt område til at tillade adgang til RDP. Alle andre remote IP'er afvises. Dette virker, fordi netværk ACLs kan konfigureres for et bestemt virtuelt slutpunkt og adgang nægtet som standard.

**Eksempel – flere regler**

| **Regel nr.** | **Remote undernet** | **Første eller sidste ark** | **Tillad/Afvis** |
|--------|---------------|----------|-------------|
| 100    | 65.0.0.0/8    | 3389     | Tillade      |
| 200    | 159.0.0.0/8   | 3389     | Tillade      |

### <a name="rule-order"></a>Regel rækkefølge

Da flere regler kan være angivet til et slutpunkt, skal der være en metode til at organisere regler for at finde ud af, hvilken regel har højere prioritet. Regelrækkefølgen angiver rangorden for. Netværk ACLs følger en *laveste prioriteres* regel rækkefølge. I eksemplet nedenfor er slutpunktet på port 80 selektivt tildelt adgang til kun visse IP-adresseområder. Hvis du vil konfigurere, vi har en regel for Afvis (regel \# 100) for adresser i 175.1.0.1/24 område. En anden regel angives derefter med rangorden for 200, der giver adgang til alle andre adresser under 175.0.0.0/8.

**Eksempel – rangorden af regler for**

| **Regel nr.** | **Remote undernet** | **Første eller sidste ark** | **Tillad/Afvis** |
|--------|---------------|----------|-------------|
| 100    | 175.1.0.1/24  | 80       | Nægte        |
| 200    | 175.0.0.0/8   | 80       | Tillade      |

## <a name="network-acls-and-load-balanced-sets"></a>Netværk ACLs og indlæse ikke-opgjorte sæt

Netværk ACLs kan angives på et Indlæs afstemmes sæt (kg angivet) slutpunkt. Hvis en ACL er angivet for kg sæt, anvendes netværk ACL alle virtuelle maskiner i pågældende kg sæt. Eksempelvis hvis kg sæt er oprettet med "Port 80", og det kg sæt indeholder 3 FOS, oprettet netværk ACL på slutpunkt "Port 80" på et VM anvender automatisk til de andre FOS.

![Netværk ACLs og indlæse ikke-opgjorte sæt](./media/virtual-networks-acl/IC674733.png)

## <a name="next-steps"></a>Næste trin

[Sådan administreres adgangskontrollister (ACLs) for slutpunkter ved hjælp af PowerShell](virtual-networks-acl-powershell.md)
