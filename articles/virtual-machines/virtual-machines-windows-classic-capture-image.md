<properties
    pageTitle="Hent et billede af en Azure Windows VM | Microsoft Azure"
    description="Hent et billede på en Windows Azure virtuelle maskine, der er oprettet med den klassiske implementeringsmodel."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

#<a name="capture-an-image-of-an-azure-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Hent et billede på en Windows Azure virtuelle maskine, der er oprettet med den klassiske implementeringsmodel.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcestyring kan finde modeloplysninger i [oprette en kopi Windows VM kører i Azure](virtual-machines-windows-vhd-copy.md).


I denne artikel beskrives, hvordan til at registrere en Azure virtuelt, der kører Windows, så du kan bruge den som et billede til at oprette andre virtuelle maskiner. Dette billede omfatter operativsystem disken og en hvilken som helst datadisce, der er knyttet til den virtuelle maskine. Det omfatter ikke netværk konfigurationer, så du skal konfigurere dem, når du opretter på andre virtuelle maskiner, der bruger billedet.

Azure gemmer billede under **Mit billeder**. Dette er den samme sted, hvor du har overført billeder er gemt. Se [om billeder til virtuelle maskiner](virtual-machines-linux-classic-about-images.md)for at få mere at vide om billeder.

##<a name="before-you-begin"></a>Inden du går i gang##

Disse trin antager, at du allerede har oprettet en Azure virtuelt og konfigureret det operativsystem, herunder vedhæfte en hvilken som helst datadisce. Hvis du ikke har gjort det endnu, kan du se disse instruktioner:

- [Oprette en virtuel maskine fra et billede](virtual-machines-windows-classic-createportal.md)
- [Hvordan du vedhæfter en datadisk til en virtuel maskine](virtual-machines-windows-classic-attach-disk.md)
- Kontrollér, at serverrollerne understøttes med Sysprep. Du kan finde yderligere oplysninger finder [Sysprep-understøttelse af serverroller](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [AZURE.WARNING] Denne proces sletter den oprindelige virtuelle maskine, når det er hentet. 

Det anbefales virtuelt target sikkerhedskopieres før caputuring et billede af en Azure virtuelt. Azure virtuelle maskiner kan sikkerhedskopieres Azure sikkerhedskopiering. Yderligere oplysninger finder du [sikkerhedskopiere Azure virtuelle computere](../backup/backup-azure-vms.md). Andre løsninger, der er tilgængelige fra certificeret partnere. Søge på Azure Marketplace for at finde ud af, hvad er tilgængelig i øjeblikket.


##<a name="capture-the-virtual-machine"></a>Registrere den virtuelle maskine

1. I [Azure klassisk portal](http://manage.windowsazure.com), **Opret forbindelse** til den virtuelle maskine. Finde en vejledning, se, [hvordan du logger på en virtuel maskine, der kører Windows Server] [].

2.  Åbn et kommandopromptvindue som administrator.

3.  Ændre mappen, der skal `%windir%\system32\sysprep`, og derefter køre sysprep.exe.

4.  Dialogboksen **Værktøj til forberedelse af System** vises. Benyt følgende fremgangsmåde:

    - Vælg **Angiv System Out of Box Experience (OOBE)** i **System oprydning handling**, og Sørg for, at **generalisere** er markeret. Finde flere oplysninger om brug af Sysprep [Sådan Brug Sysprep: en introduktion][].

    - Vælg **Luk computeren**i **Indstillinger for lukning**.

    - Klik på **OK**.

    ![Køre Sysprep](./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png)

7.  Sysprep lukker den virtuelle maskine, som ændrer status for den virtuelle maskine i portalen Azure klassisk til **ikke længere**.

8.  Klik på **virtuelle maskiner** i Azure klassisk portalen, og vælg den virtuelle maskine, du vil kopiere.

9.  Klik på kommandolinjen skal **Hent**.

    ![Registrere virtuelt](./media/virtual-machines-windows-classic-capture-image/CaptureVM.png)

    Dialogboksen **registrere den virtuelle maskine** vises.

10. Skriv et navn til det nye billede i **Billede navn**.

11. Før du tilføjer et billede af Windows Server til dit sæt af brugerdefinerede billeder, skal den generalized ved at køre Sysprep, som beskrevet i de forrige trin. Klik på **jeg har kørt Sysprep på den virtuelle maskine** for at angive, at du har.

12. Klik på afkrydsningsfeltet for at tage et skærmbillede. Det nye billede er nu tilgængelig under **billeder**.

    ![Billede af capture vellykket](./media/virtual-machines-windows-classic-capture-image/VMCapturedImageAvailable.png)

##<a name="next-steps"></a>Næste trin

Billedet er klar til at bruges til at oprette virtuelle computere. For at gøre dette skal oprette du en virtuel maskine ved hjælp af menupunktet **Fra galleriet** og vælge det billede, du lige har oprettet. Flere oplysninger under [oprette en virtuel maskine fra et billede](virtual-machines-windows-classic-createportal.md).



[Hvordan du logger på en virtuel maskine, der kører Windows Server]: virtual-machines-windows-classic-connect-logon.md
[Sådan bruges Sysprep: en introduktion]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/virtual-machines-windows-classic-capture-image/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png
