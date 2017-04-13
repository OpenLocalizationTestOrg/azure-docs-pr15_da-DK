## <a name="how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal"></a>Sådan oprettes en VNet ved hjælp af en netværk konfigurationsfil i portalen Azure

Azure bruger en XML-fil til at definere alle VNets tilgængelige til et abonnement. Du kan hente denne fil, og rediger den for at ændre eller slette eksisterende VNets og oprette nye. I dette dokument, kan du lære, hvordan du hente denne fil, som kaldes netværk konfiguration (eller netcgf)-fil, og rediger den for at oprette en ny VNet. Markér [Azure virtuelt netværk konfiguration skema](https://msdn.microsoft.com/library/azure/jj157100.aspx) for at få flere oplysninger om konfigurationsfil netværk.

Følg nedenstående trin for at oprette en VNet ved hjælp af en netcfg fil via Azure-portalen.

1. Gå til http://manage.windowsazure.com fra en browser, og hvis det er nødvendigt, kan du logge på med din Azure-konto.
2. Rul ned på listen over tjenester, og klik på **netværk** , som vist nedenfor.

    ![Azure virtuelle netværk](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure1.gif)

3. Klik på knappen **EKSPORTÉR** nederst på siden, som vist nedenfor.

    ![Knappen eksport](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure2.png)

4. Vælg det abonnement, du vil eksportere den virtuelle netværkskonfiguration fra på siden **Eksporter netværkskonfiguration** , og klik derefter på knappen markering i det nederste venstre hjørne på siden.
5. Følg vejledningen for din browser at gemme filen **NetworkConfig.xml** . Sørg for, at Bemærk, hvor du gemmer filen.
6. Åbn den fil, du gemte i trin 5 ovenfor ved hjælp af en XML- eller tekst editor program, og se efter den **<VirtualNetworkSites>** element. Hvis du har nogen netværk, der allerede har oprettet, hver netværk vises som sin egen **<VirtualNetworkSite>** element.
7. For at oprette det virtuelle netværk, der er beskrevet i dette scenarie skal du tilføje følgende XML lige under det **<VirtualNetworkSites>** element:

        <VirtualNetworkSite name="TestVNet" Location="Central US">
          <AddressSpace>
            <AddressPrefix>192.168.0.0/16</AddressPrefix>
          </AddressSpace>
          <Subnets>
            <Subnet name="FrontEnd">
              <AddressPrefix>192.168.1.0/24</AddressPrefix>
            </Subnet>
            <Subnet name="BackEnd">
              <AddressPrefix>192.168.2.0/24</AddressPrefix>
            </Subnet>
          </Subnets>
        </VirtualNetworkSite>

8.  Gemme konfigurationsfil netværk.
9.  I portalen Azure i det nederste venstre hjørne på siden klikke på **Ny**og derefter **NETVÆRKSTJENESTER**, skal du klikke på og derefter skal du klikke på **VIRTUELT netværk**og derefter klikke på **IMPORTKONFIGURATION** , som vist i nedenstående illustration.

    ![Importkonfiguration](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure3.gif)

10.  På siden **Importér konfigurationsfil netværk** , klik på **Gennemse FOR fil...**, og derefter gå til den mappe, du har gemt filen i trin 8 ovenfor, Vælg fil, og klik derefter på **Åbn**. Websiden skal ligne figuren nedenfor. Klik på pileknappen til at flytte til næste trin i det nederste højre hjørne af siden.

    ![Importér netværk fil konfigurationssiden](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure4.png)

11.   Bemærk posten for dit nye VNet, på siden **opbygge dit netværk** som vist i nedenstående illustration.

    ![Opbygning af network-siden](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure5.png)

12.   Klik på knappen markering i det nederste højre hjørne på siden for at oprette VNet. Efter et par sekunder vises din VNet på listen over tilgængelige VNets, som vist i nedenstående illustration.

    ![Nyt virtuelt netværk](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure6.png)