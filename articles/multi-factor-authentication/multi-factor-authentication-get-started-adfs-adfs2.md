<properties
    pageTitle="Brug af Azure MFA Server med AD FS 2.0 | Microsoft Azure"
    description="Dette er den Azure Multi-Factor authentication side, der beskriver, hvordan du kommer i gang med Azure MFA og AD FS 2.0."
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

# <a name="secure-cloud-and-on-premises-resources-using-azure-multi-factor-authentication-server-with-ad-fs-20"></a>Secure skyen og lokale ressourcer ved hjælp af Azure Multi-Factor Authentication Server med AD FS 2.0

Denne artikel gælder for virksomheder, der er knyttet til Azure Active Directory, og vil sikre ressourcer, der er lokalt eller i skyen. Beskytte dine ressourcer ved hjælp af Azure Multi-Factor Authentication Server og konfigurere det til at arbejde med AD FS, så totrinsbekræftelse udløses for høj værdi slutpunkter.

Denne dokumentation dækker ved hjælp af Azure Multi-Factor Authentication Server med AD FS 2.0.  Få flere oplysninger om [sikring af-sky og lokale ressourcer ved hjælp af Azure Multi-Factor Authentication Server med Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md).


## <a name="secure-ad-fs-20-with-a-proxy"></a>Secure AD FS 2.0 med en proxy
Installer Azure Multi-Factor Authentication Server på ADFS-proxy-server for at sikre AD FS 2.0 med en proxy, og Konfigurer serveren.

### <a name="configure-iis-authentication"></a>Konfigurere IIS-godkendelse

