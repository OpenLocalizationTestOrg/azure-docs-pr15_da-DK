<properties
    pageTitle="Forberede en virtuel harddisk Windows til at overføre til Azure | Microsoft Azure"
    description="Anbefalede fremgangsmåder til at forberede en Windows-Virtuelle før du overfører til Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="genlin"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/18/2016"
    ms.author="glimoli;genli"/>

# <a name="prepare-a-windows-vhd-to-upload-to-azure"></a>Forberede en virtuel harddisk Windows til at overføre til Azure
Hvis du vil overføre en Windows-VM fra det lokale til Azure, skal du korrekt forberede den virtuelle harddisk (Virtuelle). Der findes flere anbefalede trin til at fuldføre, før du overfører en virtuel harddisk til Azure. I denne artikel beskrives, hvordan du forbereder en virtuel harddisk Windows til at overføre til Microsoft Azure og også forklarer, [hvornår og hvordan du bruger Sysprep](#step23).

## <a name="prepare-the-virtual-disk"></a>Forberede den virtuelle disk

>[AZURE.NOTE] 
> Azure understøtter kun [generering af 1 virtuelle maskiner](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx) , der er i det Virtuelle filformat. Det nyere VHDX format understøttes ikke i Azure. 
>
> Den virtuelle harddisk skal være en fast størrelse ikke dynamisk. Hvis det er nødvendigt, detaljer vejledningen nedenfor for konvertering fra VHDX eller dynamisk diske. Den maksimalt tilladte størrelse for den virtuelle harddisk er 1,023 GB.


Sørg for, at den virtuelle harddisk Windows fungerer korrekt på den lokale server. Løse fejl i VM selve før du forsøger at konvertere eller overføre til Azure.

Hvis du vil konvertere harddisken virtuelle til det ønskede format til Azure, skal du bruge en af de metoder, der er angivet i de følgende afsnit. Sikkerhedskopiere VM, før du kører en hvilken som helst Virtuel disk konverteringsprocessen eller Sysprep.

### <a name="convert-using-hyper-v-manager"></a>Konvertere ved hjælp af Hyper-V Manager
- Åbn Hyper-V Manager, og vælg den lokale computer i venstre side. Klik på **handling**i menuen ovenover > **Redigere Disk**.
    - Gå til på skærmbilledet **Find virtuel harddisk** , og vælg virtuelle disken.
    - Vælg **Konverter** på det næste skærmbillede
        - Hvis du vil konvertere fra VHDX, Vælg **Virtuelle** og klikke på **Næste**
        - Hvis du vil konvertere fra dynamisk disk, Vælg **fast størrelse** , og klik på **Næste**

    - Gå til, og vælg **stien til den nye Virtuelle-fil**.
    - Klik på **Udfør** for at lukke.

### <a name="convert-using-powershell"></a>Konvertere ved hjælp af PowerShell
Du kan konvertere en virtuel disk ved hjælp af [Konverter Virtuelle PowerShell-cmdlet](http://technet.microsoft.com/library/hh848454.aspx). I eksemplet nedenfor vi konvertering fra en VHDX til Virtuelle og konvertering fra et dynamisk af til fast type:

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

### <a name="convert-from-vmware-vmdk-disk-format"></a>Konvertere fra VMware VMDK disk format
Hvis du har et Windows VM billede i [VMDK-filformat](https://en.wikipedia.org/wiki/VMDK), kan du konvertere den til en virtuel harddisk ved hjælp af [Microsoft Virtual Machine konverteringsprogram](https://www.microsoft.com/download/details.aspx?id=42497). Læs bloggen [Sådan konverteres et VMware VMDK til Hyper-V Virtuelle](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) kan finde flere oplysninger.

## <a name="prepare-windows-configuration-for-upload"></a>Forberede dig på Windows-konfiguration til at uploade

> [AZURE.NOTE] Kør følgende kommandoer med [administratorrettigheder](https://technet.microsoft.com/library/cc947813.aspx).

1. Fjerne en statisk fast rute på tabellen routing:

    - For at få vist rutetabellen, kører `route print`.
    - Markér **Brugerdata omdirigerer** afsnittene. Hvis der er en fast rute skal du bruge [distribuere slette](https://technet.microsoft.com/library/cc739598.apx) for at fjerne den.

2. Fjerne WinHTTP proxy:

    ```
    netsh winhttp reset proxy
    ```

3. Konfigurere disken SAN-politikken til [Onlineall](https://technet.microsoft.com/library/gg252636.aspx):

    ```
    diskpart san policy=onlineall
    ```

4. Bruge Coordinated Universal Time (UTC) tid til Windows, og Indstil Starttype af tjenesten Windows Time (w32time) til **automatisk**:

    ```
    REG ADD HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1
    sc config w32time start= auto
    ```


## <a name="configure-windows-services"></a>Konfigurere Windows services
5. Sørg for, at hver af følgende Windows tjenester er indstillet til **Windows standardværdier**. De er konfigureret med startindstillingerne noteret i listen nedenfor. Du kan køre disse kommandoer til at nulstille startindstillingerne for:

    ```
    sc config bfe start= auto

    sc config dcomlaunch start= auto

    sc config dhcp start= auto

    sc config dnscache start= auto

    sc config IKEEXT start= auto

    sc config iphlpsvc start= auto

    sc config PolicyAgent start= demand

    sc config LSM start= auto

    sc config netlogon start= demand

    sc config netman start= demand

    sc config NcaSvc start= demand

    sc config netprofm start= demand

    sc config NlaSvc start= auto

    sc config nsi start= auto

    sc config RpcSs start= auto

    sc config RpcEptMapper start= auto

    sc config termService start= demand

    sc config MpsSvc start= auto

    sc config WinHttpAutoProxySvc start= demand

    sc config LanmanWorkstation start= auto

    sc config RemoteRegistry start= auto
    ```


## <a name="configure-remote-desktop-configuration"></a>Konfigurere Fjernskrivebord konfiguration
6. Hvis der er en hvilken som helst selvsignerede certifikater, der er bundet til lytteren RDP Remote Desktop Protocol (), skal du fjerne dem:

    ```
    REG DELETE "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp\SSLCertificateSHA1Hash”
    ```

    Du kan finde flere oplysninger om konfiguration af certifikater til RDP lytteren se [Lytteren certifikat konfigurationer i Windows Server](https://blogs.technet.microsoft.com/askperf/2014/05/28/listener-certificate-configurations-in-windows-server-2012-2012-r2/)

7. Konfigurere [Keep-alive-](https://technet.microsoft.com/library/cc957549.aspx) værdierne for RDP-tjenesten:

    ```
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveEnable /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveInterval /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v KeepAliveTimeout /t REG_DWORD /d 1 /f
    ```

8. Konfigurere godkendelsesmetode til RDP-tjenesten:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD  /d 1 /f
    ```

9. Aktivere RDP-tjenesten ved at tilføje følgende undernøgler i registreringsdatabasen:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD  /d 0 /f
    ```


## <a name="configure-windows-firewall-rules"></a>Konfigurere Windows Firewall-regler
10. Give WinRM gennem de tre firewallprofiler (domæne, privat og offentlige), og Aktivér PowerShell Remote-tjenesten:

    ```
    Enable-PSRemoting -force
    ```

11. Kontrollér, at de følgende gæst operativsystem firewallregler er på plads:

    - Indgående

    ```
    netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (LLMNR-UDP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Datagram-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Name-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (Pub-WSD-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (SSDP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (UPnP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (WSD EventsSecure-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
    ```

    - Indgående og udgående

    ```
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes

    netsh advfirewall firewall set rule group="Core Networking" new enable=yes
    ```

    - Udgående

    ```
    netsh advfirewall firewall set rule dir=out name="Network Discovery (LLMNR-UDP-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Datagram-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Name-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (Pub-WSD-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (SSDP-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnPHost-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnP-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD Events-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD EventsSecure-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD-Out)" new enable=yes
    ```


## <a name="additional-windows-configuration-steps"></a>Yderligere trin til konfiguration af Windows
12. Køre `winmgmt /verifyrepository` for at bekræfte, at Windows Management Instrumentation (WMI) lager er ensartet. Hvis lageret er beskadiget, skal du se [dette blogindlæg](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

13. Sørg for, at indstillingerne for Start konfiguration Data (MCD) svarer til følgende:

    ```
    bcdedit /set {bootmgr} integrityservices enable

    bcdedit /set {default} device partition=C:

    bcdedit /set {default} integrityservices enable

    bcdedit /set {default} recoveryenabled Off

    bcdedit /set {default} osdevice partition=C:

    bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
    ```

14. Fjern eventuelle ekstra Transport driverens brugergrænseflade-filtre, såsom software, der analyserer TCP-pakker.
15. For at sikre at disken er sund og ensartet, skal du køre den `CHKDSK /f` kommandoen.
16. Fjern eventuelle andre software fra tredjepart og driver, der er relateret til fysisk komponenter eller anden virtualization-teknologi.
17. Sørg for, at et tredjepartsprogram ikke bruger Port 3389. Denne port bruges til tjenesten RDP i Azure. Du kan bruge den `netstat -anob` kommandoen til at kontrollere de porte, der bruger ved programmerne.
18. Hvis den Windows virtuelle harddisk, du vil overføre er et domænenavn fra domænecontrolleren, skal du følge [disse ekstra trin](https://support.microsoft.com/kb/2904015) for at forberede disken.
19. Genstart VM sikre dig, at Windows er stadig sund kan kontaktes ved hjælp af RDP-forbindelse.
20. Nulstille den aktuelle lokale administratoradgangskode, og Sørg for, at du kan bruge denne konto til at logge på Windows via RDP-forbindelse.  Denne adgangstilladelse styres af objektet "Tillad log på via Remote Desktop Services" politik. Dette objekt er placeret under "Computer Computerkonfiguration\Windows Settings\Security indstillinger\Sikkerhedsindstillinger\Lokale politikker\Tildeling af brugerrettigheder."


## <a name="install-windows-updates"></a>Installere Windows-opdateringer
22. Installere de seneste opdateringer til Windows. Hvis det ikke er muligt, skal du kontrollere, at følgende opdateringer er installeret:

    - [KB3137061](https://support.microsoft.com/kb/3137061) Microsoft Azure FOS gendanne ikke fra et netværk afbrydelse og forekomme problemer med beskadigelse af data

    - [KB3115224](https://support.microsoft.com/kb/3115224) Forbedringer af pålidelighed for VM'er, der kører på en Windows Server 2012 R2 eller Windows Server 2012-vært

    - [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031: Sikkerhedsopdatering til Microsoft Windows til adresse udvidelse af rettigheder: marts 8 2016

    - [KB3063075](https://support.microsoft.com/kb/3063075) Mange ID 129 hændelser logføres, når du kører en Windows Server 2012 R2 virtuel maskine i Microsoft Azure

    - [KB3137061](https://support.microsoft.com/kb/3137061) Microsoft Azure FOS gendanne ikke fra et netværk afbrydelse og forekomme problemer med beskadigelse af data

    - [KB3114025](https://support.microsoft.com/kb/3114025) Langsom ydeevne, når du får adgang til Azure filer lagerplads fra Windows 8.1 eller Server 2012 R2

    - [KB3033930](https://support.microsoft.com/kb/3033930) Programrettelse øges 64 KB grænsen for RIO buffere per proces til Azure-tjenesten i Windows

    - [KB3004545](https://support.microsoft.com/kb/3004545) Du kan ikke få adgang til virtuelle maskiner, der er placeret på Azure værtstjenester via en VPN-forbindelse i Windows

    - [KB3082343](https://support.microsoft.com/kb/3082343) Tværs lokale VPN-forbindelse går tabt, når Azure-til-websted VPN tunneler bruger Windows Server 2012 R2 RRAS

    - [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031: Sikkerhedsopdatering til Microsoft Windows til adresse udvidelse af rettigheder: marts 8 2016

    - [KB3146723](https://support.microsoft.com/kb/3146723) MS16-048: Beskrivelse af sikkerhedsopdateringen til for CSRSS: April 12 2016
    - [KB2904100](https://support.microsoft.com/kb/2904100) System fryser under disk I/O i Windows<a id="step23"></a>
23. Hvis du vil oprette et billede for at installere flere computere fra det, du vil generalize billedet ved at køre `sysprep` før du overfører den virtuelle harddisk til Azure. Du behøver ikke at køre `sysprep` til brug af en særlig virtuel harddisk. Du kan finde flere oplysninger om, hvordan du opretter en generalized afbildning, se følgende artikler:

    - [Oprette et VM billede fra en eksisterende Azure VM ved hjælp af implementeringsmodel ressourcestyring](virtual-machines-windows-create-vm-generalized.md)
    - [Oprette et VM billede fra en eksisterende Azure VM ved hjælp af klassisk installation modem](virtual-machines-windows-classic-capture-image.md)
    - [Sysprep-understøttelse af serverroller](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)



## <a name="suggested-extra-configurations"></a>Forslag til ekstra konfigurationer

Følgende indstillinger påvirker ikke Virtuelle overførsel. Vi anbefaler på det kraftigste, at du har dem konfigureret.

- Installer [Azure virtuelle maskiner Agent](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Når du har installeret agenten, kan du aktivere VM filtypenavne. VM filtypenavne implementere de fleste af de kritiske funktioner, du vil bruge med dine FOS som nulstilling af adgangskoder, konfigurere RDP og mange andre.

- Lagring af loggen kan være nyttige i fejlfinding af problemer med Windows går ned. Aktivere den gemte log af websteder:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f`

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpFolder /t REG_EXPAND_SZ /d "c:\CrashDumps" /f

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpCount /t REG_DWORD /d 10 /f

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpType /t REG_DWORD /d 2 /f

    sc config wer start= auto
    ```

- Når VM er oprettet i Azure, kan du konfigurere det definerede størrelse systemsidefil på drev D:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /t REG_MULTI_SZ /v PagingFiles /d "D:\pagefile.sys 0 0" /f
    ```


## <a name="next-steps"></a>Næste trin

- [Overføre et billede af Windows VM til Azure til Ressourcestyring installationer](virtual-machines-windows-upload-image.md)
