<properties
    pageTitle="Detaljeret SSH fejlfinding for en Azure VM | Microsoft Azure"
    description="Mere detaljerede oplysninger om fejlfinding på problemer med at oprette forbindelse til en Azure virtuelt SSH"
    keywords="SSH forbindelsen afvist, ssh fejl, azure ssh, SSH forbindelsen mislykkedes"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="09/01/2016"
    ms.author="iainfou"/>

# <a name="detailed-ssh-troubleshooting-steps"></a>Detaljeret SSH fejlfindingstrin

Der findes mange mulige årsager til, SSH klienten ikke muligvis kunne oprette forbindelse til SSH service på VM. Hvis du har fulgt gennem flere [generelle SSH fejlfindingstrin](virtual-machines-linux-troubleshoot-ssh-connection.md), skal du foretage yderligere fejlfinding forbindelsesproblemet. I denne artikel hjælper dig med detaljerede fejlfindingstrin til at afgøre, hvor SSH forbindelsen går ned, og hvordan du kan løse problemet.

## <a name="take-preliminary-steps"></a>Tage foreløbige trin

I det følgende diagram viser de komponenter, der er forbundet.

![Diagram, der viser komponenter SSH-tjenesten](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

Følgende trin hjælpe dig med at isolere kilden til fejlen og finde ud af løsninger eller løsninger.

Først skal du kontrollere status for VM på portalen.

[Azure-portalen](https://portal.azure.com):

1. Vælg **Gennemse**for FOS oprettet ved hjælp af den klassiske implementeringsmodel, > **virtuelle maskiner (klassisk)** > *VM navn*.

    -ELLER-

    Vælg **Gennemse**for FOS oprettet ved hjælp af Ressourcestyring modellen, > **virtuelle maskiner** > *VM navn*.

    Statusruden for VM skal vise **kører**. Rul ned til Vis seneste aktivitet for Beregn, lager og netværksressourcer.

2. Vælg **Indstillinger for** at undersøge slutpunkter, IP-adresser og andre indstillinger.

    For at identificere slutpunkter i VM'er, der blev oprettet ved hjælp af Ressourcestyring skal bekræfte, at der er defineret en [sikkerhedsgruppe netværk](../virtual-network/virtual-networks-nsg.md) . Også kontrollere, at reglerne, der er anvendt på sikkerhedsgruppen netværk og, at de er refereres til i undernettet.

I [Azure klassisk portal](https://manage.windowsazure.com), til VM'er, der blev oprettet ved hjælp af den klassiske implementeringsmodel:

1. Vælg **virtuelle maskiner** > *VM navn*.
2. Vælg den VM **Dashboard** til at kontrollere dens status.
3. Vælg **skærmen** til at se seneste aktivitet for Beregn, lager og netværksressourcer.
4. Vælg **slutpunkter for** at sikre, at der er et slutpunkt for SSH trafik.

Kontroller konfigurerede slutpunkterne for at kontrollere netværksforbindelsen, og se, hvis du kan få fat VM gennem en anden protokol, som HTTP eller en anden tjeneste.

Efter disse trin, kan du prøve at SSH oprette forbindelse igen.


## <a name="find-the-source-of-the-issue"></a>Finde kilden til problemet

SSH klienten på computeren kan mislykkes, når tjenesten SSH på Azure VM på grund af problemer eller følgende konfigurationer:

- [SSH klientcomputeren](#source-1-ssh-client-computer)
- [Organisation edge-enhed](#source-2-organization-edge-device)
- [Tjenesteslutpunkt i skyen og få adgang til listen over objekter (ACL)](#source-3-cloud-service-endpoint-and-acl)
- [Netværk sikkerhedsgrupper](#source-4-network-security-groups)
- [Linux-baserede Azure VM](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Kilde 1: SSH klientcomputeren

Kontrollér, at det kan gøre SSH forbindelser til en anden lokalt, Linux-baseret computer undgås computeren som kilde for fejlen.

![Diagram, der fremhæver SSH klient computerens komponenter](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Hvis forbindelsen mislykkes, skal du kontrollere, om følgende på din computer:

- En lokal firewall-indstilling, der blokerer for indgående eller udgående SSH trafik (TCP-22)
- Lokalt installeret proxy klientsoftware, der forhindrer SSH forbindelser
- Lokalt installeret netværk overvågning software, der forhindrer SSH forbindelser
- Andre typer af sikkerhedssoftware, enten overvåge trafik eller bestemte typer af trafik for Tillad/ikke Tillad

Hvis en af disse betingelser gælder, midlertidigt deaktivere softwaren og prøve en SSH forbindelse til en lokal computer for at finde ud af årsagen til forbindelsen er blokeret på din computer. Derefter arbejde med din netværksadministrator til at rette programindstillinger for at tillade SSH forbindelser.

Hvis du bruger certifikatgodkendelse, skal du kontrollere, at du har disse tilladelser til mappen .ssh i dit eget bibliotek:

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/\*.pub
- Chmod 600 ~/.ssh/id_rsa (eller andre filer, der har dine private nøgler, der er gemt i dem)
- Chmod 644 ~/.ssh/known_hosts (indeholder hosts, du har knyttet til via SSH)

## <a name="source-2-organization-edge-device"></a>Kilde 2: Organisation edge-enhed

Kontrollér, at en computer, der er direkte forbundet til internettet kan gøre SSH forbindelser til din Azure VM undgås enheden organisation kant som kilde for fejlen. Hvis du får adgang til VM over en VPN til-websted eller en Azure ExpressRoute forbindelse, gå til [kilde 4: netværk sikkerhedsgrupper](#nsg).

![Diagram, der fremhæver organisation edge-enhed](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Hvis du ikke har en computer, der er direkte forbindelse til internettet, skal du oprette en ny Azure VM i sin egen ressourcegruppe eller tjeneste i skyen og bruge den. Se [oprette en virtuel maskine kører Linux i Azure](virtual-machines-linux-quick-create-cli.md)kan finde flere oplysninger. Slet den ressourcegruppe eller VM og cloud-tjenesten, når du er færdig med testen.

Hvis du kan oprette forbindelse til SSH med en computer, der er direkte forbundet til internettet, kan du kontrollere din organisation edge-enhed til:

- En intern firewall, der blokerer SSH trafik med internettet
- En proxy-server, der forhindrer SSH forbindelser
- Registrering af uautoriseret adgang eller netværk overvågning software, der kører på enheder i netværket kant, der forhindrer SSH forbindelser

Arbejde med din netværksadministrator til at rette indstillingerne for din organisation kant enheder for at tillade SSH trafik med internettet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Kilde 3: Skyen tjenesteslutpunkt og ACL

> [AZURE.NOTE] Denne kilde gælder kun for VM'er, der blev oprettet ved hjælp af den klassiske implementeringsmodel. VM'er, der blev oprettet ved hjælp af Ressourcestyring, gå videre til [datakilde 4: netværk sikkerhedsgrupper](#nsg).

Hvis du vil udelukke skyen tjenesteslutpunkt og ACL som kilde for fejlen, Kontrollér, at en anden Azure VM i det samme virtuelle netværk kan gøre SSH forbindelser til din VM.

![Diagram, der fremhæver skyen tjenesteslutpunkt og ACL](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Hvis du ikke har en anden VM i det samme virtuelle netværk, kan du nemt oprette en ny. Se [oprette en Linux VM på Azure ved hjælp af CLI](virtual-machines-linux-quick-create-cli.md)kan finde flere oplysninger. Slet den ekstra VM, når du er færdig med testen.

Hvis du kan oprette forbindelse til SSH med en VM i det samme virtuelle netværk, kan du kontrollere følgende:

- **Konfigurationen af slutpunktet til SSH trafik på destinationen VM.** Den private TCP-port på slutpunktet skal svare til den TCP-port, hvor tjenesten SSH på VM lytter. (Standardporten er 22). Bekræfte SSH TCP-portnummeret i portalen Azure for FOS oprettet ved hjælp af Ressourcestyring implementeringsmodel, ved at vælge **Gennemse** > **virtuelle maskiner (v2)** > *VM navn* > **Indstillinger** > **slutpunkter**.

- **ACL for SSH trafik slutpunktet på en målliste virtuel maskine.** En ACL kan du angive tillades eller afvises indgående trafik fra internettet, baseret på dens kildens IP-adresse. Konfigureret forkert ACLs kan forhindre indgående SSH trafik til slutpunktet. Markér din ACLs for at sikre den indgående trafik fra din proxy offentlige IP-adresser eller andre edge server er tilladt. Du kan finde flere oplysninger, kan du se [om netværksadgang kontrolelement lister (ACLs)](../virtual-network/virtual-networks-acl.md).

Fjerne det aktuelle slutpunkt undgås slutpunktet som en kilde til problemet, oprette et nyt slutpunkt, og Angiv SSH navn (TCP-port 22 for det offentlige og private portnummer). Se [konfigurere slutpunkterne på en virtuel maskine i Azure](virtual-machines-windows-classic-setup-endpoints.md)kan finde flere oplysninger.

<a id="nsg"></a>
## <a name="source-4-network-security-groups"></a>Kilde 4: Netværk sikkerhedsgrupper

Netværk sikkerhedsgrupper gør det muligt at få mere detaljerede kontrol med tilladte indgående og udgående trafik. Du kan oprette regler, der strækker sig over undernet og tjenester i en Azure virtuelt netværk i skyen. Kontrollér dit netværk sikkerhed gruppe regler for at sikre, at SSH trafik til og fra internettet er tilladt.
Se [om netværk sikkerhedsgrupper](../virtual-network/virtual-networks-nsg.md)for at få mere at vide.

## <a name="source-5-linux-based-azure-virtual-machine"></a>Kilde 5: Linux-baseret Azure virtuel maskine

Sidste kilden til mulige problemer er Azure virtuelt sig selv.

![Diagram, der fremhæver Linux-baserede Azure virtuelt](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Hvis du allerede har gjort det, skal du følge vejledningen i [at nulstille en adgangskode eller SSH for Linux-baserede virtuelle maskiner](virtual-machines-linux-classic-reset-access.md).

Prøv at oprette forbindelse fra din computer igen. Hvis det ikke lykkes er følgende nogle af de mulige problemer:

- Tjenesten SSH kører ikke på en målliste virtuel maskine.
- Tjenesten SSH lytter ikke på TCP-port 22. Installere en telnet-klient på din lokale computer for at teste, og Kør "telnet *cloudServiceName*. cloudapp.net 22". Dette angiver, hvis den virtuelle maskine tillader indgående og udgående kommunikation til SSH slutpunkt.
- Lokale firewallen på en målliste virtuel maskine har regler, der forhindrer indgående eller udgående SSH trafik.
- Registrering af uautoriseret adgang eller netværk overvågning software, der kører på en Azure virtuel maskine forhindrer SSH forbindelser.


## <a name="additional-resources"></a>Yderligere ressourcer
Du kan finde flere oplysninger om fejlfinding af adgang til programmer, se [fejlfinding af adgang til et program, der kører på en Azure virtuel maskine](virtual-machines-linux-troubleshoot-app-connection.md)