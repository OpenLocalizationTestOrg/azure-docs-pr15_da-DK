<properties
    pageTitle="Azure AD-forbindelse synkronisering: synkronisering Service Manager UI | Microsoft Azure"
    description="Forstå fanen forbindelser i synkronisering Service Manager for Azure AD-forbindelse."
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
    ms.date="09/07/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-synchronization-service-manager"></a>Azure AD-forbindelse synkronisering: synkronisering Service Manager

[Handlinger](active-directory-aadconnectsync-service-manager-ui-operations.md) | [Forbindelser](active-directory-aadconnectsync-service-manager-ui-connectors.md) | [Metaverse Designer](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md) | [Metaverse-søgning](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)
--- | --- | --- | ---

![Synkroniser Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectors.png)

Fanen forbindelser, der bruges til at administrere alle systemer Synkroniser program er forbundet med.

## <a name="connector-actions"></a>Forbindelse handlinger

Handling | Kommentar
--- | ---
Oprette | Brug ikke. Brug installationsguiden til at oprette forbindelse til flere AD områder.
Egenskaber | Bruges til domæne og OU filtrering.
[Slet](#delete) | Bruges til at enten slette dataene i den forbindelse plads eller slette forbindelse til et område.
[Konfigurere køre profiler](#configure-run-profiles) | Med undtagelse af domæne filtrering, ikke noget at konfigurere her. Du kan bruge denne handling til at se allerede konfigureret køre profiler.
Kør | Bruges til at starte en manuel kørsel af en profil.
Stop | Stopper en forbindelse i øjeblikket kører en profil.
Eksportere forbindelse | Brug ikke.
Importere forbindelse | Brug ikke.
Opdatere forbindelsen | Brug ikke.
Opdater skema | Opdaterer cachelagrede skemaet. Det er foretrukne i stedet bruge indstillingen i installationsguiden siden, der også opdateringer synkronisere regler.
[Søg Connector-plads](#search-connector-space) | Bruges til at søge efter objekter og [følge et objekt og dens data hele systemet](#follow-an-object-and-its-data-through-the-system).

### <a name="delete"></a>Slet
Slettehandlingen bruges til to forskellige ting.
![Synkroniser Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

Indstillingen **Slet connector-plads** fjernes alle data, men bevare konfigurationen.

Indstillingen **Slet Connector og connector-plads** fjerner dataene og konfigurationen. Denne indstilling bruges, når du ikke ønsker at oprette forbindelse til et område længere.

Begge muligheder synkronisering af alle objekter og opdatere metaverse objekterne. Denne handling er en længerevarende handling.

### <a name="configure-run-profiles"></a>Konfigurere køre profiler
Denne indstilling giver dig mulighed at se de køre profiler, der er konfigureret til en forbindelse.

![Synkroniser Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/configurerunprofiles.png)

### <a name="search-connector-space"></a>Søg Connector-plads
Handlingen Søg forbindelse mellemrum er nyttigt at søge efter objekter og foretage fejlfinding af problemer med data.

![Synkroniser Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearch.png)

Start ved at vælge et **omfang**. Du kan søge ud fra data (RDN, DN, anker underordnede træet), eller tilstand for objektet (alle andre indstillinger).  
![Synkroniser Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchscope.png)  
Hvis du gør for eksempel en underordnet træet søgning, får du alle objekter i en OU.
![Synkroniser Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png) fra dette gitter kan du vælge et objekt, Vælg **Egenskaber**og [følge den](#follow-an-object-and-its-data-through-the-system) fra kilde forbindelse mellemrum via metaverse og til destinationen connector-plads.

## <a name="follow-an-object-and-its-data-through-the-system"></a>Følg et objekt og dens data hele systemet
Når du foretager fejlfinding af problemer med data, følge et objekt fra kilde connector-plads til metaverse, og til destinationen forbindelse space er en vigtige procedure at forstå, hvorfor data har ikke de forventede værdier.

### <a name="connector-space-object-properties"></a>Egenskaber for forbindelse objekt
**Importér**  
Når du åbner et cs objekt, er der flere faner øverst. Fanen **importere** viser de data, er midlertidigt efter en import.
![Synkroniser Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/csimport.png) den **Gamle værdi** viser det i øjeblikket er gemt i systemet og **Ny værdi** , hvad der er modtaget fra kildesystemet og er ikke blevet anvendt. I dette tilfælde, da der er en synkroniseringsfejl, kan ændringen ikke anvendes.

**Fejl**  
Fejlsiden vises kun, hvis der er et problem med objektet. Du kan se oplysningerne på siden handlinger for flere oplysninger om, hvordan du [udfører fejlfinding af synkroniseringsfejl](active-directory-aadconnectsync-service-manager-ui-operations.md#troubleshoot-errors-in-operations-tab).

**Om datakildeafstamning**  
Fanen om datakildeafstamning viser, hvordan objektet forbindelse mellemrum er relateret til objektet metaverse. Du kan se, når forbindelsen importeret senest en ændring fra det tilsluttede system, og hvilke regler, der anvendes til at udfylde data i metaverse.
![Synkroniser Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cslineage.png) i kolonnen **handling** , kan du se, der er en regel for **indgående** synkronisering med handlingen **klargøring**. Der angiver, så længe denne forbindelse mellemrum objekt er til stede, forbliver objektet metaverse. Hvis listen over Synkroniser regler viser i stedet for en regel for synkronisering med retningen **udgående** og **klargøring**, angiver, at dette objekt slettes, når objektet metaverse slettes.
![Synkroniser Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cslineageout.png) du kan også få vist i kolonnen **PasswordSync** , at den indgående forbindelse plads kan bidrage ændringer til adgangskoden, da en regel for synkronisering har værdien **Sand**. Denne adgangskode sendes til Azure AD gennem den udgående regel.

Fanen om datakildeafstamning, kan du åbne metaverse ved at klikke på [Metaverse objektegenskaber](#metaverse-object-properties).

Nederst på alle faner er der to knapper: **Preview** og **logfiler**.

**Vis udskrift**  
Eksempelsiden bruges til at synkronisere ét enkelt objekt. Det er praktisk, hvis du foretager fejlfinding af nogle regler, Synkroniser kunde og vil du se effekten af en ændring i et enkelt objekt. Du kan vælge mellem **Fuld synkronisering** og **Delta synkronisering**. Du kan også vælge mellem **Generere Preview**, som kun bevarer ændringen i hukommelsen, og **Udfør Preview**, hvilke alle stadier ændres til destinationen forbindelse mellemrum.
![Synkroniser Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/preview1.png) du kan undersøge objektet, og hvilken regel anvendes for en bestemt attribut forløb.
![Synkroniser Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/preview2.png)

**Log**  
Siden Log bruges til at se de adgangskode synkroniseringsstatus og historien, se [fejlfinding i forbindelse med synkronisering af adgangskoder](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization) for flere oplysninger.

### <a name="metaverse-object-properties"></a>Metaverse objektegenskaber
**Attributter**  
Du kan se værdierne, og hvilke forbindelse bidraget til den under fanen attributter.
![Synkroniser Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/mvattributes.png)
**forbindelser**  
Fanen forbindelser viser alle forbindelse mellemrum, der har en repræsentation af objektet.
![Synkroniser Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/mvconnectors.png) denne fane kan du gå til det [forbindelse mellemrum objekt](#connector-space-object-properties)også.

## <a name="next-steps"></a>Næste trin
Lær mere om at [synkronisere Azure AD-forbindelse](active-directory-aadconnectsync-whatis.md) konfigurationen.

Lær mere om at [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).
