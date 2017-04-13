<properties 
    pageTitle="Introduktion til Azure Multi-Factor Authentication Server"
    description="Dette er den Azure Multi-Factor authentication side, der beskriver, hvordan du kommer i gang med Azure MFA-serveren."
    services="multi-factor-authentication"
    keywords="godkendelse, azure flere faktor-godkendelse app aktivering serversiden godkendelse server download"
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

# <a name="getting-started-with-the-azure-multi-factor-authentication-server"></a>Introduktion til Azure Multi-Factor Authentication Server




<center>![Skyen](./media/multi-factor-authentication-get-started-server/server2.png)</center>

Nu, hvor vi har besluttet dig om at bruge lokale Multi-Factor authentication, Lad os komme i gang. Denne side beskrives en ny installation af serveren og få det konfiguration med lokale Active Directory. Hvis du allerede har installeret PhoneFactor serveren og søger du opgradere, under [opgradering til Azure Multi-Factor serveren](multi-factor-authentication-get-started-server-upgrade.md) , eller hvis du leder efter oplysninger om installation af se lige webtjenesten [implementerer Azure Multi-Factor Authentication Server Mobile App Web Service](multi-factor-authentication-get-started-server-webservice.md).


## <a name="download-the-azure-multi-factor-authentication-server"></a>Hent Server Azure Multi-Factor Authentication



Der findes to forskellige måder, som du kan hente Azure Multi-Factor Authentication Server. Begge gøres via Azure-portalen. Først er ved at administrere provideren Multi-Factor Auth direkte. Andet er via tjenesteindstillingerne. Den anden mulighed kræver en udbyder af Multi-Factor Auth eller licensen til Azure MFA, Azure AD Premium eller Enterprise mobilitet pakke.


### <a name="to-download-the-azure-multi-factor-authentication-server-from-the-azure-portal"></a>Hente Azure Multi-Factor Authentication serveren fra Azure-portalen
--------------------------------------------------------------------------------

1. Log på portalen Azure som Administrator.
2. Vælg Active Directory i venstre side.
3. Klik **Multi-Factor Auth udbydere** øverst på siden Active Directory
4. Klik på **Administrer** nederst
5. Der åbnes en ny side.  Klik på **overførsler.** 
 ![Hente](./media/multi-factor-authentication-sdk/download.png)
6. Klik på over **Generere aktivering legitimationsoplysninger**, **downloade.** 
 ![Hente](./media/multi-factor-authentication-get-started-server/download4.png)
7. Gem filen.



### <a name="to-download-the-azure-multi-factor-authentication-server-via-the-service-settings"></a>Hente Azure Multi-Factor Authentication serveren via tjenesteindstillingerne


1. Log på portalen Azure som Administrator.
2. Vælg Active Directory i venstre side.
3. Dobbeltklik på din forekomst af Azure AD.
4. Klik på **Konfigurer**øverst
![hente](./media/multi-factor-authentication-sdk/download2.png)
5. Vælg **Administrer Tjenesteindstillinger** under Multi-Factor authentication
6. Klik på **Gå til portalen**nederst på skærmen på indstillingssiden tjenester.
![Download](./media/multi-factor-authentication-get-started-server/servicesettings.png)
7. Der åbnes en ny side. Klik på **overførsler.**
8. Klik på over **Generere aktivering legitimationsoplysninger**, **downloade.**
9. Gem filen.




## <a name="install-and-configure-the-azure-multi-factor-authentication-server"></a>Installere og konfigurere serveren Azure Multi-Factor Authentication
Nu hvor du har hentet den server, kan du installere og konfigurere den.  Sørg for, at du installerer det på serveren opfylder følgende krav:



Azure Multi-Factor Authentication serverkrav|Beskrivelse|
:------------- | :------------- |
Hardware|<li>200 MB ledig plads på harddisken</li><li>x32 eller x64 kan processor</li><li>1 GB eller større RAM</li>
Software|<li>Windows Server 2008 eller nyere, hvis værten er en server OS</li><li>Windows 7 eller nyere, hvis værten er en klient OS</li><li>Microsoft .NET 4.0 Framework</li><li>IIS 7.0 eller nyere, hvis installerer tjenesten bruger portal eller web SDK</li>

### <a name="azure-multi-factor-authentication-server-firewall-requirements"></a>Azure Multi-Factor Authentication firewall serverkrav
--------------------------------------------------------------------------------
Hver MFA-server skal kunne kommunikere på port 443 udgående til følgende:

- https://pfd.phonefactor.NET
- https://pfd2.phonefactor.NET
- https://CSS.phonefactor.NET

Hvis udgående firewalls er begrænset på port 443, skal følgende IP-adresseområder åbnes:

