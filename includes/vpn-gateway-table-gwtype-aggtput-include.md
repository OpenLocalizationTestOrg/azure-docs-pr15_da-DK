|    | **VPN-Gateway overførselshastighed (1)** | **VPN-Gateway Maks IPsec tunnelfører (2)** | **ExpressRoute Gateway overførselshastighed** | **VPN-Gateway og ExpressRoute fungere**|
|--- |----------------------------|-----------------------------------|-------------------------------------|-----------------------------------------|
| **Grundlæggende SKU (3)(5)**              |  100 Mbps | 10                         |  500 Mbps                           | Nej   |
| **Standard SKU (4)(5)**           |  100 Mbps | 10                         | 1000 Mbps                           | Ja  |
| **Høj ydeevne SKU (4)**   | 200 Mbps  | 30                         | 2000 Mbps                           | Ja  |

- (1) VPN overførselshastigheden er et overslag, der er baseret på mål mellem VNets i samme Azure område. Det er ikke en garanterede overførsel for tværs lokale forbindelser på tværs af internettet. Det er det maksimale antal mulige gennemløb mål.
- (2) antallet af tunneler referere til RouteBased VPN. En PolicyBased VPN understøtter kun én til websted VPN-tunnel.
- (3) BGP understøttes ikke af den grundlæggende SKU.
- (4) PolicyBased VPN'er understøttes ikke for denne SKU. De understøttes til de grundlæggende SKU.
- (5) aktiv-aktiv S2S VPN-Gateway forbindelser understøttes ikke for denne SKU. Aktiv-aktiv understøttes på den HighPerformance SKU.