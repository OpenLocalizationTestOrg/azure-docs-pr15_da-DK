## <a name="scenario"></a>Scenarie

For bedre at illustrere hvordan du opretter NSGs, bruge dette dokument eksemplet nedenfor.

![VNet scenarie](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

I dette scenarie skal du oprette en NSG for hvert undernet i **TestVNet** virtuelt netværk, som beskrevet nedenfor: 

- **NSG FrontEnd**. Front end NSG anvendes til *front end* -undernet, og indeholder to regler:  
    - **rdp-regel**. Denne regel, så RDP-trafik til *front end* -undernet.
    - **web-regel**. Denne regel, så HTTP-trafik til *front end* -undernet.
- **Back-end NSG**. Back-end NSG anvendes til *back-end* -undernet, og indeholder to regler: 
    - **sql-regel**. Denne regel tillader SQL trafik kun fra *front end* -undernet.
    - **web-regel**. Denne regel afviser alle internet bundet trafik fra *back-end* -undernet.

Kombination af disse regler oprette et synes godt om DMZ scenario, hvor back-end-undernettet kan kun modtage indgående trafik til SQL fra front end-undernet, og ikke har adgang til internettet, mens front-end-undernettet kan kommunikere med internettet, og modtage indgående HTTP-anmodninger kun.
 
