<properties
    pageTitle="Linux gæster på Azure stak | Microsoft Azure"
    description="Få mere at vide hvordan oprette Linux-baserede virtuelle maskiner på Azure stablen."
    services="azure-stack"
    documentationCenter=""
    authors="anjayajodha"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anajod"/>
    
# <a name="deploy-linux-virtual-machines-on-azure-stack"></a>Installere Linux virtuelle maskiner på Azure stak

Du kan installere Linux virtuelle maskiner på Azure stak Konceptet ved at tilføje et billede af Linux-baserede til Azure stak Marketplace. Flere Linux leverandører stiller billeder, der kan føjes til en Azure stak Konceptet, eller du kan oprette din egen.

## <a name="download-an-image"></a>Hente et billede

 1. Hent og udtrække et Azure stak-kompatible billede fra linkene nedenfor, eller forberede din egen:
  - [Bitnami](https://bitnami.com/azure-stack)
  - [CentOS](http://olstacks.cloudapp.net/latest/)
  - [CoreOS](https://stable.release.core-os.net/amd64-usr/current/coreos_production_azure_image.vhd.bz2)
  - [SuSE](https://download.suse.com/Download?buildid=VCFi7y7MsFQ~)
  - [Ubuntu 14.04 LTS](https://partner-images.canonical.com/azure/azure_stack/) / [Ubuntu 16.04 LTS](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)
  
 2. Udtrække billede Virtuelle, hvis det er nødvendigt og [tilføje billedet til Marketplace](azure-stack-add-vm-image.md). Sørg for, at den `OSType` parameter er angivet til `Linux`.
 
 3. Når du har tilføjet billedet til Marketplace, der oprettes et Marketplace element, og du kan installere en Linux virtuel maskine.
  
## <a name="prepare-your-own-image"></a>Forberede dig på dit eget billede

1. Forberede din egen Linux billede ved hjælp af en af følgende fremgangsmåder:
 - [CentOS-baserede Salgsdistributioner](../virtual-machines/virtual-machines-linux-create-upload-centos.md)
 - [Debian Linux](../virtual-machines/virtual-machines-linux-debian-create-upload-vhd.md)
 - [Oracle Linux](../virtual-machines/virtual-machines-linux-oracle-create-upload-vhd.md)
 - [Red Hat Enterprise Linux](../virtual-machines/virtual-machines-linux-redhat-create-upload-vhd.md)
 - [SLES & openSUSE](../virtual-machines/virtual-machines-linux-suse-create-upload-vhd.md)
 - [Ubuntu](../virtual-machines/virtual-machines-linux-create-upload-ubuntu.md)

2. Hent og Installer [Azure Linux Agent](https://github.com/Azure/WALinuxAgent/)

    Azure Linux Agent version 2.1.3 eller højere er påkrævet for at klargøre din Linux VM på Azure stablen. Mange af fordelingerne er anført ovenfor allerede indeholder denne version af agent eller højere som en pakke i deres typer lagre (normalt kaldes `WALinuxAgent` eller `walinuxagent`). Men hvis versionen af pakken Azure-agent er mindre end 2.1.3 (det vil sige 2.0.18 eller lavere), og derefter skal du installere agenten manuelt. Den installerede version kan bestemmes, enten fra pakkenavn eller ved at køre `/usr/sbin/waagent -version` på VM.

    Følg vejledningen nedenfor for at installere Azure Linux agent manuelt-

 - Først skal du hente den nyeste version Azure Linux agent fra [Github](https://github.com/Azure/WALinuxAgent/releases), eksempel:

            # wget https://github.com/Azure/WALinuxAgent/archive/v2.2.0.tar.gz

 - Pak Azure agent:

            # tar -vzxf v2.2.0.tar.gz

 - Installere python setuptools

        **Debian / Ubuntu**

            # sudo apt-get update
            # sudo apt-get install python-setuptools

        **Ubuntu 16.04+**

            # sudo apt-get install python3-setuptools

        **RHEL / CentOS / Oracle Linux**

            # sudo yum install python-setuptools

 - Installer Azure agent:

            # cd WALinuxAgent-2.2.0
            # sudo python setup.py install --register-service

    Systemer med Python 2.x og Python 3.x installeret side om side kan være nødvendigt at køre følgende kommando:

        # sudo python3 setup.py install --register-service

    Du kan finde yderligere oplysninger finder Azure Linux Agent [vigtigt](https://github.com/Azure/WALinuxAgent/blob/master/README.md).

3. [Føj billede til Marketplace](azure-stack-add-vm-image.md). Sørg for, at den `OSType` parameter er angivet til `Linux`.

4. Når du har tilføjet billedet til Marketplace, der oprettes et Marketplace element, og du kan installere en Linux virtuel maskine.

## <a name="next-steps"></a>Næste trin

[Ofte stillede spørgsmål om Azure stak](azure-stack-faq.md)