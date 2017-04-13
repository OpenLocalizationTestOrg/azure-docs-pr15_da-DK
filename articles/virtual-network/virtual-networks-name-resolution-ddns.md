<properties
   pageTitle="Ved hjælp af dynamiske DNS til at registrere værtsnavne"
   description="Denne side giver oplysninger om, hvordan du konfigurerer dynamiske DNS til at registrere værtsnavne i dine egne DNS-servere."
   services="dns"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="" />
<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="sewhee" />

# <a name="using-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Ved hjælp af dynamiske DNS til at registrere værtsnavne i dine egne DNS-server

[Azure leverer navneoversættelse](virtual-networks-name-resolution-for-vms-and-role-instances.md) for virtuelle maskiner (VM'er) og rolle forekomster. Når din navneoversættelse skal går ud over dem, der leveres af Azure, kan du angive dine egne DNS-servere. Dette giver dig mulighed for at tilpasse din DNS-løsning efter din egen specifikke behov. For eksempel du muligvis få adgang til lokale ressourcer via domænecontrolleren Active Directory.

Når dine brugerdefinerede DNS-servere er hostet som Azure FOS kan du videresende hostname forespørgsler til den samme vnet til Azure til at løse værtsnavne. Hvis du ikke vil bruge denne rute, kan du registrere din VM værtsnavne i din DNS-server ved hjælp af dynamiske DNS.  Azure har ikke mulighed for (fx legitimationsoplysninger) til at oprette poster i din DNS-servere direkte, så alternative arrangementer er ofte det er nødvendigt. Her er nogle almindelige scenarier med alternativer.

## <a name="windows-clients"></a>Windows-klienter

Ikke-medlem af et domæne Windows-klienter forsøge usikker dynamisk DNS (DDNS)-opdateringer, når de starter, eller når deres IP-adresser ændres. DNS-navnet er værtsnavnet plus det primære DNS-suffiks. Azure forlader det primære DNS suffiks tom, men du kan angive dette i VM via [Brugergrænsefladen](https://technet.microsoft.com/library/cc794784.aspx) eller [ved hjælp af automatisering](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix).

Medlem af et domæne Windows-klienter registrere deres IP-adresser med domænecontrolleren ved hjælp af sikker dynamisk DNS. Processen domæne joinforbindelse angiver det primære DNS-suffiks på klienten og opretter og vedligeholder tillidsforholdet.

## <a name="linux-clients"></a>Linux-klienter

Linux klienter generelt registrere ikke sig selv til Start DNS-server, skal de forudsætter DHCP-serveren betyder det. Azures DHCP-servere har ikke mulighed for eller legitimationsoplysninger for at registrere poster i din DNS-server.  Du kan bruge et værktøj, der hedder *nsupdate*, som er inkluderet i pakken Bind, sende dynamiske DNS-opdateringer. Da dynamisk DNS-protokollen er standardiserede, kan du bruge *nsupdate* , selvom du ikke bruger Bind på DNS-serveren.

Du kan bruge de kroge, der leveres af DHCP-klienten til at oprette og vedligeholde posten hostname i DNS-serveren. Under cyklus DHCP afvikler klienten scriptene i */etc/dhcp/dhclient-exit-hooks.d/*. Dette kan bruges til at registrere den nye IP-adresse ved hjælp af *nsupdate*. Eksempel:

        #!/bin/sh
        requireddomain=mydomain.local

        # only execute on the primary nic
        if [ "$interface" != "eth0" ]
        then
            return
        fi

        # when we have a new IP, perform nsupdate
        if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
           [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
        then
            host=`hostname`
            nsupdatecmds=/var/tmp/nsupdatecmds
              echo "update delete $host.$requireddomain a" > $nsupdatecmds
              echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
              echo "send" >> $nsupdatecmds

              nsupdate $nsupdatecmds
        fi

        #done
        exit 0;

Du kan også bruge kommandoen *nsupdate* til at udføre sikre dynamisk DNS-opdateringer. Når du bruger en binde DNS-server, er et offentlige og private nøgler par for eksempel [oprettet](http://linux.yyz.us/nsupdate/).  DNS-serveren er [konfigureret](http://linux.yyz.us/dns/ddns-server.html) med den offentlige del af tasten, så kan bekræfte signaturen på anmodningen. Du skal bruge indstillingen *-k* til at levere nøgle-par til *nsupdate* i rækkefølge for dynamisk DNS opdatere anmodningen skal underskrives.

Når du bruger en Windows DNS-server, kan du bruge Kerberos-godkendelse med parameteren *-g* i *nsupdate* (ikke tilgængelig i Windows-versionen af *nsupdate*). Brug *kinit* for at indlæse legitimationsoplysningerne (fx fra en [keytab fil](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)) til at udføre dette. *Nsupdate g –* Vælg derefter op legitimationsoplysninger fra cachen.

Hvis det er nødvendigt, kan du føje et DNS-søgning suffiks til din FOS. DNS-suffikset er angivet i filen */etc/resolv.conf* . De fleste Linux distros automatisk at styre indholdet af denne fil, så normalt du kan ikke redigere den. Du kan dog tilsidesætte suffikset ved hjælp af DHCP-klienten *, erstatter* kommandoen. Hvis du vil gøre dette, i */etc/dhcp/dhclient.conf*skal du tilføje:

        supersede domain-name <required-dns-suffix>;

