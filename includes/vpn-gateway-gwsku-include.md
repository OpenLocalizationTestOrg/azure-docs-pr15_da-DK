Når du opretter en virtuel netværksgateway, skal du angive gatewayen SKU, du vil bruge. Når du vælger en højere gateway SKU, flere CPU'er og netværksbåndbredde, der er allokeret til gatewayen, og som et resultat af gatewayen kan understøtte højere netværkskapacitet til det virtuelle netværk.

VPN-Gateway, kan bruge følgende lagerenheder:

- Grundlæggende
- Standard
- Ved anvendelse

Når du vælger en anden SKU, skal du overveje følgende:

- Hvis du vil bruge en PolicyBased VPN-type, skal du bruge den grundlæggende SKU. PolicyBased VPN (tidligere kaldet statisk distributionen) understøttes ikke på andre SKU.
- BGP understøttes ikke på den grundlæggende SKU.
- ExpressRoute VPN Gateway fungere konfigurationer understøttes ikke på den grundlæggende SKU.
- Aktiv-aktiv S2S VPN-Gateway forbindelser kan konfigureres på den HighPerformance SKU.
