<properties 
   pageTitle="Foretage fejlfinding af problemer med installation af StorSimple | Microsoft Azure"
   description="Beskriver, hvordan du diagnosticere og løse fejl, der opstår, når du først installere StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/18/2016"
   ms.author="alkohli" />

# <a name="troubleshoot-storsimple-device-deployment-issues"></a>Foretage fejlfinding af problemer med installation StorSimple enheder

## <a name="overview"></a>Oversigt

Denne artikel indeholder nyttige fejlfinding vejledning til Microsoft Azure StorSimple installationen. Den beskriver almindelige problemer, mulige årsager og anbefalede trin for at hjælpe dig med at løse problemer, der kan opstå, når du konfigurerer StorSimple. Disse oplysninger gælder for både den fysiske enhed StorSimple lokalt og den virtuelle StorSimple-enhed.

> [AZURE.NOTE] Enhed konfiguration-relaterede problemer, der muligvis kan opstå kan opstå, når du installerer enheden for første gang, eller de kan opstå senere, når enheden er funktionsdygtige. I denne artikel fokuserer på fejlfinding af problemer med installation for første gang. Gå til [fejlfinding på problemer funktionel enhed](storsimple-troubleshoot-operational-device.md)for at foretage fejlfinding af en funktionel enhed.

I denne artikel beskrives værktøjerne til fejlfinding i forbindelse med StorSimple installationer også og indeholder en trinvis fejlfinding eksempel.

## <a name="first-time-deployment-issues"></a>Første gang installation problemer

Hvis der opstår et problem, når du installerer enheden for første gang, skal du overveje følgende:

