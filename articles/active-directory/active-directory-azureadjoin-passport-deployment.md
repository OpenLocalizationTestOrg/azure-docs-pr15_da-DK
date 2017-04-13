<properties
    pageTitle="Aktivere Microsoft Windows Hej til virksomheder i din organisation | Microsoft Azure"
    description="Installationsvejledning til at aktivere Microsoft Passport i din organisation."
    services="active-directory"
    documentationCenter=""
    keywords="konfigurere Microsoft Passport, Microsoft Windows Hej til Business-installation"
    authors="markusvi"
    manager="femila"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="markvi"/>


# <a name="enable-microsoft-windows-hello-for-business-in-your-organization"></a>Aktivere Microsoft Windows Hej til virksomheder i din organisation

Når du [opretter forbindelse til Windows 10 medlem af et domæne enheder med Azure Active Directory](active-directory-azureadjoin-devices-group-policy.md), skal du gøre følgende for at aktivere Microsoft Windows Hej til virksomheder i din organisation:

1. Installere System Center Configuration Manager  

2. Konfigurere indstillinger for politik

3. Konfigurere profilen certifikat  




## <a name="deploy-system-center-configuration-manager"></a>Installere System Center Configuration Manager 

Hvis du vil installere brugercertifikater, der er baseret på Windows Hej til Business taster, skal du følgende:

