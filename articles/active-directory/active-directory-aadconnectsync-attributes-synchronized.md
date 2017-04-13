<properties
    pageTitle="Azure AD-forbindelse synkronisering: attributter, der er synkroniseret med Azure Active Directory | Microsoft Azure"
    description="Oversigt over de attributter, der er synkroniseret til Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="markvi;andkjell"/>


# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Azure AD-forbindelse synkronisering: attributter, der er synkroniseret med Azure Active Directory
I dette emne beskrives de attributter, der er synkroniseret ved synkronisering af Azure AD-forbindelse.  
Attributterne er grupperet efter den relaterede Azure AD-app.

## <a name="attributes-to-synchronize"></a>Attributter til at synkronisere
Et almindelige spørgsmål er, *Hvad er listen over mindste attributter til at synkronisere*. Standard- og anbefalede tilgang er at placere standardegenskaberne, så en fuld GAL (Global adresseliste) kan være opbygget i skyen og for at hente alle funktioner i Office 365 arbejdsmængder. I nogle tilfælde, der er nogle attributter, din organisation ikke vil synkroniseret til skyen da disse attributter indeholder følsomme eller PII (personlige oplysninger) data, som i dette eksempel:  
![forkert attributter](./media/active-directory-aadconnectsync-attributes-synchronized/badextensionattribute.png)

I dette tilfælde skal starte med listen over attributter i dette emne, og identificere de attributter, vil indeholde store og små bogstaver eller PII data og kan ikke synkroniseres. Fravælg derefter disse attributter under installationen, ved hjælp af [Azure AD app og attributten filtrering](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering).

>[AZURE.WARNING] Når fravalg af attributter, skal du være forsigtig og kun fjerne markeringen af de attributter, der er absolut ikke muligt at synkronisere. Fravælge andre attributter kan have en negativ indflydelse på funktioner.

## <a name="office-365-proplus"></a>Office 365 ProPlus

| Attributnavn| Bruger| Kommentar |
| --- | :-: | --- |
| accountEnabled| X| Angiver, om en konto er aktiveret.|
| CN| X|  |
| vist navn| X|  |
| objectSID| X| mekanisk egenskab. AD-bruger-id, der bruges til at vedligeholde synkronisering mellem Azure AD og AD.|
| pwdLastSet| X| mekanisk egenskab. Bruges til at vide, hvornår ødelægge allerede udstedt tokens. Bruges både synkronisering af adgangskoder og sammenslutning.|
| sourceAnchor| X| mekanisk egenskab. Fast id til at bevare forholdet mellem tilføjer og Azure AD.|
| usageLocation| X| mekanisk egenskab. Brugerens land. Bruges til licensaftalen.|
| userPrincipalName| X| UPN er logon-ID til brugeren. Oftest bruger den samme som [mail] værdi.|

## <a name="exchange-online"></a>Exchange Online

