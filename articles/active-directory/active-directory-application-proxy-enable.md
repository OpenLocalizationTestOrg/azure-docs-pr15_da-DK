<properties
    pageTitle="Aktivere Azure AD-proxyen | Microsoft Azure"
    description="Slå proxyen på portalen Azure klassisk, og Installer forbindelserne for modsat proxyen."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/19/2016"
    ms.author="kgremban"/>

# <a name="enable-application-proxy-in-the-azure-portal"></a>Aktivere proxyen på portalen Azure

I denne artikel fører dig gennem trinnene til at aktivere Microsoft Azure AD-proxyen for adresselisten skyen i Azure AD.

Hvis du ikke ved, hvilken proxyen kan hjælpe dig med at gøre, lære mere om, [hvordan du giver sikker fjernadgang til lokale programmer](active-directory-application-proxy-get-started.md).

## <a name="application-proxy-prerequisites"></a>Forudsætninger for program-Proxy
Før du kan aktivere og bruge proxyen tjenester, skal du have:

- Et [Microsoft Azure AD grundlæggende eller førsteklasses abonnement](active-directory-editions.md) og en Azure AD-mappe, hvor du er en global administrator.
- En server, der kører Windows Server 2012 R2 eller Windows 8.1 eller nyere, som kan du installere programmet Proxy forbindelsen. Serveren sender anmodninger til proxyen tjenesterne i skyen, og der er nødvendige for en HTTP eller HTTPS-forbindelse til de programmer, du udgiver.

    - For single sign-on til dine publicerede programmer, skal denne computer være medlem af et domæne i det samme AD-domæne, som de programmer, du udgiver.

- Hvis der er en firewall i stien, skal du kontrollere, at den er åben, så forbindelsen kan foretage HTTPS (TCP) anmodninger til proxyen. Forbindelsen bruger disse porte sammen med underdomæner, der er en del af overordnet domæner msappproxy.net og servicebus.windows.net. Sørg for at åbne følgende porte til **udgående** trafik:

  	| Portnummer | Beskrivelse |
  	| --- | --- |
  	| 80 | Aktivere udgående HTTP-trafik for sikkerhedskontrol. |
  	| 443 | Aktivere brugergodkendelse mod Azure AD (påkrævet kun for registreringsprocessen forbindelse) |
  	| 10100 – 10120 | Aktivere LOB HTTP-svar, der sendes tilbage til proxyen |
  	| 9352, 5671 | Aktivere kommunikation mellem forbindelsen mod Azure tjenesten for indgående anmodninger. |
  	| 9350 | Valgfrit, hvis du vil aktivere bedre ydeevne for indgående anmodninger |
  	| 8080 | Aktivere Connector bootstrap rækkefølge og forbindelse automatisk opdatering |
  	| 9090 | Aktivere Connector registrering (påkrævet kun for registreringsprocessen forbindelse) |
  	| 9091 | Aktivere Connector Hav tillid til certifikatet automatisk fornyelse |

    Hvis din firewall gennemtvinger trafik ifølge kommer brugere, kan du åbne portene til trafik kommer fra Windows services kører som en netværkstjeneste. Du skal også sørge for at aktivere port 8080 NT Authority\System.

- Hvis din organisation bruger proxyservere til at oprette forbindelse til internettet, skal du se nærmere på blogindlægget [arbejde med eksisterende lokalt proxyservere](https://blogs.technet.microsoft.com/applicationproxyblog/2016/03/07/working-with-existing-on-prem-proxy-servers-configuration-considerations-for-your-connectors/) få mere at vide om, hvordan du konfigurerer dem.

## <a name="step-1-enable-application-proxy-in-azure-ad"></a>Trin 1: Aktivere proxyen i Azure AD
1. Log på som administrator på [Azure klassisk portal](https://manage.windowsazure.com/).
2. Gå til Active Directory, og vælg den mappe, hvor du vil aktivere proxyen.

    ![Active Directory - ikon](./media/active-directory-application-proxy-enable/ad_icon.png)

3. Vælg **Konfigurer** fra siden med mappen, og Rul ned til **Proxyen**.
4. Slå **aktivere Proxy programtjenester til denne mappe** til **aktiveret**.

    ![Aktivere proxyen](./media/active-directory-application-proxy-enable/app_proxy_enable.png)

5. Klik på **Hent nu**. Du føres til **Azure AD programmet Proxy forbindelse hente**. Læs og Accepter licensvilkårene, og klik på **Hent** for at gemme Windows Installer-filen (.exe) for forbindelsen.

## <a name="step-2-install-and-register-the-connector"></a>Trin 2: Installere og registrere forbindelsen
1. Køre **AADApplicationProxyConnectorInstaller.exe** på den server, du har forberedt ifølge forudsætningerne.
2. Følg vejledningen i guiden for at installere.
3. Under installationen, vil du bliver bedt om at registrere forbindelsen med proxyen af din Azure AD-lejer.

  - Angiv dine legitimationsoplysninger for Azure AD global administrator. Global administrator-lejer kan være forskellige fra dine Microsoft Azure-legitimationsoplysninger.
  - Sørg for administratoren der registrerer forbindelsen er i den samme mappe, hvor du har aktiveret tjenesten proxyen. Eksempelvis hvis lejer domæne er contoso.com skal administratoren skal være admin@contoso.com eller andre alias på domænet.
  - Hvis **IE Enhanced Security Configuration** er indstillet til **på** på serveren, hvor du installerer forbindelsen, kan blive blokeret registreringsskærmbilledet. Følg vejledningen i en fejlmeddelelse om at give adgang. Sørg for, at Internet Explorer Enhanced Security er slået fra.
  - Hvis forbindelsen registrering ikke lykkes, kan du se [Fejlfinding af proxyen](active-directory-application-proxy-troubleshoot.md).  

4. Når installationen er fuldført, føjes to nye tjenester til din server:

    - **Microsoft AAD programmet Proxy Connector** gør det muligt for forbindelse
    - **Microsoft AAD programmet Proxy forbindelse Updater** er et automatiseret update-tjenesten, som regelmæssigt kontrollerer, om nye versioner af forbindelsen og opdaterer forbindelsen, efter behov.

    ![App-Proxy forbindelse services - skærmbillede](./media/active-directory-application-proxy-enable/app_proxy_services.png)

5. Klik på **Udfør** i installationsvinduet.

Til høj tilgængelighed formål, skal du installere mindst to forbindelser. Gentag trin 2 og 3 ovenfor for at installere flere forbindelser. Hver forbindelse, skal være registreret separat.

Hvis du vil fjerne forbindelsen, skal du fjerne både tjenesten Connector og tjenesten Updater. Genstarte computeren for at fjerne tjenesten fuldstændigt.


## <a name="next-steps"></a>Næste trin

Nu er du klar til [Udgiv programmer med proxyen](active-directory-application-proxy-publish.md).

Hvis du har programmer, der er på separate netværk eller forskellige steder, kan du bruge forbindelse grupper til at organisere forskellige forbindelserne i logiske enheder. Lær mere om at [arbejde med proxyen forbindelser](active-directory-application-proxy-connectors.md).
