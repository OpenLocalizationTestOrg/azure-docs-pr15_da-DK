<properties 
   pageTitle="Foretage fejlfinding af netværk sikkerhedsgrupper - Portal | Microsoft Azure"
   description="Lær, hvordan du udfører fejlfinding af netværk sikkerhedsgrupper i Azure ressourcestyring installation modellen ved hjælp af portalen Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="AnithaAdusumilli"
   manager="narayan"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="anithaa" />

# <a name="troubleshoot-network-security-groups-using-the-azure-portal"></a>Foretage fejlfinding af netværk sikkerhedsgrupper ved hjælp af portalen Azure

> [AZURE.SELECTOR]
- [Azure-portalen](virtual-network-nsg-troubleshoot-portal.md)
- [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)

Hvis du har konfigureret netværk sikkerhedsgrupper (NSGs) på din virtuelle maskine (VM) og oplever problemer med serverforbindelsen VM, indeholder denne artikel en oversigt over diagnosticering egenskaber for NSGs til hjælp til fejlfinding af yderligere.

NSGs gør det muligt at kontrollere typerne trafik pågældende flow ind og ud af din virtuelle maskiner (VM'er). NSGs kan anvendes på undernet i et virtuelt Azure-netværk (VNet), netværksgrænseflader (NIC) eller begge dele. De effektive regler, der er anvendt på en NIC er en sammenlægning af de regler, der findes i de NSGs, der er anvendt på en NIC og det undernet, den er tilsluttet. Regler på tværs af disse NSGs kan nogle gange er i konflikt med hinanden og påvirke en VM netværksforbindelse.  

Du kan få vist alle de effektive sikkerhedsregler fra din NSGs, som anvendes på din VM netværkskort. I denne artikel viser, hvordan du udfører fejlfinding af VM forbindelsesproblemer ved hjælp af disse regler i implementeringsmodel Azure ressourcestyring. Hvis du ikke kender VNet og NSG begreber, Læs artiklerne om [virtuelle netværks](virtual-networks-overview.md) - og [netværk sikkerhedsgrupper](virtual-networks-nsg.md) oversigt.

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>Bruge effektive sikkerhedsregler til at foretage fejlfinding af VM trafikken

Dette scenario, der følger efter er et eksempel på et almindeligt forbindelsesproblem:

En VM med navnet *VM1* er en del af et undernet med navnet *Subnet1* i en VNet med navnet *WestUS VNet1*. Et forsøg på at oprette forbindelse til VM ved hjælp af RDP over TCP-port 3389 mislykkes. NSGs anvendes på både NIC *VM1 NIC1* og undernet *Subnet1*. Trafik til TCP-port 3389 er tilladt i den NSG, der er knyttet til netværksgrænsefladen *VM1 NIC1*, men TCP ping til VM1's port 3389 opstår.

Mens i dette eksempel bruges TCP-port 3389, kan følgende trin bruges til at bestemme mislykkede forsøg på indgående og udgående forbindelse via en hvilken som helst port.

### <a name="view-effective-security-rules-for-a-virtual-machine"></a>Få vist effektive sikkerhedsregler til en virtuel maskine

Benytte følgende fremgangsmåde for at foretage fejlfinding af NSGs for en VM:

Du kan se komplet liste over de effektive sikkerhedsregler på en NIC, fra VM sig selv. Du kan også tilføje, redigere og slette både NIC og undernet NSG regler fra bladet effektive regler, hvis du har tilladelse til at udføre disse handlinger.

1. Log på portalen Azure på https://portal.azure.com.
2. Klik på **flere tjenester**, og klik derefter på **virtuelle maskiner** i den viste liste.
3. Vælg en VM at foretage fejlfinding af på listen, der vises, og en VM blade med indstillinger vises.
4. Klik på **Diagnosticer & løse problemer med** og derefter vælge et almindeligt problem. I dette eksempel skal er **jeg kan ikke oprette forbindelse til min Windows VM** markeret. 

    ![](./media/virtual-network-nsg-troubleshoot-portal/image1.png)

5. Trin vises under problemet, som vist i billedet nedenfor: 

    ![](./media/virtual-network-nsg-troubleshoot-portal/image2.png)

    Klik på *effektiv sikkerhed gruppe regler* på listen over anbefalede trin.

6. Bladet **få effektive sikkerhedsregler** vises, som vist i billedet nedenfor:

    ![](./media/virtual-network-nsg-troubleshoot-portal/image3.png)

    Bemærk de følgende afsnit af billedet:

    - **Omfang:** Indstillet til *VM1*, valgte hvor VM i trin 3.
    - **Netværk interface:** *VM1 NIC1* er markeret. En VM kan have flere netværkgrænseflader (NIC). Hver NIC kan have entydige effektive sikkerhedsregler. Når fejlfinding, skal du muligvis få vist de effektive sikkerhedsregler for hver NIC.
    - **Knyttet NSGs:** NSGs kan anvendes på både NIC og undernet NIC er forbundet med. På billedet, der er anvendt en NSG både NIC og det undernet, den er tilsluttet. Du kan klikke på NSG navne for at redigere direkte regler i NSGs.
    - **VM1 nsg fanen:** Listen over regler, der vises på billedet er for NSG anvendt på NIC. Flere standardregler er oprettet af Azure, når der oprettes en NSG. Du kan ikke fjerne standardregler, men du kan tilsidesætte dem med regler for højere prioritet. Hvis du vil vide mere om standardregler skal du læse artiklen [NSG oversigt](virtual-networks-nsg.md#default-rules) .
    - **DESTINATION kolonne:** Nogle af reglerne, der har tekst i kolonnen, mens andre har adressepræfikser. Teksten er navnet på standardmærker, der anvendes til sikkerhedsreglen, når det blev oprettet. Mærkerne, er systemet id'er, der repræsenterer flere præfikser. Valg af en regel med et tag, som *AllowInternetOutBound*, viser præfikserne i bladet **adressepræfikser** .
    - **Hente:** Listen over regler kan være lang. Du kan hente en .csv-fil til offline analyse af reglerne ved at klikke på **Hent** og gemme filen.
    - **AllowRDP** Indgående regel: denne regel tillader RDP forbindelser til VM.
7. Klik på fanen **Subnet1 NSG** for at få vist de effektive regler fra NSG anvendt på undernet, som vist i billedet nedenfor: 

    ![](./media/virtual-network-nsg-troubleshoot-portal/image4.png)

    Bemærk *denyRDP* **indgående** reglen. Indgående regler, der anvendes på undernettet, evalueres før regler anvendt på netværksgrænsefladen. Da Afvis regel anvendes på undernettet, mislykkes anmodningen om at oprette forbindelse til TCP 3389, fordi Tillad reglen på NIC aldrig evalueres. 

    *DenyRDP* reglen er årsagen til, hvorfor RDP forbindelsen går ned. Det løser problemet at fjerne den.

    >[AZURE.NOTE]Hvis der er knyttet til NIC VM ikke er kører, eller NSGs ikke har anvendt til NIC eller undernet, vises ingen regler.

8. For at redigere NSG regler skal du klikke på *Subnet1 NSG* i sektionen **Tilknyttede NSGs** .
   Dette åbner bladet **Subnet1 NSG** . Du kan direkte redigere reglerne ved at klikke på **indgående sikkerhedsregler**.

    ![](./media/virtual-network-nsg-troubleshoot-portal/image7.png)

9. Når du fjerne *denyRDP* indgående reglen i **Subnet1 NSG** og tilføje en regel for *allowRDP* , ligner listen effektive regler billedet nedenfor:

    ![](./media/virtual-network-nsg-troubleshoot-portal/image8.png)

    Bekræft, at TCP-port 3389 er åbent ved at åbne en RDP-forbindelse til VM eller ved hjælp af værktøjet PsPing. Du kan få mere at vide om PsPing ved at læse den [PsPing overførselssiden](https://technet.microsoft.com/sysinternals/psping.aspx).

### <a name="view-effective-security-rules-for-a-network-interface"></a>Få vist effektive sikkerhedsregler for en netværksgrænseflade

Hvis din VM trafikken påvirkes for en bestemt NIC, kan du se en komplet liste over de effektive regler for NIC fra konteksten netværk grænseflader ved at udføre følgende trin:

1. Log på portalen Azure på https://portal.azure.com.
2. Klik på **flere tjenester**, og derefter klikke på **netværksgrænseflader** på listen, der vises.
3. Vælg en netværksgrænseflade. I det følgende billede, er en NIC med navnet *VM1 NIC1* markeret.

    ![](./media/virtual-network-nsg-troubleshoot-portal/image5.png)

    Bemærk, at **omfanget** er indstillet til netværksgrænsefladen markeret. Hvis du vil vide mere om, hvilke yderligere oplysninger, der vises, skal du læse trin 6 i afsnittet **Fejlfinding i forbindelse med NSGs for en VM** i denne artikel.

    >[AZURE.NOTE] Hvis en NSG fjernes fra et netværksgrænseflade, undernettet NSG, der er stadig træder i kraft på den angivne NIC. I dette tilfælde vil output kun vise regler fra undernet NSG. Regler vises kun, hvis NIC er knyttet til en VM.

4. Du kan redigere regler direkte til NSGs, der er knyttet til en NIC og et undernet. For at lære, hvordan læse trin 8 i afsnittet **Vis effektive sikkerhedsregler for en virtuel maskine** i denne artikel.

## <a name="view-effective-security-rules-for-a-network-security-group-nsg"></a>Få vist effektive sikkerhedsregler til en netværk sikkerhedsgruppe (NSG)

Når du ændrer NSG regler, kan du gennemgå de regler, der tilføjes på en bestemt VM gennemslagskraft. Du kan se en komplet liste over de effektive sikkerhedsregler for alle de netværkskort, der anvendes en given NSG på, uden at skulle skifte kontekst fra den angivne NSG blade. Hvis du vil foretage fejlfinding af effektive regler i en NSG ved at benytte følgende fremgangsmåde:

1. Log på portalen Azure på https://portal.azure.com.
2. Klik på **flere tjenester**, og klik derefter på **netværk sikkerhedsgrupper** i den viste liste.
3. Vælg en NSG. I det følgende billede er valgt en NSG med navnet VM1 nsg.

    ![](./media/virtual-network-nsg-troubleshoot-portal/image6.png)

    Bemærk følgende områder i det forrige billede:

    - **Omfang:** Angiv til NSG markeret.
    - **Virtuelt:** Når en NSG er anvendt på et undernet, anvendes alle netværksgrænseflader, der er knyttet til alle FOS tilsluttet til undernettet. Denne liste viser alle FOS denne NSG anvendes på. Du kan vælge en hvilken som helst VM på listen.

    >[AZURE.NOTE] Hvis en NSG anvendes til en tom undernet, vises FOS ikke. Hvis en NSG er anvendt på en NIC, som ikke er knyttet til en VM, vises disse netværkskort ikke også. 
    - **Netværk Interface:** En VM kan have flere netværkgrænseflader. Du kan vælge en netværksgrænseflade, der er knyttet til den valgte VM.
    - **AssociatedNSGs:** Når som helst kan en NIC have op til to effektive NSGs, en anvendt på NIC og den anden til undernettet. Selvom omfanget er markeret som VM1-nsg, hvis NIC har en effektiv undernet NSG, viser output begge NSGs.
4. Du kan redigere regler direkte til NSGs, der er knyttet til en NIC eller undernet. For at lære, hvordan læse trin 8 i afsnittet **Vis effektive sikkerhedsregler for en virtuel maskine** i denne artikel.

Hvis du vil vide mere om, hvilke yderligere oplysninger, der vises, skal du læse trin 6 i afsnittet **Vis effektive sikkerhedsregler for en virtuel maskine** i denne artikel.

>[AZURE.NOTE] Selvom en undernet og NIC hver kun have én NSG gælde for dem, kan en NSG knyttes til flere netværkskort og flere undernet.

## <a name="considerations"></a>Overvejelser i forbindelse med

Overvej følgende punkter, når fejlfinding af problemer:

- Standard NSG regler blokerer indgående adgang fra internettet og kun tillade VNet indgående trafik. Regler skal tilføjes eksplicit for at tillade indgående adgang fra internettet, efter behov.
- Hvis der er ingen NSG sikkerhedsregler, der forårsager en VM netværksforbindelsen mislykkes, være problemet skyldes:
    - Firewallsoftware, der kører i den VM operativsystem
    - Omdirigerer konfigureret til virtuelle anvendelser eller lokalt trafik. Internettrafik kan blive omdirigeret til en lokal installation via tvunget-tunnelføring. En RDP/SSH forbindelse fra internettet til dine VM fungerer muligvis ikke med denne indstilling, afhængigt af hvordan netværkshardwaren lokale håndterer denne trafik. Læs [Fejlfinding omdirigerer](virtual-network-routes-troubleshoot-powershell.md) artikel for at lære at diagnosticere distribuere problemer, der kan være at forhindre strømmen af trafik og VM. 
- Hvis du har peered VNets, som standard, udvide mærket VIRTUAL_NETWORK automatisk for at medtage præfikser for peered VNets. Du kan få vist disse præfikser på listen **ExpandedAddressPrefix** at foretage fejlfinding af problemer med at VNet peering connectivity. 
- Effektive sikkerhedsregler vises kun, hvis der er en NSG, der er knyttet til VM NIC og eller undernet. 
- Hvis der er ingen NSGs, der er knyttet til NIC eller undernet, og du har en offentlig IP-adresse, der er tildelt til din VM, vil alle porte være åben for indgående og udgående adgang. Hvis VM har en offentlig IP-adresse, anbefales anvende NSGs på NIC eller undernet.