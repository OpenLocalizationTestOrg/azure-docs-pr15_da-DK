<properties
   pageTitle="Retningslinjer for installation af Windows Server Active Directory på Azure virtuelle maskiner | Microsoft Azure"
   description="Hvis du ved, hvordan du kan installere AD-domænetjenester og AD Federation Services lokalt, kan du lære, hvordan de fungerer på Azure virtuelle computere."
   services="active-directory"
   documentationCenter=""
   authors="femila"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/27/2016"
   ms.author="femila"/>

# <a name="guidelines-for-deploying-windows-server-active-directory-on-azure-virtual-machines"></a>Retningslinjer for installation af Windows Server Active Directory på Azure virtuelle maskiner

Denne artikel forklares de vigtige forskelle mellem implementering af Windows Server Active Directory Domain Services (AD DS) og Active Directory Federation Services (AD FS) lokale kontra implementerer dem på virtuelle Microsoft Azure-computere.

## <a name="scope-and-audience"></a>Omfang og målgruppe

I artiklen henvender sig til dem, der allerede oplevede med installation af lokale Active Directory. Det dækker forskellene mellem installation af Active Directory på Microsoft Azure virtuelle maskiner/Azure virtuelle netværk og traditionelt lokalt Active Directory-installationer. Azure virtuelle maskiner og Azure virtuelle netværk er en del af en infrastruktur som-en-tjenesten (IaaS) giver organisationer til at udnytte dataressourcer i skyen.

