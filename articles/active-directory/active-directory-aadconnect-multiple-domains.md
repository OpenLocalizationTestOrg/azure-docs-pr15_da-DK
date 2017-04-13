<properties
    pageTitle="Azure AD Connect flere domæner"
    description="I dette dokument beskrives opsætning og konfiguration af flere domæner på øverste niveau med O365 og Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Understøttelse af flere domæne for en sammenslutning til Azure AD
Følgende dokumentation giver vejledning i, hvordan du bruger flere domæner på øverste niveau og underordnede domæner, når en sammenslutning til Office 365 eller Azure AD-domæner.

## <a name="multiple-top-level-domain-support"></a>Understøttelse af flere topdomæner
Samle flere, topdomæner med Azure AD kræver yderligere konfiguration, ikke er påkrævet, når en sammenslutning til én topdomæner.

Når et domæne er knyttet til Azure AD, angives flere egenskaber på domænet i Azure.  Et vigtigt værdi, der er IssuerUri.  Dette er en URI, der bruges af Azure AD til at identificere det domæne, som er tilknyttet tokenet.  URI ikke har brug at løse til andet end det skal være en gyldig URI.  Som standard Azure AD angiver dette til værdien af sammenslutning service identifikation i dine lokale AD FS konfiguration.

>[AZURE.NOTE]Sammenslutning service id er en URI, der identificerer entydigt en sammenslutning tjeneste.  Tjenesten sammenslutning er en forekomst af AD FS, der fungerer som tjenesten for sikkerhedstoken. 

Du kan vew IssuerUri ved hjælp af PowerShell-kommando `Get-MsolDomainFederationSettings - DomainName <your domain>`.

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

Der opstår et problem, hvis vi vil tilføje mere end én topdomæner.  For eksempel Antag, at du har konfiguration sammenslutning mellem Azure AD og dit lokale miljø.  Jeg bruger bmcontoso.com for dette dokument.  Nu jeg har tilføjet et andet, på øverste niveau domæne, bmfabrikam.com.

![Domæner](./media/active-directory-multiple-domains/domains.png)

Når vi forsøger at konvertere vores bmfabrikam.com domæne, der skal være del af et organisationsnetværk, modtager vi en fejl.  Der er årsagen til dette, Azure AD har en begrænsning, der ikke tillader egenskaben IssuerUri skal have den samme værdi for mere end ét domæne.  
  

