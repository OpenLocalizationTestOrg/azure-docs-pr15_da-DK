## <a name="vpn-gateway"></a>VPN-Gateway 
En VPN-gateway ressource gør det muligt at oprette en sikker forbindelse mellem deres lokale datacenter og Azure. En VPN-gateway ressource kan konfigureres i tre forskellige måder:
 
- **Peg på webstedet** – du kan opnå sikker adgang til dine Azure ressourcer, der er placeret i en VNET ved hjælp af en VPN-klient fra en hvilken som helst computer. 
- **Flere websted forbindelse** – du kan oprette sikker forbindelse mellem fra din lokale datacentre til ressourcer, der kører i et VNET. 
- **VNET til VNET** – du kan oprette en sikker forbindelse på tværs af Azure VNETS inden for samme område eller på tværs af områder til at opbygge arbejdsmængder med geografisk redundans.

Vigtige egenskaber i en VPN-gateway, omfatter:
 
- **Gateway-type** - dynamisk distribueret eller en statisk distribueret gateway. 
- **VPN-klient adresse puljen præfiks** – IP-adresser skal tildeles til oprettelse af forbindelse i et punkt websted konfigurationen-klienter.