| Attributnavn| Bruger| Kontakt| Gruppe| Kommentar |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Angiver, om en konto er aktiveret.|
| assistent| X| X|  |  |
| authOrig| X| X| X|  |
| c| X| X|  |  |
| CN| X|  | X|  |
| CO| X| X|  |  |
| virksomhed| X| X|  |  |
| countryCode| X| X|  |  |
| afdeling| X| X|  |  |
| Beskrivelse| X| X| X|  |
| vist navn| X| X| X|  |
| dLMemRejectPerms| X| X| X|  |
| dLMemSubmitPerms| X| X| X|  |
| extensionAttribute1| X| X| X|  |
| extensionAttribute10| X| X| X|  |
| extensionAttribute11| X| X| X|  |
| extensionAttribute12| X| X| X|  |
| extensionAttribute13| X| X| X|  |
| extensionAttribute14| X| X| X|  |
| extensionAttribute15| X| X| X|  |
| extensionAttribute2| X| X| X|  |
| extensionAttribute3| X| X| X|  |
| extensionAttribute4| X| X| X|  |
| extensionAttribute5| X| X| X|  |
| extensionAttribute6| X| X| X|  |
| extensionAttribute7| X| X| X|  |
| extensionAttribute8| X| X| X|  |
| extensionAttribute9| X| X| X|  |
| facsimiletelephonenumber| X| X|  |  |
| givenName| X| X|  |  |
| Telefon (privat)| X| X|  |  |
| info| X| X| X| Denne attribut er i øjeblikket ikke brugt for grupper.|
| Initialer| X| X|  |  |
| l| X| X|  |  |
| legacyExchangeDN| X| X| X|  |
| mailNickname| X| X| X|  |
| mangedBy|  |  | X|  |
| Manager| X| X|  |  |
| medlem|  |  | X|  |
| Mobile| X| X|  |  |
| msDS HABSeniorityIndex| X| X| X|  |
| msDS PhoneticDisplayName| X| X| X|  |
| msExchArchiveGUID| X|  |  |  |
| msExchArchiveName| X|  |  |  |
| msExchAssistantName| X| X|  |  |
| msExchAuditAdmin| X|  |  |  |
| msExchAuditDelegate| X|  |  |  |
| msExchAuditDelegateAdmin| X|  |  |  |
| msExchAuditOwner| X|  |  |  |
| msExchBlockedSendersHash| X| X|  |  |
| msExchBypassAudit| X|  |  |  |
| msExchCoManagedByLink|  |  | X|  |
| msExchDelegateListLink| X|  |  |  |
| msExchELCExpirySuspensionEnd| X|  |  |  |
| msExchELCExpirySuspensionStart| X|  |  |  |
| msExchELCMailboxFlags| X|  |  |  |
| msExchEnableModeration| X|  | X|  |
| msExchExtensionCustomAttribute1| X| X| X| Denne attribut er i øjeblikket ikke brugt af Exchange Online. |
| msExchExtensionCustomAttribute2| X| X| X| Denne attribut er i øjeblikket ikke brugt af Exchange Online. |
| msExchExtensionCustomAttribute3| X| X| X| Denne attribut er i øjeblikket ikke brugt af Exchange Online. |
| msExchExtensionCustomAttribute4| X| X| X| Denne attribut er i øjeblikket ikke brugt af Exchange Online. |
| msExchExtensionCustomAttribute5| X| X| X| Denne attribut er i øjeblikket ikke brugt af Exchange Online. |
| msExchHideFromAddressLists| X| X| X|  |
| msExchImmutableID| X|  |  |  |
| msExchLitigationHoldDate| X| X| X|  |
| msExchLitigationHoldOwner| X| X| X|  |
| msExchMailboxAuditEnable| X|  |  |  |
| msExchMailboxAuditLogAgeLimit| X|  |  |  |
| msExchMailboxGuid| X|  |  |  |
| msExchModeratedByLink| X| X| X|  |
| msExchModerationFlags| X| X| X|  |
| msExchRecipientDisplayType| X| X| X|  |
| msExchRecipientTypeDetails| X| X| X|  |
| msExchRemoteRecipientType| X|  |  |  |
| msExchRequireAuthToSendTo| X| X| X|  |
| msExchResourceCapacity| X|  |  |  |
| msExchResourceDisplay| X|  |  |  |
| msExchResourceMetaData| X|  |  |  |
| msExchResourceSearchProperties| X|  |  |  |
| msExchRetentionComment| X| X| X|  |
| msExchRetentionURL| X| X| X|  |
| msExchSafeRecipientsHash| X| X|  |  |
| msExchSafeSendersHash| X| X|  |  |
| msExchSenderHintTranslations| X| X| X|  |
| msExchTeamMailboxExpiration| X|  |  |  |
| msExchTeamMailboxOwners| X|  |  |  |
| msExchTeamMailboxSharePointUrl| X|  |  |  |
| msExchUserHoldPolicies| X|  |  |  |
| msOrg IsOrganizational|  |  | X|  |
| objectSID| X|  | X| mekanisk egenskab. AD-bruger-id, der bruges til at vedligeholde synkronisering mellem Azure AD og AD.|
| oOFReplyToOriginator|  |  | X|  |
| otherFacsimileTelephone| X| X|  |  |
| otherHomePhone| X| X|  |  |
| otherTelephone| X| X|  |  |
| personsøger| X| X|  |  |
| physicalDeliveryOfficeName| X| X|  |  |
| Postnummer| X| X|  |  |
| proxyAddresses| X| X| X|  |
| publicDelegates| X| X| X|  |
| pwdLastSet| X|  |  | mekanisk egenskab. Bruges til at vide, hvornår ødelægge allerede udstedt tokens. Bruges både synkronisering af adgangskoder og sammenslutning.|
| reportToOriginator|  |  | X|  |
| reportToOwner|  |  | X|  |
| securityEnabled|  |  | X| Afledt groupType|
| SN| X| X|  |  |
| sourceAnchor| X| X| X| mekanisk egenskab. Fast id til at bevare forholdet mellem tilføjer og Azure AD.|
| St| X| X|  |  |
| streetAddress| X| X|  |  |
| targetAddress| X| X|  |  |
| telephoneAssistant| X| X|  |  |
| telephoneNumber| X| X|  |  |
| thumbnailphoto| X| X|  |  |
| titel| X| X|  |  |
| unauthOrig| X| X| X|  |
| usageLocation| X|  |  | mekanisk egenskab. Brugerens land. Bruges til licensaftalen.|
| userCertificate| X| X|  |  |
| userPrincipalName| X|  |  | UPN er logon-ID til brugeren. Oftest bruger den samme som [mail] værdi.|
| userSMIMECertificates| X| X|  |  |
| wWWHomePage| X| X|  |  |

