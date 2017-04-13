<properties
    pageTitle="Azure Active Directory-cmdletter til konfiguration af gruppeindstillinger | Microsoft Azure"
    description="Hvordan administrere indstillingerne for grupper ved hjælp af Azure Active Directory-cmdletter."
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
    ms.date="09/22/2016"
    ms.author="curtand"/>


# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Azure Active Directory-cmdletter til konfiguration af gruppeindstillinger

Følgende indstillinger for samlet grupper kan konfigureres i mappen:

1.  Klassificeringer: den kommasepareret liste over klassificeringer, som brugerne kan angive på en gruppe. Eksempler ville være "Classified", "Secret" og "Øverste hemmeligt."

2.  URL-adresse til brugen retningslinjer: en URL-adresse, der peger brugere på vilkår for anvendelse for brug af Unified grupper, som defineret af organisationen. Denne URL-adresse, vises i brugergrænsefladen, hvor brugerne bruge grupper.

3.  Gruppere oprettelse af aktiveret: om ingen, nogle eller alle brugere har tilladelse til at oprette Unified grupper. Når indstillet til til, kan alle brugere oprette grupper. Når den er indstillet til deaktiveret, kan ingen brugere oprette grupper. Når fra, kan du også angive en sikkerhedsgruppe, hvis brugere, der er stadig tilladelse til at oprette grupper.

Disse indstillinger er konfigureret ved hjælp af en indstillinger og SettingsTemplate objekter. Første gang, se du ikke alle indstillinger for objekter i mappen. Det betyder, at mappen er konfigureret med standardindstillingerne. Du skal oprette et nyt indstillinger-objekt ved hjælp af en skabelon i indstillinger for at ændre standardindstillingerne. Indstillinger skabeloner er defineret af Microsoft.

Du kan hente det modul, der indeholder de cmdlet'er, der bruges til disse handlinger fra [Microsoft Connect-webstedet](http://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185).

## <a name="create-settings-at-the-directory-level"></a>Oprette indstillinger på mappeniveau

Disse trin oprette indstillinger på mappeniveau, som gælder for alle Office-grupper i kataloget.

1. Hvis du ikke ved, hvilken SettingTemplate skal bruges, returnerer denne cmdlet på listen over indstillinger skabeloner:

    `Get-MsolAllSettingTemplate`

    ![Liste over indstillinger skabeloner](./media/active-directory-accessmanagement-groups-settings-cmdlets/list-of-templates.png)

2. For at tilføje en brugen vejledende URL-adresse skal først du få objektet SettingsTemplate, der definerer værdien brugen vejledende URL-adresse. skabelonen Group.Unified:

    `$template = Get-MsolSettingTemplate –TemplateId 62375ab9-6b52-47ed-826b-58e47e0e304b`

3. Dernæst skal du oprette et nyt indstillinger-objekt, der er baseret på skabelonen:

    `$setting = $template.CreateSettingsObject()`

4. Derefter opdatere brugen vejledende værdi:

    `$setting["UsageGuidelinesUrl"] = "<https://guideline.com>"`

5. Til sidst skal anvende indstillingerne:

    `New-MsolSettings –SettingsObject $setting`

    ![Tilføje en brugen vejledende URL-adresse](./media/active-directory-accessmanagement-groups-settings-cmdlets/add-usage-guideline-url.png)

Her er de indstillinger, der er defineret i Group.Unified SettingsTemplate.

 **Indstilling**                          | **Beskrivelse**                                                                                             
--------------------------------------|-----------------------------------------------
 <ul><li>ClassificationList<li>Type: streng<li>Standard: ""                  | En kommasepareret liste over gyldige klassifikation værdier, der kan anvendes til Unified grupper.                
 <ul><li>EnableGroupCreation<li>Type: boolesk<li>Som standard: SAND              | Flaget, der angiver, om oprettelse af samlede grupper er tilladt i kataloget.                               
 <ul><li>GroupCreationAllowedGroupId<li>Type: streng<li>Standard: ""         | GUID af sikkerhedsgruppen, der har tilladelse til at oprette Unified grupper, selv når EnableGroupCreation == falsk.
 <ul><li>UsageGuidelinesUrl<li>Type: streng<li>Standard: ""                  | Et link til retningslinjer for anvendelse af gruppen.                                                                       

