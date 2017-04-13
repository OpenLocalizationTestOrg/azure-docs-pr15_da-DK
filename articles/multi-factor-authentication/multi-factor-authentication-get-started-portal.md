<properties 
    pageTitle="Implementering af portalen bruger til Azure Multi-Factor Authentication Server"
    description="Dette er den Azure Multi-Factor authentication side, der beskriver, hvordan du kommer i gang med Azure MFA og portalen bruger."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="kgremban"/>

# <a name="deploying-the-user-portal-for-the-azure-multi-factor-authentication-server"></a>Implementering af portalen bruger til Azure Multi-Factor Authentication Server

Portalen bruger kan administratoren for at installere og konfigurere Azure Multi-Factor Authentication User Portal. Portalen bruger er et IIS-websted, kan brugere at tilmelde dig Azure Multi-Factor Authentication og vedligeholde deres konti. En bruger kan ændre deres telefonnummer, ændre deres pinkode eller igennem Azure Multi-Factor Authentication under deres næste Log på.

Brugere kan log på portalen bruger ved hjælp af deres normal brugernavn og din adgangskode og kan enten Azure Multi-Factor Authentication kalder eller besvare spørgsmål om sikkerhed for at fuldføre deres godkendelse. Hvis brugeren tilmelding er tilladt, konfigurere en bruger deres telefonnummer og pinkode første gang de log på portalen bruger.

Bruger Portal-administratorer kan konfigurere og tilladelse til at tilføje nye brugere og opdatere eksisterende brugere.

<center>![Konfiguration](./media/multi-factor-authentication-get-started-portal/install.png)</center>

## <a name="deploying-the-user-portal-on-the-same-server-as-the-azure-multi-factor-authentication-server"></a>Implementering af portalen bruger på den samme server som Azure Multi-Factor Authentication Server

De følgende forudsætninger er påkrævet til installation af portalen brugere på samme server som Azure Multi-Factor Authentication Server:

- IIS skal være installeret herunder asp.net og IIS 6 metakoden base kompatibilitet (for IIS 7 eller nyere)
- Logget på bruger skal have administratorrettigheder for computeren og domæne, hvis det er relevant.  Dette skyldes, at kontoen skal have tilladelse til at oprette Active Directory-sikkerhedsgrupper.

### <a name="to-deploy-the-user-portal-for-the-azure-multi-factor-authentication-server"></a>Installere portalen bruger til Azure Multi-Factor Authentication Server

