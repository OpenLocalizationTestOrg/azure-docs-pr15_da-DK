1. Gå til **Ny**i portalen. Skriv "Virtuelt netværksgateway" i Søg. Find **virtuelle netværksgateway** returnerede søgningen, og klik på posten. Dette åbner bladet **Opret virtuelt netværksgateway** .
2. Klik på **Opret** i bunden af bladet **virtuelle netværksgateway** . **Oprette virtuelle netværksgateway** blade åbnes. Udfyld værdierne for din gateway, virtuelt netværk.

    ![Oprette virtuelle netværk gateway blade felter] (./media/vpn-gateway-add-gw-rm-portal-include/createvnetgw300.png "Oprette virtuelle netværk gateway blade felter")

3. **Navn**: navngive din gateway. Dette er ikke den samme som navngive en gateway-undernet. Det er navnet på den gateway-objekt, du opretter.

4. **Gatewayen type**: Vælg **VPN**. VPN-destinationsgatewayen anvender typen virtuelt netværk gateway **VPN**. 

5. **VPN-type**: Vælg den VPN-type, der er angivet til din konfiguration. De fleste konfigurationer kræver en distribution baseret VPN-type.

6. **SKU**: Vælg gatewayen SKU på rullelisten. Lagerenheder er angivet i rullemenuen, afhænger af typen VPN, du vælger.

7. **Placering**: justere feltet **placering** til at pege på den placering, hvor netværket virtuel er placeret.
 
8. Vælg det virtuelle netværk, du vil tilføje denne gateway. Klik på **virtuelle netværk** for at åbne bladet **Vælg et virtuelt netværk** . Vælg VNet. Hvis du ikke kan se din VNet, skal du kontrollere feltet **placering** peger på det område, hvor netværket virtuel er placeret.

9. Vælg en offentlig IP-adresse. Klik på **offentlige IP-adresse** for at åbne bladet **Vælg offentlige IP-adresse** . Klik på **+ Opret ny** for at åbne den **oprette offentlige IP-adresse blade**. Skriv et navn til din offentlige IP-adresse. Denne blade opretter et offentlige IP-adresse objekt, en offentlig IP-adresse dynamisk tildeles.<br>Klik på **OK** for at gemme dine ændringer på denne blade.

10. **Abonnement**: Kontrollér, at det korrekte abonnement er markeret.

11. **Ressourcegruppe**: denne indstilling, bestemmes af det virtuelle netværk, du vælger. 

12. Juster ikke **placeringen** , når du har angivet de tidligere indstillinger.

13. Kontrollere indstillingerne. Hvis du vil have din gateway, der skal vises på dashboardet til, kan du vælge **Fastgør til dashboard** i nederst del af bladet.

14. Klik på **Opret** for at starte oprettelsen af gatewayen. Indstillingerne for valideres, og du får vist "installere virtuel netværk gatewayen" side om side på dashboardet. Oprette en gateway kan tage op til 45 minutter. Du skal muligvis opdatere siden portalen for at få vist fuldførte status.

    ![Implementere virtuelle netværksgateway] (./media/vpn-gateway-add-gw-rm-portal-include/deployvnetgw150.png "Implementere virtuelle netværksgateway")

11. Når gatewayen er oprettet, kan du få vist den IP-adresse, der er tildelt til den ved at se på det virtuelle netværk i portalen. Gatewayen vises som en tilsluttet enhed. Du kan klikke på den forbundne enhed (virtuelt netværk-gateway) for at få vist flere oplysninger.



