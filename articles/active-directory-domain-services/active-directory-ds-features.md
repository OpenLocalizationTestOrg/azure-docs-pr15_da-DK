<properties
    pageTitle="Azure Active Directory-domænetjenester: Funktioner | Microsoft Azure"
    description="Funktioner i Azure Active Directory Domain Services"
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
    ms.date="10/07/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services"></a>Azure AD-domænetjenester

## <a name="features"></a>Funktioner
Følgende funktioner er tilgængelige i Azure AD-domænetjenester administrerede domæner.

- **Enkel installation oplevelse:** Du kan aktivere Azure AD-domænetjenester for din Azure AD-lejer med blot nogle få klik. Uanset om din Azure AD-lejer er en cloud-lejer eller synkroniseret med din lokale mappe, kan domænet administrerede klargøres hurtigt.

- **Understøttelse af domæne joinforbindelse:** Du kan nemt domæne joinforbindelse computere i Azure virtuelle netværket domænet administrerede er tilgængelig i. Domæne joinforbindelse oplevelse på Windows-klienten og serveren operativsystemer fungerer problemfrit mod domæner behandles af Azure AD-domænetjenester. Du kan også bruge forbindelse til et automatiseret domæne værktøjer mod disse domæner.

- **Ét domæne forekomst per Azure AD directory:** Du kan oprette et enkelt Active Directory-domæne for hver Azure AD-mappe.

- **Oprette domæner med brugerdefinerede navne:** Du kan oprette domæner med brugerdefinerede navne (for eksempel ' contoso100.com') ved hjælp af Azure AD-domænetjenester. Du kan bruge enten bekræftet eller ikke bekræftet domænenavne. Du kan eventuelt også oprette et domæne med det indbyggede domænesuffiks (det vil sige, ' *. onmicrosoft.com'), som din Azure AD-mappe.

- **Integreret med Azure AD:** Du behøver ikke at konfigurere eller administrere gentagelse til Azure AD-domænetjenester. Brugerkonti, gruppemedlemskaber og brugerlegitimationsoplysninger (adgangskoder) fra din Azure AD-mappe er automatisk tilgængelige i Azure AD-domænetjenester. Nye brugere, grupper eller ændringer til attributter fra din Azure AD-lejer eller din lokale mappe synkroniseres automatisk med Azure AD-domænetjenester.

- **NTLM og Kerberos-godkendelse:** Du kan installere programmer, der er baseret på Windows-integreret godkendelse med understøttelse af NTLM og Kerberos-godkendelse.

- **Bruge din virksomhedens legitimationsoplysninger/adgangskoder:** Adgangskoder for brugere i din Azure AD-lejer arbejde med Azure AD-domænetjenester. Brugere kan bruge deres virksomhedens legitimationsoplysninger til domæne joinforbindelse maskiner, log på interaktivt eller via Fjernskrivebord og godkende forhold til administrerede domænet.

- **LDAP-binding og LDAP-Læs support:** Du kan bruge programmer, der er baseret på LDAP-bindinger til at godkende brugere i domæner behandles af Azure AD-domænetjenester. Programmer, der bruger LDAP-Læs handlinger til forespørgsel bruger/computer attributter fra mappen kan desuden også arbejde med Azure AD-domænetjenester.

- **Secure LDAP (LDAPS):** Du kan aktivere adgang til mappen over sikre LDAP (LDAPS). Sikker adgang til LDAP-findes i det virtuelle netværk som standard. Du kan dog også du kan også aktivere sikker LDAP-adgang via internettet.

- **Gruppepolitik:** Du kan bruge en enkelt indbyggede Gruppepolitikobjekt hver for brugere og computere beholdere at gennemtvinge overholdelse af påkrævet sikkerhedspolitikker for brugerkonti og computere, der er medlem af et domæne.

- **Administrere DNS:** Medlemmer af gruppen 'AAD DC administratorer' kan administrere DNS for domænet administrerede ved hjælp af velkendte DNS-administrationsværktøjer som DNS-Administration MMC snap-in.

- **Oprette brugerdefinerede organisatoriske enheder (afdelinger):** Medlemmer af gruppen 'AAD DC administratorer' kan oprette brugerdefinerede afdelinger i administrerede domænet. Disse brugere er tildelt alle administratorrettigheder over brugerdefinerede afdelinger, så de kan Tilføj/fjern tjenestekonti, computere, grupper osv i disse brugerdefinerede afdelinger.

- **Tilgængelig i flere Azure områder:** Se siden [Azure tjenester efter område](https://azure.microsoft.com/regions/#services/) at kende områderne Azure, Azure AD-domænetjenester er tilgængelig.

- **Høj tilgængelighed:** Azure AD-domænetjenester giver høj tilgængelighed for dit domæne. Denne funktion giver garanti for højere service oppetid og fleksibilitet til mislykkede forsøg. Indbyggede sundhed overvågning tilbud automatisk afhjælpning af fejl ved ideer op nye forekomster erstatte mislykkedes forekomster og give kontinuerlig service for dit domæne.

- **Brug velkendte administrationsværktøjer:** Du kan bruge velkendte værktøjer til administration af Windows Server Active Directory som Active Directory administrativ Center eller Active Directory-PowerShell til at administrere administrerede domæner.
