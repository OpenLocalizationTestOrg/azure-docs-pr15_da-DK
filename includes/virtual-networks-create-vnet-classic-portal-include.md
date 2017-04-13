## <a name="how-to-create-a-vnet-in-the-azure-portal"></a>Sådan oprettes en VNet i portalen Azure

Følg nedenstående trin for at oprette en VNet, der er baseret på scenariet ovenfor.

1. Gå til http://manage.windowsazure.com fra en browser, og hvis det er nødvendigt, kan du logge på med din Azure-konto.
2. Klik på **Ny** > **Netværk SERVICES** > **VIRTUELT netværk** > **Brugerdefineret oprette** som vist i nedenstående illustration.

    ![Oprette VNet i portalen](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure1.gif)

3. Skriv **navnet** på VNet på siden **Virtuelle netværk detaljer** , Vælg **PLACERINGEN**, og klik på pilen i det nederste højre hjørne på siden for at gå videre til trin 2. Figuren nedenfor viser indstillingerne for vores scenarie.

    ![Virtuelt netværk oplysningssiden](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure2.png)

4. Angiv navn og IP-adresse for op til 9 DNS-servere at bruge, på siden **DNS-servere og VPN-forbindelse** . Hvis du ikke angiver en DNS-server, bruge din VNet interne naming opløsning opløsningen fra Azure. I dette scenario vil vi ikke konfigurere DNS-servere.
5. Hvis du vil give adgang til VPN-punkt-til-websted til din VNet, skal du aktivere afkrydsningsfeltet **Konfigurer en VPN-forbindelse til punkt-til-websted** . Hvis du ikke konfigurerer en VPN-forbindelse til punkt-til-websted, kan du tilføje den til din VNet når som helst efter oprettelse af. I dette scenario konfigurerer vi ikke en VPN-forbindelse til punkt-til-websted.
6. Hvis du vil give til websted VPN-forbindelse mellem din VNet og en anden VNet eller dit lokale netværk, aktivere afkrydsningsfeltet **Konfigurer en VPN til-websted** , og Angiv, om du vil bruge **ExpressRoute** eller note og navnet på netværket til at oprette forbindelse til. Hvis du ikke konfigurerer en VPN til-websted, kan du tilføje den til din VNet når som helst efter oprettelse af. I vores scenario konfigurerer vi ikke en VPN til-websted.
7. Klik på pilen i det nederste højre hjørne på siden for at gå videre til trin 3 i figuren herunder viser indstillingerne for vores scenarie.

    ![Siden DNS-servere og VPN-forbindelse](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure3.png)

8. På siden **Virtuelt netværk adresse mellemrum** under **Første IP-**, klik på *10.0.0.0* ændre adresseområde VNet og derefter skrive de første adresseområde, du vil bruge. Skriv *192.168.0.0*i vores scenario. 
9. Vælg antallet af bit til undernetmasken under **CIDR (antal adresse)** . Vores scenarie skal du vælge *16 (65536)*.
10. Klik på *undernet 1* under **undernet**, og Omdøb undernettet, hvis det er nødvendigt. I dette scenario, kan du omdøbe den til *front end*.

    >[AZURE.NOTE] Hvis du klikker uden for tekstfeltet til et undernet kan du ikke kan redigere navnet, hvis undernettet igen. For at løse, skal du fjerne undernettet ved at klikke på knappen X til højre og derefter tilføje et nyt undernet, som beskrevet i trin 13 nedenfor.

11. Angiv den første IP-adresse for undernettet under **Første IP-** for det første undernet. Skriv *192.168.1.0*i vores scenario.
12. Vælg antallet af bit til undernetmasken for det første undernet under **CIDR (antal adresse)** . Vores scenarie skal du vælge *24 (256)*.
13. Klik på **Tilføj undernet** for at tilføje et nyt undernet, hvis det er nødvendigt. Tilføje et undernet vores scenarie, og Gentag trin 10-12 til at konfigurere VNet som vist i nedenstående illustration.

    ![Virtuelt netværk adresse mellemrum side](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure4.png)

14. Klik på knappen markering i det nederste højre hjørne på siden for at oprette VNet. Efter et par sekunder vises din VNet på listen over tilgængelige VNets, som vist i nedenstående illustration.

    ![Nyt virtuelt netværk](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure5.png)