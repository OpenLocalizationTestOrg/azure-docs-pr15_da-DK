- De virtuelle netværk kan være i de samme eller forskellige Azure områder (placeringer).

- En skybaseret tjeneste eller et slutpunkt til justering af belastning kan ikke dække på tværs af virtuelle netværk, selvom de er forbundet med hinanden.

- Opretter forbindelse til flere Azure virtuelt netværk sammen kræver ikke en hvilken som helst i det lokale miljø VPN gateways, medmindre tværs lokale forbindelse er påkrævet.

- VNet-VNet understøtter forbindende virtuelle netværk. Den ikke understøtter forbindende virtuelle computere eller tjenester ikke i et virtuelt netværk i skyen.

- VNet-VNet kræver Azure VPN gateways med RouteBased (tidligere kaldet dynamisk Routing) VPN typer. 

- Virtuel netværksforbindelsen kan bruges samtidigt med flere websted VPN'er med op til 10 (standard/Standard Gateways) eller 30 (høj ydeevne Gateways) VPN tunnelfører for et virtuelt netværk VPN-gateway, oprette forbindelse til andre virtuelle netværk eller lokale websteder.

- Adresse mellemrum af de virtuelle netværk og on-premises lokale netværk websteder skal ikke overlapper. Overlappende adresse mellemrum medfører, at oprettelsen af VNet-VNet forbindelser mislykkes.

- Overflødige tunneler mellem et par af virtuelle netværk understøttes ikke.

- Alle VPN-tunneler i det virtuelle netværk dele den tilgængelige båndbredde på gatewayen Azure VPN og den samme VPN gateway oppetid SLA i Azure.

- VNet-VNet trafik varer på tværs af Microsoft Network, ikke på internettet.

- VNet-VNet trafik inden for samme område er gratis for begge retninger på tværs af område VNet-VNet udgangspunkt faktureres trafik med filoverførsel satser de udgående blandt-VNet data baseret på de kilde områder. Se [priser side](https://azure.microsoft.com/pricing/details/vpn-gateway/) for at få oplysninger.