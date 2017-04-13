<properties
    pageTitle="Foretage fejlfinding af proxyen | Microsoft Azure"
    description="Omhandler, hvordan du kan foretage fejlfinding i Azure AD-proxyen."
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
    ms.date="08/19/2016"
    ms.author="kgremban"/>



# <a name="troubleshoot-application-proxy"></a>Foretage fejlfinding af proxyen

Hvis der opstår fejl, i at få adgang til et udgivet program eller i udgivelse af programmer skal du kontrollere følgende indstillinger for at se, om Microsoft Azure AD-proxyen fungerer korrekt:

- Åbn konsollen Windows tjenester og bekræfte, at tjenesten **Microsoft AAD programmet Proxy Connector** er aktiveret og kører. Vil du måske også at kigge på siden proxyen service egenskaber, som vist på følgende billede:  
  ![Skærmbillede af vinduet Egenskaber i Microsoft AAD Application Proxy-forbindelse](./media/active-directory-application-proxy-troubleshoot/connectorproperties.png)

- Åbn Logbog og se efter proxyen forbindelse begivenheder i **Services logfiler for programmer og** > **Microsoft** > **AadApplicationProxy** > **forbindelse** > **administrator**.
- Hvis det er nødvendigt, er mere detaljerede logfiler tilgængelige ved at slå analyser og fejlfinding logfiler og aktivering af proxyen forbindelse sessionslogfilen.

## <a name="the-page-is-not-rendered-correctly"></a>Siden gengives ikke korrekt

Hvis du ikke kan høre en bestemt fejlmeddelelse, kan du stadig har problemer med programmet gengivelse eller fungerer ikke korrekt. Dette kan ske, hvis du har udgivet artikel stien, men programmet kræver indhold, der findes uden for denne sti.

Eksempelvis hvis du publicerer stien https://yourapp/app, men programmet kalder billeder i https://yourapp/media, gengives de ikke. Sørg for, at du udgiver programmet med den højeste niveau sti, du har brug for at medtage alle relevante indhold. I dette eksempel vil det være http://yourapp/.

Hvis du ændrer din sti for at medtage der refereres til indhold, men stadig har brug for brugerne at lander på et dybere link i stien, kan du se blogindlægget [konfiguration linket højre for proxyen programmer i Azure AD access panelet og Office 365-appstarteren](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/).

## <a name="general-errors"></a>Generelle fejl

Fejl | Beskrivelse | Opløsning
--- | --- | ---
Denne virksomhedens app kan ikke åbnes. Du har ikke tilladelse til at få adgang til dette program. Godkendelsen mislykkedes. Sørg for at tildele brugere med adgang til dette program. | Du kan ikke er tildelt brugeren om dette program. | Gå til fanen **program** , og under **brugere og grupper**, tildele denne bruger eller brugergruppe til dette program.
Denne virksomhedens app kan ikke åbnes. Du har ikke tilladelse til at få adgang til dette program. Godkendelsen mislykkedes. Sørg for, at brugeren har en licens til Azure Active Directory Premium eller Basic. | Dine brugere kan få denne fejl, når du forsøger at få adgang til den app, du publicerede, hvis de ikke eksplicit tildelt med en Premium/Basic licens af den abonnement administrator. | Gå til den abonnement Active Directory **licenser** fane, og Sørg for, at denne bruger eller gruppe, der er tildelt en Premium eller grundlæggende licens.


## <a name="connector-troubleshooting"></a>Foretage fejlfinding af forbindelse
Hvis registrering mislykkes under forbindelse guiden installation, er der to måder at få vist årsagen til fejlen. Søg i hændelseslog under **programmer og tjenester Logs\Microsoft\AadApplicationProxy\Connector\Admin**, enten eller køre følgende Windows PowerShell-kommando.

    Get-EventLog application –source “Microsoft AAD Application Proxy Connector” –EntryType “Error” –Newest 1

