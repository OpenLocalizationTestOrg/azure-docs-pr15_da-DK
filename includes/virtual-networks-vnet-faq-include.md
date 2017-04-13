## <a name="virtual-network-basics"></a>Grundlæggende om virtuelt netværk

### <a name="what-is-an-azure-virtual-network-vnet"></a>Hvad er en Azure virtuelt netværk (VNet)?

Du kan bruge VNets at blive klargjort og administrere virtuelt privat netværk (VPN) i Azure og, du kan også sammenkæde VNets med andre VNets i Azure eller med dine lokale IT-infrastruktur til at oprette hybrid eller tværs lokale løsninger. Hver VNet, du opretter har sin egen CIDR Bloker og kan være kædet sammen med andre VNets og lokale netværk, så længe CIDR blokke ikke falder sammen. Du kan også have kontrol af DNS-serverindstillinger for VNets og VNet segmentere i undernet.

Brug VNets til:

- Oprette et dedikeret privat kun skyen virtuelt netværk

    Nogle gange kan kræver du ikke en tværs lokale konfiguration af din løsning. Når du opretter en VNet, kan tjenester og FOS i din VNet kommunikere direkte og sikkert med hinanden i skyen. Dette bevarer trafik sikkert i VNet, men stadig gør det muligt at konfigurere slutpunkt forbindelser til de FOS og -tjenester, der kræver internetkommunikation som en del af din løsning.

- Sikker udvide dit datacenter

    Med VNets, kan du oprette traditionelle-til-websted (S2S) VPN'er for at tilpasse din datacenter kapacitet sikkert. S2S VPN bruger IPSEC til at give en sikker forbindelse mellem din virksomhedens VPN-gateway og Azure.

- Aktivere hybrid skyen scenarier

    VNets giver dig mulighed for at understøtte en række med hybrid skyen scenarier. Du kan oprette sikker forbindelse mellem skybaserede programmer til alle typer lokalt system som mainframes og Unix-systemer.

### <a name="how-do-i-know-if-i-need-a-virtual-network"></a>Hvordan ved jeg, hvis jeg har brug for et virtuelt netværk?

Gå til [Oversigt over virtuelle netværk](../articles/virtual-network/virtual-networks-overview.md) for at få vist en beslutning-tabel, der kan hjælpe dig med at bestemme den bedste løsning for design af netværk for dig.

### <a name="how-do-i-get-started"></a>Hvordan kommer jeg i gang?

