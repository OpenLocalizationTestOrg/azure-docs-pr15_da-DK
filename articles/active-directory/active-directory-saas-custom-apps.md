<properties 
    pageTitle="Konfiguration af single sign-on til programmer, der ikke er i galleriet Azure Active Directory-program | Microsoft Azure" 
    description="Få mere at vide hvordan forbindelse apps til Azure Active Directory ved hjælp af SAML og adgangskode-baserede SSO til selvbetjening" 
    services="active-directory" 
    authors="asmalser-msft"  
    documentationCenter="na" manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="02/09/2016" 
    ms.author="asmalser" />

#<a name="configuring-single-sign-on-to-applications-that-are-not-in-the-azure-active-directory-application-gallery"></a>Konfiguration af single sign-on til programmer, der ikke er i galleriet Azure Active Directory-program

I denne artikel handler om en funktion, som giver administratorer mulighed for at konfigurere single sign-on til programmer findes ikke i Azure Active Directory app galleriet *uden at skrive kode*. Denne funktion er blevet frigivet fra technical preview på 18 November 2015 og er inkluderet i [Azure Active Directory Premium](active-directory-editions.md). Hvis du søger i stedet for udvikler vejledning i at integrere brugerdefinerede apps med Azure AD via kode, kan du se [Godkendelse scenarier, hvor Azure AD](active-directory-authentication-scenarios.md).

Galleriet Azure Active Directory-program indeholder en oversigt over programmer, der er blevet konstateret, at understøtte en formular af single sign-on med Azure Active Directory, som beskrevet i [denne artikel](active-directory-appssoaccess-whatis.md). Når du (som en IT-specialist eller system integrator i organisationen) har fundet det program, du vil oprette forbindelse, du kan komme i gang ved at følge den trinvise vejledning præsenteres på administrationsportalen Azure til at aktivere enkeltlogon.

Kunder med [Azure Active Directory Premium](active-directory-editions.md) licenser også finde disse ekstra funktioner:

