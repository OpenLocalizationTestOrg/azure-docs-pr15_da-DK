

For programmer, der skal skalere Beregn ressourcer ud af, og i skala handlinger implicit afstemmes på tværs af fejl og opdatering af domæner. En introduktion til VM skala referere sæt til den seneste [Azure blog meddelelse](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/).

Se nærmere på disse videoer kan læse mere om VM skala sæt:

 - [Mark Russinovich taler Azure skala sæt](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  

 - [Virtuelt skala angiver med Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-vm-scale-sets"></a>Oprette og administrere VM skala

VM skala sæt kan defineres og installeres ved hjælp af JSON skabeloner og [REST API'er](https://msdn.microsoft.com/library/mt589023.aspx) kun som individuelle Azure ressourcestyring FOS. Derfor kan en hvilken som helst standard Azure ressourcestyring installationsmetoder bruges. Du kan finde flere oplysninger om skabeloner, [redigering Azure ressourcestyring skabeloner](../articles/resource-group-authoring-templates.md).

Et sæt eksempel skabeloner for VM skala sæt kan findes i Hurtig start Azure skabeloner GitHub lageret her:

[https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) - Søg efter skabeloner med _vmss_ i titlen.

Du får vist en knap, der linker til funktionen portalen installation på siderne detaljer for disse skabeloner. Klik på knappen for at installere sættet VM skala, og udfyld alle parametre, der kræves i portalen. Understøtter store eller blandede bogstaver, som det er mere sikkert at bruge altid små bogstaver parameterværdier, hvis du ikke er sikker på, om en ressource. Der er også en praktisk video dissektionen af en VM skala sæt skabelon her:

[VM skala sæt skabelon dissektionen](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player)

## <a name="scaling-a-vm-scale-set-out-and-in"></a>Skalering en VM skala angive ind og ud

Hvis du vil øge eller mindske antallet af virtuelle maskiner i et VM skala sæt, skal du blot ændre egenskaben _kapacitet_ og geninstaller skabelonen. Denne enkle gør det nemt at skrive din egen brugerdefinerede skalering lag, hvis du vil angive brugerdefinerede målestoksforhold hændelser, der ikke understøttes af Azure Autoskalering.

Hvis du geninstallation af en skabelon til at ændre kapaciteten, kan du definerer en meget mindre skabelon, som kun omfatter SKU og opdaterede kapaciteten. Et eksempel på dette er vist her: [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json).

Hvis du vil gennemgå de trin, som opretter en skala sæt, der automatisk skaleres, se [Automatisk skala computere i et virtuelt skala sæt](../articles/virtual-machines/virtual-machines-windows-vmss-powershell-creating.md)

## <a name="monitoring-your-vm-scale-set"></a>Overvåge dit VM målestoksforhold angive

Det anbefales i øjeblikket du bruge [Azure ressource Explorer](https://resources.azure.com) til at få vist VM skala sæt. VM skala sæt er en ressource under Microsoft.Compute, så fra dette websted, kan du se dem ved at udvide linkene nedenfor:

    subscriptions -> your subscription -> resourceGroups -> providers -> Microsoft.Compute -> virtualMachineScaleSets -> your VM scale set -> etc.

## <a name="vm-scale-set-scenarios"></a>VM skala angive scenarier

Dette afsnit beskrives nogle typiske VM skala sæt scenarier. Nogle højere niveau Azure tjenester (som Batch, Service-strukturen og Azure objektbeholder tjenesten), der anvender disse scenarier.

 - **RDP / SSH med VM skalering angive forekomster** - A VM skala sættet er oprettet i en VNET og individuelle FOS i fordeles ikke offentlige IP-adresser. Dette er et godt, fordi du ikke vil udgifter og styring omkostninger til fordeling separat IP-adresser til alle de ressourcer, der er uden status i din Beregn gitter Generelt, og du kan nemt oprette forbindelse til disse FOS fra andre ressourcer i din VNET, herunder dem, der har offentlige IP-adresser som Indlæs balancere eller enkeltstående virtuelle maskiner.

 - **Opret forbindelse til FOS ved hjælp af regler for NAT** - du kan oprette en offentlig IP-adresse, tildele den til en justering af belastning og definere indgående NAT regler som overfører en port til IP-adressen til en port på en VM i sættet VM skala. F.eks.

    Offentlige IP -> Port 50000 -> vmss\_0 -> Port 22 offentlige IP - > Port 50001 -> vmss\_1 -> Port 22 offentlige IP - > Port 50002 -> vmss\_2 -> Port 22

    Her er et eksempel på oprettelse af et VM skala sæt som bruger NAT regler til at aktivere SSH forbindelse til hver VM i en skala sæt med en enkelt offentlige IP-adresse: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)

    Her er et eksempel på at gøre det samme med RDP og Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)

 - **Opret forbindelse til FOS ved hjælp af en "jumpbox"** - Hvis du opretter en VM skala angive og en enkeltstående VM i den samme VNET, enkeltstående VM og VM skala sæt FOS kan oprette forbindelse til hinanden ved hjælp af deres interne IP-adresser, som defineret af VNET/undernet. Hvis du opretter en offentlig IP-adresse og tildele den til enkeltstående VM kan du RDP eller SSH til enkeltstående VM og derefter oprette forbindelse fra denne computer til din VM skala sæt forekomster. Der kan være, at et simpelt VM skala sæt er fuldstændig mere sikker end en simpel enkeltstående VM med en offentlig IP-adresse i dens standardkonfiguration på på dette tidspunkt.

    Et eksempel på denne metode, denne skabelon opretter en simpel Mesos klynge bestående af et enkeltstående Master VM som administrerer en VM skala sæt baseret klynge af FOS: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)

 - **Afrund robin belastning med VM skalering angive forekomster** – Hvis du vil holde arbejde for en computerklynge af FOS ved hjælp af en "round-robin" fremgangsmåde, kan du konfigurere en Azure justering af belastning med justering af belastning regler i overensstemmelse hermed. Du kan også angive sonder for at bekræfte dit program kører ved at pinge porte med en bestemt protokol, interval og anmodning om sti.

    Her er et eksempel, der opretter et VM skala sæt VM'er, der kører IIS webserver, og bruger en belastningsjustering til saldo den belastning, som hver VM modtager. Det også bruger HTTP-protokollen til ping til en bestemt URL-adresse på hver VM: [https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json) - Kig på ressourcetype Microsoft.Network/loadBalancers og networkProfile og extensionProfile i virtualMachineScaleSet.

 - **Installere en VM skala angive som en computerklynge i en PaaS klynge manager** - VM skala sæt nogle gange er beskrevet som en næste-generering af arbejder rolle. Det er en gyldig beskrivelse, men der også køres risikoen for forvirrende skala sæt funktioner med PaaS v1 arbejder rollefunktioner. I en følelse VM skala sæt indeholder en sand "arbejder rolle" eller ressource, som arbejder i, at de giver en generalized Beregn ressource, som er platform/runtime uafhængige, kan tilpasses og integrerer i Azure ressourcestyring IaaS.

    En PaaS v1 arbejder rolle, mens begrænset med hensyn til platform/runtime support (kun Windows-platform billeder) indeholder også tjenester som VIP Udskift, konfigurerbar opgraderingsindstillingerne, runtime/app installation specifikke indstillinger, som enten ikke _endnu er_ tilgængelige i VM skalere sæt, eller vil blive leveret af andre højere niveau PaaS tjenester som Service struktur. Med dette i kan husk, at du se på VM skala sæt som en infrastruktur, der understøtter PaaS. Det vil sige PaaS løsninger som Service-strukturen eller klynge ledere som Mesos kan oprette oven på VM skalere sæt som et SVG Beregn lag.

    Her er et eksempel på en Mesos klynge som installerer en VM skala angive i den samme VNET som en separat VM. Enkeltstående VM er en Mesos master, og sættet VM skala repræsenterer et sæt slave noder: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json). Fremtidige versioner af [Azure objektbeholder tjenesten](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/) implementerer mere komplekse/hærdet versioner af dette scenario, der er baseret på VM skala sæt.

