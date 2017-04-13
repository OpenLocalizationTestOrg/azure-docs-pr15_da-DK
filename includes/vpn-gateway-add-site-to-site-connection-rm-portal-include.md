1. Find din gateway, virtuelt netværk, og klik på **alle indstillinger** for at åbne bladet **Indstillinger** .

2. Klik på **forbindelser**på bladet **Indstillinger** , og klik derefter på **Tilføj** øverst i bladet for at åbne bladet **Tilføj forbindelse** .

    ![Oprette forbindelse til websted](./media/vpn-gateway-add-site-to-site-connection-rm-portal-include/addconnection250.png)

3. På bladet **Tilføj forbindelse** , **navn** , din forbindelse. 

4. Vælg **Site-to-site(IPSec)** **forbindelsestype**.

5. Værdien er fast **virtuelle netværksgateway**, fordi du vil oprette forbindelse fra denne gateway.

6. Klik på **Vælg en lokale netværksgateway** for **lokale netværksgateway**, og vælg gatewayen lokale netværk, du vil bruge. 

7. For **Delt nøgle**, værdi her skal svare til den værdi, du bruger til din lokale VPN-enhed. Hvis enheden VPN på dit lokale netværk indeholder ikke en delt nøgle, kan du udgør en og skriv det her og på din lokale enhed. Det vigtigste er, at de begge overens.

8. De resterende værdier for **abonnement**, **Ressourcegruppe**og **placering** er fast.

9. Klik på **OK** for at oprette forbindelsen. Du får vist *Oprettelse af forbindelse* flash på skærmen.

10. Når forbindelsen er fuldført, kan du se den vises i bladet **forbindelser** til din Gateway.

    ![Oprette forbindelse til websted](./media/vpn-gateway-add-site-to-site-connection-rm-portal-include/connectionstatus450.png)

