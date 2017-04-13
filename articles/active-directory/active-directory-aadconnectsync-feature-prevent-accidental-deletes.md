<properties
   pageTitle="Azure AD-forbindelse synkronisering: Undgå utilsigtede sletninger | Microsoft Azure"
   description="Dette emne beskrives funktionen Forhindring af utilsigtede sletter (forhindrer utilsigtede sletninger) i Azure AD-forbindelse."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/01/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Azure AD-forbindelse synkronisering: Undgå utilsigtede sletninger
Dette emne beskrives funktionen Forhindring af utilsigtede sletter (forhindrer utilsigtede sletninger) i Azure AD-forbindelse.

Når du installerer Azure AD-forbindelse, forhindre utilsigtede er sletter som standard aktiveret og konfigureret til at tillade en eksport med mere end 500 sletter ikke. Denne funktion er udviklet til at beskytte mod ændringer i utilsigtede konfigurationen og ændringer til din lokale mappe, der kan påvirke mange brugere og andre objekter.

Almindelige scenarier, når du ser mange sletter omfatter:

- Ændringer til [filtrering](active-directory-aadconnectsync-configure-filtering.md) hvor en hel [OU](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) eller et [domæne](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering) er ikke er valgt.
- Alle objekter i en OU slettes.
- En OU er blevet omdøbt, så alle objekter i den anses for at være væk omfang til synkronisering.

Standardværdien for 500 objekter kan ændres med PowerShell ved hjælp af `Enable-ADSyncExportDeletionThreshold`. Du skal konfigurere denne værdi for at tilpasse størrelsen af din organisation. Da Synkroniser planlæggeren kørt hver 30 minutter, er værdien antallet af sletter set inden for 30 minutter.

Hvis der er for mange sletter midlertidigt for at kunne eksporteres til Azure AD, Eksportér stopper derefter, og du modtager en mail således:

![Forhindre utilsigtede sletter mail](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/email.png)

> *Hej (tekniske Kontakt). På (tid) fundet tjenesten identitet synkronisering, antallet af sletninger overskredet grænseværdi for konfigurerede sletning for (organisationens navn). En samlet antal af (tal) objekter blev sendt til sletning i denne identitet synkroniseringen. Dette opfyldt eller overskredet grænseværdi konfigurerede sletningen af (tal) objekter. Du skal bekræfte, at disse sletninger skal behandles, før vi fortsætter. Se Forhindring utilsigtede sletninger kan finde flere oplysninger om fejlen er angivet i denne e-mail-meddelelse.*

Du kan også få vist status `stopped-deletion-threshold-exceeded` når du ser i **Synkronisering Service Manager** brugergrænseflade til eksport profilen.
![Undgå utilsigtede sletninger Synkroniser Service Manager UI](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/syncservicemanager.png)

Hvis det var uventet, undersøge og foretage korrigerende handlinger. Hvis du vil se, hvilke objekter er ved at blive slettet, skal du gøre følgende:

1. Starte **tjenesten Brugerprofilsynkronisering** fra startmenuen.
2. Gå til **forbindelser**.
3. Vælg forbindelsen med type **Azure Active Directory**.
4. Vælg **Søg Connector-plads**under **Handlinger** til højre.
5. Vælg **Afbrudt, da** i pop op-vinduet under **omfang**, og vælg et klokkeslæt i fortiden. Klik på **Søg**. Denne side indeholder en oversigt over alle objekter ved at blive slettet. Du kan få flere oplysninger om objektet ved at klikke på hvert element. Du kan også klikke på **Kolonne indstilling** for at tilføje yderligere attributter for at være synlig i gitteret.

![Søg Connector-plads](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/searchcs.png)

Alle sletter er beskedteksten, hvis du derefter gøre følgende:

1. Midlertidigt at deaktivere denne beskyttelse og lade disse sletter går igennem, køre PowerShell-cmdlet: `Disable-ADSyncExportDeletionThreshold`. Angive en Global Administrator for Azure AD-konto og en adgangskode.
![Legitimationsoplysninger](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/credentials.png)
2. Vælg handlingen, der **kører** med Azure Active Directory Connector stadig er markeret, og vælg **eksportere**.
3. Hvis du vil aktivere beskyttelse igen, skal du køre PowerShell-cmdlet: `Enable-ADSyncExportDeletionThreshold`.

## <a name="next-steps"></a>Næste trin

**Oversigt oversigtsemner**

- [Azure AD-forbindelse synkronisering: forstå og tilpasse synkronisering](active-directory-aadconnectsync-whatis.md)
- [Integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md)
