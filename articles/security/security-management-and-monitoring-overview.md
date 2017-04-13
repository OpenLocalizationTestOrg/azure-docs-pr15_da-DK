<properties
   pageTitle="Azure sikkerhedsadministration og overvågning oversigt | Microsoft Azure"
   description=" Azure leverer sikkerhedsmekanismer hjælper for administration og overvågning af Azure skytjenester og virtuelle computere.  I denne artikel giver et overblik over disse grundlæggende funktioner for sikkerhed og -tjenester. "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="terrylan"/>

# <a name="azure-security-management-and-monitoring-overview"></a>Azure sikkerhedsadministration og overvågning oversigt

Azure leverer sikkerhedsmekanismer hjælper for administration og overvågning af Azure skytjenester og virtuelle computere. I denne artikel giver et overblik over disse grundlæggende funktioner for sikkerhed og -tjenester. Der findes links til artikler, der giver oplysninger om hver, så du kan få mere at vide.

Sikkerheden for Microsofts skytjenester er en partnerskab og fælles ansvar mellem dig og Microsoft. Fælles ansvar betyder, at Microsoft er ansvarlig for Microsoft Azure og fysisk sikkerhed på dataene centers (ved hjælp af sikkerhed beskyttelse som låst badge posten døre, hegn og beskytter). Desuden leverer Azure stærke sikkerhedsniveauer skyen på laget software, der opfylder kunderne krævende sikkerhed, beskyttelse af personlige oplysninger og overholdelse behov.

Du ejer dine data og identiteter, ansvaret for at beskytte dem, sikkerheden for dine lokale ressourcer og sikkerheden for skyen komponenter resultatsæt, som du har kontrol. Microsoft giver dig sikkerhedskontrol og egenskaber kan hjælpe dig med at beskytte dine data og programmer. Dit ansvar for sikkerheden er baseret på typen skybaseret tjeneste.

I følgende tabel opsummerer saldoen på ansvar over for både Microsoft og kunden.

![Fælles ansvar][1]

Du kan finde en dybere undersøgelse sikkerhedsadministration af, [Administration af sikkerhed i Azure](azure-security-management.md).

Her er de grundlæggende funktioner skal gennemgås i denne artikel:

- Rollebaseret adgangskontrol
- Antimalwareprogram
- Multi-Factor Authentication
- ExpressRoute
- Virtuelt netværk gateways
- Administration af privilegerede identitet
- Beskyttelse af din identitet
- Sikkerhedscenter

## <a name="role-based-access-control"></a>Rollebaseret adgangskontrol

Rollebaseret Access kontrolelement (RBAC) indeholder detaljerede access-administration for Azure ressourcer. Brug af RBAC, kan du tildele personer kun mængden af access, at de skal udføre deres arbejde.  RBAC kan også hjælpe dig med at sikre, at når personer forlader organisationen de miste adgangen til ressourcer i skyen.

Lær mere:

