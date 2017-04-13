<properties
    pageTitle="Fejlfinding af problemer med Azure fil lagerplads | Microsoft Azure"
    description="Fejlfinding af problemer med Azure fil lagerplads"
    services="storage"
    documentationCenter=""
    authors="genlin"
    manager="felixwu"
    editor="na"
    tags="storage"/>

<tags
    ms.service="storage"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="genli"/>

# <a name="troubleshooting-azure-file-storage-problems"></a>Foretage fejlfinding af problemer med lagerplads til Azure-fil

I denne artikel indeholder almindelige problemer, der er relateret til Microsoft Azure fillagring, når du opretter forbindelse fra Windows og Linux klienter. Det også indeholder mulige årsager og løsninger til disse problemer.

**Generelle problemer (forekommer i både Windows og Linux-klienter)**

- [Kvote for fejl, når du forsøger at åbne en fil](#quotaerror)

- [Langsom ydeevne, når du får adgang til Azure fillagring fra Windows eller Linux](#slowboth)

**Problemer med Windows-klienten**

- [Langsom ydeevne, når du får adgang til Azure fillagring fra Windows 8.1 eller Windows Server 2012 R2](#windowsslow)

- [Fejl 53 forsøg på at tilslutte en Azure filshare](#error53)

- [NET Brug fuldføres, men jeg kan ikke se filen Azure dele tilsluttede i Windows Stifinder](#netuse)

- [Min lagerplads konto indeholder "/" og på internettet bruge kommandoen mislykkes](#slashfails)

- [Min programtjenester kan ikke få adgang til Azure filer drev.](#accessfiledrive)

- [Flere anbefalinger til optimering af ydeevne](#additional)

**Problemer med Linux-klient**

- ["Du vil kopiere en fil til en destination, der ikke understøtter kryptering" fejl under overførsel/kopiering af filer til Azure-filer](#encryption)

- ["Værten er nede" fejl på en eksisterende fil deler eller shell hænger, når du udfører liste over kommandoer på tilslutningspunktet](#errorhold)

- [Tilslutte fejl 115, når du forsøger at tilslutningen Azure filer på Linux VM](#error15)

- [Linux VM problemer tilfældige forsinkelser i kommandoer som "ls"](#delayproblem)

## <a name="general-problems"></a>Generelle problemer
<a id="quotaerror"></a>
### <a name="quota-error-when-trying-to-open-a-file"></a>Kvote for fejl, når du forsøger at åbne en fil

I Windows modtager du fejlmeddelelser, der ligner følgende:

**1816 ERROR_NOT_ENOUGH_QUOTA <> – 0xc0000044**

**STATUS_QUOTA_EXCEEDED**

**Der er ikke nok kvote er tilgængelig til at udføre denne kommando**

**Ugyldig handleværdi GetLastError: 53**

På Linux som, modtager du fejlmeddelelser, der ligner følgende:

**<filename>[adgang nægtet]**

**Disken kvoten er overskredet**

#### <a name="cause"></a>Årsag

Problemet skyldes, at du har nået den øvre grænse for samtidige Åbn håndtag, der er tilladt for en fil.

#### <a name="solution"></a>Løsning

Reducer antallet af samtidige åbne handler ved at lukke nogle håndtag, og prøv derefter igen. Se [Microsoft Azure lagerplads ydeevne og skalerbarhed tjekliste](storage-performance-checklist.md)kan finde flere oplysninger.

<a id="slowboth"></a>
### <a name="slow-performance-when-accessing-file-storage-from-windows-or-linux"></a>Langsom ydeevne, når du åbner fillagring fra Windows eller Linux

- Hvis du ikke har en bestemt i/o-størrelse minimumkrav, anbefaler vi, at du bruger 1 MB som i/o-størrelsen for optimal ydeevne.

- Hvis du kender den endelige størrelse af en fil, som du udvider med skrivning, og softwaren ikke har kompatibilitetsproblemer, når den endnu ikke er skrevet hale på den fil, der indeholder nuller, skal du angive størrelsen på forhånd i stedet for hver Skriv der et udvidelse skal du skrive.

## <a name="windows-client-problems"></a>Problemer med Windows-klienten
<a id="windowsslow"></a>
### <a name="slow-performance-when-accessing-the-file-storage-from-windows-81-or-windows-server-2012-r2"></a>Langsom ydeevne, når du åbner fillagring fra Windows 8.1 eller Windows Server 2012 R2

Sørg for, at denne programrettelse [KB3114025](https://support.microsoft.com/kb/3114025) er installeret for klienter, der kører Windows 8.1 eller Windows Server 2012 R2. Denne programrettelse forbedrer Opret, og Luk håndtaget ydeevne.

Du kan køre følgende script for at kontrollere, om denne programrettelse er blevet installeret på:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Hvis programrettelse er installeret, vises følgende output:

**HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies**

**{96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1**

> [AZURE.NOTE]  Windows Server 2012 R2 billeder i Azure Marketplace har denne programrettelse KB3114025 installeret som standard starter i December 2015.

<a id="additional"></a>
### <a name="additional-recommendations-to-optimize-performance"></a>Flere anbefalinger til optimering af ydeevne

Aldrig Opret eller Åbn en fil til cachelagrede I/O, der anmoder om skriveadgang, men ikke læseadgang. Det vil sige, når du ringe til **CreateFile()**, aldrig angive kun **GENERIC_WRITE**, men altid angive **GENERIC_READ | GENERIC_WRITE**. Kun skrive styr kan ikke cachelagre small skriver lokalt, selvom det er kun åbne håndtaget til filen. Dette er sat, en dårlige ydeevnen på små skriver. Bemærk, at "en" tilstanden at CRT- **fopen()** åbnes kun skrive styr.

<a id="error53"></a>
### <a name="error-53-when-you-try-to-mount-or-unmount-an-azure-file-share"></a>"Fejl 53" Når du forsøger at tilslutte eller afbryde en Azure filshare

Dette problem kan skyldes følgende betingelser:

#### <a name="cause-1"></a>Årsag 1

"Der opstod Systemfejl 53. Adgang nægtet." Forbindelser til Azure filshares blokeres af sikkerhedsmæssige årsager, hvis kommunikationskanalen ikke er krypteret og forbindelse ikke er oprettet på baggrund af den samme datacenter, som Azure filshares er placeret. Kommunikation kanal kryptering kan ikke, hvis brugerens klient OS ikke understøtter kryptering af små og mellemstore virksomheder. Dette er angivet med en "System 53 der opstod fejl. Adgang nægtet"fejlmeddelelse, når en bruger forsøger at tilslutte et filshare, fra lokale eller fra et andet datacenter. Windows 8, Windows Server 2012 og nyere versioner af hver negotiate anmodning, der indeholder 3.0 små og mellemstore virksomheder, som understøtter kryptering.

#### <a name="solution-for-cause-1"></a>Løsning til årsag 1

Oprette forbindelse fra en klient, der opfylder kravene i Windows 8, Windows Server 2012 eller nyere versioner eller, oprette forbindelse fra en virtuel maskine, som findes på den samme datacenter som kontoen Azure-lager, der bruges til Azure filshare.

#### <a name="cause-2"></a>Årsag 2

"Systemfejl 53" Når du opretter forbindelse til en Azure filshare kan opstå, hvis Port 445 udgående kommunikation til Azure filer datacenter er blokeret. Klik på [her](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) se oversigten over Internetudbydere, tillade eller ikke tillade adgang fra port 445.

Blokerer denne port, Comcast og nogle IT-organisationer. For at forstå, om det er grunden til bag meddelelsen "Systemfejl 53", kan du bruge Portqry forespørge TCP:445 slutpunkt. Hvis TCP:445 slutpunktet vises som filtreret, blokeres TCP-porten. Her er et eksempel på forespørgsel:

`g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

Hvis TCP 445 blive blokeret af en regel langs netværksstien, får du vist følgende output:

**TCP-port 445 (microsoft ds service): FILTRERET**

Du kan finde flere oplysninger om brug af Portqry [Beskrivelse af værktøjet Portqry.exe kommandolinjen](https://support.microsoft.com/kb/310099).

#### <a name="solution-for-cause-2"></a>Løsning til årsag 2

Arbejde med din IT-organisation til at åbne Port 445 udgående til [Azure IP-intervaller](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="cause-3"></a>Årsag 3

"Systemfejl 53" kan også være modtaget, hvis NTLMv1 kommunikation er aktiveret på klienten. Har du NTLMv1 aktiveret opretter en mindre sikker klient. Derfor blokeres kommunikation til Azure-filer. For at kontrollere, om dette er årsagen til fejlen, skal du kontrollere, følgende undernøgle i registreringsdatabasen er indstillet til en værdi af 3:

HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel.

Du kan finde flere oplysninger under emnet [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) på TechNet.

#### <a name="solution-for-cause-3"></a>Løsning til årsag 3

Du kan løse dette problem ved at gendanne LmCompatibilityLevel til værdien i registreringsdatabasenøglen HKLM\SYSTEM\CurrentControlSet\Control\Lsa til standardværdien af 3.

Azure filer understøtter kun NTLMv2-godkendelse. Sørg for, at Gruppepolitik anvendes på klienterne. Dette forhindrer denne fejl opstår. Dette er også anses for at være sikkerhedsmæssige årsager. Du kan finde flere oplysninger, se [hvordan du konfigurerer klienter til at bruge NTLMv2 ved hjælp af gruppepolitik](https://technet.microsoft.com/library/jj852207(v=ws.11).aspx)

Anbefalede politikindstillingen er **kun sende NTLMv2-svar**. Dette svarer til en værdi i registreringsdatabasen af 3. Klienter bruger kun NTLMv2-godkendelse, og de kan bruge NTLMv2 sessionssikkerhed, hvis serveren understøtter det. Domænecontrollere accepterer LM, NTLM og NTLMv2-godkendelse.

<a id="netuse"></a>
### <a name="net-use-was-successful-but-dont-see-the-azure-file-share-mounted-in-windows-explorer"></a>NET Brug fuldføres, men ikke kan se filen Azure dele tilsluttede i Windows Stifinder

#### <a name="cause"></a>Årsag

Windows Stifinder kører som standard ikke som Administrator. Hvis du støder **net Brug** fra en administratorkommandoprompt skal knytte du netværksdrevet "Som Administrator." Da tilknyttede drev er centreret om brugeren, vises den brugerkonto, der er logget på ikke drevene, hvis de er tilsluttet under en anden brugerkonto.

#### <a name="solution"></a>Løsning

Tilslutte del fra kommandolinjen en ikke-administrator. Alternativt kan du følge [denne TechNet-emne](https://technet.microsoft.com/library/ee844140.aspx) for at konfigurere værdien **EnableLinkedConnections** i registreringsdatabasen.

<a id="slashfails"></a>
### <a name="my-storage-account-contains--and-the-net-use-command-fails"></a>Min lagerplads konto indeholder "/" og på internettet bruge kommandoen mislykkes

#### <a name="cause"></a>Årsag

Hvis kommandoen **net use** kører under kommandoprompten (cmd.exe), er det parses ved at tilføje "/" som en parameter på kommandolinjen. Derved Drevtilknytningen mislykkes.

#### <a name="solution"></a>Løsning

Du kan bruge en af følgende fremgangsmåder til at løse problemet:

• Brug følgende PowerShell-kommandoen:

`New-SmbMapping -LocalPath y: -RemotePath \\server\share  -UserName acountName -Password "password can contain / and \ etc"`

Fra en batchfil kan dette gøres som

`Echo new-smbMapping ... | powershell -command –`

• Sætte dobbelte anførselstegn omkring tasten for at løse dette problem – medmindre "/" er det første tegn. Hvis det er, du enten bruge den interaktive tilstand og angive din adgangskode eller genoprette dine nøgler for at få en nøgle, der ikke starter med tegnet skråstreg (/).

<a id="accessfiledrive"></a>
### <a name="my-applicationservice-cannot-access-mounted-azure-files-drive"></a>Min programtjenester kan ikke få adgang til Azure filer drev

#### <a name="cause"></a>Årsag

Drev tilsluttes per bruger. Hvis programmet eller tjenesten kører under en anden brugerkonto, kan brugere ikke se drevet.

#### <a name="solution"></a>Løsning

Oprette forbindelse til drev fra den samme brugerkonto, hvorefter programmet er. Dette kan gøres ved hjælp af funktioner som psexec.

Du kan også oprette en ny bruger, der indeholder de samme tilladelser som network service eller system-kontoen og derefter køre **cmdkey** og **net Brug** under den pågældende konto. Brugernavnet skal være kontonavn lager, og adgangskode bør være kontonøgle lagerplads. Der er en anden indstilling **net** bruges til at videregive i kontonavn lager og nøgle i bruger og en adgangskode for parametrene for kommandoen **net use** .

Når du har fulgt disse instruktioner, modtager du muligvis vist følgende fejlmeddelelse: "Der opstod systemfejl 1312. Der findes ikke en bestemt logonsession. Det er muligvis allerede afsluttet"Når du kører **net Brug** til kontoen system/Netværkstjeneste. Hvis dette sker, skal du kontrollere, at det brugernavn, der sendes til **net Brug** omfatter domæneoplysninger (for eksempel: "[kontonavn lager]. file.core.windows .net").

## <a name="linux-client-problems"></a>Problemer med Linux-klient

<a id="encryption"></a>
### <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Fejlen "Du vil kopiere en fil til en destination, der ikke understøtter kryptering"

#### <a name="cause"></a>Årsag

BitLocker-krypterede filer kan kopieres til Azure-filer. Fillagring understøtter dog ikke NTFS genoprettelse af krypterede data. Derfor bruger du sandsynligvis genoprettelse af krypterede data i dette tilfælde. Hvis du har filer, der er krypteret gennem genoprettelse af krypterede data, kan en kopieringen til fillagring mislykkes, medmindre kopieringskommandoen dekrypterer en kopieret fil.

#### <a name="workaround"></a>Løsning

Hvis du vil kopiere en fil til lagring af filer, skal du først dekryptere den. Du kan gøre dette ved at benytte en af følgende fremgangsmåder:

• Brug **Kopiér/d**.

• Angive følgende registreringsdatabasenøgle:

- Sti = HKLM\Software\Policies\Microsoft\Windows\System
- Værditype = DWORD
- Navn = CopyFileAllowDecryptedRemoteDestination
- Værdi = 1

Bemærk, at angive registreringsdatabasenøgle påvirker alle kopi handlinger til netværksdrev.

<a id="errorhold"></a>
### <a name="host-is-down-error-on-existing-file-shares-or-the-shell-hangs-when-you-run-list-commands-on-the-mount-point"></a>"Værten er nede" fejl på en eksisterende fil deler eller shell hænger, når du kører liste over kommandoer på tilslutningspunktet

#### <a name="cause"></a>Årsag

Denne fejl opstår på Linux-klienten, når kunden har været inaktiv i en længere periode. Når denne fejl opstår, klienten afbryder forbindelsen, og klientforbindelsen timeout.

#### <a name="solution"></a>Løsning

Dette problem er nu løst i Linux kernen som en del af [ændre sæt](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93), ventende backport til Linux fordeling.

Opretholde forbindelsen til works løse dette problem, og undgå går inaktiv, bevare en fil i Azure filshare, som du skriver til med jævne mellemrum. Dette skal være en skrivehandling, som omskrivning af oprettet/ændret datoen på filen. Ellers skal du muligvis få cachelagrede resultater, og din handling kan ikke udløse forbindelsen.

<a id="error15"></a>
### <a name="mount-error-115-when-you-try-to-mount-azure-files-on-the-linux-vm"></a>"Tilslutte fejl 115" Når du forsøger at tilslutte Azure filer på Linux VM

#### <a name="cause"></a>Årsag

Linux salgsdistributioner understøtter ikke endnu krypteringsfunktioner i små og mellemstore virksomheder 3.0. I nogle salgsdistributioner modtager bruger fejlmeddelelsen "115", når de forsøger at tilslutningen Azure filer ved hjælp af små og mellemstore virksomheder 3.0 på grund af en manglende funktion.

#### <a name="solution"></a>Løsning

Hvis den Linux SMB-klient, der bruges ikke understøtter kryptering, filer tilslutningen Azure ved hjælp af små og mellemstore virksomheder 2.1 fra en Linux VM på den samme datacenter som fil lagerplads konto.

<a id="delayproblem"></a>
### <a name="linux-vm-experiencing-random-delays-in-commands-like-ls"></a>Linux VM problemer tilfældige forsinkelser i kommandoer som "ls"

#### <a name="cause"></a>Årsag

Dette kan ske, når kommandoen Indlæs ikke omfatter indstillingen **serverino** . Kommandoen ls kører uden **serverino**, **stat** på alle filer.

#### <a name="solution"></a>Løsning

Kontrollere **serverino** i indtastningen "/ osv/fstab":

azureuser.File.Core.Windows.NET/WMS/comer på/hjem/sampledir type cifs (rw, nodev, relatime, bevidsthedstilstand = 2.1, sec = ntlmssp, cachen = strenge, brugernavn = xxx domæne = X, file_mode = 0755, dir_mode = 0755, serverino, rsize = 65536, wsize = 65536, actimeo = 1)

Hvis indstillingen **serverino** ikke findes, afbryde og tilslutte Azure filer igen ved at få indstillingen **serverino** er markeret.

## <a name="learn-more"></a>Lær mere

- [Introduktion til Azure fillagring i Windows](storage-dotnet-how-to-use-files.md)

- [Introduktion til Azure fillagring på Linux](storage-how-to-use-files-linux.md)
