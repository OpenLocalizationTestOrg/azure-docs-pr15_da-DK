<properties
    pageTitle="Azure AD-forbindelse: Udføre fejlfinding på forbindelsesproblemer | Microsoft Azure"
    description="Forklarer, hvordan du udfører fejlfinding af forbindelsesproblemer med Azure AD-forbindelse."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/27/2016"
    ms.author="billmath"/>

# <a name="troubleshoot-connectivity-issues-with-azure-ad-connect"></a>Udføre fejlfinding på forbindelsesproblemer med Azure AD-forbindelse
I denne artikel beskrives det, hvordan forbindelsen mellem Azure AD-forbindelse og Azure AD fungerer, og hvordan du kan foretage fejlfinding af problemer med serverforbindelsen. Disse problemer er mest sandsynlige til at blive set i et miljø med en proxy-server.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Udføre fejlfinding på forbindelsesproblemer i installationsguiden
Azure AD-forbindelse bruger moderne godkendelse (ved hjælp af biblioteket ADAL) til godkendelse. Installationsguiden og synkronisere programmet stort kræver machine.config skal konfigureres korrekt, fordi disse er .NET-programmer.

I denne artikel får du vist hvordan Fabrikam opretter forbindelse til Azure AD gennem dens proxy. Proxyserveren hedder fabrikamproxy og bruger port 8080.

