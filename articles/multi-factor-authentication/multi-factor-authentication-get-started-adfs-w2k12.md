<properties
    pageTitle="MFA Server med Windows Server 2012 R2 AD FS | Microsoft Azure"
    description="I denne artikel beskrives, hvordan du kommer i gang med Azure Multi-Factor Authentication og AD FS i Windows Server 2012 R2."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>


# <a name="secure-your-cloud-and-on-premises-resources-using-azure-multi-factor-authentication-server-with-ad-fs-in-windows-server-2012-r2"></a>Secure dine skyen og lokale ressourcer, der bruger Azure Multi-Factor Authentication Server med AD FS i Windows Server 2012 R2

Hvis du bruger Active Directory Federation Services (AD FS) og vil sikre skyen eller lokale ressourcer, kan du konfigurere Azure Multi-Factor Authentication Server til at arbejde med AD FS. Denne konfiguration udløser totrinsbekræftelse for værdiskabende slutpunkter.

I denne artikel beskriver vi bruger Azure Multi-Factor Authentication Server med AD FS i Windows Server 2012 R2. Læs om, hvordan du [secure skyen og lokale ressourcer ved hjælp af Azure Multi-Factor Authentication Server med AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md)kan finde flere oplysninger.

## <a name="secure-windows-server-2012-r2-ad-fs-with-azure-multi-factor-authentication-server"></a>Secure Windows Server 2012 R2 AD FS med Azure Multi-Factor Authentication Server

Når du installerer Azure Multi-Factor Authentication Server, har du følgende indstillinger:

- Installer Azure Multi-Factor Authentication Server lokalt på den samme server som AD FS
- Installer Azure Multi-Factor Authentication kortet lokalt på AD FS-server, og derefter installere Multi-Factor Authentication Server på en anden computer

Før du begynder, skal du være opmærksom på følgende oplysninger:

- Du behøver ikke at installere Azure Multi-Factor Authentication Server på din ADFS-server. Men, skal du installere Multi-Factor Authentication kortet til AD FS på en Windows Server 2012 R2, der kører AD FS. Du kan installere serveren på en anden computer, hvis det er en understøttet version, og du installerer AD FS-kort separat på AD FS sammenslutning-serveren. Se følgende fremgangsmåder for at se, hvordan du installerer kortet separat.

- Når MFA Server AD FS-adapter er designet, blev det forventede, AD FS kunne overføre navnet på den afhængige part til kortet. Afhængige part navnet kan derefter bruges som et programnavn. Men dette slået ikke at være tilfældet. Hvis din organisation bruger SMS-besked eller mobilapp bekræftelse metoder, indeholder de strenge, der er defineret i indstillinger for virksomhed en pladsholder, <$*application_name*$>. Denne pladsholder erstattes ikke automatisk, når du bruger AD FS kortet. Vi anbefaler, at du fjerner pladsholderen fra de relevante strenge, når du sikrer AD FS.

- Den konto, du kan bruge til at logge på skal have rettigheder til at oprette sikkerhedsgrupper i Active Directory-tjenesten.

- Installationsguiden af Multi-Factor Authentication AD FS-adapter opretter en sikkerhedsgruppe, kaldet PhoneFactor Admins i din forekomst af Active Directory. Det føjer derefter din sammenslutning tjeneste AD FS servicekonto til denne gruppe. Vi anbefaler, at du kontrollerer på domænecontrolleren, gruppen PhoneFactor Admins faktisk er oprettet, og at AD FS-konto er medlem af denne gruppe. Hvis det er nødvendigt, tilføje manuelt servicekonto AD FS i gruppen PhoneFactor Admins på domænecontrolleren.

- Oplysninger om installation af Web Service SDK ved hjælp af brugeren portal, kan du læse om [implementering af portalen bruger til Azure Multi-Factor Authentication Server.](multi-factor-authentication-get-started-portal.md)


### <a name="install-azure-multi-factor-authentication-server-locally-on-the-ad-fs-server"></a>Installere Azure Multi-Factor Authentication Server lokalt på AD FS-server