## <a name="vm-scale-set-performance-and-scale-guidance"></a>VM skala sæt ydeevne og skalering vejledning

- Under Vis udskrift offentlige Opret ikke mere end 500 FOS i flere VM skala sæt ad gangen.
- Planlægge ikke mere end 40 FOS per lagerplads konto.
- Sprede de første bogstaver i lagerplads kontonavne som muligt.  Eksempel VMSS skabeloner i [Hurtig start Azure skabeloner](https://github.com/Azure/azure-quickstart-templates/) indeholder eksempler på, hvordan du gør dette.
- Hvis ved hjælp af brugerdefinerede FOS planlægge ikke mere end 40 FOS per VM skala angivet i en enkelt lagerplads konto.  Du skal allerede kopieres til kontoen lagerplads, før du kan begynde VM implementering sæt billedet. Se ofte stillede spørgsmål om kan finde flere oplysninger.
- Planlægge i forhold til højst 2048 FOS per VNET.  Denne grænse øges i fremtiden.
- Antallet af FOS, kan du oprette er begrænset af kvoten Beregn core i et område. Du skal muligvis kontakte kundesupport for at øge din Beregn kvote øget, selvom du har en høj grænse kerner til brug med skytjenester eller IaaS v1 i dag. Forespørge kvoten kan du køre følgende kommando for Azure CLI: _azure vm liste-brugen_og følgende PowerShell-kommando: _Get-AzureRmVMUsage_ (hvis ved hjælp af en version af PowerShell under 1.0 bruger _Get-AzureVMUsage_).

## <a name="vm-scale-set-frequently-asked-questions"></a>VM skala angive ofte stillede spørgsmål

**Q.** Hvor mange FOS kan du have i et VM skala sæt?

**A.** 100, hvis du bruger platform billeder og den kan fordeles på tværs af flere lagerplads konti. Hvis du bruger brugerdefinerede billeder, op til 40, da brugerdefinerede billeder er begrænset til en enkelt lagerplads konto under eksempelvisning.

**Q** findes hvilke andre ressource begrænsninger for VM skala sæt?

**A.** Du er begrænset til at oprette højst 500 FOS i flere skala sæt per område i perioden, preview. Den nuværende [Azure-abonnementstjeneste begrænsninger /](../articles/azure-subscription-service-limits.md) anvende.

**Q.** Er Data diske understøttes i VM skala sæt?

**A.** Ikke i den første version. Dine indstillinger for lagring af data er:

- Azure-filer (små og mellemstore virksomheder delt drev)

- OS drev

- Temp drev (lokal, ikke understøttes af Azure-lager)

- Eksterne data service (fx remote DB, Azure tabeller, Azure BLOB)

**Q.** Hvilke Azure områder understøtter for VM skala sæt?

**A.** Et område, som understøtter Azure ressourcestyring understøtter VM skala sæt.

**Q.** Hvordan kan du oprette en VM skala sæt ved hjælp af et brugerdefineret billede?

**A.** Lad egenskaben vhdContainers stå tomt, f.eks.:

    "storageProfile": {
        "osDisk": {
            "name": "vmssosdisk",
            "caching": "ReadOnly",
            "createOption": "FromImage",
            "image": {
                "uri": [https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd](https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd)
            },
            "osType": "Windows"
        }
    },


**Q.** Hvis jeg reducere angive min VM skala kapacitet fra 20 til 15, som FOS fjernes?

**A.** Virtuelle maskiner fjernes fra skalaen angive jævnt på tværs af opgradering domæner og fejl domæner til at maksimere tilgængelighed.

**Q.** Hvad med den Hvis jeg derefter øge kapaciteten fra 15-18?

**A.** Hvis du øger 18, FOS med indeks 15, 16, oprettes 17. I begge tilfælde afstemmes FOS på tværs af FDs og UDs.

**Q.** Når du bruger flere filtypenavne i et VM skala sæt, kan jeg gennemtvinge en udførelse af sekvens?

**A.** Ikke direkte, men til filtypenavnet customScript dit script kan vente til et andet filtypenavn til at fuldføre (for eksempel ved at overvåge filtypenavnet logge – se [https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install\_lap.sh](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)).

**Q.** Arbejder VM skala sæt med Azure tilgængelighed sæt?

**A.** Ja. Et VM skala sæt er en implicit tilgængelighed, der er konfigureret med 3 FDs og 5 UDs. Du behøver ikke at konfigurere noget under virtualMachineProfile. I fremtidige versioner, VM skala sæt har sandsynligvis strækker sig over flere lejere, men nu en skala angive er til et enkelt tilgængelighed sæt.
