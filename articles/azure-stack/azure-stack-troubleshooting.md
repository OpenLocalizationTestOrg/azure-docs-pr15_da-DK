<properties
    pageTitle="Fejlfinding i forbindelse med Microsoft Azure stak | Microsoft Azure"
    description="Azure stak fejlfinding."
    services="azure-stack"
    documentationCenter=""
    authors="heathl17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="helaw"/>

# <a name="microsoft-azure-stack-troubleshooting"></a>Fejlfinding i forbindelse med Microsoft Azure stak

Dette dokument indeholder almindelige oplysninger om fejlfinding til Azure stablen.

Sørg for, at dit eget udviklingsmiljø opfylder alle [krav](azure-stack-deploy.md) og [forberedelser](azure-stack-run-powershell-script.md) , før du installerer for den bedste oplevelse. 

Anbefalinger til fejlfinding af problemer, der er beskrevet i dette afsnit er udledt fra flere kilder og kan eller ikke kan muligvis løse et bestemt problem. Hvis du oplever et problem, der ikke er beskrevet, Sørg for at kontrollere [Azure stak MSDN-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) for yderligere support og yderligere oplysninger.

Kodeeksempler leveres, som er og forventede resultater kan sikres. Dette afsnit er underlagt hyppige ændringer og opdateringer, som er implementeret forbedringer til produktet.

  

## <a name="known-issues"></a>Kendte problemer

 - Du kan se følgende ikke afslutte fejl under installation, som påvirker installation succes:
     - "Ordet 'C:\WinRM\Start-Logging.ps1' genkendes ikke"
     - "Kalde EceAction: kan ikke indeksere til en null matrix" 
     - "InvokeEceAction: argument kan ikke bindes til parameter 'Meddelelse', fordi det er en tom streng."
 - Du kan få vist installation mislykkes på trin 60.61.93 med fejlen "Program med id 'URI' blev ikke fundet". Denne funktionalitet er på grund af den måde, programmer er registreret i Azure Active Directory.  Hvis du får vist denne fejl, fortsætte med at [køre installationsscriptet](azure-stack-rerun-deploy.md) fra trin 60.61.93 indtil installationen er fuldført.
 - Du får vist, at ressourcens **Tilgængelighed angive** på markedet vises under kategorien **virtualMachine ARM** – dette udseende er kun en kosmetiske problem.
 - Når du opretter en ny virtuel maskine i portalen, i det **grundlæggende** trin standard indstillingen lagerplads SSD.  Denne indstilling skal ændres til harddisk eller på trinnet **størrelse** i VM installation, kan du se ikke VM størrelser kan du vælge og fortsætte installation. 
 - Du får vist AzureRM PowerShell moduler installeres ikke længere som standard på MAS CON01 VM (i TP1 dette blev kaldet ClientVM). Denne funktionalitet er designet, fordi der ikke er en anden metode til at [installere disse moduler og oprette forbindelse](azure-stack-connect-powershell.md).  
 - Du vil se, at **Microsoft.Insights** ressource udbyder ikke automatisk er registreret for lejer abonnementer. Hvis du gerne vil se overvågningsdata for en VM installeres som en lejer, kan du køre følgende kommando fra PowerShell (efter at du [installerer og oprette forbindelse](azure-stack-connect-powershell.md) som en lejer): 
       
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Insights 

 - Vil du se eksportere funktionalitet i portalen for ressourcegrupper, men ingen tekst er viste og tilgængelig for Eksportér.      
 - Du kan starte en installation af lagerplads ressourcer, der er større end tilgængelige kvote.  Denne installation mislykkes, og ressourcerne, der konto suspenderet.  Der er to muligheder for afhjælpning:
     - Administratoren af tjenesten kan øge kvoten, selvom ændringer ikke skal træde i kraft med det samme og ofte gå op til en time at sprede.
     - Administratoren af tjenesten kan oprette et tilføjelsesprogram plan med flere kvote lejeren kan derefter føje til abonnementet.
 - Når du bruger på portalen til at oprette FOS på Azure stak miljøer med identitet i "Azure - Kina", vises ikke tilgængelige til at vælge trinnet **størrelse** i VM installation VM størrelser og vil ikke kunne fortsætte installation.
 - Når VM er faktisk installeret, kan du se fejl ved en installation i portalen.
 - Når du sletter en plan, tilbud, eller et andet abonnement, kan FOS ikke slettet.
 - Du får vist feltet VM filtypenavne på markedet.
 - Du kan ikke installere en VM fra en gemt VM billede.
 - Lejere kan se tjenester, som ikke medtages i deres abonnement.  Når lejere forsøger at installere disse ressourcer, modtager de en fejl.  Eksempel: Lejer abonnement omfatter kun lagerplads ressourcer.  Lejer får vist muligheden for at oprette andre ressourcer som FOS.  I dette scenarie, når en lejer forsøger at installere en VM, modtager de en meddelelse om VM ikke kan oprettes. 
 - Når du installerer TP2, skal du ikke aktiverer værten for OS i den virtuelle harddisk, hvor du kører scriptet Azure stak konfiguration, eller du modtager muligvis en fejl, messaging angivelse Windows snart udløber.