- **System Center Konfigurationsstyring aktuelle gren** - skal du installere version 1606 eller bedre. Se [dokumentationen til System Center Configuration Manager](https://technet.microsoft.com/library/mt346023.aspx) og [System Center Configuration Manager-teamets Blog](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx)kan finde flere oplysninger.
- **Offentlig nøgle infrastruktur** – til at aktivere Microsoft Windows Hej til virksomheder ved hjælp af certifikater for brugere, du skal have en PKI på plads. Hvis du ikke har en, eller du ikke vil bruge den til certifikater for brugere, kan du installere en ny domænenavn fra domænecontrolleren, der indeholder Windows Server 2016 build 10551 (eller bedre) installeret. Følg trinnene til at [installere en replikeringsdomænecontroller i et eksisterende domæne](https://technet.microsoft.com/library/jj574134.aspx) eller [installere en ny Active Directory-område, hvis du opretter et nyt miljø](https://technet.microsoft.com/library/jj574166). (ISOs er tilgængelig til hentning på [Signiant medier Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true)).


## <a name="configure-policy-settings"></a>Konfigurere indstillinger for politik

Hvis du vil konfigurere på Microsoft Windows Hej til Business politikindstillinger, har du to muligheder:

- Gruppepolitik i Active Directory 
- System Center Configuration Manager 


Ved brug af gruppepolitik i Active Directory er det anbefalede metode til at konfigurere Microsoft Windows Hej til Business politikindstillinger. 



Brug af System Center Configuration Manager er den foretrukne metode, når du også bruge den til at udrulle certifikater. Dette scenarie:

- Sikrer kompatibilitet med de nyere installationsscenarier
- Kræver på klientsiden Windows 10 Version 1607 eller bedre.

### <a name="configure-microsoft-windows-hello-for-business-via-group-policy-in-active-directory"></a>Konfigurere Microsoft Windows Hej for Business via gruppepolitik i Active Directory

 
**Trin**:

1.  Åbn Server Manager, og gå til **værktøjer** > **Administration af gruppepolitik**.
2.  Gå til domænenoden, der svarer til det domæne, hvor du vil aktivere Azure AD deltage fra administration af gruppepolitik.
3.  Højreklik på **Gruppepolitikobjekter**, og klik på **Ny**. Navngive din objekt til gruppepolitik, for eksempel aktivere Windows Hej til virksomheder. Klik på **OK**.
4.  Højreklik på dit nye objekt til gruppepolitik, og vælg derefter **Rediger**.
5.  Gå til **Computerkonfiguration** > **politikker** > **Administrative skabeloner** > **Windows-komponenter** > **Windows Hej til virksomheder**.
6.  Højreklik **Aktivere Windows Hej til virksomheder**, og vælg derefter **Rediger**.
7.  Vælg knappen **aktiveret** , og klik derefter på **Anvend**. Klik på **OK**.
8.  Nu kan du sammenkæde politik gruppeobjektet til en placering efter eget valg. For at aktivere denne politik for alle Windows 10-enheder for medlem af et domæne i din organisation skal oprette et link gruppepolitikken til domænet. Eksempel:
 - En bestemt organisationsenhed (OU) i Active Directory, hvor Windows 10 medlem af et domæne computere skal placeres
 - En bestemt sikkerhedsgruppe, der indeholder Windows 10 medlem af et domæne computere, der skal registreres automatisk med Azure AD


### <a name="configure-windows-hello-for-business-using-system-center-configuration-manager"></a>Konfigurere Windows Hej til virksomheder ved hjælp af System Center Configuration Manager


**Trin**:


1. Åbn **System Center Configuration Manager**, og gå derefter til **Aktiver og overholdelse af regler > Indstillinger for overholdelse af regler > virksomhed ressource Access > Windows Hej til Business profiler**.

    ![Konfigurere Windows Hej til virksomheder](./media/active-directory-azureadjoin-passport-deployment/01.png)


2. På værktøjslinjen øverst, skal du klikke på **Opret Windows Hej til virksomheder profil**.

    ![Konfigurere Windows Hej til virksomheder](./media/active-directory-azureadjoin-passport-deployment/02.png)

2. I dialogboksen **Generelt** skal du udføre følgende trin:

    ![Konfigurere Windows Hej til virksomheder](./media/active-directory-azureadjoin-passport-deployment/03.png)

    en. I tekstfeltet **navn** skal du skrive et navn til din profil, for eksempel **Min WHfB profil**.

    b. Klik på **Næste**.


2. **Understøttede platforme til** dialogboksen, Vælg de platforme, der vil blive klargjort med denne Windows Hej til business profil, og klik derefter på **Næste**.

    ![Konfigurere Windows Hej til virksomheder](./media/active-directory-azureadjoin-passport-deployment/04.png)


2. I dialogboksen **Indstillinger** skal du udføre følgende trin:

    ![Konfigurere Windows Hej til virksomheder](./media/active-directory-azureadjoin-passport-deployment/05.png)

    en. Vælg **aktiveret**som **Konfigurere Windows Hej til virksomheder**.

    b. **Bruge en modul (Trusted Platform)**, vælge **påkrævet**. 

    c. Vælg **certifikat-baserede**som **godkendelsesmetode**.

    d. Klik på **Næste**.



2. Dialogboksen **Oversigt** , klik på **Næste**.

2. Klik på **Luk**dialogboksen **fuldførelse** .


2. Klik på **Implementer**på værktøjslinjen øverst.

    ![Konfigurere Windows Hej til virksomheder](./media/active-directory-azureadjoin-passport-deployment/06.png)



## <a name="configure-the-certificate-profile"></a>Konfigurere profilen certifikat 

Hvis du bruger certifikat-baseret godkendelse til lokale godkendelse, skal du konfigurere og installere en Certifikatprofil. Denne opgave skal du konfigurere en NDES server og certifikat registrering punkt websted rolle i System Center Configuration Manager. Se [forudsætninger for certifikat profiler i Konfigurationsstyring til](https://technet.microsoft.com/library/dn261205.aspx)få mere at vide.

1. Åbn **System Center Configuration Manager**, og gå derefter til **Aktiver og overholdelse af regler > Indstillinger for overholdelse af regler > virksomhed ressource Access > certifikat profiler**.


2. Vælg en skabelon, der indeholder chipkort logon udvidede vigtige brugen (EKU).

På siden **SCEP tilmelding** af certifikat profilen skal du vælge **Installer for at Passport for det arbejde, ellers mislykkes** som **Nøgle Storage Provider**.



## <a name="next-steps"></a>Næste trin
* [Windows 10 for virksomheden: måder at bruge enheder for arbejde](active-directory-azureadjoin-windows10-devices-overview.md)
* [Hvis du vil udvide skyen funktioner til Windows 10-enheder via Azure Active Directory deltage](active-directory-azureadjoin-user-upgrade.md)
* [Godkendelse af identiteter uden adgangskoder via Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Få mere at vide om brugsscenarier til Azure AD deltage](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Tilslutte enheder, der er medlem af et domæne til Azure AD til Windows 10 oplevelser](active-directory-azureadjoin-devices-group-policy.md)
* [Konfigurere Azure AD deltage](active-directory-azureadjoin-setup.md)