IP-undernet|Netmaske|IP-område
:------------- | :------------- | :------------- |
134.170.116.0/25|255.255.255.128|134.170.116.1 – 134.170.116.126
134.170.165.0/25|255.255.255.128|134.170.165.1 – 134.170.165.126
70.37.154.128/25|255.255.255.128|70.37.154.129 – 70.37.154.254

Hvis du ikke bruger Azure Multi-Factor Authentication begivenhed bekræftelse funktioner, og brugerne ikke kan kan godkendelse IP-adresse med Multi-Factor Auth-mobilapps fra enheder på virksomhedens netværk områder reduceres til følgende:


IP-undernet|Netmaske|IP-område
:------------- | :------------- | :------------- |
134.170.116.72/29|255.255.255.248|134.170.116.72 – 134.170.116.79
134.170.165.72/29|255.255.255.248|134.170.165.72 – 134.170.165.79
70.37.154.200/29|255.255.255.248|70.37.154.201 – 70.37.154.206


### <a name="to-install-and-configure-the-azure-multi-factor-authentication-server"></a>Installere og konfigurere serveren Azure Multi-Factor Authentication
--------------------------------------------------------------------------------


1. Dobbeltklik på den eksekverbare fil. Dette starter installationen.
2. Sørg for, at mappen er korrekte på skærmbilledet Vælg mappe til Installation og klikke på Næste.
3. Når installationen er fuldført, skal du klikke på Udfør.  Dette vil starte konfigurationsguiden.
4. Konfigurationsguiden af velkomstsiden, Marker **Spring ved hjælp af guiden Konfiguration af godkendelse** og klik på **Næste**.  Dette vil lukke guiden og starte serveren.
    ![Skyen](./media/multi-factor-authentication-get-started-server/skip2.png)
5. Klik på knappen **Generér aktivering legitimationsoplysninger** på siden, som vi har hentet fra serveren, igen.  Kopiér disse oplysninger til Azure MFA serveren i felterne, og klik på **Aktivér**.


Ovenstående trin viser en hurtig installation med konfigurationsguiden.  Du kan køre godkendelsesguiden igen ved at markere den i menuen Funktioner på serveren.



##<a name="import-users-from-active-directory"></a>Importere brugere fra Active Directory

Nu, hvor serveren er installeret og konfigureret kan du hurtigt importere brugere til Azure MFA-Server.

### <a name="to-import-users-from-active-directory"></a>Sådan importeres brugere fra Active Directory
--------------------------------------------------------------------------------


1. Vælg **brugere**i Azure MFA Server i venstre side.
2. Til bunden, og vælg **Importer fra Active Directory**.
3. Nu du enten søge for individuelle brugere eller søge mappen AD efter afdelinger med brugere i dem.  I dette tilfælde vil vi angive, hvilke brugere OU.
4. Fremhæv alle brugere i højre side, og klik på **Importér**.  Du modtager en pop op-vindue, der fortæller, at du blev gennemført.  Luk vinduet Importer.

![Skyen](./media/multi-factor-authentication-get-started-server/import2.png)

## <a name="send-users-an-email"></a>Sende en mail til brugerne
Nu hvor du har importeret dine brugere til Azure Multi-Factor Authentication serveren, anbefales det, du sender brugerne en mail, der fortæller, at de er blevet registreret i Multi-Factor authentication.

Der er forskellige måder at konfigurere dine brugere til brug af Multi-Factor authentication med Azure Multi-Factor Authentication-serveren.  Eksempelvis hvis du kender brugernes telefonnumre eller var i stand til at importere telefonnumrene til Azure Multi-Factor Authentication serveren fra deres firmaadressekartoteket, vil e-mailen Giv brugerne besked om, at de har konfigureret til at bruge Azure Multi-Factor Authentication, viser nogle instruktioner om at bruge Azure Multi-Factor Authentication og informere brugeren om det telefonnummer, så de modtager deres godkendelse på.  

Indholdet af e-mailen, varierer afhængigt af metoden til godkendelse, der er angivet for brugeren (fx telefonopkald, SMS, mobilapp).  Eksempelvis hvis brugeren skal bruge en pinkode, når de godkender, fortæller e-mailen dem hvad der er angivet deres indledende pinkode.  Brugere er som regel kræves for at ændre deres pinkode under deres første godkendelse.

Hvis brugeres telefonnumre ikke er konfigureret eller importeret til Azure Multi-Factor Authentication Server eller brugere er allerede konfigureret til at bruge-mobilappen til godkendelse, kan du sende dem en mail, hvor dem om, at de er blevet konfigureret til at bruge Azure Multi-Factor Authentication og det vil dirigerer brugeren til at udføre deres konto tilmelding via Azure Multi-Factor Authentication User Portal.  Et link, der medtages, at brugeren klikker på at få adgang til portalen bruger. Når brugeren klikker på linket, åbnes og tage dem til deres egen virksomhed Azure Multi-Factor Authentication User Portal deres webbrowser.   


