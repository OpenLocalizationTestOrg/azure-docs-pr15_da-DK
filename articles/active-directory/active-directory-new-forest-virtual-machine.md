<properties
    pageTitle="Installere et Active Directory-område på et netværk med Azure virtuelle | Microsoft Azure"
    description="Et selvstudium, der forklarer, hvordan du opretter et nyt Active Directory-område på en virtuel maskine (VM) i et virtuelt Azure-netværk."
    services="active-directory, virtual-network"
    keywords="Active directory virtuel maskine installere active directory-område, azure active directory-videoer "
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    tags=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/10/2016"
    ms.author="markusvi"/>


# <a name="install-a-new-active-directory-forest-on-an-azure-virtual-network"></a>Installere en ny Active Directory-område på en Azure virtuelt netværk

Dette emne viser, hvordan du opretter et nyt Windows Server Active Directory-miljø i en Azure virtuelt netværk på en virtuel maskine (VM) på en [Azure virtuelt netværk](../virtual-network/virtual-networks-overview.md). I dette tilfælde er Azure virtuelle netværket ikke forbundet med en lokale netværk.

Du kan også være interesseret i disse relaterede emner:

- For en video, der viser disse trin, se, [hvordan du installerer et nyt Active Directory-område i en Azure virtuelt netværk](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
- Du kan også kan du [konfigurere en VPN til-websted](../vpn-gateway/vpn-gateway-site-to-site-create.md) , og klik derefter enten ved at installere en ny skov eller udvide et lokalt område til et Azure virtuelle netværk. Se [installere en Active Directory replikeringsdomænecontroller i et virtuelt Azure-netværk](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)for disse trin.
-  Du kan finde grundlæggende vejledning om installation af Active Directory-domænetjenester (AD DS) på en Azure virtuelt netværk, [retningslinjer for installation af Windows Server Active Directory på virtuelle Azure-computere](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>Scenarie Diagram

I dette scenarie skal skal eksterne brugere have adgang til programmer, der kører på servere, der er medlem af et domæne. VM'er, der kører application servere og VM'er, der kører domænecontrollere installeres installeret på deres egne skybaseret tjeneste i en Azure virtuelt netværk. De er også inkluderet inden for en angivet for forbedret fejltolerance tilgængelighed.

![Active Directory-område på en virtuelle maskiner i Azure virtuelt netværk ][1] 7
## <a name="how-does-this-differ-from-on-premises"></a>Hvordan adskiller ud fra det lokale?

Der er ikke meget forskellen mellem installerer et domænenavn fra domænecontrolleren på Azure kontra i det lokale miljø. De vigtigste forskelle er angivet i den følgende tabel.

Sådan konfigureres...  | Lokalt  | Azure virtuelt netværk
------------- | -------------  | ------------
**IP-adressen til domænecontrolleren**  | Tildele statiske IP-adresse på netværk adapter egenskaber   | Køre sæt AzureStaticVNetIP cmdlet for at tildele en statisk IP-adresse
**DNS-klient resolver**  | Angive Foretrukne og alternative DNS serveradressen på netværket adapter egenskaber for medlem af et domæne   | Angiver DNS-serveradressen på den egenskaberne virtuelt netværk
**Active Directory-database-lager**  | Du kan også ændre standardplaceringen fra C:\  | Du skal ændre standardplaceringen fra C:\



## <a name="create-an-azure-virtual-network"></a>Oprette en Azure virtuelt netværk

1. Log på portalen Azure klassisk.
2. Oprette et virtuelt netværk. Klik på **netværk** > **Opret et virtuelt netværk**. Brug værdierne i den følgende tabel for at fuldføre guiden.

    På denne side i guiden...  | Angive disse værdier
    ------------- | -------------
    **Virtuelt netværk detaljer**  | <p>Navn: Skriv et navn til netværket virtuel</p><p>Område: Vælg nærmeste region</p>
    **DNS- og VPN**  | <p>Tomt DNS-server</p><p>Ikke Vælg enten VPN</p>
    **Virtuelt netværk adresse mellemrum**  | <p>Undernet navn: Skriv et navn til dit undernet</p><p>Start-IP-: <b>10.0.0.0</b></p><p>CIDR: <b>/24 (256)</b></p>



## <a name="create-vms-to-run-the-domain-controller-and-dns-server-roles"></a>Oprette FOS for at køre domænecontrolleren og DNS-serverroller

Gentag de følgende trin for at oprette FOS at være vært for rollen DC efter behov. Du skal installere mindst to virtuelle DCs for at give fejltolerance og redundans. Hvis det Azure virtuelle netværk indeholder mindst to DCs, der er konfigureret på samme måde (det vil sige, de er begge globale kataloger, Kør DNS-server, og ingen indeholder en hvilken som helst FSMO-rolle og osv.) Placer VM'er, der kører dem DCs i en angivet for forbedret fejltolerance tilgængelighed.

For at oprette FOS ved hjælp af Windows PowerShell i stedet for Brugergrænsefladen, skal du se [Bruge Azure PowerShell til at oprette og konfigurerer på forhånd virtuelle Windows-baserede computere](../virtual-machines/virtual-machines-windows-classic-create-powershell.md).

1. Klik på **Ny**i portalen klassisk > **beregne** > **virtuelt** > **Fra galleriet**. Brug følgende værdier til at fuldføre guiden. Acceptér standardværdien for en indstilling, medmindre en anden værdi forslag eller påkrævet.

    På denne side i guiden...  | Angive disse værdier
    ------------- | -------------
    **Vælg et billede**  | Windows Server 2012 R2 Datacenter
    **Virtuelt konfiguration**  | <p>Virtuelt navn: Skriv et enkelt navn (såsom AzureDC1).</p><p>Nye brugernavn: Skriv navnet på en bruger. Denne bruger vil være medlem af den lokale administratorgruppe på VM. Du skal bruge dette navn til at logge på VM for første gang. Den indbyggede konto med navnet Administrator virker ikke.</p><p>Ny adgangskode/Bekræft: Skriv en adgangskode</p>
    **Virtuelt konfiguration**  | <p>Skybaseret tjeneste: Vælg <b>Opret en ny tjeneste i skyen</b> for den første VM og vælge det samme sky service navn, når du opretter flere VM'er, der er vært for rollen DC.</p><p>Skyen tjenesten DNS-navn: Angiv et globalt entydigt navn</p><p>Område/forbindelse gruppe/virtuelle netværk: Angiv navnet på det virtuelle netværk (såsom WestUSVNet).</p><p>Lagerplads konto: Vælg <b>Brug en automatisk oprettede lagerplads konto</b> til den første VM, og vælg derefter den samme kontonavn-lager, når du opretter flere VM'er, der er vært for rollen DC.</p><p>Tilgængelighed sæt: Vælg <b>Opret et sæt tilgængelighed</b>.</p><p>Tilgængelighed angivet navn: Skriv et navn til tilgængeligheden angive når du opretter den første VM og vælg derefter samme navn, når du opretter flere FOS.</p>
    **Virtuelt konfiguration**  | <p>Vælg <b>Installer VM Agent</b> og eventuelle andre udvidelser, du har brug for.</p>
2. Vedhæfte en disk til hver VM, som kører rollen DC server. Yderligere disken er nødvendig for at gemme AD-database, logfiler og SYSVOL. Angiv en størrelse på disken (såsom 10 GB), og Lad **Host Cache indstilling** indstillet til **ingen**. Du kan finde trinnene, [hvordan du vedhæfter en Disk med Data til en Windows virtuel maskine](../virtual-machines/virtual-machines-windows-classic-attach-disk.md).
3. Når du logger på første gang til VM, åbne **Server Manager** > **File and lagerplads Services** til at oprette en lydstyrken på disken ved hjælp af NTFS.
4. Reservere en statisk IP-adresse til VM'er, der kører rollen DC. Hente Microsoft Web Platform Installer og [installere Azure PowerShell](../powershell-install-configure.md) for at reservere en statisk IP-adresse, og kør cmdlet sæt AzureStaticVNetIP. Eksempel:

    ' Get-AzureVM - ServiceName AzureDC1-navngive AzureDC1 | Sæt AzureStaticVNetIP - IP-adresse 10.0.0.4 | Opdater AzureVM

Du kan finde flere oplysninger om at angive en statisk IP-adresse, skal du se [konfigurere en statisk interne IP-adresse til en VM](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-windows-server-active-directory"></a>Installere Windows Server Active Directory

Brug den samme rutinemæssige at [installere Active Directory-Domænetjenester](https://technet.microsoft.com/library/jj574166.aspx) , at du bruger i det lokale miljø (det vil sige, du kan bruge Brugergrænsefladen, en smart-fil eller Windows PowerShell). Du skal angive legitimationsoplysninger for Administrator for at installere en ny skov. For at angive en placering til Active Directory-databasen og logge på SYSVOL skal du ændre standardplaceringen fra operativsystemdrevet til cd'en flere data, der er vedhæftet til VM.

Når DC installationen afsluttes, skal du oprette forbindelse til VM igen og logge på DC. Husk at angive legitimationsoplysninger til domænet.

## <a name="reset-the-dns-server-for-the-azure-virtual-network"></a>Nulstille DNS-server til Azure virtuelt netværk

1. Nulstil domænes DNS-indstilling på den nye DC/DNS-server.
  1. I Server Manager skal du klikke på **Funktioner** > **DNS**.
  2. Højreklik på navnet på DNS-serveren i **DNS Manager**, og klik på **Egenskaber**.
  3. Klik på IP-adressen på domænes, og klik på **Rediger**under fanen **videresendelse** .  Vælg IP-adressen, og klik på **Slet**.
  4. Klik på **OK** for at lukke editor og **Ok** igen for at lukke egenskaberne DNS-server.
2. Opdatere DNS-serverindstillingen for det virtuelle netværk.
  1. Klik på **Virtuelle netværk** > Dobbeltklik på det virtuelle netværk, du har oprettet > **Konfigurer** > **DNS-servere**, Skriv navnet og DIP på et af de VM'er, der kører rollen DC/DNS-server, og klik på **Gem**.
  2. Vælg VM, og klik på **Genstart** for at udløse VM for at konfigurere DNS resolver indstillinger med IP-adressen på den nye DNS-server.


## <a name="create-vms-for-domain-members"></a>Oprette FOS til medlem af et domæne

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


## <a name="see-also"></a>Se også

-  [Sådan installeres et nyt Active Directory-område i en Azure virtuelt netværk](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
-  [Retningslinjer for installation af Windows Server Active Directory på Azure virtuelle maskiner](https://msdn.microsoft.com/library/azure/jj156090.aspx)

-  [Konfigurere en VPN til-websted](../vpn-gateway/vpn-gateway-site-to-site-create.md)
-  [Installere en Active Directory replikeringsdomænecontroller på en Azure virtuelt netværk](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)
-  [Microsoft Azure IT Pro IaaS: (01) virtuelt grundlæggende](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
-  [Microsoft Azure IT Pro IaaS: (05) oprette virtuelle netværk og tværs lokale forbindelse](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
-  [Oversigt over virtuelt netværk](../virtual-network/virtual-networks-overview.md)
-  [Hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md)
-  [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
-  [Reference til Azure Cmdlet](https://msdn.microsoft.com/library/azure/jj554330.aspx)
-  [Angive Azure VM statiske IP-adresse](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)
-  [Sådan tildeles statiske IP-Adresser til Azure VM](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)
-  [Installere en ny Active Directory-område](https://technet.microsoft.com/library/jj574166.aspx)
-  [Introduktion til Active Directory Domain Services (AD DS) Virtualization (niveau 100)](https://technet.microsoft.com/library/hh831734.aspx)

<!--Image references-->
[1]: ./media/active-directory-new-forest-virtual-machine/AD_Forest.png
