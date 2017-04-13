<properties
    pageTitle="Azure AD-forbindelse synkronisering: forstå og tilpasse synkroniseringen | Microsoft Azure"
    description="Forklarer, hvordan Azure AD-forbindelse synkroniserer fungerer, og hvordan du tilpasser."
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
    ms.date="08/29/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Azure AD-forbindelse synkronisering: forstå og tilpasse synkronisering
Azure Active Directory forbinde-synkroniseringstjenester (Azure AD-forbindelse sync) er en primære komponent i Azure AD-forbindelse. Det sig tager af alle de handlinger, der er relateret til at synkronisere identitetsdata mellem dit lokale miljø og Azure AD. Azure AD-forbindelse synkronisering er en opgaves efterfølgende DirSync, Azure Active Directory-synkronisering og Forefront identitet Manager med Azure Active Directory Connector konfigureret.

Dette emne er Startside for **synkronisering af Azure AD-forbindelse** (også kaldet **Synkroniser program**) og viser en liste over links til alle andre emner, der er relateret til den. Links til Azure AD-forbindelse, kan du se [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).

Tjenesten sync består af to komponenter, komponenten lokale **Azure AD-forbindelse synkronisering** og service-side i Azure AD kaldet **Azure AD-forbindelse Synkroniser tjeneste**. Tjenesten er fælles for DirSync, Azure Active Directory-synkronisering og Azure AD-forbindelse.

## <a name="azure-ad-connect-sync-topics"></a>Azure AD-forbindelse Synkroniser emner

Emne | Hvad det dækker, og hvornår skal jeg læse
----- | -----
**Azure AD-forbindelse Synkroniser grundlæggende** |
[Forstå arkitekturen](active-directory-aadconnectsync-understanding-architecture.md) | For dem, for hvem der er ny bruger synkronisering-program og vil vide mere om arkitekturen og udtryk, der anvendes.
[Tekniske koncepter](active-directory-aadconnectsync-technical-concepts.md) | En korte version af emnet arkitektur og kortvarigt forklares de ord, der bruges.
[Topologier til Azure AD-forbindelse](active-directory-aadconnect-topologies.md) | Beskriver de forskellige topologier og scenarier Synkroniser program understøtter.
**Brugerdefineret konfiguration** |
[Køre installationsguiden igen](active-directory-aadconnectsync-installation-wizard.md) | Beskriver, hvilke muligheder du har tilgængelige, når du kører installationsguiden af Azure AD-forbindelse igen.
[Forstå deklarativ klargøring](active-directory-aadconnectsync-understanding-declarative-provisioning.md)| I denne artikel beskrives konfigurationsmodellen kaldet deklarativ klargøring.
[Forstå deklarativ klargøring udtryk](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) | I denne artikel beskrives syntaksen for udtryksprog i deklarativ klargøring.
[Forstå standardkonfiguration](active-directory-aadconnectsync-understanding-default-configuration.md)| I denne artikel beskrives de out of box regler og standardkonfiguration. Også beskriver, hvordan reglerne, der fungerer sammen for out of box scenarier til at arbejde.
[Forstå brugere og kontakter](active-directory-aadconnectsync-understanding-users-and-contacts.md) | Fortsætter på det forrige emne og det beskrives, hvordan konfigurationen for brugere og kontaktpersoner fungerer sammen, især i et miljø med flere områder.
[Sådan foretager en ændring af standard konfigurationen](active-directory-aadconnectsync-change-the-configuration.md) | Vejleder dig gennem Sådan gør du en almindelig konfiguration ændre til attribut flyder.
[Bedste fremgangsmåder til at ændre standardkonfigurationen](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) | Understøtter begrænsninger og til at foretage ændringer af konfigurationen out of box.
[Konfiguration af filtrering](active-directory-aadconnectsync-configure-filtering.md) | I denne artikel beskrives de forskellige indstillinger til at begrænse, hvilke objekter, der synkroniseres til Azure AD og trinvise Sådan konfigureres disse indstillinger.
**Funktioner og -scenarier** |
[Undgå utilsigtede sletninger](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) | I denne artikel beskrives funktionen *Undgå utilsigtede sletninger* , og hvordan du konfigurerer den.
[Opgavestyring](active-directory-aadconnectsync-feature-scheduler.md) | I denne artikel beskrives de indbyggede scheduler, som er importere, synkronisering og eksport af data.
[Implementere synkronisering af adgangskoder](active-directory-aadconnectsync-implement-password-synchronization.md) | I denne artikel beskrives, hvordan fungerer synkronisering af adgangskoder, hvordan du kan implementere, og hvordan du betjene og foretage fejlfinding af.
[Enhed tilbageførsel](active-directory-aadconnect-feature-device-writeback.md) | I denne artikel beskrives, hvordan enhed tilbageførsel fungerer i Azure AD-forbindelse.
[Directory filtypenavne](active-directory-aadconnectsync-feature-directory-extensions.md) | I denne artikel beskrives, hvordan du udvider Azure AD-skema med dine egne brugerdefinerede attributter.
**Synkronisere Service** |
[Azure AD-forbindelse Synkroniser service-funktioner](active-directory-aadconnectsyncservice-features.md) | Beskriver Synkroniser service side og hvordan du kan ændre synkroniseringsindstillinger i Azure AD.
[Duplikere attribut fleksibilitet](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) | Beskriver, hvordan du aktiverer og bruger **userPrincipalName** og **proxyAddresses** dublerede attribut værdier fleksibilitet.
**Handlinger og brugergrænseflade** |
[Synkronisering Service Manager](active-directory-aadconnectsync-service-manager-ui.md) | I denne artikel beskrives synkronisering Service Manager UI, herunder [Handlinger](active-directory-aadconnectsync-service-manager-ui-operations.md), [forbindelser](active-directory-aadconnectsync-service-manager-ui-connectors.md), [Metaverse Designer](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md)og [Metaverse-søgning](active-directory-aadconnectsync-service-manager-ui-mvsearch.md) faner.
[Driftsopgaver og overvejelser i forbindelse med](active-directory-aadconnectsync-operations.md) | I denne artikel beskrives funktionsdygtige problemstillinger, såsom nedbrud.
**Hvordan...** |
[Nulstille Azure AD-konto](active-directory-aadconnectsync-howto-azureadaccount.md) | Sådan nulstilles legitimationsoplysningerne for den tjenestekonto, der bruges til at oprette forbindelse fra Azure AD-forbindelse synkronisering til Azure AD.
**Flere oplysninger og referencer** |
[Porte](active-directory-aadconnect-ports.md) | Hvilke porte, skal du åbne mellem Synkroniser engine og din lokale mapper og Azure AD-lister.
[Attributter, der er synkroniseret til Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md) | Viser alle de attributter, der synkroniseres mellem lokale AD og Azure AD.
[Funktioner Reference](active-directory-aadconnectsync-functions-reference.md) | Viser alle de funktioner, der er tilgængelige i deklarativ klargøring.

## <a name="additional-resources"></a>Yderligere ressourcer

* [Integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md)
