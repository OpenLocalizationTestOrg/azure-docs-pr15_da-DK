## <a name="create-an-event-hub"></a>Oprette en begivenhed Hub

1. Logge på [Azure-portalen][], og klik på **Ny** øverst til venstre på skærmen.

2. Klik på **Data + Analytics**, og klik derefter på **Begivenheden Hubs**.

    ![](./media/event-hubs-create-event-hub/create-event-hub9.png)

3. Angiv et navneområde navn i bladet **Opret navneområde** . Det kontrolleres med det samme for at se, om navnet er tilgængelig.

    ![](./media/event-hubs-create-event-hub/create-event-hub1.png)

4. Efter at sikre, at navneområdet navn er tilgængelig skal du vælge det priser niveau (Basic eller Standard). Også vælge en Azure-abonnement, ressourcegruppe og placering, hvor oprette ressourcen. 

2. Klik på **Opret** for at oprette navneområdet.

6. Klik på det nyoprettede navneområde på listen begivenhed Hubs navneområde.      

    ![](./media/event-hubs-create-event-hub/create-event-hub2.png)

7. Klik på **Begivenhed Hubs**i bladet navneområde.

    ![](./media/event-hubs-create-event-hub/create-event-hub3.png)

8. Klik på **Tilføj begivenhed Hub**øverst i bladet.

    ![](./media/event-hubs-create-event-hub/create-event-hub4.png)

3. Skriv et navn til din begivenhed-Hub, og derefter klikke på **Opret**.

    ![](./media/event-hubs-create-event-hub/create-event-hub5.png)

4. Klik på nyoprettede begivenhed Hub navnet på listen over begivenhed Hubs. 

    ![](./media/event-hubs-create-event-hub/create-event-hub6.png)

5. Klik på **delt access politikker**tilbage i bladet navneområde (ikke den bestemte begivenhed Hub blade), og klik derefter på **RootManageSharedAccessKey**.

    ![](./media/event-hubs-create-event-hub/create-event-hub7.png)

5. Klik på knappen Kopiér for at kopiere forbindelsesstrengen **RootManageSharedAccessKey** til Udklipsholder. Gem denne forbindelsesstreng bruge senere i selvstudiet.

    ![](./media/event-hubs-create-event-hub/create-event-hub8.png)

Begivenhed centrum der nu oprettes, og du har forbindelse strengene skal du sende og modtage hændelser.

[Azure-portalen]: https://portal.azure.com/