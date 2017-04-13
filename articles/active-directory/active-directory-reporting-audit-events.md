<properties
   pageTitle="Azure Active Directory overvåge rapport hændelser | Microsoft Azure"
   description="Overvåget hændelser, der er tilgængelige til visning og hente fra din Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/19/2016"
   ms.author="dhanyahk"/>

# <a name="azure-active-directory-audit-report-events"></a>Azure Active Directory overvåge rapport hændelser

*Denne dokumentation er en del af den [Azure Active Directory rapportering Guide] (aktiv-directory-rapportering-guide.md).*

Azure Active Directory overvågningslog rapporten hjælper med at identificere privilegerede handlinger, der er opstået i deres Azure Active Directory kunder. Privilegerede handlinger omfatter udvidelse ændringer (for eksempel rolleoprettelse eller nulstilling af adgangskoder), ændre politik konfigurationer (for eksempel adgangskodepolitikker) eller ændringer til directory konfigurationen (for eksempel skifter til sammenslutning Domæneindstillinger). Rapporterne, der giver posten overvågningslog for navnet på begivenheden, agent, der har udført handlingen, target ressourcen påvirkes af ændringen, og den dato og det klokkeslæt (i UTC). Kunder er kunne hente listen over overvågningshændelser for deres Azure Active Directory via [Azure-portalen](https://portal.azure.com/), som beskrevet i [visningen overvåge logfilerne](active-directory-reporting-azure-portal.md).


## <a name="list-of-audit-report-events"></a>Liste over overvåge rapport hændelser
<!--- audit event descriptions should be in the past tense --->

Begivenheder                               | Beskrivelse af begivenhed
------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------
**Brugerhændelser, der**                      |
Tilføj bruger                             | Føjet en bruger til biblioteket.
Slette bruger                          | En bruger har slettet fra mappen.
Angive egenskaber for licens               | Angiv egenskaberne licens for en bruger i kataloget.
Nulstil din brugeradgangskode                  | Nulstille adgangskoden for en bruger i kataloget.
Skift brugerens adgangskode                 | Ændret adgangskoden for en bruger i kataloget.
Ændre brugerlicens                  | Ændres den licens, der er tildelt til en bruger i kataloget. Hvis du vil se, hvilke licenser er blevet opdateret, se på egenskaberne [Opdater bruger](#update-user-attributes)
Opdater bruger                          | Opdateret en bruger i kataloget. [Se nedenfor](#update-user-attributes) for attributter, der kan opdateres.
Angiv kraft Skift brugerens adgangskode       | Angiv den egenskab, der fremtvinger en bruger til at ændre deres adgangskode på login.
Opdatere brugerlegitimationsoplysninger        | Brugeren har ændret adgangskoden  
**Gruppebegivenheder**                     |
Tilføje gruppe                            | Oprettet en gruppe i kataloget.
Opdatere gruppe                         | Opdateret en gruppe i kataloget. For at se, hvilke egenskaber er blevet opdateret skal referere til [Gruppen egenskaber overvåget](#update-group-attributes) i afsnittet nedenfor
Slet gruppe                         | En gruppe har slettet fra mappen.
Føje medlem til gruppe            | Føjes et medlem til en gruppe i kataloget.
Fjerne medlem fra gruppe         | Fjernes et medlem fra en gruppe i kataloget.
CreateGroupSettings              | Oprettet gruppeindstillinger
UpdateGroupSettings          | Opdateret gruppeindstillinger. For at se, hvilke gruppeindstillinger er blevet opdateret skal referere til [Gruppen egenskaber overvåget](#update-group-attributes) i afsnittet nedenfor
DeleteGroupSettings            | Slettede gruppeindstillinger
SetGroupLicense                  | Angiv gruppe licens.
SetGroupManagedBy              | Angiv gruppe, der skal administreres af bruger
AddGroupMember               | Tilføjelse af medlem til gruppe
RemoveGroupMember            | Fjerne medlem fra gruppe
AddGroupOwner                | Tilføjede ejer til gruppe
RemoveGroupOwner                 | Fjernede ejer fra gruppe
**Programhændelser**               |
Tilføje service hovedstolen                | Tilføjet en tjeneste sikkerhedskonto til mappen.
Fjerne tjenesten hovedstolen             | Fjernes en tjeneste sikkerhedskonto fra adresselisten.
Tilføj tjenesten vigtigste legitimationsoplysninger    | Tilføjede legitimationsoplysninger til en tjeneste sikkerhedskonto.
Fjerne tjenesten vigtigste legitimationsoplysninger | Fjernede legitimationsoplysninger fra en tjeneste vigtigste.
Føje delegering                 | Oprettet en [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) i kataloget.
Angiv delegering post                 | Opdateret en [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) i kataloget.
Fjern delegering post              | Slettes en [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) i kataloget.
**Rolle begivenheder**                      |
Føje rollemedlem til rolle              | Føjet en bruger til en mappe rolle.
Fjerne rollemedlem fra rolle         | Fjernet en bruger fra en directory rolle.
Konfigurere virksomhedens kontaktoplysninger      | Angive virksomhed niveau kontaktindstillinger. Dette omfatter mailadresser for marketing samt tekniske beskeder om Microsoft Online Services.
Føje delegering                 | Oprettet en [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) i kataloget.
Angiv delegering post                 | Opdateret en [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) i kataloget.
Fjern delegering post              | Slettes en [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) i kataloget.
AddSevicePrincipalOwner          | Tilføjede ejeren til tjenesten hovedstolen.
RemoveSevicePrincipalOwner   | Fjernet ejer fra tjenesten hovedstolen.
AddApplication  | Tilføje program.
UpdateApplication | Opdater programmet. For at se, hvilke indstillinger er blevet opdateret skal referere til [Programmet egenskaber overvåget](#update-application-attributes) i afsnittet nedenfor
DeleteApplication | Slette programmet.
RestoreApplication|Gendanne programmet.
AddApplicationOwner   |     Føj ejer-program.
RemoveApplicationOwner    |     Fjerne ejer fra programmet.
**Rolle begivenheder**                         |
Føje rollemedlem til rolle                 | Føjet en bruger til en mappe rolle.
Fjerne rollemedlem fra rolle            | Fjernet en bruger fra en directory rolle.
AddRoleDefinition               | Tilføjede rolledefinition.
UpdateRoleDefinition                | Opdateret rolledefinition. For at se, hvilke Rolleindstillinger for er blevet opdateret skal referere til [Rolle Definition egenskaber overvåget](#update-role-definition-attributes) i afsnittet nedenfor
DeleteRoleDefinition                | Slettet rolledefinition.
AddRoleAssignmentToRoleDefinition | Tilføjede rolletildeling til rolledefinition.
RemoveRoleAssignmentFromRoleDefinition | Fjernede rolletildeling fra rolledefinition.
AddRoleFromTemplate     | Tilføjede rolle fra skabelon.
UpdateRole  | Opdaterede rolle.
AddRoleScopeMemberToRole    | Tilføjelse af relateret medlem til rolle.
RemoveRoleScopedMemberFromRole  | Fjernet relateret medlem fra rolle.
**Enhed begivenheder (alle nye begivenheder)**                    |
AddDevice               | Tilføjede enhed.
UpdateDevice            | Opdaterede enhed. For at se, hvilke egenskaber for enheden er blevet opdateret skal referere til [Egenskaber for enheden Audited](#update-device-attributes) i afsnittet nedenfor
DeleteDevice            | Slettede enhed.
AddDeviceConfiguration      | Enhedskonfiguration af tilføjede.
UpdateDeviceConfiguration   | Enhedskonfiguration af opdaterede. For at se, hvilke egenskaber for enheden konfiguration er blevet opdateret skal referere til [konfiguration af egenskaber for enheden Audited](#update-device-configuration-attributes) i afsnittet nedenfor
DeleteDeviceConfiguration   | Enhedskonfiguration af slettede.
AddRegisteredOwner     | Tilføjede registreret ejer til enhed.
AddRegisteredUsers     | Føjet registrerede brugere til enhed.
RemoveRegisteredOwner    | Fjerne registrerede ejer fra enhed.
RemoveRegisteredUsers    | Fjerne registrerede brugere fra enheden.
RemoveDeviceCredentials  | Fjerne enhed legitimationsoplysninger.
**B2B begivenheder**                       |
Batchen invitationer overført.              | En administrator har overført en fil, der indeholder invitationer til at blive sendt til partner brugere.
Batchen invitationer behandles.             | En fil, der indeholder invitationer til partner brugere er blevet behandlet.
Invitere eksterne brugere.                | En ekstern bruger er blevet inviteret til mappen.
Indløse Inviter eksterne brugere.         | En ekstern bruger har indløst deres invitation til mappen.
Føje eksterne brugere til gruppen.          | En ekstern bruger er blevet tildelt medlemskab til en gruppe i kataloget.
Tildele eksterne brugere til programmet. | En ekstern bruger er blevet tildelt direkte adgang til et program.
Oprettelse af virus lejer.               | En ny lejer er blevet oprettet i Azure AD ved invitation indløsningskurs.
Oprettelse af virus bruger.                 | En bruger er oprettet i en eksisterende lejer i Azure AD ved invitation indløsningskurs.
**Administrative enheder (alle nye begivenheder)**             |
AddAdministrativeUnit   |   Tilføje administrative enhed.
UpdateAdministrativeUnit|   Opdater administrative enhed. For at se, hvilke Administrative enhed egenskaber er blevet opdateret skal referere til [Administrative enhed egenskaber overvåget](#update-administrative-unit-attributes) i afsnittet nedenfor
DeleteAdministrativeUnit|   Slette administrative enhed.
AddMemberToAdministrativeUnit|  Tilføj medlem til administrative enhed.
RemoveMemberFromAdministrativeUnit|     Fjerne medlem fra administrative enhed.
**Directory begivenheder**                 |
Føje partner til virksomhed               | Føjet en partner til biblioteket.
Fjerne partneren fra virksomhed          | Fjernes en partner fra adresselisten.
DemotePartner   |   Sænke partner.
Tilføje domænet til firma                | Føjet et domæne til biblioteket.
Fjerne domæne fra virksomhed           | Fjernet et domæne fra adresselisten.
Opdatere domæne                        | Opdateret et domæne på adresselisten. For at se, hvilke Domain properties er blevet opdateret skal referere til [Domain Properties overvåget](#update-domain-attributes) i afsnittet nedenfor
Angive domænegodkendelse            | Ændret domæne standardindstillingen for virksomheden.
Konfigurere virksomhedens kontaktoplysninger      | Angive virksomhed niveau kontaktindstillinger. Dette omfatter mailadresser for marketing samt tekniske beskeder om Microsoft Online Services.
Angive indstillinger for sammenslutning på domæne    | Opdateret sammenslutning indstillingerne for et domæne.
Bekræfte domæne                        | Bekræftet et domæne på adresselisten.
Bekræfte bekræftet maildomæne         | Bekræftet et domæne på den mappe, ved hjælp af mailbekræftelsen.
Indstille DirSyncEnabled flaget for virksomhed   | Angiv egenskaben, der gør det muligt for en mappe til Azure Active Directory-synkronisering.
Angive adgangskodepolitik                  | Angiv længde og tegn begrænsninger til brugernes adgangskoder.
Angive firmaoplysninger              | Opdateret felterne Virksomhed-niveau. Se [Get-MsolCompanyInformation](https://msdn.microsoft.com/library/azure/dn194126.aspx) PowerShell-cmdlet kan finde flere oplysninger.
SetCompanyAllowedDataLocation  |    Konfigurere virksomhedens tilladt dataplacering.
SetCompanyDirSyncEnabled    |   Angive DirSyncEnabled flag.
SetCompanyDirSyncFeature |  Indstille DirSync funktion.
SetCompanyInformation |   Angive firmaoplysninger.
SetCompanyMultiNationalEnabled    |     Angive virksomhed multinationale funktion er aktiveret.
SetDirectoryFeatureOnTenant   |     Indstille directory funktion på lejer.
SetTenantLicenseProperties  |   Angive lejer licensegenskaber.
CreateCompanySettings   |   Oprette firmaindstillinger
UpdateCompanySettings   |   Opdatere indstillinger for virksomhed. For at se, hvilke virksomhed egenskaber er blevet opdateret skal referere til [virksomhedens egenskaber overvåget](#update-company-attributes) i afsnittet nedenfor
DeleteCompanySettings   |   Slette firmaindstillinger
SetAccidentalDeletionThreshold   |  Angive grænseværdi for utilsigtet sletning.
SetRightsManagementProperties   |   Angive rights management egenskaber.
PurgeRightsManagementProperties     |   Rydde rights management egenskaber.
UpdateExternalSecrets   |   Opdatere eksterne hemmeligheder
**Politik begivenheder (alle nye begivenheder)**           |
AddPolicy   |   Føj politik.
UpdatePolicy    |   Opdater politik.
DeletePolicy    |   Slette politik.
AddDefaultPolicyApplication     |   Føj politik-program.
AddDefaultPolicyServicePrincipal    |   Føj politik til tjenesten hovedstolen.
RemoveDefaultPolicyApplication  |   Fjern politik fra programmet.
RemoveDefaultPolicyServicePrincipal     |   Fjern politik fra tjenesten hovedstolen.
RemovePolicyCredentials     |   Fjerne politik legitimationsoplysninger.

## <a name="audit-report-retention"></a>Overvåge rapport opbevaring
Hændelser i rapporten Azure AD overvågningslog bevares i 180 dage. Du kan finde flere oplysninger om opbevaring på rapporter, [Azure Active Directory rapport opbevaringspolitikker](active-directory-reporting-retention.md).

For kunder, der er interesseret i at gemme deres overvågningshændelser i opbevaring længere, kan API rapportering bruges til at adskille regelmæssigt overvågningshændelser i et separat datalager. Se [Introduktion til rapportering API](active-directory-reporting-api-getting-started.md) til detaljer.

## <a name="properties-included-with-each-audit-event"></a>Egenskaber, der følger med hver enkelt hændelse overvågningslog

Egenskaben      | Beskrivelse
------------- | --------------------------------------------------------------
Dato og klokkeslæt | Dato og klokkeslæt, der er indtruffet hændelsen overvågningslog
Agent         | Den bruger eller tjeneste hovedstolen, udføres handlingen
Handling        | Den handling, der blev udført
Mål        | Den bruger eller tjeneste hovedstolen, der blev udført handlingen på


## <a name="update-user-attributes"></a>"Opdater bruger" attributter
Hændelsen "Opdater bruger" overvågningslog indeholder flere oplysninger om, hvilke brugerattributter er blevet opdateret. For hver attribut er både den tidligere værdi og den nye værdi inkluderet.

Attribut                           | Beskrivelse
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled                      | Brugeren kan logge på.
AssignedLicense                     | Alle licenser, der er tildelt til brugeren.
AssignedPlan                        | Service planer, der er resultatet af de licenser, der er tildelt til brugeren.
LicenseAssignmentDetail             | Få at vide om licenser, der er tildelt til brugeren. Eksempelvis hvis gruppe-baserede licensering var involveret medtages dette den gruppe, der har givet i licens.
Mobile                              | Brugerens mobiltelefon.
OtherMail                           | Brugerens alternative mailadresse.
OtherMobile                         | Brugerens alternative mobiltelefon.
StrongAuthenticationMethod          | En liste over bekræftelse metoder konfigureret af brugeren til multi-Factor Authentication som ringe til talebesked, SMS eller bekræftelse kode fra en mobilapp.
StrongAuthenticationRequirement     | Hvis Multi-Factor Authentication gennemtvinges, aktiveres eller deaktiveres for denne bruger.
StrongAuthenticationUserDetails     | Brugerens telefonnummer, alternative telefonnummer og mailadresse, der bruges til multi-Factor Authentication og din adgangskode Nulstil bekræftelse.
StrongAuthenticationPhoneAppDetail  | Detaljeret forof telefonappene registreret til at udføre 2FA logon
TelephoneNumber                     | Brugerens telefonnummer.
AlternativeSecurityId               | En alternativ sikkerheds-ID til objektet.
CreationType                        |Oprettelse af metode for brugeren, (enten ved at blive inviteret eller virus).
InviteTicket                        |Liste over invitation billetter til brugeren.
InviteReplyUrl                      |Liste over URL-adresser til at svare på accept af invitationen.
InviteResources                     |Liste over ressourcer, som brugeren er blevet inviteret.
LastDirSyncTime                     |Sidst blev opdateret på objektet på grund af synkronisering fra den autoritative (kunde, lokalt) directory.
MSExchRemoteRecipientType           |Kort til SSD modtager typer. Referere til [SSD modtager typer] https://msdn.microsoft.com/library/microsoft.office.interop.outlook.recipient.type.aspx for modtager typer
PreferredDataLocation               |Den foretrukne placering for brugerens, gruppens, kontaktens, offentlig mappe eller enheds data.
ProxyAddresses                      |Den adresse, som genkendes et Exchange Server modtager objekt i en fremmed postsystem.
StsRefreshTokensValidFrom           |Varetager opdatere oplysninger om tilbagekaldelse token – en hvilken som helst STS Opdater tokens, der er udstedt før denne gang betragtes som udløbet.
UserPrincipalName                   |UPN, der er en Internet-typografi logonnavn for en bruger.
UserState                           |Status for brugeren PendingApproval/PendingAcceptance/accepteret/PendingVerification.
UserStateChangedOn                  |Tidsstempel for seneste ændring af UserState. Bruges til at udløse livscyklus arbejdsprocesser.
UserType                            |Type af bruger. Medlem (0), gæst (1), Viral(2).

## <a name="update-group-attributes"></a>"Opdater gruppe" attributter
Attribut                           | Beskrivelse
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
Klassifikation            | Klassifikation for en samlet gruppe (HBI, MBI osv.).
Beskrivelse               | Læsbar beskrivende sætninger om objektet.  
Vist navn               |Det viste navn for et objekt
DirSyncEnabled            |Angiver, om synkroniseringen sker fra en autoritative (kunde, lokalt) directory.
GroupLicenseAssignment    |Licensaftalen af en gruppe
GroupType                 |Type gruppe, Unified (0)
IsMembershipRuleLocked    |Angiver, at egenskaben MembershipRule er angivet af selvbetjening gruppe management-tjenesten og ikke kan ændres af brugere. Gælder kun for grupper, hvor GroupType indeholder GroupType.DynamicMembership
IsPublic                  |Flag, der angiver, hvis gruppen er offentlige/private.
LastDirSyncTime           |Sidst blev opdateret objektet som et resultat af synkronisering fra den autoritative (kunde, lokal) directory.
Mail                      |Primære e-mail-adresse
MailEnabled               |Angiver, om denne gruppe har e-mail-funktion.
MailNickname              |Moniker for et address book objekt, typisk del af navnet mail foregående den "@" symbol.   
MembershipRule            |En streng, der udtrykker kriteriet bruges ved selvbetjening gruppe management-tjenesten til at bestemme, hvilke medlemmer skal tilhøre denne gruppe. Se også IsMembershipRuleLocked. Gælder kun for grupper, hvor GroupType indeholder GroupType.DynamicMembership.
MembershipRuleProcessingState| En optællingsværdi, der er defineret af selvbetjening gruppe management service definerer status for medlemskab behandling af denne gruppe. Gælder kun for grupper, hvor GroupType indeholder GroupType.DynamicMembership.
ProxyAddresses            |Den adresse, som genkendes et Exchange Server modtager objekt i en fremmed postsystem.
RenewedDateTime           |Tidsstemplet registrering af når en gruppe senest blev fornyet.   
SecurityEnabled           |Angiver, om medlemskab i gruppen kan påvirke godkendelse beslutninger.
WellKnownObject           |Etiketter en directory-objektet, angive den som et af de foruddefinerede.

## <a name="update-device-attributes"></a>"Opdatere enhed" attributter
Attribut                           | Beskrivelse
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled | Angiver, om en sikkerhedskonto kan godkende.
CloudAccountEnabled | Angiver, om en sikkerhedskonto kan godkende. Skrevet af InTune, når enheden er styr på lokal.
CloudDeviceOSType | Type af enheden, der er baseret på OS f.eks. Windows RT, iOS. Når angivet af en skybaseret tjeneste (såsom Intune), bliver denne attribut autoritative i kataloget for DeviceOSType.
CloudDeviceOSVersion | Version af Operativsystemet. Når angivet af en skybaseret tjeneste (såsom Intune), bliver denne attribut autoritative i kataloget for DeviceOSVersion.
CloudDisplayName | Værdien af LDAP-attributten vist navn. Når angivet af en skybaseret tjeneste (såsom Intune), bliver denne attribut autoritative i mappen for vist navn.
CloudCreated |Angiver, om objektet, der er oprettet af skytjenester.
CompliantUntil | Hvilke indtil enhed anses kompatibel.
DeviceMetadata |Brugerdefineret metadataene for enheden
DeviceObjectVersion | Denne attribut bruges til at identificere skemaversionen af enheden.
DeviceOSType |Type af enheden, der er baseret på OS f.eks. Windows RT, iOS. Skrevet af tjenesten registrering og beregnet til at blive opdateret med MDM lys management-tjenesten eller STS management-tjenesten.
DeviceOSVersion |Version af Operativsystemet. Skrevet af tjenesten registrering og beregnet til at blive opdateret med MDM lys management-tjenesten eller STS management-tjenesten.
DevicePhysicalIds |Attribut med flere værdier, der er beregnet til at gemme id'erne for den fysiske enhed. Dette kan omfatte BIOS-id'er, TPM thumbprints, hardware bestemte id'er osv.
DirSyncEnabled | Angiver, om synkroniseringen sker fra en autoritative (kunde, lokal) directory.
Vist navn |Det viste navn for et objekt
IsCompliant | Denne attribut bruges til at administrere mobilenhed management status på enheden.
IsManaged |Denne attribut bruges til at angive enheden, der administreres af en sky MDM.
LastDirSyncTime |Sidst blev opdateret på objektet på grund af synkronisering fra den autoritative (kunde, lokal) directory.

## <a name="update-device-configuration-attributes"></a>"Opdatere enhedskonfigurationen" attributter
Attribut                           | Beskrivelse
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
MaximumRegistrationInactivityPeriod |Det maksimale antal dage, en enhed kan være inaktiv, før det betragtes som til at blive fjernet.
RegistrationQuota   |Politik bruges til at begrænse antallet af enhed registreringer, der er tilladt for en enkelt bruger.

## <a name="update-service-principal-configuration-attributes"></a>"Opdatere vigtigste tjenestekonfiguration" attributter
Attribut                           | Beskrivelse
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled |Angiver, om en sikkerhedskonto kan godkende.
AppPrincipalId | Eksterne, programmet brugerdefineret identitet for en sikkerhedskonto.
Vist navn |Det viste navn for et objekt
ServicePrincipalName    | Tjenestens hovednavn, der indeholder "navn/nøglecenter", hvor navn angiver en programmet klasse værdi og nøglecenter indeholder mindst hostname [: port] eller "navn", der angiver et id for tjenesten hovedstolen.

## <a name="update-app-attributes"></a>"Opdatere App" attributter
Attribut                           | Beskrivelse
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AppAddress |Sæt af adresser (Omdiriger URL-adresser), der er tildelt til en tjeneste sikkerhedskonto.
Sikkerhedskontrol                               |Program-ID i Appen   
AppIdentifierUri | Program URI, som identificerer programmet.  Det er normalt program access URL-adressen.
AppLogoUrl |URL-adressen for det program logo billede, der er gemt i et CDN.
AvailableToOtherTenants | SAND programmet som flere lejer (det vil sige kan bruges af andre lejere).
Vist navn | Det viste navn for navnet på et program
Ret |Liste over programmet rettigheder.
ExternalUserAccountDelegationsAllowed |Flag, der angiver, om ressourcen program er en der er tillid til og kan oprette delegering posterne til eksterne brugerkonti.
GroupMembershipClaims |Medlemskab krav gruppepolitikken.
PublicClient | True, hvis klienten ikke beholde hemmeligt (det vil sige ikke-fortrolige klienten i OAuth2.0)
RecordConsentConditions | Typer af samtykke betingelser, som defineret af kontrakt betingelser: ingen-(0), SilentConsentForPartnerManagedApp(1). Denne værdi skal være synlig i Graph API skemaet og kan kun være sæt/ændres af lejeradministratorer.
RequiredResourceAccess |XML-indholdet af en værdi af egenskaben RequiredResourceAccess.
WebApp |Hvis sand, du angiver, at dette program er en WebApp.
WwwHomepage |Primære websiden.

## <a name="update-role-attributes"></a>"Opdater rollen" attributter
Attribut                           | Beskrivelse
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AppAddress |Sæt af adresser (Omdiriger URL-adresser), der er tildelt til en tjeneste sikkerhedskonto.
BelongsToFirstLoginObjectSet |Hvis sand, du angiver, at dette objekt hører til sæt af objekter, der kræves for at aktivere logon for den første administrator af en ny lejer.
Indbygget |Angiver, om levetiden for et objekt er ejes af systemet.
Beskrivelse | Læsbar beskrivende sætninger om objektet.  
Vist navn |Det viste navn for et objekt
MailNickname | Moniker for et address book objekt, typisk del af navnet mail foregående den "@" symbol.
RoleDisabled |Angiver, om rollen skal ignoreres henblik på adgangskontrol.
RoleTemplateId | Angivelse af rolleskabelonen.
ServiceInfo |Tjenestens klargøring oplysninger, der kan blive brugt i MOAC og/eller andre forekomster af tjenesten (i de samme eller en anden tjeneste former).
TaskSetScopeReference |Identificerer en opgaveAngive og en række områder, der er knyttet til en rolle eller RoleTemplate.
ValidationError |Oplysninger, der er udgivet af en samlet tjeneste, der beskriver en permanent, tjenestens fejl vedrørende egenskaber eller oprette en kæde fra en administrator Objekthandling til at løse.
WellKnownObject |Etiketter en directory-objektet, angive den som et af de foruddefinerede.

## <a name="update-role-definition-attributes"></a>"Opdatere rolledefinition" attributter
Attribut                           | Beskrivelse
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AssignableScopes    |Samling af godkendelse områder, der kan benyttes, når du tildeler denne RoleDefinition til en sikkerhedskonto.
Vist navn     |Det viste navn for et objekt
GrantedPermissions  |Tilladelser af denne RoleDefinition.


## <a name="update-administrative-unit-attributes"></a>"Opdatere Administrative enhed" attributter
Attribut                           | Beskrivelse
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
Beskrivelse |   Denne egenskab er opdateret, når du ændrer beskrivelsen af en administrativ enhed.
Vist navn |   Denne egenskab er opdateret, når du ændrer navnet på en administrativ enhed.

## <a name="update-company-attributes"></a>"Opdater virksomhed" attributter
Attribut                           | Beskrivelse
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AllowedDataLocation     | En placering, hvor virksomhedens brugere har tilladelse til at blive klargjort.
AuthorizedServiceInstance   | Navnene på forekomster af tjenesten, kan være installeret en plan.
DirSyncEnabled |Angiver, om synkroniseringen sker fra en autoritative (kunde, lokal) directory.
DirSyncStatus |Angiver, om synkronisering af address book objekter i denne lejer kontekst forekommer fra en autoritative (kunde, lokal) adresseliste. en udvidelse af egenskaben DirSyncEnabled på virksomhedens objekter.
DirSyncFeatures |Bit flag for at holde styr på funktionssæt aktiveret og deaktiveret dirsync for lejeren.
DirectoryFeatures |Aktiveret/deaktiveret directory funktioner.
DirSyncConfiguration |Indeholder alle DirSync konfiguration, der er specifikke for den aktuelle lejer.
Vist navn |Det viste navn for et objekt
IsMnc |Et boolesk flag indstillet til "true" iff firmaet er aktiveret for funktionen multinationale virksomhed.
ObjectSettings | En samling af indstillinger, der gælder for omfanget af objektet.
PartnerCommerceUrl |URL-adressen til partnerens websted med handel.
PartnerHelpUrl |URL-adressen til webstedet med partnerens Hjælp.
PartnerSupportEmail | URL-adressen til partnerens understøttelse af mail.
PartnerSupportTelephone |URL-adressen til partnerens support telefon.
PartnerSupportUrl |URL-adressen til partnerens supportwebsted.
StrongAuthenticationDetails |Oplysninger, der er relateret til StrongAuthentication.
StrongAuthenticationPolicy |Stærk godkendelsespolitik for firmaet.
TechnicalNotificationMail |E-mail-adresse til at informere tekniske problemer, der vedrører et firma.
TelephoneNumber |Telefonnumre, der er i overholder med ITU anbefaling E.123.
TenantType |Typen af en lejer. Hvis denne værdi ikke er angivet, er lejeren en virksomhed. Ellers mulige værdier er: MicrosoftSupport (0), SyndicatePartner (1), BreadthPartner (2) BreadthPartnerDelegatedAdmin (3) ResellerPartnerDelegatedAdmin (4) ValueAddedResellerPartnerDelegatedAdmin (5).
VerifiedDomain |Et sæt af DNS-domænenavne, der er bundet til et firma.

## <a name="update-domain-attributes"></a>"Opdatere domæne" attributter
Attribut                           | Beskrivelse
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
Funktioner    |Bit flag, der beskriver funktionerne i domænet, hvis en hvilken som helst.
Standard |Angiver, om domænet er standardværdi. for eksempel standard UserPrincipalName suffikset når en administrator opretter en ny bruger i MOAC.
Indledende |Angiver, om domænet er det indledende domæne for virksomheden, som tildelt af OCP. Det indledende domæne er et entydigt underdomæne til et Microsoft Online domæne; e.g.contoso3.microsoftonline.com.
LiveType    |Det tilsvarende Windows Live navneområde, hvis en hvilken som helst type.
Navn    | Id for slutpunktet.
PasswordNotificationWindowDays  |Antallet af dage før en adgangskode udløber brugeren får besked om.
PasswordValidityPeriodDays  | Antallet af dage, der er velegnet til en adgangskode, før den skal ændres.

Poster for overvågning er en påkrævet kontrolelementet til mange overholdelse af regler. Det anbefales, at kunden sender en kopi af dette Hjælp-emne med kopien af kundens eksporterede overvågningslog rapport for at hjælpe med at forklare rapportoplysninger for kunder ved hjælp af Azure Active Directory overvåge rapporten til at opfylde deres overholdelse af regler til. Hvis revisor vil have at forstå de regler, der aktuelt opfylder Azure, skal du føre revisor til [overholdelse af siden](https://azure.microsoft.com/support/trust-center/compliance/) i Microsoft Azure Trust Center.