- [Active Directory-teamets blog på RBAC](http://i1.blogs.technet.com/b/ad/archive/2015/10/12/azure-rbac-is-ga.aspx)
- [Azure rollebaseret adgangskontrol](../active-directory/role-based-access-control-configure.md)

## <a name="antimalware"></a>Antimalwareprogram

Du kan bruge Antimalwareprogram software fra større sikkerhed leverandører som Microsoft, Symantec, Trend Micro, McAfee og Kaspersky til at beskytte din virtuelle maskiner mod skadelig filer, adware og andre trusler med Azure.

Microsoft Antimalwareprogram giver dig mulighed for at installere en Antimalwareprogram agent for både PaaS roller og virtuelle computere. Baseret på System Center Endpoint Protection, viser denne funktion dokumenterede lokalt sikkerhedsadministrator til skyen.

Vi tilbyder også tæt integration af Tendenss [Deep sikkerhed](http://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/)™ og [SecureCloud](http://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/)™ Azure-platformen-produkter. DeepSecurity er en antivirusprogram og SecureCloud er en løsning til kryptering. DeepSecurity installeres inde i FOS ved hjælp af en udvidelsesmodel. Ved hjælp af portalen Brugergrænsefladen og PowerShell, kan du vælge at bruge DeepSecurity inde i nye VM'er, der er der spundet op eller eksisterende VM'er, der allerede er implementeret.

Symantec slutpunkt beskyttelse (SEP) understøttes også på Azure. Kunder har mulighed for at angive, at de skal bruge SEP inden for en VM via portalen integration. SEP kan installeres på et helt nyt VM via Azure-portalen eller kan være installeret på en eksisterende VM ved hjælp af PowerShell.

Lær mere:

- [Implementering af Antimalwareprogram løsninger på Azure virtuelle maskiner](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
- [Microsoft Antimalwareprogram til Azure Cloud Services og virtuelle maskiner](../security/azure-security-antimalware.md)
- [Sådan installerer og konfigurerer Trend Micro Deep Security som en tjeneste på en Windows-VM](../virtual-machines/virtual-machines-windows-classic-install-trend.md)
- [Sådan installerer og konfigurerer Symantec Endpoint Protection på en Windows-VM](../virtual-machines/virtual-machines-windows-classic-install-symantec.md)
- [Nye Antimalwareprogram indstillinger for beskyttelse af Azure virtuelle maskiner – McAfee Endpoint Protection](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Azure Multi-Factor authentication (MFA) er en metode til godkendelse, der kræver brug af mere end én metode til bekræftelse og tilføjer en kritiske anden sikkerhedslag bruger logon og transaktioner. MFA hjælper med at beskytter adgang til data og programmer under overholdelse af brugernes behov for en enkelt logonprocessen. Det leverer stærke godkendelse via et udvalg af bekræftelse – telefonopkald, SMS-besked eller mobilapp besked eller bekræftelse kode og 3 part ed tokens.

Lær mere:

- [Multi-Factor authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
- [Hvad er Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)
- [Hvordan fungerer Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-how-it-works.md)

## <a name="expressroute"></a>ExpressRoute

Microsoft Azure ExpressRoute kan du udvide dit lokale netværk i Microsoft cloud over en dedikeret privat forbindelse via en udbyder af connectivity. Med ExpressRoute, kan du oprette forbindelse til Microsofts skytjenester, som Microsoft Azure, Office 365 og CRM Online. Forbindelse kan være fra en en-til-alle netværk (VPN IP), et punkt til punkt Ethernet-netværk eller en virtuel tværs forbindelse via en udbyder af connectivity på en samtidig placering facilitet. ExpressRoute forbindelser går ikke via offentlige internettet. Dette giver mulighed for ExpressRoute forbindelser til at tilbyde flere pålidelighed, hastigheden, nederste latenstider og højere sikkerhed end typisk forbindelser via internettet.

Lær mere:

- [ExpressRoute teknisk oversigt](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Virtuelt netværk gateways

VPN Gateways, også kaldet Azure virtuelle netværk Gateways, der bruges til at sende netværkstrafik mellem virtuelle netværk og lokale placeringer. De bruges også til at sende trafik mellem flere virtuelle netværk i Azure (VNet til VNet).  VPN gateways giver sikker tværs lokale forbindelsen mellem Azure og infrastrukturen.

Lær mere:

- [Om VPN gateways](../vpn-gateway/vpn-gateway-about-vpngateways.md)
- [Oversigt over Azure netværk sikkerhed](security-network-overview.md)

## <a name="privileged-identity-management"></a>Administration af privilegerede identitet

Nogle gange skal brugere gennemføre privilegerede transaktioner i Azure ressourcer eller andre SaaS programmer. Det betyder ofte organisationer har give dem permanent privilegerede adgang i Azure Active Directory (Azure AD). Dette er en voksende sikkerhedsrisikoen for ressourcer, der er hostet skyen, fordi organisationer ikke kan tilstrækkeligt overvåge hvad disse brugere laver med deres privilegerede adgang.
Desuden, hvis en brugerkonto med privilegerede access afsløret, påvirke den ét brud overordnede skyen sikkerhed. Azure AD rettigheder identitet Management hjælper med at løse denne risiko ved at sænke visning tidspunktet for rettigheder og øge indsigt i brugen.  

Privilegerede identitet Management introducerer begrebet en midlertidig administrator for en rolle eller "just in time" administratoradgang, som er en bruger, der skal udføre en aktiveringsprocessen for den pågældende tildelt rolle. Aktiveringsprocessen ændres tildelingen af brugeren til en rolle i Azure AD fra inaktiv til aktiv for en angivet tid periode som otte timer.

Lær mere:

- [Azure AD rettigheder identitet administration](../active-directory/active-directory-privileged-identity-management-configure.md)
- [Komme i gang med administration af Azure AD privilegerede identitet](../active-directory/active-directory-privileged-identity-management-getting-started.md)

## <a name="identity-protection"></a>Beskyttelse af din identitet

Azure Active Directory (AD) identitetsbeskyttelse giver en samlet visning af mistænkelige logon aktiviteter og potentielle svagheder til at beskytte din virksomhed. Beskyttelse af din identitet registrerer mistænkelige aktiviteter, som brugere og rettigheder (admin) identiteter, baseret på signaler som skadelige angreb lækket legitimationsoplysninger og logon fra fremmede steder og inficeret enheder.

Ved at angive beskeder og anbefalede afhjælpning, beskyttelse af din identitet, der hjælper med at reducere risici i realtid. Den beregner bruger risikoen alvor, og du kan konfigurere risikoen-baserede politikker for automatisk at få adgang til fra fremtidige trusler beskytter-program.

Lær mere:

- [Beskyttelse af Azure Active Directory din identitet](../active-directory/active-directory-identityprotection.md)
- [Kanal 9: Azure AD og identitet Vis: identitet beskyttelse Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Sikkerhedscenter

Azure Sikkerhedscenter kan du forhindre, at registrere og besvare trusler og giver du større indsigt i, og kontrol over, sikkerheden for dine Azure ressourcer. Det giver integreret sikkerhed overvågning og politik for administration på tværs af dine abonnementer på Azure, hjælper med at registrere trusler, der ellers går sig og fungerer med et omfattende netværk af sikkerhedsløsninger.

Sikkerhedscenter hjælper dig med at optimere og overvåge sikkerheden for dine Azure ressourcer ved at:

- Gør det muligt at definere politikker for ressourcerne Azure abonnement afhængigt af din virksomheds sikkerhedsbehov og typer programmer eller følsomheden for dataene i hvert abonnement.
- Overvågning tilstanden for din Azure virtuelle maskiner, netværk og programmer.
- Lave en liste over prioriterede sikkerhedsadvarsler, herunder beskeder fra integreret partnerløsninger, sammen med de oplysninger, du skal bruge for hurtigt at undersøge og anbefalinger om, hvordan du afhjælpning et angreb.

Lær mere:

- [Introduktion til Azure Security Center](../security-center/security-center-intro.md)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png