## <a name="sharepoint-online"></a>SharePoint Online

| Attributnavn| Bruger| Kontakt| Gruppe| Kommentar |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Angiver, om en konto er aktiveret.|
| authOrig| X| X| X|  |
| c| X| X|  |  |
| CN| X|  | X|  |
| CO| X| X|  |  |
| virksomhed| X| X|  |  |
| countryCode| X| X|  |  |
| afdeling| X| X|  |  |
| Beskrivelse| X| X| X|  |
| vist navn| X| X| X|  |
| dLMemRejectPerms| X| X| X|  |
| dLMemSubmitPerms| X| X| X|  |
| extensionAttribute1| X| X| X|  |
| extensionAttribute10| X| X| X|  |
| extensionAttribute11| X| X| X|  |
| extensionAttribute12| X| X| X|  |
| extensionAttribute13| X| X| X|  |
| extensionAttribute14| X| X| X|  |
| extensionAttribute15| X| X| X|  |
| extensionAttribute2| X| X| X|  |
| extensionAttribute3| X| X| X|  |
| extensionAttribute4| X| X| X|  |
| extensionAttribute5| X| X| X|  |
| extensionAttribute6| X| X| X|  |
| extensionAttribute7| X| X| X|  |
| extensionAttribute8| X| X| X|  |
| extensionAttribute9| X| X| X|  |
| facsimiletelephonenumber| X| X|  |  |
| givenName| X| X|  |  |
| hideDLMembership|  |  | X|  |
| Telefon (privat)| X| X|  |  |
| info| X| X| X|  |
| initialer| X| X|  |  |
| ipPhone| X| X|  |  |
| l| X| X|  |  |
| mail| X| X| X|  |
| mailnickname| X| X| X|  |
| managedBy|  |  | X|  |
| Manager| X| X|  |  |
| medlem|  |  | X|  |
| middleName| X| X|  |  |
| Mobile| X| X|  |  |
| msExchTeamMailboxExpiration| X|  |  |  |
| msExchTeamMailboxOwners| X|  |  |  |
| msExchTeamMailboxSharePointLinkedBy| X|  |  |  |
| msExchTeamMailboxSharePointUrl| X|  |  |  |
| objectSID| X|  | X| mekanisk egenskab. AD-bruger-id, der bruges til at vedligeholde synkronisering mellem Azure AD og AD.|
| oOFReplyToOriginator|  |  | X|  |
| otherFacsimileTelephone| X| X|  |  |
| otherHomePhone| X| X|  |  |
| otherIpPhone| X| X|  |  |
| otherMobile| X| X|  |  |
| otherPager| X| X|  |  |
| otherTelephone| X| X|  |  |
| personsøger| X| X|  |  |
| physicalDeliveryOfficeName| X| X|  |  |
| Postnummer| X| X|  |  |
| postOfficeBox| X| X|  |  |
| preferredLanguage| X|  |  |  |
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | mekanisk egenskab. Bruges til at vide, hvornår ødelægge allerede udstedt tokens. Bruges både synkronisering af adgangskoder og sammenslutning.|
| reportToOriginator|  |  | X|  |
| reportToOwner|  |  | X|  |
| securityEnabled|  |  | X| Afledt groupType|
| SN| X| X|  |  |
| sourceAnchor| X| X| X| mekanisk egenskab. Fast id til at bevare forholdet mellem tilføjer og Azure AD.|
| St| X| X|  |  |
| streetAddress| X| X|  |  |
| targetAddress| X| X|  |  |
| telephoneAssistant| X| X|  |  |
| telephoneNumber| X| X|  |  |
| thumbnailphoto| X| X|  |  |
| titel| X| X|  |  |
| unauthOrig| X| X| X|  |
| URL-adresse| X| X|  |  |
| usageLocation| X|  |  | mekanisk egenskab. Brugerens land. Bruges til licensaftalen.|
| userPrincipalName| X|  |  | UPN er logon-ID til brugeren. Oftest bruger den samme som [mail] værdi.|
| wWWHomePage| X| X|  |  |

