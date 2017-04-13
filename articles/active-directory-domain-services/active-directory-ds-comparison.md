<properties
    pageTitle="Azure AD-domænetjenester: Sammenligne Azure AD-domænetjenester til Foretag selv domænecontrollere | Microsoft Azure"
    description="Sammenligning af Azure Active Directory Domain Services til DIY domænecontrollere"
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
    ms.date="10/01/2016"
    ms.author="maheshu"/>

# <a name="how-to-decide-if-azure-ad-domain-services-is-right-for-your-use-case"></a>Beslutte, hvis Azure AD-domænetjenester der passer bedst til dit use case
Azure AD-domænetjenester gør det muligt at installere din arbejdsmængder i Azure infrastrukturtjenester, uden at skulle bekymre dig om at vedligeholde infrastrukturen identitet. Denne administreret tjeneste er forskellig fra en typisk Windows Server Active Directory-installation, du skal installere og administrere dine egne. Tjenesten er udviklet til øget for installation, automatiseret Tilstandsovervågning og afhjælpning og en enkelt identitet infrastruktur til skyen. Vi konstant udvikling af tjenesten for at tilføje understøttelse af almindelige installationsscenarier.

Beslutte, om du vil bruge Azure AD-domænetjenester eller rotere rundt op og administrere dine egne (gør det selv) i Azure AD-infrastruktur:

- Se listen over [Funktioner, som Azure AD-domænetjenester](active-directory-ds-features.md).

- Gennemse almindelige [installationsscenarier, hvor Azure AD-domænetjenester](active-directory-ds-scenarios.md).

