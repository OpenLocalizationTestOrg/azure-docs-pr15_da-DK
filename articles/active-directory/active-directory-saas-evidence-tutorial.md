<properties
    pageTitle="Selvstudium: Azure Active Directory-integration med Evidence.com | Microsoft Azure"
    description="Lær at konfigurere single sign-on mellem Azure Active Directory og Evidence.com."
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
    ms.date="02/23/2016"
    ms.author="asmalser"/>


# <a name="tutorial-azure-active-directory-integration-with-evidencecom"></a>Selvstudium: Azure Active Directory-integration med Evidence.com

Formålet med dette selvstudium er at se, hvordan du konfigurerer enkeltlogon mellem Azure Active Directory (AAD) og Evidence.com. Dette scenario, der er beskrevet i dette selvstudium antager, at du allerede har følgende elementer:
    
* Et gyldigt Microsoft Azure-abonnement
* Et Evidence.com abonnement med single sign-On aktiveret (mail earlyaccess@evidence.com Hvis SAML-baseret single sign-on ikke er aktiveret)

Når du har fuldført dette selvstudium, vil de AAD brugere, som du har tildelt Evidence.com access kunne enkelt Log på ved hjælp af panelet AAD Access programmet.

## <a name="add-evidencecom-to-your-directory"></a>Tilføje Evidence.com på adresselisten

Dette afsnit beskrives, hvordan du tilføjer Evidence.com som et integreret program i Azure Active Directory.

**Sådan aktiveres programmet integration af dokumentation:**

1.  Klik på **Active Directory**i [Azure klassisk portal](https://manage.windowsazure.com), i den venstre navigationsrude.

2.  Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3.  For at åbne visningen programmer i visningen directory, skal du klikke på **programmer** i den øverste menu.

4.  Klik på **Tilføj**for at åbne galleriet programmet, og klik derefter på **Tilføj et program i galleriet**.

5.  Skriv **Evidence.com**i søgefeltet.

6.  Vælg **Evidence.com**i resultatruden, og klik derefter på **udført** for at tilføje programmet.


## <a name="configuring-single-sign-on"></a>Konfigurere Enkeltlogon

Dette afsnit beskrives, hvordan du aktiverer brugere til at godkende Evidence.com med deres konto i Azure Active Directory, ved hjælp af sammenslutning, der er baseret på SAML-protokollen.

**For at konfigurere Enkeltlogon skal du udføre følgende trin:**

1.  Når du har tilføjet Evidence.com i portalen Azure klassisk, skal du klikke på **Konfigurere Single Sign-On**. 
 
2.  Vælg **Azure AD Single Sign-On**på det næste skærmbillede, og klik derefter på **Næste**.

3.  På skærmbilledet Konfigurer App URL-adressen skal angive URL-adressen, hvor brugerne kan logge på din Evidence.com lejer URL-adresse (eksempel: https://yourtenant.evidence.com og klik derefter på **Næste**. 

4.  Klik på linket **Downloade certifikatet** , og Gem den til dit lokale drev. Dette certifikat og metadata URL-adresserne (enheds-ID, SSO log i URL-adresse og log af URL-adressen) bruges til at konfigurere SSO på Evidence.com websted. 

5.  I et separat browservindue, logge på din Evidence.com lejer som administrator, og gå til fanen **Administration**
      
6.  Klik på **kontoret Single Sign-On**
 
7.  Vælg **SAML baseret Single Sign på**
 
8.  Kopiere **Udsteder URL-adresse**, **Single Sign-On** og **Enkelt log af** værdier, der vises i Azure klassisk portalen og til de tilsvarende felter i Evidence.com.

9.  Åbn certifikatet, der har hentet i trin 4 ved hjælp af en teksteditor som Notepad.exe, og kopiere og indsætte indholdet i feltet **Sikkerhedscertifikat** . 

10. Gemme konfigurationen i Evidence.com.
 
11. Azure klassisk portalen, Markér **Bekræft, at du har konfigureret single sign-on – som beskrevet herover**. Kontrollere dette gør det aktuelle certifikatet for at begynde at arbejde for dette program afkrydsningsfelt.
 
12. Enkelt sign-on – bekræftelsessiden, klik på **udført**.  


## <a name="creating-an-evidencecom-test-user"></a>Oprette en Evidence.com testbruger

Azure AD-brugere skal kunne logge på, skal de klargøres for adgang i programmet Evidence.com. I dette afsnit beskrives, hvordan du opretter Azure AD-brugerkonti i Evidence.com

**Til at klargøre en brugerkonto i Evidence.com:**

1.  Log på webstedet Evidence.com virksomhed som administrator i et browservindue.

2.  Gå til fanen **Administration** .

3.  Klik på **Tilføj bruger**.

4.  Klik på knappen **Tilføj** .

5.  **E-mailadresse** på den nye bruger tilføjet skal svare til brugernavnet for brugerne på Azure AD, som du ønsker at give adgang. Hvis brugernavn og mailadressen ikke er den samme værdi i din organisation, kan du bruge den **Evidence.com > Egenskaber > Single Sign-On** sektion af Azure klassisk portalen for at ændre nameidenitifer sendes til Evidence.com skal være mailadressen.


## <a name="assigning-users-to-evidencecom"></a>Tildele brugere til Evidence.com

For klargjort AAD brugere skal kunne se Evidence.com på deres Access Panel, skal de være tildelt adgang i portalen Azure klassisk.

**Tildele brugere til Evidence.com:**

1.  Klik på hurtig startsiden for Evidence.com i portalen Azure klassisk skal **tildele brugere til Evidence.com**.
 
2.  Vælg, om du vil tildele en bruger eller en gruppe til Evidence.com, og klik på knappen markering i menuen **Vis** .
 
3.  Vælg brugerne gruppere, til hvem du vil tildele Evidence.com på listen **brugere** .
 
4.  I sidefoden, skal du klikke på knappen **tildele** .

