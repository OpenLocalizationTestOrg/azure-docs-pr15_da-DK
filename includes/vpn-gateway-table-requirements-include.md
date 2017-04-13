I følgende tabel vises kravene til PolicyBased og RouteBased VPN gateways. I denne tabel gælder for både ressourcestyring og klassisk installation modeller. Klassisk modellen, PolicyBased VPN gateways er den samme som statisk gateways og distribuere-baserede gateways er den samme som dynamiske gateways.


|   | **PolicyBased Basic VPN-Gateway** | **RouteBased Basic VPN-Gateway** | **RouteBased Standard VPN-Gateway**   | **RouteBased høj ydeevne VPN-Gateway** |
|---|---------------------------------------|---------------------------------------|----------------------------|----------------------------------|
|    **Websted til websted connectivity (S2S)**  | PolicyBased VPN-konfiguration        | RouteBased VPN-konfiguration  | RouteBased VPN-konfiguration     | RouteBased VPN-konfiguration    |
| **Punkt-til-Site connectivity (P2S**)      | Ikke understøttet   | Understøttede (kan fungere sammen med S2S)  | Understøttede (kan fungere sammen med S2S)  | Understøttede (kan fungere sammen med S2S) |
| **Godkendelsesmetode**                 |    Delt nøgle  | Delt nøgle til S2S connectivity, certifikater til P2S forbindelse | Delt nøgle til S2S connectivity, certifikater til P2S forbindelse | Delt nøgle til S2S connectivity, certifikater til P2S forbindelse |
| **Maksimale antal S2S forbindelser**       | 1                              | 10                                                                    | 10                                | 30                               |
| **Maksimale antal P2S forbindelser**       | Ikke understøttet                  | 128                                                                   | 128                               | 128                              |
|**Understøttelse af aktive routing (BGP)**           | Ikke understøttet                  | Ikke understøttet                                                         | Understøttes                     | Understøttes                   |
 
