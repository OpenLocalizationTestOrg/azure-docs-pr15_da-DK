<properties
    pageTitle="Azure AD-forbindelse synkronisering: køre installationsguiden endnu en gang | Microsoft Azure"
    description="Forklarer, hvordan installationsguiden virker andet gang, du kører den."
    keywords="Installationsguiden af Azure AD-forbindelse kan du konfigurere vedligeholdelsesindstillinger for anden gang du kører den"
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
    ms.date="08/31/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Azure AD-forbindelse synkronisering: køre installationsguiden endnu en gang
Første gang du kører guiden til installation af Azure AD-forbindelse, vejleder den dig gennem hvordan du konfigurerer din installation. Hvis du kører installationsguiden igen, indeholder indstillinger for vedligeholdelse.

Du kan finde installationsguiden af i startmenuen med navnet **Azure AD-forbindelse**.

![Menuen Start](./media/active-directory-aadconnectsync-installation-wizard/startmenu.png)

Når du starter installationsguiden, kan du se en side med disse indstillinger:

![Side med en liste over flere opgaver](./media/active-directory-aadconnectsync-installation-wizard/additionaltasks.png)

Hvis du har installeret ADFS med Azure AD-forbindelse, har du endnu flere muligheder. De yderligere indstillinger, du har til ADFS er beskrevet i [ADFS administration](active-directory-aadconnect-federation-management.md#ad-fs-management).

Vælg en af opgaverne, og klik på **Næste** at fortsætte.

> [AZURE.IMPORTANT] Mens du har åbne installationsguiden, alle handlinger i Synkroniser engine afbrydes midlertidigt. Kontrollér, at du lukker installationsguiden, så snart du har fuldført dine ændringer i konfigurationen.

## <a name="view-current-configuration"></a>Få vist aktuelle konfiguration
Denne indstilling giver dig et hurtigt overblik over indstillingerne er konfigureret i øjeblikket.

![Side med en liste over alle indstillinger og deres tilstand](./media/active-directory-aadconnectsync-installation-wizard/viewconfig.png)

Klik på **forrige** for at gå tilbage. Hvis du vælger **Afslut**, kan du lukke installationsguiden.

## <a name="customize-synchronization-options"></a>Tilpasse indstillinger for synkronisering
Denne indstilling bruges til at foretage ændringer i konfigurationen af synkronisering. Du kan se et undersæt af indstillinger fra installationssti brugerdefineret konfiguration. Du får vist denne indstilling, selvom du oprindeligt brugte hurtig installation.

- [Tilføje flere mapper](active-directory-aadconnect-get-started-custom.md#connect-your-directories). Se [slette en forbindelse](active-directory-aadconnectsync-service-manager-ui-connectors.md#delete)til at fjerne en mappe.
- [Skift domæne og OU filtrering](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering).
- Fjerne gruppen filtrering.
- [Ændre valgfrie funktioner](active-directory-aadconnect-get-started-custom.md#optional-features).

De andre indstillinger fra den indledende installation kan ikke ændres, og er ikke tilgængelige. Disse indstillinger er:

- Ændre egenskaben skal bruges til userPrincipalName og sourceAnchor.
- Ændre metoden tilslutter for objekter fra anden skov.
- Aktiver, gruppe-baseret filtrering.

## <a name="refresh-directory-schema"></a>Opdater directory skema
Denne indstilling bruges, hvis du har ændret i skemaet i en af dine lokale Active Directory-Domænetjenester områder. For eksempel du muligvis har installeret Exchange eller opgraderet til et Windows Server 2012-skema med enhedsobjekter. I dette tilfælde skal du bede Azure AD-forbindelse til at læse skemaet igen fra Active Directory-Domænetjenester og opdatere cachen. Denne handling genopretter også Synkroniser regler. Hvis du tilføjer skemaet Exchange, som et eksempel, tilføjes synkronisering af reglerne for Exchange af konfigurationen.

Når du vælger denne indstilling, vises alle mapper i konfigurationen. Du kan bevare standardindstillingen og opdatere alle områder eller fjerne markeringen af nogle af dem.

![Side med en liste over alle mapper i miljøet](./media/active-directory-aadconnectsync-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Konfigurere tilstanden for midlertidige
Denne indstilling kan du aktivere og deaktivere midlertidige tilstand på serveren. Kan finde flere oplysninger om midlertidige tilstand, og hvordan de bruges i [Handlinger](active-directory-aadconnectsync-operations.md#staging-mode).

Indstillingen viser hvis midlertidige er i øjeblikket er aktiveret eller deaktiveret:  
![Indstilling, der også viser den aktuelle tilstand for midlertidige tilstand](./media/active-directory-aadconnectsync-installation-wizard/stagingmodecurrentstate.png)

Hvis du vil ændre tilstanden, Vælg denne indstilling og markere eller fjerne markeringen i afkrydsningsfeltet.  
![Indstilling, der også viser den aktuelle tilstand for midlertidige tilstand](./media/active-directory-aadconnectsync-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Ændre bruger-logon
Denne indstilling kan du ændre fra synkronisering af adgangskoder til sammenslutning eller anden vej rundt. Du kan ikke ændre **ikke konfigurerer**.

Se [brugerlogon](active-directory-aadconnect-user-signin.md#changing-user-sign-in-method)kan finde flere oplysninger om denne indstilling.

## <a name="next-steps"></a>Næste trin

- Lær mere om af konfigurationsmodellen bruges ved synkronisering af Azure AD-forbindelse i [Forstå deklarativ klargøring](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

**Oversigt oversigtsemner**

- [Azure AD-forbindelse synkronisering: forstå og tilpasse synkronisering](active-directory-aadconnectsync-whatis.md)
- [Integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md)