1. Hent og Installer Azure Multi-Factor Authentication Server på din ADFS-server. Oplysninger om installation, kan du læse om [Introduktion til Azure Multi-Factor Authentication Server](multi-factor-authentication-get-started-server.md).
2. Klik på ikonet **AD FS** i Azure Multi-Factor Authentication Server management console, og derefter vælge indstillingerne, **Tillad bruger tilmelding** og **Tillad brugere at vælge metode**.
3. Vælg eventuelle andre indstillinger, du vil angive for organisationen.
4. Klik på **Installer AD FS Adapter**.
<center>![Skyen](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. Hvis vinduet Active Directory, der betyder to ting, der vises. Computeren er tilsluttet et domæne, og Active Directory-konfiguration til sikring af kommunikation mellem AD FS-kortet og multi-Factor godkendelsestjenesten er ufuldstændig. Klik på **Næste** for at fuldføre denne konfiguration automatisk, eller Vælg den **springe over automatisk konfiguration af Active Directory og konfigurere indstillinger manuelt** afkrydsningsfeltet, og klik derefter på **Næste**.
6. Hvis en lokal gruppe windows vises, betyder det to ting. Din computer er ikke tilsluttet et domæne, og den lokale gruppe konfiguration til sikring af kommunikation mellem AD FS-kortet og tjenesten Multi-Factor Authentication er ufuldstændig. Klik på **Næste** for at fuldføre denne konfiguration automatisk, eller Vælg den **springe over automatisk konfiguration af en lokal gruppe og konfigurere indstillinger manuelt** afkrydsningsfeltet, og klik derefter på **Næste**.
7. Klik på **Næste**i installationsguiden. Azure Multi-Factor Authentication Server opretter gruppen PhoneFactor Admins og føjer AD FS servicekonto til gruppen PhoneFactor Admins.
<center>![Skyen](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. Klik på **Næste**på siden **Start installationsprogrammet** .
9. Klik på **Næste**i Multi-Factor Authentication AD FS-adapter installationsprogram.
10. Klik på **Luk** , når installationen er fuldført.
11. Når kortet er blevet installeret, skal du registrere den med AD FS. Åbn Windows PowerShell og køre følgende kommando:<br>
    `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`
   <center>![Skyen](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. For at bruge dit nyligt registrerede kort skal du redigere politikken globale godkendelse i AD FS. Gå til noden **Politikker for godkendelse** i AD FS-administrationskonsollen. Klik på linket **Rediger** ud for afsnittet **Globale indstillinger** i sektionen **Multi-factor Authentication** . Vælg **Multi-Factor Authentication** som en yderligere godkendelsesmetode i vinduet **Rediger globale godkendelsespolitik** , og klik derefter på **OK**. Kortet er registreret som WindowsAzureMultiFactorAuthentication. Genstart AD FS-tjenesten for registreringen, træder i kraft.

<center>![Skyen](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

På dette tidspunkt er Multi-Factor Authentication Server konfigureret til at være en udbyder af yderligere godkendelse til brug med AD FS.

## <a name="install-a-standalone-instance-of-the-ad-fs-adapter-by-using-the-web-service-sdk"></a>Installere en enkeltstående forekomst af AD FS-adapter ved hjælp af Web Service SDK
1. Installere Web Service SDK på den server, der kører Multi-Factor Authentication Server.
2. Kopier følgende filer fra \Program Files\Multi-faktor-godkendelsesserver-katalog på serveren, som du planlægger at installere AD FS-adapter:
  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Registrer MultiFactorAuthenticationAdfsAdapter.ps1
  - Unregister MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.config
3. Kør installationsfilen MultiFactorAuthenticationAdfsAdapterSetup64.msi.
4. Multi-Factor Authentication AD FS-adapter installer, klikke på **Næste** for at starte installationen.
5. Klik på **Luk** , når installationen er fuldført.

## <a name="edit-the-multifactorauthenticationadfsadapterconfig-file"></a>Rediger filen MultiFactorAuthenticationAdfsAdapter.config

Følg disse trin for at redigere filen MultiFactorAuthenticationAdfsAdapter.config:

1. Angive noden **UseWebServiceSdk** til **Sand**.  
2. Angive værdien for **WebServiceSdkUrl** til URL-adressen til multi-Factor Authentication Web Service SDK. For eksempel: *https://contoso.com/&lt;certificatename&gt;/MultiFactorAuthWebServicesSdk/PfWsSdk.asmx*, hvor certificatename er navnet på dit certifikat.  
3. Redigere scriptet Register-MultiFactorAuthenticationAdfsAdapter.ps1 ved at tilføje *- ConfigurationFilePath &lt;stien&gt; * til slutningen af den `Register-AdfsAuthenticationProvider` kommandoen, hvor * &lt;stien&gt; * er den fulde sti til filen MultiFactorAuthenticationAdfsAdapter.config.

### <a name="configure-the-web-service-sdk-with-a-username-and-password"></a>Konfigurere Web Service SDK med et brugernavn og adgangskode

Der er to muligheder for at konfigurere Web Service SDK. Først med et brugernavn og adgangskode, og andet er med et klientcertifikat. Følg disse trin for den første indstilling, eller gå videre til andet.  

1. Angive værdien for **WebServiceSdkUsername** til en konto, der er medlem af gruppen PhoneFactor Admins sikkerhed. Brug den &lt;domæne&gt;& #92; &lt;brugernavn&gt; format.  
2. Angive værdien for **WebServiceSdkPassword** til den relevante kontoadgangskode.

### <a name="configure-the-web-service-sdk-with-a-client-certificate"></a>Konfigurere Web Service SDK med et klientcertifikat

Hvis du ikke vil bruge et brugernavn og din adgangskode, skal du følge disse trin for at konfigurere Web Service SDK med et klientcertifikat.

1. Få et klientcertifikat fra et nøglecenter for den server, der kører Web Service SDK. Lær, hvordan du [får fat klientcertifikater](https://technet.microsoft.com/library/cc770328.aspx).  
2. Importere klientcertifikat til den lokale computer personligt certifikat store på den server, der kører Web Service SDK. Sørg for, at den nøglecenter offentlige certifikat er i der er tillid til rodcertifikater certifikat store.  
3. Eksportere de offentlige og private nøgler af klientcertifikat til en .pfx-fil.  
4. Eksportere den offentlige nøgle i Base64 format til en .cer-fil.  
5. I Server Manager skal bekræfte, at funktionen Web Server (IIS) \Web Server\Security\IIS tilknytning af klientcertifikat er installeret. Hvis det ikke er installeret, kan du vælge **Tilføj roller og funktioner** til at føje denne funktion.  
6. I IIS Manager: Dobbeltklik **Configuration Editor** på det websted, der indeholder den virtuelle Web Service SDK-mappe. Det er vigtigt at gøre dette på niveauet for websted og ikke på niveauet for virtuel mappe.  
7. Gå til afsnittet **system.webServer/security/authentication/iisClientCertificateMappingAuthentication** .  
8. Angive aktiveret til **Sand**.  
9. Angive oneToOneCertificateMappingsEnabled til **Sand**.  
10. Klik på knappen **...** ud for oneToOneMappings, og klik derefter på linket **Tilføj** .  
11. Åbn den Base64 .cer-fil, du eksporterede tidligere. Fjerne *– BEGYNDER certifikat---*, *---AFSLUTTE certifikat –*og et linjeskift. Kopiere den resulterende streng.  
12. Angiv certifikat til den streng, der er kopieret i det foregående trin.  
13. Angive aktiveret til **Sand**.  
14. Angiv brugernavn til en konto, der er medlem af gruppen PhoneFactor Admins sikkerhed. Brug den &lt;domæne&gt;& #92; &lt;brugernavn&gt; format.  
15. Angiv adgangskoden til den relevante kontoadgangskode, og luk derefter Configuration Editor.  
16. Klik på linket **Anvend** .  
17. Dobbeltklik på **godkendelse**i den virtuelle Web Service SDK-mappe.  
18. Bekræft, at ASP.NET repræsentation og basisgodkendelse er indstillet til **aktiveret**, og at alle andre elementer er indstillet til **deaktiveret**.  
19. Dobbeltklik på **SSL indstillinger**i den virtuelle Web Service SDK-mappe.  
20. Angive klientcertifikater til **Acceptér**, og klik derefter på **Anvend**.  
21. Kopiér den .pfx-fil, du har eksporteret tidligere på serveren, der kører AD FS kortet.  
22. Importere .pfx-filen til den lokale computer personligt certifikat store.  
23. Højreklik, og vælg **Administrer Private nøgler**, og derefter give læseadgang til den konto, du brugte til at logge på tjenesten AD FS.  
24. Åbn klientcertifikat og kopiere miniature fra fanen **Detaljer** .  
25. Angive **WebServiceSdkCertificateThumbprint** i filen MultiFactorAuthenticationAdfsAdapter.config med strengen kopieret i det forrige trin.  


Til sidst skal køre \Program Files\Multi for at registrere kortet,-faktor godkendelse Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1 script i PowerShell. Kortet er registreret som WindowsAzureMultiFactorAuthentication. Genstart AD FS-tjenesten for registreringen, træder i kraft.

## <a name="related-topics"></a>Relaterede emner

Få hjælp til fejlfinding, se [Azure Multi-Factor Authentication ofte stillede spørgsmål](multi-factor-authentication-faq.md)