Først skal vi at sikre, at [**machine.config**](active-directory-aadconnect-prerequisites.md#connectivity) er konfigureret korrekt.  
![machineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/machineconfig.png)

>[AZURE.NOTE]
I nogle ikke-Microsoft blogs er det dokumenteret, bør foretages ændringer miiserver.exe.config i stedet. Denne fil er dog blive overskrevet på hver så lige, hvis det ikke virker under indledende installation, systemet går ned på første opgradering opgraderingen. Derfor anbefales det at opdatere machine.config i stedet for.

Proxy-server skal også have de nødvendige URL-adresser, der er åbnet. Den officielle liste er beskrevet i [Office 365 URL-adresser og IP-adresseområder ](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Følgende tabel er af disse, absolut som minimum skal kunne oprette forbindelse til Azure AD overhovedet. Denne liste omfatter ikke alle valgfrie funktioner, som adgangskode tilbageførsel eller Azure AD forbinde tilstand. Det er beskrevet her for at hjælpe med fejlfinding for den indledende konfiguration.

URL-ADRESSE | Port | Beskrivelse
---- | ---- | ----
mscrl.Microsoft.com | HTTP/80 | Bruges til at hente lister over tilbagekaldte certifikater.
\*. verisign.com | HTTP/80 | Bruges til at hente lister over tilbagekaldte certifikater.
\*. entrust.com | HTTP/80 | Bruges til at hente lister over tilbagekaldte certifikater til MFA.
\*. windows.net | HTTPS/443 | Bruges til at logge på Azure AD.
Secure.aadcdn.microsoftonline p.com | HTTPS/443 | Bruges til MFA.
\*. microsoftonline.com | HTTPS/443 | Bruges til at konfigurere din Azure AD-mappe og Importér/Eksportér data.

## <a name="errors-in-the-wizard"></a>Fejl i guiden
Installationsguiden bruger to forskellige sikkerhedskontekster. Der anvendes i øjeblikket logget brugeren på siden **Opret forbindelse til Azure AD** . På siden **Konfigurer** ændres den til den [konto, kører tjenesten til synkronisering af programmet](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts). De proxy konfigurationer vi yder er global på computeren, så hvis der er et problem, problemet vil de fleste sandsynligt vises allerede på siden **Opret forbindelse til Azure AD** i guiden.

Dette er de mest almindelige fejl, der vises i installationsguiden.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>Installationsguiden er ikke konfigureret korrekt
Denne fejl vises, når guiden selve ikke kan nå proxyen.
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomachineconfig.png)

- Hvis du ser ud, skal du kontrollere [machine.config](active-directory-aadconnect-prerequisites.md#connectivity) er konfigureret korrekt.
- Hvis, der ser rigtig ud, skal du følge trinnene i [Bekræft proxy connectivity](#verify-proxy-connectivity) at se, om problemet findes uden for guiden samt.

### <a name="the-mfa-endpoint-cannot-be-reached"></a>MFA slutpunktet kan ikke kontaktes
Denne fejl vises, hvis slutpunkt **https://secure.aadcdn.microsoftonline-p.com** ikke kan nås og din globale administratorer har MFA aktiveret.  
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomicrosoftonlinep.png)

- Hvis du ser ud, skal du kontrollere, slutpunkt secure.aadcdn.microsoftonline-p.com er blevet føjet til proxyen.

### <a name="the-password-cannot-be-verified"></a>Adgangskoden kan ikke bekræftes
Hvis installationsguiden er lykkes at oprette forbindelse til Azure AD, men ikke kan bekræftes selve adgangskoden, kan du se dette: ![badpassword](./media/active-directory-aadconnect-troubleshoot-connectivity/badpassword.png)

- Er adgangskoden en midlertidig adgangskode, og skal ændres? Er det faktisk den rigtige adgangskode? Prøv at logge på https://login.microsoftonline.com (på en anden computer end den Azure AD-forbindelse server), og Bekræft kontoen, der kan bruges.

### <a name="verify-proxy-connectivity"></a>Kontrollere proxy forbindelsen
For at bekræfte, om serveren Azure AD-forbindelse har den faktiske forbindelse med Proxy og Internet skal vi bruge nogle PowerShell til at se Hvis proxyen tillade webanmodninger eller ej. Kør i en PowerShell-prompt `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`. (Teknisk første opkaldet er til https://login.microsoftonline.com og fungerer også, men de andre URI er hurtigere at svare).

PowerShell bruger konfigurationen i machine.config til at kontakte proxyen. Indstillingerne i winhttp/netsh skal ikke påvirke disse cmdlet'er.

Hvis proxyen er korrekt konfigureret, skal du have en succes status: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest200.png)

Hvis du modtager **kan ikke oprette forbindelse til fjernserveren** derefter PowerShell forsøger at ringe direkte uden brug af proxyen, eller DNS er ikke konfigureret korrekt. Sørg for, at filen **machine.config** er konfigureret korrekt.
![unabletoconnect](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequestunable.png)

Hvis proxyen, der ikke er korrekt konfigureret, vi får en fejl: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest403.png)
![proxy407](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest407.png)

Fejl | Fejltekst | Kommentar
---- | ---- | ---- |
403 | Forbudt | Proxyen er ikke blevet åbnet til den ønskede URL-adresse. Besøger proxykonfiguration, og Sørg for, at [URL-adresser](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) har været åbnet.
407 | Proxygodkendelse kræves | Proxy-server kræves logon, og ingen blev leveret. Hvis din proxy-server kræver godkendelse, Sørg for, at du vil tilføje denne konfigureret i machine.config. Kontroller også, at du bruger domænekonti for den bruger, der kører guiden samt som for tjenestekontoen.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>Mønstret kommunikation mellem Azure AD-forbindelse og Azure AD
Hvis du har fulgt alle trinnene ovenfor og stadig ikke kan oprette forbindelse kan du på dette tidspunkt starte studere netværk logfiler. Dette afsnit er dokumentere et normal og vellykket connectivity mønster. Det også listen over almindelige rød herrings, kan ignoreres, hvis du læser loggene netværk.

- Der vil være opkald til https://dc.services.visualstudio.com. Det er ikke nødvendigt at have denne Åbn i proxy efter installationen kan fuldføres, og disse kan ignoreres.
- Du vil se, at dns-opløsning vises alle de faktiske værter for at være i DNS-navn mellemrum nsatc.net og andre navneområder ikke under microsoftonline.com. Men der bliver ikke en hvilken som helst web serviceanmodninger på de faktiske servernavne, og du behøver ikke at tilføje disse til proxyen.
- De slutpunkter adminwebservice og provisioningapi (se nedenfor i loggene) er registrering slutpunkter og bruges til at finde det faktiske slutpunkt til bruger og vil være anderledes afhængigt af dit område.

### <a name="reference-proxy-logs"></a>Reference proxy logfiler
Her er en lagring fra en faktisk proxy logfilen og siden i guiden installation fra hvor det er taget (dublerede poster til samme slutpunktet er blevet fjernet). Dette kan bruges som en reference til din egen proxy og netværk logfiler. Faktisk slutpunkterne kan være forskellige i dit miljø (især dem, i *kursiv*).

**Oprette forbindelse til Azure AD**

Tid | URL-ADRESSE
--- | ---
1/11/2016 8:31 | Connect://login.microsoftonline.com:443
1-11/2016 8:31 | Connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:32 | oprette forbindelse: / /*bba800 anker*. microsoftonline.com:443
1/11/2016 8:32 | Connect://login.microsoftonline.com:443
1-11/2016 8:33 | Connect://provisioningapi.microsoftonline.com:443
1-11/2016 8:33 | oprette forbindelse: / /*bwsc02-relay*. microsoftonline.com:443

**Konfigurere**

Tid | URL-ADRESSE
--- | ---
1-11/2016 8:43 | Connect://login.microsoftonline.com:443
1-11/2016 8:43 | oprette forbindelse: / /*bba800 anker*. microsoftonline.com:443
1-11/2016 8:43 | Connect://login.microsoftonline.com:443
1-11/2016 8:44 | Connect://adminwebservice.microsoftonline.com:443
1-11/2016 8:44 | oprette forbindelse: / /*bba900 anker*. microsoftonline.com:443
1/11/2016 8:44 | Connect://login.microsoftonline.com:443
1-11/2016 8:44 | Connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:44 | oprette forbindelse: / /*bba800 anker*. microsoftonline.com:443
1/11/2016 8:44 | Connect://login.microsoftonline.com:443
1/11/2016 8:46 | Connect://provisioningapi.microsoftonline.com:443
1/11/2016 8:46 | oprette forbindelse: / /*bwsc02-relay*. microsoftonline.com:443

**Indledende synkronisering**

Tid | URL-ADRESSE
--- | ---
1-11/2016 8:48 | Connect://login.Windows.NET:443
1/11/2016 8:49 | Connect://adminwebservice.microsoftonline.com:443
1-11/2016 8:49 | oprette forbindelse: / /*bba900 anker*. microsoftonline.com:443
1-11/2016 8:49 | oprette forbindelse: / /*bba800 anker*. microsoftonline.com:443

## <a name="authentication-errors"></a>Af godkendelsesfejl
Dette afsnit beskrives fejl, der kan returneres fra ADAL (godkendelse biblioteket bruges af Azure AD-forbindelse) og PowerShell. Fejlen forklaring hjælpe dig i forstå de næste trin.

### <a name="invalid-grant"></a>Ugyldige Giv
Ugyldigt brugernavn eller adgangskode. Yderligere oplysninger finder du i [feltet adgangskode ikke kan bekræftes](#the-password-cannot-be-verified) .

### <a name="unknown-user-type"></a>Ukendt brugertype
Azure AD mappen blev ikke fundet eller løst. Måske du forsøger at logge på med et brugernavn i et ikke-bekræftede domæne?

### <a name="user-realm-discovery-failed"></a>Blev ikke fundet en bruger ressource
Netværks- eller proxyindstillinger konfigurationsproblemer. Netværket kan ikke nås, se [fejlfinding af forbindelsesproblemer i installationsguiden](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>Brugeradgangskode udløbet
Dine legitimationsoplysninger er udløbet. Ændre din adgangskode.

### <a name="authorizationfailure"></a>AuthorizationFailure
Ukendt problem.

### <a name="authentication-cancelled"></a>Godkendelse annulleret
Multi-Factor authentication (MFA) udfordringen blev annulleret.

### <a name="connecttomsonline"></a>ConnectToMSOnline
Godkendelse lykkedes, men Azure AD PowerShell har et problem under godkendelse.

### <a name="azurerolemissing"></a>AzureRoleMissing
Godkendelse lykkedes. Du er ikke en global administrator.

### <a name="privilegedidentitymanagement"></a>PrivilegedIdentityManagement
Godkendelse lykkedes. Administration af privilegerede identitet er blevet aktiveret, og du i øjeblikket er ikke en global administrator. Du kan få flere oplysninger i [Privilegerede identitet administration](active-directory-privileged-identity-management-getting-started.md) .

### <a name="companyinfounavailable"></a>CompanyInfoUnavailable
Godkendelse lykkedes. Kunne ikke hente oplysninger fra Azure AD.

### <a name="retrievedomains"></a>RetrieveDomains
Godkendelse lykkedes. Kunne ikke hente domæneoplysninger fra Azure AD.

## <a name="troubleshooting-steps-for-previous-releases"></a>Trin til fejlfinding i tidligere versioner.
Den Logonassistent er udgået med udgaver, der starter med build-nummeret 1.1.105.0 (udgivet februar 2016). Dette afsnit og konfigurationen skal ikke længere er påkrævet, men der bevares som reference.

For enkelt – Log på Assistent til at fungere sammen, skal være konfigureret winhttp. Dette kan gøres med [**netsh**](active-directory-aadconnect-prerequisites.md#connectivity).  
![Netsh](./media/active-directory-aadconnect-troubleshoot-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>Logonassistent er ikke konfigureret korrekt
Denne fejl vises, når Logonassistent ikke kan nå proxyen eller proxyen ikke tillader, at din anmodning.
![nonetsh](./media/active-directory-aadconnect-troubleshoot-connectivity/nonetsh.png)

- Hvis du ser ud, kan du se på proxykonfigurationen i [netsh](active-directory-aadconnect-prerequisites.md#connectivity) og bekræft den er korrekt.
![netshshow](./media/active-directory-aadconnect-troubleshoot-connectivity/netshshow.png)
- Hvis, der ser rigtig ud, skal du følge trinnene i [Bekræft proxy connectivity](#verify-proxy-connectivity) at se, om problemet findes uden for guiden samt.

## <a name="next-steps"></a>Næste trin
Lær mere om at [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).