Gå til [den virtuelle netværk dokumentation](https://azure.microsoft.com/documentation/services/virtual-network/) at komme i gang. Denne side indeholder links til almindelige trinnene til konfiguration af samt oplysninger, der kan hjælpe dig med at forstå de ting, du skal tage i betragtning, når du designer dit virtuelle netværk.

### <a name="what-services-can-i-use-with-vnets"></a>Hvilke tjenester kan jeg bruge sammen med VNets?

VNets kan bruges sammen med en række forskellige Azure-tjenester, som Cloud Services (PaaS), virtuelle maskiner og Web Apps. Der er dog nogle tjenester, der ikke understøttes på en VNet. Skal du se den specifikke tjeneste, du vil bruge, og Bekræft, at den er kompatibel.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Kan jeg bruge VNets uden tværs lokale connectivity?

Ja. Du kan bruge en VNet uden brug af websted til websted connectivity. Dette er især nyttig, hvis du vil køre domæne enheder og SharePoint-farme i Azure.

## <a name="virtual-network-configuration"></a>Virtuel netværkskonfiguration

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Hvilke værktøjer skal jeg bruge til at oprette en VNet?

Du kan bruge følgende værktøjer til at oprette eller konfigurere et virtuelt netværk:

- Azure-portalen (for klassisk og ressourcestyring VNets).

- En netværk konfigurationsfil (netcfg - for klassisk VNets). Se [konfigurere et virtuelt netværk ved hjælp af et netværk konfigurationsfil](../articles/virtual-network/virtual-networks-using-network-configuration-file.md).

- PowerShell (for klassisk og ressourcestyring VNets).

- Azure CLI (for klassisk og ressourcestyring VNets).

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Hvilke adresseområder kan jeg bruge i min VNets?

Du kan bruge offentlige IP-adresseområder og en hvilken som helst IP-adresseområder, der er defineret i [RFC 1918](http://tools.ietf.org/html/rfc1918).

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Kan jeg have offentlige IP-adresser i min VNets?

Ja. Du kan finde flere oplysninger om offentlige IP-adresseområder, [offentlige IP-adresseområde i et virtuelt netværk (VNet)](../articles/virtual-network/virtual-networks-public-ip-within-vnet.md). Husk på, at din offentlige IP-adresser ikke vil være direkte tilgængeligt via internettet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-virtual-network"></a>Er der en grænse for antallet af undernet i min virtuelt netværk?

Der er ingen begrænsning på antallet af undernet, du bruger i en VNet. Alle undernet skal være helt indeholdt i adresseområde virtuelt netværk og skal ikke overlapper med hinanden.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Er der nogen begrænsninger på ved hjælp af IP-adresser i disse undernet?

Azure forbeholder sig nogle IP-adresser i hvert undernet. De første og sidste IP-adresserne på undernet er reserveret til protokollen overensstemmelse, sammen med 3 flere adresser, der bruges til Azure tjenester.

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Hvordan små og hvor stor kan være VNets og undernet?

Det mindste undernet vi understøtter er en /29, og den største er en /8 (ved hjælp af CIDR undernetdefinitioner).

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Kan jeg overføre mit VLAN'er til Azure med VNets?

Nej. VNets er Layer 3 overlejrer. Azure understøtter ikke nogen lag 2-semantik.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Kan jeg angive brugerdefinerede routing politikker på min VNets og undernet?

Ja. Du kan bruge bruger defineret Routing (UDR). Besøg [brugerdefineret omdirigerer og IP-videresendelse](../articles/virtual-network/virtual-networks-udr-overview.md)kan finde flere oplysninger om UDR.

### <a name="do-vnets-support-multicast-or-broadcast"></a>Understøtter VNets multicast- eller broadcast?

Nej. Vi understøtter ikke multicast- eller broadcast.

### <a name="what-protocols-can-i-use-within-vnets"></a>Hvilke protokoller kan jeg bruge i VNets?

Du kan bruge standard IP-baserede protokoller i VNets. Multicast, broadcast, IP-i-IP-encapsulated pakker og generisk Routing indkapsling (GRE) pakker er dog blokeret i VNets. Standard protokoller, der fungerer sammen, omfatter:

- TCP
- UDP
- ICMP

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Kan jeg pinge min standard routere inden for en VNet?

Nej.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Kan jeg bruge tracert til diagnosticere forbindelse?

Nej.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Kan jeg tilføje undernet, når VNet er blevet oprettet?

Ja. Undernet, kan føjes til VNets når som helst som undernetadressen ikke er en del af en anden undernet i VNet.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Kan jeg ændre størrelsen på min undernet, når jeg har oprettet den?

Du kan tilføje, fjerne, udvid eller Formindsk et undernet, hvis der er ingen FOS eller tjenester, der er implementeret i den ved hjælp af PowerShell-cmdlet'er eller NETCFG filen. Du kan også tilføje, fjerne, udvid eller Formindsk en hvilken som helst præfikser på enheder, så længe de undernet, der indeholder FOS eller tjenester ikke påvirkes af ændringen.

### <a name="can-i-modify-subnets-after-i-created-them"></a>Kan jeg redigere undernet, efter at jeg har oprettet dem?

Ja. Du kan tilføje, fjerne og ændre de CIDR blokke, der bruges af en VNet.

### <a name="can-i-connect-to-the-internet-if-i-am-running-my-services-in-a-vnet"></a>Kan jeg forbindelse til internettet, hvis jeg kører mine tjenester i en VNet?

Ja. Alle tjenester, der er installeret i en VNet kan oprette forbindelse til internettet. Hver skybaseret tjeneste, der er implementeret i Azure har et offentligt tilgængelige VIP, der er tildelt til den. Du skal definere input slutpunkter for PaaS roller og slutpunkter for virtuelle maskiner til at aktivere disse tjenester til at acceptere forbindelser fra internettet.

### <a name="do-vnets-support-ipv6"></a>Understøtter VNets IPv6?

Nej. Du kan ikke bruge IPv6 med VNets på nuværende tidspunkt.

### <a name="can-a-vnet-span-regions"></a>Kan en VNet dække områder?

Nej. En VNet er begrænset til et enkelt område.

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Kan jeg oprette forbindelse en VNet til en anden VNet i Azure?

Ja. Du kan oprette VNet til VNet kommunikation ved hjælp af REST API'er eller Windows PowerShell. Du kan også forbinde VNets via VNet Peering. Få vist flere detaljer om peering [her.](../articles/virtual-network/virtual-network-peering-overview.md)

## <a name="name-resolution-dns"></a>Navneoversættelse (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Hvad er min DNS-indstillinger for VNets?

Brug tabellen beslutning på siden [Navneoversættelse for FOS og rolle forekomster](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) til at vejlede dig gennem alle DNS-indstillinger tilgængelige.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Kan jeg angive DNS-serverne for en VNet?

Ja. Du kan angive DNS-server IP-adresser i indstillingerne for VNet. Dette anvendes som standard DNS-serverne for alle FOS i VNet.

### <a name="how-many-dns-servers-can-i-specify"></a>Hvor mange DNS-servere kan angive?

Du kan angive op til 12 DNS-servere.

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Kan jeg ændre min DNS-servere, når jeg har oprettet netværket?

Ja. Du kan ændre listen DNS-server til din VNet når som helst. Hvis du ændrer listen DNS-server, skal du genstarte hver af FOS i din VNet i rækkefølge efter dem for at fortsætte efter den nye DNS-server.


### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Hvad er Azure-producenter DNS og fungerer det med VNets?

Azure-producenter DNS er en med flere lejer DNS-tjeneste, som Microsoft. Azure registrerer alle dine FOS og rolle forekomster i denne tjeneste. Denne tjeneste indeholder navneoversættelse hostname for FOS og rolle forekomster, der er indeholdt i den samme skytjeneste og fulde Domænenavn for FOS og rolle forekomster i den samme VNet.

> [AZURE.NOTE] Er der en begrænsning på nuværende tidspunkt til de første 100 skytjenester i det virtuelle netværk til kryds-lejer navneoversættelse ved hjælp af Azure-producenter DNS. Denne begrænsning gælder ikke, hvis du bruger dine egne DNS-server.

### <a name="can-i-override-my-dns-settings-on-a-per-vm--service-basis"></a>Kan jeg tilsidesætte mine DNS-indstillinger på en per-VM / service datotype?

Ja. Du kan angive DNS-servere på grundlag af tjeneste i skyen at tilsidesætte standardindstillingerne for netværk. Men, anbefales det, at du bruger netværk hele DNS som muligt.

### <a name="can-i-bring-my-own-dns-suffix"></a>Kan jeg flytte mine egne DNS-suffiks?

Nej. Du kan ikke angive et brugerdefineret DNS-suffiks til din VNets.

## <a name="vnets-and-vms"></a>VNets og FOS

### <a name="can-i-deploy-vms-to-a-vnet"></a>Kan jeg installere FOS på en VNet?

Ja.

### <a name="can-i-deploy-linux-vms-to-a-vnet"></a>Kan jeg installere Linux FOS på en VNet?

Ja. Du kan installere en hvilken som helst distro af Linux understøttes af Azure.

### <a name="what-is-the-difference-between-a-public-vip-and-an-internal-ip-address"></a>Hvad er forskellen på en offentlig VIP og en intern IP-adresse?

- Den interne IP-adresse er en IP-adresse, der er tildelt hver VM inden for en VNet af DHCP. Det er ikke offentlig adgang. Hvis du har oprettet en VNet, tildeles den interne IP-adresse fra det område, du har angivet i undernet indstillingerne for din VNet. Hvis du ikke har en VNet, tildeles en intern IP-adresse stadig. Den interne IP-adresse, forbliver med VM til dets levetid, medmindre, der er blevet deallokeret VM.

- En offentlig VIP er den offentlige IP-adresse, der er tildelt din skyen tjenesten eller indlæse belastningsjusteringstjenesten. Det er ikke tildelt direkte til din VM NIC. VIP forbliver sammen med skytjenesten den er tildelt til, indtil alle FOS i den valgte skybaseret tjeneste deallokeres eller slettet. På det pågældende tidspunkt, er den udgivet.

### <a name="what-ip-address-will-my-vm-receive"></a>Hvilke IP-adresse får min VM?

- **Interne IP-adresse-** Hvis du installerer en VM til en VNet, modtager VM en intern IP-adresse fra en gruppe af interne IP-adresser, du angiver. FOS kommunikere inden for VNets ved hjælp af interne IP-adresser. Selvom Azure sætter en dynamisk interne IP-adresse, kan du anmode om en statisk adresse for din VM. Hvis du vil vide mere om statisk interne IP-adresser, gå til [Sådan angives en statiske interne IP-Adresser](../articles/virtual-network/virtual-networks-reserved-private-ip.md).

- **VIP-** Din VM er også knyttet til en VIP, selvom en VIP aldrig tildeles til VM direkte. En VIP er en offentlig IP-adresse, der kan tildeles til en skybaseret tjeneste. Du kan eventuelt reservere en VIP for skybaseret tjeneste.

- **ILPIP-** Du kan også konfigurere en forekomst niveau offentlige IP-adresse (ILPIP). ILPIPs er direkte tilknyttet VM i stedet for skytjenesten. Hvis du vil vide mere om ILPIPs skal du besøge [Forekomst niveau offentlige IP-oversigt](../articles/virtual-network/virtual-networks-instance-level-public-ip.md).

### <a name="can-i-reserve-an-internal-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Kan jeg reservere en intern IP-adresse til en VM, som jeg opretter på et senere tidspunkt?

Nej. Du kan reservere en intern IP-adresse. Hvis en intern IP-adresse er tilgængelig tildeles det til en VM eller rolle forekomst af DHCP. Denne VM muligvis eller muligvis ikke den tastaturgenvej, du vil den interne IP-adresse skal tildeles. Du kan dog ændre en allerede oprettet VM interne IP-adresse til en hvilken som helst tilgængelige interne IP-adresse.

### <a name="do-internal-ip-addresses-change-for-vms-in-a-vnet"></a>Gøre interne IP-adresser ændring for FOS i en VNet?

Ja. Interne IP-adresser forblive med VM i dets levetid, medmindre VM er blevet deallokeret. Når en VM er blevet deallokeret, frigives den interne IP-adresse, medmindre du har defineret en statisk interne IP-adresse til din VM. Hvis VM er blot ikke længere (og ikke indsat i feltet status **stoppet (Deallocated)**) forbliver IP-adressen tildelte til VM.

### <a name="can-i-manually-assign-ip-addresses-to-nics-in-vms"></a>Kan jeg manuelt tildele IP-adresser til netværkskort i FOS?

Nej. Du må ikke ændre en hvilken som helst interface egenskaber for FOS. Eventuelle ændringer kan medføre potentielt miste forbindelse til VM.

### <a name="what-happens-to-my-ip-addresses-if-i-shut-down-a-vm"></a>Hvad sker der til Min IP-adresser, hvis jeg lukker en VM?

Intet. IP-adresserne (både offentlige VIP og interne IP-adresse) forbliver med din skybaseret tjeneste eller VM.

> [AZURE.NOTE] Hvis du vil blot lukke VM, ikke bruge Management Portal for at gøre dette. Knappen Luk computeren der i øjeblikket Dealloker den virtuelle maskine.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-re-deploying"></a>Kan jeg flytte FOS fra ét undernet til en anden undernet i en VNet uden at installere igen?

Ja. Du kan finde flere oplysninger [her](../articles/virtual-network/virtual-networks-move-vm-role-to-subnet.md).

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Kan jeg konfigurere en statisk MAC-adresse til min VM?

Nej. En MAC-adresse kan ikke konfigureres statisk.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-it-has-been-created"></a>MAC-adressen forbliver de samme for min VM når den er blevet oprettet?

Ja, MAC-adressen forbliver de samme til et VM selvom VM er blevet standset (deallocated) og relanceret.

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Kan jeg oprette forbindelse til internettet fra en VM i en VNet?

Ja. Alle tjenester, der er installeret i en VNet kan oprette forbindelse til internettet. Desuden har hver skybaseret tjeneste, der er implementeret i Azure et offentligt tilgængelige VIP, der er tildelt til den. Du skal definere input slutpunkter for PaaS roller og slutpunkter for FOS til at aktivere disse tjenester til at acceptere forbindelser fra internettet.

## <a name="vnets-and-services"></a>VNets og -tjenester

### <a name="what-services-can-i-use-with-vnets"></a>Hvilke tjenester kan jeg bruge sammen med VNets?

Du kan kun bruge Beregn tjenester i VNets. Beregne services er begrænset til Skytjenester (internettet og arbejder roller) og FOS.

### <a name="can-i-use-web-apps-with-virtual-network"></a>Kan jeg bruge Web Apps med virtuelt netværk?

Ja. Du kan installere Web Apps i en VNet ved hjælp af ASE (App servicemiljø). Tilføje til den pågældende, Web Apps sikkert oprette forbindelse og få adgang til ressourcer i din Azure VNet, hvis du har punkt-til-site-konfigureret til din VNet. Yderligere oplysninger finder du se følgende:


- [Oprette Webapps i en App Service-miljø](../articles/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)

- [Web Apps virtuelt netværksintegration](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)

- [Brug af VNet Integration og Hybrid forbindelser med Webapps](https://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/)

- [Integrere en online med et virtuelt Azure-netværk](../articles/app-service-web/web-sites-integrate-with-vnet.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Kan jeg installere skytjenester med internettet og arbejder roller (PaaS) i en VNet?

Ja. Du kan installere PaaS tjenester i VNets.

### <a name="how-do-i-deploy-paas-roles-to-a-vnet"></a>Hvordan kan jeg installere PaaS roller til en VNet?

Du kan udføre dette ved at angive VNet navn og rolle /subnet tilknytningerne i afsnittet netværk konfiguration af konfigurationen af tjenesten. Du behøver ikke at opdatere din binære filer.

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Kan jeg flytte mine tjenester og VNets?

Nej. Du kan ikke flytte tjenester og VNets. Du skal slette, og installere tjenesten til at flytte den til en anden VNet igen.

## <a name="vnets-and-security"></a>VNets og sikkerhed

### <a name="what-is-the-security-model-for-vnets"></a>Hvad er sikkerhedsmodel for VNets?

VNets er helt adskilt fra hinanden og andre tjenester, der findes i Azure infrastruktur. En VNet er sikkerhed og rettighedsadministration rammen.

### <a name="can-i-define-acls-or-nsgs-on-my-vnets"></a>Kan jeg definerer ACLs eller NSGs på min VNets?

Nej. Du kan ikke knytte ACLs eller NSGs til VNets. Dog ACLs kan defineres på input slutpunkter for VM'er, der er implementeret til en VNets, og NSGs kan være knyttet til den undernet eller NIC.

### <a name="is-there-a-vnet-security-whitepaper"></a>Er der en VNet sikkerhed hvidbog?

Ja. Kan du downloade den [her](http://go.microsoft.com/fwlink/?LinkId=386611).

## <a name="apis-schemas-and-tools"></a>API'er, ved hjælp af skemaer og værktøjer

### <a name="can-i-manage-vnets-from-code"></a>Kan jeg administrere VNets fra kode?

Ja. Du kan bruge REST API'er til at administrere VNets og tværs lokale forbindelse. Du kan finde flere oplysninger [her](http://go.microsoft.com/fwlink/?LinkId=296833).

### <a name="is-there-tooling-support-for-vnets"></a>Er der værktøj understøttelse af VNets?

Ja. Du kan bruge PowerShell og kommandolinjen værktøjer til en lang række platforme. Du kan finde flere oplysninger [her](http://go.microsoft.com/fwlink/?LinkId=317721).
