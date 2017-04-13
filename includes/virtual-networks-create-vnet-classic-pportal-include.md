## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Sådan oprettes en klassisk VNet i portalen Azure

Følg nedenstående trin for at oprette en klassisk VNet baseret på ovenstående scenarie.

1. Gå til http://portal.azure.com fra en browser, og hvis det er nødvendigt, kan du logge på med din Azure-konto.
2. Klik på **Ny** > **netværk** > **virtuelle netværk**, kan du se, på listen **Vælg en implementeringsmodel** allerede viser **Klassisk**, og klik derefter på **Opret**, som det fremgår figuren nedenfor.

    ![Oprette VNet Azure-portalen](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)

3. Skriv **navnet** på VNet på bladet **virtuelle netværk** , og klik **-adresseområde**. Konfigurere indstillinger for din adresse plads til VNet og dens første undernet, og klik derefter på **OK**. I nedenstående figur vises CIDR Bloker indstillingerne for vores scenario.

    ![Adresse mellemrum blade](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)

4. Klik på **Ressourcegruppe** og vælg en ressourcegruppe tilføje VNet til, eller klik på **Opret ny ressourcegruppe** for at føje VNet til en ny ressourcegruppe. I nedenstående figur vises ressourcen gruppeindstillinger til en ny ressourcegruppe kaldet **TestRG**. Du kan finde flere oplysninger om ressourcegrupper ved at besøge [Azure ressourcestyring oversigt](../articles/virtual-network/resource-group-overview.md#resource-groups).

    ![Oprette ressource gruppe blade](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)

5. Hvis det er nødvendigt, kan du ændre indstillinger for **abonnement** og **placering** for din VNet. 

6. Hvis du ikke vil se VNet som et felt i **Startboard**, kan du deaktivere **Fastgør til Startboard**. 

7. Klik på **Opret** , og Bemærk feltet med navnet **oprette virtuelle netværk** , som vist i nedenstående illustration.

    ![Oprette VNet i portalen](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)

8. Vent på VNet skal oprettes, og klik på den for at tilføje flere undernet, når du får vist feltet nedenfor.

    ![Oprette VNet i portalen](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)

9. Du bør se **konfiguration** for din VNet, som vist nedenfor. 

    ![Oprette VNet i portalen](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)

10. Klik på **undernet** > **Tilføj**, skriv derefter et **navn** , og Angiv et **adresseområde (CIDR blok)** til dit undernet, og klik derefter på **OK**. Figuren nedenfor viser indstillingerne for vores aktuelle scenario.

    ![Oprette VNet Azure-portalen](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)