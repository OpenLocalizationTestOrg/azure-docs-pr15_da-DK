<properties
    pageTitle="Azure AD Connect sundhed ofte stillede spørgsmål"
    description="Disse ofte stillede spørgsmål finder du svar på spørgsmål om Azure AD forbinde tilstand. Disse ofte stillede spørgsmål omhandler spørgsmål om brug af tjenesten, herunder fakturering model, muligheder, begrænsninger og support."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>


# <a name="azure-ad-connect-health-frequently-asked-questions-faq"></a>Azure AD Connect sundhed ofte stillede spørgsmål (FAQ)

Disse ofte stillede spørgsmål finder du svar på spørgsmål om Azure AD forbinde tilstand. Disse ofte stillede spørgsmål omhandler spørgsmål om brug af tjenesten, herunder fakturering model, muligheder, begrænsninger og support.

## <a name="general-questions"></a>Generelle spørgsmål



**Sp: jeg håndtere flere Azure AD-mapper. Hvordan skifter jeg mellem til den med Azure Active Directory Premium?**

Du kan skifte mellem forskellige Azure AD-lejere ved at vælge den aktuelt signeret brugernavn i øverste højre hjørne og vælge den relevante konto. Hvis kontoen ikke vises her, kan du vælge Log af og derefter bruge global administrator legitimationsoplysningerne for den mappe, der har Azure Active Directory Premium er aktiveret til at logge på.

## <a name="installation-questions"></a>Spørgsmål om installationen



**Sp: Hvordan påvirker det for at installere den Azure AD forbinde Health Agent på individuelle servere?**

Hvordan installerer Microsoft Azure AD forbinde sundhed supportmedarbejdere ADFS, Web Application proxyservere, Azure AD-forbindelse (sycn)-servere domænecontrollere er minimale i forhold CPU, hukommelse forbrug netværksbåndbredde og lager.

Nedenstående tal er en tilnærmelse.

- CPU-forbrug: ~ 1% stigning
- Hukommelsesforbrug: op til 10% af total systemets hukommelse

>[AZURE.NOTE] I tilfælde af tjenesten agent, der ikke kan kommunikere til Azure, gemmer agenten dataene lokalt, op til et defineret maksimumgrænsen. Agenten overskriver de "cachelagrede" data på grundlag af "mindst senest repareret".

- Lokale bufferen lagerplads til Azure AD forbinde sundhed supportmedarbejdere: ~ 20 MB
- Det anbefales, at du klargøre en meget diskplads om 1024 MB (1 GB) til AD FS overvåge kanalen til Azure AD forbinde sundhed supportmedarbejdere at behandle alle overvågningsdataene, før den er overskrevet for AD FS-servere.

**Sp: kan jeg anvende for at genstarte min servere under installationen af den Azure AD forbinde sundhed supportmedarbejdere?**

Nej. Installationen af supportmedarbejdere kræver ikke, at du at genstarte serveren. Installation af nogle af de nødvendige trin kan dog kræver en genstart af serveren.

For eksempel på Windows Server 2008 R2 installationen af .net kræver 4,5 Framework en genstart af serveren.


**Sp: betyder Azure AD forbinde Health Services arbejde via en pass-through-http-proxy?**

Ja.  Under gå handlinger, kan du konfigurere Health Agent for at videresende udgående HTTP-anmodninger ved hjælp af en HTTP-Proxy for. Du kan finde flere oplysninger, se [konfigurere Azure AD forbinde sundhed konsulenter til at bruge HTTP-Proxy.](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)
Hvis du vil konfigurere en proxy under Agent registrering skal du muligvis ændre indstillingerne for Internet Explorer Proxy på forhånd.
1. Åbn Internet Explorer -> Indstillinger for Internet -> Indstillinger -> forbindelser LAN-indstillinger ->.
2. Vælg Brug en proxyserver til LAN.
3. Vælg Avanceret, hvis du har forskellige proxy porte til HTTP og HTTPS/Secure.

**Sp: Azure AD forbinde Health Services understøtter basisgodkendelse ved tilslutning til http-proxyer?**

Nej. En metode til at angive vilkårlig brugernavn og adgangskode til basisgodkendelse understøttes ikke i øjeblikket.


**Sp: hvilken version af Active Directory-Domænetjenester understøttes af Azure AD forbinde tilstand til AD DS?**

Overvågning af Active Directory-Domænetjenester understøttes, mens installeret på følgende OS versioner:

- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

## <a name="operations-questions"></a>Handlinger spørgsmål



**Sp: har jeg brug for at aktivere overvågning på proxyservere min AD FS-programmet på computeren eller proxyservere min Web-program?**

Nej, overvågning behøver ikke at være aktiveret på Web Application Proxy (WAP)-servere. Aktivere den på AD FS-servere.


**Sp: hvordan Azure AD forbinde sundhed beskeder få løst?**

Azure AD forbinde sundhed beskeder få løst på en succes betingelse. Azure AD forbinde sundhed supportmedarbejdere registrere og rapportere succes betingelser til tjenesten med jævne mellemrum. For nogle beskeder er de tidsbaserede. Hvis den samme fejltilstand ikke er opfyldt inden for 72 timer fra generering af påmindelser, løst beskeden med andre ord automatisk.




**Sp: hvilke Firewallportene skal jeg åbne til den Azure AD forbinde Health Agent til at fungere sammen?**

Du skal have TCP/UDP-porte 443 og 5671 åben for den Azure AD forbinde Health Agent lov til at kommunikere med Azure AD sundhed service slutpunkter.


**Sp: Hvorfor kan jeg se to servere med det samme navn på Azure AD forbinde sundhed portalen?**

Når du fjerner en agent fra en server, fjernes serveren ikke automatisk fra Azure AD forbinde Portal.  Hvis du manuelt en agent er fjernet fra en server eller fjernet selve serveren, skal du manuelt slette posten server fra portalen Azure AD forbinde tilstand. Gå til for at få mere at vide [slette en server eller tjeneste forekomst.](active-directory-aadconnect-health-operations.md#delete-a-server-or-service-instance)

Hvis reimaged en server eller har oprettet en ny server med de samme oplysninger (såsom computernavn), kan du ikke har fjernet den server, der allerede er registreret fra portalen Azure AD forbinde sundhed installeret på agent på den nye server, kan du se to elementer med samme navn.  
I dette tilfælde skal du slette den post, der hører til det ældre server manuelt. Dataene for denne server skal være forældet.

## <a name="related-links"></a>Relaterede links

* [Azure AD Connect tilstand](active-directory-aadconnect-health.md)
* [Azure AD Connect sundhed Agent-Installation](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect sundhed handlinger](active-directory-aadconnect-health-operations.md)
* [Brug af Azure AD forbinde tilstand med AD FS](active-directory-aadconnect-health-adfs.md)
* [Ved hjælp af Azure AD forbinde tilstand til synkronisering](active-directory-aadconnect-health-sync.md)
* [Brug af Azure AD forbinde tilstand med AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect sundhed versionshistorik](active-directory-aadconnect-health-version-history.md)
