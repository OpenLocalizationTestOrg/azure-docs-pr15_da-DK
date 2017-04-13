<properties
    pageTitle="Programmer, der bruger betinget adgangsregler i Azure Active Directory | Microsoft Azure"
    description="Med betinget adgangskontrol kontrollerer Azure Active Directory for bestemte betingelser, når den godkender brugeren, og for at give adgang til programmer."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/26/2016"
    ms.author="markvi"/>

# <a name="applications-that-use-conditional-access-rules-in-azure-active-directory"></a>Programmer, der bruger betinget adgangsregler i Azure Active Directory

Betinget adgangsregler understøttes i Azure Active Directory (Azure AD)-forbindelse programmer, før integreret organisationsnetværket software som en (SaaS) tjenesteprogrammer, programmer, der bruger adgangskode enkeltlogon (SSO), line of business-programmer og programmer, der bruger Azure AD-proxyen. Se [Services aktiveret med betinget adgang](active-directory-conditional-access-technical-reference.md#Services-enabled-with-conditional-access)til en detaljeret liste over, kan du bruge betinget access-programmer. Betinget access fungerer både med bærbare og stationære programmer, der bruger moderne godkendelse. I denne artikel dækker vi hvordan betinget access fungerer i bærbare og stationære apps.

Du kan bruge Azure AD-logon sider i programmer, der bruger moderne godkendelse. Med en logon side er en brugeren bedt om til multi-Factor authentication. Meddelelsen vises Hvis brugerens adgang blokeres. Moderne godkendelse er påkrævet for kameraet, for at godkende med Azure AD, så enhed-baserede betinget access politikker evalueres.

Det er vigtigt at vide, hvilke programmer kan bruge betinget adgangsregler, og de trin, som du muligvis tage til at sikre indgangspunkter andre programmer.

## <a name="applications-that-use-modern-authentication"></a>Programmer, der bruger moderne godkendelse

> [AZURE.NOTE] Hvis du har en betinget access-politik i Azure AD, der har en tilsvarende i Office 365, kan du konfigurere begge betinget access politikker. Dette vil anvende, for eksempel på betinget access politikker for Exchange Online eller SharePoint Online.

Følgende programmer understøtter betinget adgang til Office 365 og andre Azure AD-forbindelse tjenesteprogrammer:

| Target service  | Platform  | Program                                                  |
|--------------|-----------------|----------------------------------------------------------------|
|Office 365 Exchange Online | Windows 10|Kalender-mail/personer app, Outlook 2016, Outlook 2013 (med moderne godkendelse), Skype for Business (med moderne godkendelse)|
|Office 365 Exchange Online| Windows 8.1, Windows 7 |Outlook 2016, Outlook 2013 (med moderne godkendelse), Skype for Business (med moderne godkendelse)|
|Office 365 Exchange Online|iOS, Android|  Outlook-mobilappen|
|Office 365 Exchange Online|Mac OS X| Outlook 2016 til multi-Factor godkendelse og placering. understøttelse af enhed-baseret politik planlagt i fremtiden, Skype for Business-support, der er planlagt i fremtiden|
|Office 365 SharePoint Online|Windows 10| Office 2016 apps, Universal Office-apps, Office 2013 (med moderne godkendelse), OneDrive for Business-appen (næste generering af synkroniseringsklient eller NGSC) understøtter planlagt i fremtiden, Office-grupper support planlagt i fremtiden, SharePoint-app support planlagt i fremtiden|
|Office 365 SharePoint Online|Windows 8.1, Windows 7|Office 2016 apps, Office 2013 (med moderne godkendelse), OneDrive for Business-appen (Groove-synkroniseringsklienten)|
|Office 365 SharePoint Online|iOS, Android|  Office-mobilapps |
|Office 365 SharePoint Online|Mac OS X| Office 2016 apps til multi-Factor authentication og placering. understøttelse af enhed-baseret politik planlagt i fremtiden|
|Office 365 Yammer|Windows 10, iOS og Android | Office Yammer-app|
|Dynamics CRM|Windows 10, Windows 8.1, Windows 7, iOS og Android | Dynamics CRM-app|
|PowerBI service|Windows 10, Windows 8.1, Windows 7, iOS og Android | PowerBI app|
|Azure Remote-App-tjenesten|Windows 10, Windows 8.1, Windows 7, iOS, Android og Mac OS X |Azure Remote-app|
|Alle mine Apps app-tjenesten|Android- og iOS|Alle mine Apps app-tjenesten |


## <a name="applications-that-do-not-use-modern-authentication"></a>Programmer, der ikke bruger moderne godkendelse

I øjeblikket, skal du bruge andre metoder til at blokere adgang til apps, der ikke bruger moderne godkendelse. Adgangsregler til apps, der ikke bruger moderne godkendelse håndhæves ikke af betinget adgang. Dette er primært en overvejelser for adgang til Exchange og SharePoint. De fleste tidligere versioner af apps bruger ældre access kontrolelement protokoller.

### <a name="control-access-in-office-365-sharepoint-online"></a>Styre adgangen i Office 365 SharePoint Online
Du kan deaktivere ældre protokoller for SharePoint-adgang ved hjælp af cmdlet'en Set-SPOTenant til. Brug denne cmdlet til at forhindre, at Office-klienter, der bruger ikke moderne godkendelsesprotokoller adgang til SharePoint Online-ressourcer.

**Eksempel-kommandoen**:    `Set-SPOTenant -LegacyAuthProtocolsEnabled $false`

### <a name="control-access-in-office-365-exchange-online"></a>Styre adgangen i Office 365 Exchange Online

Exchange har to overordnede kategorier af protokoller. Gennemse følgende indstillinger, og vælg derefter den politik, der passer til din organisation.

-   **Exchange ActiveSync**. Som standard håndhæves ikke betinget access politikker for multi-Factor godkendelse og placering for Exchange ActiveSync. Du har brug at beskytte adgang til disse tjenester ved at konfigurere Exchange ActiveSync-politikken direkte eller ved at blokere Exchange ActiveSync ved hjælp af Active Directory Federation Services (AD FS) regler.
-   **Ældre protokoller**. Du kan blokere ældre protokoller med AD FS. Denne blokerer for adgang til ældre Office-klienter, som Office 2013 uden et moderne godkendelse er aktiveret og tidligere versioner af Office.


### <a name="use-ad-fs-to-block-legacy-protocol"></a>Brug AD FS til at blokere ældre protokol

Du kan bruge følgende eksempel regler til at blokere ældre protokol access på niveauet for AD FS. Vælg to almindelige konfigurationer.

#### <a name="option-1-allow-exchange-activesync-and-allow-legacy-apps-but-only-on-the-intranet"></a>Mulighed 1: Tillad Exchange ActiveSync og tillade ældre apps, men kun på intranettet

Ved at anvende de følgende tre regler til den AD FS stole part tillid til Microsoft Office 365 identitet Platform, Exchange ActiveSync trafik og browser og moderne godkendelsestrafik, du har adgang til. Ældre apps blokeres fra den ekstranet.

##### <a name="rule-1"></a>Regel 1

    @RuleName = “Allow all intranet traffic”
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-2"></a>Regel 2

    @RuleName = “Allow Exchange ActiveSync ”
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-3"></a>Regel 3

    @RuleName = “Allow extranet browser and browser dialog traffic”
    c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

#### <a name="option-2-allow-exchange-activesync-and-block-legacy-apps"></a>Mulighed 2: Tillader Exchange ActiveSync og blokere ældre apps

Ved at anvende de følgende tre regler til den AD FS stole part tillid til Microsoft Office 365 identitet Platform, Exchange ActiveSync trafik og browser og moderne godkendelsestrafik, du har adgang til. Ældre apps blokeres fra enhver placering.

##### <a name="rule-1"></a>Regel 1

    @RuleName = “Allow all intranet traffic only for browser and modern authentication clients”
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-2"></a>Regel 2

    @RuleName = “Allow Exchange ActiveSync”
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-3"></a>Regel 3

    @RuleName = “Allow extranet browser and browser dialog traffic”
    c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");
