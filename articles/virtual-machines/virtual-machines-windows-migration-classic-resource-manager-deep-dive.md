<properties
    pageTitle="Tekniske undersøgelse på platform understøttes overførsel fra klassisk til Azure ressourcestyring | Microsoft Azure"
    description="I denne artikel indeholder en teknisk undersøgelse på platform understøttes overførsel af ressourcer fra klassisk til Azure ressourcestyring"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="kasing"/>

# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Tekniske undersøgelse på platform understøttes overførsel fra klassisk til Azure ressourcestyring
Lad os en gå i dybden på overførsel fra Azure klassisk implementeringsmodel til implementeringsmodel Azure ressourcestyring. Vi se på ressourcer på en ressource og funktion niveau kan hjælpe dig med at forstå, hvordan Azure-platformen overfører ressourcer mellem to installation-modellerne. Få mere at vide ved at læse artiklen service: [Platform understøttes overførsel af IaaS ressourcer fra klassisk til Azure ressourcestyring](virtual-machines-windows-migration-classic-resource-manager.md).

## <a name="detailed-guidance-on-migration"></a>Detaljerede retningslinjer for overførsel

Du kan finde klassiske og ressourcestyring repræsentationer for ressourcerne, i den følgende tabel. Andre funktioner og ressourcer understøttes ikke i øjeblikket.

| Klassisk repræsentation                                 | Ressourcestyring repræsentation                 | Detaljerede noter                                                                                                                                                                                                                                                                                                                                                                                                                                                      
|--------------------------------------------------------|-------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Navn på skyen tjeneste                                     | DNS-navn                                        | Under overflytningen, oprettes en ny ressourcegruppe til hver skytjeneste med naming mønsteret `<cloudservicename>-migrated`. Denne ressourcegruppe indeholder alle dine ressourcer. Navnet på skyen tjenesten bliver et DNS-navn, der er knyttet til den offentlige IP-adresse.                                                                                                                                                                                                    |   
| Virtuelt                                        | Virtuelt                                 | VM-specifikke egenskaber overføres uændret. Visse osProfile oplysninger, som computernavn, gemmes ikke i den klassiske implementeringsmodel og forbliver tomme efter overførslen.                                                                                                                                                                                                                                                                |   
| Diskressourcer, der er knyttet til VM                          | Implicit knyttet til VM diske                   | Diske er ikke tilpasset som på øverste niveau ressourcer i implementeringsmodel ressourcestyring. De er overført som implicit diske under VM. Kun diske, der er knyttet til en VM understøttes i øjeblikket. Ressourcestyring FOS kan nu bruge klassisk lagerplads konti, som giver mulighed for disk, der overføres nemt uden nogen opdateringer. |   
| VM filtypenavne                                          | VM filtypenavne                                   | Alle de ressource udvidelser, undtagen XML-udvidelser, overføres fra den klassiske implementeringsmodel.                                                                                                                                                                                                                                                                                                                                                                        |   
| Virtuelt certifikater                           | Certifikater i Azure vigtige samling                 | Hvis en skybaseret tjeneste indeholder service certifikater, en ny Azure vigtige samling per skybaseret tjeneste og flyttes certifikater til den vigtige samling af legitimationsoplysninger. FOS opdateres for at referere til certifikater fra den vigtige samling af legitimationsoplysninger.                                                                                                                                                                                                                               |  
| WinRM konfiguration                                    | WinRM konfiguration under osProfile             | Windows Fjernadministration-konfiguration flyttes uændret som en del af overførslen.                                                                                                                                                                                                                                                                                                                                                                                            |   
| Tilgængelighed – Angiv egenskab                              | Tilgængelighed sæt ressource                       | Specifikation af tilgængelighed sæt blev en egenskab på VM i modellen Klassisk installation. Tilgængelighed sæt bliver en på øverste niveau ressource som en del af overførslen. Følgende konfigurationer understøttes ikke: flere tilgængelighed angiver per skybaseret tjeneste, eller en eller flere tilgængelighed angiver sammen med VM'er, der ikke er i en hvilken som helst tilgængelighed, der er angivet i en skybaseret tjeneste.                                                                                |   
| Konfiguration af netværk på en VM                          | Grænsefladen primære netværk                       | Konfiguration af netværk på en VM repræsenteres som den primære netværk interface ressource efter overførslen. For VM'er, der ikke er i et virtuelt netværk, ændrer den interne IP-adresse under overførsel.                                                                                                                                                                                                                                                            |   
| Flere netværksgrænseflader på en VM                    | Netværksgrænseflader                              | Hvis en VM har flere netværkgrænseflader, der er knyttet til den, bliver hver netværksgrænseflade en på øverste niveau ressource som en del af overførslen i implementeringsmodel ressourcestyring sammen med alle egenskaberne.                                                                                                                                                                                                                                                            |  
| Netværksbelastningen slutpunkt sæt                             | Justering af belastning                                   | I modellen Klassisk installation tildeles platformen en implicit justering af belastning for hver tjeneste i skyen. Under overflytningen, der oprettes en ny belastningsjustering ressource, og sættet justering af belastning slutpunkt bliver belastningsjustering regler.                                                                                                                                                                                                                                     |   
| Indgående NAT regler                                      | Indgående NAT regler                               | Indtast slutpunkter, der er defineret i VM konverteres til indgående netværk adresse oversættelse regler under belastning under overførslen.                                                                                                                                                                                                                                                                                                                                           |  
| VIP-adresse                              | Offentlige IP-adresse med DNS-navn                 | Den virtuelle IP-adresse bliver til en offentlig IP-adresse og er knyttet til justering af belastning.                                                                                                                                                                                                                                                                                                                                                        |   
| Virtuelt netværk                                        | Virtuelt netværk                                 | Det virtuelle netværk er overført med alle dens egenskaber til implementeringsmodel ressourcestyring. Der oprettes en ny ressourcegruppe med navnet `-migrated`. Der er [ikke-understøttede konfigurationer](virtual-machines-windows-migration-classic-resource-manager.md).                                                                                                                                                                                                  |   
| Reserverede IP'er                                           | Offentlige IP-adresse med metoden statisk allokering  | Reserverede IP'er, der er knyttet til justering af belastning overføres sammen med overførsel af skytjenesten eller den virtuelle maskine. Ikke-tilknyttede reserveret IP-overførsel understøttes ikke i øjeblikket.                                                                                                                                                                                                                                                           |   
| Offentlige IP-adresse per VM                               | Offentlige IP-adresse med metoden dynamisk fordeling | Den offentlige IP-adresse, der er knyttet til VM konverteres som en offentlige IP-adresseressource med metoden for fordeling indstillet til statisk.                                                                                                                                                                                                                                                                                                                                   |   
| NSGs                                | NSGs                         | Netværk sikkerhedsgrupper, der er knyttet til et undernet sammenkædes som en del af overflytningen til implementeringsmodel ressourcestyring. NSG i den klassiske implementeringsmodel fjernes ikke under overførslen. Handlingerne management-plan til NSG er dog blokeret, når overførslen er i gang.                                                                                                                                                                             |  
| DNS-servere                                            | DNS-servere                                     | DNS-servere, der er knyttet til et virtuelt netværk eller VM overføres som en del af den tilsvarende migrering, ressource, sammen med alle egenskaberne.                                                                                                                                                                                                                                                                                                                    |   
| UDRs                                    | UDRs                             | Brugerdefinerede omdirigerer, der er knyttet til et undernet sammenkædes som en del af overflytningen til implementeringsmodel ressourcestyring. UDR i den klassiske implementeringsmodel fjernes ikke under overførslen. Handlingerne management-plan til UDR blokeres, når overførslen er i gang.                                                                                                                                                                             |   
| IP-adresse videresendelse egenskab for en VM netværkskonfiguration | IP-adresse for viderestilling af egenskaben i NIC               | IP viderestilling af egenskaben i en VM konverteres til en egenskab på netværksgrænsefladen under overførslen.                                                                                                                                                                                                                                                                                                                                                           |   
| Justering af belastning med flere IP'er                        | Justering af belastning med flere offentlige IP-ressourcer | Alle offentlige IP-adresse, der er knyttet til justering af belastning er konverteret til en offentlig IP-ressource og der er knyttet til justering af belastning efter overførslen.                                                                                                                                                                                                                                                                                                       |   
| Interne DNS-navne på VM                           | Interne DNS-navne på NIC                    | Under overflytningen overføres de interne DNS-suffikser for FOS til en skrivebeskyttet egenskab med navnet "InternalDomainNameSuffix" på NIC. Suffikset forbliver uændret efter overførslen og VM opløsning skal fortsætte med at arbejde som tidligere.                                                                                                                                                                                                           |   