## <a name="deployment"></a>Installation

### <a name="deployment-failure"></a>Fejl ved installation
Hvis du får en fejl under installationen, Azure stak installationsprogrammet giver dig mulighed at fortsætte en mislykket installation ved at følge den [Kør installation trin](azure-stack-rerun-deploy.md).

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>I slutningen af installationen, PowerShell sessionen stadig er åben og vises ikke noget output

Denne funktionalitet er sandsynligvis lige resultatet af standardfunktionsmåden for et PowerShell-kommandovindue skal, når den er markeret. Konceptet installationen rent faktisk er fuldført, men scriptet blev afbrudt, når du vælger i vinduet. Du kan kontrollere, at det er tilfældet ved at søge efter ordet "select" i kommandovinduet titellinje.  Tryk på ESC for at fjerne markeringen af det, og meddelelsen fuldførelse skal vises efter den.

## <a name="templates"></a>Skabeloner

### <a name="azure-template-wont-deploy-to-azure-stack"></a>Azure skabelon installere ikke til Azure stak

Kontrollere, at:

- Skabelonen skal bruge en Microsoft Azure-tjeneste, der allerede er tilgængelige eller udskrift Azure stablede.
- API'er, der bruges til en bestemt ressource, der understøttes af den lokale Azure stak forekomst, og at du målretter en gyldig placering ("lokal" i Azure stak Technical Preview (TP) 2, vs "Af USA" eller "Syd Indien" i Azure).
- Du læse [denne artikel](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md) om Test-AzureRmResourceGroupDeployment-cmdletter, som fanger små forskelle i Azure ressourcestyring syntaks.

