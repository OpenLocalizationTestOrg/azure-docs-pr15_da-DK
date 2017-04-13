<properties
    pageTitle="Sådan administreres sammenslutning certifikater i Azure AD | Microsoft Azure"
    description="Lær, hvordan du tilpasser udløbsdatoen for dine sammenslutning certifikater, og hvordan du kan forny certifikater, der snart udløber."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"
    ms.author="asmalser-msft"/>

#<a name="managing-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Administrere certifikater for organisationsnetværket Single Sign-On i Azure Active Directory

Denne artikel omhandler almindelige spørgsmål, der er relateret til certifikater, som opretter Azure Active Directory for at oprette organisationsnetværket enkeltlogon (SSO) til dine SaaS-programmer.

I denne artikel er kun relevant for apps, der er konfigureret til at bruge **Azure AD Single Sign-On**, som vist i eksemplet nedenfor:

![Azure AD-Single Sign-On](./media/active-directory-sso-certs/fed-sso.PNG)

##<a name="how-to-customize-the-expiration-date-for-your-federation-certificate"></a>Hvordan du tilpasser udløbsdatoen for dit organisationsnetværk certifikat

Certifikater er som standard konfigureret til at udløbe efter to år. Du kan vælge en anden udløbsdato for dit certifikat ved at følge trinnene nedenfor. De inkluderede skærmbilleder bruge Salesforce for eksempel, men disse trin kan anvende en samlet SaaS-App.

1. Klik på **Konfigurer single sign-on –**på Hurtig Start-siden for dit program i Azure Active Directory.

    ![Åbn konfigurationsguiden af SSO.](./media/active-directory-sso-certs/config-sso.png)

2. Vælg **Azure AD Single Sign-On**, og klik derefter på **Næste**.

3. Skriv i feltet **Sign-On URL-adressen** for dit program, og Markér afkrydsningsfeltet for at **konfigurere certifikatet, der bruges til organisationsnetværket single sign-on**. Klik derefter på **Næste**.

    ![Azure AD-Single Sign-On](./media/active-directory-sso-certs/new-app-config-sso.PNG)

4. Vælg **Opret et nyt certifikat**på den næste side, og vælg, hvor lang tid du vil have det certifikat, der skal gælde for. Klik derefter på **Næste**.

    ![Oprette et nyt certifikat](./media/active-directory-sso-certs/new-app-config-cert.PNG)

5. Klik derefter på **Hent certifikat**. Hvis du vil se, hvordan du overfører certifikatet til din bestemt SaaS-app, skal du klikke på **Vis konfigurationsvejledning**.

    ![Hent og derefter upload certifikatet](./media/active-directory-sso-certs/new-app-config-app.PNG)

6. Certifikatet, der være ikke aktiveret, indtil du markerer feltet bekræftelse nederst i dialogboksen, og tryk derefter på Send.

##<a name="how-to-renew-a-certificate-that-will-soon-expire"></a>Hvordan du kan forny et certifikat, der snart udløber

Fornyelse trinnene nedenfor bør ideelt medføre ikke længere nedetid for dine brugere. De skærmbilleder, der bruges i denne sektion funktion Salesforce som et eksempel, men disse trin kan anvende en samlet SaaS-App.

1. Azure Active Directory, på siden Hurtig Start for dit program, klik på i **Konfigurere Single Sign-On**.

    ![Åbn konfigurationsguiden af SSO](./media/active-directory-sso-certs/renew-sso-button.PNG)

2. På den første side i dialogboksen **Azure AD Single Sign-On** skal allerede være markeret, så klik på **Næste**.

3. Markér afkrydsningsfeltet for **Konfigurer certifikatet, der bruges til organisationsnetværket single sign-on**på den anden side. Klik derefter på **Næste**.

    ![Azure AD-Single Sign-On](./media/active-directory-sso-certs/renew-config-sso.PNG)

4. Vælg **Opret et nyt certifikat**på den næste side, og vælg, hvor lang tid du vil have det nye certifikat skal gælde for. Klik derefter på **Næste**.

    ![Oprette et nyt certifikat](./media/active-directory-sso-certs/new-app-config-cert.PNG)

5. Klik på **Hent certifikat**. Til korrekt rewnew certifikatet, skal du udføre følgende to trin:

    - Overføre det nye certifikat til appen SaaS enkelt sign-on – konfiguration skærm. Hvis du vil se, hvordan du gør dette for din bestemt SaaS-app, skal du klikke på **Vis konfigurationsvejledning**.

    - Markér afkrydsningsfeltet bekræftelse nederst i dialogboksen for at aktivere det nye certifikat i Azure AD, og klik derefter på **Næste** til at sende.

    > [AZURE.IMPORTANT] Single sign-on til appen deaktiveres øjeblik enten en af disse to trin er fuldført, men det vil aktiveret igen, når det andet trin er fuldført. Derfor for at minimere nedetid, skal du kan forberede dig til at udføre begge disse trin i en lang tid fra hinanden.

    ![Hent og derefter upload certifikatet](./media/active-directory-sso-certs/renew-config-app.PNG)

## <a name="related-articles"></a>Relaterede artikler

- [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)
- [Adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md)
- [Fejlfinding i forbindelse med SAML-baseret Single Sign-On](active-directory-saml-debugging.md)