* Selvbetjenings-integration af alle programmer, der understøtter SAML 2.0 identitetsudbydere (SP-definerede eller IdP-definerede)
* Selvbetjenings-integration af et webprogram, der indeholder en HTML-baserede logon side med [adgangskode-baserede SSO](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
* Selvbetjening tilslutning af programmer, der bruger protokollen SCIM til bruger klargøring ([beskrevet her](active-directory-scim-provisioning.md))
* Muligheden for at tilføje links til et program i [Office 365-appstarteren](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) eller [Azure AD access panel](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)

Dette kan omfatte ikke kun SaaS programmer, du bruger, men har ikke endnu er blevet på-ske til galleriet Azure AD-program, men fra tredjepart webprogrammer, som din organisation er installeret på servere du styre, enten i skyen eller i det lokale miljø.

Disse funktioner, også kaldet *app integration skabeloner*giver forbindelsespunkter, der er baseret på standarder for apps, der understøtter SAML, SCIM eller formularbaseret godkendelse, og Medtag fleksible muligheder og indstillinger for kompatibilitet med en lang række programmer. 

##<a name="adding-an-unlisted-application"></a>Tilføje et program, der ikke står på listen

Log på administrationsportalen Azure ved hjælp af kontoen Azure Active Directory-administrator for at oprette forbindelse et program ved hjælp af en skabelon til app-integration, og gå til den **Active Directory > [Directory] > programmer** skal du vælge **Tilføj**, og klik derefter på **Tilføj et program i galleriet**. 

![][1]

Du kan tilføje en en-app ved hjælp af kategorien **brugerdefineret** i venstre side eller ved at vælge linket **Tilføj en programmet** , som vises i søgeresultaterne, hvis din ønskede app ikke blev fundet i galleriet app. Når du har angivet et navn til dit program, kan du konfigurere den enkelt sign-on – indstillinger og funktionalitet. 

**Hurtige tip**: brug søgefunktionen til at kontrollere, om programmet allerede findes i galleriet programmet på computeren som en bedste fremgangsmåde. Hvis appen er fundet og dens beskrivelse omtaler "single sign-on", og klik derefter programmet understøttes allerede til medlem af organisationsnetværket single sign-on. 

![][2]

Tilføje et program på denne måde giver en minder meget om oplevelse til den tilgængelig for allerede integrerede programmer. For at starte skal du vælge **Konfigurere Single Sign-On**. Det næste skærmbillede viser følgende tre indstillinger til konfiguration af enkelt Log på, som er beskrevet i følgende afsnit.

![][3]


##<a name="azure-ad-single-sign-on"></a>Azure AD-Single Sign-On

Vælg denne indstilling for at konfigurere SAML-baseret godkendelse for programmet. Dette kræver, at programunderstøttelse SAML 2.0, og du skal indsamle oplysninger om, hvordan du bruger SAML funktionerne i programmet, før du fortsætter. Når du har valgt **Næste**, bliver du bedt om at angive tre forskellige URL-adresser svarer til SAML slutpunkterne for programmet. 

![][4]
 
Dette er:

* **Log på URL-adresse (SP-definerede kun)** – hvor brugeren går til logon til dette program. Hvis programmet er konfigureret til at udføre service provider-definerede enkelt Log på, derefter, når en bruger navigerer til denne URL-adresse, gøre udbyderen af de nødvendige omdirigering til Azure AD til at godkende og logge på som bruger i. Hvis dette felt er udfyldt, derefter Azure AD skal bruge denne URL-adresse for at starte programmet fra Office 365 og panelet Azure AD-adgang. Hvis dette felt er ommited og derefter Azure AD i stedet udfører identitetsudbyder-startet sign-on når appen startes fra Office 365, panelet Azure AD adgang eller fra Azure AD single sign-on – URL-adresse (copiable fra fanen Dashboard).

* **Udsteder URL-adresse** - udsteder URL-adressen skal entydigt identificerer programmet til hvilke enkelt logge på konfigureres. Dette er den værdi, der sender Azure AD tilbage til anvendelse som parameteren **Målgruppe** af SAML tokenet, og der forventes programmet til at validere det. Denne værdi vises også som **Enheds-ID** i en hvilken som helst SAML metadata, der leveres af programmet. Kontrollere programmets SAML dokumentation få mere at vide om, hvad det er enheds-ID eller målgruppe værdi. Nedenfor er et eksempel på hvordan målgruppe URL-adressen vises i SAML tokenet returneres til programmet:

```
    <Subject>
    <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:unspecificed">chad.smith@example.com</NameID>
        <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
      </Subject>
      <Conditions NotBefore="2014-12-19T01:03:14.278Z" NotOnOrAfter="2014-12-19T02:03:14.278Z">
        <AudienceRestriction>
          <Audience>https://tenant.example.com</Audience>
        </AudienceRestriction>
      </Conditions>
```

* **Svar URL** - svar URL-adressen er, hvor programmet forventer at modtage SAML tokenet. Dette kaldes også **program Consumer ACS (Service) URL-adresse**. Dokumentationen programmets SAML få mere at vide om, hvad SAML token besvarelsen ACS URL-adressen eller.
 Når disse er indsat, skal du klikke på **Næste** for at gå videre til næste skærmbillede. Dette skærmbillede indeholder oplysninger om, hvad der skal konfigureres i programmet side til at aktivere den til at acceptere et SAML token fra Azure AD. 

![][5]

Hvilke værdier der kræves kan variere, afhængigt af programmet, så kig programmets SAML dokumentation få mere at vide. **Log på** og **logge** service URL-adressen begge løse til den samme slutpunkt, som er SAML behandling af anmodning slutpunkt for din forekomst af Azure AD. Udsteder URL-adressen er den værdi, der vises som den "udsteder" i SAML tokenet udstedt til programmet. 

Når dit program er konfigureret, kan du klikke på knappen **Næste** , og klik derefter på **udført** for at lukke dialogboksen. 

##<a name="assigning-users-and-groups-to-your-saml-application"></a>Tildele brugere og grupper til dit SAML-program 

Når dit program er konfigureret for at bruge Azure AD som en SAML-baseret identitetsudbyder, er det næsten klar til at teste. Som et kontrolelement med sikkerhed sende Azure AD ikke et token, så de kan logge på programmet, medmindre de har fået adgang til ved hjælp af Azure AD. Brugere kan have tildelt adgang direkte eller via en gruppe, som de er medlem af. 

Hvis du vil tildele en bruger eller gruppe til dit program, skal du klikke på knappen **Tildel brugere** . Vælg den bruger eller gruppe, du vil tildele, og vælg derefter knappen **tildele** . 

![][6]

Tildele en bruger, så Azure AD at udstede et token til brugeren, samt forårsager et felt for dette program vises i brugerens Access Panel. En programmet vises også i Office 365-program starteren, hvis brugeren bruger Office 365. 

Du kan overføre et felt logo til programmet ved hjælp af knappen **Overfør Logo** under fanen **Konfigurer** for programmet. 

###<a name="customizing-the-claims-issued-in-the-saml-token"></a>Tilpasse krav udstedt i SAML tokenet 

Når en bruger godkendes til programmet, udstede en SAML token Azure AD til appen, der indeholder oplysninger (eller krav) om den bruger, der entydigt identificerer dem. Som standard omfatter dette brugerens brugernavn, din mailadresse, fornavn og efternavn. 

Du kan få vist eller redigere de krav, der er sendt i SAML tokenet til programmet under fanen **attributter** . 

![][7]

Der er to mulige årsager, hvorfor du muligvis skal redigere krav udstedt i SAML tokenet: •området programmet er blevet skrevet til at kræve en anden række krav URI'er eller Kræv værdier •din program er blevet installeret på en måde, der kræver NameIdentifier gøre krav skal være et andet tal end det brugernavn (AKA brugerens hovednavn), der er gemt i Azure Active Directory. 

Oplysninger om, hvordan du kan tilføje og redigere krav om disse scenarier, se i denne [artikel om tilpasning af krav](active-directory-saml-claims-customization.md). 

###<a name="testing-the-saml-application"></a>Tester SAML-program 

Når SAML URL-adresser og certifikat er konfigureret i Azure AD og i programmet, brugere eller grupper, der er tildelt til programmet i Azure, og krav er gennemgået og redigeres, hvis det er nødvendigt, og derefter brugeren er klar til at logge på programmet. 

Teste blot logge på panelet Azure AD adgang på https://myapps.microsoft.com ved hjælp af en brugerkonto, du har tildelt programmet, og klik derefter på på feltet for programmet for at starte enkelt enkeltlogon-processen. Alternativt kan du gå direkte til den Sign-On URL-adressen til det program, og log på derfra. 

Se denne [artikel om hvordan du udfører fejlfinding SAML-baseret single sign-on til programmer](active-directory-saml-debugging.md) til fejlfinding tip 

##<a name="password-single-sign-on"></a>Adgangskode Single Sign-On

Vælg denne indstilling til at konfigurere [adgangskode-baserede enkeltlogon](active-directory-appssoaccess-whatis.md) for et webprogram, der indeholder en HTML-logonsiden. Adgangskode-baserede SSO, der også kaldes adgangskode vaulting, gør det muligt at administrere brugeradgang og adgangskoder til webprogrammer, som ikke understøtter identitetssammenslutninger. Det er også nyttig til scenarier, hvor flere brugere skal dele en enkelt konto, f.eks, til din organisations sociale medier app-konti. 

Når du har valgt **Næste**, bliver du bedt om at angive URL'EN for programmets webbaseret logon side. Bemærk, at det skal være den side, der indeholder felterne brugernavn og din adgangskode input. Når du har angivet, starter Azure AD en proces til at fortolke logonsiden for et input brugernavn og en adgangskode, der er indtastet. Hvis processen ikke lykkes, derefter hjælper det dig med en alternativ processen med at installere en browser-udvidelse (kræver Internet Explorer, Chrome eller Firefox), der gør det muligt at tage felterne manuelt.

Når siden Log på, registreres, brugere og grupper kan blive udpeget og legitimationsoplysninger politikker kan angives ligesom almindelige [adgangskode SSO apps](active-directory-appssoaccess-whatis.md).

Bemærk: Du kan overføre et felt logo til programmet ved hjælp af knappen **Overfør Logo** under fanen **Konfigurer** for programmet. 

##<a name="existing-single-sign-on"></a>Eksisterende Single Sign-On

Vælg denne indstilling for at tilføje et link til programmet til din organisations Azure AD Access Panel eller Office 365-portalen. Du kan bruge det til at føje links til brugerdefineret web-apps, der aktuelt bruger Azure Active Directory Federation Services (eller en anden sammenslutning tjeneste) i stedet for Azure AD til godkendelse. Eller du kan tilføje deep links til bestemte SharePoint-sider eller andre websider, der kun skal vises på din bruger Access paneler. 

Når du har valgt **Næste**, bliver du bedt om at angive URL-adressen til programmet til at oprette et link til. Når fuldført, kan brugere og grupper tildeles til programmet, som får programmet skal vises i [Office 365-appstarteren](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) eller [Azure AD access panel](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users) for disse brugere.

Bemærk: Du kan overføre et felt logo til programmet ved hjælp af knappen **Overfør Logo** under fanen **Konfigurer** for programmet.

## <a name="related-articles"></a>Relaterede artikler

- [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)
- [Hvordan du tilpasser krav udstedt i SAML Token for allerede integrerede Apps](active-directory-saml-claims-customization.md)
- [Fejlfinding i forbindelse med SAML-baseret Single Sign-On](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saas-custom-apps/customapp1.png
[2]: ./media/active-directory-saas-custom-apps/customapp2.png
[3]: ./media/active-directory-saas-custom-apps/customapp3.png
[4]: ./media/active-directory-saas-custom-apps/customapp4.png
[5]: ./media/active-directory-saas-custom-apps/customapp5.png
[6]: ./media/active-directory-saas-custom-apps/customapp6.png
[7]: ./media/active-directory-saas-custom-apps/customapp7.png
