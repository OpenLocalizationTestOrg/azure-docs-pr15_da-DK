<properties
   pageTitle="Få adgang til VM-ID"
   description="I denne artikel beskrives, åbner og bruger Azure VM Entydigt ID"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="kmouss"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/08/2016"
   ms.author="kmouss"/>
   
# <a name="accessing-and-using-azure-vm-unique-id"></a>Åbne og bruge Azure VM Entydigt ID

Entydigt ID for Azure VM er et 128 bit id kodes og gemt i alle Azure IaaS VM'S SMBIOS og kan aktuelt læses ved hjælp af platformen BIOS kommandoer.

Entydigt ID for Azure VM er en skrivebeskyttet egenskab. Azure Entydigt ID for VM ikke ændre ved genstart lukning (enten er planlagt for ikke-planlagt), Start/Stop deaktivere allokere, service reparation eller gendanne på plads. Men hvis VM er et øjebliksbillede og kopieret for at oprette en ny forekomst, konfigureres nyt Azure VM ID.

> [AZURE.NOTE] Hvis du har ældre FOS oprettet og kører, da denne nye funktion har gennemført (18 September 2014), skal du Genstart din VM til automatisk tilsendt en Azure entydigt id


For at åbne Azure entydige VM-ID'ET fra inden for VM:


## <a name="create-a-vm"></a>Oprette en VM
 

Få mere at vide under [oprette en virtuel maskine](virtual-machines-linux-creation-choices.md)


## <a name="connect-to-the-vm"></a>Oprette forbindelse til VM
 

Du kan finde flere oplysninger, se [SSH fra Linux](virtual-machines-linux-mac-create-ssh-keys.md)


## <a name="query-vm-unique-id"></a>Entydigt ID for forespørgsel VM

Kommandoen (eksempler på Brug **Ubuntu**):

    sudo dmidecode | grep UUID
    
Eksempel forventede resultater:

    UUID: 090556DA-D4FA-764F-A9F1-63614EDA019A
    
På grund af stor Endian bit rækkefølge, kan de faktiske Entydigt ID for VM i dette tilfælde:

    DA 56 05 09 – FA D4 – 4f 76 - A9F1-63614EDA019A
    
    
Entydigt ID for Azure VM kan bruges i forskellige scenarier, om VM kører på Azure eller lokalt og kan hjælpe dine licenser, rapportering eller generelle sporing krav du muligvis har på din Azure IaaS installationer. Mange uafhængige softwareleverandører opbygning af programmer og for at godkende dem på Azure kan kræve for at identificere en Azure VM i hele dets livscyklus og til at fortælle om VM kører på Azure, lokalt eller på andre skyen udbydere. Dette platform id kan for eksempel hjælpe registrerer Hvis softwaren er korrekt licenseret eller hjælp til at koordinere VM data til kilden som til at hjælpe om konfiguration af højre omfanget af den rette platform og til at registrere og koordinere disse målepunkter blandt andre anvendelsesområder.
