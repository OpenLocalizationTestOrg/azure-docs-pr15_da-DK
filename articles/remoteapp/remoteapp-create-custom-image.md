<properties
    pageTitle="Sådan oprettes en brugerdefineret skabelonbillede til Azure RemoteApp | Microsoft Azure"
    description="Lær at oprette en brugerdefineret skabelon afbildning til Azure RemoteApp. Du kan bruge denne skabelon med en hybrid eller skyen af websteder."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016" 
    ms.author="elizapo"/>

# <a name="how-to-create-a-custom-template-image-for-azure-remoteapp"></a>Sådan oprettes en brugerdefineret skabelonbillede til Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Azure RemoteApp bruger et billede af Windows Server 2012 R2 skabelon til at hoste alle de programmer, du vil dele med dine brugere. For at oprette en brugerdefineret RemoteApp skabelon afbildning, kan du starte med et eksisterende billede eller oprette en ny. 


> [AZURE.TIP] Vidste du, at du kan oprette et billede fra en Azure VM? SAND historie, og det klipper på mængden af tid det tager at importere billedet. Se trinnene [her](remoteapp-image-on-azurevm.md).

Krav til det billede, der kan overføres til brug sammen med Azure RemoteApp er:


- Billedstørrelsen skal være et multiplum af MB. Hvis du forsøger at overføre et billede, der ikke er et præcist multiplum, mislykkes overførslen.
- Billedets størrelse skal være 127 GB eller mindre.
- Det skal være på en Virtuelle fil (VHDX filer [Hyper-V virtuelle harddiske] understøttes ikke i øjeblikket).
- Den virtuelle harddisk må ikke være et virtuelt generering af 2.
- Den virtuelle harddisk kan være enten fast størrelse eller dynamisk udvidelse. En dynamisk voksende Virtuelle anbefales, da det tager mindre tid at overføre til Azure end en fast størrelse Virtuelle fil.
- Disken skal være initialiseret ved hjælp af den start post MBR (Master) partitionering typografi. Typografien GUID partition GPT (table) partition understøttes ikke.
- Den virtuelle harddisk skal indeholde en enkelt installation af Windows Server 2012 R2. Det kan indeholde flere enheder, men kun én, der indeholder en installation af Windows.
- Rollen Remote Desktop Session Host (RDSH) og computeroplevelsesfunktionen skal være installeret.
- Rollen Remote Desktop Connection Broker skal *ikke* installeres.
- Den kryptere File System (EFS) skal være deaktiveret.
- Billedet skal være blevet behandlet med Sysprep ved hjælp af parametrene **/oobe / generalize/shutdown** (må ikke bruge parameteren **/mode:vm** ).
- Overføre din Virtuelle fra en snapshot kæde understøttes ikke.


**Inden du går i gang**

Du skal gøre følgende, før du opretter tjenesten:

