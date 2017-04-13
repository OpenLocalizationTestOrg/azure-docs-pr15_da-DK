<properties
    pageTitle="Sådan installeres Access Kontrolpanel-udvidelse til Internet Explorer ved hjælp af gruppepolitik | Microsoft Azure"
    description="Sådan bruger Gruppepolitik til at installere tilføjelsesprogrammet Internet Explorer til portalen mine Apps."
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>
<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/16/2016"
    ms.author="markvi"/>

#<a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Sådan installeres Access Kontrolpanel-udvidelse til Internet Explorer ved hjælp af gruppepolitik

Dette selvstudium viser, hvordan du bruger Gruppepolitik til at installere fra en fjernplacering filtypenavnet Access Panel til Internet Explorer på dine brugeres computere. Dette lokalnummer er påkrævet for Internet Explorer-brugere, der har brug for at logge på apps, der er konfigureret ved hjælp af [adgangskode-baserede enkeltlogon](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

Det anbefales, at administratorer automatisere installationen af denne udvidelse. Ellers får brugere at hente og installere filtypenavnet selv, som er giver risiko for brugerfejl, der kræver administratorrettigheder. Dette selvstudium viser en metode til at automatisere softwareinstallationer ved hjælp af gruppepolitik. [Lær mere om Gruppepolitik.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

Filtypenavnet Access Panel er også tilgængelig for [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) og [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), som ikke kræver administratorrettigheder for at installere.

##<a name="prerequisites"></a>Forudsætninger

- Du har konfigureret [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), og du har tilsluttet sig dine brugeres computere til dit domæne.
- Du skal have tilladelsen "Rediger indstillinger" for at redigere gruppepolitikobjekter (GPO'er). Som standard medlemmer af følgende sikkerhedsgrupper have denne tilladelse: domæneadministratorer, administratorer for virksomhed og Gruppeejere af Creator. [Lær mere.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

##<a name="step-1-create-the-distribution-point"></a>Trin 1: Oprette punktet fordeling

Først skal du markere installationsprogrammet på en netværksplacering, der kan åbnes fra alle de computere, du vil installere fra en fjernplacering filtypenavnet på. For at gøre dette, skal du følge disse trin:

1. Logge serveren som administrator

2. I vinduet **Server Manager** skal du gå til **filer og lagerplads tjenester**.

    ![Åbne filer og lagerplads tjenester](./media/active-directory-saas-ie-group-policy/files-services.png)

3. Gå til fanen **aktier** . Klik på **opgaver** > **Nyt Share...**

    ![Åbne filer og lagerplads tjenester](./media/active-directory-saas-ie-group-policy/shares.png)

4. Fuldføre **Guiden Ny del** , og Angiv tilladelser til at sikre, at det kan åbnes fra dine brugeres computere. [Lær mere om aktier.](https://technet.microsoft.com/library/cc753175.aspx)

5. Hente følgende Microsoft Windows Installer-pakke (.msi-fil): [Access Panel Extension.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access Panel Extension.msi)

6. Kopiere installationsprogrammet til den ønskede placering på dialogboksen del.

    ![Kopiér .msi-filen til dig er dele.](./media/active-directory-saas-ie-group-policy/copy-package.png)

8. Kontrollér, at din klientcomputere kan få adgang til installationsprogrammet fra den delte mappe. 

##<a name="step-2-create-the-group-policy-object"></a>Trin 2: Oprette politik gruppeobjektet

1. Log på den server, hvor installationen af Active Directory-domænetjenester (AD DS).

2. I Server Manager skal du gå til **Funktioner** > **Administration af gruppepolitik**.

    ![Gå til Funktioner > Gruppér politik Management](./media/active-directory-saas-ie-group-policy/tools-gpm.png)

3. Få vist det organisatoriske enhed (OU) hierarki i venstre rude i vinduet **Administration af gruppepolitik** , og finde ud af, i hvilket omfang du gerne vil anvende gruppepolitikken. For eksempel, du vil vælge en lille OU skal installeres til nogle få brugere til test, eller du kan vælge en på øverste niveau OU skal installeres til hele organisationen.

    > [AZURE.NOTE] Hvis du vil oprette eller redigere din organisation-enheder (afdelinger), skifte tilbage til Server Manager og gå til **Funktioner** > **Active Directory-brugere og computere**.

4. Når du har valgt en OU, højreklik på den, og vælg **Opret et Gruppepolitikobjekt i dette domæne, og knyt det hertil...**

    ![Oprette et nyt Gruppepolitikobjekt](./media/active-directory-saas-ie-group-policy/create-gpo.png)

5. Skriv et navn til den nye objekt til gruppepolitik i prompten **Nye GPO** .

    ![Navngive det nye GPO](./media/active-directory-saas-ie-group-policy/name-gpo.png)

6. Højreklik på det objekt til gruppepolitik, som du lige har oprettet, og klik på **Rediger**.

    ![Rediger det nye GPO](./media/active-directory-saas-ie-group-policy/edit-gpo.png)

##<a name="step-3-assign-the-installation-package"></a>Trin 3: Tildele installationspakken

1. Find ud af, om du vil gerne installere filtypenavnet baseret på **Computerkonfiguration** eller **Brugerkonfiguration**. Når du bruger [computerkonfigurationen](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx), installeres filtypenavnet på computeren, uanset hvilken brugerne skal logge på den. Med en [Brugerkonfiguration](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx)kan har brugere på den anden side filtypenavnet installeret efter dem uanset hvilke computere de logger på.

2. Gå til en af følgende mappesti, afhængigt af hvilken type af konfigurationen af du vælger i venstre rude i vinduet **Redigeringsprogrammet til gruppepolitik administration** :
    - `Computer Configuration/Policies/Software Settings/`
    - `User Configuration/Policies/Software Settings/`

3. Højreklik på **installation af Software**, og derefter vælge **Ny** > **pakke...**

    ![Oprette en ny softwareinstallationspakke](./media/active-directory-saas-ie-group-policy/new-package.png)

4. Gå til den delte mappe, der indeholder installationsprogrammet fra [trin 1: oprette fordeling punkt](#step-1-create-the-distribution-point), Vælg .msi-filen, og klik på **Åbn**.

    > [AZURE.IMPORTANT] Hvis del er placeret på denne samme server, kan du kontrollere, at du har adgang til .msi-filen gennem filstien netværk i stedet for lokale filstien.

    ![Vælg installationspakken fra den delte mappe.](./media/active-directory-saas-ie-group-policy/select-package.png)

5. I **Installer Software** bliver spurgt, skal du vælge **tildelt** til din installationsmetode. Klik derefter på **OK**.

    ![Vælg tildelt, og klik derefter på OK.](./media/active-directory-saas-ie-group-policy/deployment-method.png)

Filtypenavnet er nu installeret til den OU, du har valgt. [Lær mere om Gruppepolitik.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

##<a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Trin 4: Automatisk aktiverer udvidelse til Internet Explorer 

Ud over kører installationsprogrammet, skal hver udvidelse til Internet Explorer være aktiveret før den kan bruges. Følg nedenstående trin for at aktivere filtypenavnet Access Panel, ved hjælp af gruppepolitik:

1. Gå til en af følgende stier, afhængigt af hvilken type af konfigurationen af du vælger i i vinduet **Redigeringsprogrammet til Gruppepolitik Management** [trin 3: tildele installationspakken](#step-3-assign-the-installation-package):
    - `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
    - `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`

2. Højreklik på **Liste over tilføjelsesprogrammer**, og klik på **Rediger**.
    ![Redigere liste over tilføjelsesprogrammer.](./media/active-directory-saas-ie-group-policy/edit-add-on-list.png)

3. Vælg **aktiveret**i vinduet **Liste over tilføjelsesprogrammer** . Klik på **Vis …**under afsnittet **Indstillinger** .

    ![Klik på Aktivér, og klik derefter på Vis...](./media/active-directory-saas-ie-group-policy/edit-add-on-list-window.png)

4. I vinduet **Vis indhold** skal du udføre følgende trin:

    1. For den første kolonne (feltet **Værdinavn** ), kopiere og indsætte følgende klasse-ID:`{030E9A3F-7B18-4122-9A60-B87235E4F59E}`

    2. Skriv for den anden kolonne **(værdifeltet)** , i følgende værdi:`1`

    3. Klik på **OK** for at lukke vinduet **Vis indhold** .

    ![Udfylde værdier som angivet ovenfor.](./media/active-directory-saas-ie-group-policy/show-contents.png)

5. Klik på **OK** for at anvende ændringerne og lukke vinduet **Liste over tilføjelsesprogrammer** .

Filtypenavnet skulle nu være aktiveret for computere i den valgte OU. [Lær mere om brug af gruppepolitik til at aktivere eller deaktivere tilføjelsesprogrammer i Internet Explorer.](https://technet.microsoft.com/library/dn454941.aspx)

##<a name="step-5-optional-disable-remember-password-prompt"></a>Trin 5 (valgfrit): deaktivere "Husk adgangskode" Prompt

Når brugere-logon til websteder ved hjælp af filtypenavnet Access Panel, viser Internet Explorer muligvis følgende meddelelse, hvor du bliver spurgt "Vil du gemme adgangskoden?"

![](./media/active-directory-saas-ie-group-policy/remember-password-prompt.png)

Hvis du vil forhindre brugerne i at se denne prompt, skal du følge nedenstående trin for at forhindre Autofuldførelse i at huske adgangskoder:

1. I vinduet **Redigeringsprogrammet til gruppepolitik administration** skal du gå til stien vises nedenfor. Bemærk, at denne indstilling er kun tilgængelig under **Konfiguration af brugeren**.
    - `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`

2. Find indstillingen med navnet **slå funktionen Autofuldførelse for brugernavne og adgangskoder i formularer**.

    > [AZURE.NOTE] Tidligere versioner af Active Directory kan angive denne indstilling med navnet **ikke tillader Autofuldførelse adgangskoder skal gemmes**. Konfiguration for den pågældende indstilling er forskellig fra den indstilling, der er beskrevet i dette selvstudium.

    ![Husk at se efter dette under brugerindstillinger.](./media/active-directory-saas-ie-group-policy/disable-auto-complete.png)

3. Højreklik på indstillingen ovenfor, og klik på **Rediger**.

4. I vinduet med titlen **slå funktionen Autofuldførelse for brugernavne og adgangskoder i formularer**, vælge **deaktiveret**.

    ![Vælg Deaktiver](./media/active-directory-saas-ie-group-policy/disable-passwords.png)

5. Klik på **OK** for at anvende ændringerne og lukke vinduet.

Brugere vil ikke længere kunne gemme deres legitimationsoplysninger eller Brug autofuldførelse, for at få adgang til tidligere gemte legitimationsoplysninger. Denne politik tillader dog brugere kan fortsætte med at bruge autofuldførelse til andre typer formularfelter, som søgefelter.

> [AZURE.WARNING] Hvis denne politik er aktiveret, når brugere har valgt at gemme de nogle legitimationsoplysninger, politik er *ikke* fjerne markeringen i de legitimationsoplysninger, der allerede er gemt.

##<a name="step-6-testing-the-deployment"></a>Trin 6: Test af implementeringen

Følg nedenstående trin for at kontrollere Hvis lokalnummer installationen er fuldført:

1. Hvis du har installeret ved hjælp af **Computerens konfiguration**, log på en klientcomputer, der hører til den OU, som du har valgt i [trin 2: oprette politik gruppeobjektet](#step-2-create-the-group-policy-object). Hvis du installeres ved hjælp af **User Configuration**, skal du sørge for at logge på som en bruger, der hører til denne OU.

2. Det kan tage et par log ins for gruppepolitikken ændres til fuldt opdatere med denne computer. Åbn **et kommandopromptvindue** for at tvinge opdateringen, og køre følgende kommando:`gpupdate /force`

3. Du skal genstarte computeren for til at finde sted installationen. Start kan tage betydeligt længere tid end normalt under filtypenavnet installationer.

4. Åbn **Internet Explorer**, når du har genstartet. I øverste højre hjørne af vinduet skal du klikke på **værktøjer** (tandhjulsikonet) og derefter vælge **Administrer tilføjelsesprogrammer**.

    ![Gå til Funktioner > Administrer tilføjelsesprogrammer](./media/active-directory-saas-ie-group-policy/manage-add-ons.png)

5. Kontrollér, at **Access Panel lokalnummer** er blevet installeret og, dens **Status** er angivet til **aktiveret**i vinduet **Administrer tilføjelsesprogrammer** .

    ![Kontrollér, at filtypenavnet Access Panel er installeret og aktiveret.](./media/active-directory-saas-ie-group-policy/verify-install.png)

## <a name="related-articles"></a>Relaterede artikler

- [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)
- [Adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md)
- [Fejlfinding i forbindelse med Access Kontrolpanel-udvidelse til Internet Explorer](active-directory-saas-ie-troubleshooting.md)