## <a name="lync-online"></a>Lync Online

| Attributnavn| Bruger| Kontakt| Gruppe| Kommentar |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Angiver, om en konto er aktiveret.|
| c| X| X|  |  |
| CN| X|  | X|  |
| CO| X| X|  |  |
| virksomhed| X| X|  |  |
| afdeling| X| X|  |  |
| Beskrivelse| X| X| X|  |
| vist navn| X| X| X|  |
| facsimiletelephonenumber| X| X| X|  |
| givenName| X| X|  |  |
| Telefon (privat)| X| X|  |  |
| ipPhone| X| X|  |  |
| l| X| X|  |  |
| mail| X| X| X|  |
| mailNickname| X| X| X|  |
| managedBy|  |  | X|  |
| Manager| X| X|  |  |
| medlem|  |  | X|  |
| Mobile| X| X|  |  |
| msExchHideFromAddressLists| X| X| X|  |
| msRTCSIP-ApplicationOptions| X|  |  |  |
| msRTCSIP-DeploymentLocator| X| X|  |  |
| msRTCSIP-linje| X| X|  |  |
| msRTCSIP-OptionFlags| X| X|  |  |
| msRTCSIP-OwnerUrn| X|  |  |  |
| msRTCSIP-PrimaryUserAddress| X| X|  |  |
| msRTCSIP-UserEnabled| X| X|  |  |
| objectSID| X|  | X| mekanisk egenskab. AD-bruger-id, der bruges til at vedligeholde synkronisering mellem Azure AD og AD.|
| otherTelephone| X| X|  |  |
| physicalDeliveryOfficeName| X| X|  |  |
| Postnummer| X| X|  |  |
| preferredLanguage| X|  |  |  |
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | mekanisk egenskab. Bruges til at vide, hvornår ødelægge allerede udstedt tokens. Bruges både synkronisering af adgangskoder og sammenslutning.|
| securityEnabled|  |  | X| Afledt groupType|
| SN| X| X|  |  |
| sourceAnchor| X| X| X| mekanisk egenskab. Fast id til at bevare forholdet mellem tilføjer og Azure AD.|
| St| X| X|  |  |
| streetAddress| X| X|  |  |
| telephoneNumber| X| X|  |  |
| thumbnailphoto| X| X|  |  |
| titel| X| X|  |  |
| usageLocation| X|  |  | mekanisk egenskab. Brugerens land. Bruges til licensaftalen.|
| userPrincipalName| X|  |  | UPN er logon-ID til brugeren. Oftest bruger den samme som [mail] værdi.|
| wWWHomePage| X| X|  |  |

