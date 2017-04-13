<properties
    pageTitle="Single sign-on – med proxyen | Microsoft Azure"
    description="Beskriver, hvordan du giver enkeltlogon ved hjælp af Azure AD-proxyen."
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
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="kgremban"/>


# <a name="single-sign-on-with-application-proxy"></a>Single sign-on – med proxyen

Enkeltlogon er et væsentligt element i Azure AD-proxyen. Den indeholder den bedste brugeroplevelse med følgende trin:

1. En bruger logger ind i skyen  
2. Alle sikkerhed valideringer sker i skyen (forhåndsgodkendelse)  
3. Når anmodningen sendes til programmet lokalt, programmet Proxy forbindelsen giver sig ud for brugeren. Back end-programmet mener, at dette er en almindelig bruger, der kommer fra en enhed, der er medlem af et domæne.

![Access-diagram fra slutbrugerens via proxyen, til virksomhedens netværk](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_diagram.png)

Azure AD-proxyen hjælper dig med at give en enkeltlogon (SSO) oplevelse for dine brugere. Brug følgende fremgangsmåde til at publicere dine ved hjælp af SSO-apps:


## <a name="sso-for-on-prem-iwa-apps-using-kcd-with-application-proxy"></a>SSO for lokalt IWA apps ved hjælp af KCD med proxyen
Du kan aktivere enkeltlogon til dine programmer ved hjælp af integreret Windows godkendelse (IWA) ved at give programmet Proxy forbindelser tilladelse i Active Directory til at repræsentere brugere, og sende og modtage tokens på deres vegne.


### <a name="network-diagram"></a>Netværksdiagram

I dette diagram beskrives strømmen, når en bruger forsøger at få adgang til et lokalt program, der bruger IWA.

![Microsoft AAD godkendelse rutediagram](./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png)

1. Brugeren skriver URL-adressen for at få adgang til programmet lokalt via proxyen.
2. Proxyen omdirigerer anmodningen til Azure AD godkendelsestjenester til preauthenticate. På dette tidspunkt gælder Azure AD eventuelle relevante godkendelse og godkendelse politikker, som multifaktor-godkendelse. Hvis brugeren er godkendt, Azure AD opretter et token og sender den til brugeren.
3. Brugeren overfører tokenet til proxyen.
4. Proxyen valideret tokenet og henter bruger vigtigste navn (UPN) fra den og derefter sender anmodningen, UPN og det primære navn SPN (Service) til forbindelsen via en Dual godkendte sikker kanal.
5. Forbindelsen udfører Kerberos begrænset delegering (KCD) forhandling med lokalt AD, repræsenterer bruger for at få et Kerberos-id til programmet.
6. Active Directory sender Kerberos-tokenet til programmet til forbindelsen.
7. Forbindelsen sender den oprindelige anmodning til programserver, ved hjælp af Kerberos-tokenet den modtaget fra AD.
8. Programmet sender svar til den forbindelse, returneres derefter til tjenesten proxyen og til sidst til brugeren.

### <a name="prerequisites"></a>Forudsætninger

Før du går i gang med SSO for proxyen, kontrollere dit miljø er klar med de følgende indstillinger og konfigurationer:

