Følg nedenstående trin for at oprette en VNet i implementeringsmodel ressourcestyring ved hjælp af portalen Azure. Skærmbillederne, der er angivet som eksempler. Sørg for at erstatte værdierne med dine egne. Se [Oversigt over virtuelle netværk](../articles/virtual-network/virtual-networks-overview.md)kan finde flere oplysninger om at arbejde med virtuelle netværk.

1. I en browser, gå til [Azure-portalen](http://portal.azure.com) og, hvis det er nødvendigt, kan du logge på med din Azure-konto.

2. Klik på **Ny**. Skriv "Virtuelt netværk" i feltet **Søg på marketplace** . Find **Virtuelt netværk** fra listen returnerede, og klik for at åbne bladet **Virtuelt netværk** .

    ![Find virtuelt netværk ressource blade] (./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal700.png "Find virtuelt netværk ressource blade")

3. Vælg **Ressourcestyring**i bunden af bladet virtuelt netværk på listen **Vælg en implementeringsmodel** , og klik derefter på **Opret**.


    ![Vælg ressourcestyring] (./media/vpn-gateway-basic-vnet-rm-portal-include/resourcemanager250.png "Vælg ressourcestyring")

4. Konfigurere indstillingerne for VNet på bladet **Opret virtuelt netværk** . Når du udfylder felterne, bliver rødt udråbstegn et grønt flueben, når de tegn, der er angivet i feltet er gyldige.

    ![Validering af felt] (./media/vpn-gateway-basic-vnet-rm-portal-include/checkmark300.png "Validering af felt")

5. **Oprette virtuelle netværk** blade ligner i følgende eksempel. Der kan være værdier, der er udfyldt automatisk. Hvis det er tilfældet, skal du erstatte værdierne med dine egne.

    ![Oprette virtuelle netværk blade] (./media/vpn-gateway-basic-vnet-rm-portal-include/createvnet300.png "Oprette virtuelle netværk blade")

6. **Navn**: Skriv navnet på netværket virtuel.

7. **Adresseområde**: Angiv-adresseområde. Hvis du har flere adresse mellemrum at tilføje, kan du tilføje din første adresseområde. Du kan tilføje yderligere adresse mellemrum senere, når du har oprettet VNet.
 
8. **Undernet navn**: Tilføj undernet navn og undernet adresseområde. Du kan tilføje yderligere undernet senere, når du har oprettet VNet.

10. **Abonnement**: Bekræft, at abonnementet er angivet er det rigtige arbejdsområde. Du kan ændre abonnementer ved hjælp af på rullelisten.

11. **Ressourcegruppe**: Vælg en eksisterende ressourcegruppe, eller Opret en ny ved at skrive et navn til din nye ressourcegruppe. Hvis du opretter en ny gruppe, du navngive ressourcegruppe ifølge din planlagte konfiguration af værdier. Du kan finde flere oplysninger om ressourcegrupper ved at besøge [Azure ressourcestyring oversigt](resource-group-overview.md#resource-groups).

12. **Placering**: Vælg en placering til dine VNet. Placeringen bestemmer, hvor de ressourcer, du anvender denne VNet er placeret.

13. Vælg **Fastgør til dashboard** , hvis du vil kunne nemt finde din VNet på dashboardet til, og klik derefter på **Opret**.
    
    ![Fastgør til dashboard] (./media/vpn-gateway-basic-vnet-rm-portal-include/pintodashboard150.png "Fastgør til dashboard")

14. Når du klikker på **Opret**, vil du se et felt på dit dashboard, der vil afspejle status for din VNet. Feltet ændres, når VNet oprettes.

    ![Oprette virtuelle netværk side om side] (./media/vpn-gateway-basic-vnet-rm-portal-include/deploying150.png "Oprette virtuelle netværk side om side")