## <a name="azure-rms"></a>Azure RMS

| Attributnavn| Bruger| Kontakt| Gruppe| Kommentar |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Angiver, om en konto er aktiveret.|
| CN| X|  | X| Almindelige navn eller alias. Oftest præfikset for [mail] værdi.|
| vist navn| X| X| X| En streng, der repræsenterer det navn, der ofte vises som det fulde navn (Fornavn Efternavn).|
| mail| X| X| X| fulde mailadresse.|
| medlem|  |  | X|  |
| objectSID| X|  | X| mekanisk egenskab. AD-bruger-id, der bruges til at vedligeholde synkronisering mellem Azure AD og AD.|
| proxyAddresses| X| X| X| mekanisk egenskab. Bruges af Azure AD. Indeholder alle sekundære mailadresser for brugeren.|
| pwdLastSet| X|  |  | mekanisk egenskab. Bruges til at vide, hvornår ødelægge allerede udstedt tokens.|
| securityEnabled|  |  | X| Udledt fra groupType.|
| sourceAnchor| X| X| X| mekanisk egenskab. Fast id til at bevare forholdet mellem tilføjer og Azure AD.|
| usageLocation| X|  |  | mekanisk egenskab. Brugerens land. Bruges til licensaftalen.|
| userPrincipalName| X|  |  | Denne UPN er logon-ID til brugeren. Oftest bruger den samme som [mail] værdi.|

## <a name="intune"></a>Intune

| Attributnavn| Bruger| Kontakt| Gruppe| Kommentar |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Angiver, om en konto er aktiveret.|
| c| X| X|  |  |
| CN| X|  | X|  |
| Beskrivelse| X| X| X|  |
| vist navn| X| X| X|  |
| mail| X| X| X|  |
| mailnickname| X| X| X|  |
| medlem|  |  | X|  |
| objectSID| X|  | X| mekanisk egenskab. AD-bruger-id, der bruges til at vedligeholde synkronisering mellem Azure AD og AD.|
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | mekanisk egenskab. Bruges til at vide, hvornår ødelægge allerede udstedt tokens. Bruges både synkronisering af adgangskoder og sammenslutning.|
| securityEnabled|  |  | X| Afledt groupType|
| sourceAnchor| X| X| X| mekanisk egenskab. Fast id til at bevare forholdet mellem tilføjer og Azure AD.|
| usageLocation| X|  |  | mekanisk egenskab. Brugerens land. Bruges til licensaftalen.|
| userPrincipalName| X|  |  | UPN er logon-ID til brugeren. Oftest bruger den samme som [mail] værdi.|

## <a name="dynamics-crm"></a>Dynamics CRM

