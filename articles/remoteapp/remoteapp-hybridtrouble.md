
<properties
    pageTitle="Fejlfinding i forbindelse med oprettelse af RemoteApp hybrid samlinger | Microsoft Azure"
    description="Lær, hvordan du udfører fejlfinding af fejl under oprettelse RemoteApp hybrid af websteder"
    services="remoteapp"
    documentationCenter=""
    authors="vkbucha"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="troubleshoot-creating-azure-remoteapp-hybrid-collections"></a>Fejlfinding i forbindelse med oprettelse af websteder Azure RemoteApp-hybrid

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

En hybrid af websteder er hostet i og lagrer data i Azure skyen, men kan brugere access-data og ressourcer, der er gemt på dit lokale netværk. Brugere kan få adgang til apps ved at logge på med legitimationsoplysningerne virksomhedens synkroniseret eller knyttet til Azure Active Directory. Du kan installere en hybrid af websteder, der bruger et eksisterende virtuelle Azure-netværk, eller du kan oprette et nyt virtuelt netværk. Vi anbefaler, at du opretter eller bruger et virtuelt netværksundernet med et stort nok CIDR område til forventede fremtidig vækst til Azure RemoteApp.

Din samling endnu ikke har oprettet? Se [oprette en hybrid af websteder](remoteapp-create-hybrid-deployment.md) for at se trinnene.

Hvis du har problemer med at oprette af websteder, eller hvis samlingen ikke fungerer, som du synes, det skal, skal du se følgende oplysninger.

## <a name="your-image-is-invalid"></a>Billedet er ugyldige ##
Hvis du ser en meddelelse som "GoldImageInvalid", når du venter Azure til at klargøre din samling, betyder det, at dit billede af designskabelon ikke opfylder [defineret billede krav](remoteapp-imagereqs.md). Gå så læse disse [krav](remoteapp-imagereqs.md), løse dit billede og forsøger at oprette din samling igen.



## <a name="does-your-vnet-have-network-security-groups-defined"></a>Har din VNET netværk sikkerhedsgrupper defineret? ##
Hvis du har netværk sikkerhedsgrupper, der er defineret i det undernet, du bruger til af websteder, skal du kontrollere disse [URL-adresser og porte](remoteapp-ports.md) er tilgængelige fra dit undernet.

Du kan føje flere netværk sikkerhedsgrupper til FOS implementeret af dig i undernet for tættere kontrolelement.

## <a name="are-you-using-your-own-dns-servers-and-are-they-accessible-from-your-vnet-subnet"></a>Bruger du dine egne DNS-servere? Og de er tilgængelige fra dit undernet, VNET? ##
>[AZURE.NOTE] Du skal skal du kontrollere DNS-servere i din VNET er altid op og altid kunne løse de virtuelle maskiner på VNET som vært. Brug ikke Google DNS til dette.


Du kan bruge dine egne DNS-servere for hybride af websteder. Du angiver dem i dit netværk konfiguration skema eller via portalen management når du opretter dit virtuelle netværk. DNS-servere, der bruges i den rækkefølge, de er angivet i en failover måde (i modsætning til round robin).  
Se [Navneoversættelse for FOS og rolle forekomster](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) at sikre, at dine DNS-servere er konfigureret correcly.

Sørg for DNS-serverne for dine websteder er tilgængelige og fra VNET undernettet, du har angivet for denne samling.

Eksempel:

    <VirtualNetworkConfiguration>
    <Dns>
      <DnsServers>
        <DnsServer name="" IPAddress=""/>
      </DnsServers>
    </Dns>
    </VirtualNetworkConfiguration>

![Definere dine DNS](./media/remoteapp-hybridtrouble/dnsvpn.png)

## <a name="are-you-using-an-active-directory-domain-controller-in-your-collection"></a>Bruger du en Active Directory-domænecontrolleren i af websteder? ##
I øjeblikket kun én Active Directory-domæne kan knyttes til Azure RemoteApp. Samlingen hybrid understøtter kun Azure Active Directory-konti, der er blevet synkroniseret med DirSync værktøj fra en Windows Server Active Directory-installation nærmere betegnet kan enten synkroniseret med indstillingen synkronisering af adgangskoder eller synkroniseret med Active Directory Federation Services (AD FS) sammenslutning konfigureret. Du skal oprette et brugerdefineret domæne, der svarer til UPN-suffiks til domæne til dit lokale domæne og konfigurere katalogintegration.

Se [Konfiguration af Active Directory til Azure RemoteApp](remoteapp-ad.md) kan finde flere oplysninger.

Sørg for, at domænet oplysninger er gyldige og domænecontrolleren kan nås fra den VM, der er oprettet i det undernet, der bruges til Azure Remote-App. Sørg også for de angivne service kontolegitimationsoplysninger har tilladelse til at tilføje computere til det angivne domæne og, at det angivne AD navn kan løses fra DNS fremgår af VNET.

## <a name="what-domain-name-did-you-specify-when-you-created-your-collection"></a>Hvilke domænenavn har du angivet, da du oprettede din af websteder? ##

Det domænenavn, du har oprettet eller tilføjet skal være en intern domænenavn (ikke dit Azure AD-domænenavn) og skal være i løsning DNS-format (contoso.local). For eksempel, du har et internt Active Directory-navn (contoso.local) og en Active Directory-UPN (contoso.com) – du skal bruge det interne navn, når du opretter din samling.
