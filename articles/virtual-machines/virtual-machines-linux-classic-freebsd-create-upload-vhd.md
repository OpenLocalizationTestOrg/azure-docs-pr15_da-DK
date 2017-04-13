<properties
   pageTitle="Oprette og overføre et billede af FreeBSD VM | Microsoft Azure"
   description="Lær at oprette og overføre en virtuel harddisk (Virtuelle), der indeholder FreeBSD operativsystemet for at oprette en Azure virtuelt"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/29/2016"
   ms.author="kyliel"/>

# <a name="create-and-upload-a-freebsd-vhd-to-azure"></a>Oprette og overføre en FreeBSD VHD til Azure

I denne artikel beskrives, hvordan til at oprette og overføre en virtuel harddisk (Virtuelle), der indeholder FreeBSD operativsystemet. Når du overfører det, kan du bruge det som dit eget billede til at oprette en virtuel maskine (VM) i Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


## <a name="prerequisites"></a>Forudsætninger
I denne artikel antager, at du har følgende elementer:

- **En Azure abonnement**– Hvis du ikke har en konto, kan du oprette én på blot et par minutter. Hvis du har et MSDN-abonnement, skal du se [månedlige Azure kredit for Visual Studio abonnenter.](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Ellers skal se, hvordan du [opretter en gratis prøveversion konto](https://azure.microsoft.com/pricing/free-trial/).  

- **Azure PowerShell værktøjer**– feltet Azure PowerShell modul skal være installeret og konfigureret til at bruge dit Azure-abonnement. For at hente modulet, skal du se [Azure-overførsler](https://azure.microsoft.com/downloads/). Et selvstudium, der beskriver, hvordan installere og konfigurere modulet findes her. Brug cmdlet'en [Azure overførsler](https://azure.microsoft.com/downloads/) til at overføre den virtuelle harddisk.

- **FreeBSD operativsystemet er installeret i en .vhd-fil**– et understøttes FreeBSD operativsystem skal installeres til en virtuel harddisk. Der findes flere værktøjer for at oprette .vhd-filer. For eksempel kan du bruge en virtualization løsning som Hyper-V til at oprette .vhd-filen, og Installer operativsystemet. Finde oplysninger om, hvordan du kan installere og bruge Hyper-V, [installere Hyper-V og oprette et virtuelt](http://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] Det nyere VHDX format understøttes ikke i Azure. Du kan konvertere disken til Virtuelle format ved hjælp af Hyper-V Manager eller cmdlet [Konvertér virtuelle](https://technet.microsoft.com/library/hh848454.aspx). Desuden er der et [selvstudium på MSDN om, hvordan du bruger FreeBSD med Hyper-V](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx).

Denne opgave omfatter følgende fem trin.

## <a name="step-1-prepare-the-image-for-upload"></a>Trin 1: Forbered billedet til at uploade

På den virtuelle maskine, hvor du har installeret operativsystemet FreeBSD, ved at udføre følgende procedurer:

1. Aktivere DHCP.

        # echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
        # service netif restart

2. Aktivere SSH.

    SSH er aktiveret som standard efter installationen fra disk. Hvis det ikke er aktiveret eller anden grund, eller hvis du bruger FreeBSD VHD direkte, Skriv følgende:

        # echo 'sshd_enable="YES"' >> /etc/rc.conf
        # ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key
        # ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
        # service sshd restart

3. Konfigurere en seriel konsol.

        # echo 'console="comconsole vidconsole"' >> /boot/loader.conf
        # echo 'comconsole_speed="115200"' >> /boot/loader.conf

4. Installere sudo.

    Rod-konto er deaktiveret i Azure. Det betyder, at du har brug at anvende sudo fra en uden privilegier bruger til at køre kommandoer med administratorrettigheder.

        # pkg install sudo
;
5. Forudsætninger for Azure Agent.

        # pkg install python27  
        # pkg install Py27-setuptools27   
        # ln -s /usr/local/bin/python2.7 /usr/bin/python   
        # pkg install git

6. Installer Azure Agent.

    Den nyeste version af Azure Agent kan altid findes på [github](https://github.com/Azure/WALinuxAgent/releases). Den 2.0.10 + officielt understøtter FreeBSD 10 og 10.1, og versionen 2.1.4 understøtter officielt FreeBSD 10.2 og nyere versioner.

        # git clone https://github.com/Azure/WALinuxAgent.git  
        # cd WALinuxAgent  
        # git tag  
        …
        WALinuxAgent-2.0.16
        …
        v2.1.4
        v2.1.4.rc0
        v2.1.4.rc1

    2.0, Lad os bruge 2.0.16 som et eksempel:

        # git checkout WALinuxAgent-2.0.16
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  

    2.1, Lad os bruge 2.1.4 som et eksempel:

        # git checkout v2.1.4
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  
        # ln -sf /usr/local/sbin/waagent2.0 /usr/sbin/waagent2.0

    >[AZURE.IMPORTANT] Når du installerer Azure Agent, er det en god ide at kontrollere, at den kører:

        # waagent -version
        WALinuxAgent-2.1.4 running on freebsd 10.3
        Python: 2.7.11
        # service –e | grep waagent
        /etc/rc.d/waagent
        # cat /var/log/waagent.log

7. Deprovision systemet.

    Deprovision systemet for at rydde den og gøre det passer til klargøring af igen. Følgende kommando sletter også sidste klargjort brugerkontoen og de tilknyttede data:

        # echo "y" |  /usr/local/sbin/waagent -deprovision+user  
        # echo  'waagent_enable="YES"' >> /etc/rc.conf

    Nu kan du lukke din VM.

## <a name="step-2-create-a-storage-account-in-azure"></a>Trin 2: Oprette en lagerplads konto i Azure ##

Du har brug for en lagerplads konto i Azure at overføre en .vhd-fil, så den kan bruges til at oprette en virtuel maskine. Du kan bruge Azure klassisk portalen til at oprette en lagerplads konto.

1. Log på [Azure klassisk portal](https://manage.windowsazure.com).

2. På kommandolinjen skal du vælge **Ny**.

3. Vælg **Data Services** > **lagerplads** > **Hurtig oprettelse**.

    ![Hurtigt oprette en lagerplads-konto](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storage-quick-create.png)

4. Udfyld felterne på følgende måde:

    - Skriv et navn på underdomæne til brug i lagerplads konto URL-adressen i feltet **URL-adresse** . Posten kan indeholde fra 3-24 tal og bogstaver. Dette navn bliver værtsnavnet i URL-adressen, der bruges til at løse Azure Blob-lager, Azure kø lagerplads eller Azure-tabel lagerplads ressourcer til abonnementet.

    - Vælg den **placering eller forbindelse gruppe** for kontoen lagerplads i rullemenuen **Placering/forbindelse gruppe** . En forbindelse gruppe kan du få dine skytjenester og lagring i det samme datacenter.

    - Beslut, om at bruge **Geografisk overflødige** gentagelse til kontoen lagerplads, i feltet **Gentagelse** . Geografisk replikering er aktiveret som standard. Denne indstilling kopieres dataene til en sekundær placering uden omkostninger for dig, så din lagerplads mislykkes til den nye placering, hvis en overordnet fejl optræder i den primære placering. Den sekundære placering tildeles automatisk og kan ikke ændres. Hvis du har brug for mere kontrol over placeringen af din skybaserede lagerplads på grund af lovmæssige krav eller organisatoriske politik, kan du deaktivere geografisk gentagelse. Dog være opmærksom på, hvis du senere vil slå geografisk gentagelse, du vil betale et enkeltstående data filoverførsel gebyr kan replikeres dine eksisterende data til den sekundære placering. Lagerplads tjenester uden geografisk gentagelse tilbydes på rabat. Flere oplysninger om administration af geografisk replikering af lagerplads konti kan findes her: [oprette, administrere, eller slette en lagerplads konto](../storage-create-storage-account/#replication-options).

    ![Angiv oplysninger om lagerplads](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storage-create-account.png)


5. Vælg **Opret lagerplads konto**. Kontoen vises nu under **lagerplads**.

    ![Lagerplads konto, der blev oprettet](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storagenewaccount.png)

6. Dernæst skal du oprette en objektbeholder til dine overførte .vhd-filer. Vælg kontonavnet lagerplads, og vælg derefter **beholdere**.

    ![Oplysning om lagerplads konto](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_detail.png)

7. Vælg **Opret en objektbeholder**.

    ![Oplysning om lagerplads konto](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_container.png)

8. Skriv et navn til din objektbeholder i feltet **navn** . Vælg derefter i rullemenuen **Access** hvilken type af access-politik, du vil.

    ![Navnet på objektbeholderen](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_containervalues.png)

    > [AZURE.NOTE] Som standard beholderen er privat, og kan kun åbnes af ejeren af kontoen. For at tillade offentlige læseadgang til BLOB i beholderen, men ikke på objektbeholder egenskaber og metadata, skal du bruge indstillingen **Offentlige Blob** . For at give fulde offentlige læseadgang for objektbeholder og BLOB skal du bruge indstillingen **Offentlige objektbeholder** .

## <a name="step-3-prepare-the-connection-to-azure"></a>Trin 3: Forberede forbindelsen til Azure

Før du kan overføre en .vhd-fil, skal du oprette en sikker forbindelse mellem din computer og abonnementet Azure. Du kan bruge metoden Azure Active Directory (Azure AD) eller metoden certifikat til at gøre dette.

### <a name="use-the-azure-ad-method-to-upload-a-vhd-file"></a>Brug Azure AD-metoden til at overføre en .vhd-fil

1. Åbn Azure PowerShell-konsollen.

2. Skriv følgende kommando:  
    `Add-AzureAccount`

    Denne kommando åbnes et vindue, hvor du kan logge på med din arbejds- eller skolekonto logon.

    ![PowerShell-vinduet](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/add_azureaccount.png)

3. Azure godkender og gemmer legitimationsoplysninger oplysninger. Derefter lukker det vinduet.

### <a name="use-the-certificate-method-to-upload-a-vhd-file"></a>Bruge metoden certifikat til at overføre en .vhd-fil

1. Åbn Azure PowerShell-konsollen.

2. Type:  `Get-AzurePublishSettingsFile`.

3. Et browservindue åbnes og beder dig om at hente en .publishsettings fil. Denne fil indeholder oplysninger og et certifikat for abonnementet Azure.

    ![Browser overførselssiden](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Browser_download_GetPublishSettingsFile.png)

3. Gem filen .publishsettings.

4. Type:  `Import-AzurePublishSettingsFile <PathToFile>`, hvor `<PathToFile>` er den fulde sti til filen .publishsettings.

   Se [Introduktion til Azure-cmdletter](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx)kan finde flere oplysninger.

   Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md)kan finde flere oplysninger om installation og konfiguration af PowerShell.

## <a name="step-4-upload-the-vhd-file"></a>Trin 4: Overføre .vhd-filen

Når du overfører .vhd-filen, kan du placere det et vilkårligt sted i dit Blob-lager. Her er nogle ord, du skal bruge, når du overfører filen:
-  **BlobStorageURL** er URL-adressen for lagerplads-konto, du oprettede i trin 2.
-  **YourImagesFolder** er beholderen i Blob-lager, hvor du vil gemme dine billeder.
- **VHDName** er det navn, der vises i Azure klassisk portalen for at identificere den virtuelle harddisk.
- **PathToVHDFile** er den fulde sti og navnet på filen .vhd.


Skriv fra Azure PowerShell vinduet du har brugt i det forrige trin:

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## <a name="step-5-create-a-vm-with-the-uploaded-vhd-file"></a>Trin 5: Oprette en VM med overførte .vhd-filen
Når du overfører .vhd-filen, kan du tilføje den som et billede til listen over brugerdefinerede billeder, der er knyttet til dit abonnement og oprette en virtuel maskine med dette brugerdefinerede billede.

1. Skriv fra Azure PowerShell vinduet du har brugt i det forrige trin:

        Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

    > [AZURE.NOTE]Brug Linux OS filtype. Den aktuelle version af Azure PowerShell accepterer kun "Linux" eller "Windows" som en parameter.

2. Når du har fuldført de forrige trin, er det nye billede vises, når du klikker på fanen **billeder** på portalen Azure klassisk.  

    ![Vælg et billede](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/addfreebsdimage.png)

3. Oprette en virtuel maskine fra galleriet. Dette billede er nu tilgængelig under **Mine billeder**.
4. Vælg det nye billede. Derefter skal du gå til instruktionerne til at konfigurere en værtsnavn, adgangskode, SSH nøgle osv.

    ![Brugerdefineret billede](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/createfreebsdimageinazure.png)

4. Når du har fuldført klargøringen, får du vist din FreeBSD VM kører i Azure.

    ![Billede af FreeBSD i azure](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/freebsdimageinazure.png)