| Attributnavn| Bruger| Kontakt| Gruppe| Kommentar |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Angiver, om en konto er aktiveret.|
| c| X| X|  |  |
| CN| X|  | X|  |
| CO| X| X|  |  |
| virksomhed| X| X|  |  |
| countryCode| X| X|  |  |
| Beskrivelse| X| X| X|  |
| vist navn| X| X| X|  |
| facsimiletelephonenumber| X| X|  |  |
| givenName| X| X|  |  |
| l| X| X|  |  |
| managedBy|  |  | X|  |
| Manager| X| X|  |  |
| medlem|  |  | X|  |
| Mobile| X| X|  |  |
| objectSID| X|  | X| mekanisk egenskab. AD-bruger-id, der bruges til at vedligeholde synkronisering mellem Azure AD og AD.|
| physicalDeliveryOfficeName| X| X|  |  |
| Postnummer| X| X|  |  |
| preferredLanguage| X|  |  |  |
| pwdLastSet| X|  |  | mekanisk egenskab. Bruges til at vide, hvornår ødelægge allerede udstedt tokens. Bruges både synkronisering af adgangskoder og sammenslutning.|
| securityEnabled|  |  | X| Afledt groupType|
| SN| X| X|  |  |
| sourceAnchor| X| X| X| mekanisk egenskab. Fast id til at bevare forholdet mellem tilføjer og Azure AD.|
| St| X| X|  |  |
| streetAddress| X| X|  |  |
| telephoneNumber| X| X|  |  |
| titel| X| X|  |  |
| usageLocation| X|  |  | mekanisk egenskab. Brugerens land. Bruges til licensaftalen.|
| userPrincipalName| X|  |  | UPN er logon-ID til brugeren. Oftest bruger den samme som [mail] værdi.|

## <a name="3rd-party-applications"></a>3 part-programmer
Denne gruppe er et sæt af egenskaber, der bruges som de minimale attributter, der kræves for en generisk arbejdsbelastningen eller et program. Det kan bruges til en arbejdsbyrde, der ikke er angivet i en anden sektion eller til en ikke-Microsoft-app. Det bruges eksplicit til følgende:

