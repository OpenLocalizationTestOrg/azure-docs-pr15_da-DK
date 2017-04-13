<properties
    pageTitle="Installere en replikeringsdomænecontroller til Active Directory i Azure | Microsoft Azure"
    description="Et selvstudium, der forklarer, hvordan du installerer et domænenavn fra domænecontrolleren fra et lokalt Active Directory-område på en Azure virtuel maskine."
    services="virtual-network"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="virtual-network"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="curtand"/>


# <a name="install-a-replica-active-directory-domain-controller-in-an-azure-virtual-network"></a>Installere en Active Directory replikeringsdomænecontroller på en Azure virtuelt netværk

Dette emne viser, hvordan du installerer ekstra domæne enheder (også kaldet replika DCs) til et lokalt Active Directory-domæne på Azure virtuelle maskiner (VM'er) i en Azure virtuelt netværk.

Du kan også være interesseret i disse relaterede emner:

-  Du kan også kan du installere en ny Active Directory-område i en Azure virtuelt netværk. Se [installere en ny Active Directory-område i en Azure virtuelt netværk](../active-directory/active-directory-new-forest-virtual-machine.md)for disse trin.
-  Du kan finde grundlæggende vejledning om installation af Active Directory-domænetjenester (AD DS) på en Azure virtuelt netværk, [retningslinjer for installation af Windows Server Active Directory på virtuelle Azure-computere](https://msdn.microsoft.com/library/azure/jj156090.aspx).


## <a name="scenario-diagram"></a>Scenarie diagram

I dette scenarie skal skal eksterne brugere have adgang til programmer, der kører på servere, der er medlem af et domæne. VM'er, der kører application servere og replika DCs er installeret i en Azure virtuelt netværk. Det virtuelle netværk kan være forbundet til det lokale netværk ved en [- til-websted VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) -forbindelse, som vist i følgende diagram, eller du kan bruge [ExpressRoute](../expressroute/expressroute-locations-providers.md) til en hurtigere forbindelse.

Application servere og domænecontrollerne er installeret på separate skytjenester for at distribuere Beregn behandling og inden for [tilgængelighed sæt](../virtual-machines/virtual-machines-windows-manage-availability.md) til forbedret fejltolerance.
Domænecontrollerne replikeres med hinanden og med lokale DCs ved hjælp af Active Directory-replikering. Ingen synkronisering værktøjer, der skal bruges.

![Diagram pf Active Directory replikeringsdomænecontroller en Azure vnet][1]

## <a name="create-an-active-directory-site-for-the-azure-virtual-network"></a>Oprette et websted med Active Directory for det Azure virtuelle netværk

Det er en god ide at oprette et websted i Active Directory, der repræsenterer det netværk område, der svarer til det virtuelle netværk. Der hjælper med at optimere godkendelse, gentagelse og andre DC placering handlinger. Følgende fremgangsmåde beskriver, hvordan du opretter et websted, og du kan finde flere baggrund, under [tilføje et nyt websted](https://technet.microsoft.com/library/cc781496.aspx).

1. Åbn Active Directory-områder og -tjenester: **Server Manager** > **værktøjer** > **Active Directory-websteder og tjenester**.
2. Oprette et websted til at repræsentere det område, hvor du har oprettet en Azure virtuelt netværk: Klik på **websteder** > **handling** > **nyt websted** > Skriv navnet på det nye websted, som Azure os Vest > Vælg hyperlink til et websted > **OK**.
3. Oprette et undernet og knytte til det nye websted: Dobbeltklik på **websteder** > skal du højreklikke på **undernet** > **nye undernet** > Skriv IP-adresseområder for det virtuelle netværk (såsom 10.1.0.0/16 i scenarie diagrammet) > Vælg det nye Azure websted > **OK**.

## <a name="create-an-azure-virtual-network"></a>Oprette en Azure virtuelt netværk

1. Klik på **Ny**i [Azure klassisk portal](https://manage.windowsazure.com), > **Netværk Services** > **Virtuelt netværk** > **Brugerdefineret oprette** og bruge følgende værdier til at fuldføre guiden.

    På denne side i guiden...  | Angive disse værdier
    ------------- | -------------
    **Virtuelt netværk detaljer**  | <p>Navn: Skriv et navn til det virtuelle netværk, som WestUSVNet.</p><p>Område: Vælg det nærmeste område.</p>
    **DNS- og VPN-forbindelse**  | <p>DNS-servere: Angiv navn og IP-adressen på en eller flere lokale DNS-servere.</p><p>Connectivity: Vælge **Konfigurer et websted til websted VPN**.</p><p>Lokale netværk: Angiv et nyt lokale netværk.</p><p>Hvis du bruger ExpressRoute i stedet for en VPN-forbindelse, kan du se [konfigurere en ExpressRoute forbindelse via en Exchange-udbyder](../expressroute/expressroute-locations-providers.md).</p>
    **Websted til websted connectivity**  | <p>Navn: Skriv et navn til den lokale netværk.</p><p>VPN-enhed IP-adresse: angive den offentlige IP-adressen på den enhed, der opretter forbindelse til det virtuelle netværk. VPN-enheden kan ikke være placeret bag en NAT.</p><p>Address: Angiv adresseområder til dit lokale netværk (såsom 192.168.0.0/16 i scenarie diagrammet).</p>
    **Virtuelt netværk adresse mellemrum**  | <p>Adresseområde: Angiv IP-adresseområder FOS, du vil køre i det Azure virtuelle netværk (såsom 10.1.0.0/16 i scenarie diagrammet). Denne adresseområde kan ikke overlappe med adresseområder i det lokale netværk.</p><p>Undernet: Angiv et navn og adresse for et undernet til application servere (såsom Frontend, 10.1.1.0/24) og domænecontrollerne (såsom backend-version, 10.1.2.0/24).</p><p>Klik på **Tilføj gateway undernet**.</p>

2. Derefter skal du konfigurere gatewayen virtuelt netværk for at oprette en sikker til websted VPN-forbindelse. Se [konfigurere et virtuelt netværksgateway](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) til instruktionerne.
3. Opret websted til websted VPN-forbindelse mellem det nye virtuelle netværk og en lokal VPN-enhed. Se [konfigurere et virtuelt netværksgateway](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) til instruktionerne.


## <a name="create-azure-vms-for-the-dc-roles"></a>Oprette Azure FOS til DC roller

Gentag de følgende trin for at oprette FOS at være vært for rollen DC efter behov. Du skal installere mindst to virtuelle DCs for at give fejltolerance og redundans. Hvis det Azure virtuelle netværk indeholder mindst to DCs, der er konfigureret på samme måde (det vil sige, de er begge globale kataloger, Kør DNS-server, og ingen indeholder en hvilken som helst FSMO-rolle og osv.) Placer VM'er, der kører dem DCs i en angivet for forbedret fejltolerance tilgængelighed.
For at oprette FOS ved hjælp af Windows PowerShell i stedet for Brugergrænsefladen, skal du se [Bruge Azure PowerShell til at oprette og konfigurerer på forhånd virtuelle Windows-baserede computere](../virtual-machines/virtual-machines-windows-classic-create-powershell.md).

1. Klik på **Ny**i [Azure klassisk portal](https://manage.windowsazure.com) > **beregne** > **virtuelt** > **Fra galleriet**. Brug følgende værdier til at fuldføre guiden. Acceptér standardværdien for en indstilling, medmindre en anden værdi forslag eller påkrævet.

    På denne side i guiden...  | Angive disse værdier
    ------------- | -------------
    **Vælg et billede**  | Windows Server 2012 R2 Datacenter
    **Virtuelt konfiguration**  | <p>Virtuelt navn: Skriv et enkelt navn (såsom AzureDC1).</p><p>Nye brugernavn: Skriv navnet på en bruger. Denne bruger vil være medlem af den lokale administratorgruppe på VM. Du skal bruge dette navn til at logge på VM for første gang. Den indbyggede konto med navnet Administrator virker ikke.</p><p>Ny adgangskode/Bekræft: Skriv en adgangskode</p>
    **Virtuelt konfiguration**  | <p>Skybaseret tjeneste: Vælg <b>Opret en ny tjeneste i skyen</b> for den første VM og vælge det samme sky tjenestenavn, når du opretter flere VM'er, der er vært for rollen DC.</p><p>Skyen tjenesten DNS-navn: Angiv et globalt entydigt navn</p><p>Område/forbindelse gruppe/virtuelle netværk: Angiv navnet på det virtuelle netværk (såsom WestUSVNet).</p><p>Lagerplads konto: Vælg <b>Brug en automatisk oprettede lagerplads konto</b> til den første VM, og vælg derefter den samme kontonavn-lager, når du opretter flere VM'er, der er vært for rollen DC.</p><p>Tilgængelighed sæt: Vælg <b>Opret et sæt tilgængelighed</b>.</p><p>Tilgængelighed angivet navn: Skriv et navn til tilgængeligheden angive når du opretter den første VM og vælg derefter samme navn, når du opretter flere FOS.</p>
    **Virtuelt konfiguration**  | <p>Vælg <b>Installer VM Agent</b> og eventuelle andre udvidelser, du har brug for.</p>
2. Vedhæfte en disk til hver VM, som kører rollen DC server. Yderligere disken er nødvendig for at gemme AD-database, logfiler og SYSVOL. Angiv en størrelse på disken (såsom 10 GB), og Lad **Host Cache indstilling** indstillet til **ingen**. Du kan finde trinnene, [hvordan du vedhæfter en Disk med Data til en Windows virtuel maskine](../virtual-machines/virtual-machines-windows-classic-attach-disk.md).
3. Når du logger på første gang til VM, åbne **Server Manager** > **File and lagerplads Services** til at oprette en lydstyrken på disken ved hjælp af NTFS.
4. Reservere en statisk IP-adresse til VM'er, der kører rollen DC. Hente Microsoft Web Platform Installer og [installere Azure PowerShell](../powershell-install-configure.md) for at reservere en statisk IP-adresse, og kør cmdlet sæt AzureStaticVNetIP. Eksempel:

    ' Get-AzureVM - ServiceName AzureDC1-navngive AzureDC1 | Sæt AzureStaticVNetIP - IP-adresse 10.0.0.4 | Opdater AzureVM

Du kan finde flere oplysninger om at angive en statisk IP-adresse, skal du se [konfigurere en statisk interne IP-adresse til en VM](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-ad-ds-on-azure-vms"></a>Installere Active Directory-Domænetjenester på Azure FOS

Log på en VM, og Bekræft, at du har forbindelse på tværs af websted til websted VPN- eller ExpressRoute forbindelsen til ressourcer på dit lokale netværk. Derefter skal du installere Active Directory-Domænetjenester på Azure FOS. Du kan bruge samme fremgangsmåde, som du kan bruge til at installere en ekstra DC på dit lokale netværk (brugergrænseflade, Windows PowerShell eller en smart-fil). Når du installerer Active Directory-Domænetjenester, Kontrollér, at du angiver nye lydstyrken for placeringen af AD-database, logfiler og SYSVOL. Hvis du har brug for en opfriskning på AD DS installation, kan du se [Installere Active Directory-domænetjenester (niveau 100)](https://technet.microsoft.com/library/hh472162.aspx) eller [installere en Windows Server 2012 replikeringsdomænecontroller i et eksisterende domæne (niveau 200)](https://technet.microsoft.com/library/jj574134.aspx).

## <a name="reconfigure-dns-server-for-the-virtual-network"></a>Omkonfigurere DNS-server for det virtuelle netværk

1. Klik på navnet på det virtuelle netværk i [Azure klassisk portal](https://manage.windowsazure.com), og klik derefter på fanen **Konfigurer** at [omkonfigurere DNS server IP-adresser for netværket virtuel](../virtual-network/virtual-networks-manage-dns-in-vnet.md) til brug af de statiske IP-adresser, der er tildelt til replika DCs i stedet for en lokal DNS-servere IP-adresser.

2. For at sikre, at alle replika DC FOS på det virtuelle netværk er konfigureret med at bruge DNS-servere på det virtuelle netværk, skal du klikke på **virtuelle maskiner**, klik på statuskolonnen for hver VM og klik derefter på **Genstart**. Vent, indtil VM viser **kører** tilstand, før du forsøger at logge på den.

## <a name="create-vms-for-application-servers"></a>Oprette FOS for programmet servere

1. Gentag de følgende trin for at oprette FOS til at køre som application servere. Acceptér standardværdien for en indstilling, medmindre en anden værdi forslag eller påkrævet.

    På denne side i guiden...  | Angive disse værdier
    ------------- | -------------
    **Vælg et billede**  | Windows Server 2012 R2 Datacenter
    **Virtuelt konfiguration**  | <p>Virtuelt navn: Skriv et enkelt navn (såsom AppServer1).</p><p>Nye brugernavn: Skriv navnet på en bruger. Denne bruger vil være medlem af den lokale administratorgruppe på VM. Du skal bruge dette navn til at logge på VM for første gang. Den indbyggede konto med navnet Administrator virker ikke.</p><p>Ny adgangskode/Bekræft: Skriv en adgangskode</p>
    **Virtuelt konfiguration**  | <p>Skybaseret tjeneste: Vælg **Opret en ny tjeneste i skyen** for den første VM og vælge det samme sky service navn, når du opretter flere VM'er, der er vært for programmet.</p><p>Skyen tjenesten DNS-navn: Angiv et globalt entydigt navn</p><p>Område/forbindelse gruppe/virtuelle netværk: Angiv navnet på det virtuelle netværk (såsom WestUSVNet).</p><p>Lagerplads konto: Vælg **Brug en automatisk oprettede lagerplads konto** til den første VM, og vælg derefter den samme kontonavn-lager, når du opretter flere VM'er, der er vært for programmet.</p><p>Tilgængelighed sæt: Vælg **Opret et sæt tilgængelighed**.</p><p>Tilgængelighed angivet navn: Skriv et navn til tilgængeligheden angive når du opretter den første VM og vælg derefter samme navn, når du opretter flere FOS.</p>
    **Virtuelt konfiguration**  | <p>Vælg <b>Installer VM Agent</b> og eventuelle andre udvidelser, du har brug for.</p>

2. Efter hver VM er klargjort, kan du logge på og deltage i den til domænet. Klik på **Lokalserver**i **Server Manager** > **arbejdsgruppe** > **ændring...** **domæne** og derefter skrive navnet på dit lokale domæne. Angive legitimationsoplysninger for en domænebruger,, og genstart derefter VM for at fuldføre forbindelse til et domæne.

For at oprette FOS ved hjælp af Windows PowerShell i stedet for Brugergrænsefladen, skal du se [Bruge Azure PowerShell til at oprette og konfigurerer på forhånd virtuelle Windows-baserede computere](../virtual-machines/virtual-machines-windows-classic-create-powershell.md).

Se [Introduktion til Azure-cmdletter](https://msdn.microsoft.com/library/azure/jj554332.aspx) og [Azure Cmdlet Reference](https://msdn.microsoft.com/library/azure/jj554330.aspx)kan finde flere oplysninger om brug af Windows PowerShell.

## <a name="additional-resources"></a>Yderligere ressourcer

-  [Retningslinjer for installation af Windows Server Active Directory på Azure virtuelle maskiner](https://msdn.microsoft.com/library/azure/jj156090.aspx)
-  [Hvordan du overfører eksisterende lokalt Hyper-V domæne enheder til Azure ved hjælp af Azure PowerShell](http://support.microsoft.com/kb/2904015)
-  [Installere en ny Active Directory-område på en Azure virtuelt netværk](../active-directory/active-directory-new-forest-virtual-machine.md)
-  [Azure virtuelt netværk](../virtual-network/virtual-networks-overview.md)
-  [Microsoft Azure IT Pro IaaS: (01) virtuelt grundlæggende](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
-  [Microsoft Azure IT Pro IaaS: (05) oprette virtuelle netværk og tværs lokale forbindelse](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
-  [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
-  [Cmdletter til Azure administration](https://msdn.microsoft.com/library/azure/jj152841)

<!--Image references-->
[1]: ./media/active-directory-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png
