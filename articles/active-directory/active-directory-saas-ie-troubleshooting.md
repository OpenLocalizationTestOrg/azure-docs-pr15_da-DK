<properties
    pageTitle="Fejlfinding i forbindelse med Access Kontrolpanel-udvidelse til Internet Explorer | Microsoft Azure"
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

#<a name="troubleshooting-the-access-panel-extension-for-internet-explorer"></a>Fejlfinding i forbindelse med Access Kontrolpanel-udvidelse til Internet Explorer

Denne artikel kan hjælpe dig med at løse følgende problemer:

- Er du ikke kan få adgang til dine apps via portalen mine Apps, mens du bruger Internet Explorer.
- Du kan få vist "Installere Software"-meddelelse, selvom du allerede har installeret softwaren.

Hvis du er administrator, skal du se også: [hvordan du kan installere Access Panel udvidelse til Internet Explorer ved hjælp af gruppepolitik](active-directory-saas-ie-group-policy.md)

##<a name="run-the-diagnostic-tool"></a>Køre værktøjet til diagnosticering

Du kan diagnosticere problemer med installation med filtypenavnet Access Panel ved at downloade og køre værktøjet Access Panel diagnosticering:

1. [Klik her for at hente den Diagnosticeringsværktøj.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)

2. Åbn fil, og tryk på knappen **udtrække alle** .

    ![Tryk på udtrække alle](./media/active-directory-saas-ie-troubleshooting/extract1.png)

3. Tryk på knappen **udtrække** at fortsætte.

    ![Tryk på Udpak](./media/active-directory-saas-ie-troubleshooting/extract2.png)

4. Køre værktøjet, højreklik på filen med navnet **AccessPanelExtensionDiagnosticTool**, og vælg derefter **Åbn med > Microsoft Windows baseret Script Host**.

    ![Åbn med > baseret Microsoft Windows Script Host](./media/active-directory-saas-ie-troubleshooting/open_tool.png)

5. Derefter vises følgende diagnosticering vindue, der beskriver, hvad der kan gå galt med din installation.

    ![En stikprøve af vinduet diagnosticering](./media/active-directory-saas-ie-troubleshooting/tool_preview.png)

6. Klik på "**Ja**" for at lade programmet, der løser de problemer, der er fundet.

7. Luk alle Internet Explorer-vinduet for at gemme ændringerne, og Åbn derefter Internet Explorer igen.<br />Hvis du stadig ikke kan få adgang til dine apps, kan du prøve nedenstående trin.

##<a name="check-that-the-access-panel-extension-is-enabled"></a>Kontrollér, at Access Panel filtypenavn er aktiveret

Sådan kontrolleres, at Access Kontrolpanel-udvidelse er aktiveret i Internet Explorer:

1. Klik på **tandhjulsikonet** i øverste højre hjørne af vinduet i Internet Explorer. Vælg herefter **Internetindstillinger**.<br />(I ældre versioner af Internet Explorer kan du finde det under **Værktøjer > Internetindstillinger**.

    ![Gå til Funktioner > Internetindstillinger](./media/active-directory-saas-ie-troubleshooting/internetoptions.png)

2. Klik på fanen **programmer** , og derefter klikke på knappen **Administrer tilføjelsesprogrammer** .

    ![Klik på Administrer tilføjelsesprogrammer](./media/active-directory-saas-ie-troubleshooting/internetoptions_programs.png)

3. I denne dialogboks, Vælg **Access Panel lokalnummer** , og klik derefter på knappen **Aktiver** .

    ![Klik på Aktivér](./media/active-directory-saas-ie-troubleshooting/enableaddon.png)

4. Luk alle Internet Explorer-vinduet for at gemme ændringerne, og Åbn derefter Internet Explorer igen.

##<a name="enable-extensions-for-inprivate-browsing"></a>Aktivere udvidelser til InPrivate-Browsing

Hvis du bruger tilstanden InPrivate-Browsing:

1. Klik på **tandhjulsikonet** i øverste højre hjørne af vinduet i Internet Explorer. Vælg herefter **Internetindstillinger**.<br />(I ældre versioner af Internet Explorer kan du finde det under **Værktøjer > Internetindstillinger**.

    ![En stikprøve af vinduet diagnosticering](./media/active-directory-saas-ie-troubleshooting/inprivateoptions.png)

2. Gå til fanen **beskyttelse af personlige oplysninger** , og derefter **fjerne markeringen i** afkrydsningsfeltet med navnet **deaktivere værktøjslinjer og udvidelser når InPrivate-Browsing starter**</p>

    ![Fjern markeringen Deaktiver værktøjslinjer og udvidelser, når InPrivate-Browsing starter](./media/active-directory-saas-ie-troubleshooting/enabletoolbars.png)

3. Luk alle Internet Explorer-vinduet for at gemme ændringerne, og Åbn derefter Internet Explorer igen.

##<a name="uninstall-the-access-panel-extension"></a>Fjerne filtypenavnet Access Panel

Fjerne filtypenavnet Access Panel fra din computer:

1. Tryk på **Windows-tasten** til at åbne menuen Start på tastaturet. Når menuen er åben, kan du skrive noget for at foretage en søgning. Skriv "Kontrolpanel", og Åbn derefter **Kontrolpanel** , når det vises i søgeresultaterne.

    ![Søg efter i Kontrolpanel](./media/active-directory-saas-ie-troubleshooting/search_sm.png)

2. Ændre indstillingen **Vis efter** til **store ikoner**i øverste højre hjørne af Kontrolpanel. Find derefter, og klik på knappen **programmer og funktioner** .

    ![Chang visningen til at vise store ikoner](./media/active-directory-saas-ie-troubleshooting/control_panel.png)

3. Vælg **Access Panel lokalnummer**, og klik på knappen **Fjern** fra listen.

    ![Klik på Fjern](./media/active-directory-saas-ie-troubleshooting/uninstall.png)

4. Derefter kan du prøve at installere filtypenavnet igen for at se, om problemet er blevet løst.

Hvis du støder på problemer med fjernelse af filtypenavnet, kan du også fjerne den ved hjælp af værktøjet [Microsoft rette den](https://go.microsoft.com/?linkid=9779673) .

## <a name="related-articles"></a>Relaterede artikler

- [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)
- [Adgang til programmer og single sign-on med Azure Active Directory](active-directory-appssoaccess-whatis.md)
- [Sådan installeres Access Kontrolpanel-udvidelse til Internet Explorer ved hjælp af gruppepolitik](active-directory-saas-ie-group-policy.md)
