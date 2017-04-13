## <a name="scenario"></a>Scenarie

For bedre at illustrere hvordan du opretter en VNet og undernet, bruge dette dokument eksemplet nedenfor.

![VNet scenarie](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

I dette scenarie skal du oprette en VNet med navnet **TestVNet** med en reserveret CIDR blok med **192.168.0.0./16**. Din VNet skal indeholde de følgende undernet: 

- **Front end**, som bruger **192.168.1.0/24** som dens CIDR blok.
- **Backend-version**, som bruger **192.168.2.0/24** som dens CIDR blok.

 