1. I Azure Multi-Factor Authentication Server: Klik på ikonet User Portal i menuen til venstre, skal du klikke installere User Portal.
1. Klik på Næste.
1. Klik på Næste.
1. Hvis computeren er tilsluttet et domæne og Active Directory-konfiguration til sikring af kommunikation mellem User Portal og Azure Multi-Factor godkendelsestjenesten er ufuldstændige, vises Active Directory-trinnet. Klik på knappen Næste for at fuldføre automatisk denne konfiguration.
1. Klik på Næste.
1. Klik på Næste.
1. Klik på Luk.
1. Åbn en webbrowser fra en hvilken som helst computer, og gå til den URL-adresse, hvor User Portal blev installeret (fx https://www.publicwebsite.com/MultiFactorAuth). Sørg for, at der vises ingen certifikat advarsler eller fejl.

<center>![Konfiguration](./media/multi-factor-authentication-get-started-portal/portal.png)</center>

## <a name="deploying-the-azure-multi-factor-authentication-server-user-portal-on-a-separate-server"></a>Implementere Azure Multi-Factor Authentication Server User Portal på en anden Server

For at kunne bruge Azure Multi-Factor Authentication App, skal der følgende kræves så appen korrekt kan kommunikere med User Portal:

Se Hardware- og softwarekrav for hardware- og softwarekrav:

- Du skal bruge v6.0 eller nyere af Azure Multi-Factor Authentication Server.
- User Portal skal være installeret på en forbindelse til internettet web-server, der kører Microsoft® Internet Information Services (IIS) 6, IIS 7.x eller nyere.
- Når du bruger IIS 6, sikre ASP.NET v2.0.50727 er installeret, registreret og angivet til tilladt.
- Påkrævet rolle services, når du bruger IIS 7.x eller højere omfatter ASP.NET og IIS 6 metabasekompatibilitet.
- User Portal skal være fastgjort med et SSL-certifikat.
- Azure Multi-Factor Authentication Web Service SDK skal være installeret i IIS 6, IIS 7.x eller nyere på den server, Azure Multi-Factor Authentication Server er installeret på.
- Azure Multi-Factor Authentication Web Service SDK skal være fastgjort med et SSL-certifikat.
- User Portal skal kunne oprette forbindelse til Azure Multi-Factor Authentication Web Service SDK via SSL.
- User Portal skal kunne godkendes i Azure Multi-Factor Authentication Web Service SDK hjælp af en tjenestekonto, der er medlem af en sikkerhedsgruppe, kaldet "PhoneFactor administratorer" legitimationsoplysninger. Denne tjenestekonto og gruppe findes i Active Directory, hvis Azure Multi-Factor Authentication Server kører på en server, der er medlem af et domæne. Denne tjenestekonto og gruppe findes lokalt på Azure Multi-Factor Authentication serveren Hvis det ikke er tilsluttet et domæne.

Installere portalen bruger på en server end Azure Multi-Factor Authentication serveren kræver følgende tre trin:

1. Installere webtjenesten SDK
2. Installere portalen bruger
3. Konfigurere indstillingerne for brugerkonti Portal Server Azure Multi-Factor Authentication


### <a name="install-the-web-service-sdk"></a>Installere webtjenesten SDK

Hvis Azure Multi-Factor Authentication Web Service SDK ikke allerede er installeret på Azure Multi-Factor Authentication serveren, gå til serveren, og Åbn Azure Multi-Factor Authentication Server. Klik på ikonet Web Service SDK skal du klikke på Installer Web Service SDK... knappen, og følg vejledningen præsenteres. Web Service SDK skal være fastgjort med et SSL-certifikat. Et selvsigneret certifikat er i orden hertil, men det har importeres til "Der er tillid til rodnøglecentre" store af den lokale computerkonto på webserveren User Portal, så det skal have tillid til certifikatet, når du starter SSL-forbindelse.

<center>![Konfiguration](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### <a name="install-the-user-portal"></a>Installere portalen bruger

Før du installerer portalen bruger på en separat server, skal være opmærksom på følgende:

- Det er praktisk til at åbne en webbrowser på internettet web-serveren, og gå til URL-adressen til den Web Service SDK, som er angivet i filen web.config. Hvis browseren kan kommer til webtjenesten, skal den beder dig om legitimationsoplysninger. Angiv brugernavn og adgangskode, der er angivet i filen web.config nøjagtigt, som den vises i filen. Sørg for, at der vises ingen certifikat advarsler eller fejl.
- Hvis en omvendt proxy- eller firewall sidder foran webserveren User Portal og udføre SSL overføre, kan du redigere filen User Portal web.config og tilføje følgende nøgle til den <appSettings> afsnit så portalen brugeren kan bruge http i stedet for https. <add key="SSL_REQUIRED" value="false"/>

#### <a name="to-install-the-user-portal"></a>Installere portalen bruger

1. Åbn Windows Stifinder på Azure Multi-Factor Authentication Server serveren, og Naviger til den mappe, hvor Azure Multi-Factor Authentication Server er installeret (fx C:\Program Files\Multi-faktor-godkendelsesserver). Vælge 32-bit eller 64-bit versionen af MultiFactorAuthenticationUserPortalSetup installationsfilen efter behov for serveren, User Portal installeres på. Kopiere installationsfilen på internettet-serveren.
2. På internettet web-serveren, skal du køre installationsfilen med administratorrettigheder. Den nemmeste måde at gøre dette er at åbne en kommandoprompt som administrator og gå til det sted, hvor installationsfilen blev kopieret.
3. Kør installationsfilen MultiFactorAuthenticationUserPortalSetup64, ændre navnet på webstedet og virtuel mappe, hvis du ønsker.
4. Når du afslutte installationen af User Portal, gå til C:\inetpub\wwwroot\MultiFactorAuth (eller relevante bibliotek, der er baseret på navnet på den virtuelle mappe) og redigere filen web.config.
5. Find USE_WEB_SERVICE_SDK nøgle, og Skift værdien fra false til true. Find tasterne WEB_SERVICE_SDK_AUTHENTICATION_USERNAME og WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD og angive værdierne til brugernavnet og adgangskoden til den tjenestekonto, som er medlem af PhoneFactor Admins sikkerheden gruppe (se afsnittet krav ovenfor). Sørg for at angive brugernavn og din adgangskode mellem anførselstegn i slutningen af linjen (værdi = "" / >). Det anbefales at bruge et kvalificeret brugernavn (fx domæne\brugernavn eller machine\username)
6. Finde indstillingen pfup_pfwssdk_PfWsSdk og ændre værdien fra "http://localhost:4898/PfWsSdk.asmx" til URL-adressen til den Web Service SDK, der kører på Azure Multi-Factor Authentication Server (fx https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Da SSL bruges til denne forbindelse, skal du henvise Web Service SDK ved servernavnet og ikke IP-adresse, da SSL-certifikat, der er udstedt til navnet på den server, og hvilken Webadresse skal svare til navnet på certifikatet. Hvis servernavnet fra internettet serveren ikke løser til en IP-adresse, skal du tilføje en post til hosts-filen på den pågældende server til at knytte navnet på den Azure Multi-Factor Authentication Server til dens IP-adresse. Gem filen web.config, når der er foretaget ændringer.
7. Hvis webstedet, User Portal blev installeret under (fx standardwebstedet) der ikke allerede er binded med et offentligt signeret certifikat, installere certifikatet på serveren Hvis den ikke allerede er installeret, kan du åbne IIS Manager og binde certifikatet til webstedet.
8. Åbn en webbrowser fra en hvilken som helst computer, og gå til den URL-adresse, hvor User Portal blev installeret (fx https://www.publicwebsite.com/MultiFactorAuth). Sørg for, at der vises ingen certifikat advarsler eller fejl.



## <a name="configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Konfigurere indstillingerne for brugerkonti portalen i Azure Multi-Factor Authentication Server
Nu hvor portalen er installeret, skal du konfigurere Azure Multi-Factor Authentication serveren for at arbejde med portalen.

Azure Multi-Factor Authentication server er der flere muligheder for portalen bruger.  Den følgende tabel indeholder en liste over disse indstillinger og en explaination af de bruges til.

Portalen brugerindstillinger|Beskrivelse|
:------------- | :------------- |
Bruger portalen URL-adresse| Kan du angive URL-adressen til hvor portalen hostet.
Primære godkendelse| Kan du angive typen godkendelse skal bruges, når du logger på portalen.  Windows, Radius eller LDAP-godkendelse.
Tillad brugere at logge på|Gør det muligt for brugerne at angive et brugernavn og din adgangskode på logonsiden til portalen bruger.  Hvis dette ikke er markeret, vil feltet være nedtonet.
Tillad bruger tilmelding|Gør det muligt for brugeren at tilmelde dig Multi-Factor authentication ved at tage dem til en konfigurationssiden, der beder dem om yderligere oplysninger såsom telefonnummer.  Spørg for sikkerhedskopiering phone giver brugerne mulighed at angive et alternativt telefonnummer.  Spørg efter fra tredjepart ed token giver brugerne mulighed at angive et 3 part ed token.
Tillad brugere at starte One-Time Spring| Dette gør det muligt for brugere at starte en enkeltstående Spring.  Hvis en bruger sæt dette op den træder i kraft, næste gang logger brugeren ind.  Spørg efter Spring sekunder giver brugeren med et felt, så de kan ændre standardværdien på 300 sekunder.  Ellers er enkeltstående Spring kun god til 300 sekunder.
Tillad brugere at vælge metode| Gør det muligt for brugerne angiver deres primære kontaktmetode.  Dette kan være telefonopkald, SMS-besked, mobilapp eller ed token.
Tillad brugere at vælge sprog|  Gør det muligt for brugeren at ændre det sprog, der bruges til det telefonopkald, SMS-besked, mobilapp eller ed token.
Tillad brugere at aktivere-mobilappen| Gør det muligt for brugerne at oprette en Aktiveringskode for at fuldføre aktiveringen-mobilappen, der bruges med serveren.  Du kan også angive antallet enheder, de kan du aktivere på.  Mellem 1 og 10.
Bruge spørgsmål om sikkerhed til reserve|Kan du bruge spørgsmål om sikkerhed i tilfælde af Multi-Factor-godkendelse mislykkes.  Du kan angive antallet spørgsmål om sikkerhed, der skal besvares korrekt.
Tillad brugere at knytte fra tredjepart ed token| Gør det muligt for brugerne at angive et tredjeparts ed token.
Brug ed token som reserve|Giver mulighed for brug af et ed token i tilfælde, at Multi-Factor godkendelse ikke er lykkes.  Du kan også angive timeout for session i minutter.
Aktivere logføring|Gør det muligt at logge på portalen bruger.  Logfilerne er placeret på: C:\Program Files\Multi-faktor-godkendelse Server\Logs.

Fleste af disse indstillinger er synlige for brugeren, når de er aktiveret og bruger fortegnene i portalen bruger.

![Portalen brugerindstillinger](./media/multi-factor-authentication-get-started-portal/portalsettings.png)



### <a name="to-configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Du kan konfigurere indstillingerne for brugerkonti portalen i Azure Multi-Factor Authentication Server




1. Klik på ikonet User Portal i Azure Multi-Factor Authentication Server. Angiv URL-adressen til portalen bruger i tekstfeltet URL-adresse bruger Portal under fanen Indstillinger. Denne URL-adresse, der skal indsættes i mails, der sendes til brugere, når de er importeret til Azure Multi-Factor Authentication Server, hvis e-mail-funktionen er aktiveret.
2. Vælg de indstillinger, du vil bruge i portalen bruger. Eksempelvis hvis brugere har tilladelse til at styre deres godkendelsesmetoder, sikre dig, at Tillad brugere at vælge metode er markeret sammen med de metoder, de kan vælge mellem.
3. Klik på linket Hjælp i øverste højre hjørne for at få hjælp, om nogen af de viste indstillinger.

<center>![Konfiguration](./media/multi-factor-authentication-get-started-portal/config.png)</center>


## <a name="administrators-tab"></a>Under fanen administratorer
Denne fane blot kan du tilføje brugere, der har administratorrettigheder.  Når du tilføjer en administrator, kan du finjustere de tilladelser, de modtager.  Denne måde, kan du være sikker på, at kun give de nødvendige tilladelser til administratoren.  Blot klikke på knappen Tilføj og derefter vælge og bruger og deres tilladelser, og klik derefter på Tilføj.

![Bruger portalen administratorer](./media/multi-factor-authentication-get-started-portal/admin.png)


## <a name="security-questions"></a>Spørgsmål om sikkerhed
Denne fane kan du angive spørgsmål om sikkerhed, som brugerne skal give svar på, hvis brug sikkerhedsspørgsmål til fallback indstilling er markeret.  Azure Multi-Factor Authenticaton Server leveres med standardspørgsmål, du kan bruge.  Du kan også ændre rækkefølgen, eller Tilføj dine egne spørgsmål.  Når du tilføjer dine egne spørgsmål, kan du angive det sprog, du vil have disse spørgsmål, der skal vises i samt.

![Spørgsmål om bruger portalen sikkerhed](./media/multi-factor-authentication-get-started-portal/secquestion.png)


## <a name="passed-sessions"></a>Sendte sessioner

## <a name="saml"></a>SAML
Kan du konfigurere portalen bruger for at acceptere krav fra identitetsudbyder ved hjælp af SAML.  Du kan angive timeout sessionen skal du angive bekræftelse certifikatet, og Log af Omdiriger URL-adresse.

![SAML](./media/multi-factor-authentication-get-started-portal/saml.png)

## <a name="trusted-ips"></a>Der er tillid til IP'er
Denne fane kan du angive enten enkelt IP-adresser eller IP-adresseområder, der kan føjes, så hvis en bruger logget ind fra en af disse IP-adresser, derefter Multi-Factor authentication er tilsidesat.

![Bruger portal IP-adresser, der er tillid til](./media/multi-factor-authentication-get-started-portal/trusted.png)

## <a name="self-service-user-enrollment"></a>Selvbetjening bruger tilmelding
Hvis du vil dine brugere at logge på og tilmelde dig skal du vælge afkrydsningsfeltet Tillad brugere at logge på og tillade tilmelding brugerindstillinger. Husk, at de indstillinger, du vælger vil påvirke logon brugeroplevelsen.

For eksempel, når en bruger logger på portalen bruger og klikker på knappen Log på, føres de til siden Azure Multi-Factor Authentication bruger konfiguration.  Afhængigt af hvordan du har konfigureret Azure Multi-Factor Authentication, kan brugeren ikke kunne vælge deres godkendelsesmetode.  

Vælg den stemme ringe godkendelsesmetode eller er allerede konfigureret til at bruge denne metode, spørger siden brugeren skal angive deres primære telefonnummer og et lokalnummer, hvis det er relevant.  De kan også have adgang til at angive et ekstra telefonnummer.  

![Bruger portal IP-adresser, der er tillid til](./media/multi-factor-authentication-get-started-portal/backupphone.png)

Hvis brugeren skal bruge en pinkode, når de godkender, vil siden også bede dem om at angive en pinkode.  Når du har angivet deres telefonnummer og pinkode (hvis relevant), brugeren klikker på den ringe mig nu Godkend knap.  Azure Multi-Factor Authentication foretager et telefonopkald godkendelse til brugerens primære telefonnummer.  Brugeren skal besvare opkaldet, og skriv deres pinkode (hvis relevant) og tryk på # til at gå videre til næste trin i selvstændig tilmeldingsprocessen.   

Hvis brugeren markerer godkendelsesmetode SMS-tekst eller er allerede konfigureret til at bruge denne metode, spørger siden brugeren til deres mobiltelefonnummer.  Hvis brugeren skal bruge en pinkode, når de godkender, vil siden også bede dem om at angive en pinkode.  Når du har angivet deres telefonnummer og pinkode (hvis relevant), brugeren klikker på den tekst mig nu Godkend knap.  Azure Multi-Factor Authentication udfører en SMS-godkendelse til brugerens mobiltelefon.  Brugeren skal modtage SMS som indeholder en en-klokkeslæt-adgangskode (OTP) og svare på meddelelsen med OTP samt deres pinkode, hvis det er relevant) til at gå videre til næste trin i selvstændig tilmeldingsprocessen.

![Bruger portal SMS](./media/multi-factor-authentication-get-started-portal/text.png)   

Hvis brugeren markerer mobilapp godkendelsesmetoden eller er allerede konfigureret til at bruge denne metode, spørger siden brugeren om at installere appen Azure Multi-Factor Authentication på deres enhed og generere en Aktiveringskode.  Når du har installeret appen Azure Multi-Factor Authentication, brugeren klikker på knappen Generér Aktiveringskode.    

>[AZURE.NOTE]For at bruge appen Azure Multi-Factor Authentication, skal brugeren aktivere pushmeddelelser for deres enhed.

Siden viser derefter en Aktiveringskode og en URL-adresse sammen med en stregkode billede.  Hvis brugeren skal bruge en pinkode, når de godkender, vil siden også bede dem om at angive en pinkode.  Brugeren skriver Aktiveringskode og URL-adresse i appen Azure Multi-Factor Authentication eller bruger stregkode scanneren til at scanne stregkode billede og klikker på knappen Aktivér.    

Når aktiveringen er fuldført, skal brugeren klikker på knappen godkende mig nu.  Azure Multi-Factor Authentication udfører en godkendelse til brugerens-mobilappen.  Brugeren skal angive deres pinkode (hvis relevant) og trykke på knappen Godkend i deres-mobilappen til at gå videre til næste trin i selvstændig tilmeldingsprocessen.  


Hvis administratorer har konfigureret Azure Multi-Factor Authentication serveren for at indsamle sikkerhedsspørgsmål og svar, hentes brugeren derefter til siden spørgsmål om sikkerhed.  Brugeren skal vælge fire spørgsmål om sikkerhed og give svar på de markerede spørgsmål.    

![Spørgsmål om bruger portalen sikkerhed](./media/multi-factor-authentication-get-started-portal/secq.png)  

Bruger selvstændig tilmelding er nu færdig, og brugeren er logget på portalen bruger.  Brugerne kan logge på tilbage til portalen bruger når som helst i fremtiden at ændre deres telefonnumre, ben, godkendelsesmetoder og spørgsmål om sikkerhed Hvis tillader deres administratorer.