## <a name="illustration-of-a-simple-migration-walkthrough"></a>Illustration af en simpel overførsel gennemgang

Følgende skærmbillede viser en eksisterende skytjeneste med en VM (ikke i et virtuelt netværk) efter forberedelse fasen:

![Klassisk repræsentation efter forberede](./media/virtual-machines-windows-migration-classic-resource-manager/classic-migration-prepare-portal.png)

Når overførselsprocessen er fuldført, vises følgende skærmbilleder, de nye ressourcer, der er oprettet i en ny ressourcegruppe: ![ressourcestyring repræsentation efter forberede](./media/virtual-machines-windows-migration-classic-resource-manager/resourcemanager-migration-prepare-portal.png)

## <a name="next-steps"></a>Næste trin

Nu hvor du har forstået overførslen af klassisk IaaS ressourcer til Ressourcestyring, kan du starte overflytningen ressourcer.

- [Bruge PowerShell til at overføre IaaS ressourcer fra klassisk til Azure ressourcestyring](virtual-machines-windows-ps-migration-classic-resource-manager.md)
- [Brug CLI til at overføre IaaS ressourcer fra klassisk til Azure ressourcestyring](virtual-machines-linux-cli-migration-classic-resource-manager.md)
- [Platform understøttes overførsel af IaaS ressourcer fra klassisk til Azure ressourcestyring](virtual-machines-windows-migration-classic-resource-manager.md)
- [Klone en klassisk virtuel maskine til Azure ressourcestyring ved hjælp af PowerShell-scripts, community](virtual-machines-windows-migration-scripts.md)