![Sammenslutning fejl](./media/active-directory-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>SupportMultipleDomain Parameter

Til løsning, vi har brug at tilføje en anden IssuerUri, som kan udføres ved hjælp af den `-SupportMultipleDomain` parameter.  Denne parameter bruges med følgende cmdletter:
    
- `New-MsolFederatedDomain`
- `Convert-MsolDomaintoFederated`
- `Update-MsolFederatedDomain`

Denne parameter gør Azure AD konfigurere IssuerUri, så den er baseret på navnet på domænet.  Det kan være entydigt på tværs af mapper i Azure AD.  Ved hjælp af parameteren kan PowerShell-kommando kan fuldføres.

![Sammenslutning fejl](./media/active-directory-multiple-domains/convert.png)

Se på indstillingerne for vores nye bmfabrikam.com domæne, du kan se følgende:

![Sammenslutning fejl](./media/active-directory-multiple-domains/settings.png)

Bemærk, at `-SupportMultipleDomain` ændrer ikke de andre slutpunkter, er stadig er konfigureret til at pege på vores sammenslutning service på adfs.bmcontoso.com.

En anden ting, der `-SupportMultipleDomain` betyder er, at det sikrer, at AD FS-systemet indeholder værdien stort udsteder i tokens, der er udstedt til Azure AD. Det gør dette ved at tage domæne del af brugere UPN og værdien indstilles som domænet i IssuerUri, det vil sige https://{upn suffiks} / adfs/services/sikkerhed og rettighedsadministration. 

Dermed under godkendelse til Azure AD eller Office 365, elementet IssuerUri i brugerens token bruges til at finde domænet i Azure AD.  Hvis ikke blev fundet et match på godkendelse mislykkes. 

Eksempelvis hvis en brugers UPN er bsimon@bmcontoso.com, elementet IssuerUri i token AD FS problemer indstilles til http://bmcontoso.com/adfs/services/trust. Dette svarer til Azure AD-konfigurationen, og godkendelse lykkes.

Følgende er tilpasset Kræv reglen, der implementerer denne logik:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type =   "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


>[AZURE.IMPORTANT]For at kunne bruge parameteren - SupportMultipleDomain, når du forsøger at tilføje nye eller konvertere allerede tilføjede domæner, skal du har konfigureret din organisationsnetværket tillid til at understøtte dem oprindeligt.  


## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Sådan opdaterer du tillidsforhold mellem AD FS og Azure AD
Hvis du ikke konfigurere organisationsnetværket tillid mellem AD FS og din forekomst af Azure AD, skal du muligvis genoprette denne sikkerhed og rettighedsadministration.  Dette skyldes, når den er oprindeligt konfiguration uden den `-SupportMultipleDomain` parameter, IssuerUri er konfigureret med standardværdien.  I skærmbilledet nedenfor du kan se IssuerUri er indstillet til https://adfs.bmcontoso.com/adfs/services/trust.

Så nu, hvis vi har tilføjet et nyt domæne i Azure AD-portalen og derefter forsøge at konvertere den ved hjælp af `Convert-MsolDomaintoFederated -DomainName <your domain>`, vi få vist følgende fejlmeddelelse.

![Sammenslutning fejl](./media/active-directory-multiple-domains/trust1.png)

Hvis du forsøger at tilføje den `-SupportMultipleDomain` Skift vi får vist følgende fejlmeddelelse:

![Sammenslutning fejl](./media/active-directory-multiple-domains/trust2.png)

Blot forsøger at køre `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` på det oprindelige domæne også medfører en fejl.

![Sammenslutning fejl](./media/active-directory-multiple-domains/trust3.png)

Brug følgende fremgangsmåde til at tilføje et ekstra domæne på øverste niveau.  Hvis du allerede har tilføjet et domæne og ikke har brugt den `-SupportMultipleDomain` parameter start med trin for at fjerne og opdatere dit oprindelige domæne.  Hvis du ikke har tilføjet et topdomæne kan endnu du starte med fremgangsmåden for at tilføje et domæne, ved hjælp af PowerShell af Azure AD Connect.

Du kan følge nedenstående trin for at fjerne Microsoft Online trust og opdatere dit oprindelige domæne.

2.  På din ADFS sammenslutningsserver, der er åben **AD FS Management.** 
2.  I venstre side, udvide **Hav tillid til relationer** og **Stole part har tillid til**
3.  Slette posten **Microsoft Office 365 identitet Platform** i højre side.
![Fjerne Microsoft Online](./media/active-directory-multiple-domains/trust4.png)
1.  Kør følgende på en computer, der har [Azure Active Directory-modulet til Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) installeret: `$cred=Get-Credential`.  
2.  Angiv brugernavn og adgangskoden til en global administrator for det Azure AD-domæne, du en sammenslutning med
2.  Angiv i PowerShell`Connect-MsolService -Credential $cred`
4.  Angiv i PowerShell `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`.  Dette er for det oprindelige domæne.  Så ved hjælp af ovenstående domænerne det ville være:`Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`


Brug følgende trin til at tilføje den nye topdomæne ved hjælp af PowerShell

1.  Kør følgende på en computer, der har [Azure Active Directory-modulet til Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) installeret: `$cred=Get-Credential`.  
2.  Angiv brugernavn og adgangskoden til en global administrator for det Azure AD-domæne, du en sammenslutning med
2.  Angiv i PowerShell`Connect-MsolService -Credential $cred`
3.  Angiv i PowerShell`New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Brug følgende trin til at føje det nye websted på øverste domæne ved hjælp af Azure AD-forbindelse.

1.  Start Azure AD-forbindelse fra skrivebordet eller i menuen start
2.  Vælg "Tilføj et ekstra Azure AD-domæne" ![tilføje en ekstra Azure AD-domæne](./media/active-directory-multiple-domains/add1.png)
3.  Angiv din Azure AD og legitimationsoplysningerne for Active Directory
4.  Vælg det andet domæne, du vil konfigurere til sammenslutning.
![Tilføje en ekstra Azure AD-domæne](./media/active-directory-multiple-domains/add2.png)
5.  Klik på Installer


### <a name="verify-the-new-top-level-domain"></a>Bekræfte den nye topdomæne
Ved hjælp af kommandoen PowerShell `Get-MsolDomainFederationSettings - DomainName <your domain>`du kan få vist den opdaterede IssuerUri.  Skærmbilledet nedenfor viser sammenslutning indstillinger er blevet opdateret på vores oprindelige domæne http://bmcontoso.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

Og IssuerUri på vores nye domæne er blevet indstillet til https://bmfabrikam.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/settings2.png)


##<a name="support-for-sub-domains"></a>Understøttelse af underdomæner
Når du tilføjer et underdomæne på grund af vejen Azure AD håndteres domæner, arver det indstillingerne i overordnet.  Det betyder, at IssuerUri skal svare til overordnede.

Så kan sig for eksempel, at jeg har bmcontoso.com og derefter tilføje corp.bmcontoso.com.  Det betyder, at IssuerUri for en bruger fra corp.bmcontoso.com skal være **http://bmcontoso.com/adfs/services/trust.**  Men standard reglen implementeret over for Azure AD vil oprette et token med en udsteder som **http://corp.bmcontoso.com/adfs/services/trust.** som ikke svarer til domænet påkrævet værdi og godkendelse mislykkes.

### <a name="how-to-enable-support-for-sub-domains"></a>Sådan aktiveres understøttelse af underdomæner
Før du kan løse dette problem AD FS afhængige part tillid for Microsoft Online, skal opdateres.  For at gøre dette, skal du konfigurere en regel for brugerdefineret krav, så det Rampe fra en hvilken som helst underdomæner fra brugerens UPN-suffiks, når bygning af den brugerdefinerede udsteder værdi. 

Følgende kravet gøre følgende:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^((.*)([.|@]))?(?<domain>[^.]*[.].*)$", "http://${domain}/adfs/services/trust/"));

Brug følgende trin til at tilføje et brugerdefineret krav til at understøtte underdomæner.

1.  Åbn AD FS Management
2.  Højreklik på Microsoft Online RP Hav tillid til, og vælg Rediger Kræv regler
3.  Vælg den tredje Kræv regel, og Erstat ![Rediger krav](./media/active-directory-multiple-domains/sub1.png)
4.  Erstat den aktuelle krav:
    
        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));
        
    med
    
        `c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^((.*)([.|@]))?(?<domain>[^.]*[.].*)$", "http://${domain}/adfs/services/trust/"));`
    
![Erstatte krav](./media/active-directory-multiple-domains/sub2.png)
5.  Klik på Ok.  Klik på Anvend.  Klik på Ok.  Luk AD FS Management.