### <a name="configuring-email-and-email-templates"></a>Konfiguration af mail og e-mail-skabeloner

Du kan konfigurere indstillingerne for at sende disse mails ved at klikke på ikonet mail i venstre side.  Dette er, hvor du kan angive af SMTP-oplysninger for din mailserver, og det kan du sende en ramme bredden mail ved at tilføje et afkrydsningsfelt i dialogboksen Send mails til brugere.

![E-mail-indstillinger](./media/multi-factor-authentication-get-started-server/email1.png)

Du kan se alle de forskellige e-mail-skabeloner, der er tilgængelige for at vælge mellem under fanen mail indhold.  Så afhængigt af hvordan du har konfigureret dine brugere at bruge Multi-Factor godkendelse, kan du vælge skabelonen passer, som passer bedst dig.

![E-mail-skabeloner](./media/multi-factor-authentication-get-started-server/email2.png)

## <a name="how-the-azure-multi-factor-authentication-server-handles-user-data"></a>Hvordan Azure Multi-Factor Authentication Server håndterer brugerdata

Når du bruger Multi-Factor Authentication (MFA) Server lokalt, gemmes en brugers data i de lokale servere. Ingen fast brugerdata er gemt i skyen. Når brugeren foretager en to-faktor-godkendelse, sender MFA serveren data til Azure MFA skybaseret tjeneste til at udføre godkendelsen. Når disse anmodninger om godkendelse sendes til skybaseret tjeneste, der sendes følgende felter i logfiler og anmodningen, så de er tilgængelige i kundens godkendelse/anvendelsesrapporter. Nogle af felterne er valgfrit, så de kan aktiveres eller deaktiveres i Multi-Factor Authentication serveren. Kommunikation fra serveren MFA til MFA skybaseret tjeneste bruger SSL/TLS over port 443 udgående. Disse felter er:

- Entydigt ID - brugernavn eller interne MFA server-ID
- Første og sidste Name - valgfrit
- Mailadresse - valgfrit
- Telefonnummer - ved at gøre et taleopkald eller SMS-godkendelse
- Enhed token – når du udfører mobilapp godkendelse
- Godkendelsestilstand
- Godkendelse resultat
- MFA servernavn
- MFA Server IP-adresse
- Klient IP-Hvis det er muligt



Ud over ovenstående felter er godkendelse resultat (succes/nægtelse) og årsag til en hvilken som helst denials også gemt med Godkendelsesdataene, og tilgængelige via godkendelse/anvendelsesrapporter.


## <a name="advanced-azure-multi-factor-authentication-server-configurations"></a>Avancerede Azure Multi-Factor Authentication serverkonfigurationer
Brug tabellen nedenfor for yderligere oplysninger om Avanceret konfiguration og konfigurationsoplysninger.

Metode|Beskrivelse
:------------- | :------------- |
[User Portal](multi-factor-authentication-get-started-portal.md)|  Oplysninger om installation og konfiguration af portalen bruger, herunder installation og bruger selvbetjening.
[Active Directory Federation-tjenesten](multi-factor-authentication-get-started-adfs.md)|Oplysninger om konfiguration af Azure Multi-Factor Authentication med AD FS.
[RADIUS-godkendelse](multi-factor-authentication-get-started-server-radius.md)|  Oplysninger om installation og konfiguration af Azure MFA Server med RADIUS.
[IIS-godkendelse](multi-factor-authentication-get-started-server-iis.md)|Oplysninger om installation og konfiguration af Azure MFA Server med IIS.
[Windows-godkendelse](multi-factor-authentication-get-started-server-windows.md)|  Oplysninger om installation og konfiguration af Azure MFA Server med Windows-godkendelse.
[LDAP-godkendelse](multi-factor-authentication-get-started-server-ldap.md)|Oplysninger om installation og konfiguration af Azure MFA Server med LDAP-godkendelse.
[Remote Desktop Gateway og Azure Multi-Factor Authentication Server ved hjælp af RADIUS](multi-factor-authentication-get-started-server-rdg.md)|  Oplysninger om installation og konfiguration af Azure MFA Server med Remote Desktop Gateway ved hjælp af RADIUS.
[Synkroniser med Windows Server Active Directory](multi-factor-authentication-get-started-server-dirint.md)|Oplysninger om installation og konfiguration af synkronisering mellem Active Directory og Azure MFA-serveren.
[Implementere Azure Multi-Factor Authentication Server Mobile-App-webtjeneste](multi-factor-authentication-get-started-server-webservice.md)|Oplysninger om installation og konfiguration af webtjenesten Azure MFA server.
