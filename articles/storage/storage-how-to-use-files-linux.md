<properties
    pageTitle="Sådan bruges Azure filer med Linux | Microsoft Azure"
        description="Oprette en Azure filshare i skyen med denne trinvist selvstudium. Administrere din fil del indhold, og oprette forbindelse til et filshare fra en Azure virtuel maskine (VM) bruger Linux eller et lokalt program, der understøtter små og mellemstore virksomheder 3.0."
        services="storage"
        documentationCenter="na"
        authors="mine-msft"
        manager="aungoo"
        editor="tysonn" />

<tags ms.service="storage"
      ms.workload="storage"
      ms.tgt_pltfrm="na"
      ms.devlang="na"
      ms.topic="article"
      ms.date="10/18/2016"
      ms.author="minet" />


# <a name="how-to-use-azure-file-storage-with-linux"></a>Sådan bruges Azure fillagring med Linux

## <a name="overview"></a>Oversigt

Azure fillagring indeholder filshares i skyen ved hjælp af standard små og mellemstore virksomheder-protokollen. Du kan overføre enterprise-programmer, der er afhænger af filservere at Azure med Azure filer. Programmer, der kører i Azure kan nemt tilslutte filshares fra Azure virtuelle maskiner, der kører Linux. Og med den nyeste version af fillagring, du kan også oprette forbindelse til et filshare fra et lokalt program, der understøtter små og mellemstore virksomheder 3.0.