- [Tilmelde dig](https://azure.microsoft.com/services/remoteapp/) til RemoteApp.
- Oprette en brugerkonto i Active Directory for at bruge som RemoteApp tjenestekontoen. Begræns tilladelser for denne konto, så den kan kun deltage computere til domænet. Du kan få flere oplysninger i [Konfigurere Azure Active Directory for RemoteApp](remoteapp-ad.md) .
- Indsamle oplysninger om dit lokale netværk: IP-adresse, oplysninger og detaljer om VPN-enhed.
- Installer [Azure PowerShell](../powershell-install-configure.md) -modulet.
- Indsamle oplysninger om de brugere, du vil give adgang til. Det kan være enten Microsoft-kontooplysninger eller Active Directory arbejde kontooplysninger for brugere.



## <a name="create-a-template-image"></a>Oprette et billede af designskabelon ##

Dette er de overordnede trin til at oprette et nyt skabelonbillede fra bunden:

1.  Find et billede af Windows Server 2012 R2 opdatering DVD eller ISO.
2.  Oprette en Virtuelle fil.
4.  Installere Windows Server 2012 R2.
5.  Installere rollen Remote Desktop Session Host (RDSH) og funktionen computeroplevelse.
6.  Installere yderligere funktioner, der kræves, før dine programmer.
7.  Installere og konfigurere dine programmer. Tilføj en hvilken som helst apps eller -programmer, du vil dele til menuen **Start** af billedet, især i **%systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programs for at gøre det nemmere at Deling apps.
8.  Udføre eventuelle yderligere Windows konfigurationer, der kræves, før dine programmer.
9.  Deaktivere kryptering filsystemet (genoprettelse af krypterede data).
10. **Nødvendig:** Gå til Windows Update, og Installer alle vigtige opdateringer.
9.  SYSPREP billedet.

Den detaljerede vejledning til at oprette et nyt billede er:

1.  Find et billede af Windows Server 2012 R2 opdatering dvd'en eller ISO.
2.  Oprette en Virtuelle fil ved hjælp af Diskadministration.
    1.  Start Disk Management (diskmgmt.msc).
    2.  Oprette en dynamisk voksende Virtuelle af 40 GB eller mere i størrelse. (Anslå mængden af plads til Windows, programmer og tilpasninger. Windows Server med RDSH rolle og computeroplevelsesfunktionen installeret kræver om 10 GB mellemrum).
        1.  Klik på **handling > oprette Virtuelle**.
        2.  Angiv den placering, størrelse og Virtuelle format. Vælg **dynamisk udvidelse**, og klik derefter på **OK**.

            Dette vil køre i flere sekunder. Når Virtuelle oprettelse er fuldført, skal du se en ny disk uden en hvilken som helst bogstav og "Ikke initialiseret" midlertidigt i Disk Management console.

        - Højreklik på disken (ikke diskplads), og klik derefter på **Initialiseret Disk**. Markér **MBR** (Master Start Record) som typografien partition, og klik derefter på **OK**.
        - Oprette en ny enhed: skal du højreklikke på den diskplads, og klik derefter på **Ny simpel lydstyrken**. Du kan acceptere standardindstillingerne i guiden, men Sørg for, at du tildele et bogstav til at undgå mulige problemer, når du overfører skabelon billedet.
        - Højreklik på disken, og klik derefter på **Fjern virtuel harddisk**.





1. Installere Windows Server 2012 R2:
    1. Oprette en ny virtuel maskine. Brug guiden Ny virtuelt i Hyper-V Manager eller klient Hyper-V.
        1. På siden Angiv generering af skal du vælge **generering af 1**.
        2. Vælg **Brug en eksisterende virtuel harddisk**på siden oprette forbindelse virtuel harddisk, og gå til den virtuelle harddisk, du har oprettet i ovenstående trin.
        2. Vælg **installere et operativsystem fra en start CD/DVD_ROM**på siden Indstillinger for Installation, og vælg derefter placeringen af Windows Server 2012 R2 installationsmedier.
        3. Vælg andre indstillinger i guiden, som er nødvendige for at installere Windows og dine programmer. Har afsluttet guiden.
    2.  Når guiden afsluttes, skal redigere indstillingerne for den virtuelle maskine og foretag eventuelle andre ændringer, der er nødvendige for at installere Windows og dine programmer, som antallet processorer, virtuelle, og klik derefter på **OK**.
    4.  Oprette forbindelse til den virtuelle maskine og installere Windows Server 2012 R2.
1. Installere rollen Remote Desktop Session Host (RDSH) og funktionen computeroplevelse:
    1. Start Server Manager.
    2. Klik på **Tilføj roller og funktioner** på velkomstsiden eller i menuen **Administrer** .
    3. Klik på **Næste** på siden inden du går i gang.
    4. Vælg **rollebaseret eller funktion-baserede installation**, og klik derefter på **Næste**.
    5. Vælg den lokale computer på listen, og klik derefter på **Næste**.
    6. Vælg **Remote Desktop Services**, og klik derefter på **Næste**.
    7. Udvid **brugergrænseflader og infrastruktur** og vælg **Computeroplevelse**.
    8. Klik på **Tilføj funktioner**, og klik derefter på **Næste**.
    9. Klik på **Næste**på siden Remote Desktop Services.
    10. Klik på **Remote Desktop sessionsværten**.
    11. Klik på **Tilføj funktioner**, og klik derefter på **Næste**.
    12. Vælg **Genstart destinationsserveren automatisk om nødvendigt**på siden Bekræft installation valg, og klik derefter på **Ja** i advarslen genstart.
    13. Klik på **Installer**. Vil du genstarte computeren.
1.  Installere yderligere funktioner, der kræves, før dine programmer, som .NET Framework 3.5. For at installere funktionerne, du Kør tilføje roller og guiden funktioner.
7.  Installere og konfigurere de programmer, du vil publicere gennem RemoteApp.

>[AZURE.IMPORTANT]
>
>Installere rollen RDSH før du installerer programmer for at sikre, at eventuelle problemer med kompatibilitet programmer er blevet fundet før billedet er overført til RemoteApp.
>
>Kontrollér, at en genvej til dit program (**.lnk** -fil) vises i menuen **Start** for alle brugere (%systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programs). Også sikre, at det ikon, du ser i menuen **Start** er, hvad du skal kunne se. Hvis ikke, skal du ændre den. (Du ikke *har* tilføje programmet til starten menuen, men det gør det meget nemmere at udgive programmet i RemoteApp. Ellers skal skal du angive installationsstien for programmet, når du publicerer app'en.)


8.  Udføre eventuelle yderligere Windows konfigurationer, der kræves, før dine programmer.
9.  Deaktivere kryptering filsystemet (genoprettelse af krypterede data). Kør følgende kommando på en øgede kommandovindue:

        Fsutil behavior set disableencryption 1

    Du kan også angive eller tilføje følgende DWORD-værdi i registreringsdatabasen:

        HKLM\System\CurrentControlSet\Control\FileSystem\NtfsDisableEncryption = 1
9.  Hvis du opretter dit billede i en Azure virtuelt, omdøbe den ** \%windir%\Panther\Unattend.xml** fil, som blokeres Overfør scriptet bruges senere fra arbejde. Ændre navnet på denne fil til Unattend.old, så du stadig har filen i tilfælde af, at du vil vende tilbage til din installation.
10. Gå til Windows Update, og Installer alle vigtige opdateringer. Du skal køre Windows Update flere gange for at hente alle opdateringer. (Nogle gange kan du installere en opdatering, og selve opdateringen kræver en opdatering).
10. SYSPREP billedet. På en kommandoprompt ved at køre følgende kommando:

    **C:\Windows\System32\sysprep\sysprep.exe / generalize/Oobe / Shutdown**

    **Note:** Brug ikke parameteren **/mode:vm** af kommandoen SYSPREP, selv om dette er en virtuel maskine.


## <a name="next-steps"></a>Næste trin ##
Nu hvor du har brugerdefineret skabelon billedet, skal du overføre billedet til din RemoteApp af websteder. Brug oplysningerne i følgende artikler til at oprette din af websteder:


- [Sådan oprettes en hybrid samling af RemoteApp](remoteapp-create-hybrid-deployment.md)
- [Sådan oprettes en skyen samling af RemoteApp](remoteapp-create-cloud-deployment.md)
 