- Hvis du foretager fejlfinding af en fysisk enhed, skal du kontrollere, at hardwaren, der er installeret og konfigureret som beskrevet i [installere enheden StorSimple 8100](storsimple-8100-hardware-installation.md) eller [installere enheden StorSimple 8600](storsimple-8600-hardware-installation.md).
- Se forudsætninger for installation. Sørg for, at du har alle de oplysninger, der er beskrevet i [Tjekliste for konfiguration af installation](storsimple-deployment-walkthrough.md#deployment-configuration-checklist).
- Gennemse produktbemærkninger StorSimple for at se Hvis problemet er beskrevet. Produktbemærkninger omfatter løsninger på problemer med kendte installation. 

Under installation af enhed problemer de mest almindelige, som brugerne har ansigt opstå, når de kører konfigurationsguiden, og når de registrerer enheden via Windows PowerShell til StorSimple. (Du bruge Windows PowerShell til StorSimple til at registrere og konfigurere enheden StorSimple. Finde flere oplysninger om enhed registrering, [trin 3: konfigurere og registrere din enhed via Windows PowerShell til StorSimple](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)).

De følgende afsnit kan hjælpe dig med at løse problemer, der kan opstå, når du konfigurerer StorSimple enheden for første gang.

## <a name="first-time-setup-wizard-process"></a>Første gang konfigurationen af guiden

Følgende trin Opsummer guiden konfigurationen. Du kan finde detaljerede installationsoplysninger [Implementer enheden lokale StorSimple](storsimple-deployment-walkthrough.md).

1. Køre [Aktiver HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) cmdlet for at starte konfigurationsguiden, der vejleder dig gennem de resterende trin. 
2. Konfigurere netværket: konfigurationsguiden kan du konfigurere netværksindstillinger for grænsefladen DATA 0 netværk på enheden StorSimple. Disse indstillinger er følgende:
  - Virtuel IP-adresse (VIP), undernetmaske og gateway-cmdlet'en [Set-HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) til udføres i baggrunden. Den konfigurerer IP-adresse, undernetmaske og gateway for grænsefladen DATA 0 netværk på enheden StorSimple.
  - Primære DNS-server-cmdlet'en [Set-HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) til udføres i baggrunden. Den konfigurerer DNS-indstillingerne for din StorSimple løsning.
  - NTP-server-cmdlet'en [Set-HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) til udføres i baggrunden. Den konfigurerer serverindstillinger NTP for din StorSimple løsning.
  - Valgfri webproxy-cmdlet'en [Set-HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) til udføres i baggrunden. Det angiver og muliggør web proxykonfigurationen af din StorSimple-løsning.
3. Konfigurere adgangskoderne: det næste trin er at konfigurere enhedsadministrator og StorSimple øjebliksbillede Manager adgangskoder. Hvis du kører opdatering 1, derefter skal ikke du konfigurere den StorSimple øjebliksbillede Manager-adgangskode.
  - Administratoradgangskode enhed bruges til at logge på din enhed. Standard enhedens adgangskode er **Adgangskode1**.
  - Den StorSimple øjebliksbillede Manager-adgangskode er påkrævet, når du konfigurerer en enhed for at bruge StorSimple øjebliksbillede Manager. Du skal først angive adgangskoden i guiden, og du kan angive og ændre det fra tjenesten StorSimple Manager. Denne adgangskode godkender enhed med StorSimple øjebliksbillede Manager.
 
    > [AZURE.IMPORTANT] Adgangskoder er indsamlet før registrering, men anvendes kun, når du har registreret enheden korrekt. Hvis der er en fejl ved at anvende en adgangskode, bliver du bedt om at angive adgangskoden igen, indtil de påkrævede adgangskoder (, opfylder kompleksitetskravene) er indsamlet.

4. Registrere enheden: det sidste trin er at registrere enheden med tjenesten StorSimple Manager kører i Microsoft Azure. Registreringen kræver, at du at [få tasten service registrering](storsimple-manage-service.md#get-the-service-registration-key) fra Azure klassisk-portalen, og giv det i konfigurationsguiden. Når enheden er blevet registreret, leveres en tjeneste data krypteringsnøgle til dig. Sørg for at bevare denne krypteringsnøgle i et sikkert sted, da det vil være påkrævet for at registrere alle efterfølgende enheder med tjenesten.

## <a name="common-errors-during-device-deployment"></a>Almindelige fejl under installation af enhed

De følgende tabeller viser de mest almindelige fejl, du kan støde på, hvornår du:

- Konfigurere de nødvendige netværksindstillinger.
- Konfigurere valgfri web proxy-indstillinger.
- Konfigurere enheden administratoren og StorSimple øjebliksbillede Manager adgangskoder. 
- Registrere enheden. 

## <a name="errors-during-the-required-network-settings"></a>Fejl under de nødvendige netværksindstillinger

| Nej.| Fejlmeddelelse | Mulige årsager | Anbefalede handling |
| ---| ------------- | --------------- | ------------------ |
| 1  | Kalde HcsSetupWizard: Denne kommando kan kun køre på den aktive controller. | Konfiguration blev udført på den passive controller.| Kør denne kommando fra den aktive controller. Yderligere oplysninger finder du se [identificere en aktive controller på din enhed](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).|
| 2 | Kalde HcsSetupWizard: Enheden er ikke klar. | Der er problemer med netværksforbindelsen på DATA 0.| Markér den fysiske netværksforbindelsen på DATA 0.|
| 3 | Kalde HcsSetupWizard: Der er en IP-adressekonflikt med et andet system på netværket (undtagelse fra HRESULT: 0x80070263). | IP-adresse, der er angivet for DATA 0 var allerede i brug af en anden system. | Angiv en ny IP-adresse, der ikke er i brug.|
| 4 | Kalde HcsSetupWizard: En klyngeressource mislykkedes. (Undtagelse fra HRESULT: 0x800713AE). | Duplikere VIP. Den medfølgende IP-adresse er allerede i brug.| Angiv en ny IP-adresse, der ikke er i brug.|
| 5 | Kalde HcsSetupWizard: Ugyldige IPv4-adresse. | IP-adressen er angivet i et forkert format.| Markér formatet, og leverer din IP-adresse igen. Du kan finde flere oplysninger, se [IPv4-adresser][1]. |
| 6 | Kalde HcsSetupWizard: Ugyldig IPv6-adresse. | IP-adressen er angivet i et forkert format.| Markér formatet, og leverer din IP-adresse igen. Du kan finde flere oplysninger, se [IPv6-adresser][2].|
| 7 | Kalde HcsSetupWizard: Der er ikke flere slutpunkter fra den første eller sidste ark mapper. (Undtagelse fra HRESULT: 0x800706D9) | Funktionen klynge virker ikke. | [Kontakt Microsoft Support](storsimple-contact-microsoft-support.md) til næste trin.

## <a name="errors-during-the-optional-web-proxy-settings"></a>Fejl under proxyindstillinger valgfrit web

| Nej.| Fejlmeddelelse | Mulige årsager | Anbefalede handling |
| ---| ------------- | --------------- | ------------------ |
| 1  | Kalde HcsSetupWizard: Ugyldig parameter (undtagelse fra HRESULT: 0x80070057) | En af de parametre, der er knyttet til proxyindstillingerne er ikke gyldige.| URI er ikke angivet i det korrekte format. Brug følgende format: http://*<IP address or FQDN of the web proxy server>*:*<TCP port number>* |
| 2 | Kalde HcsSetupWizard: RPC-serveren ikke tilgængelig (undtagelse fra HRESULT: 0x800706ba) | Den egentlige årsag er et af følgende:<ol><li>Klyngen er ikke op.</li><li>Passive controller kan ikke kommunikere med den aktive controller, og kommandoen Kør fra passive controller.</li></ol> | Afhængigt af den egentlige årsag:<ol><li>[Kontakt Microsoft Support](storsimple-contact-microsoft-support.md) for at sikre, at klyngen er op.</li><li>Kør kommandoen fra den aktive controller. Hvis du vil køre kommandoen fra den passive controller, skal du sikre dig, at den passive controller kan kommunikere med den aktive controller. Du skal kontakte [Microsoft Support](storsimple-contact-microsoft-support.md) , hvis dette er brudt.</li></ol> |
| 3 | Kalde HcsSetupWizard: RPC-kaldet mislykkedes (undtagelse fra HRESULT: 0x800706be) | Klynge er nede. | [Kontakt Microsoft Support](storsimple-contact-microsoft-support.md) for at sikre, at klyngen er op.|
| 4 | Kalde HcsSetupWizard: Klynge ressourcen blev ikke fundet (undtagelse fra HRESULT: 0x8007138f) | Klyngeressourcen blev ikke fundet. Dette kan ske, når installationen ikke er korrekt. | Du muligvis nulstille enheden til standardindstillingerne. [Kontakt Microsoft Support](storsimple-contact-microsoft-support.md) for at oprette en klyngeressource.|
| 5 | Kalde HcsSetupWizard: Klynge ressource ikke online (undtagelse fra HRESULT: 0x8007138c)| Klyngeressourcer er ikke online. | [Kontakt Microsoft Support](storsimple-contact-microsoft-support.md) til næste trin.|

## <a name="errors-related-to-device-administrator-and-storsimple-snapshot-manager-passwords"></a>Fejl, som relaterer til enhedsadministrator og StorSimple øjebliksbillede Manager adgangskoder

Standard enhed administratoradgangskode er **Adgangskode1**. Denne adgangskode udløber efter den første logon Derfor skal du bruge konfigurationsguiden til at ændre den. Når du registrerer enheden for første gang, skal du angive en ny enhed administratoradgangskode. 

Hvis du bruger programmet StorSimple øjebliksbillede Manager kører på værten for Windows Server til at administrere enheden, skal du også angive en StorSimple øjebliksbillede Manager-adgangskode under registrering for første gang. 

Sørg for, at dine adgangskoder opfylder følgende krav:

- Din enhed administratoradgangskode skal være mellem 8 og 15 tegn.
- Adgangskoden StorSimple øjebliksbillede Manager skal være 14 eller 15 tegn.
- Adgangskoder skal indeholde 3 af 4 følgende tegntyper: små bogstaver, store bogstaver, numeriske og speciel. 
- Adgangskoden må ikke være den samme som de seneste 24 adgangskoder.

Desuden Husk på, adgangskoder udløber hvert år og kan ændres, når du har registreret enheden korrekt. Hvis registreringen af en eller anden grund ikke lykkes, ændres adgangskoderne ikke. Gå til [Brug tjenesten StorSimple Manager til at ændre dine StorSimple adgangskoder](storsimple-change-passwords.md)kan finde flere oplysninger om enhedsadministrator og StorSimple øjebliksbillede Manager adgangskoder.

En eller flere af følgende fejl kan opstå, når konfigurationen af enhedsadministrator og StorSimple øjebliksbillede Manager adgangskoder.

| Nej.| Fejlmeddelelse | Anbefalede handling |
| ---| ------------- | ------------------ | 
| 1  | Adgangskoden overstiger den maksimale længde. |Bruge en adgangskode, der opfylder følgende krav:<ul><li>Din enhed administratoradgangskode skal være mellem 8 og 15 tegn.</li><li>Adgangskoden StorSimple øjebliksbillede Manager skal være 14 eller 15 tegn.</li></ul> | 
| 2 | Adgangskoden opfylder ikke den ønskede længde. | Bruge en adgangskode, der opfylder følgende krav:<ul><li>Din enhed administratoradgangskode skal være mellem 8 og 15 tegn.</li><li>Adgangskoden StorSimple øjebliksbillede Manager skal være 14 eller 15 tegn.</lu></ul> |
| 3 | Adgangskoden skal indeholde små bogstaver. | Adgangskoder skal indeholde 3 af 4 følgende tegntyper: små bogstaver, store bogstaver, numeriske og speciel. Sørg for, at adgangskoden opfylder kravene. |
| 4 | Adgangskoden skal indeholde numeriske tegn. | Adgangskoder skal indeholde 3 af 4 følgende tegntyper: små bogstaver, store bogstaver, numeriske og speciel. Sørg for, at adgangskoden opfylder kravene. |
| 5 | Adgangskoden skal indeholde specialtegn. | Adgangskoder skal indeholde 3 af 4 følgende tegntyper: små bogstaver, store bogstaver, numeriske og speciel. Sørg for, at adgangskoden opfylder kravene. |
| 6 | Adgangskoden skal indeholde 3 af 4 følgende tegntyper: store bogstaver, små bogstaver, numeriske og speciel. | Din adgangskode indeholder ikke de nødvendige typer tegn. Sørg for, at adgangskoden opfylder kravene. |
| 7 | Parameter svarer ikke til bekræftelse. | Sørg for, at adgangskoden opfylder alle og, at du har angivet den korrekt. |
| 8 | Din adgangskode svarer ikke til standard. | Standardadgangskoden er *Adgangskode1*. Du skal ændre denne adgangskode, når du logger på for første gang. |
| 9 | Den adgangskode, du har angivet stemmer ikke overens enhedens adgangskode. Skriv adgangskoden igen. | Markér adgangskoden, og skriv den igen. |

Adgangskoder indsamles før enheden er registreret, men anvendes kun efter registrering er fuldført. Adgangskode gendannelse arbejdsprocessen kræver, at kameraet, for at være registreret. 

> [AZURE.IMPORTANT] Generelt, hvis et forsøg på at anvende en adgangskode ikke, forsøger derefter softwaren gentagne gange at indsamle adgangskoden, indtil det lykkes. I sjældne tilfælde kan ikke anvendes adgangskoden. I så fald kan du registrere enheden og fortsætte, men adgangskoderne, bliver ikke ændret. Får du ingen angivelse om, hvilken adgangskode ikke blev ændret – administratoradgangskode enhed eller den StorSimple øjebliksbillede Manager-adgangskode. Hvis problemet forekommer, anbefaler vi, at du ændrer begge adgangskoder.

Du kan nulstille adgangskoder i Azure klassisk portalen via tjenesten StorSimple Manager. Gå til få mere at vide: 

- [Skift administratoradgangskode enhed](storsimple-change-passwords.md#change-the-device-administrator-password).
- [Ændre adgangskoden StorSimple øjebliksbillede Manager](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password).

## <a name="errors-during-device-registration"></a>Fejl under registrering af enhed

Du kan bruge tjenesten StorSimple Manager kører i Microsoft Azure til at registrere enheden. Du kan støde på en eller flere af følgende problemer under enhed registrering.

| Nej.| Fejlmeddelelse | Mulige årsager | Anbefalede handling |
| ---| ------------- | --------------- | ------------------ |
| 1  | Fejl 350027: Kunne ikke registrere enheden med StorSimple Manager. |  | Vent et par minutter, og prøv derefter igen. Hvis problemet fortsætter, skal [du kontakte Microsoft Support](storsimple-contact-microsoft-support.md).|
| 2  | Fejl 350013: Der opstod en fejl i forbindelse med registrering enheden. Det kan være på grund af forkert tjenestens registreringsnøgle. | | Registrer enheden igen med den korrekte service registreringsnøgle. Du kan finde flere oplysninger, se [få tasten service registrering.](storsimple-manage-service.md#get-the-service-registration-key) |
| 3 | Fejl 350063: Godkendelse til StorSimple Manager tjenesten overføres, men registrering mislykkedes. Udfør handlingen efter et stykke tid igen. | Denne fejl angiver, at godkendelse med ACS er passeret, men register opkaldet foretages til tjenesten mislykkes. Det kan skyldes et resultat af en sporadisk netværk fejl. | Hvis problemet fortsætter, kan du [kontakte Microsoft Support](storsimple-contact-microsoft-support.md). |
| 4 | Fejl 350049: Tjenesten kunne ikke nås under registrering. | Når opkaldet foretages til tjenesten, modtog en web undtagelse. I nogle tilfælde kan dette få fast med prøver igen senere. | Kontroller din IP-adresse og DNS-navn, og derefter prøve igen. Hvis problemet fortsætter, [Kontakt Microsoft Support.](storsimple-contact-microsoft-support.md) | 
| 5 | Fejl 350031: Enheden er allerede blevet registreret. | | Ingen handling, der er nødvendigt. |
| 6 | Fejl 350016: Enhed registrering mislykkedes. | |Kontrollér, at tasten registrering er korrekt. |
| 7 | Kalde HcsSetupWizard: Der opstod en fejl under registrering din enhed Det kan skyldes forkert IP-adresse eller DNS-navn. Kontrollér dine netværksindstillinger, og prøv igen. Hvis problemet fortsætter, skal [du kontakte Microsoft Support](storsimple-contact-microsoft-support.md). (Fejl 350050) | Sørg for, at enheden kan pinge det eksterne netværk. Hvis du ikke har forbindelse til eksterne netværk, mislykkes registreringen med denne fejl. Denne fejl kan være en kombination af en eller flere af følgende:<ul><li>Forkert IP-adresse</li><li>Forkert undernet</li><li>Forkert gateway</li><li>Forkerte DNS-indstillinger</li></ul> | Se trinnene i [en trinvis fejlfinding eksempel](#step-by-step-storsimple-troubleshooting-example). |
| 8 | Kalde HcsSetupWizard: Den aktuelle handling mislykkedes på grund af en intern tjenestefejl [0x1FBE2]. Udfør handlingen efter et stykke tid igen. Hvis problemet fortsætter, skal du kontakte Microsoft Support. | Dette er en generisk fejl udløst for alle bruger usynlige fejl fra tjenesten eller agent. Den mest almindelige årsag kan være, at ACS-godkendelse mislykkes. En mulig årsag til fejlen er, at der er problemer med konfigurationen af NTP-server og klokkeslættet på enheden ikke er indstillet korrekt. | Rette tid (Hvis der opstår problemer), og prøv handlingen registrering. Hvis du bruger kommandoen sæt HcsSystem - tidszone til at tilpasse tidszonen, hvert ord i tidszone (for eksempel "Pacific Standard Time") med stort.  Hvis problemet fortsætter, trin [Kontakt Microsoft Support](storsimple-contact-microsoft-support.md) for næste. |
| 9 | Advarsel: Kan ikke aktivere enheden. Administratoren enhed og StorSimple øjebliksbillede Manager adgangskoder er ikke blevet ændret. | Hvis registreringen mislykkes, skal ændres administratoren enhed og StorSimple øjebliksbillede Manager adgangskoder ikke. |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>Værktøjer til fejlfinding i forbindelse med StorSimple installationer

StorSimple indeholder flere værktøjer, som du kan bruge til at foretage fejlfinding af din StorSimple løsning. Dette omfatter:

- Supportpakker og enhed logfiler 
- Cmdlet'er, der er udviklet specielt til fejlfinding 

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Support-pakker og enhed hændelseslogge tilgængelig til fejlfinding

En support-pakken indeholder alle relevante logfiler, der kan hjælpe Microsoft Support-teamet med fejlfinding af problemer med enheden. Du kan bruge Windows PowerShell til StorSimple til at oprette en krypteret supportpakke, som du derefter kan dele med supportpersonale.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>Sådan får du vist loggene eller indholdet af pakken support

1. Bruge Windows PowerShell til StorSimple til at generere en supportpakke, som beskrevet i [oprette og administrere en support-pakken](storsimple-create-manage-support-package.md).

2. Hent [dekryptering script](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) lokalt på klientcomputeren.

3. Brug denne [trinvis vejledning i](storsimple-create-manage-support-package.md#edit-a-support-package) at åbne og dekryptere support-pakken.

4. Loggene dekrypteres support-pakke er i etw/etvx format. Du kan udføre følgende trin for at få vist filerne i Windows Logbog:
  1. Kør kommandoen **eventvwr** på din Windows-klient. Dette starter Logbog.
  2. Klik på **Åbn gemt logfil** i ruden **Handlinger** , og peg på logfiler i etvx/etw format (support-pakken). Du kan nu få vist filen. Når du åbner filen, kan du højreklikke og Gem filen som tekst.
   
    > [AZURE.IMPORTANT] Du kan også bruge cmdlet'en **Get-WinEvent** til at åbne filerne i Windows PowerShell. Se [Få WinEvent](https://technet.microsoft.com/library/hh849682.aspx) kan finde flere oplysninger i dokumentationen til Windows PowerShell-cmdlet reference.

5. Når loggene åbner i Logbog, kan du se efter følgende logfiler, der indeholder problemer i forbindelse med konfigurationen af enheden:

  - hcs_pfconfig/Operational Log
  - hcs_pfconfig/Config

6. I logfilerne, søge efter strenge, der relaterer til cmdlet'erne kaldet ved konfigurationsguiden. Du kan finde en liste over disse cmdlet'er i [førstegangsbrugere konfigurationen af guiden](#first-time-setup-wizard-process) . 

7. Hvis du ikke kan finde ud af årsagen til problemet, kan du [kontakte Microsoft Support](storsimple-contact-microsoft-support.md) til næste trin. Følg trinnene i [oprette en anmodning om support](storsimple-contact-microsoft-support.md#create-a-support-request) , når du kontakte Microsoft Support for at få hjælp.

## <a name="cmdlets-available-for-troubleshooting"></a>Cmdlet'er, der er tilgængelige til fejlfinding

Brug følgende Windows PowerShell-cmdlet'er til at registrere connectivity fejl.

- `Get-NetAdapter`: Brug denne cmdlet til at registrere tilstanden for netværksgrænseflader. 

- `Test-Connection`: Brug denne cmdlet til at kontrollere netværksforbindelsen i og uden for netværket.

- `Test-HcsmConnection`: Brug denne cmdlet til at kontrollere forbindelsen af en enhed, der blev registreret.

Hvis du kører opdatering 1 på enheden StorSimple, er du også følgende diagnosticering cmdletter.

- `Sync-HcsTime`: Brug denne cmdlet til at få vist enhed tid og Tvungen synkronisering af klokkeslæt med NTP-serveren.

- `Enable-HcsPing`og `Disable-HcsPing`: Brug disse cmdlet'er til at tillade hosts ping netværksgrænseflader på enheden StorSimple til. Som standard reagerer StorSimple netværksgrænseflader ikke på ping anmodninger.

- `Trace-HcsRoute`: Brug denne cmdlet som et værktøj til netværkssporing distribution. Den sender pakker til hver router hele vejen til den endelige destination over en periode, og derefter beregner resultater ud fra de pakker, der returneres fra hvert hop. Da `Trace-HcsRoute` viser graden af pakketab på en given router eller et link, kan du finde hvilke routere eller links kan forårsage netværksproblemer. 

- `Get-HcsRoutingTable`: Brug denne cmdlet til at vise den lokale IP-routing-tabel.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Foretage fejlfinding af med Cmdletten Get-NetAdapter

Når du konfigurerer netværksgrænseflader til en enhed førstegangsbrugere installation, er hardware status ikke tilgængelig i tjenesten StorSimple Manager Brugergrænsefladen fordi enheden endnu ikke er registreret med tjenesten. Desuden siden Status for Hardware afspejler måske ikke altid korrekt tilstanden for enheden, især hvis der opstår problemer, der påvirker synkronisering af tjenesten. I følgende situationer kan du bruge den `Get-NetAdapter` cmdlet til at bestemme den tilstand og status for dit netværksgrænseflader.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Se en liste over alle netværkskort på din enhed

1. Starte Windows PowerShell til StorSimple, og skriv derefter `Get-NetAdapter`. 

2. Bruge output fra den `Get-NetAdapter` cmdlet og følgende retningslinjer at forstå status for dit netværksgrænseflade.
  - Hvis grænsefladen er sund og aktiveret, vises **ifIndex** status som **op**.
  - Hvis grænsefladen er i orden, men ikke fysisk forbundet (via et netværkskabel), vises **ifIndex** som **deaktiveret**.
  - Hvis grænsefladen er sund, men ikke aktiveret, vises **ifIndex** status som **NotPresent**.
  - Hvis grænsefladen ikke findes, vises den ikke på denne liste. Tjenesten StorSimple Manager Brugergrænsefladen viser stadig denne grænseflade i tilstanden mislykkedes.

Du kan finde flere oplysninger om, hvordan du bruger denne cmdlet ved at gå til [GetNetAdapter](https://technet.microsoft.com/library/jj130867.aspx) i reference til Windows PowerShell-cmdlet. 

De følgende afsnit vises eksempler på output fra den `Get-NetAdapter` cmdlet. 

 I disse eksempler controller 0 blev passive controller og blev konfigureret på følgende måde:

- DATA 0, RAPPORTDATA 1, 2 DATA og DATA 3 netværk grænseflader fandtes på enheden.
- DATA 4 og 5 DATA netværkskort var ikke Præsenter; de er derfor ikke angivet i output.
- DATA 0 er aktiveret.

Controller 1 er den aktive controller og blev konfigureret på følgende måde:

- DATA 0, RAPPORTDATA 1, DATA 2, 3 DATA, DATA 4 og 5 DATA netværk grænseflader fandtes på enheden.
- DATA 0 er aktiveret.

**Eksempel output – controller 0**

Følgende er output fra controller 0 (passive controller). DATA 1, 2 DATA og DATA 3 har ikke forbindelse. DATA 4 og 5 DATA vises ikke, fordi de ikke er til stede på enheden. 

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**Eksempel output – controller 1**

Følgende er output fra controller 1 (den aktive controller). Kun DATA 0 netværksgrænsefladen på enheden er konfigureret og fungerer.

     Controller1>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
     Ethernet 2           HCS VNIC                                    13       Up
     DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
     DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent

 
## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Foretage fejlfinding af med Cmdletten Test-forbindelse

Du kan bruge den `Test-Connection` til at finde ud af, om enheden StorSimple kan oprette forbindelse til eksterne netværk. Hvis alle netværk parametre, herunder DNS er konfigureret korrekt i konfigurationsguiden, kan du bruge den `Test-Connection` -cmdlet til pinge en kendte adressen uden for netværket, som outlook.com. 

Du skal aktivere ping til at udføre fejlfinding på forbindelsesproblemer med denne cmdlet Hvis ping er deaktiveret.

Se følgende eksempler på output fra den `Test-Connection` cmdlet. 

> [AZURE.NOTE] I eksemplet første er enheden konfigureret med en forkert DNS. DNS-Posterne er korrekt i det andet eksempel.
 
**Eksempel output – forkerte DNS**

I det følgende eksempel er der ikke nogen output for IPV4 og IPV6-adresser, som angiver, at DNS ikke er løst. Det betyder, at der er ingen forbindelse til det eksterne netværk og en korrekte DNS skal tilføres. 

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**Eksempel på output – rette DNS**

I det følgende eksempel returnerer DNS IPv4-adresse, der angiver, at DNS-Posterne er konfigureret korrekt. Dette bekræfter, at der er forbindelse til det eksterne netværk. 

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Foretage fejlfinding af med Cmdletten Test-HcsmConnection

Brug den `Test-HcsmConnection` cmdlet til en enhed, der allerede er tilsluttet og registreret hos din StorSimple Manager-tjeneste. Denne cmdlet hjælper dig med at kontrollere forbindelsen mellem en enhed med registrerede og tilsvarende StorSimple Manager-tjenesten. Du kan køre denne kommando på Windows PowerShell til StorSimple. 

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Til at køre følgende cmdlet Test-HcsmConnection

1. Sørg for, at enheden er registreret.

2. Kontrollere Enhedsstatus for. Hvis enheden er deaktiveret, i vedligeholdelsestilstand eller offline, kan du muligvis vist følgende fejl: 

   - ErrorCode.CiSDeviceDecommissioned – dette angiver, at enheden er deaktiveret.
   - ErrorCode.DeviceNotReady – dette angiver, at enheden er i vedligeholdelsestilstand.
   - ErrorCode.DeviceNotReady – dette angiver, at enheden ikke er online.

3. Kontrollér, at tjenesten StorSimple Manager kører (Brug cmdlet'en [Get-ClusterResource](https://technet.microsoft.com/library/ee461004.aspx) ). Hvis tjenesten ikke kører, kan du muligvis vist følgende fejl:

   - ErrorCode.CiSApplianceAgentNotOnline
   - ErrorCode.CisPowershellScriptHcsError – dette angiver, at der opstod en undtagelse, når du har kørt Get-ClusterResource.

4. Markér tokenet Access ACS (Control Service). Hvis det viser en web undtagelse, kan det være resultatet af et problem med gateway, en manglende godkendelse af proxyen, en forkert DNS eller en godkendelsesfejl. Du muligvis vist følgende fejl:

   - ErrorCode.CiSApplianceGateway – angiver en HttpStatusCode.BadGateway undtagelse: navn resolver-tjenesten kunne ikke fortolkes værtsnavnet. 
   - ErrorCode.CiSApplianceProxy – angiver en HttpStatusCode.ProxyAuthenticationRequired undtagelse (http-statuskode 407): klienten kunne ikke godkende med proxy-server. 
   - ErrorCode.CiSApplianceDNSError – angiver en WebExceptionStatus.NameResolutionFailure undtagelse: navn resolver-tjenesten kunne ikke fortolkes værtsnavnet.
   - ErrorCode.CiSApplianceACSError – dette angiver, at tjenesten returnerede fejlen godkendelse, men der er forbindelse.
   
    Hvis det ikke Udløs en web undtagelse, søge efter ErrorCode.CiSApplianceFailure. Dette angiver, at maskinen mislykkedes.

5. Kontroller skyen service forbindelsen. Hvis tjenesten viser en web undtagelse, kan du muligvis vist følgende fejl:

  - ErrorCode.CiSApplianceGateway – angiver en HttpStatusCode.BadGateway undtagelse: en mellemliggende proxyserver modtaget en forkert anmodning fra en anden proxy eller fra den oprindelige server.
  - ErrorCode.CiSApplianceProxy – angiver en HttpStatusCode.ProxyAuthenticationRequired undtagelse (http-statuskode 407): klienten kunne ikke godkende med proxy-server. 
  - ErrorCode.CiSApplianceDNSError – angiver en WebExceptionStatus.NameResolutionFailure undtagelse: navn resolver-tjenesten kunne ikke fortolkes værtsnavnet.
  - ErrorCode.CiSApplianceACSError – dette angiver, at tjenesten returnerede fejlen godkendelse, men der er forbindelse.
  
    Hvis det ikke Udløs en web undtagelse, søge efter ErrorCode.CiSApplianceSaasServiceError. Her angives et problem med tjenesten StorSimple Manager.
 
6. Du kan kontrollere Azure Service Bus internetforbindelsen. ErrorCode.CiSApplianceServiceBusError angiver, at enheden ikke oprette forbindelse til tjenesten Bus.
 
Logfiler CiSCommandletLog0Curr.errlog og CiSAgentsvc0Curr.errlog vil have flere oplysninger som oplysninger om undtagelser. 

Reference dokumentation kan finde flere oplysninger om, hvordan du kan bruge cmdlet'en til, skal du gå til [Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) i Windows PowerShell.

> [AZURE.IMPORTANT] Du kan køre denne cmdlet til både aktiv og passiv controller. 
 
Se følgende eksempler på output fra den `Test-HcsmConnection` cmdlet. 

**Eksempel på output – blev registreret enhed, der kører StorSimple Release (marts 2014)**

Første prøven er fra en enhed, der er registreret med tjenesten StorSimple Manager og har ingen problemer med serverforbindelsen. 

     Controller1>Test-HcsmConnection -verbose
     Checking device state  ... Success.
     Device registered successfully
     Checking device authentication.  ... This operation will take few minutes to complete....
     Checking device authentication.  ... Success.
     Checking connectivity from device to StorSimple Manager service.  ... This operation will take few minutes to complete....
     Checking connectivity from device to StorSimple Manager service.  ... Success.
     Checking connectivity from StorSimple Manager service to StorSimple device. .... Success.
     Controller1>

**Eksempel på output – blev registreret enhed kører StorSimple opdatering 1**

Hvis du kører opdatering 1 på enheden StorSimple, skal du ikke at køre den med parameteren detaljeret.

      Controller1>Test-HcsmConnection
       
      Checking device registration state  ... Success
      Device registered successfully
       
      Checking primary NTP server [time.windows.com] ... Success
       
      Checking web proxy  ... NOT SET
       
      Checking primary IPv4 DNS server [10.222.118.154] ... Success
      Checking primary IPv6 DNS server  ... NOT SET
      Checking secondary IPv4 DNS server [10.222.120.24] ... Success
      Checking secondary IPv6 DNS server  ... NOT SET
       
      Checking device online  ... Success
 
      Checking device authentication  ... This will take a few minutes.
      Checking device authentication  ... Success
       
      Checking connectivity from device to service  ... This will take a few minutes.
       
      Checking connectivity from device to service  ... Success
       
      Checking connectivity from service to device  ... Success
       
      Checking connectivity to Microsoft Update servers  ... Success
      Controller1>

**Eksempel på output – offline enhed, der kører StorSimple Release (marts 2014)**

Dette eksempel er fra en enhed, der har status som **Offline** på portalen Azure klassisk.

     Checking device state: Success 
     Device is registered successfully 
     Checking connectivity from device to SaaS.. Failure

Enheden kan ikke oprette forbindelse ved hjælp af den aktuelle web proxykonfiguration. Det kan skyldes et problem med web proxykonfigurationen eller et problem med netværksforbindelsen. I dette tilfælde skal du sikre dig, at dine web proxyindstillinger er korrekte, og dine web proxyservere er online og tilgængelig. 

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Foretage fejlfinding af med Cmdletten Synkroniser HcsTime

Brug denne cmdlet enhed tiden skal vises. Hvis enheden tiden har en forskydning med NTP-serveren, kan du derefter bruge denne cmdlet til at synkronisere tiden med kraft, med din NTP-server. Hvis forskydning mellem enheden og NTP-server er større end 5 minutter, får du vist en advarsel. Hvis forskydningen er større end 15 minutter, så gå enheden offline. Du kan stadig bruge denne cmdlet tvinge synkronisering af klokkeslæt. Men hvis offset overskrider 15 timer, derefter du vil ikke kunne kraft-synkronisering, vises tidspunktet, og en fejlmeddelelse.

**Eksempel på output – tvunget tid synkronisere ved hjælp af synkronisering HcsTime**
 
     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.
 
     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Foretage fejlfinding af med Aktivér HcsPing og Deaktiver HcsPing cmdletter

Brug disse cmdlet'er til at sikre, at netværksgrænseflader på din enhed besvare ICMP ping anmodninger. Som standard reagerer StorSimple netværksgrænseflader ikke på ping anmodninger. Brug af denne cmdlet er den nemmeste måde at vide om din enhed er online og tilgængelig.  

**Eksempel på output – Aktivér HcsPing og Deaktiver HcsPing**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Foretage fejlfinding af med spor HcsRoute cmdlet

Brug denne cmdlet som et værktøj til netværkssporing distribution. Den sender pakker til hver router hele vejen til den endelige destination over en periode, og derefter beregner resultater ud fra de pakker, der returneres fra hvert hop. Fordi cmdlet viser graden af pakketab på en hvilken som helst router eller link, kan du finde hvilke routere eller links kan forårsage netværksproblemer.

**Eksempel på output viser, hvordan du spore ruten af en pakke med spor HcsRoute**

     Controller0>Trace-HcsRoute -Target 10.126.174.25
     
     Tracing route to contoso.com [10.126.174.25]
     over a maximum of 30 hops:
       0  HCSNode0 [10.126.173.90]
       1  contoso.com [10.126.174.25]
      
     Computing statistics for 25 seconds...
                 Source to Here   This Node/Link
     Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
       0                                           HCSNode0 [10.126.173.90]
                                     0/ 100 =  0%   |
       1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
      [10.126.174.25]
      
     Trace complete.

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Foretage fejlfinding af med Cmdletten Get-HcsRoutingTable

Brug denne cmdlet til at få vist tabellen routing til enheden StorSimple. En routing tabel er et sæt regler, der kan hjælpe med at finde ud af, hvor datapakker, der er ude at rejse via et netværk (Internetprotokol) omdirigeret. 

Routing tabellen viser grænsefladerne og gateways, som omdirigerer data til de angivne netværk. Det giver også den routing metrikværdi, som er beslutning maker for den sti, når en bestemt destination. Nederst routing-metrik, jo højere prioritet. 

Eksempelvis hvis du har 2 netværksgrænseflader, DATA 2 og 3, skal du DATA forbindelse til internettet. Hvis routing omfanget af DATA 2 og 3 DATA er 15 og 261 henholdsvis, er DATA 2 med en lavere routing metrikværdi grænsefladen Foretrukne, der bruges til at få adgang til internettet.

Hvis du kører opdatering 1 på enheden StorSimple, har grænsefladen dine DATA med 0 netværk højeste indstillingen til skyen trafikken. Det betyder, at selvom der er andre cloud-aktiverede brugergrænseflader, vil skyen trafikken dirigeres gennem DATA 0. 

Hvis du støder på `Get-HcsRoutingTable` cmdlet uden at angive alle parametre (som i følgende eksempel vises), vil cmdlet output både IPv4 og IPv6 routing tabeller. Du kan også angive `Get-HcsRoutingTable -IPv4` eller `Get-HcsRoutingTable -IPv6` at få en relevant routing tabel.

      Controller0>
      Controller0>Get-HcsRoutingTable
      ===========================================================================
      Interface List
       14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
       12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
       13...28 18 78 bc 4b 85 ......HCS VNIC
        1...........................Software Loopback Interface 1
       21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
       22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
      ===========================================================================
       
      IPv4 Route Table
      ===========================================================================
      Active Routes:
      Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
              127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
              127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
            169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
          169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
        169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
          192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
        192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
        192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
              224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
              224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
              224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
        255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
        255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
      ===========================================================================
      Persistent Routes:
        Network Address          Netmask  Gateway Address  Metric
                0.0.0.0          0.0.0.0  192.168.111.100       5
      ===========================================================================
       
      IPv6 Route Table
      ===========================================================================
      Active Routes:
       If Metric Network Destination      Gateway
        1    306 ::1/128                  On-link
       13    276 fd99:4c5b:5525:d80b::/64 On-link
       13    276 fd99:4c5b:5525:d80b::1/128
                                          On-link
       13    276 fd99:4c5b:5525:d80b::3/128
                                          On-link
       13    276 fe80::/64                On-link
       12    261 fe80::/64                On-link
       13    276 fe80::17a:4eba:7c80:727f/128
                                          On-link
       12    261 fe80::fc97:1a53:e81a:3454/128
                                          On-link
        1    306 ff00::/8                 On-link
       13    276 ff00::/8                 On-link
       12    261 ff00::/8                 On-link
       14    266 ff00::/8                 On-link
      ===========================================================================
      Persistent Routes:
        None
       
      Controller0>
 
## <a name="step-by-step-storsimple-troubleshooting-example"></a>Trinvis fejlfinding StorSimple-eksempel

I følgende eksempel viser en trinvis fejlfinding for en StorSimple-installation. I dette eksempel scenario mislykkes enhed registrering med en fejl, der angiver, at netværksindstillingerne eller DNS-navnet er forkert.

Den fejlmeddelelse, er:

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

Fejlen kan skyldes et af følgende:

- Forkert hardwareinstallation
- Fejlbehæftede netværk-grænseflader
- Forkert IP-adresse, undernetmaske, gateway, primære DNS-server eller webtjenesteproxy
- Forkert registreringsnøgle
- Forkert firewall-indstillinger

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Til at finde og løse problemet enhed registrering

1. Se din enhedskonfiguration: Kør på den aktive controller `Invoke-HcsSetupWizard`.

     > [AZURE.NOTE] Konfigurationsguiden skal køre på den aktive controller. For at bekræfte, at du har forbindelse til den aktive controller, kan du se på banneret præsenteres på seriel konsollen. Banner angiver, om du har forbindelse til controller 0 eller 1-controller, og om controlleren er aktiv eller passiv. Få mere at vide ved at gå til [Identificer en aktive controller på din enhed](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).
 
2. Sørg for, at enheden kabel korrekt: kontrollere netværket kabler på enheden tilbage plan. Der er specifikke for enhedsmodel på kabler. Gå til at [installere enheden StorSimple 8100](storsimple-8100-hardware-installation.md) eller [installere enheden StorSimple 8600](storsimple-8600-hardware-installation.md)kan finde flere oplysninger.

     > [AZURE.NOTE] Hvis du bruger 10 GbE netværksporte, skal du bruge de medfølgende QSFP SFP netværkskort og SFP kabler. Se [listen over kabler, parametre, og når du skal anbefales af OEM leverandør for Mellanox porte](http://www.mellanox.com/page/cables?mtag=cable_overview)kan finde flere oplysninger.
 
3. Kontrollere tilstanden for netværksgrænsefladen:

   - Brug cmdlet'en Get-NetAdapter til at registrere tilstanden for netværksgrænseflader til DATA 0. 
   - Hvis linket ikke er fungerer, angiver **ifindex** status, at grænsefladen er ned. Derefter skal du kontrollere netværksforbindelsen på port maskinen og Skift. Du skal også at udelukke forkert kabler. 
   - Hvis du har mistanke om, at DATAENE 0 port på den aktive controller mislykkes, kan du kontrollere det ved at oprette forbindelse til DATA 0 port på controller 1. For at bekræfte, fjern netværkskablet fra bagsiden af enheden fra controller 0, at kablet til controller 1 og derefter køre cmdlet'en Get-NetAdapter til igen. 
   Hvis DATAENE 0 port på mislykkes, en controller skal [du kontakte Microsoft Support](storsimple-contact-microsoft-support.md) til næste trin. Det være nødvendigt at erstatte controlleren på systemet.
 
4. Kontrollere forbindelsen til parameteren:
   - Sørg for, at DATAENE 0 netværksgrænseflader på controller 0 og controller 1 i din primære omslutning er på det samme undernet. 
   - Kontrollér hub eller router. Typisk, skal du oprette forbindelse begge enheder til den samme hub eller router. 
   - Sørg for, at de parametre, du bruger til forbindelsen har DATA 0 ved begge enheder i det samme vLAN.
   
5. Fjerne brugerfejl:

  - Kør konfigurationsguiden igen (Kør **Aktiver HcsSetupWizard**), og Angiv værdierne igen for at sikre, at der ikke er nogen fejl. 
  - Bekræfte registreringen nøgle, der bruges. Den samme registreringsnøgle kan bruges til at oprette forbindelse flere enheder til en StorSimple Manager-tjeneste. Brug proceduren i [få tasten service registrering](storsimple-manage-service.md#get-the-service-registration-key) til at sikre, at du bruger den korrekte registreringsnøgle.

    > [AZURE.IMPORTANT] Hvis du har flere tjenester, der kører, skal du sikre dig, at tasten registrering for tjenesten relevante bruges til at registrere enheden. Hvis du har registreret en enhed med den forkerte StorSimple Manager-tjeneste, skal du [kontakte Microsoft Support](storsimple-contact-microsoft-support.md) til næste trin. Du kan være nødvendigt at udføre en fabrik nulstilling af enheden (hvilket kan medføre tab af data) til derefter forbinde den til den tilsigtede tjeneste.

6. Brug testforbindelsen cmdlet til at bekræfte, at du har forbindelse til det eksterne netværk. Gå til [fejlfinding i forbindelse med med Cmdletten Test-forbindelse](#troubleshoot-with-the-test-connection-cmdlet)kan finde flere oplysninger.

7. Kontrollér, om firewall forstyrrelser. Hvis du har bekræftet, virtuelle IP-adresse (VIP), undernet, gateway og DNS-indstillingerne er alle korrekte, og du kan stadig se forbindelsesproblemer derefter det er muligt, at din firewall blokerer kommunikationen mellem din enhed og eksterne netværk. Du har brug at sikre, at port 80 og 443 er tilgængelige på enheden StorSimple for udgående kommunikation. Se [netværk krav til enheden StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device)kan finde flere oplysninger.

8. Se på logfiler. Gå til [Support-pakker og enhed hændelseslogge tilgængelig til fejlfinding](#support-packages-and-device-logs-available-for-troubleshooting).

9. Hvis de forrige trin ikke løser problemet, skal [du kontakte Microsoft Support](storsimple-contact-microsoft-support.md) for at få hjælp.

## <a name="next-steps"></a>Næste trin
[Lær, hvordan du udfører fejlfinding af en funktionel enhed](storsimple-troubleshoot-operational-device.md).

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 
