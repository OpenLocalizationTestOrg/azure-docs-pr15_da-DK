<properties
   pageTitle="Oprette en StorSimple supportpakke | Microsoft Azure"
   description="Lær at oprette, dekryptere og redigere en supportpakke til enheden StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/17/2016"
   ms.author="alkohli" />


# <a name="create-and-manage-a-storsimple-support-package"></a>Oprette og administrere en StorSimple support-pakke

## <a name="overview"></a>Oversigt

En StorSimple support-pakke er en nem at bruge metode, der indsamles af alle relevante logfiler for at hjælpe Microsoft Support med at foretage fejlfinding af problemer, StorSimple enhed. Indsamlede logfiler er krypteret og komprimeres.

Dette selvstudium indeholder en trinvis vejledning til at oprette og administrere support-pakken.

## <a name="create-and-upload-a-support-package-in-the-azure-classic-portal"></a>Oprette og overføre en supportpakke i portalen Azure klassisk

Du kan oprette og overføre en supportpakke til webstedet Microsoft Support via siden **vedligeholdelse** af tjenesten på portalen Azure klassisk.

> [AZURE.NOTE] Upload kræver en support adgangsnøgle. Din support engineering kan give dette for dig i en mail.

En krypteret og komprimeret support-pakken (.cab-fil) er oprettet og overføres til webstedet for Support. Understøttelse af engineering kan derefter hente denne pakke fra webstedet til Support til at løse problemet.

Udfør følgende trin i klassisk portalen for at oprette en supportpakke.

#### <a name="to-create-a-support-package-in-the-azure-classic-portal"></a>Sådan oprettes en supportpakke i portalen Azure klassisk

1. Vælg **enheder** > **vedligeholdelse**.

2. Vælg **Opret og Overfør support-pakken**, i sektionen **understøtter pakke** .

3. I dialogboksen **Opret og Overfør support-pakken** skal du gøre følgende:

    ![Oprette supportpakke](./media/storsimple-create-manage-support-package/IC740923.png)

    - Indtast adgangsnøglen i tekstfeltet **Support adgangsnøgle** . Din Microsoft support engineering skal sende denne adgangsnøgle for dig i en mail.

    - Markér afkrydsningsfeltet for at give samtykke for automatisk at overføre pakken support til webstedet for Microsoft Support.

    - Klik på ikonet Kontrollér ![Ikonet](./media/storsimple-create-manage-support-package/IC740895.png).


## <a name="manually-create-a-support-package"></a>Manuelt oprette en supportpakke

I nogle tilfælde skal du manuelt oprette pakken support via Windows PowerShell til StorSimple. Eksempel:

- Hvis du har brug at fjerne følsomme oplysninger fra din logfiler, før du deler med Microsoft Support.

- Hvis du har problemer med overførsel af pakke på grund af problemer med serverforbindelsen.

Du kan dele din manuelt oprettet support-pakken med Microsoft Support via mail. Udfør følgende trin for at oprette en supportpakke i Windows PowerShell til StorSimple.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Sådan oprettes en supportpakke i Windows PowerShell til StorSimple

1. Skriv følgende kommando for at starte en session med Windows PowerShell som administrator på fjerncomputeren, der bruges til at oprette forbindelse til enheden StorSimple skal:

    `Start PowerShell`

2. I Windows PowerShell-session, oprette forbindelse til konsollen SSAdmin på din enhed:

    - Ved kommandoprompten skal du angive:

        `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`

    1. Angiv din administratoradgangskode enhed i den dialogboks, der åbnes. Standardadgangskoden, er:

        `Password1`

        ![Dialogboksen til PowerShell legitimationsoplysninger](./media/storsimple-create-manage-support-package/IC740962.png)

    2. Klik på **OK**.
    1. Ved kommandoprompten skal du angive:

        `Enter-PSSession $MS`

3. Angiv den relevante kommando i den session, der åbnes.

    - Angiv for netværksdrev, der er beskyttet med adgangskode:

        `Export-HcsSupportPackage –PackageTag "MySupportPackage" –Credential "Username" -Force`

        Du bliver bedt om en adgangskode, en sti til den delte netværksmappe og en adgangskode til kryptering (da support-pakken er krypteret). En support-pakken der oprettes i den angivne mappe.

    - For aktier, der ikke er beskyttet med adgangskode, du ikke skal bruge den `-Credential` parameter. Angiv følgende oplysninger:

        `Export-HcsSupportPackage –PackageTag "MySupportPackage" -Force`

        Support-pakken oprettes ved begge enheder i den angivne delte netværksmappe. Det er en krypteret komprimeret fil, der kan sendes til Microsoft Support i forbindelse med fejlfinding. Du kan finde flere oplysninger [Kontakt Microsoft Support](storsimple-contact-microsoft-support.md).


### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Eksportér HcsSupportPackage cmdlet-parametre
Du kan bruge følgende parametre med Eksportér HcsSupportPackage cmdlet.

| Parameter            | Påkrævede/valgfrie | Beskrivelse                                                                                                                                                             |
|----------------------|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `-Path`                 | Påkrævet          | Bruge til at angive placeringen af den delte netværksmappe, hvor support-pakken er placeret.                                                                 |
| `-EncryptionPassphrase` | Påkrævet          | Bruge til at angive en adgangskode for at kryptere support-pakken.                                                                                                        |
| `-Credential`           | Valgfri          | Bruge til at angive legitimationsoplysninger for den delte netværksmappe.                                                                                        |
| `-Force`                | Valgfri          | Bruge til at springe kryptering adgangskoden bekræftelse.                                                                                                                |
| `-PackageTag`           | Valgfri          | Bruge til at angive en mappe under *stien* , hvor support-pakken er placeret. Standard er [enhedens navn]-[dags dato og time:yyyy-MM-dd-HH-mm-ss].       |
| `-Scope`                | Valgfri          | Angiv som **klynge** (standard) til at oprette en support-pakken ved begge enheder. Hvis du vil oprette en pakke kun for den aktuelle controller, skal du angive **Controller**. |


## <a name="edit-a-support-package"></a>Redigere en supportpakke

Når du har oprettet en supportpakke, skal du muligvis redigere pakken for at fjerne følsomme oplysninger. Dette kan omfatte lydstyrken navne, enhed IP-adresser og sikkerhedskopiering navne fra logfilerne.

> [AZURE.IMPORTANT] Du kan kun redigere en supportpakke, der blev oprettet via Windows PowerShell for StorSimple. Du kan ikke redigere en pakke, der er oprettet i Azure klassisk portalen med tjenesten StorSimple Manager.

Redigere en support-pakken før du overfører det på webstedet Microsoft Support, først dekryptere support-pakken, redigere filerne, og derefter kryptere den igen. Udfør følgende trin.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Redigere en supportpakke i Windows PowerShell til StorSimple

1. Oprette en supportpakke, som beskrevet tidligere, i [at oprette en supportpakke i Windows PowerShell til StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).

2. [Hente scriptet](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) lokalt på din klient.

3. Importere Windows PowerShell-modulet. Angiv stien til den lokale mappe, hvor du har hentet scriptet. For at importere modulet skal du angive:

    `Import-module <Path to the folder that contains the Windows PowerShell script>`

4. Alle filer er *.aes* , som er komprimeret og krypteret. For at dekomprimere og dekryptere filer skal du angive:

    `Open-HcsSupportPackage <Path to the folder that contains support package files>`

    Bemærk, at de faktiske filtypenavne nu vises for alle filerne.

    ![Redigere support-pakken](./media/storsimple-create-manage-support-package/IC750706.png)

5. Når du bliver bedt om kryptering adgangskoden, kan du angive den adgangskode, du brugte, da support-pakken blev oprettet.

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:EncryptionPassphrase: ****

6. Gå til den mappe, der indeholder logfilerne. Da logfilerne er nu dekomprimeret og dekrypteres, har disse oprindelige filtypenavne. Redigere disse filer for at fjerne alle kunde-specifikke oplysninger, som lydstyrken navne og enhed IP-adresser, og Gem filerne.

7. Luk filer for at komprimere dem med gzip og kryptere dem med AES 256. Dette er til hastighed og sikkerhed i overførsel af pakken support via et netværk. Hvis du vil komprimere og kryptere filer, skal du angive følgende:

    `Close-HcsSupportPackage <Path to the folder that contains support package files>`

    ![Redigere support-pakken](./media/storsimple-create-manage-support-package/IC750707.png)

8. Når du bliver bedt om det, du give en kryptering adgangskoden til pakken ændret support.

        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****

9. Skriv den nye adgangskode, så kan du dele den med Microsoft Support når der anmodes om.


### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Eksempel: Redigere filer i en support-pakken på en adgangskodebeskyttet del

I følgende eksempel viser, hvordan dekryptere, redigere, og kryptere en supportpakke igen.

        PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

        PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Close-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32>

## <a name="next-steps"></a>Næste trin

- Lær, hvordan du [bruger supportpakker og enhed hændelseslogge at foretage fejlfinding af installationen af enhed](storsimple-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).

- Lær, hvordan du [bruger tjenesten StorSimple Manager for at administrere din StorSimple enhed](storsimple-manager-service-administration.md).
