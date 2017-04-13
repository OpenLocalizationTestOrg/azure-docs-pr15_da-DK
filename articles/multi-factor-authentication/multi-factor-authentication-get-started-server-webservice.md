<properties 
    pageTitle="Kom godt i gang MFA Server Mobile-App-webtjeneste"
    description="Azure Multi-Factor Authentication App giver mulighed for yderligere out-of-band-godkendelse.  Det kan MFA serveren for at bruge pushmeddelelser til brugere."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="getting-started-the-mfa-server-mobile-app-web-service"></a>Kom godt i gang MFA Server Mobile-App-webtjeneste

Azure Multi-Factor Authentication App giver mulighed for yderligere out-of-band-godkendelse. I stedet for at placere et automatiseret telefonopkald eller SMS til brugeren ved logon, flytter Azure Multi-Factor Authentication en meddelelse til Azure Multi-Factor Authentication App på brugerens smartphone eller tablet. Brugeren skal du blot trykker "Godkend" (eller indsætter en pinkode og trykker "Godkend") i appen for at logge på.

For at kunne bruge Azure Multi-Factor Authentication App, skal der følgende kræves så appen korrekt kan kommunikere med Mobile App-webtjeneste:

- Se Hardware- og softwarekrav til hardware- og softwarekrav
- Du skal bruge v6.0 eller nyere på den Server, Azure Multi-Factor Authentication
- Mobile-App-webtjeneste skal være installeret på en forbindelse til internettet web-server, der kører Microsoft® Internet Information Services (IIS) IIS 7.x eller nyere.  Du kan finde flere oplysninger om IIS [IIS.NET](http://www.iis.net/).
- Kontrollér ASP.NET v4.0.30319 er installeret, registreret og angivet til tilladt
- Påkrævet rolle services omfatter ASP.NET og IIS 6 metabasekompatibilitet
- Mobile-App-webtjeneste skal være tilgængeligt via en offentlig URL-adresse
- Mobile-App-webtjeneste skal være fastgjort med et SSL-certifikat.
- Azure Multi-Factor Authentication Web Service SDK skal være installeret i IIS 7.x eller nyere på serveren, Azure Multi-Factor Authentication Server
- Azure Multi-Factor Authentication Web Service SDK skal være fastgjort med et SSL-certifikat.
- Mobile-App-webtjeneste skal kunne oprette forbindelse til Azure Multi-Factor Authentication Web Service SDK via SSL
- Mobile-App-webtjeneste skal kunne godkendes i Azure Multi-Factor Authentication Web Service SDK hjælp af en tjenestekonto, der er medlem af en sikkerhedsgruppe, kaldet "PhoneFactor administratorer" legitimationsoplysninger. Denne tjenestekonto og gruppe findes i Active Directory, hvis Azure Multi-Factor Authentication Server kører på en server, der er medlem af et domæne. Denne tjenestekonto og gruppe findes lokalt på Azure Multi-Factor Authentication serveren Hvis det ikke er tilsluttet et domæne.


Installere portalen bruger på en server end Azure Multi-Factor Authentication serveren kræver følgende tre trin:

1. Installere webtjenesten SDK
2. Installere webtjeneste-mobilappen
3. Konfigurere indstillingerne for mobilapp i Azure Multi-Factor Authentication Server
4. Aktivere Azure Multi-Factor Authentication app'en for slutbrugere

## <a name="install-the-web-service-sdk"></a>Installere webtjenesten SDK

Hvis Azure Multi-Factor Authentication Web Service SDK ikke allerede er installeret på Azure Multi-Factor Authentication serveren, gå til serveren, og Åbn Azure Multi-Factor Authentication Server. Klik på ikonet Web Service SDK skal du klikke på Installer Web Service SDK... knappen, og følg vejledningen præsenteres. Web Service SDK skal være fastgjort med et SSL-certifikat. Et selvsigneret certifikat er i orden hertil, men det har importeres til "Der er tillid til rodnøglecentre" store af den lokale computerkonto på webserveren User Portal, så det skal have tillid til certifikatet, når du starter SSL-forbindelse.

<center>![Konfiguration](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)</center>

## <a name="install-the-mobile-app-web-service"></a>Installere webtjeneste-mobilappen
Før du installerer mobilapp-webtjeneste, du være opmærksom på følgende:

- Hvis portalen Azure Multi-Factor Authentication brugeren allerede er installeret på serveren til internettet, kan det brugernavn, din adgangskode og URL-adressen til Web Service SDK kopieres fra portalen bruger web.config fil.
- Det er praktisk til at åbne en webbrowser på internettet web-serveren, og gå til URL-adressen til den Web Service SDK, som er angivet i filen web.config. Hvis browseren kan kommer til webtjenesten, skal den beder dig om legitimationsoplysninger. Angiv brugernavn og adgangskode, der er angivet i filen web.config nøjagtigt, som den vises i filen. Sørg for, at der vises ingen certifikat advarsler eller fejl.
- Hvis en omvendt proxy- eller firewall sidder foran webserveren Mobile App-webtjeneste og udføre SSL overføre, kan du redigere filen Mobile App-webtjeneste web.config og tilføje følgende nøgle til den <appSettings> afsnit, så den Mobile App webtjeneste kan bruge http i stedet for https. SSL er dog stadig kræves fra den Mobile-App til firewall/reverse proxy. <add key="SSL_REQUIRED" value="false"/>

### <a name="to-install-the-mobile-app-web-service"></a>Installere webtjeneste-mobilappen

<ol>
<li>Åbn Windows Stifinder på Azure Multi-Factor Authentication serveren, og Naviger til den mappe, hvor Azure Multi-Factor Authentication Server er installeret (fx C:\Program Files\Azure Multi-Factor Authentication). Vælge 32-bit eller 64-bit version af Azure Multi-Factor AuthenticationPhoneAppWebServiceSetup installationsfilen efter behov for serveren, Mobile App-webtjeneste, der skal installeres på. Kopiere installationsfilen på internettet-serveren.</li>

<li>På internettet web-serveren, skal du køre installationsfilen med administratorrettigheder. Den nemmeste måde at gøre dette er at åbne en kommandoprompt som administrator og gå til det sted, hvor installationsfilen blev kopieret.</li>  

<li>Kør installationsfilen Multi-Factor AuthenticationMobileAppWebServiceSetup, ændre webstedet, hvis det er nødvendigt, og Skift den virtuelle mappe til et kort navn som "PA". Et kort virtuelle mappenavn anbefales, da brugerne skal indtaste den Mobile App Service URL-adresse i den mobile enhed under aktivering.</li>

<li>Når du afslutte installationen af Azure Multi-Factor AuthenticationMobileAppWebServiceSetup, gå til C:\inetpub\wwwroot\PA (eller relevante bibliotek, der er baseret på navnet på den virtuelle mappe) og redigere filen web.config.</li>  

<li>Find tasterne WEB_SERVICE_SDK_AUTHENTICATION_USERNAME og WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD og angive værdierne til brugernavnet og adgangskoden til den tjenestekonto, som er medlem af PhoneFactor Admins sikkerheden gruppe (se afsnittet krav ovenfor). Det kan være den samme konto, der bruges som Azure Multi-Factor Authentication User Portal identitet, hvis, der tidligere er installeret. Sørg for at angive brugernavn og din adgangskode mellem anførselstegn i slutningen af linjen (værdi = "" / >). Det anbefales at bruge et kvalificeret brugernavn (fx domæne\brugernavn eller machine\username).</li>  

<li>Finde indstillingen pfMobile App Web Service_pfwssdk_PfWsSdk og ændre værdien fra "http://localhost:4898/PfWsSdk.asmx" til URL-adressen til den Web Service SDK, der kører på Azure Multi-Factor Authentication Server (fx https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Da SSL bruges til denne forbindelse, skal du henvise Web Service SDK ved servernavnet og ikke IP-adresse, da SSL-certifikat, der er udstedt til navnet på den server, og hvilken Webadresse skal svare til navnet på certifikatet. Hvis servernavnet fra internettet serveren ikke løser til en IP-adresse, skal du tilføje en post til hosts-filen på den pågældende server til at knytte navnet på den Azure Multi-Factor Authentication Server til dens IP-adresse. Gem filen web.config, når der er foretaget ændringer.</li>  

<li>Hvis webstedet, Mobile App-webtjeneste blev installeret under (fx standardwebstedet) der ikke allerede er binded med et offentligt signeret certifikat, installere certifikatet på serveren Hvis den ikke allerede er installeret, kan du åbne IIS Manager og binde certifikatet til webstedet.</li>  

<li>Åbn en webbrowser fra en hvilken som helst computer, og gå til den URL-adresse, hvor Mobile App-webtjeneste er installeret (fx https://www.publicwebsite.com/PA). Sørg for, at der vises ingen certifikat advarsler eller fejl.</li>

### <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Konfigurere indstillingerne for mobilapp i Azure Multi-Factor Authentication Server
Nu hvor webtjenesten mobil-app'en er installeret, skal du konfigurere Azure Multi-Factor Authentication serveren for at arbejde med portalen.

#### <a name="to-configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Du kan konfigurere indstillingerne for mobilapp i Azure Multi-Factor Authentication Server

1. Klik på ikonet User Portal i Azure Multi-Factor Authentication Server. Hvis brugerne har tilladelse til at styre deres godkendelsesmetoder, under fanen Indstillinger under Tillad brugere at vælge metode, se Mobile-App. Uden at denne funktion er aktiveret, skal slutbrugere du kontakte din helpdesk for at fuldføre aktiveringen for Mobile-App.
2. Markér afkrydsningsfeltet Tillad brugere for at aktivere Mobile-App boks.
3. Markér feltet Tillad bruger tilmelding.
4. Klik på ikonet Mobile-App.
5. Angiv den URL-adresse, som bruges sammen med den virtuelle mappe, som blev oprettet, når du installerer Azure Multi-Factor AuthenticationMobileAppWebServiceSetup. Du kan angive et kontonavn i det relevante felt. Denne virksomhedens navn vises i programmet mobile. Hvis tomt, vises navnet på din Multi-Factor Auth udbyder, der er oprettet i portalen Azure administration.



<center>![Konfiguration](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>
