<properties
    pageTitle="Ofte stillede spørgsmål - Azure Active Directory-domænetjenester | Microsoft Azure"
    description="Ofte stillede spørgsmål om Azure Active Directory Domain Services"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="maheshu"/>

# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Azure Active Directory-domænetjenester: Ofte stillede spørgsmål (ofte stillede spørgsmål)

Denne side finder du svar på ofte stillede spørgsmål om Azure Active Directory Domain Services. Holde søge tilbage efter opdateringer.

### <a name="troubleshooting-guide"></a>Vejledning til fejlfinding
Se vores [fejlfinding vejledning](active-directory-ds-troubleshooting.md) til løsninger på almindelige problemer, der er stødt på, når du konfigurerer eller administrere Azure AD-domænetjenester.


### <a name="configuration"></a>Konfiguration

#### <a name="can-i-create-multiple-domains-for-a-single-azure-ad-directory"></a>Kan jeg oprette flere domæner til en enkelt Azure AD-mappe?
Nej. Du kan kun oprette et enkelt domæne behandles af Azure AD-domænetjenester for en enkelt Azure AD-mappe.  

#### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Kan jeg aktivere Azure AD-domænetjenester i et virtuelt Azure ressourcestyring-netværk?
Nej. Azure AD-domænetjenester kan kun aktiveres i et klassisk Azure virtuelt netværk. Du kan oprette forbindelse klassisk virtuelt netværk til et ressourcestyring virtuelt netværk ved hjælp af virtuelt netværk peering for at bruge domænet administrerede i et virtuelt netværk ressourcestyring.

#### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Kan jeg gøre Azure AD-domænetjenester tilgængelige i flere virtuelle netværk i mit abonnement?
Tjenesten selve understøtter ikke dette scenario direkte. Azure AD-domænetjenester er tilgængelig i kun én virtuelt netværk ad gangen. Du kan dog konfigurere forbindelsen mellem flere virtuelle netværk til at fremvise Azure AD-domænetjenester til andre virtuelle netværk. I denne artikel beskrives, hvordan du kan [oprette forbindelse virtuelle netværk i Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

#### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Kan jeg aktivere Azure AD-domænetjenester ved hjælp af PowerShell?
PowerShell/automatiserede udrulning af Azure AD-domænetjenester er ikke tilgængelig i øjeblikket.

#### <a name="is-azure-ad-domain-services-available-in-the-new-azure-portal"></a>Er Azure AD-domænetjenester tilgængelig i den nye Azure portal?
Nej. Azure AD-domænetjenester kan konfigureres kun i [Azure klassisk portal](https://manage.windowsazure.com). Vi forventer, at udvide understøttelsen af [Azure portal](https://portal.azure.com) i fremtiden.

#### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Kan jeg tilføje domæne enheder til en administreret Azure AD Domain Services-domæne?
Nej. Det domæne, der leveres af Azure AD-domænetjenester er et administreret domæne. Du ikke behøver at klargøre, konfigurere eller på anden måde håndtere domænecontrollere for dette domæne - er disse management aktiviteter angivet som en tjeneste af Microsoft. Du kan derfor ikke føje ekstra domæne enheder (læse-og skriveadgang eller skrivebeskyttet) til administrerede domænet.

### <a name="administration-and-operations"></a>Administration og drift

#### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Kan jeg oprette forbindelse til domænecontrolleren for min administrerede domæne ved hjælp af Fjernskrivebord?
Nej. Du har ikke tilladelse til at oprette forbindelse til domænecontrollere for den administrerede domæne via Fjernskrivebord. Medlemmer af gruppen 'AAD DC administratorer' kan administrere administrerede domænet ved hjælp af AD administrationsværktøjer som Active Directory Administration Center (ADAC) eller AD PowerShell. Disse funktioner er installeret ved hjælp af funktionen 'Administrationsværktøjer Remote Server' på en Windows-server, der er knyttet til administrerede domænet.

#### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Jeg har aktiveret Azure AD-domænetjenester. Hvilke brugerkonto skal jeg bruge til domæne joinforbindelse maskiner til dette domæne?
Brugere, du har føjet til den administrative gruppe (for eksempel ' AAD DC administratorer') kan domæne join-computere. Desuden er brugere i denne gruppe tildelt adgang til Fjernskrivebord for computere, der er blevet sluttet til domænet.

#### <a name="can-i-wield-domain-administrator-privileges-for-the-domain-provided-by-azure-ad-domain-services"></a>Kan jeg bruger domæneadministratorrettigheder for det domæne, der leveres af Azure AD-domænetjenester?
Nej. Du er ikke tildelt administratorrettigheder på administrerede domænet. Både ' domæneadministrator ' og ' virksomhedsadministratoren ' rettigheder er ikke tilgængelige for dig at bruge inden for domænet. Eksisterende domæneadministrator eller enterprise administratorgrupper i adresselisten Azure AD også ikke er tildelt domæne/enterprise administratorrettigheder på domænet.

#### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-domains-provided-by-azure-ad-domain-services"></a>Kan jeg ikke ændre gruppemedlemskaber ved hjælp af LDAP-Adresseliste eller andre AD-administration på domæner, der leveres af Azure AD-domænetjenester?
Nej. Gruppemedlemskaber kan ikke ændres på domæner, der er håndteret af Azure AD-domænetjenester. Det samme gælder for brugerattributter. Du kan dog ændre være medlem af gruppen eller brugerattributter i Azure AD eller på dit lokale domæne. Disse ændringer synkroniseres automatisk med Azure AD-domænetjenester.

#### <a name="can-i-extend-the-schema-of-the-domain-provided-by-azure-ad-domain-services"></a>Kan jeg udvide skemaet for det domæne, der leveres af Azure AD-domænetjenester?
Nej. Skemaet administreres af Microsoft til administrerede domænet. Skema filtypenavne, der ikke understøttes af Azure AD-domænetjenester.

#### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Kan jeg ændre eller tilføje DNS-poster i mit administrerede domæne?
Ja. Brugere, der hører til gruppen 'AAD DC administratorer' er tildelt ' DNS' administratorrettigheder, hvis du vil ændre DNS-poster i administrerede domænet. Disse brugere kan bruge DNS Manager-konsollen på en computer, der kører Windows Server, der er knyttet til administrerede domænet til at administrere DNS. Hvis du vil bruge DNS Manager-konsollen, installere 'DNS serverværktøjer', som er en del af funktionen 'Administrationsværktøjer Remote Server' valgfrit på serveren. Flere oplysninger om [Funktioner til administration, overvågning og fejlfinding DNS](https://technet.microsoft.com/library/cc753579.aspx) findes på TechNet.


### <a name="billing-and-availability"></a>Fakturering og tilgængelighed

#### <a name="is-azure-ad-domain-services-a-paid-service"></a>Er Azure AD domænetjenester en betalt tjeneste?
Ja. Du kan finde flere oplysninger, se [priser side](https://azure.microsoft.com/pricing/details/active-directory-ds/).

#### <a name="is-there-a-free-trial-for-the-service"></a>Er der en gratis prøveversion for tjenesten?
Denne tjeneste er inkluderet i den gratis prøveversion til Azure. Du kan tilmelde dig en [gratis prøveversion af en måned af Azure](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems"></a>Kan jeg få Azure AD-domænetjenester som en del af Enterprise mobilitet pakke (EMS)?
#### <a name="do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Skal jeg Azure AD Premium bruge Azure AD-domænetjenester?
Nej. Azure AD-domænetjenester er en pay-as-you-go Azure service og er ikke en del af EMS. Azure AD-domænetjenester er tilgængelige for alle udgaver af Azure AD (gratis, grundlæggende og, Premium) og er faktureret på grundlag af hver time, afhængigt af brugen.

#### <a name="what-azure-regions-is-the-service-available-in"></a>Hvilke Azure områder findes tjenesten i?
Se siden [Azure Services efter område](https://azure.microsoft.com/regions/#services/) for at se en liste over de Azure områder, hvor Azure AD-domænetjenester er tilgængelig.