Du kan oprette Azure filshares bruger [Azure-portalen](https://portal.azure.com), Azure lagerplads PowerShell-cmdletter, Azure-lager klientbiblioteker eller Azure lagerplads REST-API. Desuden, fordi filshares er små og mellemstore virksomheder aktier, du kan få adgang til dem via standard filsystemet API'er.

Lagring af filer er oprettet på den samme teknologi som Blob tabel og kø lagerplads, så fillagring tilbyder tilgængelighed, holdbarhed, skalerbarhed og geografisk-redundans, der er indbygget i Azure lagerplads platform. Flere oplysninger om fil lagerplads ydeevne mål og begrænsninger for, i [Azure lagerplads skalerbarhed og ydeevne destinationer](storage-scalability-targets.md).

Lagring af filer er nu alment tilgængelig og understøtter både små og mellemstore virksomheder 2.1 og små og mellemstore virksomheder 3.0. Du kan finde yderligere oplysninger om fillager [fil service REST-API](https://msdn.microsoft.com/library/azure/dn167006.aspx).

>[AZURE.NOTE] Linux SMB klienten understøtter ikke kryptering, endnu, så tilslutte et filshare fra Linux stadig kræver, at klienten er i samme Azure område som det pågældende filshare. Understøttelse af kryptering for Linux er dog roadmap med Linux udviklere ansvarlig for små og mellemstore virksomheder funktionalitet. Distribuerede Linux-versioner, der understøtter kryptering i fremtiden vil kunne tilslutte en Azure filshare hvor som helst samt.

## <a name="video-using-azure-file-storage-with-linux"></a>Video: Brug af Azure fillagring med Linux

Her er en video, der viser, hvordan du opretter og bruger Azure filshares på Linux.

> [AZURE.VIDEO azure-file-storage-with-linux]

## <a name="choose-a-linux-distribution-to-use"></a>Vælg en Linux fordeling bruge ##

Når du opretter en Linux virtuel maskine i Azure, kan du angive et billede af Linux som understøtter små og mellemstore virksomheder 2.1 eller højere fra billedgalleriet Azure. Nedenfor finder du en liste over anbefalede Linux billeder:

- Ubuntu Server 14.04 +
- RHEL 7 +
- CentOS 7 +
- Debian 8
- openSUSE 13,2 +
- SUSE Linux Enterprise Server 12
- SUSE Linux Enterprise Server 12 (Premium billede)

## <a name="mount-the-file-share"></a>Oprette forbindelse til det pågældende filshare ##

For at tilslutte det pågældende filshare fra en virtuel maskine kører Linux, skal du muligvis installere en små og mellemstore virksomheder/CIFS-klient, hvis du bruger fordelingen ikke har en indbygget klient. Dette er kommandoen fra Ubuntu for at installere en valg cifs-af websted:

    sudo apt-get install cifs-utils

Derefter skal vil du foretage en tilslutning Peg (mkdir mymountpoint), og derefter udstede kommandoen Indlæs, der ligner dette:

     sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename ./mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777

Du kan også tilføje indstillinger i din /etc/fstab til at tilslutte dele.

Bemærk, at 0777 her repræsenterer en directory/fil tilladelse kode, der giver udførelse af/læse-og skriveadgang tilladelser for alle brugere. Du kan erstatte den med andre fil tilladelse kode, følge Linux fil tilladelse dokument.

For at holde et filshare, der er tilsluttet efter genstart, kan du også føje en indstilling som under i din /etc/fstab:

    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777

Eksempelvis hvis du har oprettet en Azure VM ved hjælp af Linux billede Ubuntu Server 15.04 (som er tilgængelige fra billedgalleriet Azure), du kan oprette forbindelse til filen som nedenfor:

    azureuser@azureconubuntu:~$ sudo apt-get install cifs-utils
    azureuser@azureconubuntu:~$ sudo mkdir /mnt/mountpoint
    azureuser@azureconubuntu:~$ sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    azureuser@azureconubuntu:~$ df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

Hvis du bruger CentOS 7.1, kan du tilslutte filen som nedenfor:

    [azureuser@AzureconCent ~]$ sudo yum install samba-client samba-common cifs-utils
    [azureuser@AzureconCent ~]$ sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    [azureuser@AzureconCent ~]$ df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

Hvis du bruger Åbn SUSE 13,2, kan du tilslutte filen som nedenfor:

    azureuser@AzureconSuse:~> sudo zypper install samba*  
    azureuser@AzureconSuse:~> sudo mkdir /mnt/mountpoint
    azureuser@AzureconSuse:~> sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    azureuser@AzureconSuse:~> df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

## <a name="manage-the-file-share"></a>Administrere det pågældende filshare ##

[Azure-portalen](https://portal.azure.com) indeholder en brugergrænseflade til administration af Azure fillagring. Du kan udføre følgende handlinger fra webbrowseren:

- Uploade og downloade filer til og fra din filshare.
- Overvåge den faktiske brugen af hver filshare.
- Justere kvoten for del størrelse.
- Kopiér den `net use` kommandoen til at bruge til at tilslutte din filshare fra en Windows-klient.

Du kan også bruge Azure på tværs af platforme kommandolinjen (Azure CLI) fra Linux til at administrere det pågældende filshare. Azure CLI indeholder et sæt af Åbn kilde, på tværs af platforme kommandoer for dig at arbejde med Azure-lager, herunder fillagring. Den indeholder meget af de samme funktioner, der findes i Azure Portal samt omfattende data funktionaliteten i access. Eksempler, under [Brug af Azure CLI med Azure-lager](storage-azure-cli.md).

## <a name="develop-with-file-storage"></a>Udvikle med fillagring ##

Som en udvikler, kan du oprette et program med fillagring ved hjælp af [Azure lagerplads klientbibliotek til Java](https://github.com/azure/azure-storage-java). Du kan finde kodeeksempler, [hvordan du bruger fillagring fra Java](storage-java-how-to-use-file-storage.md).

Du kan også bruge [Azure lagerplads klientbibliotek til Node.js](https://github.com/Azure/azure-storage-node) for at udvikle mod fillagring.

## <a name="feedback-and-more-information"></a>Feedback og få mere at vide ##

Linux brugere, vi vil gerne høre fra dig!

Azure fillagring for Linux brugernes gruppe indeholder et forum, hvor du kan dele feedback, mens du evaluerer og indføre fillagring på Linux. Mail [Azure fil lagerplads Linux brugere](mailto:azurefileslinuxusers@microsoft.com) at deltage i brugernes gruppe.

## <a name="next-steps"></a>Næste trin

Se disse links for at finde flere oplysninger om Azure fillagring.

### <a name="conceptual-articles-and-videos"></a>Grundlæggende artikler og videoer

- [Azure filer opbevaring: en frictionless sky små og mellemstore virksomheder filsystem til Windows og Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
- [Introduktion til Azure fillagring i Windows](storage-dotnet-how-to-use-files.md)

### <a name="tooling-support-for-file-storage"></a>Værktøjer understøttelse af fillagring

- [Overføre data med AzCopy kommandolinjeparametre Utility](storage-use-azcopy.md)
- [Oprette og administrere filshares](storage-azure-cli.md#create-and-manage-file-shares) ved hjælp af Azure CLI

### <a name="reference"></a>Reference

- [Fil-tjenesten REST-API reference](http://msdn.microsoft.com/library/azure/dn167006.aspx)
- [Azure filer artiklen om fejlfinding](storage-troubleshoot-file-connection-problems.md)

### <a name="blog-posts"></a>Blogindlæg

- [Azure fillagring er nu alment tilgængelig](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
- [Indvendig Azure fillagring](https://azure.microsoft.com/blog/inside-azure-file-storage/)
- [Introduktion til Microsoft Azure fil Service](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [Faste forbindelser til Microsoft Azure-filer](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
