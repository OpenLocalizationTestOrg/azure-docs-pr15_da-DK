<properties
    pageTitle="Azure Active Directory-PowerShell preview-cmdletter til gruppeadministration af i Azure AD | Microsoft Azure"
    description="Denne side indeholder PowerShell eksempler for at hjælpe dig med at administrere dine grupper i Azure Active Directory"
    keywords="Azure AD, Azure Active Directory, PowerShell, administration af grupper, gruppe"
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="curtand"/>

# <a name="azure-active-directory-preview-cmdlets-for-group-management"></a>Azure Active Directory preview-cmdletter til gruppeadministration af

> [AZURE.SELECTOR]
- [Azure-portalen](active-directory-groups-create-azure-portal.md)
- [Azure klassisk portal](active-directory-accessmanagement-manage-groups.md)
- [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)

Følgende dokument får du med eksempler på, hvordan du kan bruge PowerShell til at administrere dine grupper i Azure Active Directory (Azure AD).  Den indeholder også oplysninger om at komme i gang med modulet Azure AD PowerShell preview. Du skal først [hente Azure AD PowerShell-modulet](http://go.microsoft.com/fwlink/p/?LinkId=828627).

## <a name="installing-the-azure-ad-powershell-module"></a>Installation af Azure AD PowerShell-modulet

For at installere modulet AzureAD PowerShell eksempel skal du bruge følgende kommandoer:

    PS C:\Windows\system32> install-module azureadpreview

For at bekræfte, at modulet preview er installeret, kan du bruge følgende kommando:

    PS C:\Windows\system32> get-module azureadpreview

    ModuleType Version    Name                                ExportedCommands
    ---------- -------    ----                                ----------------
    Binary     1.1.146.0  azureadpreview                      {Add-AzureADAdministrati...}

Nu kan du begynde at bruge cmdlet'erne i modulet. Se [online referencedokumentation](https://msdn.microsoft.com/library/azure/mt757216.aspx)for en fuldstændig beskrivelse af cmdletter i modulet AzureAD Preview.

## <a name="connecting-to-the-directory"></a>Oprette forbindelse til mappen
Før du kan begynde at administrere grupper ved hjælp af Azure AD PowerShell-cmdletter preview, skal du tilslutte din PowerShell-session til den mappe, du vil administrere. Brug følgende kommando til at udføre dette:

    PS C:\Windows\system32> Connect-AzureAD -Force

Cmdletten bliver du bedt om de legitimationsoplysninger, du vil bruge til at få adgang til mappen. I dette eksempel skal vi bruger karen@drumkit.onmicrosoft.com adgang til demonstration mappen. Cmdletten vil returnere en bekræftelse for at få vist sessionen var tilsluttet korrekt til adresselisten:

    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…

Nu kan du begynde at bruge AzureAD preview-cmdletter til at administrere grupper i mappen.

## <a name="retrieving-groups"></a>Hentning af grupper
Du kan bruge cmdlet'en Get-AzureADGroups til at hente eksisterende grupper fra adresselisten. Brug cmdlet'en uden parametre for at hente alle grupperne i mappen:

    PS C:\Windows\system32> get-azureadgroup

Cmdlet returnerer alle grupper i mappen forbundne.

Du kan bruge parameteren - objectID til at hente en bestemt gruppe, du angiver gruppens objectID:

    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b

Cmdlet returnerer nu gruppen Hvis objectID svarer til værdien i parameteren, du har angivet:

    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Du kan søge efter en bestemt gruppe ved hjælp af Filterparameteren -. Denne parameter tager delsætningen ODATA filter og returnerer alle grupper, der svarer til filteret, som i følgende eksempel:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Bemærk, at AzureAD PowerShell-cmdletterne implementere OData forespørgsel standard, du kan finde flere oplysninger [her](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter).

## <a name="creating-groups"></a>Oprette grupper
Brug cmdlet'en ny AzureADGroup for at oprette en ny gruppe i mappen. Denne cmdlet opretter en ny sikkerhedsgruppe hedder "Marketing":

    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"

## <a name="updating-groups"></a>Opdatering af grupper
Hvis du vil opdatere en eksisterende gruppe, kan bruge cmdlet'en Set-AzureADGroup til. I dette eksempel skal vi er ved at ændre egenskaben vist navn for gruppen "Intune administratorer." Først skal vi søger efter den gruppe, ved hjælp af cmdlet'en Get-AzureADGroup til og filtrere ved hjælp af attributten vist navn:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Vi er næste, ved at ændre egenskaben beskrivelse til den nye værdi "Intune enhed administratorer":

    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"

Nu hvis vi finde gruppen igen, skal vi ser egenskaben beskrivelse er opdateret for at afspejle den nye værdi:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

## <a name="deleting-groups"></a>Slette grupper
For at slette grupper fra adresselisten, skal du bruge Fjern AzureADGroup cmdlet på følgende måde:

    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b

## <a name="managing-members-of-groups"></a>Administrere medlemmer af grupper
Hvis du vil føje nye medlemmer til en gruppe skal du bruge Tilføj AzureADGroupMember cmdlet. Denne kommando føjer et medlem til Intune administratorgruppen vi har brugt i det forrige eksempel:

    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Parameteren - ObjectId er ObjectID på den gruppe, som vi vil føje et medlem, og RefObjectId - er ObjectID på den bruger vi vil tilføje som et medlem af gruppen.

Brug cmdlet'en Get-AzureADGroupMember til, som i dette eksempel for at få de eksisterende medlemmer af en gruppe:

    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                        72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                        8120cc36-64b4-4080-a9e8-23aa98e8b34f User

For at fjerne det medlem, vi tidligere er føjet til gruppen, skal du bruge Cmdletten Fjern AzureADGroupMember, som er vist her:

    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Brug cmdlet'en Vælg AzureADGroupIdsUserIsMemberOf til at undersøge gruppe membership(s) for en bruger. Denne cmdlet tager som dens parametre ObjectId på den bruger, du vil kontrollere gruppemedlemskaber og en liste over grupper, du vil kontrollere medlemskaberne. En liste over grupper skal være leveres i form af et komplekst variabel af typen "Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck", så vi skal først oprette en variabel med denne type:

    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck

Dernæst skal vi giver dig værdier for groupIds at tjekke attributten "GroupIds" af denne komplekst variabel:

    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"

Nu, hvis vi ønsker at kontrollere gruppemedlemskaber for en bruger med ObjectID 72cd4bbd-2594-40a2-935c-016f3cfeeeea mod grupper i $g, skal vi bruge:

    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                               Value
    -------------                                                                                               -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}


Den returnerede værdi er en liste over grupper, som brugeren er medlem. Du kan også anvende denne metode til at kontrollere kontakter, grupper eller principper for tjenesten medlemskab af en bestemt liste over grupper, ved hjælp af Vælg AzureADGroupIdsContactIsMemberOf, Vælg AzureADGroupIdsGroupIsMemberOf eller Vælg AzureADGroupIdsServicePrincipalIsMemberOf

## <a name="managing-owners-of-groups"></a>Administrere ejere af grupper
Hvis du vil føje ejere til en gruppe, skal du bruge Tilføj AzureADGroupOwner cmdlet:

    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Parameteren - ObjectId er ObjectID på den gruppe, som vi vil føje en ejer, og RefObjectId - er ObjectID på den bruger vi vil tilføje som en ejer af gruppen.

For at hente ejerne af en gruppe skal du bruge Get-AzureADGroupOwner:

    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

Cmdlet returnerer listen over ejere for den angivne gruppe:

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                        e831b3fd-77c9-49c7-9fca-de43e109ef67 User

Hvis du vil fjerne en ejer fra en gruppe, skal du bruge Fjern AzureADGroupOwner:

    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67

## <a name="next-steps"></a>Næste trin

Du kan finde flere Azure Active Directory PowerShell dokumentation på [Azure Active Directory-cmdletter](http://go.microsoft.com/fwlink/p/?LinkId=808260).

* [Adgangskontrol til ressourcer med Azure Active Directory-grupper](active-directory-manage-groups.md)

* [Integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md)
