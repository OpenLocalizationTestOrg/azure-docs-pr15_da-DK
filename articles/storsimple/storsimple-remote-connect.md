<properties 
   pageTitle="Oprette forbindelse fra en fjernplacering til enheden StorSimple | Microsoft Azure"
   description="Forklarer, hvordan du konfigurerer din enhed til fjernadministration og hvordan du kan oprette forbindelse til Windows PowerShell til StorSimple via HTTP eller HTTPS."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/21/2016"
   ms.author="alkohli" />

# <a name="connect-remotely-to-your-storsimple-device"></a>Oprette forbindelse fra en fjernplacering til enheden StorSimple

## <a name="overview"></a>Oversigt

Du kan bruge Windows PowerShell remoting til at oprette forbindelse til din StorSimple enhed. Når du knytter denne måde, kan du ikke kan se en menu. (Du se en menu, hvis du bruger konsollen seriel på enheden for at oprette forbindelse). Med Windows PowerShell remoting, du opretter forbindelse til en bestemt runspace. Du kan også angive sproget i brugergrænsefladen. 

Du kan finde flere oplysninger om brug af Windows PowerShell remoting til at administrere din enhed, skal du gå til [Brug af Windows PowerShell til StorSimple til at administrere din StorSimple enhed](storsimple-windows-powershell-administration.md).

Dette selvstudium beskrives det, hvordan du kan konfigurere din enhed til fjernadministration og hvordan du kan oprette forbindelse til Windows PowerShell til StorSimple. Du kan bruge HTTP eller HTTPS til at oprette forbindelse via Windows PowerShell remoting. Men, når du vælger, hvordan du kan oprette forbindelse til Windows PowerShell til StorSimple, skal du overveje følgende: 

- Oprette forbindelse direkte til enhed seriel konsollen ikke er sikker, men ikke oprette forbindelse til konsollen seriel via netværk parametre er. Vær forsigtig med sikkerhedsrisikoen, når du opretter forbindelse til enhed seriel konsollen over netværk parametre. 

- Forbindelse via en HTTP-session kan tilbyde større sikkerhed end forbindelse via konsollen seriel via netværket. Selvom det ikke er den mest sikre metode, er det accepteres på netværk, der er tillid til. 

- Forbindelse via en HTTPS-session med et selvsigneret certifikat er den mest sikre og den anbefalede indstilling.

Du kan oprette forbindelse fra en fjernplacering til Windows PowerShell-brugergrænsefladen. Fjernadgang til enheden StorSimple via Windows PowerShell-grænseflade er ikke aktiveret som standard. Du skal først aktivere fjernadministration på enheden, og klik derefter på klienten, der bruges til at få adgang til din enhed.

De trin, der er beskrevet i denne artikel er udført på en hostsystem, der kører Windows Server 2012 R2.

## <a name="connect-through-http"></a>Oprette forbindelse via HTTP

Oprette forbindelse til Windows PowerShell til StorSimple via en HTTP-session giver større sikkerhed forbindelse via seriel konsollen på enheden StorSimple. Selvom det ikke er den mest sikre metode, er det accepteres på netværk, der er tillid til.

Du kan bruge enten Azure klassisk portalen eller serielle konsollen til at konfigurere fjernadministration. Vælge mellem følgende procedurer:

