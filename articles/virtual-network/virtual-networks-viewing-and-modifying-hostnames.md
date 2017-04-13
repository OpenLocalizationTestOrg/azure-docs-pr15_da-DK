<properties 
   pageTitle="Få vist og ændre værtsnavne | Microsoft Azure"
   description="Hvordan du kan få vist og ændre værtsnavne til Azure virtuelle maskiner, web og arbejder roller for navneoversættelse"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="viewing-and-modifying-hostnames"></a>Få vist og ændre værtsnavne

Hvis du vil give din rolle forekomster skal refereres til ved værtsnavn, skal du angive værdien for værtsnavnet i tjenesten konfigurationsfil for hver rolle. Du kan gøre det ved at tilføje den ønskede værtsnavn attributten **vmName** for elementet **rolle** . Værdi for attributten **vmName** bruges som udgangspunkt for værtsnavnet for hver rolle forekomst. Eksempelvis hvis **vmName** er *webrole* , og der er tre forekomster af rollen, bliver host navnene på forekomsterne *webrole0*, *webrole1*og *webrole2*. Du behøver ikke at angive et værtsnavn til virtuelle maskiner i konfigurationsfil, fordi værtsnavnet for en virtuel maskine er udfyldt baseret på navnet på den virtuelle computer. Du kan finde flere oplysninger om at konfigurere en Microsoft Azure-tjeneste, se [Azure Service konfiguration skema (.cscfg filer)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>Få vist værtsnavne

Du kan få vist host navnene på virtuelle maskiner og rolle forekomster i en skybaseret tjeneste ved hjælp af værktøjerne nedenfor.

### <a name="azure-portal"></a>Azure-portalen

Du kan bruge [Azure portal](http://portal.azure.com) til at få vist navnene host for virtuelle maskiner bladet oversigt for en virtuel maskine. Husk på, at bladet viser en værdi for **navn** og **Host Name**. Selvom det er først på samme måde, vil ændre værtsnavnet ikke ændre navnet på den virtuelle maskine eller rolle forekomst.

Rolle forekomster kan også ses i portalen Azure, men når du viser forekomster i en skybaseret tjeneste, værtsnavnet vises ikke. Du får vist et navn for hver forekomst, men med navnet repræsenterer ikke værtsnavnet.

### <a name="service-configuration-file"></a>Tjenesten konfigurationsfil

Du kan hente konfigurationsfil tjeneste for en udløst tjeneste fra bladet **Konfigurer** til tjenesten i portalen Azure. Derefter kan du se for attributten **vmName** for elementet **rollenavn** at se værtsnavnet. Husk på, at dette værtsnavn bruges som udgangspunkt til værtsnavnet for hver rolle forekomst. Eksempelvis hvis **vmName** er *webrole* , og der er tre forekomster af rollen, bliver host navnene på forekomsterne *webrole0*, *webrole1*og *webrole2*.

### <a name="remote-desktop"></a>Fjernskrivebord

Når du aktiverer Fjernskrivebord (Windows), Windows PowerShell remoting (Windows) eller SSH (Linux og Windows) forbindelser til din virtuelle maskiner eller rolle forekomster, kan du se værtsnavn fra en aktiv Fjernskrivebord forbindelse på forskellige måder:

- Skriv hostname kommandoprompt eller SSH terminal.

- Skrive ipconfig/alle ved kommandoprompten (kun Windows).

- Få vist navnet på computer i systemets indstillinger (kun Windows).

### <a name="azure-service-management-rest-api"></a>Azure Service Management REST-API

Fra en RESTEN klient, skal du følge disse instruktioner:

1. Sørg for, at du har et klientcertifikat til at oprette forbindelse til Azure-portalen. For at få et klientcertifikat skal du følge trinnene præsenteres på [Sådan: Hent og importere publiceringsindstillinger og abonnementsoplysninger](https://msdn.microsoft.com/library/dn385850.aspx). 

1. Angive en sidehoved-post med navnet x-ms-version med en værdi på 2013-11-01.

1. Sende en anmodning i følgende format: https://management.core.windows.net/\<subscrition-id\>/services/hostedservices/\<navnet tjenesten\>?embed-detaljer = true

1. Se efter elementet **HostName** for hvert **RoleInstance** element.

>[AZURE.WARNING] Du kan også få vist det interne domænesuffiks for din skybaseret tjeneste fra RESTEN opkald svaret ved at markere elementet **InternalDnsSuffix** eller ved at køre ipconfig/alt fra en kommandoprompt i en session med Fjernskrivebord (Windows) eller ved at køre kat /etc/resolv.conf fra en SSH terminal (Linux).

## <a name="modifying-a-hostname"></a>Ændre et værtsnavn

Du kan ændre værtsnavnet for en hvilken som helst virtuelt eller rolle forekomst, ved at uploade en ændret service konfigurationsfil eller ved at omdøbe computeren fra en session med Fjernskrivebord.

## <a name="next-steps"></a>Næste trin

[Navneoversættelse (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Azure Service konfiguration skema (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Azure virtuelt netværk konfiguration skema](http://go.microsoft.com/fwlink/?LinkId=248093)

[Angiv DNS-indstillinger for brug af netværk konfigurationsfiler](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)