- Yammer (kun brugeren er optaget)
- [Hybrid Business to Business (B2B) tværs org samarbejdsscenarier, som ressourcer som SharePoint](http://go.microsoft.com/fwlink/?LinkId=747036)

Denne gruppe er et sæt af egenskaber, der kan bruges, hvis Azure AD-mappen ikke bruges til at understøtte Office 365, Dynamics eller Intune. Den har et lille sæt core attributter.

| Attributnavn| Bruger| Kontakt| Gruppe| Kommentar |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Angiver, om en konto er aktiveret.|
| CN| X|  | X|  |
| vist navn| X| X| X|  |
| givenName| X| X|  |  |
| mail| X|  | X|  |
| managedBy|  |  | X|  |
| mailNickName| X| X| X|  |
| medlem|  |  | X|  |
| objectSID| X|  |  | mekanisk egenskab. AD-bruger-id, der bruges til at vedligeholde synkronisering mellem Azure AD og AD.|
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | mekanisk egenskab. Bruges til at vide, hvornår ødelægge allerede udstedt tokens. Bruges både synkronisering af adgangskoder og sammenslutning.|
| SN| X| X|  |  |
| sourceAnchor| X| X| X| mekanisk egenskab. Fast id til at bevare forholdet mellem tilføjer og Azure AD.|
| usageLocation| X|  |  | mekanisk egenskab. Brugerens land. Bruges til licensaftalen.|
| userPrincipalName| X|  |  | UPN er logon-ID til brugeren. Oftest bruger den samme som [mail] værdi.|

## <a name="windows-10"></a>Windows 10
Et medlem af et domæne computer(device) i Windows 10 synkroniserer nogle attributter til Azure AD. Se [forbinde medlem af et domæne enheder til Azure AD til Windows 10 har](active-directory-azureadjoin-devices-group-policy.md)kan finde flere oplysninger om scenarierne. Følgende attributter Synkroniser altid, og Windows 10 vises ikke som en app, kan du fjerne markeringen af. En Windows 10 medlem af et domæne computer identificeres ved at få den attribut userCertificate udfyldt.

| Attributnavn| Enhed| Kommentar |
| --- | :-: | --- |
| accountEnabled| X| |
| deviceTrustType| X| Hårdt værdi for computere, der er medlem af et domæne. |
| vist navn | X| |
| MS-DS-CreatorSID | X| Kaldes også registeredOwnerReference.|
| objectGUID | X| Kaldes også deviceID.|
| objectSID | X| Kaldes også onPremisesSecurityIdentifier.|
| Operativsystemet | X| Kaldes også deviceOSType.|
| operatingSystemVersion | X| Kaldes også deviceOSVersion.|
| userCertificate | X| |

Følgende attributter for **bruger** er ud over de andre apps, du har valgt.  

| Attributnavn| Bruger| Kommentar |
| --- | :-: | --- |
| domainFQDN| X| Kaldes også DNSDomænenavn. For eksempel contoso.com.|
| domainNetBios| X| Kaldes også netBiosName. For eksempel CONTOSO.|

## <a name="exchange-hybrid-writeback"></a>Exchange hybrid tilbageførsel
Følgende attributter skrives tilbage fra Azure AD til lokale Active Directory når du vælger for at aktivere **Exchange hybrid**. Afhængigt af din Exchange-version, kan være synkroniseret færre attributter.

| Attributnavn| Bruger| Kontakt| Gruppe| Kommentar |
| --- | :-: | :-: | :-: | --- |
| msDS ExternalDirectoryObjectID| X|  |  | Udledt fra cloudAnchor i Azure AD. Denne attribut er nye i Exchange 2016.|
| msExchArchiveStatus| X|  |  | Onlinearkivet: Gør det muligt for kunder til at arkivere mail.|
| msExchBlockedSendersHash| X|  |  | Filtrering: Skriver tilbage filtrering lokalt og online sikre og blokerede afsendere data fra klienter.|
| msExchSafeRecipientsHash| X|  |  | Filtrering: Skriver tilbage filtrering lokalt og online sikre og blokerede afsendere data fra klienter.|
| msExchSafeSendersHash| X|  |  | Filtrering: Skriver tilbage filtrering lokalt og online sikre og blokerede afsendere data fra klienter.|
| msExchUCVoiceMailSettings| X|  |  | Aktivere Unified Messaging (UM) - Online talebesked: bruges af Microsoft Lync Server integration at vise Lync Server lokalt, at brugeren har talebeskeder i onlinetjenester.|
| msExchUserHoldPolicies| X|  |  | Hold til retslig: Aktiverer skytjenester for at finde ud af, hvilke brugere er under retslig Hold.|
| proxyAddresses| X| X| X| Kun den x500 adresse fra Exchange Online er indsat.|

## <a name="device-writeback"></a>Enhed tilbageførsel
Enhedsobjekter er oprettet i Active Directory. Disse objekter kan være enheder tilsluttet Azure AD eller medlem af et domæne Windows 10 computere.

| Attributnavn| Enhed| Kommentar |
| --- | :-: | --- |
| altSecurityIdentities | X| |
| vist navn | X| |
| DN | X| |
| msDS CloudAnchor | X| |
| msDS DeviceID | X| |
| msDS DeviceObjectVersion | X| |
| msDS DeviceOSType | X| |
| msDS DeviceOSVersion | X| |
| msDS DevicePhysicalIDs | X| |
| msDS KeyCredentialLink | X| Kun med Windows Server 2016 AD skema |
| msDS IsCompliant | X| |
| msDS IsEnabled | X| |
| msDS IsManaged | X| |
| msDS RegisteredOwner | X| |


## <a name="notes"></a>Noter

- Når du bruger et andet ID, er i det lokale miljø attribut userPrincipalName synkroniseret med Azure AD-attribut onPremisesUserPrincipalName. Attributten alternative ID for eksempel mail er synkroniseret med Azure AD-attribut userPrincipalName.
- På listerne over gælder objekttype **bruger** også for objektet type **iNetOrgPerson**.

## <a name="next-steps"></a>Næste trin
Lær mere om at [synkronisere Azure AD-forbindelse](active-directory-aadconnectsync-whatis.md) konfigurationen.

Lær mere om at [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).