- Dine apps, som SharePoint online, er indstillet til at bruge integreret Windows-godkendelse. Se [Aktivere understøttelse af Kerberos-godkendelse](https://technet.microsoft.com/library/dd759186.aspx)kan finde flere oplysninger, eller se [planlægge efter Kerberos-godkendelse i SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx)til SharePoint.

- Alle dine apps har tjenestens hovednavne.

- Den server, der kører forbindelsen og den server, der kører appen er domæne tilsluttet, og en del af det samme domæne eller have tillid til domæner. Du kan finde flere oplysninger om forbindelse til et domæne, [deltage i en Computer til et domæne](https://technet.microsoft.com/library/dd807102.aspx).

- Den server, der kører forbindelsen har adgang til at læse TokenGroupsGlobalAndUniversal for brugere. Dette er en standardindstilling, der muligvis blevet påvirket af sikkerhed hardening miljøet. Få mere hjælp med dette i [KB2009157](https://support.microsoft.com/en-us/kb/2009157).

### <a name="active-directory-configuration"></a>Konfiguration af Active Directory

Active Directory konfigurationen varierer afhængigt af om dit program Proxy Connector og publicerede serveren er i det samme domæne eller ej.

#### <a name="connector-and-published-server-in-the-same-domain"></a>Forbindelse og publicerede server i det samme domæne

1. Gå til **Funktioner**i Active Directory, > **brugere og computere**.
2. Vælg den server, der kører forbindelsen.
3. Højreklik, og vælg **Egenskaber** > **delegering**.
4. Vælg **Hav tillid til denne computer til delegering til angivne services** og tilføje værdien for SPN identiteten af application server under **tjenester, som denne konto kan præsentere delegerede legitimationsoplysninger**.
5. Dette gør det muligt for programmet Proxy forbindelsen til at repræsentere brugere i AD mod de programmer, der er defineret på listen.

![Egenskaber for forbindelse SVR vinduet skærmbillede](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

#### <a name="connector-and-published-server-in-different-domains"></a>Forbindelse og publicerede server i forskellige domæner

1. Du kan finde en liste over forudsætninger til at arbejde med KCD på tværs af domæner [Kerberos-begrænset delegering på tværs af domæner](https://technet.microsoft.com/library/hh831477.aspx).
2. I Windows 2012 R2, bruge den `principalsallowedtodelegateto` egenskaben i Connector serveren for at aktivere den markerede Proxy uddelegere af Connector-server, hvor publicerede serveren er `sharepointserviceaccount` og stedfortræderrolle serveren er `connectormachineaccount`.

        $connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

        Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

        Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount


>[AZURE.NOTE] `sharepointserviceaccount`kan være computerkonto SPS eller en tjenestekonto, som SPS app puljen kører under.


### <a name="azure-classic-portal-configuration"></a>Azure konfiguration af klassisk portal

1. Publicere dit program henhold til de instruktioner, der er beskrevet i [Udgiv programmer med proxyen](active-directory-application-proxy-publish.md). Sørg for at vælge **Azure Active Directory** som **Preauthentication metode**.
2. Når dit program vises på listen over programmer, skal du markere det og klik på **Konfigurer**.
3. Angiv **Interne godkendelsesmetode** til **Integreret Windows-godkendelse**under **Egenskaber**.  
  ![Avancerede programkonfiguration](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)  
4. Angive **Interne programmet SPN** af application server. I dette eksempel er hovednavn for tjenesten til vores udgivet program http/lob.contoso.com.  

>[AZURE.IMPORTANT] Hvis din lokale UPN og UPN i Azure Active Directory ikke er ens, skal du konfigurere den [delegerede login identitet](#delegated-login-identity) for at forhåndsgodkendelse til at arbejde.

|  |  |
| --- | --- |
| Interne godkendelsesmetode | Hvis du bruger Azure AD til forhåndsgodkendelse, kan du angive en intern godkendelsesmetode til at aktivere dine brugere kan få glæde af enkeltlogon (SSO) til dette program. <br><br> Vælg **Integreret Windows-godkendelse** (IWA), hvis dit program bruger IWA, og du kan konfigurere Kerberos begrænset delegering (KCD) for at aktivere SSO for dette program. Programmer, der bruger IWA skal konfigureres ved hjælp af KCD, ellers proxyen vil ikke kunne publicere disse programmer. <br><br> Vælg **ingen** , hvis dit program ikke bruge IWA. |
| Interne program SPN | Dette er tjenesten hovednavn (SPN) af interne programmet, er konfigureret i lokalt Azure AD. Hovednavn for tjenesten bruges af programmet Proxy forbindelsen til at hente Kerberos tokens for programmet ved hjælp af KCD. |


## <a name="sso-for-non-windows-apps"></a>SSO til ikke-Windows-apps
Kerberos delegering strømmen i Azure AD-proxyen starter, når Azure AD godkender brugeren i skyen. Når anmodningen modtages i det lokale miljø, udsteder Azure AD-program-Proxy forbindelsen en Kerberos brugertilladelse på vegne af brugeren ved arbejde med det lokale Active Directory. Denne proces kaldes som Kerberos begrænset delegering (KCD). I den næste fase sendes en anmodning til back end-program med denne Kerberos brugertilladelse. Der findes et antal protokoller, der definerer, hvordan du kan sende sådanne anmodninger. De fleste Windows-servere forventer Negotiate/SPNego, der understøttes nu på Azure AD-proxyen.

![Ikke-Windows SSO-diagram](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_nonwindows_diagram.png)

### <a name="delegated-login-identity"></a>Delegeret login identitet
Delegeret login identitet hjælper dig med at håndtere to forskellige login scenarier:

- Ikke-Windows-programmer, der typisk få bruger-id i form af et brugernavn eller Søren kontonavn, ikke en e-mailadresse (username@domain).
- Alternativ login konfigurationer hvor UPN i Azure AD og UPN i din lokale Active Directory er forskellige.

Med proxyen, kan du vælge hvilke identitet, der skal bruge til at hente Kerberos brugertilladelse. Denne indstilling er i programmet. Nogle af disse indstillinger er egnet til systemer, der ikke kan acceptere e-mail-adresseformatet, andre er designet til at logge på alternativ.

![Delegeret login identitet parameter skærmbillede](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

Hvis delegeret login identitet bruges, kan værdien, der muligvis ikke entydigt for alle domæner eller områder i organisationen. Du kan undgå dette problem ved at publicere disse programmer, der er to gange ved hjælp af to forskellige Connector-grupper. Da hvert program, der har en anden bruger målgruppe, kan du deltage i dens forbindelser til et andet domæne.


## <a name="working-with-sso-when-on-premises-and-cloud-identities-are-not-identical"></a>Arbejde med SSO når i det lokale miljø og skyen identiteter ikke er ens
Medmindre andet er konfigureret, antages proxyen det, at brugere har præcis den samme identitet i skyen og i det lokale miljø. Du kan konfigurere, for hvert program, hvilken identitet skal bruges, når du udfører enkeltlogon.  

Denne funktion gør det muligt for mange organisationer, som har forskellige lokalt og skyen identiteter have SSO fra skyen til lokalt apps uden at brugere skal du angive forskellige brugernavne og adgangskoder. Dette omfatter organisationer, som:

- Har flere domæner internt (joe@us.contoso.com, joe@eu.contoso.com) og et enkelt domæne i skyen (joe@contoso.com).

- Har ikke understøtter routing domænenavn internt (joe@contoso.usa) og en juridisk i skyen.

- Brug ikke domænenavne internt (joe)

- Bruge forskellige aliasser lokalt og i skyen. F.eks. joe-johns@contoso.comvs.joej@contoso.com  

Det hjælper også med programmer, der ikke kan acceptere adresser i form af e-mail-adresse, som er en meget almindelige scenarie for ikke-Windows back end-servere.


### <a name="setting-sso-for-different-cloud-and-on-prem-identities"></a>Konfigurere SSO for forskellige skyen og lokalt identiteter

1. Konfigurere indstillinger for Azure AD-forbindelse, så den overordnede identitet vil være mailadressen (mail). Dette gøres som en del af processen Tilpas, ved at ændre feltet **Princippet brugernavn** i synkroniseringsindstillingerne for. Bemærk, at disse indstillinger også bestemmer, hvordan brugere logger på Office 365, Windows10 enheder og andre programmer, der bruger Azure AD som deres identitet butik.  
  ![Identificere brugere skærmbillede - brugerens hovednavn rullemenu](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. Vælg den **Delegerede Login identitet** skal bruges i programmet konfigurationsindstillinger for det program, du vil ændre:
  - Princippet brugernavn:joe@contoso.com  
  - Alternative princippet brugernavn:joed@contoso.local  
  - Brugernavn en del af primært brugernavn: joe  
  - Brugernavn en del af alternative princippet brugernavn: joed  
  - Lokalt Søren kontonavn: afhængigt af lokalt domænecontroller konfiguration

  ![Delegeret login identitet rullemenuen skærmbillede](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)  

### <a name="troubleshooting-sso-for-different-identities"></a>Fejlfinding i forbindelse med SSO for forskellige identiteter
Hvis der er en fejl i processen SSO vises i hændelsesloggen forbindelse maskine, som beskrevet i [fejlfinding](active-directory-application-proxy-troubleshoot.md).
Men i nogle tilfælde anmodningen sendes blev til back end-programmet mens dette program kan svare i forskellige andre HTTP-svar. Fejlfinding i forbindelse med disse tilfælde skal starte ved at undersøge begivenhed tal 24029 på den forbindelse maskine i hændelsesloggen proxyen session. Det bruger-id, der blev brugt til delegering vises i feltet "brugere" inden for oplysninger om begivenheden. Vælg **Vis analytisk og fejlfinding af logfiler** i tilfælde af, at fremviseren menuen Vis for at slå session log.


## <a name="see-also"></a>Se også

- [Udgive programmer med proxyen](active-directory-application-proxy-publish.md)
- [Foretage fejlfinding af problemer, du har kørende med proxyen](active-directory-application-proxy-troubleshoot.md)
- [Arbejde med krav opmærksom på programmer](active-directory-application-proxy-claims-aware-apps.md)
- [Aktivere betinget adgang](active-directory-application-proxy-conditional-access.md)

Se [proxyen blog](http://blogs.technet.com/b/applicationproxyblog/) for de seneste nyheder og opdateringer


<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg
