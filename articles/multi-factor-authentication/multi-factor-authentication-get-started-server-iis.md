<properties 
    pageTitle="IIS-godkendelse og Azure Multi-Factor Authentication Server"
    description="Dette er den Azure Multi-Factor authentication side, der hjælper med at implementere IIS-godkendelse og Azure Multi-Factor Authentication Server."
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
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="iis-authentication"></a>IIS-godkendelse

Afsnittet IIS-godkendelse i Azure Multi-Factor Authentication Server kan du aktivere og konfigurere IIS-godkendelse til integration med Microsoft IIS webprogrammer. Azure Multi-Factor Authentication Server installerer et plug-in, kan filtrere anmodninger om henvisninger til IIS-webserveren for at tilføje Azure Multi-Factor Authentication. Plug-in'en IIS yder support til formularbaseret godkendelse og integreret Windows HTTP-godkendelse. Der er tillid til IP'er kan også være konfigureret til undtaget interne IP-adresser fra to faktor-godkendelse.


![IIS-godkendelse](./media/multi-factor-authentication-get-started-server-iis/iis.png)


## <a name="using-form-based-iis-authentication-with-azure-multi-factor-authentication-server"></a>Ved hjælp af IIS formularbaseret godkendelse med Azure Multi-Factor Authentication Server

Installer Azure Multi-Factor Authentication Server på webserveren IIS for at sikre et IIS-webprogram, der anvender formularbaseret godkendelse, og Konfigurer serveren i den følgende procedure.