| Fejl | Beskrivelse | Opløsning |
| --- | --- | --- |
| Forbindelse registreringen mislykkedes: Sørg for, at du har aktiveret proxyen på portalen Azure administration og, at du har angivet din Active Directory brugernavnet og adgangskoden korrekt. Fejl: "en eller flere opstod fejl." | Du har lukket vinduet registrering uden at udføre login til Azure AD. | Køre guiden forbindelse igen og registrere forbindelsen. |
| Forbindelse registreringen mislykkedes: Sørg for, at du har aktiveret proxyen på portalen Azure administration og, at du har angivet din Active Directory brugernavnet og adgangskoden korrekt. Fejl: "AADSTS50001: ressource `https://proxy.cloudwebappproxy.net /registerapp` er deaktiveret." | Proxyen er deaktiveret.  | Kontrollér, at du aktiverer proxyen på portalen Azure klassisk før du forsøger at registrere forbindelsen. Du kan finde yderligere oplysninger om aktivering af proxyen, [aktivere proxyen tjenester](active-directory-application-proxy-enable.md). |
| Forbindelse registreringen mislykkedes: Sørg for, at du har aktiveret proxyen på portalen Azure administration og, at du har angivet din Active Directory brugernavnet og adgangskoden korrekt. Fejl: "en eller flere opstod fejl." | Hvis vinduet til åbnes og lukker derefter med det samme uden gør det muligt at logge på, får du sandsynligvis denne fejl. Denne fejl opstår, når der er fejlen netværk på din computer. | Sørg for, at det er muligt at oprette forbindelse fra en browser til et offentligt websted og at portene er åbne som angivet i [proxyen forudsætninger](active-directory-application-proxy-enable.md). |
| Forbindelse registreringen mislykkedes: Sørg for, at computeren har forbindelse til internettet. Fejl: "der var ikke noget slutpunkt lytter på `https://connector.msappproxy.net :9090/register/RegisterConnector` , der kan acceptere meddelelsen. Dette skyldes ofte en forkert adresse eller SOAP-handling. Se InnerException, hvis de findes, få mere at vide. " | Hvis du logge på med dit Azure AD-brugernavn og din adgangskode, men du får vist denne fejl, kan det være, at alle porte over 8081 blokeres. | Sørg for, at de nødvendige porte er åbne. Du kan finde yderligere oplysninger finder [proxyen forudsætninger](active-directory-application-proxy-enable.md). |
| Ryd fejl vises i vinduet registrering. – Kan ikke fortsætte kun at lukke vinduet. | Du har angivet forkert brugernavn eller adgangskode. | Prøv igen. |
| Forbindelse registreringen mislykkedes: Sørg for, at du har aktiveret proxyen på portalen Azure administration og, at du har angivet din Active Directory brugernavnet og adgangskoden korrekt. Fejl: "AADSTS50059: ingen lejer-identificerende oplysninger findes i enten anmodningen, eller som en hvilken som helst medfølgende legitimationsoplysninger og Søg efter tjenesten princippet URI mislykkes. | Du forsøger at logge på med en Microsoft-Account og ikke et domæne, der er en del af organisations-ID på den mappe, du forsøger at få adgang til. | Sørg for, at administratoren er en del af det samme domænenavn som lejer domænet, for eksempel hvis Azure AD-domæne er contoso.com skal administratoren skal være admin@contoso.com. |
| Der kunne ikke hente den aktuelle udførelse af politik til kørsel af PowerShell-scripts. | Hvis forbindelsen installationen mislykkes, skal du kontrollere Sørg for, at PowerShell udførelse af politik ikke er deaktiveret. | Åbne redigeringsprogrammet til gruppepolitik. Gå til **Konfiguration computeren** > **Administrative skabeloner** > **Windows-komponenter** > **Windows PowerShell** , og dobbeltklik på **Slå udførelse af Script**. Dette kan angives til **Ikke konfigureret** eller **aktiveret**. Hvis indstillet til **aktiveret**skal du sørge for, under indstillinger for den udførelse af politik er indstillet til enten **Tillad lokale scripts og remote signeret** eller **Tillad alle scripts**. |
| Forbindelsen kunne ikke hentes konfigurationen. | Forbindelsens klientcertifikat, som bruges til godkendelse, udløbet. Dette sker også, hvis du har installeret bag en proxy forbindelsen. I dette tilfælde forbindelsen kan ikke få adgang til internettet og vil ikke kunne give programmer for at eksterne brugere. | Forny tillid manuelt ved hjælp af den `Register-AppProxyConnector` cmdlet i Windows PowerShell. Hvis din forbindelse er bag en proxy, er det nødvendigt at give adgang til internettet til Connector-konti "netværk services" og "lokalt system." Dette kan gøres ved at give dem adgang til proxyen eller ved at angive dem til at tilsidesætte proxyen. |
| Forbindelse registreringen mislykkedes: Sørg for, at du er en Global Administrator for din Active Directory til at registrere forbindelsen. Fejl: "registrering anmodningen blev afvist." | Det alias, du forsøger at logge på med er ikke administrator for dette domæne. Forbindelsen er altid installeret på den mappe, der ejer brugerens domæne. | Sørg for, at administratoren du forsøger at logge på, som har globale tilladelser til Azure AD-lejer.|


