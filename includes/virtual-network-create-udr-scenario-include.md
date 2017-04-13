## <a name="scenario"></a>Scenarie

For bedre at illustrere hvordan du opretter UDRs, bruge dette dokument eksemplet nedenfor.

![BESKRIVELSE AF AFBILDNING](./media/virtual-network-create-udr-scenario-include/figure1.png)

I dette scenarie skal du oprette én UDR til *Front end undernet* og en anden UDR til *Back end-undernet* , som beskrevet nedenfor: 

- **UDR FrontEnd**. Front end UDR anvendes til *front end* -undernet, og indeholder én rute:  
    - **RouteToBackend**. Dette vil sende al trafik til back-end-undernet, at den **FW1** virtuelle maskine.
- **Back-end UDR**. Back-end UDR anvendes til *back-end* -undernet, og indeholder én rute: 
    - **RouteToFrontend**. Dette sender al trafik til front-end undernettet til virtuelt **FW1** .

Kombination af disse omdirigerer sikrer, at al trafik bestemt fra ét undernet til en anden bliver dirigeret til den **FW1** virtuelle maskine, hvor der anvendes som et virtuelt maskinen. Du skal også aktivere videresendelse af IP-adresse for VM, at sikre, at det kan modtage trafik bestemt til andre FOS.