1. Klik på ikonet **IIS-godkendelse** i menuen til venstre i Azure Multi-Factor Authentication Server.
2. Klik på fanen **Formularbaserede** .
3. Klik på **Tilføj...** knappen.
<center>![Konfiguration](./media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>
4. Finde brugernavn, din adgangskode og domæne variabler automatisk, angive Login URL-adressen (som https://sso.contoso.com/adfs/ls) i dialogboksen Auto-Configure Form-Based websted, og klik på OK.
5. Markér afkrydsningsfeltet **Kræv Azure Multi-Factor Authentication bruger stemmer overens** , hvis alle brugere har været eller der skal importeres til serveren og underlagt totrinsbekræftelse. Hvis et stort antal brugere endnu ikke er blevet importeret til serveren og/eller bliver undtaget fra totrinsbekræftelse, Lad feltet være umarkerede. Du kan finde flere oplysninger om denne funktion skal Hjælp-filen.
6. Hvis siden variabler ikke kan registreres automatisk, skal du klikke på **Angiv manuelt...** knappen i dialogboksen Auto-Configure Form-Based websted.
7. Angiv URL-adressen til siden ADFS login i feltet Send URL-adresse (som https://sso.contoso.com/adfs/ls), og Angiv navnet på et program (valgfrit) i dialogboksen Add Form-Based websted. Navnet på programmet vises i Azure Multi-Factor Authentication rapporter og kan vises i SMS eller Mobile-App godkendelse meddelelser. Se Hjælp-filen kan finde flere oplysninger under sende URL-adressen.
8. Angive formatet for anmodning om at "Skrive eller få".
9. Angiv brugernavn variabel (ctl00$ ContentPlaceHolder1$ UsernameTextBox) og adgangskode variabel (ctl00$ ContentPlaceHolder1$ PasswordTextBox). Hvis siden formularbaserede login viser en domæne tekstboks, skal du angive variablen domæne. Du skal muligvis gå til siden login i en webbrowser, skal du højreklikke på siden, og vælg **Vis kilde** for at finde navnene på felterne input i logonsiden.
10. Markér afkrydsningsfeltet **Kræv Azure Multi-Factor Authentication bruger stemmer overens** , hvis alle brugere har været eller der skal importeres til serveren og underlagt totrinsbekræftelse. Hvis et stort antal brugere endnu ikke er blevet importeret til serveren og/eller bliver undtaget fra totrinsbekræftelse, Lad feltet være umarkerede.
<center>![Konfiguration](./media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>
11. Klik på **Avanceret...** knappen Gennemse avancerede indstillinger. Du kan konfigurere indstillinger, herunder også muligheden for at vælge en brugerdefineret nægtelse siden fil til cachen vellykket godkendelse til webstedet ved hjælp af cookies, og vælg, hvordan skal godkende de primære legitimationsoplysninger.
12. Da proxyserveren ADFS ikke sandsynligvis være knyttet til domænet, kan du bruge LDAP at oprette forbindelse til domænecontrolleren til import af brugers og før godkendelse. Klik på fanen **Primære godkendelse** , og vælg **LDAP Bind** før godkendelse godkendelse type i dialogboksen Advanced Form-Based websted.
13. Når du er færdig, skal du klikke på knappen **OK** for at vende tilbage til dialogboksen Add Form-Based websted. Se Hjælp-filen kan finde flere oplysninger på siden Avancerede indstillinger.
14. Klik på knappen **OK** for at lukke dialogboksen.
15. Når de URL-adresse og siden variabler har fundet eller har angivet, viser dataene websted i panelet formularbaserede.
16. Klik på fanen **Oprindelige modul** , og vælg serveren, det websted, der kører ADFS-proxy under (som "standardwebstedet") eller programmet ADFS-proxy (som "ls" under "adfs") til at aktivere IIS-plug-in'en på det ønskede niveau.
17. Klik på feltet **aktivere IIS-godkendelse** øverst på skærmen.
18. IIS-godkendelse er nu aktiveret.

### <a name="configure-directory-integration"></a>Konfigurere katalogintegration

Du har aktiveret IIS godkendelse, men for at udføre den foreløbige godkendelse til din Active Directory (AD) via LDAP skal du konfigurere LDAP-forbindelse til domænecontrolleren.

1. Klik på ikonet **Katalogintegration** .
2. Vælg alternativknappen **Brug bestemte LDAP-konfiguration** på fanen Indstillinger.
<center>![Konfiguration](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>
3. Klik på **Rediger...** knappen.
4. I dialogboksen Rediger LDAP-konfiguration skal du udfylde felterne med de oplysninger, der kræves for at oprette forbindelse til domænecontrolleren AD. Beskrivelser af felterne, der medtages i tabellen nedenfor. Disse oplysninger er også medtaget i Azure Multi-Factor Authentication Server Hjælp-filen.
5. Test LDAP-forbindelse ved at klikke på knappen **Test** .
<center>![Konfiguration](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>
6. Hvis LDAP-forbindelse der er oprettet, kan du klikke på knappen **OK** .

### <a name="configure-company-settings"></a>Konfigurere indstillinger for virksomhed

1. Derefter skal du klikke på ikonet **Indstillinger for virksomhed** , og vælg fanen **Brugernavn opløsning** .
2. Vælg alternativknappen **Brug LDAP entydigt id attribut for matchende brugernavne** .
3. Hvis brugerne angiver deres brugernavn i formatet "domæne\brugernavn", skal serveren, der skal kunne derpå fjerne domænet fra brugernavnet, når programmet opretter LDAP-forespørgslen. Der kan udføres via en indstilling i registreringsdatabasen.
4. Åbn Registreringseditor og gå til HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/Positive netværk/PhoneFactor på en 64-bit-server. Tage "Wow6432Node" af stien, hvis du er på en 32-bit-server. Oprette en DWORD-registreringsdatabasenøgle kaldet "UsernameCxz_stripPrefixDomain" og angive værdien til 1. Azure Multi-Factor Authentication nu sikring af ADFS-proxy.

Sørg for, at brugere er blevet importeret fra Active Directory til serveren. Se, [der er tillid til IP'er afsnit](#trusted-ips) , hvis du vil gerne whitelist interne IP-adresser, så totrinsbekræftelse ikke er påkrævet, når du logger på webstedet fra disse placeringer.

<center>![Konfiguration](./media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## <a name="ad-fs-20-direct-without-a-proxy"></a>AD FS 2.0 direkte uden en proxy

Du kan sikre AD FS når AD FS-proxy ikke bruges. Installer Azure Multi-Factor Authentication Server på AD FS server og Konfigurer serveren i følgende trin:

1. Klik på ikonet **IIS-godkendelse** i menuen til venstre i Azure Multi-Factor Authentication Server.
2. Klik på fanen **HTTP** .
3. Klik på **Tilføj...** knappen.
4. Angiv URL-adressen for det ADFS-websted, hvor der udføres HTTP-godkendelse (som https://sso.domain.com/adfs/ls/auth/integrated) i feltet Base URL-adresse i dialogboksen Tilføj Base URL-adresse for. Indtast navnet på et program (valgfrit). Navnet på programmet vises i Azure Multi-Factor Authentication rapporter og kan vises i SMS eller Mobile-App godkendelse meddelelser.
5. Hvis du vil, justere inaktiv timeout og maksimum session gange.
6. Markér afkrydsningsfeltet **Kræv Azure Multi-Factor Authentication bruger stemmer overens** , hvis alle brugere har været eller der skal importeres til serveren og underlagt totrinsbekræftelse. Hvis et stort antal brugere endnu ikke er blevet importeret til serveren og/eller bliver undtaget fra totrinsbekræftelse, Lad feltet være umarkerede. Du kan finde flere oplysninger om denne funktion skal Hjælp-filen.
7. Markér feltet cookie cachen, hvis du ønsker.
<center>![Konfiguration](./media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>
8. Klik på knappen **OK** .
9. Klik på fanen **Oprindelige modul** , og vælg serveren, webstedet (som "standardwebstedet") eller ADFS-program (som "ls" under "adfs") til at aktivere IIS-plug-in'en på det ønskede niveau.
10. Klik på feltet **aktivere IIS-godkendelse** øverst på skærmen. Azure Multi-Factor Authentication nu sikring af ADFS.

Sørg for, at brugere er blevet importeret fra Active Directory til serveren. I afsnittet der er tillid til IP-adresser, hvis du vil gerne whitelist interne IP-adresser, så totrinsbekræftelse ikke er påkrævet, når du logger på webstedet fra disse placeringer.


## <a name="trusted-ips"></a>Der er tillid til IP'er
Der er tillid til IP'er Tillad brugere at tilsidesætte Azure Multi-Factor Authentication for anmodninger om websted, der kommer fra bestemte IP-adresser eller undernet. For eksempel kan du få undtaget brugere fra totrinsbekræftelse når de logge på fra office. Dette, skal du angive office-undernettet som en der er tillid til IP'er post.

### <a name="to-configure-trusted-ips"></a>Sådan konfigureres der er tillid til IP'er


1. Klik på fanen **IP'er, der er tillid til** i sektionen IIS-godkendelse.
1. Klik på **Tilføj...** knappen.
1. Når dialogboksen Tilføj der er tillid til IP'er vises, skal du vælge en af alternativknapper **Enkelt IP-adresse**, **IP-område**eller **undernet** .
1. Angiv den IP-adresse, område af IP-adresser eller undernet, der skal være whitelisted. Hvis indtaste et undernet, Vælg den relevante netmaske, og klik på knappen **OK** . Der er tillid til IP-adresse er nu tilføjet.


<center>![Konfiguration](./media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>
