1. I portalen, fra **alle de ressourcer**, skal du klikke på **+ Tilføj**. Skriv **lokale netværksgateway**i **alt** blade søgefeltet og derefter Klik for at søge. Dette vil returnere en liste. Klik på **lokale netværksgateway** for at åbne bladet og derefter på **Opret** for at åbne bladet **Opret lokale netværksgateway** .

    ![oprette lokale netværksgateway](./media/vpn-gateway-add-lng-rm-portal-include/addlng250.png)

2. **Oprette lokale netværk gateway blade**, Angiv et **navn** til dit lokale netværk gateway-objekt.
 
3. Angiv en gyldig offentlige **IP-adresse** for VPN-enhed eller virtuelt netværksgateway, du vil oprette forbindelse.<br>Hvis dette lokale netværk repræsenterer en lokal placering, er dette den offentlige IP-adresse på den VPN-enhed, du vil oprette forbindelse til. Det være ikke bag NAT og skal være nås via Azure.<br>Hvis dette lokale netværk repræsenterer en anden VNet, skal du angive den offentlige IP-adresse, der er tildelt til gatewayen virtuelt netværk for denne VNet.<br>

4. **Adresseområde** refererer til-adresseområder for det netværk, der repræsenterer denne lokale netværk. Du kan tilføje flere adresseområder mellemrum. Sørg for, at de områder, du angiver her ikke overlapper med områder af andre netværk, du vil oprette forbindelse til.
 
5. Bekræft, at det korrekte abonnement vises **abonnement**.

6. Vælg den ressourcegruppe, du vil bruge til **Ressourcegruppe**. Du kan enten oprette en ny ressourcegruppe, eller Vælg en, du allerede har oprettet.

7. Vælg den placering, som dette objekt oprettes i for **placering**. Kan du vælge det samme sted, hvor din VNet er placeret i, men du behøver ikke at gøre dette.

8. Klik på **Opret** for at oprette gatewayen lokale netværk.