- Til sidst skal [sammenligne Azure AD-domænetjenester gør det selv AD indstillingen](active-directory-ds-comparison.md#compare-azure-ad-domain-services-to-diy-ad-domain-in-azure).


## <a name="compare-azure-ad-domain-services-to-diy-ad-domain-in-azure"></a>Sammenligne Azure AD-domænetjenester til DIY AD domæne i Azure
Den følgende tabel kan du vælge mellem ved hjælp af Azure AD-domænetjenester og administrere dine egne Azure AD-infrastruktur.

|**Funktion**|**Azure AD-domænetjenester**|**'Gør det selv' AD i Azure FOS**|
|---|:---:|:---:|
|[**Administreret tjeneste**](active-directory-ds-comparison.md#managed-service)|**& #x 2713;**|**& #x 2715;**|
|[**Sikre installationer**](active-directory-ds-comparison.md#secure-deployments)|**& #x 2713;**|Administratoren til at sikre installationen.|
|[**DNS-server**](active-directory-ds-comparison.md#dns-server)|**& #x 2713;** (administreret tjeneste)|**& #x 2713;**|
|[**Domæne eller Enterprise administratorrettigheder**](active-directory-ds-comparison.md#domain-or-enterprise-administrator-privileges)|**& #x 2715;**|**& #x 2713;**|
|[**Forbindelse til et domæne**](active-directory-ds-comparison.md#domain-join)|**& #x 2713;**|**& #x 2713;**|
|[**Godkendelse af domæner ved hjælp af NTLM og Kerberos**](active-directory-ds-comparison.md#domain-authentication-using-ntlm-and-kerberos)|**& #x 2713;**|**& #x 2713;**|
|[**Brugerdefineret OU struktur**](active-directory-ds-comparison.md#custom-ou-structure)|**& #x 2713;**|**& #x 2713;**|
|[**Skema filtypenavne**](active-directory-ds-comparison.md#schema-extensions)|**& #x 2715;**|**& #x 2713;**|
|[**AD domæner har tillid til**](active-directory-ds-comparison.md#ad-domain-or-forest-trusts)|**& #x 2715;**|**& #x 2713;**|
|[**LDAP læse**](active-directory-ds-comparison.md#ldap-read)|**& #x 2713;**|**& #x 2713;**|
|[**Sikker LDAP (LDAPS)**](active-directory-ds-comparison.md#secure-ldap)|**& #x 2713;**|**& #x 2713;**|
|[**LDAP-Skriv**](active-directory-ds-comparison.md#ldap-write)|**& #x 2715;**|**& #x 2713;**|
|[**Gruppepolitik**](active-directory-ds-comparison.md#group-policy)|Enkel|Fuld|
|[**Geografisk distribueret installationer**](active-directory-ds-comparison.md#geo-dispersed-deployments)|**& #x 2715;**|**& #x 2713;**|


#### <a name="managed-service"></a>Administreret tjeneste
Azure AD-domænetjenester domæner administreres af Microsoft. Du behøver ikke at bekymre dig om rettelse, opdateringer, overvågning, sikkerhedskopier, og sikre, at tilgængeligheden af dit domæne. Disse administrationsopgaver, som en tjeneste som Microsoft Azure for din administrerede domæner.

#### <a name="secure-deployments"></a>Sikre installationer
Administrerede domænet er sikkert låst ud fra Microsofts Sikkerhed bedste fremgangsmåder til AD installationer. Disse bedste fremgangsmåder stammer fra mange år AD-produktteamet erfaring engineering og understøttende AD installationer. I gør det selv installationer skal du udføre bestemte installation trin for at låse ned/secure din installation.

#### <a name="dns-server"></a>DNS-server
En Azure AD-domænetjenester administrerede domæne omfatter administrerede DNS-tjenester. Medlemmer af gruppen 'AAD DC administratorer' kan administrere DNS på administrerede domænet. Medlemmer af denne gruppe får fuld Administration af DNS-rettigheder til administrerede domænet. DNS-administration kan udføres ved hjælp af DNS-Administration en konsollen inkluderet i pakken fjernserver værktøjer (RSAT).

#### <a name="domain-or-enterprise-administrator-privileges"></a>Domæne eller virksomhedens Administrator rettigheder
Disse administratorrettigheder tilbydes ikke på en AAD DS administrerede domæne. Programmer, der kræver disse administratorrettigheder skal være installeret/Kør kan ikke køres mod administrerede domæner. Et mindre undersæt af administratorrettigheder er tilgængelig for medlemmer af gruppen delegeret administration kaldet 'AAD DC administratorer'. Disse rettigheder omfatter rettigheder til at konfigurere DNS, konfigurere Gruppepolitik, får administratorrettigheder på medlem af et domæne maskiner osv.

#### <a name="domain-join"></a>Forbindelse til et domæne
Du kan deltage virtuelle maskiner til administrerede domænet svarende til, hvordan du deltager i computere til et AD-domæne.

#### <a name="domain-authentication-using-ntlm-and-kerberos"></a>Godkendelse af domæner ved hjælp af NTLM og Kerberos
Du kan bruge dine virksomhedens legitimationsoplysninger til at godkende med administrerede domænet med Azure AD Domain Services. Legitimationsoplysninger holdes synkroniseret med din Azure AD-lejer. Azure AD-forbindelse sikrer, at lokale legitimationsoplysninger, der er foretaget ændringer synkroniseres til Azure AD for synkroniserede lejere. Med en DIY domænekonfiguration skal du konfigurere et domæne tillidsforhold med et lokalt konto område for brugerne at godkende med deres virksomhedens legitimationsoplysninger. Alternativt kan du muligvis konfigurere AD gentagelse til at sikre, at brugeradgangskoder synkroniserer din Azure domænecontroller virtuelle maskiner.

#### <a name="custom-ou-structure"></a>Brugerdefineret OU struktur
Medlemmer af gruppen 'AAD DC administratorer' kan oprette brugerdefinerede afdelinger i administrerede domænet.

#### <a name="schema-extensions"></a>Skema filtypenavne
Du kan udvide base skemaet for en administreret Azure AD Domain Services-domæne. Programmer, der er afhænger af udvidelser til AD skema (for eksempel nye attributter under brugerobjektet) kan ikke være hæves og flyttes til AAD DS domæner.

#### <a name="ad-domain-or-forest-trusts"></a>AD-domæne eller tillidsforhold fra område
Administrerede domæner kan ikke konfigureres til at konfigurere tillidsforhold (indgående/udgående) med andre domæner. Scenarier som ressource skov installationer eller tilfælde, hvor du foretrækker ikke at synkronisere adgangskoderne til Azure AD kan derfor ikke bruge Azure AD-domænetjenester.

#### <a name="ldap-read"></a>LDAP-læst
Administrerede domænet understøtter LDAP læse arbejdsmængder. Derfor kan du installere programmer, der udfører LDAP-Læs handlinger forhold til administrerede domænet.

#### <a name="secure-ldap"></a>Sikker LDAP
Du kan konfigurere Azure AD Domain Services for at give sikker LDAP-adgang til dit administrerede domæne, herunder via internettet.

#### <a name="ldap-write"></a>LDAP-Skriv
Administrerede domænet er skrivebeskyttet for user-objekter. Derfor fungerer programmer, der udfører LDAP skrivning mod attributter for brugerobjektet ikke i et administreret domæne. Desuden kan brugernes adgangskoder ikke ændres fra i de administrerede domæne. Et andet eksempel ville være ændring af gruppemedlemskaber eller gruppe attributter i det administrerede domæne, som ikke er tilladt. Men eventuelle ændringer i brugerattributter eller adgangskoder, der er foretaget i Azure AD (via PowerShell/Azure portal) eller en lokal AD synkroniseres til AAD DS administrerede domænet.

#### <a name="group-policy"></a>Gruppepolitik
Avancerede Gruppepolitik konstruktioner understøttes ikke på AAD DS administrerede domænet. For eksempel kan du opretter og Installer separat gruppepolitikobjekter for hver brugerdefineret OU i domænet eller bruger WMI filtrering for generel målretning af. Der er et indbygget GPO hver til 'AADDC computere' og 'AADDC brugere' beholdere, som kan tilpasses til at kunne konfigurere Gruppepolitik.

#### <a name="geo-dispersed-deployments"></a>Fordelt over geografisk installationer
Azure AD-domænetjenester administrerede domæner er tilgængelige i et enkelt virtuelt netværk i Azure. For scenarier, som kræver, at være tilgængeligt i flere Azure områder på hele verden kan opsætning af domæne enheder i Azure IaaS FOS være bedre alternativ.


## <a name="do-it-yourself-diy-ad-deployment-options"></a>'Gør det selv' (DIY) AD installationsindstillinger
Du kan være nødvendigt installation Brug-tilfælde, hvor du har brug for nogle af de funktioner, som en installation af Windows Server AD. I disse tilfælde kan du overveje et af følgende gør det selv (DIY) indstillinger:

- **Enkeltstående skyen domæne:** Du kan konfigurere en enkeltstående 'skyen domæne' ved hjælp af Azure virtuelle maskiner, der er konfigureret som domæne enheder. Denne infrastruktur kan ikke integreres med dine lokale AD-miljø. Denne indstilling kræver et andet sæt af 'skyen legitimationsoplysninger' til at login/administrere FOS i skyen.

- **Ressource skov installation:** Du kan konfigurere et domæne i ressource skov topologien, ved hjælp af Azure virtuelle maskiner, der er konfigureret som domæne enheder. Derefter skal du kan konfigurere et AD tillidsforhold med dine lokale AD-miljø. Du kan domæne joinforbindelse computere (Azure FOS) til denne ressourceskov i skyen. Brugergodkendelse sker over enten en VPN-/ ExpressRoute forbindelse til din lokale mappe.

- **Udvid domænet i det lokale miljø til Azure:** Du kan oprette forbindelse et Azure virtuelle netværk til dit lokale netværk ved hjælp af en VPN-/ ExpressRoute forbindelse, så Azure FOS kan være medlem af dine lokale AD. En anden mulighed er at fremme replika domæne enheder på dit lokale domæne i Azure som en VM. Du kan derefter konfigurere det til at gentage over en VPN-/ ExpressRoute forbindelse til din lokale mappe. Denne installation tilstand udvides effektivt domænet i det lokale miljø til Azure.

> [AZURE.NOTE] Du kan se valgmuligheden Foretag selv passer bedre til din installation Brug-sager. Overveje at [dele feedback](active-directory-ds-contact-us.md) kan hjælpe os med at forstå, hvad funktioner ville hjælper du vælger Azure AD-domænetjenester i fremtiden. Denne feedback hjælper os med at udvikle tjenesten, så de passer bedre til dine installationer og brug sager.

Vi har udgivet [retningslinjer for installation af Windows Server Active Directory på virtuelle Azure-computere](https://msdn.microsoft.com/library/azure/jj156090.aspx) for at gøre det nemmere at foretag selv installationer.


## <a name="related-content"></a>Relateret indhold
- [Funktioner - Azure AD-domænetjenester](active-directory-ds-features.md)

- [Installationsscenarier - Azure AD-domænetjenester](active-directory-ds-scenarios.md)

- [Retningslinjer for installation af Windows Server Active Directory på Azure virtuelle maskiner](https://msdn.microsoft.com/library/azure/jj156090.aspx)