- [Brug Azure klassisk portalen for at aktivere fjernadministration via HTTP](#use-the-azure-classic-portal-to-enable-remote-management-over-http)

- [Bruge konsollen seriel til at aktivere fjernadministration via HTTP](#use-the-serial-console-to-enable-remote-management-over-http)

Når du aktiverer fjernadministration, kan du bruge følgende procedure til at forberede klienten til en ekstern forbindelse.

- [Forberede klienten til remote forbindelse](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-classic-portal-to-enable-remote-management-over-http"></a>Brug Azure klassisk portalen for at aktivere fjernadministration via HTTP 

Udfør følgende trin i Azure klassisk portalen for at aktivere fjernadministration via HTTP.

#### <a name="to-enable-remote-management-through-the-azure-classic-portal"></a>Aktivere fjernstyring via portalen Azure klassisk

1. Få adgang til **enheder** > **Konfigurer** til din enhed.

2. Rul ned til sektionen **Fjernadministration** .

3. Angive **aktivere fjernadministration** til **Ja**.

4. Du kan nu få forbindelse via HTTP. (Standard er at oprette forbindelse via HTTPS). Sørg for, at HTTP er markeret.

    >[AZURE.NOTE] Oprette forbindelse via HTTP accepteres kun på netværk, der er tillid til.

6. Klik på **Gem** nederst på siden.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>Bruge konsollen seriel til at aktivere fjernadministration via HTTP

Udfør følgende trin på enheden seriel console for at aktivere fjernadministration.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Aktivere fjernstyring via konsollen enhed seriel

1. Vælg indstilling 1 i menuen Seriel konsol. Du kan finde flere oplysninger om brug af seriel konsollen på enheden, gå til [Opret forbindelse til Windows PowerShell til StorSimple via enhed seriel administrationskonsollen](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console).

2. Når du bliver spurgt, Skriv:`Enable-HcsRemoteManagement –AllowHttp`

3. Du vil have besked om sikkerhedsproblemer ved at bruge HTTP til at oprette forbindelse til enheden. Når du bliver bedt om det, Bekræft ved at skrive **Y**.

4. Kontrollere, at HTTP er aktiveret ved at skrive:`Get-HcsSystem`

5. Kontrollér, at feltet **RemoteManagementMode** indeholder **HttpsAndHttpEnabled**. Følgende illustration viser disse indstillinger i trykfarver.

     ![Seriel HTTPS og HTTP-aktiveret](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Forberede klienten til remote forbindelse

Udfør følgende trin på klienten til at aktivere fjernadministration.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Sådan forberedes remote forbindelse klienten

1. Starte en session med Windows PowerShell som administrator.

2. Skriv følgende kommando for at føje StorSimple enheden IP-adressen til klientens der er tillid til hosts liste: 

     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`

     Erstat <*device_ip*> med IP-adressen på din enhed Eksempel: 

     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`

3. Skriv følgende kommando for at gemme enhed legitimationsoplysningerne i en variabel: 

     *$cred = get-legitimationsoplysninger*

4. I den dialogboks, der vises:

    1. Skriv brugernavnet i dette format: *device_ip\SSAdmin*.
    2. Skriv enhed administratorens adgangskode, der blev angivet, når enheden er konfigureret med konfigurationsguiden. Standardadgangskoden er *Adgangskode1*.

7. Starte en session med Windows PowerShell på enheden ved at skrive denne kommando:

     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`

     >[AZURE.NOTE] Hvis du vil oprette en session med Windows PowerShell til brug med den virtuelle StorSimple-enhed, skal du tilføje den `–Port` parameter og angive den offentlige port, som du har konfigureret i Remoting til StorSimple virtuelle maskinen.

     På dette tidspunkt, bør du have en aktiv Windows PowerShell-fjernsession på enheden.

    ![PowerShell remoting med HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Oprette forbindelse via HTTPS

Oprette forbindelse til Windows PowerShell til StorSimple via en HTTPS-session er den mest sikre og anbefalede metode fra en fjernplacering at oprette forbindelse til din Microsoft Azure StorSimple enhed. I det følgende forklares, hvordan du konfigurerer de serielle konsol og klienten computere, så du kan bruge HTTPS til at oprette forbindelse til Windows PowerShell til StorSimple.

Du kan bruge enten Azure klassisk portalen eller serielle konsollen til at konfigurere fjernadministration. Vælge mellem følgende procedurer:

- [Brug Azure klassisk portalen for at aktivere fjernadministration over HTTPS](#use-the-azure-classic-portal-to-enable-remote-management-over-https)

- [Bruge konsollen seriel til at aktivere fjernadministration over HTTPS](#use-the-serial-console-to-enable-remote-management-over-https)

Når du aktiverer fjernadministration, kan du bruge følgende procedurer til at forberede værten for en fjernadministration og oprette forbindelse til enheden fra den eksterne vært.

- [Forberede fjernadministration værten](#prepare-the-host-for-remote-management)

- [Oprette forbindelse til enheden fra den eksterne vært](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-classic-portal-to-enable-remote-management-over-https"></a>Brug Azure klassisk portalen for at aktivere fjernadministration over HTTPS

Udfør følgende trin i Azure klassisk portalen for at aktivere fjernadministration over HTTPS.

#### <a name="to-enable-remote-management-over-https-from-the-azure-classic-portal"></a>Aktivere fjernadministration over HTTPS fra portalen Azure klassisk

1. Få adgang til **enheder** > **Konfigurer** til din enhed.

2. Rul ned til sektionen **Fjernadministration** .

3. Angive **aktivere fjernadministration** til **Ja**.

4. Du kan nu vælge at oprette forbindelse ved hjælp af HTTPS. (Standard er at oprette forbindelse via HTTPS). Sørg for, at HTTPS er markeret. 

5. Klik på **Hent fjernadministration certifikat**. Angiv en placering for at gemme filen. Du skal installere dette certifikat på den klient på computeren eller host computer, du vil bruge til at oprette forbindelse til enheden.

6. Klik på **Gem** nederst på siden.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>Bruge konsollen seriel til at aktivere fjernadministration over HTTPS

Udfør følgende trin på enheden seriel console for at aktivere fjernadministration.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Aktivere fjernstyring via konsollen enhed seriel

1. Vælg indstilling 1 i menuen Seriel konsol. Du kan finde flere oplysninger om brug af seriel konsollen på enheden, gå til [Opret forbindelse til Windows PowerShell til StorSimple via enhed seriel administrationskonsollen](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console).

2. Når du bliver spurgt, Skriv: 

     `Enable-HcsRemoteManagement`

    Dette bør aktivere HTTPS på din enhed.

3. Kontrollere, at HTTPS er blevet aktiveret ved at skrive: 

     `Get-HcsSystem`

    Sørg for, at feltet **RemoteManagementMode** indeholder **HttpsEnabled**. Følgende illustration viser disse indstillinger i trykfarver.

     ![Seriel HTTPS aktiveret](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)

4. Fra outputtet af `Get-HcsSystem`, kopiere serienummeret for enheden og gemme den til senere brug.

    >[AZURE.NOTE] Serienummeret knytter CN til navnet på i certifikatet.

5. Bestille et fjernadministration certifikat ved at skrive: 
 
     `Get-HcsRemoteManagementCert`

    Et certifikat, der ligner følgende vises.

    ![Få fjernadministration certifikat](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)

5. Kopiér oplysningerne i certifikatet fra **– Begynd certifikat –** **–** afslutte certifikat – i et tekstredigeringsprogram som Notesblok og gemme den som en .cer-fil. (Du kan kopiere denne fil til din eksterne vært, når du klargør værten.)

    >[AZURE.NOTE] Du kan oprette et nyt certifikat ved at bruge den `Set-HcsRemoteManagementCert` cmdlet.

### <a name="prepare-the-host-for-remote-management"></a>Forberede fjernadministration værten

For at forberede værtscomputeren for en ekstern forbindelse, der bruger en HTTPS-session skal du udføre følgende procedurer:

- [Importere .cer-filen i roden store af klienten eller eksterne vært](#to-import-the-certificate-on-the-remote-host).

- [Tilføje serienumrene enhed til hosts-filen på din eksterne vært](#to-add-device-serial-numbers-to-the-remote-host).

Hver af disse procedurer er beskrevet nedenfor.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>Importere certifikatet på den eksterne vært

1. Højreklik på .cer-filen, og vælg **Installer certifikat**. Dette starter guiden Import.

    ![Importguiden er 1 af certifikat](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)

2. Vælg **Lokal computer**for **placering**, og klik derefter på **Næste**.

3. Placer **alle certifikater i følgende store**, og klik derefter på **Gennemse**. Gå til store på roden af din eksterne vært, og klik derefter på **Næste**.

    ![Importguiden er 2 af certifikat](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)

4. Klik på **Udfør**. Der vises en meddelelse, der fortæller, at importen blev fuldført.

    ![Importguiden er 3 af certifikat](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Føje enhed serienumre til den eksterne vært

1. Start Notesblok som administrator, og Åbn derefter filen hosts findes i \Windows\System32\Drivers\etc.

2. Føje følgende tre poster til filen hosts: **DATA 0 IP-adresse**, **Controller 0 fast IP-adresse**og **Controller 1 fast IP-adresse**.

3. Angiv serienummeret enhed, du gemte tidligere. Knytte dette til IP-adresse, som vist på følgende billede. Føje til Controller 0 og 1 Controller, **Controller0** og **Controller1** i slutningen af serienummeret (CN navn).

    ![Tilføje CN-navn til hosts-fil](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)

4. Gem filen hosts.

### <a name="connect-to-the-device-from-the-remote-host"></a>Oprette forbindelse til enheden fra den eksterne vært

Brug af Windows PowerShell og SSL til at angive en SSAdmin session på din enhed fra en ekstern vært eller klient. SSAdmin sessionen knytter til indstilling 1 i menuen [Seriel konsol](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console) på din enhed.

Udfør følgende procedure på den computer, hvorfra du vil gøre forbindelsen til fjerncomputeren Windows PowerShell.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-ssl"></a>Angive en SSAdmin session på enheden ved hjælp af Windows PowerShell og SSL

1. Starte en session med Windows PowerShell som administrator.

2. Tilføj enhed IP-adressen til kundens der er tillid til værter ved at indtaste:

     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`

    Hvor <*device_ip*> er IP-adressen på din enhed Eksempel: 

     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`

3. Oprette en ny legitimationsoplysninger ved at skrive: 

     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`

    Hvor <*IP target enhed*> er IP-adressen på DATA 0 til din enhed for eksempel **10.126.173.90** som vist i den foregående billede af hosts-filen. Desuden levere administratoradgangskode til din enhed.

4. Oprette en session ved at skrive:

     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`

    Angiv <*serienummeret for target enhed*> for parameteren - computernavn i cmdlet. Dette serienummer blev knyttet til DATA 0 i hosts-filen på din eksterne vært IP-adresse for eksempel **SHX0991003G44MT** som vist på følgende billede.

5. Type: 

     `Enter-PSSession $session`

6. Skal du vente et par minutter, og klik derefter du vil have forbindelse til din enhed via HTTPS over SSL. Du får vist en meddelelse om, at du har forbindelse til din enhed.

    ![PowerShell remoting ved hjælp af HTTPS og SSL](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Næste trin

- Lær mere om at [bruge Windows PowerShell for at administrere din StorSimple enhed](storsimple-windows-powershell-administration.md).

- Lær mere om [ved hjælp af tjenesten StorSimple Manager for at administrere din StorSimple enhed](storsimple-manager-service-administration.md).