Se [Installationsvejledningen til AD DS](https://technet.microsoft.com/library/cc753963) eller [planlægge din AD FS-](https://technet.microsoft.com/library/dn151324.aspx) efter behov for dem, der ikke er fortrolig med AD installation.

I denne artikel antager, at læseren er fortrolig med følgende begreber:

- Windows Server AD DS installation og administration
- Installation og konfiguration af DNS for at understøtte en Windows Server AD DS infrastruktur
- Windows Server AD FS installation og administration
- Implementering, konfiguration og administration af afhængige part programmer (websteder og web services), kan bruge Windows Server AD FS tokens
- Generelle virtuelt begreber, som Sådan konfigureres en virtuel maskine, virtuelle disk og virtuelle netværk

I denne artikel beskrives kravene til et hybridinstallation scenarie i hvilke Windows Server AD DS eller AD FS er delvist udløst lokalt og delvist installeres på Azure virtuelle computere. Dokumentet dækker først kritiske forskellene mellem kører Windows Server AD DS og AD FS på Azure virtuelle maskiner kontra lokale og vigtige beslutninger, der påvirker design og installation. Resten af papiret forklares retningslinjer for hvert af beslutning punkterne i flere detaljer, og hvordan du anvender retningslinjerne på forskellige installationsscenarier.

I denne artikel omhandler ikke konfigurationen af [Azure Active Directory](http://azure.microsoft.com/services/active-directory/), som er en REST-baseret tjeneste, som indeholder identitet administration og access control-funktioner til skyen programmer. Azure Active Directory (Azure AD) og Windows Server AD DS er, men udviklet til at fungere sammen for at give en identitets- og management-løsning til dags hybrid IT-miljøer og moderne programmer. For at forstå forskellene og relationer mellem Windows Server AD DS og Azure AD, skal du overveje følgende:

1. Du kan køre Windows Server AD DS i skyen på Azure virtuelle maskiner, når du bruger Azure til at udvide dit lokale datacenter til skyen.
2. Du kan bruge Azure AD til at give dine brugere enkeltlogon til Software som-en-tjenesten (SaaS)-programmer. Microsoft Office 365 bruger denne teknologi, for eksempel og programmer, der kører på Azure eller andre skyen platforme kan også bruge den.
3. Du kan bruge Azure AD (dens Access Control Service) til at lade brugerne kan logge på ved hjælp af identiteter fra Facebook, Google, Microsoft og andre identitetsudbydere til programmer, der er placeret i skyen eller i det lokale miljø.

Du kan finde flere oplysninger om disse forskelle, [Azure identitet](fundamentals-identity.md).

## <a name="related-resources"></a>Relaterede ressourcer

Du kan downloade og køre [Vurdering af Azure virtuelt parathed](https://www.microsoft.com/download/details.aspx?id=40898). Vurdering bliver undersøge dit lokale miljø og automatisk oprette en brugerdefineret rapport, der er baseret på den vejledning, der findes i dette emne for at hjælpe dig med at overføre miljøet til Azure.

Vi anbefaler, at du også vælge først gennemser selvstudier, vejledninger og videoer, der dækker de følgende emner:

- [Konfigurere et virtuelt netværk kun skyen på portalen Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)
- [Konfigurere en VPN til-websted på portalen Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md)
- [Installere en ny Active Directory-område på en Azure virtuelt netværk](active-directory-new-forest-virtual-machine.md)
- [Installere en Active Directory replikeringsdomænecontroller på Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)
- [Microsoft Azure IT Pro IaaS: (01) virtuelt grundlæggende](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
- [Microsoft Azure IT Pro IaaS: (05) oprette virtuelle netværk og tværs lokale forbindelse](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

## <a name="introduction"></a>Introduktion

De grundlæggende krav til installation af Windows Server Active Directory på Azure virtuelle maskiner variere meget lille fra implementering af den på lokale virtuelle computere (og til en vis grad, fysiske computere). For eksempel i tilfælde af Windows Server AD DS, hvis domænecontrollerne (DCs), som du installerer på Azure virtuelle maskiner er kopier i en eksisterende lokale virksomhedens/domæner, og klik derefter på Azure-installation kan stort set behandles på samme måde, som du kan behandle ethvert andet yderligere Windows Server Active Directory-websted. Det vil sige, skal være defineret undernet i Windows Server AD DS, et websted, der er oprettet, undernet knyttet til dette websted og forbindelse til andre websteder, der bruger relevante hyperlinks. Der er dog nogle forskelle, der er fælles for alle Azure installationer og nogle, variere, alt efter scenariet bestemt installation. To grundlæggende forskelle er beskrevet nedenfor:

### <a name="azure-virtual-machines-may-need-connectivity-to-the-on-premises-corporate-network"></a>Azure virtuelle maskiner muligvis forbindelse til virksomhedens netværk i det lokale miljø.

Oprette forbindelse Azure virtuelle maskiner tilbage til et lokalt firmanetværk kræver Azure virtuelt netværk, som indeholder en-til-websted eller websted-til-punkt virtuelt privat netværk (VPN) komponent få problemfrit forbindelse Azure virtuelle maskiner og lokale computere. Denne VPN-komponent kan også aktivere lokale domæne medlemscomputere at få adgang til et Windows Server Active Directory-domæne, hvis domænecontrollere er hostet udelukkende på Azure virtuelle computere. Det er vigtigt at være opmærksom på, dog, hvis VPN mislykkes, godkendelse og andre handlinger, som er afhænger af Windows Server Active Directory også mislykkes. Mens brugere kan muligvis logge på med eksisterende cachelagrede legitimationsoplysninger, alle peer-to-peer- eller forsøg på klient til server-godkendelse, billetter har endnu udstedes eller er blevet forældede mislykkes.

Se [Virtuelt netværk](http://azure.microsoft.com/documentation/services/virtual-network/) til en video demonstration og en liste over selvstudier med trinvis vejledning, herunder [konfigurere et websted til websted VPN i portalen Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [AZURE.NOTE] Du kan også installere Windows Server Active Directory i en Azure virtuelt netværk, der ikke har forbindelse med et lokalt netværk. Retningslinjer i dette emne forudsætter dog, et netværk med Azure virtuelle bruges, fordi det giver IP håndtere funktioner, der er vigtige for Windows Server.

### <a name="static-ip-addresses-must-be-configured-with-azure-powershell"></a>Statiske IP-adresser skal være konfigureret med Azure PowerShell.

Dynamiske adresser er allokeret som standard, men brug cmdlet'en Set-AzureStaticVNetIP til at tildele en statisk IP-adresse i stedet. Som angiver en statisk IP-adresse, der bevares via tjenesten reparation og VM lukning/genstart. Du kan finde yderligere oplysninger finder [statisk interne IP-adresse til virtuelle computere](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/).

## <a name="BKMK_Glossary"></a>Begreber og definitioner

Følgende er en ikke-omfattende liste over vilkårene for forskellige Azure teknologier, der skal refereres til i denne artikel.

- **Azure virtuelle maskiner**: feltet IaaS tilbyder i Azure, der gør det muligt at installere VM'er, der kører næsten alle traditionelt lokal server arbejdsbyrde.

- **Azure virtuelt netværk**: Netværkstjeneste i Azure, så kunderne kan oprette og administrere virtuelle netværk i Azure og sikkert knytte dem til deres egen lokale netværksinfrastruktur ved hjælp af et virtuelt privat netværk (VPN).

- **Virtuelle IP-adresse**: en forbindelse til internettet IP-adresse, der ikke er bundet til et bestemt computeren eller netværket interface kort. Skytjenester, tildeles en virtuel IP-adresse til at modtage netværkstrafik, som omdirigeres til en Azure VM. En virtuel IP-adresse er en egenskab for en skytjeneste, som kan indeholde et eller flere Azure virtuelle computere. Bemærk også, at en Azure virtuelt netværk kan indeholde et eller flere skytjenester. Virtuelle IP-adresser giver oprindelige belastning funktioner.

- **Dynamisk IP-adresse**: Dette er den IP-adresse, der kun er interne. Det skal være konfigureret som en statisk IP-adresse (ved hjælp af cmdlet'en Set-AzureStaticVNetIP til) til VM'er, der hoster rollerne DC/DNS-server.

- **Tjenesten reparation**: den proces, hvori Azure automatisk returnerer en tjeneste til en igangværende tilstand igen når programmet registrerer, at tjenesten mislykkes. Tjenesten reparation er en af aspekter af Azure, der understøtter tilgængelighed og fleksibilitet. Mens sandsynlighed, svarer til en ikke-planlagt genstart resultatet følger en tjeneste reparation hændelse for et DC, der kører på en VM, men det indeholder et par side-effekter:

 - Det virtuelle netværkskort i VM ændres
 - MAC-adressen for det virtuelle netværkskort ændres
 - Processor/CPU-ID VM ændres
 - IP-konfigurationen af det virtuelle netværkskort ændres ikke, så længe VM er knyttet til et virtuelt netværk og den VM IP-adressen er statisk.

 Ingen af disse funktionsmåder påvirker Windows Server Active Directory, da det har ingen afhængighed på MAC-adresse eller Processor/CPU-ID, og alle Windows Server Active Directory-installationer på Azure er anbefalet til at blive kørt i en Azure virtuelt netværk, som beskrevet ovenfor.

## <a name="is-it-safe-to-virtualize-windows-server-active-directory-domain-controllers"></a>Er det sikkert at virtualisere Windows Server Active Directory domain enheder?

Installation af Windows Server Active Directory DCs på Azure virtuelle maskiner, er underlagt de samme retningslinjer som kører DCs lokalt i en virtuel maskine. Kører virtuelt DCs er hensigtsmæssigt fejlsikret, så længe retningslinjer for at sikkerhedskopiere og gendanne DCs følges. Du kan finde flere oplysninger om begrænsninger og retningslinjer for at køre virtuelt DCs [Kører domænecontrollere i Hyper-V](https://technet.microsoft.com/library/dd363553).

Hypervisors Giv eller trivialize teknologier, der kan medføre problemer med mange distribuerede systemer, herunder Windows Server Active Directory. For eksempel på en fysisk server, du kan klone en disk eller bruge ikke-understøttede metoder til at annullere tilstanden for en server, herunder brug af SANs osv., men at gøre det på en fysisk server er meget sværere end gendanne et virtuelt øjebliksbillede i en hypervisor. Azure indeholder funktioner, der kan resultere i den samme uønskede betingelse. For eksempel skal du ikke kopiere Virtuelle filer af DCs i stedet for at udføre regelmæssige sikkerhedskopier, da gendanne dem kan medføre en lignende situation til brug af øjebliksbillede gendannelsesfunktioner.

Sådan Rollback introducere USN bobler, der kan medføre permanent divergerende stater mellem DCs. Der kan medføre problemer med f.eks.:

- Hængende objekter
- Inkonsekvent adgangskoder
- Inkonsekvent attributværdier
- Skema uoverensstemmelser Hvis skema master rulles tilbage

Du kan finde flere oplysninger om, hvordan DCs påvirkes se [USN og USN Rollback](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv.aspx#usn_and_usn_rollback).

Begynder med Windows Server 2012, [yderligere sikkerhedsforanstaltninger er indbygget i Active Directory-Domænetjenester](https://technet.microsoft.com/library/hh831734.aspx). Disse sikkerhedsforanstaltninger til at beskytte virtuelt domænecontrollere mod ovennævnte problemer, så længe den underliggende hypervisor platform understøtter VM GenerationID. Azure understøtter VM-GenerationID, hvilket betyder, at domænet enheder, der kører Windows Server 2012 eller nyere på Azure virtuelle maskiner har yderligere sikkerhedsforanstaltninger.

> [AZURE.NOTE] Du skal lukke og genstarte en VM, der kører rollen domænecontroller i Azure i operativsystemet gæst i stedet for ved hjælp af indstillingen **Luk** i portalen Azure klassisk. I dag, får ved hjælp af portalen klassisk for at lukke en VM VM kan deallokeres. En deallocated VM indeholder fordelen ved at skulle ikke betale, men det også nulstiller VM-GenerationID, som er uønskede for et DC. Når VM-GenerationID er nulstillet, invocationID af Active Directory-Domænetjenester databasen også er nulstillet, RELATIVE puljen kasseret og SYSVOL er markeret som ikke-autoritative. Se [Introduktion til Active Directory-domænetjenester (AD DS) Virtualization](https://technet.microsoft.com/library/hh831734.aspx) og [Sikkert virtualisering DFSR](http://blogs.technet.com/b/filecab/archive/2013/04/05/safely-virtualizing-dfsr.aspx)kan finde flere oplysninger.

## <a name="why-deploy-windows-server-ad-ds-on-azure-virtual-machines"></a>Hvorfor installere Windows Server AD DS på virtuelle Azure-computere?

Mange Windows Server AD DS installationer er velegnet til installation som FOS på Azure. Lad os antage, at du har en virksomhed i Europa, der skal godkendes brugere i et andet sted i Asien. Virksomheden har ikke tidligere har installeret Windows Server Active Directory DCs i Asien på grund af omkostninger for at installere dem og begrænset erfaring for at administrere servere efter installationen. Anmodninger om godkendelse fra Asien er derfor behandles af DCs i Europa med ikke-optimal resultater. I dette tilfælde kan du installere et DC på en VM, du har angivet skal køres i Azure datacenter i Asien. Vedhæfte DC til et Azure virtuelle netværk, der er tilsluttet direkte til den fjernplacering forbedrer ydeevnen godkendelse.

Azure er også velegnet som erstatning til ellers dyrt nedbrud gendannelse (DR) websteder. Relativt lav-omkostninger for vært for et lille antal domænecontrollere og et enkelt virtuelt netværk på Azure repræsenterer et flot alternativ.

Til sidst, kan du installere et netværksprogram på Azure, som SharePoint, som kræver Windows Server Active Directory, men har ingen afhængighed på det lokale netværk eller på virksomhedens Windows Server Active Directory. I dette tilfælde implementere et isolerede område på Azure til at opfylde SharePoint serverens krav er optimal. Igen, implementering af netværksprogrammer, der kræver forbindelse til den lokale netværk og virksomhedens Active Directory er også understøttet.

> [AZURE.NOTE] Da den indeholder en layer 3-forbindelse, kan den VPN-komponent, der indeholder forbindelsen mellem en Azure virtuelt netværk og et lokalt netværk også aktivere medlemsservere, der kører lokalt for at udnytte DCs, der kører som Azure virtuelle maskiner på Azure virtuelt netværk. Men hvis VPN er tilgængelig, kommunikation mellem lokale computere og Azure-baserede domænecontrollere fungerer ikke, resulterende i godkendelse og forskellige andre fejl.  

## <a name="contrasts-between-deploying-windows-server-active-directory-domain-controllers-on-azure-virtual-machines-versus-on-premises"></a>Står i kontrast mellem installation af Windows Server Active Directory domain enheder på virtuelle Azure-computere eller lokalt

- Til en hvilken som helst Windows Server Active Directory Installation scenarie, der indeholder mere end en enkelt VM, er det nødvendigt at bruge en Azure virtuelt netværk til IP-adresse konsistens. Bemærk, at denne vejledningen antages, at DCs kører på en Azure virtuelt netværk.

- Som med lokale DCs, anbefales det statiske IP-adresser. En statisk IP-adresse kan kun konfigureres ved hjælp af Azure PowerShell. Du kan finde flere oplysninger i [statisk interne IP-adresse til FOS](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/) . Hvis du har overvågning systemer eller andre løsninger, der kontrollerer for statiske IP-adressekonfiguration i operativsystemet gæst, kan du tildele den samme statiske IP-adresse til egenskaberne netværk adapter VM. Men vær opmærksom på, at netværkskortet, bliver kasseret, hvis VM gennemgår service reparation eller lukkes ned i portalen klassisk og har adressen deallokeres. Det er tilfældet, skal den statiske IP-adresse i gæst nulstilles.

- Implementere FOS i et virtuelt netværk ikke indikerer (eller kræver) forbindelse tilbage til et lokalt netværk; det virtuelle netværk kan kun, hvis denne mulighed. Du skal oprette et virtuelt netværk til privat kommunikation mellem Azure og dit lokale netværk. Du skal installere en VPN-slutpunktet på det lokale netværk. VPN-Forbindelsen er åbnet fra Azure på det lokale netværk. Se [Oversigt over virtuelle netværk](../virtual-network/virtual-networks-overview.md) og [konfigurere et websted til websted VPN i portalen Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md)kan finde flere oplysninger.

> [AZURE.NOTE] Muligheden for at [oprette en VPN-forbindelse til punkt-til-websted](../vpn-gateway/vpn-gateway-point-to-site-create.md) er tilgængelig til at oprette forbindelse individuelle Windows-baserede computere direkte til en Azure virtuelt netværk.

- Uanset om du opretter en virtuel netværk eller ej, Azure gebyrer for udgangspunkt trafik, men ikke vandindtrængen. Forskellige Windows Server Active Directory designvalg kan påvirke, hvor meget udgangspunkt trafik genereres af en hybridinstallation. For eksempel begrænser anvender en skrivebeskyttet domænecontrolleren (RODC) udgangspunkt trafik fordi det ikke gentage udgående. Men beslutningen om at installere en RODC skal vejes mod behovet for at udføre skrivning mod DC og [kompatibilitet](https://technet.microsoft.com/library/cc755190) , programmer og tjenester i webstedet har med RODC'er. Se [Azure priser på et hurtigt](http://azure.microsoft.com/pricing/)kan finde flere oplysninger om trafik gebyrer.

- Mens du har fuldført bestemme over hvilke server ressourcer, der skal bruges til FOS lokale miljø, som RAM, disk størrelse og osv., på Azure skal du vælge på en liste over forudkonfigurerede serverstørrelser. For et DC bruges en datadisk ud over operativsystemet disken for at gemme Windows Server Active Directory-databasen.

## <a name="can-you-deploy-windows-server-ad-fs-on-azure-virtual-machines"></a>Kan du installere Windows Server AD FS på Azure virtuelle computere?

Ja, du kan installere Windows Server AD FS på Azure virtuelle maskiner, og de [bedste fremgangsmåder til AD FS-installation](https://technet.microsoft.com/library/dn151324.aspx) i det lokale miljø gælder også for AD FS-installation på Azure. Men nogle af de bedste fremgangsmåder såsom justering af belastning og høj tilgængelighed kræver teknologier ud over hvad AD FS tilbyder sig selv. De skal være udstyret med den underliggende struktur. Lad os gennemgå nogle af disse bedste fremgangsmåder, og se, hvordan de kan der opnås ved hjælp af Azure FOS og en Azure virtuelt netværk.

1. **Aldrig fremvise sikkerhed token service (STS) servere direkte til internettet.**

    Det er vigtigt, da STS problemer sikkerhedstokens. Servere STS såsom AD FS-servere behandles som et resultat med det samme niveau af beskyttelse som et domænenavn fra domænecontrolleren. Hvis en STS afsløret, har ondsindede brugere mulighed for at udstede access tokens der potentielt indeholder krænkelse af deres vælger at afhængige part-programmer og andre STS servere i have tillid til organisationer.

2. **Installere Active Directory-domænecontrollere for alle bruger domæner i det samme netværk som AD FS-servere.**

    AD FS-servere bruge Active Directory Domain Services til at godkende brugere. Det anbefales at installere domænecontrollere på samme netværk som AD FS-servere. Dette giver Forretningskontinuitet i tilfælde af sammenkædningen mellem Azure netværk og dit lokale netværk er brudt, og gør det muligt for kortere ventetid og forbedret ydeevne ved logon.

3. **Installere flere AD FS noder til høj tilgængelighed og justering af belastning.**

    I de fleste tilfælde er fejl i et program, der gør det muligt for AD FS kan accepteres, fordi de programmer, der kræver sikkerhedstokens, er ofte opgavekritiske. Som et resultat, og fordi AD FS nu er placeret i den kritiske vej til at få adgang til vigtigste programmer, AD FS-tjenesten skal være meget tilgængelige gennem flere AD FS proxyer og AD FS-servere. For at opnå fordelingen af anmodninger skal installeres typisk Indlæs balancere foran både AD FS proxyer og AD FS-servere.

4. **Installere en eller flere Web proxyen noder adgang til internettet.**

    Når brugere skal have adgang til programmer, der er beskyttet af AD FS-tjenesten, skal AD FS-tjenesten være tilgængelig fra internettet. Dette opnås ved at installere tjenesten Web proxyen. Det anbefales at implementere mere end én node med henblik på høj tilgængelighed og justering af belastning.

5. **Begrænse adgangen fra Web proxyen noder til interne netværksressourcer.**

    Hvis du vil give eksterne brugere adgang til AD FS fra internettet, skal du installere programmet Webtjenesteproxy noder (eller AD FS-Proxy i tidligere versioner af Windows Server). Noderne webprogram proxy vises direkte på internettet. De er ikke påkrævet for at være medlem af et domæne, og de skal kun adgang til AD FS-serverne over TCP-port 443 og 80. Det anbefales, at kommunikation til alle andre computere (især domænecontrollere) blokeres.

    Dette er typisk opnået lokalt ved hjælp af en DMZ. Firewalls Brug en whitelist funktionsmåde til at begrænse trafik fra DMZ til det lokale netværk (det vil sige, kun trafik fra de angivne IP-adresser og over angivne porte er tilladt, og alle andre trafikken er blokeret).

I følgende diagram vises en traditionel lokale AD FS-installation.

![Diagram over en traditionel lokalt Active Directory Federation Services-installation](media/active-directory-deploying-ws-ad-guidelines/ADFS_onprem.png)

Men Azure leverer ikke oprindelige, komplette firewall egenskab, andre indstillinger skal bruges til at begrænse trafik. I følgende tabel vises hver indstilling og dens fordele og ulemper ved.

| Indstillingen | Fordel | Ulempe |
| ------ | --------- | ------------ |
| [Azure netværk ACLs](virtual-networks-acl.md) | Mindre dyrt og nemmere indledende konfiguration | Yderligere ACL netværkskonfiguration påkrævet, hvis alle nye FOS føjes til installationen |
| [Barracuda viser firewall](https://www.barracuda.com/products/ngfirewall) | Whitelist tilstand og den kræver ingen ACL netværkskonfiguration | Øgede omkostninger og kompleksitet til første opsætning |

De overordnede trin til at udrulle AD FS er i dette tilfælde som følger:

1. Oprette et virtuelt netværk med connectivity i tværs det lokale miljø, ved hjælp af en VPN- eller [ExpressRoute](http://azure.microsoft.com/services/expressroute/).

2. Installer domænecontrollere på det virtuelle netværk. Dette trin er valgfrit, men det anbefales.

3. Installer medlem af et domæne AD FS-servere på det virtuelle netværk.

4. Oprette en [intern Indlæs afstemmes sæt](http://azure.microsoft.com/blog/internal-load-balancing/) , der indeholder AD FS-serverne og bruger en ny privat IP-adresse i det virtuelle netværk (en dynamisk IP-adresse).

  1. Opdatere DNS for at oprette det fulde Domænenavn til at pege på den private (dynamisk) IP-adressen på sættet interne Indlæs afstemmes.

5. Oprette en skybaseret tjeneste (eller et separat virtuelt netværk) for noderne Web proxyen.

6. Installere Web proxyen knuderne i skybaseret tjeneste eller virtuelt netværk

  1. Oprette en ekstern Indlæs afstemmes sæt, der omfatter noderne Web proxyen.

  2. Opdatere eksterne DNS-navnet (fulde Domænenavn) til at pege på skyen service offentlige IP-adresse (den virtuelle IP-adresse).

  3. Konfigurere AD FS-proxy for at bruge det fulde Domænenavn, der svarer til de interne Indlæs afstemmes AD FS-servere.

  4. Opdater kravbaseret websteder for at bruge eksterne fulde Domænenavn for deres krav udbyder.

7. Begrænse adgang mellem Web proxyen til en hvilken som helst computer i det virtuelle AD FS-netværk.

Hvis du vil begrænse trafik sættet netværksbelastningen for den Azure interne justering af belastning skal konfigureres til kun trafik til TCP-port 80 og 443, og alle andre trafik til den interne dynamiske IP-adressen på sættet Indlæs afstemmes udelades.

![Diagram over ADFS netværk ACLs med TCP 443 + 80 tilladt.](media/active-directory-deploying-ws-ad-guidelines/ADFS_ACLs.png)

Trafik til AD FS-serverne vil være tilladt kun efter følgende kilder:

- Den Azure interne justering af belastning.
- IP-adressen på en administrator på den lokale netværk.

> [AZURE.WARNING] Designet skal forhindre Web proxyen noder fra at kontakte andre FOS i det Azure virtuelle netværk eller nogen steder på det lokale netværk. Der kan udføres ved at konfigurere firewallregler i det lokale anvendelses for Express distribuere forbindelser eller VPN-enhed til at websted VPN-forbindelser.

En ulempe til denne indstilling er at skulle konfigurere netværk ACLs for flere enheder, herunder interne justering af belastning, AD FS-servere og andre servere, der er føjet til det virtuelle netværk. Hvis en hvilken som helst enhed er føjet til installationen uden at konfigurere netværk ACLs for at begrænse trafik til den, kan være hele installationen på ansvar. Hvis der ændrer nogensinde noderne Web proxyen IP-adresser, skal netværket ACLs nulstilles (hvilket betyder, at proxyerne skal være konfigureret til at bruge [statisk dynamiske IP-adresser](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/)).

![ADFS på Azure med netværk ACLS.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure.png)

En anden mulighed er at bruge maskinen [Barracuda viser Firewall](https://www.barracuda.com/products/ngfirewall) til at styre trafik mellem AD FS-proxyservere og AD FS-servere. Denne indstilling er i overensstemmelse med bedste fremgangsmåder til sikkerhed og høj tilgængelighed og kræver mindre administration efter den indledende installation, fordi Barracuda viser Firewall maskinen har en whitelist tilstand af firewall administration og den kan være installeret direkte på en Azure virtuelt netværk. Behovet for at konfigurere netværk ACLs helst en ny server er føjet til installationen. Men denne indstilling føjer indledende installation kompleksitet og omkostninger.

I dette tilfælde installeres to virtuelle netværk i stedet for ét. Vi vil ringe til dem VNet1 og VNet2. VNet1 indeholder proxyerne og VNet2 indeholder STSs og netværksforbindelsen tilbage til virksomhedens netværk. VNet1 er derfor fysisk (selvom næsten) isolerede fra VNet2 og også fra virksomhedens netværk. VNet1 derefter er forbundet med VNet2 ved hjælp af en speciel tunnelføring teknologi kendt som Transport uafhængige netværk arkitektur (TINA). TINA tunnel er knyttet til hver af de virtuelle netværk ved hjælp af en Barracuda viser firewall – én Barracuda på hver af de virtuelle netværk.  For høj tilgængelighed anbefales det, du anvender to F.eks i hver virtuelt netværk en aktiv, de andre passiv. De tilbyder særdeles omfattende firewallbeskyttelse funktioner som gør det muligt for os til at efterligne driften af en traditionel lokalt DMZ i Azure.

![ADFS på Azure med firewall.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure_firewall.png)

Du kan finde flere oplysninger, se [AD FS: udvide en krav opmærksom på lokale front end-program til internettet](#BKMK_CloudOnlyFed).

### <a name="an-alternative-to-ad-fs-deployment-if-the-goal-is-office-365-sso-alone"></a>Et alternativ til AD FS-installation, hvis formålet er Office 365 SSO alene

Der er en anden alternativ til at implementere AD FS helt, hvis formålet er, at kun til at aktivere-logon til Office 365. Du kan i så fald blot installere DirSync med adgangskode Synkroniser lokalt og opnå den samme formål med minimale installation kompleksitet, fordi denne fremgangsmåde ikke kræver AD FS eller Azure.

Følgende tabel sammenligner Sådan logon processer fungerer med og uden at installere AD FS.

| Office 365 enkelt sign-on – ved hjælp af AD FS og DirSync | Office 365 samme enkeltlogon ved hjælp af DirSync + synkronisering af adgangskoder |
| ------------- | ------------- |
| 1. brugeren logger på virksomhedens netværk og er godkendt til Windows Server Active Directory. | 1. brugeren logger på virksomhedens netværk og er godkendt til Windows Server Active Directory. |
| 2. brugeren forsøger at få adgang til Office 365 (jeg @contoso.com). | 2. brugeren forsøger at få adgang til Office 365 (jeg @contoso.com). |
| 3. office 365 omdirigerer brugeren til Azure AD. | 3. office 365 omdirigerer brugeren til Azure AD. |
| 4. Da Azure AD ikke godkende brugeren og forstår, der er et tillidsforhold med AD FS lokalt, omdirigerer brugeren til AD FS. | 4. azure AD kan ikke acceptere Kerberos-billetter direkte, og der findes ingen tillidsforhold, så der anmodes om, at brugeren Angiv legitimationsoplysninger. |
| 5. bruger sender en Kerberos brugertilladelse til AD FS STS. | 5. brugeren angiver den samme lokale adgangskode, og Azure AD valideret dem mod det brugernavn og adgangskode, der blev synkroniseret ved DirSync. |
| 6. AD FS transformerer Kerberos brugertilladelse til de nødvendige token format/krav og omdirigerer brugeren til Azure AD. | 6. azure AD omdirigerer brugeren til Office 365. |
| 7. brugeren godkendes til Azure AD (en anden transformation forekommer). |  7. brugeren kan logge på Office 365 og OWA ved hjælp af Azure AD-token. |
| 8. azure AD omdirigerer brugeren til Office 365. |  |
| 9. brugeren er automatisk logget Office 365. |  |

I Office 365 med DirSync med adgangskode Synkroniser scenarie (ingen AD FS) erstattes enkeltlogon af "samme sign-on" hvor "samme" blot betyder, at brugerne igen skal indtaste deres samme lokale legitimationsoplysninger, når du åbner Office 365. Bemærk, at disse data kan huskes af brugerens browser for at reducere efterfølgende instruktionerne.

### <a name="additional-food-for-thought"></a>Yderligere mad for tanken

- Hvis du installerer en AD FS-proxy på en Azure virtuel maskine, er det nødvendigt forbindelse til AD FS-servere. Hvis de er i det lokale miljø, anbefales det, at du udnytte til websted VPN-forbindelsen fra det virtuelle netværk til at tillade Web proxyen noder til at kommunikere med deres AD FS-servere.

- Hvis du installerer en AD FS server på en Azure virtuel maskine, er nødvendigt forbindelse til Windows Server Active Directory-domænecontrollere, attribut butikker og konfigurationsdatabaser og kræver muligvis også en ExpressRoute eller et websted til websted VPN-forbindelse mellem Azure virtuelt netværk og lokale netværk.

- Gebyrer anvendes på al trafik fra Azure virtuelle maskiner (udgangspunkt trafik). Hvis omkostninger er fører faktor, er det anbefales at installere noderne Web proxyen på Azure, at forlade de AD FS-servere i det lokale miljø. Hvis AD FS-serverne er installeret på Azure virtuelle maskiner samt, påløber yderligere omkostninger for at godkende lokale brugere. Udgangspunkt trafik medfører en omkostning uanset om det er gå på tværs ExpressRoute eller VPN-forbindelsen til websted.

- Hvis du beslutter at bruge Azures oprindelige server belastningsjustering funktioner til høj tilgængeligheden af AD FS-servere, være opmærksom på, at belastning giver sonder, der bruges til at bestemme tilstanden for de virtuelle maskiner i skytjenesten. I forbindelse med Azure virtuelle maskiner (i modsætning til internettet eller arbejder roller) bruges en brugerdefineret efterprøvning af af, da den agent, der svarer til standard sonder ikke er til stede på Azure virtuelle maskiner. Du kan bruge en brugerdefineret TCP efterprøvning af af for enkel, – dette kræver kun der oprettes en TCP-forbindelse (en TCP SYN målgruppe sendt og reageret på med et TCP SYN-ACK segment) korrekt til at bestemme virtuelt tilstand. Du kan konfigurere brugerdefineret efterprøvning af af for at bruge en hvilken som helst TCP-port, som din virtuelle maskiner aktivt lytter.

> [AZURE.NOTE] Maskiner, der har brug for at få vist det samme sæt af porte direkte til internettet (såsom port 80 og 443) kan ikke dele den samme skybaseret tjeneste. Derfor, anbefales det, at du opretter en dedikeret skybaseret tjeneste til dine Windows Server AD FS-servere for at undgå potentielle overlapper mellem portkravene til et program og Windows Server AD FS.

## <a name="deployment-scenarios"></a>Installationsscenarier

De følgende afsnit beskrives almindelig installationsscenarier for at henlede opmærksomheden på vigtige overvejelser, der skal tages højde. Hvert scenario indeholder links til flere oplysninger om de beslutninger og faktorer du bør overveje.

1. [AD DS: Installere et AD DS-aware program med underlagt krav om virksomhedens netværksforbindelse](#BKMK_CloudOnly)

    For eksempel er en forbindelse til internettet SharePoint-tjenesten installeret på en Azure virtuelt. Programmet har ingen afhængigheder på virksomhedens netværk ressourcer. Programmet kræver Windows Server AD DS, men kræver ikke virksomhedens Windows Server AD DS.

2. [AD FS: Udvide en krav opmærksom på lokale front end-program til internettet](#BKMK_CloudOnlyFed)

    For eksempel skal en krav-program, der er blevet installeret lokalt og bruges af virksomhedens brugere være tilgængeligt via internettet. Programmet skal åbnes direkte via internettet, både efter forretningsforbindelser ved hjælp af deres egen virksomhedens identitet og efter eksisterende virksomhedens brugere.

3. [AD DS: Installere en Windows Server AD DS-aware program, der kræver forbindelse til virksomhedens netværk](#BKMK_HybridExt)

    For eksempel er en LDAP-aware program, der understøtter Windows-integreret godkendelse og bruger Windows Server AD DS som et lager til konfiguration og brugerprofil data installeret på en Azure virtuelt. Det er hensigtsmæssigt for programmet udnytte eksisterende virksomhedens Windows Server AD DS og angive enkeltlogon. Programmet er ikke krav-aware.

### <a name="BKMK_CloudOnly"></a>1. AD DS: Installere et AD DS-aware program med underlagt krav om virksomhedens netværksforbindelse

![Kun skyen AD DS installation](media/active-directory-deploying-ws-ad-guidelines/ADDS_cloud.png)
**Figur 1**

#### <a name="description"></a>Beskrivelse

SharePoint er installeret på en Azure virtuelt og programmet har ingen afhængigheder på virksomhedens netværk ressourcer. Programmet kræver Windows Server AD DS, men *ikke* kræver virksomhedens Windows Server AD DS. Ingen Kerberos eller medlem af organisationsnetværket tillidsforhold er påkrævet, fordi brugere er selv klargjort via programmet til Windows Server AD DS-domæne, der er også vært i skyen på Azure virtuelle maskiner.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Scenarie overvejelser, og hvordan teknologiområder gælder for dette scenario

- [Netværkstopologi](#BKMK_NetworkTopology): oprette en Azure virtuelt netværk uden tværs lokale connectivity (også kaldet til websted connectivity).

- [DC installationskonfiguration](#BKMK_DeploymentConfig): installere en ny domænenavn fra domænecontrolleren ind i et nyt, enkelt domæne, Windows Server Active Directory-område. Dette skal installeres sammen med Windows DNS-server.

- [Windows Server Active Directory websted topologi](#BKMK_ADSiteTopology): Brug af Windows Server Active Directory standardwebstedet (alle computere befinder sig i standard-første-sted-navn).

- [IP-adresser og DNS](#BKMK_IPAddressDNS):

 - Angiv en statisk IP-adresse til DC ved hjælp af sæt AzureStaticVNetIP Azure PowerShell-cmdlet.
 - Installere og konfigurere Windows Server DNS på domæne eller på Azure.
 - Konfigurere egenskaber for virtuelle netværk med navn og IP-adressen på den VM, der er vært rollerne DC og DNS-server.

- [Globalt katalog](#BKMK_GC): den første DC i området, der skal være en globalt katalog-server. Yderligere DCs bør også være konfigureret som globale kataloger, fordi det globale katalog ikke kræver yderligere arbejde fra Domænecontrolleren i en enkelt domæner.

- [Placeringen af Windows Server AD DS database og SYSVOL](#BKMK_PlaceDB): føje data disken til DCs kører som Azure FOS for at gemme den Windows Server Active Directory-database, logfiler og SYSVOL.

- [Sikkerhedskopiere og gendanne](#BKMK_BUR): finde ud af, hvor du vil gemme system tilstand sikkerhedskopier. Hvis det er nødvendigt, kan du tilføje en anden datadisk til DC VM til lagring af sikkerhedskopier.

### <a name="BKMK_CloudOnlyFed"></a>2 AD FS: udvide en krav opmærksom på lokale front end-program til internettet

![Sammenslutning med tværs lokale connectivity](media/active-directory-deploying-ws-ad-guidelines/Federation_xprem.png)
**Figur 2**

#### <a name="description"></a>Beskrivelse

Et krav-aware program, der er blevet installeret lokalt og bruges af virksomhedens brugere skal være tilgængelige direkte fra internettet. Programmet fungerer som en web front end til en SQL-database, hvor der gemmes data. De SQL-servere, der bruges af programmet findes også i virksomhedens netværk. To Windows Server AD FS STSs og en belastningsjustering er blevet installeret i det lokale miljø til at give adgang til virksomhedens brugere. Programmet nu skal åbnes desuden direkte via internettet, både efter forretningsforbindelser ved hjælp af deres egen virksomhedens identitet og efter eksisterende erhvervsbrugere.

I et forsøg på at forenkle og imødekomme installation og konfiguration af dette nye krav, er det besluttet, to ekstra web frontends og to Windows Server AD FS proxyservere installeres på Azure virtuelle computere. Alle fire FOS direkte, kan ses på internettet og får forbindelse til netværket lokalt med Azure den virtuelle netværkets til websted VPN egenskab.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Scenarie overvejelser, og hvordan teknologiområder gælder for dette scenario

- [Netværkstopologi](#BKMK_NetworkTopology): oprette en Azure virtuelt netværk og [konfigurere netværksmuligheder for tværs det lokale miljø](../vpn-gateway/vpn-gateway-site-to-site-create.md).

 > [AZURE.NOTE] Sørg for, at URL-adressen, der er defineret i certifikatskabelonen og de deraf følgende certifikater kan kontaktes af Windows Server AD FS forekomster kører på Azure for hver af Windows Server AD FS-certifikater. Dette kan kræve tværs lokale forbindelse til dele af infrastrukturen PKI. For eksempel hvis den tilbagekaldte certifikater slutpunkt er LDAP-baserede og hostet udelukkende lokalt, derefter kryds lokale connectivity vil blive krævet. Hvis dette ikke er hensigtsmæssigt, kan det være nødvendigt at bruge certifikater, der er udstedt af et Nøglecenter, hvis listen over tilbagekaldte certifikater, der er tilgængelige via internettet.

- [Konfiguration af Cloud services](#BKMK_CloudSvcConfig): sikre, at du har to skytjenester i rækkefølge indeholder to netværksbelastningen virtuelle IP-adresser. Den første skytjeneste virtuelle IP-adresse får adgang til de to Windows Server AD FS-proxy FOS på port 80 og 443. Windows Server AD FS-proxy FOS bliver konfigureret til at pege på IP-adressen på den lokale-belastningsjustering, forsider Windows Server AD FS STSs. Den anden skytjeneste virtuelle IP-adresse får adgang til de to VM'er, der kører web frontend igen på port 80 og 443. Konfigurere et brugerdefineret efterprøvning af af for at sikre belastningsjustering kun dirigerer trafik til fungerer Windows Server AD FS-proxy og web frontend FOS.

- [Sammenslutning serverkonfiguration](#BKMK_FedSrvConfig): konfigurere Windows Server AD FS som sammenslutningsserveren (STS) til at generere sikkerhedstokens til Windows Server Active Directory-området, der er oprettet i skyen. Konfigurere sammenslutning krav udbyder tillidsforhold med de forskellige partnere, som du vil acceptere identiteter fra, og Konfigurer afhængige part tillidsforhold med de forskellige programmer, du vil generere tokens til.

    I de fleste scenarier implementeret Windows Server AD FS-proxy-servere i en forbindelse til internettet kapacitet af sikkerhedsmæssige årsager, mens deres Windows Server AD FS sammenslutning versionerne forbliver isolerede fra direkte forbindelse til internettet. Uanset scenariet installation, skal du konfigurere din skytjeneste med en virtuel IP-adresse, der giver en offentligt tilgængelig IP-adresse og port, der kunne belastning på tværs af din to forekomster af Windows Server AD FS STS eller proxy forekomster.

- [Windows Server AD FS høj tilgængelighed konfiguration](#BKMK_ADFSHighAvail): det anbefales at implementere en Windows Server AD FS-farm med mindst to servere for failover og justering af belastning. Du kan overveje at bruge Windows interne Database (arbejdsgruppe-ID'ET) til Windows Server AD FS konfigurationsdata, og brug den interne Indlæs justering af belastning funktion af Azure for at distribuere indgående anmodninger på tværs af servere i farmen.

Se [Installationsvejledningen til AD DS](https://technet.microsoft.com/library/cc753963)kan finde flere oplysninger.


### <a name="BKMK_HybridExt"></a>3. AD DS: Installere en Windows Server AD DS-aware program, der kræver forbindelse til virksomhedens netværk

![Tværs lokale Active Directory-Domænetjenester installation](media/active-directory-deploying-ws-ad-guidelines/ADDS_xprem.png)
**figur 3**

#### <a name="description"></a>Beskrivelse

En LDAP-aware programmet installeres på en Azure virtuel maskine. Det understøtter Windows-integreret godkendelse og bruger Windows Server AD DS som et lager til konfiguration og bruger profildata. Målet er for programmet udnytte eksisterende virksomhedens Windows Server AD DS og angive enkeltlogon. Programmet er ikke krav-aware. Brugere kan også have adgang til programmet direkte fra internettet. For at optimere til ydeevne og omkostninger, er det besluttet dig for, at være installeret to ekstra domæne enheder, der er en del af virksomhedens domæne sammen med programmet på Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Scenarie overvejelser, og hvordan teknologiområder gælder for dette scenario

- [Netværkstopologi](#BKMK_NetworkTopology): oprette en Azure virtuelt netværk med [tværs lokale forbindelse](../vpn-gateway/vpn-gateway-site-to-site-create.md).

- [Installationsmetode](#BKMK_InstallMethod): installere replika DCs fra virksomhedens Windows Server Active Directory-domæne. For en replika DC, kan du installere Windows Server AD DS på VM, og du kan også bruge funktionen installere fra medier (IFM) til at reducere mængden af data, der skal replikeres til den nye DC under installationen. Se [installere en Active Directory replikeringsdomænecontroller på Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)et selvstudium. Selvom du bruger IFM, kan det være mere effektivt at oprette de virtuelle DC i det lokale miljø og flytte det hele virtuelle harddisk (Virtuelle) i skyen i stedet for replikering Windows Server AD DS under installationen. Det anbefales, at du sletter den virtuelle harddisk fra det lokale netværk, når den er blevet kopieret til Azure for sikkerheden.

- [Windows Server Active Directory websted topologi](#BKMK_ADSiteTopology): oprette et nyt Azure websted i Active Directory-områder og -tjenester. Oprette en Windows Server Active Directory undernetobjektet for at repræsentere den Azure virtuelt netværk og føje undernettet til webstedet. Oprette et nyt websted link, der indeholder det nye Azure websted og det sted, hvor det Azure virtuelle netværk VPN slutpunkt er placeret for at styre og optimere Windows Server Active Directory-trafik til og fra Azure.

- [IP-adresser og DNS](#BKMK_IPAddressDNS):

 - Angiv en statisk IP-adresse til DC ved hjælp af sæt AzureStaticVNetIP Azure PowerShell-cmdlet.
 - Installere og konfigurere Windows Server DNS på domæne eller på Azure.
 - Konfigurere egenskaber for virtuelle netværk med navn og IP-adressen på den VM, der er vært rollerne DC og DNS-server.

- [Geografisk distribueret DCs](#BKMK_DistributedDCs): konfigurere yderligere virtuelle netværk efter behov. Hvis webstedet Active Directory-topologien kræver DCs i lande, der svarer til forskellige Azure områder, end du ønsker at oprette Active Directory-områder i overensstemmelse hermed.

- [Skrivebeskyttet DCs](#BKMK_RODC): Du kan installere en RODC på Azure-webstedet, alt efter behov for at udføre skrive handlinger mod DC og kompatibiliteten af programmer og -tjenester på webstedet med RODC'er. Du kan finde flere oplysninger om kompatibilitet-programmer, [skrivebeskyttet domæne enheder programmet kompatibilitet vejledning](https://technet.microsoft.com/library/cc755190).

- [Globalt katalog](#BKMK_GC): globale kataloger er nødvendige for at logon serviceanmodninger i med flere domæner områder. Hvis du ikke installerer et globalt katalog på Azure-webstedet, vil du betale udgangspunkt trafik omkostninger, da anmodninger om brugergodkendelse medføre forespørgsler globale kataloger på andre steder. Hvis du vil minimere trafikken, kan du aktivere universel gruppe medlemskab cachelagring af webstedet Azure i Active Directory-områder og -tjenester.

    Hvis du installerer et globalt katalog, konfigurere hyperlinks til websteder og websted links omkostninger, så det globale katalog på Azure-webstedet ikke er foretrukne som en kilde DC ved andre globale kataloger, der skal gentage de samme delvise domæne partitioner.

- [Placeringen af Windows Server AD DS database og SYSVOL](#BKMK_PlaceDB): føje data disken til DCs, der kører på Azure FOS for at gemme den Windows Server Active Directory-database, logfiler og SYSVOL.

- [Sikkerhedskopiere og gendanne](#BKMK_BUR): finde ud af, hvor du vil gemme system tilstand sikkerhedskopier. Hvis det er nødvendigt, kan du tilføje en anden datadisk til DC VM til lagring af sikkerhedskopier.

## <a name="deployment-decisions-and-factors"></a>Beslutninger, installation og faktorer

I denne tabel indeholder en oversigt over de Windows Server Active Directory-teknologiområder, der påvirkes i de foregående scenarier og de tilsvarende beslutninger skal du tænke på, med links til mere detaljeret nedenfor. Nogle teknologiområder muligvis ikke gælder for hver installation scenarie, og nogle teknologiområder kan være flere kritiske til et scenarie med installation end andre teknologiområder.

Eksempelvis hvis du installerer en replika DC i et virtuelt netværk og dit område er kun en enkelt domæne, kan derefter vælge at implementere en globalt katalog-server i så fald ikke kritiske til installation scenariet fordi den ikke opretter eventuelle yderligere gentagelse krav. På den anden side Hvis området har flere domæner, derefter beslutningen om at installere et globalt katalog i et virtuelt netværk, kan det påvirke tilgængelige båndbredde, ydeevne, godkendelse, directory opslag osv.

| Windows Server Active Directory-teknologi område | Beslutninger | Faktorer |
| ---- | ---- | ---- |
| [Netværkstopologi](#BKMK_NetworkTopology) | Opretter et virtuelt netværk? | <li>Krav til at få adgang til Corp ressourcer</li> <li>Godkendelse</li> <li>Kontoadministration</li> |
| [DC installationskonfiguration](#BKMK_DeploymentConfig) | <li>Installere et separat område uden en hvilken som helst tillidsforhold?</li> <li>Installere en ny skov med sammenslutning?</li> <li>Installere en ny skov Windows Server Active Directory skov tillid til eller Kerberos?</li> <li>Udvide Corp område ved at installere en replika DC?</li> <li>Udvide Corp område ved at installere en ny underordnet domæne eller domænetræet?</li> | <li>Sikkerhed</li> <li>Overholdelse af angivne standarder</li> <li>Omkostninger</li> <li>Fleksibilitet og fejltolerance</li> <li>Programmet kompatibilitet</li> |
| [Topologi for Windows Server Active Directory-websted](#BKMK_ADSiteTopology) | Hvordan kan du konfigurere undernet, websteder og hyperlinks til websteder med Azure virtuelt netværk til at optimere trafik og minimere omkostninger? | <li>Definitioner af undernet og websted</li> <li>Egenskaber for dataforbindelse-webstedet og besked om ændringer</li> <li>Replikering komprimering</li> |
| [IP-adresser og DNS](#BKMK_IPAddressDNS) | Sådan konfigureres IP-adresser og navneoversættelse? | <li>Brug cmdlet'en Brug sæt-AzureStaticVNetIP til at tildele en statisk IP-adresse</li> <li>Installere Windows Server DNS server og konfigurere egenskaberne virtuelt netværk med navn og IP-adressen på den VM, der er vært rollerne DC og DNS-server</li> |
| [Geografisk distribueret DCs](#BKMK_DistributedDCs) | Hvordan kan replikeres til DCs på separate virtuelle netværk? | Hvis webstedet Active Directory-topologien kræver DCs i lande, der svarer til forskellige Azure områder, end du ønsker at oprette Active Directory-områder i overensstemmelse hermed. [Konfigurer virtuelt netværk til virtuelle netværk forbindelse](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) , der skal mellem domænecontrollere på separate virtuelle netværk. |
| [Skrivebeskyttet DCs](#BKMK_RODC) | Bruge skrivebeskyttet eller skrivbar DCs? | <li>Filtrere HBI/PII attributter</li> <li>Filtrere hemmeligheder</li> <li>Grænse for udgående trafik</li> |
| [Globalt katalog](#BKMK_GC) | Installere globalt katalog? | <li>Gøre alle globale kataloger DCs for enkelt domæner</li> <li>For med flere domæner skov der globale kataloger kræves til godkendelse</li> |
| [Installationsmetode](#BKMK_InstallMethod) | Sådan installeres DC i Azure? | Enten: <li>Installere Active Directory-Domænetjenester ved hjælp af Windows PowerShell eller Dcpromo</li> <li>Flytte Virtuelle af en lokal virtuelle DC</li> |
| [Placeringen af Windows Server AD DS database og SYSVOL](#BKMK_PlaceDB) | Lagringssted til Windows Server AD DS database, logfiler og SYSVOL? | Ændre Dcpromo.exe standardværdier. Disse kritiske Active Directory-filer *skal* placeres på Azure datadisce i stedet for operativsystem diske, der implementerer cachelagring. |
| [Sikkerhedskopiering og gendannelse](#BKMK_BUR) | Sådan beskytter og Gendan data? | Oprette system tilstand sikkerhedskopier |
| [Sammenslutning serverkonfiguration](#BKMK_FedSrvConfig) | <li>Installere en ny skov med sammenslutning i skyen?</li> <li>Installere AD FS lokalt og få vist en proxy i skyen?</li> | <li>Sikkerhed</li> <li>Overholdelse af angivne standarder</li> <li>Omkostninger</li> <li>Adgang til programmer ved forretningsforbindelser</li> |
| [Konfiguration af Cloud services](#BKMK_CloudSvcConfig) | En skybaseret tjeneste er implicit installeres første gang du opretter en virtuel maskine. Har du brug at installere yderligere skytjenester? | <li>En VM eller FOS kræver direkte visning til internettet?</li> <li> Kræver tjenesten justering af belastning?</li> |
| [Sammenslutning serverkrav til offentlige og private IP-Adresser, der adresserer (dynamisk IP kontra virtuelle IP-adresse)](#BKMK_FedReqVIPDIP) | <li>Skal forekomsten af Windows Server AD FS nås direkte fra internettet?</li> <li>Kræver det program, der installeres i skyen sin egen internettet IP-adresse og port?</li> | Oprette en skytjeneste for hver virtuelle IP-adresse, der kræves af din installation |
| [Konfiguration af Windows Server AD FS høj tilgængelighed](#BKMK_ADFSHighAvail) | <li>Antallet af knuder på min Windows Server AD FS-serverfarm?</li> <li>Hvor mange noder til at installere på min Windows Server AD FS-proxy farm?</li> | Fleksibilitet og fejltolerance |

### <a name="BKMK_NetworkTopology"></a>Netværkstopologi

For at overholde de IP-adresse konsistens og DNS-kravene i Windows Server Active Directory-Domænetjenester, er det nødvendigt at først oprette en [Azure virtuelt netværk](../virtual-network/virtual-networks-overview.md) og vedhæfte din virtuelle maskiner til den. Når oprettelsen, skal du beslutte, om du vil udvide eventuelt forbindelse til lokale virksomhedens netværk, som transparent forbinder Azure virtuelle maskiner med lokale computere – Dette opnås ved hjælp af traditionelle VPN-teknologier og kræver, at en VPN-slutpunkt skal vises på kanten af virksomhedens netværk. Det vil sige, startes VPN-Forbindelsen fra Azure til virksomhedens netværk, ikke omvendt.

Bemærk, at yderligere gebyrer anvende, når du udvider et virtuelt netværk til dit lokale netværk ud over de almindelige gebyrer, der gælder for hver VM. Der er specielt gebyrer, CPU-tid for gateway Azure virtuelt netværk og udgangspunkt trafikken genereres af hver VM, der kommunikerer med lokale computere på tværs af VPN-Forbindelsen. Se [Azure priser på et hurtigt](http://azure.microsoft.com/pricing/)kan finde flere oplysninger om netværk trafik gebyrer.

### <a name="BKMK_DeploymentConfig"></a>DC installationskonfiguration

Den måde, du konfigurerer DC afhænger af kravene til tjenesten, du vil køre på Azure. For eksempel kan du installere en ny skov, isolerede fra din egen virksomhedens skov til test en test af koncept, et nyt program eller nogle andre kort sigt projekt, der kræver katalogtjenester, men ikke specifikke adgang til interne virksomhedsressourcer.

Som en fordel, en isolerede skov DC ikke replikeres med lokale DCs, hvilket resulterer i mindre udgående netværkstrafik, der genereres af systemet sig selv direkte reducere omkostninger. Se [Azure priser på et hurtigt](http://azure.microsoft.com/pricing/)kan finde flere oplysninger om netværk trafik gebyrer.

Som et andet eksempel Antag, at du har krav til beskyttelse af personlige oplysninger for en tjeneste, men tjenesten afhænger af adgang til din interne Windows Server Active Directory. Hvis du har tilladelse til host data for tjenesten i skyen, kan du installere et nyt underordnet domæne til din interne skov på Azure. I dette tilfælde kan du installere en DC til det nye underordnede domæne (uden globalt katalog for at hjælpe adresse beskyttelse af personlige oplysninger problemstillinger). Dette scenarie skal sammen med en replika DC installation kræver et virtuelt netværk for forbindelsen til din lokale DCs.

Hvis du opretter en ny skov, skal du vælge, om at bruge [Active Directory har tillid til](https://technet.microsoft.com/library/cc771397) , eller [har tillid til sammenslutning](https://technet.microsoft.com/library/dd807036). Saldo kravene bestemmes af kompatibilitet, sikkerhed, overholdelse af regler, omkostninger og fleksibilitet. For eksempel for at kunne udnytte [selektiv godkendelse](https://technet.microsoft.com/library/cc755844) kan du installere en ny skov på Azure og oprette en Windows Server Active Directory tillid mellem den lokale skov og området, skyen. Hvis programmet er krav opmærksom på, men kan du installere sammenslutning tillidsforhold i stedet for tillidsforhold fra Active Directory-område. En anden faktor bliver omkostninger til enten gentage flere data ved at forlænge din lokale Windows Server Active Directory i skyen eller oprette mere udgående trafik som et resultat af godkendelses- og indlæsning af forespørgsler.

Krav til tilgængelighed og fejltolerance påvirker også efter eget valg. Eksempelvis hvis linket afbrydes, programmer, som anvender en Kerberos sikkerhed og rettighedsadministration eller en sammenslutning sikkerhed og rettighedsadministration er alle sandsynligt helt ødelagt medmindre du har installeret tilstrækkelige infrastruktur på Azure. Alternativ installationskonfigurationer som replika DCs (skrivbar eller RODC'er) øge sandsynligheden for at kunne acceptere link afbrydelser.

### <a name="BKMK_ADSiteTopology"></a>Topologi for Windows Server Active Directory-websted

Du skal definere korrekt websteder og hyperlinks til websteder for at optimere trafik og minimere omkostninger. Websteder, hyperlinks til websteder og undernet påvirker topologi for replikering mellem DCs og strømmen af godkendelsestrafik. Overvej følgende trafik omkostninger og installere og konfigurere DCs ifølge kravene i scenariet installation:

- Der findes et nominel gebyr i timen for gatewayen selve:

 - Den kan startes og stoppes efter behov

 - Hvis stoppet, er Azure FOS adskilt fra virksomhedens netværk

- Indgående trafik er gratis

- Udgående trafik faktureres ifølge [Azure priser på et hurtigt](http://azure.microsoft.com/pricing/). Du kan optimere webstedsegenskaber for dataforbindelse mellem lokale websteder og skyen på følgende måde:

 - Hvis du bruger flere virtuelle netværk, konfigurere websted-links og deres omkostninger korrekt for at forhindre, at Windows Server AD DS prioritering webstedet Azure over et, der kan overføre det samme antal tjenesten gratis. Du kan også overveje at deaktivere bro alle webstedets indstillingen kæde (BASL) (som er aktiveret som standard). Dette sikrer, at kun direkte tilsluttet websteder replikeres med hinanden. DCs i transitive forbundne websteder er ikke længere kan replikeres direkte med hinanden, men skal gentage via et fælles websted eller websteder. Hvis mellemliggende webstederne utilgængelige eller anden grund, sker replikering mellem DCs i transitive forbundne websteder ikke selvom forbindelsen mellem webstederne er tilgængelig. Til sidst, hvor sektioner af transitive gentagelse funktionsmåde forbliver hensigtsmæssigt, oprette websted link broer, der indeholder de relevante hyperlinks og websteder, som lokalt, virksomhedens netværk websteder.

 - [Konfigurer websted link omkostninger](https://technet.microsoft.com/library/cc794882) korrekt for at undgå utilsigtede trafik. Eksempelvis hvis **Prøve næste nærmeste websted** indstilling er aktiveret, skal du kontrollere virtuelt netværk websteder ikke er næste tættest ved at øge de omkostninger, der er knyttet for objektet websted-link, der forbinder webstedet Azure tilbage til virksomhedens netværk.

 - Konfigurere websted link [intervaller](https://technet.microsoft.com/library/cc794878) og [tidsplaner](https://technet.microsoft.com/library/cc816906) afhængigt af konsistens krav og rente af objektet ændres. Justere gentagelse tidsplan med ventetid tolerance. DCs replikeres kun den sidste tilstand for en værdi, så reducere gentagelse intervallet kan spare udgifter, hvis der er et tilstrækkelige objekt ændre rente.

- Hvis minimering omkostninger er en prioritet, sikre replikering er planlagt, og Skift meddelelse ikke er aktiveret. Dette er standardkonfigurationen, når replikering mellem forskellige websteder. Dette er ikke vigtigt Hvis du anvender en RODC i et virtuelt netværk fordi RODC ikke replikeres eventuelle ændringer, der er udgående. Men hvis du installerer en skrivbar DC, skal du sikre dig hyperlinket til webstedet ikke er konfigureret til at gentage opdateringer med unødvendige frekvens. Hvis du installerer en globalt katalog-server (globalt katalog), skal du kontrollere, at alle andre websted, der indeholder et globalt katalog kopieres domæne partitioner fra en kilde DC på et websted, der er forbundet med en linket gruppe eller hyperlinks, der har en lavere pris end det globale katalog på Azure-webstedet.


- Det er muligt at reducere yderligere stadig netværkstrafik, der genereres af replikering mellem websteder ved at ændre algoritmen gentagelse komprimering. Algoritmen komprimering styres af algoritmen REG_DWORD registreringsdatabasen posten HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Replicator komprimering. Standardværdien er 3, som svarer til algoritmen Xpress Komprimer. Du kan ændre værdien til 2, der ændres algoritmen til MSZip. Dette vil øge komprimeringen i de fleste tilfælde, men det sker for regning CPU-forbrug. Se, [hvordan Active Directory topologi for replikering fungerer](https://technet.microsoft.com/library/cc755994)kan finde flere oplysninger.

### <a name="BKMK_IPAddressDNS"></a>IP-adresser og DNS

Azure virtuelle maskiner fordeles "lejer af DHCP adresser" som standard. Fordi Azure virtuelt netværk dynamiske adresser fortsætter med en virtuel maskine til levetiden for den virtuelle maskine, er Windows Server AD DS krav opfyldt.

Som et resultat, når du bruger en dynamisk adresse på Azure, er du i effekt ved hjælp af en statisk IP-adresse, fordi det er distribueret for lånet periode, og tidsrum lease er lig med levetiden for skytjenesten.

Dynamisk adresse er dog deallokeres, hvis VM er lukket. Hvis du vil forhindre, at IP-adressen der deallokeres, kan du [bruge Set-AzureStaticVNetIP for at tildele en statisk IP-adresse](http://social.technet.microsoft.com/wiki/contents/articles/23447.how-to-assign-a-private-static-ip-to-an-azure-vm.aspx).

Til navneoversættelse, du installerer din egen (eller udnytter dit eksisterende) DNS-serverinfrastruktur Azure-producenter DNS opfylder ikke avancerede navn opløsning behovene i Windows Server AD DS. For eksempel det ikke understøtter dynamiske SRV-poster, og så videre. Navneoversættelse er en kritiske konfigurationselement til DCs og klienter, der er medlem af et domæne. DCs skal kunne registrere ressourceposter og løse andre DC ressourceposter.
Fejl tolerance og ydeevne årsager er det velegnet til at installere Windows Server DNS-tjenesten på DCs kører på Azure. Derefter konfigurere egenskaberne Azure virtuelt netværk med navn og IP-adressen på DNS-serveren. Når andre FOS på det virtuelle netværk starter, konfigureres deres DNS resolver klientindstillinger med DNS-server som en del af dynamisk IP-adresse fordeling.

> [AZURE.NOTE] Du kan ikke deltage i det lokale miljø computere til et Windows Server AD DS Active Directory-domæne, der er placeret på Azure direkte via internettet. Portkravene til Active Directory og handlingen domæne join gengive den upraktisk at direkte vise de nødvendige porte og i kraft, en hel DC til internettet.

FOS registrere deres DNS-navn automatisk ved start, eller når der er en Navneændring af.

Se [installere en ny Active Directory-område på Microsoft Azure](active-directory-new-forest-virtual-machine.md)kan finde flere oplysninger om dette eksempel og et andet eksempel, der viser, hvordan du klargør den første VM og installere Active Directory-Domænetjenester på den. Du kan finde flere oplysninger om brug af Windows PowerShell, skal du se [Installere Azure PowerShell](../powershell-install-configure.md) og [Cmdletter til administration af Azure](https://msdn.microsoft.com/library/azure/jj152841).

### <a name="BKMK_DistributedDCs"></a>Geografisk distribueret DCs

Azure indeholder fordele, når vært for flere DCs på forskellige virtuelle netværk:

- Flere websted fejltolerance

- Fysisk afstand til gren kontorer (ventetid på nederste)

Du kan finde oplysninger om at konfigurere direkte kommunikation mellem virtuelle netværk [Konfigurer virtuelt netværk til virtuelle netværksforbindelse](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="BKMK_RODC"></a>Skrivebeskyttet DCs

Du har brug at vælge, om du vil installere skrivebeskyttet eller skrivbar DCs. Du kan hælde vinkel til at udrulle RODC'er, fordi du ikke vil have fysisk kontrol over dem, men RODC'er er designet til at være installeret på placeringer, hvor deres fysisk sikkerhed er risiko, som gren kontorer.

Azure præsentere ikke fysisk sikkerhedsrisikoen af en gren office, men kan stadig bevise RODC'er skal være mere rentable, fordi de funktioner, de giver er velegnet til disse miljøer selvom for meget forskellige årsager. For eksempel RODC'er har ingen udgående gentagelse og er i stand til at udfylde selektivt hemmeligheder (adgangskoder). Ulempen, manglen disse hemmeligheder kan det være nødvendigt efter behov udgående trafik til at validere dem som en bruger eller computer godkender. Men hemmeligheder kan selektivt på forhånd og cachelagret.

RODC'er giver en ekstra fordel i og omkring HBI og PII problemstillinger, fordi du kan føje attributter, der indeholder følsomme oplysninger RODC filtreret attributsæt (FORS). FORS er et brugerdefinerbare sæt af egenskaber, der ikke replikeres til RODC'er. Du kan bruge FORS som en beskytter, hvis du ikke er tilladt eller ikke vil gemme PII eller HBI på Azure. Du kan finde flere oplysninger, se [RODC filtrerede attribut angive [(https://technet.microsoft.com/library/cc753459)].

Sørg for, at programmer ikke kompatibelt med RODC'er, du vil bruge. Mange Windows Server Active Directory-aktiverede programmer fungerer fint med RODC'er, men nogle programmer kan udføre ineffektiv eller mislykkes, hvis de ikke har adgang til en skrivbar DC. Du kan finde yderligere oplysninger finder [og skrivebeskyttet DCs programmet kompatibilitet vejledning](https://technet.microsoft.com/library/cc755190).

### <a name="BKMK_GC"></a>Globalt katalog

Du har brug at vælge, om at installere en global katalog. I en enkelt domæner, skal du konfigurere alle DCs som globalt katalog-servere. Det, ikke øger omkostninger, fordi der skal knyttes nogen ekstra gentagelse trafik.

Globale kataloger er nødvendige for at udvide Universal gruppemedlemskaber under godkendelsesprocessen i et med flere domæner område. Hvis du ikke installerer et globalt katalog, generere arbejdsbelastningen på det virtuelle netværk, der godkender mod et DC på Azure indirekte udgående godkendelsestrafik Hvis du vil forespørge globale kataloger lokalt under hvert forsøg på godkendelse.

De omkostninger, der er knyttet til globale kataloger er mindre forudsigelige, fordi de være vært for hver domæne (i del). Hvis arbejdsbelastningen vært for en forbindelse til internettet tjeneste og godkender brugere mod Windows Server AD DS, kan omkostningerne, der kan være helt uventede. For at reducere globalt katalog forespørgsler uden for webstedet skyen under godkendelse, kan du [aktivere Universal medlemskab cachelagring af grupper](https://technet.microsoft.com/library/cc816928).

### <a name="BKMK_InstallMethod"></a>Installationsmetode

Du har brug at vælge, hvordan du installerer DCs på det virtuelle netværk:

- Hæve nye DCs. Yderligere oplysninger finder du se [installere en ny Active Directory-område i en Azure virtuelt netværk](active-directory-new-forest-virtual-machine.md).

- Flytte den virtuelle harddisk af en lokal virtuelle DC i skyen. I dette tilfælde skal du sikre dig, at lokale virtuelle DC "flyttes," ikke "kopierede" eller "klonede."

Brug kun Azure virtuelle maskiner for DCs (i modsætning til Azure "web" eller "arbejder" rolle VM'er). De er pålidelig og holdbarheden af tilstanden for et DC er påkrævet. Azure virtuelle maskiner er udviklet til arbejdsmængder som DCs.

Brug ikke SYSPREP til at installere eller klone DCs. Muligheden for at klone DCs er kun tilgængelige begyndelsen i Windows Server 2012. Funktionen kloningssoftware kræver understøttelse VMGenerationID i den underliggende hypervisor. Hyper-V i Windows Server 2012 og Azure virtuelle netværk begge understøtter VMGenerationID, ligesom fra tredjepart virtualization softwareleverandører.

### <a name="BKMK_PlaceDB"></a>Placeringen af Windows Server AD DS database og SYSVOL

Vælg, hvor du vil placere Windows Server AD DS database og logge på SYSVOL. De skal installeres på Azure-Data diske.

> [AZURE.NOTE] Azure datadisce er begrænset til 1 TB.

Data diskdrev gøre ikke cache skriver som standard. Data diskdrev, der er knyttet til en VM bruger Skriv gennem cachelagring. Skrivningen er cachelagring gør sikker med anvendt til robust Azure-lager, før posteringen er færdig fra perspektivet, hvor VM-operativsystemet. Den indeholder holdbarhed, fra en smule langsommere skriver.

Dette er vigtig for Windows Server AD DS, fordi disk cachelagring af skrivning i baggrunden ugyldiggør antagelser af DC. Windows Server AD DS forsøger at deaktivere cachelagring, men det er op til disken EY system imødekomme den. Fejl ved at deaktivere cachelagring resultere under visse omstændigheder USN rollback, hvilket resulterer i hængende objekter og andre problemer.

Som en bedste fremgangsmåde for virtuelle DCs du gøre følgende:

- Angiv indstillingen Host Cache foretrukket på disken Azure data for ingen. Dette forhindrer problemer med cachelagring for AD DS handlinger.

- Gemme den database, logfiler og SYSVOL på enten samme datadisk eller separate datadisce. Dette er typisk en separat disk fra disken bruges til selve operativsystemet. De vigtigste takeaway er, at Windows Server AD DS database og SYSVOL, ikke skal være gemt på en Azure operativsystem disk type. Som standard installeres installationsprocessen AD DS disse komponenter i mappen % systemroot %, hvilket ikke anbefales til Azure.

### <a name="BKMK_BUR"></a>Sikkerhedskopiering og gendannelse

Vær opmærksom på Hvad er og understøttes ikke for sikkerhedskopiering og gendannelse af et DC Generelt, og mere specifikt dem, der kører i et VM. Se [sikkerhedskopierings- og virtuelt DCs Gendan overvejelser i forbindelse med](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv#backup_and_restore_considerations_for_virtualized_domain_controllers).

Opret system tilstand sikkerhedskopier ved hjælp af sikkerhedskopiering software, der er specielt opmærksom på sikkerhedskopien krav til Windows Server AD DS, som Windows Server sikkerhedskopiering.

Undlad at kopiere eller klone Virtuelle filer af DCs i stedet for at udføre regelmæssige sikkerhedskopier. En gendannelse skal overhovedet kræves, gør det ved hjælp af klonede eller kopierede virtuelle harddiske uden Windows Server 2012 og en understøttede hypervisor introducerer USN bobler.

### <a name="BKMK_FedSrvConfig"></a>Sammenslutning serverkonfiguration

Konfiguration af Windows Server AD FS-sammenslutningsservere (STSs) afhænger af i en del om de programmer, du vil installere på Azure har brug at få adgang til ressourcer på dit lokale netværk.

Hvis programmerne, der opfylder følgende kriterier, kan du installere programmerne på isolationsniveauet fra dit lokale netværk.

- De accepterer SAML sikkerhedstokens

- De er exposable til internettet

- De ikke har adgang til lokale ressourcer

I dette tilfælde konfigurere Windows Server AD FS STSs på følgende måde:

1. Konfigurere en isolerede område med enkelt domæne på Azure.

2. Giv samlet adgang til området ved at konfigurere en Windows Server AD FS sammenslutning serverfarm.

3. Konfigurere Windows Server AD FS (sammenslutning serverfarm og sammenslutning serverfarm proxy) i det lokale område.

4. Oprette et organisationsnetværk tillidsforhold mellem lokale og Azure forekomster af Windows Server AD FS.

På den anden side Hvis programmerne kræver adgang til lokale ressourcer, kan du konfigurere Windows Server AD FS med programmet på Azure på følgende måde:

1. Konfigurere forbindelsen mellem lokale netværk og Azure.

2. Konfigurere en Windows Server AD FS sammenslutning serverfarm i det lokale område.

3. Konfigurere en Windows Server AD FS sammenslutning serverfarm proxy på Azure.

Denne konfiguration har fordelen ved at reducere visning af lokale ressourcer, der svarer til konfiguration af Windows Server AD FS med programmer i et afskærmede netværk.

Bemærk, i et scenarie, man kan etablere tillidsforhold relationer med flere identitetsudbydere, hvis det er nødvendigt business to business samarbejde.

### <a name="BKMK_CloudSvcConfig"></a>Konfiguration af Cloud services

Skytjenester er nødvendigt, hvis du vil vise en VM direkte til internettet eller til at vise et internettet NLB-program. Dette er muligt, fordi hver skybaseret tjeneste giver adgang til en enkelt konfigurerbar virtuelle IP-adresse.

### <a name="BKMK_FedReqVIPDIP"></a>Sammenslutning serverkrav til offentlige og private IP-Adresser, der adresserer (dynamisk IP kontra virtuelle IP-adresse)

Hver Azure virtuelt modtager en dynamisk IP-adresse. En dynamisk IP-adresse er en privat adresse, der er tilgængelige kun inden for Azure. I de fleste tilfælde, men bliver det nødvendigt at konfigurere en virtuel IP-adresse til din Windows Server AD FS-installationer. Den virtuelle IP-adresse er nødvendig for at få vist Windows Server AD FS slutpunkter til internettet, og der skal bruges af organisationsnetværket partnere og kunder til godkendelse og løbende administration. En virtuel IP-adresse er en egenskab for en skybaseret tjeneste, som indeholder en eller flere Azure virtuelle computere. Hvis krav-aware programmet installeret på Azure og Windows Server AD FS er både internettet og del almindelige porte, hver kræver et virtuelt IP-adressen på et nyt, og det vil derfor være nødvendigt at oprette en skybaseret tjeneste for programmet og et andet til Windows Server AD FS.

Yderligere oplysninger om definitioner af betingelserne se virtuelle IP-adresse og dynamiske IP-adresse, [begreber og definitioner](#BKMK_Glossary).

### <a name="BKMK_ADFSHighAvail"></a>Konfiguration af Windows Server AD FS høj tilgængelighed

Selvom det er muligt at installere enkeltstående Windows Server AD FS federation services, anbefales det at installere en farm med mindst to noder til både AD FS STS og proxyer for fremstilling miljøer.

Se [AD FS 2.0 installation topologi overvejelser](https://technet.microsoft.com/library/gg982489) i den [AD FS 2.0 Design vejledning](https://technet.microsoft.com/library/dd807036) beslutte, hvilke installation konfigurationsindstillinger, der er bedst passer til dine specifikke behov.

> [AZURE.NOTE] Hvis du vil have justering af belastning for Windows Server AD FS slutpunkterne på Azure, konfigurere alle medlemmer af Windows Server AD FS-farm i den samme skybaseret tjeneste, og brug Indlæs justering af belastning findes i Azure til HTTP (standard 80) og HTTPS-porte (standard 443). Du kan finde yderligere oplysninger finder [Azure belastningsjustering efterprøvning af af](https://msdn.microsoft.com/library/azure/jj151530).
Windows Server netværk Indlæs netværksaflastningsfunktionen (NA) understøttes ikke på Azure.