1. Klik på ikonet IIS-godkendelse i menuen til venstre i Azure Multi-Factor Authentication Server.
2. Klik på fanen formularbaserede.
3. Klik på Tilføj... knappen.
4. For at registrere brugernavn, adgangskode og domæne variabler automatisk, angive Login URL-adressen (fx https://localhost/contoso/auth/login.aspx) i dialogboksen Auto-Configure Form-Based websted og klik på OK.
5. Markér afkrydsningsfeltet Kræv Multi-Factor Authentication bruger match, hvis alle brugere har været eller der skal importeres til serveren og underlagt Multi-Factor authentication. Hvis et stort antal brugere endnu ikke er blevet importeret til serveren og/eller bliver undtaget fra Multi-Factor authentication, Lad feltet være umarkerede.
6. Hvis siden variabler ikke kan registreres automatisk, skal du klikke på den angive manuelt... knappen i dialogboksen Auto-Configure Form-Based websted.
7. Angiv URL-adressen til siden login i feltet Send URL-adresse, og Angiv navnet på et program (valgfrit) i dialogboksen Add Form-Based websted. Navnet på programmet vises i Azure Multi-Factor Authentication rapporter og kan vises i SMS eller Mobile-App godkendelse meddelelser. Se Hjælp-filen kan finde flere oplysninger under sende URL-adressen.
8. Vælg det korrekte format anmodning. Dette er angivet til "INDLÆG eller Hent" for de fleste webprogrammer.
9. Angiv brugernavn variabel, adgangskode variabler og domæne variabel (hvis det vises på siden login). Du skal muligvis gå til siden login i en webbrowser, skal du højreklikke på siden, og vælg "Vis kilde" til at finde navnene på felterne input i siden.
10. Markér afkrydsningsfeltet Kræv Azure Multi-Factor Authentication bruger match, hvis alle brugere har været eller der skal importeres til serveren og underlagt Multi-Factor authentication. Hvis et stort antal brugere endnu ikke er blevet importeret til serveren og/eller bliver undtaget fra Multi-Factor authentication, Lad feltet være umarkerede. Se Hjælp-filen til yderligere oplysninger om denne funktion.
11.  Klik på de avancerede... knappen Gennemse avancerede indstillinger, herunder også muligheden for at vælge en brugerdefineret nægtelse siden fil til at cachelagre vellykket godkendelse til webstedet for et bestemt tidsrum ved hjælp af cookies og for at vælge, om du vil godkende primære legitimationsoplysningerne mod Windows-domænet, en LDAP-adresseliste eller en RADIUS-server. Klik på knappen OK for at vende tilbage til dialogboksen Add Form-Based websted, når du er færdig. Se Hjælp-filen kan finde flere oplysninger på siden Avancerede indstillinger.
12. Klik på knappen OK.
13. Når de URL-adresse og siden variabler har fundet eller har angivet, vises dataene websted i panelet formularbaserede.
14. Se aktivere IIS Plug-ins til Azure Multi-Factor Authentication Server afsnittet direkte nedenfor til at fuldføre konfigurationen IIS-godkendelse.

## <a name="using-integrated-windows-authentication-with-azure-multi-factor-authentication-server"></a>Ved hjælp af integreret Windows-godkendelse med Azure Multi-Factor Authentication Server

Installer Azure Multi-Factor Authentication Server på webserveren IIS for at sikre en IIS-webprogram, der bruger integreret Windows HTTP-godkendelse, og Konfigurer serveren i den følgende procedure.

1. Klik på ikonet IIS-godkendelse i menuen til venstre i Azure Multi-Factor Authentication Server.
2. Klik på fanen HTTP. Klik på fanen formularbaserede.
3. Klik på Tilføj... knappen.
4. Angiv URL-adressen til det websted, hvor HTTP-godkendelse er udført (fx http://localhost/owa) i feltet Base URL-adresse i feltet Tilføj Base URL-adresse dialog, og Angiv navnet på et program (valgfrit). Navnet på programmet vises i Azure Multi-Factor Authentication rapporter og kan vises i SMS eller Mobile-App godkendelse meddelelser.
5. Juster inaktiv timeout og maksimum session gange Hvis standard ikke er tilstrækkelige.
6. Markér afkrydsningsfeltet Kræv Multi-Factor Authentication bruger match, hvis alle brugere har været eller der skal importeres til serveren og underlagt Multi-Factor authentication. Hvis et stort antal brugere endnu ikke er blevet importeret til serveren og/eller bliver undtaget fra Multi-Factor authentication, Lad feltet være umarkerede. Se Hjælp-filen til yderligere oplysninger om denne funktion.
7. Markér feltet cookie cachen, hvis du ønsker.
8. Klik på knappen OK.
9. Se afsnittet [aktivere IIS Plug-ins til Azure Multi-Factor Authentication Server](#enable-iis-plug-ins-for-azure-multi-factor-authentication-server) direkte nedenfor for at fuldføre konfigurationen IIS-godkendelse.


## <a name="enable-iis-plug-ins-for-azure-multi-factor-authentication-server"></a>Aktivere IIS Plug-ins til Azure Multi-Factor Authentication Server

Når du har konfigureret den formularbaserede eller HTTP-godkendelse URL-adresser og indstillinger, skal du vælge de placeringer, hvor Azure Multi-Factor Authentication IIS plug-ins skal være indlæst og aktiveret i IIS. Benyt følgende fremgangsmåde:

1. Hvis kører på IIS 6, klik på fanen ISAPI- og vælge det websted, der kører webprogrammet under (fx standardwebstedet) for at aktivere Azure Multi-Factor Authentication ISAPI-filteret plug-in'en for det pågældende websted.
2. Hvis kører på IIS 7 eller nyere, klik på fanen oprindelige modul, og vælg den server, websteder eller programmer til at aktivere IIS-plug-in'en på den ønskede blive overskrevet.
3. Klik på feltet aktivere IIS godkendelse øverst på skærmen. Azure Multi-Factor Authentication nu sikring af det valgte IIS-program. Sørg for, at brugere er blevet importeret til serveren. I afsnittet der er tillid til IP'er nedenfor, hvis du vil gerne whitelist interne IP-adresser, så, at to-faktor-godkendelse ikke kræves, når du logger på webstedet fra disse placeringer.


## <a name="trusted-ips"></a>Der er tillid til IP'er

Den der er tillid til IP'er kan tilsidesætte Azure Multi-Factor Authentication for anmodninger om websted, der kommer fra bestemte IP-adresser eller undernet-brugere. For eksempel kan du få undtaget brugere fra Azure Multi-Factor Authentication under tilmeldingen fra office. Dette, skal du angive office-undernettet som en post i der er tillid til IP'er. Hvis du vil konfigurere der er tillid til IP-adresser, skal du benytte følgende fremgangsmåde:

1. Klik på fanen IP'er, der er tillid til i sektionen IIS-godkendelse.
2. Klik på Tilføj... knappen.
3. Når dialogboksen Tilføj der er tillid til IP'er vises, skal du vælge enkelt IP-, IP-område eller undernet alternativknappen.
4. Skriv IP-adressen, område af IP-adresser eller undernet, der skal være whitelisted. Hvis indtaste et undernet, Vælg den relevante netmaske, og klik på knappen OK. Whitelist er nu tilføjet.
