## <a name="scenario"></a>Scenarie

Dette dokument fører gennem en installation, der bruger flere netværkskort i FOS i et bestemt scenarie. I dette scenarie skal have du en to niveauer IaaS arbejdsbyrde Azure som vært. Hvert niveau er implementeret i et separat undernet i et virtuelt netværk (VNet). Front-end niveauet består af flere webservere grupperet i belastningsjustering indstillet til høj tilgængelighed. Back-end-niveau består af flere databaseservere. Disse databaseservere installeres med to netværkskort hver, en database adgang, et til administration. Dette scenario indeholder også netværk sikkerhedsgrupper (NSGs) til at styre, hvilken trafik har tilladelse til at hvert undernet og NIC i installationen. I nedenstående figur vises den grundlæggende arkitektur i dette scenarie.  

![MultiNIC scenario](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

