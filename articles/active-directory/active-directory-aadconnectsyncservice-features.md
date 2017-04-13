<properties
    pageTitle="Azure AD-forbindelse Synkroniser service-funktioner og konfiguration | Microsoft Azure"
    description="I denne artikel beskrives service side-funktioner til Azure AD-forbindelse Synkroniser tjeneste."
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
    ms.date="08/22/2016"
    ms.author="andkjell;markvi"/>

# <a name="azure-ad-connect-sync-service-features"></a>Azure AD-forbindelse Synkroniser service-funktioner

Synkroniseringsfunktionen af Azure AD-forbindelse har to komponenter:

- Komponenten lokalt med navnet **Azure AD-forbindelse synkronisering**, også kaldet **Synkroniser program**.
- Tjenesten fra Azure AD også kendt som **Azure AD-forbindelse Synkroniser service**

Dette emne forklares det, hvordan de følgende funktioner i **Azure AD-forbindelse synkroniseringstjenesten** fungerer, og hvordan du kan konfigurere dem ved hjælp af Windows PowerShell.

Disse indstillinger er konfigureret af [Azure Active Directory-modulet til Windows PowerShell](http://aka.ms/aadposh). Hent og installer det separat fra Azure AD-forbindelse. Cmdlet'erne beskrevet i dette emne blev introduceret i [2016 marts slip (build 9031.1)](http://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Hvis du har ikke de cmdlet'er, der er beskrevet i dette emne, eller de ikke giver det samme resultat, derefter kontrollere, at du kører den nyeste version.

For at se konfigurationen i Azure AD-biblioteket skal køre `Get-MsolDirSyncFeatures`.  
![Get-MsolDirSyncFeatures resultat](./media/active-directory-aadconnectsyncservice-features/getmsoldirsyncfeatures.png)

Mange af disse indstillinger kan kun ændres af Azure AD-forbindelse.

Følgende indstillinger kan konfigureres ved `Set-MsolDirSyncFeature`:

DirSyncFeature | Kommentar
--- | ---
[DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) | Gør det muligt for en attribut, der skal være sat i karantæne, når det er en dublet af et andet objekt i stedet for hele objektet ned under Eksportér.
[EnableSoftMatchOnUpn](#userprincipalname-soft-match) | Gør det muligt for objekter til en joinforbindelse userPrincipalName ud over primære SMTP-adresse.
[SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) | Giver mulighed for synkronisering af programmet til at opdatere userPrincipalName-attributten for administrerede/licens (uden for organisationsnetværket) brugere.

Når du har aktiveret en funktion, kan det deaktiveres igen.

>[AZURE.NOTE] Fra August 24 2016 funktionen *Dupliker attribut fleksibilitet* er aktiveret som standard for nye Azure AD-mapper. Denne funktion kan også gennemført og aktiveret på kataloger, der er oprettet før denne dato. Du får besked via mail, når mappen er ved at få denne funktion er aktiveret.

Følgende indstillinger er konfigureret ved Azure AD-forbindelse og kan ikke ændres af `Set-MsolDirSyncFeature`:

DirSyncFeature | Kommentar
--- | ---
DeviceWriteback | [Azure AD-forbindelse: Aktivering af enhed tilbageførsel](active-directory-aadconnect-feature-device-writeback.md)
DirectoryExtensions | [Azure AD-forbindelse synkronisering: Directory filtypenavne](active-directory-aadconnectsync-feature-directory-extensions.md)
PasswordSync | [Implementere synkronisering af adgangskoder til Azure AD-forbindelse synkronisering](active-directory-aadconnectsync-implement-password-synchronization.md)
UnifiedGroupWriteback | [Eksempel: Gruppe tilbageførsel](active-directory-aadconnect-feature-preview.md#group-writeback)
UserWriteback | Understøttes ikke i øjeblikket.

## <a name="duplicate-attribute-resiliency"></a>Duplikere attribut fleksibilitet
I stedet for fejlbehæftede at blive klargjort objekter med dublerede UPN / proxyAddresses, attributten dublerede er "sat i karantæne" og en midlertidig værdi er tildelt. Når konflikten er løst, ændres den midlertidige UPN automatisk til den korrekte værdi. Dette problem kan være aktiveret for UPN og proxyAddress separat. Få mere at vide under [identitet synkronisering og dublerede attribut fleksibilitet](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md).

## <a name="userprincipalname-soft-match"></a>UserPrincipalName bløde match
Når denne funktion er aktiveret, er bløde match aktiveret for UPN ud over den [primære SMTP-adresse](https://support.microsoft.com/kb/2641663), som altid er aktiveret. Bløde match-bruges til at matche eksisterende brugere i skyen i Azure AD med lokale brugere.

Hvis du vil match lokalt AD konti med eksisterende konti, der er oprettet i skyen og du bruger ikke Exchange Online og derefter denne funktion er nyttig. I dette scenarie skal har du generelt ikke en grund til at angive egenskaben SMTP i skyen.

Denne funktion er som standard for nyligt oprettet Azure AD-mapper. Du kan se, hvis denne funktion er aktiveret for dig ved at køre:  
```
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Hvis denne funktion ikke er aktiveret for din Azure AD-mappe, kan du aktivere den ved at køre:  
```
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>Synkronisere userPrincipalName opdateringer
Historisk set opdateringer til UserPrincipalName-attributten ved hjælp af synkroniseringstjenesten fra det lokale er blevet blokeret, medmindre begge af disse betingelser er opfyldt:

- Brugeren administreres (ikke-sammenkædet).
- Brugeren har ikke fået tildelt en licens.

Yderligere oplysninger finder du [brugernavne i Office 365, Azure, eller Intune ikke svarer til den lokale UPN eller alternative logon-ID](https://support.microsoft.com/kb/2523192).

Aktivere denne funktion gør det muligt for synkronisering af programmet til at opdatere userPrincipalName, når det er ændret lokalt, og du bruger synkronisering af adgangskoder. Denne funktion understøttes ikke, hvis du bruger sammenslutning.

Denne funktion er som standard for nyligt oprettet Azure AD-mapper. Du kan se, hvis denne funktion er aktiveret for dig ved at køre:  
```
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Hvis denne funktion ikke er aktiveret for din Azure AD-mappe, kan du aktivere den ved at køre:  
```
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

Når du har aktiveret denne funktion, forbliver eksisterende userPrincipalName værdier som-er. Normal delta Synkroniser på brugere opdateres ved næste ændring af userPrincipalName-attributten lokale UPN.  

## <a name="see-also"></a>Se også

- [Synkronisering af Azure AD-forbindelse](active-directory-aadconnectsync-whatis.md)
- [Integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).
