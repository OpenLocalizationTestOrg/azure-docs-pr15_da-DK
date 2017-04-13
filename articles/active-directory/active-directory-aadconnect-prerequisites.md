<properties
   pageTitle="Azure AD-forbindelse: Forudsætninger og hardware | Microsoft Azure"
   description="I dette emne beskrives forudsætninger og hardwarekrav til Azure AD-forbindelse"
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
   ms.date="10/12/2016"
   ms.author="billmath"/>

# <a name="prerequisites-for-azure-ad-connect"></a>Forudsætninger for Azure AD-forbindelse
Dette emne beskrives forudsætninger og hardwarekrav til Azure AD-forbindelse.

## <a name="before-you-install-azure-ad-connect"></a>Før du installerer Azure AD-forbindelse
Før du installerer Azure AD-forbindelse, er der et par ting, du skal bruge.

### <a name="azure-ad"></a>Azure AD
- En Azure-abonnement eller [Azure prøveabonnement](https://azure.microsoft.com/pricing/free-trial/). Dette kun er påkrævet for at få adgang til portalen Azure og ikke til brug af Azure AD-forbindelse. Hvis du bruger PowerShell eller Office 365, behøver du ikke en Azure-abonnement for at bruge Azure AD-forbindelse. Du kan også bruge Office 365-portalen, hvis du har en Office 365-licens. Med et betalt Office 365-licens kan du også få til portalen Azure fra Office 365-portalen.
    - Du kan også bruge funktionen Azure AD preview [Azure-portalen](https://portal.azure.com). Denne portal kræver ikke en Azure-licens.
- [Tilføj og bekræfte domænet](active-directory-add-domain.md) du planlægger at bruge i Azure AD. Eksempelvis hvis du planlægger at bruge contoso.com for dine brugere, og Sørg for dette domæne er bekræftet og du kun bruger ikke standarddomænet contoso.onmicrosoft.com.
- En Azure AD-lejer kan som 50k standardobjekter. Når du har bekræftet dit domæne, øges grænsen til 300 k objekter. Hvis du har brug for endnu flere objekter i Azure AD skal du åbne en understøttelse af store og små bogstaver for at få grænsen for øget yderligere. Hvis du har brug for mere end 500 k objekter skal du en licens, som Office 365, Azure AD grundlæggende, Azure AD Premium eller Enterprise mobilitet pakke.

### <a name="prepare-your-on-premises-data"></a>Forberede dine lokale data
- Gennemse [valgfrit Synkroniser funktioner kan du aktivere i Azure AD](active-directory-aadconnectsyncservice-features.md) og evaluere hvilke funktioner, du skal aktivere.

### <a name="on-premises-servers-and-environment"></a>Lokale servere og -miljø
- Den AD version og skov funktionelle skemaniveau skal være Windows Server 2003 eller nyere. Domænecontrollerne kan køre alle versioner, så længe skemaet og skov niveau krav er opfyldt.
- Hvis du planlægger at bruge funktionen **adgangskode tilbageførsel** være domænecontrollerne på Windows Server 2008 (med seneste SP) eller nyere. Hvis din DCs er på 2008 (pre-R2) skal du også anvende [programrettelse KB2386717](http://support.microsoft.com/kb/2386717).
- Domænecontrolleren bruges af Azure AD skal kunne skrives. Det understøttes ikke for at bruge en RODC (skrivebeskyttet domænenavn fra domænecontrolleren) og Azure AD-forbindelse følger ikke en hvilken som helst Skriv omdirigeringer.
- Azure AD-forbindelse kan ikke installeres på Small Business Server eller Windows Server Essentials. Serveren, der skal bruge Windows Server standard eller bedre.
- Azure AD-forbindelse serveren skal have en fuld grafiske brugergrænseflade installeret. Det er ikke understøttet for at installere på server core.
- Azure AD-forbindelse skal være installeret på Windows Server 2008 eller nyere. Denne server kan være et domænenavn fra domænecontrolleren eller et medlemsserver, hvis ved hjælp af hurtig indstillinger. Hvis du bruger brugerdefinerede indstillinger, kan også være enkeltstående serveren, og du behøver ikke at være knyttet til et domæne.
- Hvis du installerer Azure AD-forbindelse på Windows Server 2008, skal du sørge for at anvende den nyeste serverhotfix fra Windows Update. Installationen er ikke kan starte med en ikke-opdateret server.
- Hvis du planlægger at bruge funktionen **synkronisering af adgangskoder**, være Azure AD-forbindelse server på Windows Server 2008 R2 SP1 eller nyere.
- Azure AD-forbindelse serveren skal have [.NET Framework 4.5.1](#component-prerequisites) eller nyere og [Microsoft PowerShell 3.0](#component-prerequisites) eller nyere installeret.
- Hvis Active Directory Federation Services installeres, hvor AD FS eller Web proxyen installerede servere skal være Windows Server 2012 R2 eller nyere. [Windows Fjernadministration](#windows-remote-management) skal være aktiveret på følgende servere for fjerninstallation.
- Hvis Active Directory Federation Services installeres, skal du [SSL-certifikater](#ssl-certificate-requirements).
- Hvis Active Directory Federation Services installeres, skal du konfigurere [navneoversættelse](#name-resolution-for-federation-servers).
- Azure AD-forbindelse kræver en SQL Server-database til at gemme identitetsdata. Som standard en SQL Server 2012 Express LocalDB (lightversionen af SQL Server Express) er installeret, og servicekonto for tjenesten er oprettet på den lokale computer. SQL Server Express har en 10GB størrelsesgrænse, der gør det muligt at administrere cirka 100.000 objekter. Hvis du har brug for til at administrere en højere mængde directory-objekter, skal du pege installationsguiden til en anden installation af SQL Server.
- Hvis du bruger en separat SQL Server, gælder følgende krav:
    - Azure AD-forbindelse understøtter alle typer af Microsoft SQL Server fra SQL Server 2008 (med SP4) til SQL Server-2014. Microsoft Azure SQL-Database er **ikke understøttet** som en database.
    - Du skal bruge en case-insensitive SQL-sortering. Disse er identificeret med en \_CI_ i deres navn. Det er **ikke understøttet** at bruge en sorteringsindstilling, der er identificeret med \_CS_ i deres navn.
    - Du kan kun have én Synkroniser program per databaseforekomst. Det er **ikke understøttet** til at dele databaseforekomst med FIM/MIM synkronisering, DirSync eller Azure Active Directory-synkronisering.

### <a name="accounts"></a>Konti
- En Global Administrator for Azure AD-konto til Azure AD-mappe, du vil integrere med. Dette skal være en **skole-eller organisation** og kan ikke være en **Microsoft-konto**.
- Hvis du bruger udtrykkelig indstillinger eller opgradere fra DirSync, skal du have en virksomhedens Administrator-konto til din lokale Active Directory.
- Hvis du bruger den brugerdefinerede indstillinger installationssti [konti i Active Directory](./connect/active-directory-aadconnect-accounts-permissions.md) .

### <a name="azure-ad-connect-server-configuration"></a>Azure AD-forbindelse serverkonfiguration
- Hvis din globale administratorer har MFA aktiveret, skal være URL-adressen **https://secure.aadcdn.microsoftonline-p.com** i listen over pålidelige websteder. Du bliver bedt om at føje dette til listen over pålidelige websteder, hvis det ikke er tilføjet, før du bliver bedt om en MFA udfordring. Du kan bruge Internet Explorer til at føje det til de websteder, der er tillid til.

### <a name="connectivity"></a>Forbindelse
- Azure AD-forbindelse serveren kræver DNS-opløsning til både intranet og internet. DNS-server skal kunne løse navne både til din lokale Active Directory samt Azure AD slutpunkterne.
- Hvis du har firewalls på intranettet, og du har brug at åbne porte mellem Azure AD-forbindelse servere og enheder dit domæne, kan du se [Azure AD Connect porte](active-directory-aadconnect-ports.md) kan finde flere oplysninger.
- Hvis din proxy- eller firewall grænseværdi, som kan få adgang til URL-adresser, derefter URL-adresserne beskrevet i [Office 365 URL-adresser og IP-adresser](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) skal være åben.
    - Hvis du bruger Microsoft Cloud i Tyskland eller Microsoft Azure Government skyen, kan du se [Azure AD-forbindelse Synkroniser service forekomster overvejelser](active-directory-aadconnect-instances.md) til URL-adresser.
- Azure AD-forbindelse er som standard bruge TLS 1.0 til at kommunikere med Azure AD. Du kan ændre dette til TLS 1.2 ved at følge trinnene i [aktivere TLS 1.2 til Azure AD-forbindelse](#enable-tls-12-for-azure-ad-connect).
- Hvis du bruger en udgående proxy til at oprette forbindelse til internettet, skal følgende indstilling i filen **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** føjes til installationsguiden og Azure AD-forbindelse synkronisering kunne oprette forbindelse til internettet og Azure AD. Denne tekst skal angives i bunden af filen. I denne kode &lt;PROXYADRESS&gt; repræsenterer faktisk proxy IP-adresse eller host name.

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

- Hvis din proxy-server kræver godkendelse, [tjenestekonto](./connect/active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts) skal være placeret i domænenavn, og du skal bruge installationssti tilpassede indstillinger til at angive en [brugerdefineret tjenestekonto](./connect/active-directory-aadconnect-get-started-custom.md#install-required-components). Du skal også en anden ændring af machine.config. Med denne besvare ændring i machine.config installationsguiden og synkronisere program anmodninger om godkendelse fra proxy-server. I alle installationsguiden bruges sider, undtagen siden **Konfigurer** , den signerede i brugerens legitimationsoplysninger. På siden **Konfigurer** i slutningen af installationsguiden skiftet konteksten til den [tjenestekonto](./connect/active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts) , der blev oprettet af dig. Afsnittet machine.config ser sådan ud.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

Du kan finde flere oplysninger om [standardproxy Element](https://msdn.microsoft.com/library/kd3cf2ex.aspx)i MSDN.

Hvis du har problemer med forbindelsen, skal du se [fejlfinding af forbindelsesproblemer](active-directory-aadconnect-troubleshoot-connectivity.md).

### <a name="other"></a>Andre
- Valgfrit: En test bruger for at bekræfte synkroniseringen.

## <a name="component-prerequisites"></a>Forudsætninger for komponent

### <a name="powershell-and-net-framework"></a>PowerShell og .net Framework
Azure AD-forbindelse, afhænger af Microsoft PowerShell og .NET Framework 4.5.1. Du har brug for denne version eller nyere installeret på din server. Afhængigt af din version af Windows Server, skal du gøre følgende:

- Windows Server 2012R2
  - Microsoft PowerShell er installeret som standard, ingen handling er påkrævet.
  - .NET framework 4.5.1 og nyere versioner tilbydes via Windows Update. Sørg for, at du har installeret de seneste opdateringer til Windows Server i Kontrolpanel.
- Windows Server 2008R2 og Windows Server 2012
  - Den seneste version af Microsoft PowerShell er tilgængelig i **Windows Management Framework 4.0**tilgængelig på [Microsoft Download Center](http://www.microsoft.com/downloads).
  - .NET framework 4.5.1 og nyere versioner er tilgængelige på [Microsoft Download Center](http://www.microsoft.com/downloads).
- Windows Server 2008
  - Den seneste understøttede version af PowerShell er tilgængelig i **Windows Management Framework 3.0**tilgængelig på [Microsoft Download Center](http://www.microsoft.com/downloads).
 - .NET framework 4.5.1 og nyere versioner er tilgængelige på [Microsoft Download Center](http://www.microsoft.com/downloads).

### <a name="enable-tls-12-for-azure-ad-connect"></a>Aktivere TLS 1.2 til Azure AD Connect
Azure AD-forbindelse som standard med TLS 1.0 til at kryptere kommunikationen mellem Synkroniser program server og Azure AD. Du kan ændre dette ved at konfigurere .net-programmer til at bruge TLS 1.2 som standard på serveren. Kan finde flere oplysninger om TLS 1.2 i [Microsoft sikkerhed vejledende 2960358](https://technet.microsoft.com/security/advisory/2960358).

1. TLS 1.2 kan ikke aktiveres på Windows Server 2008. Du skal bruge Windows Server 2008R2 eller nyere. Sørg for, du har den .net 4.5.1 programrettelse installeret for dit operativsystem, skal du se [Microsoft sikkerhed vejledende 2960358](https://technet.microsoft.com/security/advisory/2960358). Du muligvis denne eller en nyere version, der allerede er installeret på din server.
2. Hvis du bruger Windows Server 2008R2, derefter skal du kontrollere TLS 1.2 er aktiveret. På Windows Server 2012 server og nyere versioner, bør TLS 1.2 allerede være aktiveret.
```
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
```
3. Angiv denne registreringsdatabasenøgle for alle operativsystemer, og genstart serveren.
```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
"SchUseStrongCrypto"=dword:00000001
```
4. Hvis du vil aktivere TLS 1.2 mellem Synkroniser program serveren og en ekstern SQL Server, skal du derefter kontrollere, at du har de nødvendige versioner, der er installeret til [TLS 1.2 understøttelse af Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Forudsætninger for sammenslutning installation og konfiguration

### <a name="windows-remote-management"></a>Windows Fjernadministration
Når du bruger Azure AD-forbindelse for at installere Active Directory Federation Services eller webproxyen, kan du kontrollere kravene nedenfor for at sikre, at forbindelsen og konfiguration lykkes:

- Hvis serveren er tilsluttet sig-domæne, kan du sikre dig, at Windows Remote administrerede er aktiveret
    - I en øgede PSH kommandovindue skal du bruge kommandoen`Enable-PSRemoting –force`
- Hvis serveren er en ikke-domæne joinforbundne WAP maskine, er der et par ekstra krav
    - På den destinationsadresse maskine (WAP maskine):
         - Sikre, at winrm (Windows Fjernadministration / WS-Management) tjenesten kører via snap-in-tjenester
         - I en øgede PSH kommandovindue skal du bruge kommandoen`Enable-PSRemoting –force`
    - På den computer, som kører guiden (hvis den destinationsadresse maskine er ikke-domæne joinforbundne eller har tillid til domæne):
        - I en øgede PSH kommandovindue skal du bruge kommandoen`Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
        - I Server Manager:
             - føje DMZ WAP vært til maskine puljen (server manager -> Administrer -> Tilføj servere... Brug DNS-fanen)
             - Under fanen Server Manager alle servere: Højreklik på WAP server og vælge Administrer som... skal du angive lokale (ikke domæne) creds for WAP maskinen
             - Til at validere remote connectivity PSH i fanen Server Manager alle servere: Højreklik på WAP server og vælge Windows PowerShell.  En fjernsession PSH skal åbnes, for at sikre remote PowerShell-sessioner kan oprettes.

### <a name="ssl-certificate-requirements"></a>SSL-certifikat krav
**Vigtigt:** det har anbefales for at bruge det samme SSL-certifikat på tværs af alle knudepunkter på din ADFS-farm samt alle webprogram proxyservere.

- Certifikatet, der skal være en X509 certifikat.
- Du kan bruge et selvsigneret certifikat på sammenslutningsservere i et testmiljø. Men til et produktionsmiljø anbefales det, at du henter certifikatet fra et offentligt Nøglecenter.
    - Hvis bruger et certifikat, der ikke er offentligt der er tillid til, kan du sikre dig, at der er tillid til, på den lokale server og alle sammenslutningsservere det certifikat, der er installeret på hver Web Application Proxy-server
- Certifikatets identitet skal svare til navnet på sammenslutning-tjenesten (for eksempel sts.contoso.com).
    - Identitet er enten en emne alternativt navn (SAN) udvidelse af typen dNSName eller, hvis der er ingen SAN poster, emnenavnet angivet som et almindeligt navn.  
    - Flere SAN poster kan være til stede i certifikatet, forudsat de svarer til navnet på tjenesten sammenslutning.
    - Hvis du planlægger at bruge arbejdsplads Join, en ekstra SAN er påkrævet med værdien **enterpriseregistration.** efterfulgt af suffikset bruger vigtigste navn (UPN) i din organisation, for eksempel **enterpriseregistration.contoso.com**.
- Certifikater, der er baseret på CryptoAPI næste generering af (CNG) nøgler og vigtige lagerplads udbydere understøttes ikke. Det betyder, at du skal bruge et certifikat, der er baseret på en CSP (cryptographic service provider) og ikke en KSP (vigtige lagerplads provider).
- Jokertegn certifikater understøttes.

### <a name="name-resolution-for-federation-servers"></a>Navneoversættelse for sammenslutningsservere
- Konfigurere DNS-poster for AD FS sammenslutning navnet på tjenesten (fx sts.contoso.com) til både intranet (din interne DNS-server) og ekstranet (offentlige DNS gennem din domæneregistrator). På intranettet DNS record sikre, at du bruger A poster og ikke CNAME-poster. Dette er påkrævet til windows-godkendelse til at fungere korrekt fra computeren domæne joinforbundne.
- Hvis du anvender mere end én AD FS server eller Web Application Proxy-server, derefter sikre dig, at du har konfigureret din justering af belastning og, at DNS-posterne for AD FS sammenslutning navnet på tjenesten (for eksempel sts.contoso.com) peger på justering af belastning.
- Sørg for, at AD FS sammenslutning navnet på tjenesten (for eksempel sts.contoso.com) føjes til intranetzonen i Internet Explorer til windows-integreret godkendelse til at arbejde for browserprogrammer ved hjælp af Internet Explorer på intranettet. Dette kan styres via Gruppepolitik og installeres på dit domæne tilsluttet computere.

## <a name="azure-ad-connect-supporting-components"></a>Azure AD-forbindelse understøttende komponenter
Følgende er en liste over komponenter, der installeres Azure AD-forbindelse på den server, hvor Azure AD-forbindelse er installeret. Denne liste er til en grundlæggende hurtig installation.  Hvis du vælger at bruge en anden SQL Server på siden Installer synkronisering tjenester, er er SQL Express LocalDB ikke installeret lokalt.

- Azure AD Connect tilstand
- Microsoft Online Services-logon-assistenten fra for IT-fagfolk (installeret, men ingen afhængighed af det)
- Microsoft SQL Server 2012 kommandolinjeværktøjer
- Microsoft SQL Server 2012 udtrykkelig LocalDB
- Microsoft SQL Server 2012 Native Client
- Microsoft Visual C++ 2013 videredistribution pakke

## <a name="hardware-requirements-for-azure-ad-connect"></a>Hardwarekrav til Azure AD-forbindelse
Tabellen nedenfor viser minimumskravene til Azure AD-forbindelse synkronisering af computeren.

| Antallet af objekter i Active Directory | CPU | Hukommelse | Størrelse på harddisken |
| ------------------------------------- | --- | ------ | --------------- |
| Færre end 10.000 | 1,6 GHz | 4 GB | 70 GB |
| 10.000 – 50000 | 1,6 GHz | 4 GB | 70 GB |
| 50000 – 100.000 | 1,6 GHz | 16 GB | 100 GB |
| Den fulde version af SQL Server kræves for 100.000 eller flere objekter|  |  |  |
| 100.000 – 300.000 | 1,6 GHz | 32 GB | 300 GB |
| 300.000 – 600,000 | 1,6 GHz | 32 GB | 450 GB |
| Mere end 600,000 | 1,6 GHz | 32 GB | 500 GB |

Minimumkrav til computere, der kører AD FS eller Web Application Servers er følgende:

- CPU: To core 1,6 GHz eller højere
- HUKOMMELSE: 2GB eller nyere
- Azure VM: A2 konfiguration eller nyere

## <a name="next-steps"></a>Næste trin
Lær mere om at [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).