## <a name="read-settings-at-the-directory-level"></a>Læs indstillinger på mappeniveau

Disse trin Læs indstillinger på mappeniveau, som gælder for alle Office-grupper i kataloget.

1. Læse alle eksisterende directory indstillinger:

    `Get-MsolAllSettings`

2. Læse alle indstillinger for en bestemt gruppe:

    `Get-MsolAllSettings -TargetType Groups -TargetObjectId <groupObjectId>`

3. Læs specifik mappe indstillinger ved hjælp af SettingId GUID:

    `Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

    ![Indstillinger for ID GUID](./media/active-directory-accessmanagement-groups-settings-cmdlets/settings-id-guid.png)

## <a name="update-settings-at-the-directory-level"></a>Opdatere indstillinger for på mappeniveau

Disse trin opdatere indstillinger for på mappeniveau, som gælder for alle Office-grupper i kataloget.

1. Få objektet eksisterende indstillinger:

    `$setting = Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

2. Få den værdi, du vil opdatere:

    `$value = $Setting.GetSettingsValue()`

3. Opdatere værdien:

    `$value["AllowToAddGuests"] = "false"`

4. Opdater indstillingen:

    `Set-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c –SettingsValue $value`

## <a name="remove-settings-at-the-directory-level"></a>Fjerne indstillinger på mappeniveau

Dette trin fjerner indstillinger på mappeniveau, som gælder for alle Office-grupper i kataloget.

    `Remove-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

## <a name="cmdlet-syntax-reference"></a>Syntaksreference for cmdlet

Du kan finde flere Azure Active Directory PowerShell dokumentation på [Azure Active Directory-cmdletter](http://go.microsoft.com/fwlink/p/?LinkId=808260).

## <a name="settingstemplate-object-reference-groupunified-settingstemplate-object"></a>SettingsTemplate objektreferencer (Group.Unified SettingsTemplate objekt)

- "navn": "EnableGroupCreation", "type": "System.Boolean", "standardværdi": "sand", "beskrivelse": "En boolesk flag, der angiver, om funktionen samlede gruppe oprettelse er på."

- "navn": "GroupCreationAllowedGroupId", "type": "System.Guid", "standardværdi": "", "beskrivelse": "GUID af sikkerhedsgruppen, der er whitelisted til at oprette samlede grupper".

- "navn": "ClassificationList", "type": "System.String", "standardværdi": "", "beskrivelse": "En kommasepareret liste over gyldige klassifikation værdier, der kan anvendes på samlede grupper."

- "navn": "UsageGuidelinesUrl", "type": "System.String", "standardværdi": "", "beskrivelse": "Et link til gruppen brugen retningslinjer."

Navn | type | Standardværdi | Beskrivelse
----------  | ----------  | ---------  | ----------
"EnableGroupCreation"  | "System.Boolean"  | "true"  | "En boolesk flag, der angiver, om funktionen samlede gruppe oprettelse er på."
"GroupCreationAllowedGroupId"  | "System.Guid"  | ""  | "GUID af sikkerhedsgruppen, der er whitelisted til at oprette Unified grupper".
"ClassificationList"  | "System.String"  | ""  | "En kommasepareret liste over gyldige klassifikation værdier, der kan anvendes til Unified grupper."
"UsageGuidelinesUrl"  | "System.String"  | ""  | "Et link til gruppen brugen retningslinjer."

## <a name="next-steps"></a>Næste trin

Du kan finde flere Azure Active Directory PowerShell dokumentation på [Azure Active Directory-cmdletter](http://go.microsoft.com/fwlink/p/?LinkId=808260).

Yderligere instruktion fra Microsoft program manager Rob de Jong er tilgængelig på [Robs grupper Blog](http://robsgroupsblog.com/blog/configuring-settings-for-office-365-groups-in-azure-ad).

* [Adgangskontrol til ressourcer med Azure Active Directory-grupper](active-directory-manage-groups.md)

* [Integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md)