## <a name="kerberos-errors"></a>Kerberos-fejl

| Fejl | Beskrivelse | Opløsning |
| --- | --- | --- |
| Der kunne ikke hente den aktuelle udførelse af politik til kørsel af PowerShell-scripts. | Hvis forbindelsen installationen mislykkes, skal du kontrollere Sørg for, at PowerShell udførelse af politik ikke er deaktiveret. | Åbne redigeringsprogrammet til gruppepolitik. Gå til **Konfiguration computeren** > **Administrative skabeloner** > **Windows-komponenter** > **Windows PowerShell** , og dobbeltklik på **Slå udførelse af Script**. Dette kan angives til **Ikke konfigureret** eller **aktiveret**. Hvis angivet til **aktiveret**skal du sørge for, under indstillinger for den udførelse af politik er indstillet til enten **Tillad lokale scripts og remote signeret** eller **Tillad alle scripts**. |
| 12008 - azure AD overskredet det maksimale antal tilladte Kerberos-godkendelse forsøg på at back end-serveren. | Denne hændelse kan skyldes forkert konfiguration mellem Azure AD og programserver back end- eller et problem i dato og klokkeslæt konfiguration på begge computere. | Back end-serveren afvist Kerberos-hændelsen, der er oprettet af Azure AD. Bekræfte den Azure AD og back end-programmet på serveren er konfigureret korrekt. Sørg for, at konfigurationen af dato og klokkeslæt på Azure AD- og back end-programmet på serveren er synkroniseret. |
| 13016 - azure AD kan ikke hentes en Kerberos brugertilladelse på vegne af brugeren, fordi der ikke er nogen UPN i tokenet kant eller i en access-cookie. | Der er problemer med konfigurationen af STS. | Ret UPN Kræv konfiguration i STS. |
| 13019 - azure AD kan ikke hentes en Kerberos brugertilladelse på vegne af brugeren på grund af følgende generelle API-fejl. | Denne hændelse kan skyldes forkert konfiguration mellem Azure AD og domænecontroller-serveren eller et problem i dato og klokkeslæt konfiguration på begge computere. | Domænecontrolleren afslået Kerberos-hændelsen, der er oprettet af Azure AD. Bekræfte pågældende Azure AD og back end-programmet på serveren er konfigureret korrekt, især SPN konfigurationen. Sørg for Azure AD er tilsluttet det samme domæne som domænecontrolleren for at sikre, at domænecontrolleren opretter sikkerhed og rettighedsadministration med Azure AD domæne. Sørg for, at konfigurationen af dato og klokkeslæt på Azure AD og domænecontrolleren synkroniseres. |
| 13020 - azure AD kan ikke hentes en Kerberos brugertilladelse på vegne af brugeren, da back end-database fundet ikke er defineret. | Denne hændelse kan skyldes forkert konfiguration mellem Azure AD og domænecontroller-serveren eller et problem i dato og klokkeslæt konfiguration på begge computere. | Domænecontrolleren afslået Kerberos-hændelsen, der er oprettet af Azure AD. Bekræfte den Azure AD og back end-programmet på serveren er konfigureret korrekt, især SPN konfigurationen. Sørg for Azure AD er tilsluttet det samme domæne som domænecontrolleren for at sikre, at domænecontrolleren opretter sikkerhed og rettighedsadministration med Azure AD domæne. Sørg for, at konfigurationen af dato og klokkeslæt på Azure AD og domænecontrolleren synkroniseres. |
| 13022 - azure AD godkende ikke brugeren, fordi back end-serveren besvarer Kerberos-godkendelsesforsøg med en HTTP 401 fejl. | Denne hændelse kan skyldes forkert konfiguration mellem Azure AD og programserver back end- eller et problem i dato og klokkeslæt konfiguration på begge computere. | Back end-serveren afvist Kerberos-hændelsen, der er oprettet af Azure AD. Bekræfte pågældende Azure AD og back end-programmet på serveren er konfigureret korrekt. Sørg for, at konfigurationen af dato og klokkeslæt på Azure AD- og back end-programmet på serveren er synkroniseret. |
| Webstedet kan ikke få vist siden. | Brugeren kan få denne fejl, når du forsøger at få adgang til den app, du publicerede Hvis programmet som en IWA. Det definerede hovednavn for tjenesten til dette program kan være forkerte. | Til IWA apps: Sørg for, at det hovednavn for tjenesten er konfigureret til dette program er korrekte. |
| Webstedet kan ikke få vist siden. | Brugeren kan få denne fejl, når du forsøger at få adgang til den app, du publicerede Hvis programmet som en OWA. Dette kan skyldes et af følgende: <br> -Det definerede hovednavn for tjenesten til dette program er forkert. <br> -Den bruger, der har forsøgt at få adgang til programmet bruger en Microsoft-konto i stedet for de korrekte virksomhedskonto til at logge på, eller brugeren er en gæstebrugere. <br> -Den bruger, der har forsøgt at få adgang til programmet er ikke defineret korrekt for dette program i lokalt side. | Disse trin for at reducere tilsvarende: <br> -Sørg for, at det hovednavn for tjenesten er konfigureret til dette program er korrekte. <br> -Sørg for, at brugeren logger på med deres virksomhedens konto, der svarer til domænet for det udgivne program. Microsoft-Account brugere og Gæst kan ikke få adgang til IWA programmer. <br> -Sørg for, at denne bruger har de rette tilladelser, som defineret for denne back end-program på computeren lokalt. |
| Denne virksomhedens app kan ikke åbnes. Du har ikke tilladelse til at få adgang til dette program. Godkendelsen mislykkedes. Sørg for at tildele brugere med adgang til dette program. | Dine brugere kan få denne fejl, når du forsøger at få adgang til den app, du publicerede, hvis de bruger Microsoft-konti i stedet for deres virksomhedskonto til at logge på. Gæstebrugere kan også få denne fejl. | Microsoft-Account brugere og gæster kan ikke få adgang til IWA programmer. Kontrollér, at brugeren logger på med deres virksomhedens konto, der svarer til domænet for det udgivne program. |
| Denne virksomhedens app kan ikke åbnes lige nu. Prøv igen senere... Forbindelsen fik timeout. | Dine brugere kan få denne fejl, når du forsøger at få adgang til den app, du publicerede, hvis de ikke korrekt er defineret for dette program i lokalt side. | Sørg for, at dine brugere har de rette tilladelser, som defineret for denne back end-program på computeren lokalt. |


## <a name="see-also"></a>Se også

- [Aktivere proxyen for Azure Active Directory](active-directory-application-proxy-enable.md)
- [Udgive programmer med proxyen](active-directory-application-proxy-publish.md)
- [Aktivere enkeltlogon](active-directory-application-proxy-sso-using-kcd.md)
- [Aktivere betinget adgang](active-directory-application-proxy-conditional-access.md)

Se [proxyen blog](http://blogs.technet.com/b/applicationproxyblog/) for de seneste nyheder og opdateringer


<!--Image references-->
[1]: ./media/active-directory-application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
