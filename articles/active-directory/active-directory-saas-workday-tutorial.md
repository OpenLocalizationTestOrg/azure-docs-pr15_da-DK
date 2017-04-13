<properties 
    pageTitle="Selvstudium: Azure Active Directory-integration med arbejdsdag | Microsoft Azure" 
    description="Lær at bruge arbejdsdag med Azure Active Directory til at aktivere enkeltlogon, automatiseret klargøring og mere!." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="09/09/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-workday"></a>Selvstudium: Azure Active Directory-integration med arbejdsdag
  
Formålet med dette selvstudium er at vise integration af Azure og arbejdsdag. Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:

-   Et gyldigt Azure-abonnement
-   En lejer i arbejdsdag
  
Dette scenario, der er beskrevet i dette selvstudium består af følgende grundpillerne:

1.  Aktivere programmet integrationen for arbejdsdag
2.  Konfigurere Enkeltlogon
3.  Konfiguration af brugerklargøring af
4.  Konfiguration af brugerklargøring af

![Scenarie] (./media/active-directory-saas-workday-tutorial/IC782919.png "Scenarie")

##<a name="enabling-the-application-integration-for-workday"></a>Aktivere programmet integrationen for arbejdsdag
  
Formålet med dette afsnit er at oprette en disposition for at aktivere programmet integration til Salesforce.

###<a name="to-enable-the-application-integration-for-workday-perform-the-following-steps"></a>For at aktivere programmet integration for arbejdsdag skal du udføre følgende trin:

1.  Klik på **Active Directory**i Azure klassisk-portalen på den venstre navigationsrude.

    ![Active Directory] (./media/active-directory-saas-workday-tutorial/IC700993.png "Active Directory")

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

    ![Programmer] (./media/active-directory-saas-workday-tutorial/IC700994.png "Programmer")

4.  Klik på **Tilføj en App**for at åbne **Programmet galleriet**, og klik derefter på **Tilføj et program for organisationen at bruge**.

    ![Hvad vil du gøre?] (./media/active-directory-saas-workday-tutorial/IC700995.png "Hvad vil du gøre?")

5.  I **søgefeltet**skal du skrive **arbejdsdag**.

    ![Arbejdsdag] (./media/active-directory-saas-workday-tutorial/IC701021.png "Arbejdsdag")

6.  Vælg **arbejdsdag**i resultatruden, og klik derefter på **udført** for at tilføje programmet.

    ![Arbejdsdag] (./media/active-directory-saas-workday-tutorial/IC701022.png "Arbejdsdag")

##<a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon
  
Formålet med dette afsnit er til at disponere hvordan du aktiverer brugere til at godkende arbejdsdag med deres konto i Azure AD ved hjælp af sammenslutning, der er baseret på SAML-protokollen.  
Som en del af denne procedure er du påkrævet for at oprette et base-64-kodet certifikat.  
Hvis du ikke kender med denne procedure, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>For at konfigurere Enkeltlogon skal du udføre følgende trin:

1.  På siden **arbejdsdag** application integration, klik på **Konfigurer single sign-on –** for at åbne dialogboksen **Konfigurer Single Sign-On** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-workday-tutorial/IC782920.png "Konfigurere Enkeltlogon")

2.  Vælg **Microsoft Azure AD Single Sign-On**, og klik derefter på **Næste**på siden **hvordan vil du brugere til at logge arbejdsdag** .

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-workday-tutorial/IC782921.png "Konfigurere Enkeltlogon")

3.  Udfør følgende trin på siden **Konfigurer App URL-adressen** , og klik derefter på **Næste**.

    ![Konfigurere App URL-adresse] (./media/active-directory-saas-workday-tutorial/IC782957.png "Konfigurere App URL-adresse")

    en. I tekstfeltet **Log på URL-adresse** skal du skrive URL-adressen bruges af dine brugere til at logge på ved hjælp af følgende mønster arbejdsdag:`https://impl.workday.com/<tenant>/login-saml2.htmld`

    b.  I tekstfeltet **Arbejdsdag svar URL-adresse** skal du skrive arbejdsdag svar URL-adressen ved hjælp af følgende mønster:`https://impl.workday.com/<tenant>/login-saml.htmld`

    >[AZURE.NOTE]Dine svar URL-adressen skal have et underdomæne (f.eks.: www, wd2, wd3, wd3 impl, wd5, wd5 impl). 
    >Ved hjælp af noget som "*http://www.myworkday.com*" fungerer, men "*http://myworkday.com*" ikke. 
 
