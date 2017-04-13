<properties
    pageTitle="Azure AD-domænetjenester: Netværk retningslinjer | Microsoft Azure"
    description="Azure Active Directory Domain Services netværk overvejelser i forbindelse med"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="maheshu"/>

# <a name="networking-considerations-for-azure-ad-domain-services"></a>Azure AD-domænetjenester netværk overvejelser i forbindelse med

## <a name="how-to-select-an-azure-virtual-network"></a>Sådan markeres en Azure virtuelt netværk
Følgende retningslinjer hjælpe dig med at vælge et virtuelt netværk til brug med Azure AD-domænetjenester.

### <a name="type-of-azure-virtual-network"></a>Type Azure virtuelt netværk

- Du kan aktivere Azure AD-domænetjenester i et klassisk Azure virtuelt netværk.

- Azure AD-domænetjenester **kan ikke være aktiveret i virtuelle netværk, der er oprettet ved hjælp af Azure ressourcestyring**.

- Du kan oprette forbindelse et ressourcestyring-baserede virtuelt netværk til en klassisk virtuelt netværk, hvor Azure AD Domain Services er aktiveret. Du kan derefter bruge Azure AD-domænetjenester i ressourcestyring-baserede virtuelle netværket. Yderligere oplysninger finder du i afsnittet [netværksforbindelse](active-directory-ds-networking.md#network-connectivity) .

- **Internationale virtuelle netværk**: Hvis du planlægger at bruge en eksisterende virtuelt netværk, kan du sikre dig, at det er et internationale virtuelt netværk.

    - Virtuelt netværk, der bruger ældre forbindelse grupper-funktion kan ikke bruges med Azure AD-domænetjenester.

    - Bruge Azure AD-domænetjenester, [overføre ældre virtuelle netværk med internationale virtuelle netværk](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).


### <a name="azure-region-for-the-virtual-network"></a>Azure område for det virtuelle netværk

- Din Azure AD-domænetjenester, administrerede domæne er implementeret i det samme Azure område som det virtuelle netværk, du vælger at aktivere tjenesten i.

- Vælg et virtuelt netværk i en Azure område, der understøttes af Azure AD-domænetjenester.

- Se siden [Azure tjenester efter område](https://azure.microsoft.com/regions/#services/) at kende områderne Azure, Azure AD-domænetjenester er tilgængelig.


### <a name="requirements-for-the-virtual-network"></a>Krav til det virtuelle netværk

- **Afstand til din Azure arbejdsbelastninger**: Vælg det virtuelle netværk, der i øjeblikket vært/er vært for virtuelle maskiner, der skal have adgang til Azure AD-domænetjenester.

- **Brugerdefineret/sætter-dine-ejer DNS-servere**: Sørg for, at der er ingen brugerdefinerede DNS-servere, der er konfigureret til det virtuelle netværk.

- **Eksisterende domæner med det samme domænenavn**: Sørg for, at du ikke har et eksisterende domæne med det samme domænenavn, der er tilgængelige på det pågældende virtuelle netværk. For eksempel Antag, at du har et domæne kaldet 'contoso.com' allerede findes i den valgte virtuelt netværk. Senere, forsøger du at aktivere en Azure AD-domænetjenester administrerede domæne med det samme domænenavn (som er 'contoso.com') på det pågældende virtuelle netværk. Der opstår en fejl, når du forsøger at aktivere Azure AD-domænetjenester. Denne fejl er på grund af navnekonflikter for domænenavnet på det pågældende virtuelle netværk. I dette tilfælde skal du bruge et andet navn til at konfigurere domænet Azure AD-domænetjenester administrerede. Du kan også deaktivere klargøre det eksisterende domæne og derefter fortsætte med at aktivere Azure AD-domænetjenester.

> [AZURE.WARNING] Du kan ikke flytte Domain Services til et andet virtuelle netværk, når du har aktiveret tjenesten.


## <a name="network-security-groups-and-subnet-design"></a>Netværk sikkerhedsgrupper og undernet design
Et [Netværk sikkerhed gruppe (NSG)](../virtual-network/virtual-networks-nsg.md) indeholder en liste over listen ACL (Access Control) regler, der tillade eller nægte netværkstrafik til din VM forekomster i et virtuelt netværk. NSGs kan være knyttet til undernet eller enkelte VM forekomster i pågældende undernet. Når en NSG er knyttet til et undernet, regler ACL til alle VM forekomsterne i pågældende undernet. Desuden trafik til en enkelt VM kan være begrænset yderligere ved at knytte en NSG direkte til VM.

![Anbefalede undernet design](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)


### <a name="best-practices-for-choosing-a-subnet"></a>Bedste fremgangsmåder for at vælge et undernet
- Installer Azure AD-domænetjenester til en **separat dedikeret undernet** inden for Azure virtuelle netværk.

- Gælder ikke NSGs til det dedikerede undernet domænets administrerede. Hvis du skal anvende NSGs til det dedikerede undernet, sikre, at du **ikke blokerer de porte, der kræves til at vedligeholde og administrere dit domæne**.

- Alt for Begræns ikke antallet af IP-adresser, der er tilgængelige i det dedikerede undernet domænets administrerede. Denne begrænsning forhindrer, at tjenesten gøre to domænecontrollere tilgængelige for dit administrerede domæne.

- **Aktivér ikke Azure AD-domænetjenester i gateway-undernet** af netværket virtuel.


> [AZURE.WARNING] Når du knytter en NSG med et undernet, hvori Azure AD-domænetjenester er aktiveret, du kan afbryde Microsofts evne til at vedligeholde og administrere domænet. Desuden afbrydes synkronisering mellem din Azure AD-lejer og domænet administrerede. **SERVICENIVEAUAFTALEN gælder ikke for installationer, hvor en NSG er anvendt, der blokerer Azure AD-domænetjenester fra opdatering og administration af dit domæne.**


### <a name="ports-required-for-azure-ad-domain-services"></a>Porte, der kræves til Azure AD-domænetjenester
Følgende porte der kræves til Azure AD-domænetjenester til tjenesten og vedligehold domænet administrerede. Sørg for, at disse porte ikke blokeres for det undernet, hvor du har aktiveret dit administrerede domæne.

| Portnummer | Formål |
|---|---|
| 443 | Synkronisering med din Azure AD-lejer |
| 3389 | Administration af dit domæne |
| 5986 | Administration af dit domæne |
| 636 | Sikker LDAP (LDAPS) adgang til dit administrerede domæne |



## <a name="network-connectivity"></a>Netværksforbindelsen
En administreret Azure AD Domain Services-domæne kan aktiveres kun inden for et enkelt klassisk virtuelt netværk i Azure. Virtuelle netværk, der er oprettet ved hjælp af Azure ressourcestyring understøttes ikke.


### <a name="scenarios-for-connecting-azure-networks"></a>Scenarier, hvor der opretter forbindelse til Azure netværk
Forbinde Azure virtuelt netværk for at bruge administrerede domænet i et af følgende installationsscenarier:

#### <a name="use-the-managed-domain-in-more-than-one-azure-classic-virtual-network"></a>Brug administrerede domænet i mere end én Azure klassisk virtuelt netværk
Du kan oprette forbindelse til andre Azure klassisk virtuelle netværk til det Azure klassiske virtuelle netværk, hvor du har aktiveret Azure AD-domænetjenester. Denne VPN-forbindelse gør det muligt at bruge administrerede domænet med din arbejdsbelastninger, som er implementeret i andre virtuelle netværk.

![Klassisk virtuelle netværksforbindelsen](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### <a name="use-the-managed-domain-in-a-resource-manager-based-virtual-network"></a>Bruge administrerede domænet i et ressourcestyring-baserede virtuelt netværk
Du kan oprette forbindelse et virtuelt netværk for ressourcestyring-baseret på Azure klassisk virtuelle netværket, hvor du har aktiveret Azure AD-domænetjenester. Denne forbindelse gør det muligt at bruge administrerede domænet med din arbejdsbelastninger, som er implementeret i ressourcestyring-baserede virtuelle netværket.

![Ressourcestyring til klassisk virtuelle netværksforbindelsen](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)


### <a name="network-connection-options"></a>Indstillinger for netværksforbindelser

- **VNet-VNet forbindelser ved hjælp af websted til websted VPN-forbindelser**: oprette forbindelse et virtuelt netværk til en anden virtuelt netværk (VNet til VNet) ligner forbindelse et virtuelt netværk til en lokal placering. Begge typer connectivity bruges en VPN-gateway for at give en sikker tunnel ved hjælp af IPsec/IKE.

    ![Virtuel netværksforbindelse ved hjælp af VPN-Gateway](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [Du kan finde flere oplysninger - forbinder virtuelle netværk ved hjælp af VPN-gateway](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)


- **VNet-VNet forbindelser ved hjælp af virtuelt netværk peering**: virtuelt netværk peering er en funktion, der forbinder to virtuelle netværk i det samme område via netværket Azure grundlæggende element. Når peered, vises de to virtuelle netværk som en for alle connectivity formål. De administreres stadig som separate ressourcer, men virtuelle maskiner i disse virtuelle netværk kan kommunikere med hinanden direkte ved hjælp af private IP-adresser.

    ![Virtuel netværksforbindelse ved hjælp af peering](./media/active-directory-domain-services-design-guide/vnet-peering.png)

    [Flere oplysninger om-virtuelle netværk peering](../virtual-network/virtual-network-peering-overview.md)



<br>

## <a name="related-content"></a>Relateret indhold

- [Azure virtuelt netværk peering](../virtual-network/virtual-network-peering-overview.md)

- [Konfigurere en VNet-VNet forbindelse til den klassiske implementeringsmodel](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

- [Azure netværk sikkerhedsgrupper](../virtual-network/virtual-networks-nsg.md)