Du kan også bruge de Azure stak skabeloner, der er angivet i [GitHub lager](http://aka.ms/AzureStackGitHub/) kan hjælpe dig med at komme i gang.

## <a name="virtual-machines"></a>Virtuelle maskiner

### <a name="after-starting-my-microsoft-azure-stack-poc-host-all-my-tenants-vms-are-gone-from-hyper-v-manager-and-come-back-automatically-after-waiting-a-bit"></a>Når du starter vært for mit Microsoft Azure stak Konceptet, alle mine Lejeres FOS er blevet fjernet fra Hyper-V Manager og vende tilbage automatisk efter vente lidt?

Når systemet dukker op lagerplads undersystem og RPs har du brug for at finde ud af konsistens tilbage. Hvor lang tid der afhænger af hardwaren og specifikationer for bruges, men det kan være noget tid efter en genstart på værten for lejer FOS for vende tilbage og genkendes.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>Jeg har slettet nogle virtuelle maskiner, men stadig se de Virtuelle filer på disken. Forventes problemet?

Ja, denne funktionsmåde er forventet. Det er designet på denne måde, fordi:

- Når du sletter en VM, slettes ikke virtuelle harddiske. Diske er separat ressourcer i ressourcegruppen.
- Når en lagerplads konto bliver slettet, sletningen er synligt med det samme via Azure ressourcestyring (portal, PowerShell), men det kan indeholde diskene holdes stadig på lager, indtil Spildopsamling kører.

Hvis du kan se virtuelle harddiske "enkeltstående", er det vigtigt at vide, hvis de er en del af mappen for en lagerplads-konto, som er blevet slettet. Hvis lagerplads kontoen ikke blev slettet, er det normale de findes stadig.

Du kan læse mere om at konfigurere opbevaring grænseværdi i [administrere lagerplads konti](azure-stack-manage-storage-accounts.md).

## <a name="installation-steps"></a>Installationstrin
Følgende oplysninger om Azure stak installationstrin kan være nyttige til fejlfinding:  

| Indeks | Navn | Beskrivelse|
| ----- | ----- | -----|
|0.11 | (DEP) Validere fysiske computere | Validering af hardware og OS konfiguration på de fysiske noder. |
| 0.12 | (DEP) Konfigurere fysiske maskiner netværk til Konceptet | Konfiguration af virtuelt netværk parametre og grænseflader. |
| 0.14 | (DEP) Installere domæne | Installere Active Directory på virtuel maskine. |
| 0,15 | (DEP) Konfigurere domæne-serveren | Konfigurere domænet med sikkerhedsgrupper osv. |
| 0,16 | (DEP) Konfigurere fysisk maskine | Konfigurere netværk, deltage i et domæne og konfiguration af lokale administratorer. |
| 0.18 | (ST) Konfigurere lagerplads klynge | Oprette lagerplads klynge, oprette en lagerplads puljen og fil server. |
| 0.19 | CPI) Konfiguration af strukturen infrastruktur | Konfigurere forudsætninger for installation af struktur. |
| 0.21 | (NETTOOVERSIGT) Konfigurer BGP og NAT | Installerer BGP og NAT - bruges kun til en Node. |
| 0.22 | (NETTOOVERSIGT) Konfigurere NAT og tidsserver | Synkroniserer tidsserveren og konfigurerer NAT poster. |
| 40.41 | CPI) Oprette gæst FOS | Oprette management FOS. |
| 40.42 | (FBI) Konfigurere PowerShell JEA | Konfigurere PowerShell JEA for alle roller. |
| 40.43 | (FBI) Konfigurere Azure stak nøglecenter | Installerer Azure stak nøglecenter. |
| 40.44 | (FBI) Konfigurere Azure stak nøglecenter | Konfigurerer Azure stak nøglecenter. |
| 40.45 | (NETTOOVERSIGT) Konfigurere AK på FOS | Installerer AK på gæst FOS |
| 40.46 | (NETTOOVERSIGT) Konfigurere AK på FOS | Konfigurere AK på gæst FOS |
| 40.47 | (NETTOOVERSIGT) Konfigurere gæst FOS | Konfigurere management FOS med AK ACLs. |
| 60.61.81 | (FBI) Installere Azure stak strukturerede Ring Services - FabricRing betingelse | Opretter VIPs for FabricRing |
| 60.61.82 | (FBI) Installere Azure stak strukturerede Ring Services - installere strukturen Ring klynge | Installerer og konfigurerer Azure stak strukturen Ring klynge. |
| 60.61.83 | (FBI) Installere Admin filtypenavne for ressource udbydere | Hvordan du installerer administrator Extensions for ressource udbydere |
| 60.61.84 | (ACS) Konfigurere Azure-ensartet lagerplads i nodeniveau. | Installerer og konfigurerer Azure-ensartet lagerplads i nodeniveau. |
| 60.61.85 | (ACS) Konfigurere Azure-ensartet lagerplads i klynge niveau. | Installerer og konfigurerer Azure-ensartet lagerplads i klynge niveau. |
| 60.61.86 | (FBI) Installere Azure stak strukturerede Ring Controller Services - betingelse | Forudsætninger for InfraServiceController |
| 60.61.87 | (FBI) Installere Azure stak strukturerede Ring Controller Services - betingelse | Forudsætninger for CPI |
| 60.61.88 | (FBI) Installere Azure stak strukturerede Ring Controller Services - betingelse | Forudsætninger for ASAppGateway |
| 60.61.89 | (FBI) Installere Azure stak strukturerede Ring Controller Services - betingelse | Forudsætninger for lagerplads Controller |
| 60.61.90 | (FBI) Installere Azure stak strukturerede Ring Controller Services - betingelse | Forudsætninger for HealthMonitoring |
| 60.61.91 | (FBI) Installere Azure stak strukturerede Ring Controller Services - betingelse | Forudsætninger for Regulativ |
| 60.61.92 | (FBI) Installere Azure stak strukturerede Ring Controller Services - betingelse | Forudsætninger for PMM |
| 60.61.93 | (Katal) Oprette principper for AzureStack Service | Oprette Azure Graph programmer og principper for tjenesten i AAD. |
| 60.61.94 | (NETTOOVERSIGT) Konfiguration af GW FOS | Installerer GW på gæst FOS. |
| 60.61.95 | (NETTOOVERSIGT) Konfigurere GW FOS | Konfigurerer GW på gæst FOS. |
| 60.61.96 | (NETTOOVERSIGT) Installere IDN'er på værter | Installere IDN'er på infrastruktur værter |
| 60.61.97 | (NETTOOVERSIGT) Konfigurere IDN'er | Konfigurere IDN'er rolle |
| 60.61.98 | (FBI) Konfiguration af WSUS FOS | Installerer WSUS-serveren på gæst FOS. |
| 60.61.99 | (FBI) Konfigurere WSUS FOS | Konfigurerer WSUS-serveren for gæst FOS. |
| 60.61.100 | (FBI) Konfigurere Azure SQL FOS | Installerer Azure SQL server på gæst FOS |
| 60.61.101 | (Katal) Konfigurere forudsætninger for blev FOS. | Konfigurerer forudsætninger for Microsoft Azure stak på gæst FOS. |
| 60.61.102 | (Katal) Installationsprogrammet kunne FOS | Installerer Microsoft Azure stak på gæst FOS. |
| 60.120.121 | (FBI) Installere ressource udbydere og enheder | Installationer ressource udbydere og enheder |
| 60.120.121 | (FBI) Installere ressource udbydere og enheder | Installationer ressource udbydere og enheder |
| 60.120.121 | (FBI) Installere ressource udbydere og enheder | Installationer ressource udbydere og enheder |
| 60.120.121 | (FBI) Installere ressource udbydere og enheder | Installationer ressource udbydere og enheder |
| 60.120.121 | (FBI) Installere ressource udbydere og enheder | Installationer ressource udbydere og enheder |
| 60.120.121 | (FBI) Installere ressource udbydere og enheder | Installationer ressource udbydere og enheder |
| 60.120.121 | (FBI) Installere ressource udbydere og enheder | Installationer ressource udbydere og enheder |
| 60.120.121 | (FBI) Installere ressource udbydere og enheder | Installationer ressource udbydere og enheder |
| 60.120.122 | (FBI) Controller konfiguration | Konfigurerer enheder |
| 60.120.123 | (Katal) Konfigurere blev FOS | Konfigurerer Microsoft Azure stak på gæst FOS. |
| 60.120.124 | (Katal) Azure stak AAD konfiguration. | Konfigurerer Azure stak med Azure AD. |
| 60.120.125 | (Katal) Installere ADFS | Installerer Active Directory Federation Services (ADFS) |
| 60.120.126 | (Katal) Installere ADFS/Graph | Azure stak Graph-installationer |
| 60.120.127 | (Katal) Konfigurere ADFS | Konfigurerer Active Directory Federation Services (ADFS) |
| 60.140.141 | (FBI) Konfigurere vejledende pris | Konfigurerer Storage ressource Provider |
| 60.140.142 | (ACS) Konfigurere Azure-ensartet Storage. | Konfigurerer Azure-ensartet lagerplads. |
| 60.140.143 | (FBI) Oprette lagerplads konti | Oprette alle lagerplads konti der skal bruges i forskellige udbydere. |
| 60.140.144 | (FBI) Registrere brug til vejledende pris | Registrere brug for lagerplads udbyder. |
| 60.140.145 | CPI) Overføre oprettede FOS, værter og klynge til CPI | Overfører objekter af oprettede FOS, værter og klynge til CPI |
| 60.140.146 | (FBI) Konfigurere Windows Defender | Konfigurerer Windows Defender |
| 60.160.161 | (MÅN) Konfigurere overvågning Agent | Konfigurerer overvågning Agent |
| 60.160.162 | (FBI) NRP betingelse | Installationer NRP forudsætninger |
| 60.160.163 | (FBI) NRP installation | Installationer NRP  |
| 60.160.164 | (FBI) NRP konfiguration | Konfigurerer NRP |
| 60.160.165 | (FBI) Kapacitetsplanlægning betingelse | Installationer Kapacitetsplanlægning forudsætninger |
| 60.160.166 | (FBI) Kapacitetsplanlægning installation | Installationer Kapacitetsplanlægning |
| 60.160.167 | (FBI) Kapacitetsplanlægning konfiguration | Konfigurerer Kapacitetsplanlægning |
| 60.160.168 | (FBI) FRP betingelse | Installationer FRP forudsætninger |
| 60.160.169 | (FBI) FRP installation | Installationer FRP  |
| 60.160.170 | (FBI) FRP konfiguration | Konfigurerer FRP |
| 60.160.174 | (FBI) URP nødvendige | Installerer URP forudsætninger |
| 60.160.175 | (FBI) URP installation | Installerer URP  |
| 60.160.176 | (FBI) URP konfiguration | Konfigurerer URP |
| 60.160.171 | (FBI) HRP betingelse | Installationer HRP forudsætninger |
| 60.160.172 | (FBI) HRP installation | Installationer HRP  |
| 60.160.173 | (FBI) HRP konfiguration | Konfigurerer HRP |
| 60.160.177 | (KV) KeyVault betingelse | Installationer KeyVault forudsætninger |
| 60.160.178 | (KV) KeyVault installation | Installerer KeyVault  |
| 60.160.179 | (KV) KeyVault konfiguration | Konfigurerer KeyVault | 
| 60.190.191 | (FBI) Konfigurere galleriet | Konfigurere galleriet |
| 60.190.192 | (FBI) Konfigurere strukturerede Ring Services | Konfigurere strukturerede Ring Services |
| 60.221 | (FBI) Konfiguration af konsol FOS | Installerer Console server på gæst FOS. |
| 60.222 | (FBI) Konfiguration af konsol FOS | Flyt DVM indhold til konsollen VM. |
| 251 | Forberede til fremtidige host genstarter | Angiv genstart politikken |


## <a name="next-steps"></a>Næste trin

[Ofte stillede spørgsmål](azure-stack-FAQ.md)