4.  Klik på **Hent certifikat**for at hente dit certifikat, på siden **Konfigurer single sign-on – på arbejdsdag** , og Gem derefter certifikatfilen på din computer.

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-workday-tutorial/IC782922.png "Konfigurere Enkeltlogon")

5.  Log på din arbejdsdag virksomheds websted som en administrator i et andet browservindue.

6.  Gå til **menuen \> Workbench**.

    ![Workbench] (./media/active-directory-saas-workday-tutorial/IC782923.png "Workbench")

7.  Gå til **Kontoadministration af**.

    ![Kontoadministration] (./media/active-directory-saas-workday-tutorial/IC782924.png "Kontoadministration")

8.  Gå til at **redigere lejer opsætning – sikkerhed**.

    ![Redigere lejer sikkerhed] (./media/active-directory-saas-workday-tutorial/IC782925.png "Redigere lejer sikkerhed")

9.  I sektionen **Omdirigering URL-adresser** skal du udføre følgende trin:

    ![Omdirigering af URL-adresser] (./media/active-directory-saas-workday-tutorial/IC7829581.png "Omdirigering af URL-adresser")

    en. Klik på **Tilføj række**.

    b. Skriv **Arbejdsdag lejer URL-adresse** , du har angivet på siden **Konfigurer App URL-adressen** i portalen Azure klassisk i tekstfeltet **Login omdirigere URL** og tekstfeltet **Mobile omdirigere URL** .
    
    c. Kopiere **Enkelt Sign-Out URL-adressen**på Azure-klassisk portalen på **Konfigurer single sign-on – på arbejdsdag** dialogboksen siden, og Indsæt det i tekstfeltet **Log omdirigere URL** .

    d.  Skriv navnet miljø i **miljø** tekstfelt.  


    >[AZURE.NOTE] Værdien for attributten miljø er bundet til værdien af lejer URL-adressen:
    >
    >-   Hvis domænenavnet for lejeren arbejdsdag URL-adressen starter med impl (f.eks.: *https://impl.workday.com/\<lejer\>/login-saml2.htmld*), attributten **miljø** skal være angivet til implementering.
    >-   Hvis navnet på det domæne, der starter med noget andet, skal du kontakte arbejdsdag for at få den tilsvarende **miljø** værdi.

10. I sektionen **SAML konfiguration** skal du udføre følgende trin:

    ![SAML konfiguration] (./media/active-directory-saas-workday-tutorial/IC782926.png "SAML konfiguration")

    en.  Vælg **Aktiver SAML godkendelse**.

    b.  Klik på **Tilføj række**.

11. I sektionen SAML identitetsudbydere skal du udføre følgende trin:

    ![SAML identitetsudbydere] (./media/active-directory-saas-workday-tutorial/IC7829271.png "SAML identitetsudbydere")

    en. Skriv et udbydernavn i tekstfeltet identitet udbydernavn (f.eks.: *SPInitiatedSSO*).

    b. Kopiér værdien **Identitet Provider-ID** i Azure klassisk-portalen på **Konfigurer single sign-on – på arbejdsdag** dialogboksen siden, og Indsæt det i tekstfeltet **udsteder** .

    c. Vælg **Aktiver arbejdsdag Initialted log**.

    d. Kopiér værdien **Enkelt Sign-Out URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på arbejdsdag** dialogboksen siden, og Indsæt det i tekstfeltet **Log anmode om URL** .


    e. Klik på **Identitet udbyder offentlig nøglecertifikat**, og klik derefter på **Opret**. 

    ![Oprette] (./media/active-directory-saas-workday-tutorial/IC782928.png "Oprette")

    f. Klik på **oprette x509 offentlig nøgle**. 
        
    ![Oprette] (./media/active-directory-saas-workday-tutorial/IC782929.png "Oprette")


1. I sektionen **Vis x509 offentlig nøgle** skal du udføre følgende trin: 

    ![Vis x509 offentlig nøgle] (./media/active-directory-saas-workday-tutorial/IC782930.png "Vis x509 offentlig nøgle") 

    en. Skriv et navn til certifikatet i tekstfeltet **navn** (f.eks.: *PV\_SP*).
        
    b. Skriv den gyldige, fra attributværdien af dit certifikat i tekstfeltet **Gyldig fra** .
    
    c.  Skriv den gyldige til attributværdien af dit certifikat i tekstfeltet **Gyldige til** .
        
    >[AZURE.NOTE] Du kan få den gyldige fra dato og det gyldig for til-dato fra det hentede certifikat ved at dobbeltklikke på den. Datoerne, der er angivet under fanen **Detaljer** .

    d. Oprette en **Base-64-kodet** fil fra dit hentede certifikat.  

    >[AZURE.TIP] Du kan finde flere oplysninger, se, [hvordan du konverterer et binært certifikat til en tekstfil](http://youtu.be/PlgrzUZ-Y1o)

    e.  Åbn dit base-64-kodet certifikat i Notesblok, og derefter kopiere indholdet af den.
    
    f.  Indsætte indholdet af Udklipsholder i tekstfeltet **certifikat** .
    
    g.  Klik på **OK**.

12.  Udfør følgende trin: 

    ![SSO-konfiguration] (./media/active-directory-saas-workday-tutorial/IC7829351111.png "SSO-konfiguration")

    en.  Aktivere den **x509 privat nøgle par**.

    b.  Skriv **http://www.workday.com**i tekstfeltet **Service Provider-ID** .

    c.  Vælg **Aktivér startet SP SAML godkendelse**.

    d.  Kopiér værdien **Enkelt Sign-On URL-adressen** på Azure-klassisk portalen på **Konfigurer single sign-on – på arbejdsdag** dialogboksen siden, og Indsæt det i tekstfeltet **IdP SSO URL-adressen** .
     
    e. Vælg **ikke Deflate anmodningen om SP-definerede godkendelse**.

    f. Vælg **SHA256**som **Anmode om signatur godkendelsesmetode**. 
        
    ![Godkendelse anmode om signatur metode] (./media/active-directory-saas-workday-tutorial/IC782932.png "Godkendelse anmode om signatur metode") 
 
    g. Klik på **OK**. 
        
    ![OK] (./media/active-directory-saas-workday-tutorial/IC782933.png "OK")

12. Klik på **Næste**i Azure klassisk portalen, på siden **Konfigurer single sign-on – på arbejdsdag** . 

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-workday-tutorial/IC782934.png "Konfigurere Enkeltlogon")

13. **Enkelt sign-on –** bekræftelsessiden, klik på **udført**. 

    ![Konfigurere Enkeltlogon] (./media/active-directory-saas-workday-tutorial/IC782935111.png "Konfigurere Enkeltlogon")



##<a name="configuring-user-provisioning"></a>Konfiguration af brugerklargøring af
  
For at få en testbruger, der er klargjort i arbejdsdag, skal du kontakte supportteam arbejdsdag.  
Supportteam arbejdsdag opretter brugeren for dig.

##<a name="assigning-users"></a>Tildele brugere
  
Hvis du vil teste din konfiguration, skal du give Azure AD-brugere, du vil tillade brug af dit programadgang til den ved at tildele dem.

###<a name="to-assign-users-to-workday-perform-the-following-steps"></a>Hvis du vil tildele brugere til arbejdsdag, skal du udføre følgende trin:

1.  Oprette en testkonto i Azure klassisk portalen.

2.  Klik på **Tildel brugere**på siden **arbejdsdag **application integration.

    ![Tildele brugere] (./media/active-directory-saas-workday-tutorial/IC782935.png "Tildele brugere")

3.  Vælg din testbruger, klik på **Tildel**, og klik derefter på **Ja** for at bekræfte din tildeling.

    ![Ja] (./media/active-directory-saas-workday-tutorial/IC767830.png "Ja")
  
Hvis du vil teste enkelt sign-on – indstillingerne, skal du åbne panelet adgang. Se [Introduktion til panelet adgang](active-directory-saas-access-panel-introduction.md)kan finde flere oplysninger om panelet adgang.