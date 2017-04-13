<properties
    pageTitle="Azure Active Directory Domain Services: Deltage i en Windows Server VM til en administreret domæne | Microsoft Azure"
    description="Deltage i en Windows Server virtuel maskine til Azure AD-domænetjenester"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/02/2016"
    ms.author="maheshu"/>

# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Deltage i en Windows Server virtuel maskine til en administreret domæne

> [AZURE.SELECTOR]
- [Azure klassisk portal - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
- [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)

<br>

I denne artikel beskrives, hvordan til at deltage i en virtuel maskine, der kører Windows Server 2012 R2 til en administreret Azure AD Domain Services-domæne, ved hjælp af portalen Azure klassisk.


## <a name="step-1-create-the-windows-server-virtual-machine"></a>Trin 1: Oprette virtuelt Windows Server
Følg instruktionerne i selvstudiet [Opret en virtuel maskine kører Windows Azure klassisk portalen](../virtual-machines/virtual-machines-windows-classic-tutorial.md) . Det er vigtigt at sikre, at den nyligt oprettede virtuelt er knyttet til det samme virtuelle netværk, hvor du har aktiveret Azure AD-domænetjenester. Indstillingen 'Hurtig Opret' muligt ikke at deltage i den virtuelle maskine til et virtuelt netværk. Derfor skal du bruge indstillingen fra galleriet' til at oprette den virtuelle maskine.

Udfør følgende trin for at oprette en Windows virtuel maskine, der er knyttet til det virtuelle netværk, hvor du har aktiveret Azure AD-domænetjenester.

1. Klik på **Ny**i Azure klassisk-portalen på kommandolinjen i bunden af vinduet.

2. Klikke på **virtuelt**under **beregne**, og derefter klikke på **Fra galleriet**.

3. Det første skærmbillede kan du **vælge et billede** til din virtuelle maskine på listen over tilgængelige billeder. Vælg det ønskede billede.

    ![Vælg billede](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)

4. Det andet skærmbillede kan du vælge computernavn, størrelse og administratorbrugernavn og adgangskode. Bruge niveau og størrelse, der er nødvendige for at køre din app eller arbejdsbyrde. Navnet på den bruger, du vælger her er en lokal administratorbruger på computeren. Angiv ikke en domænebrugerkonto legitimationsoplysninger her.

    ![Konfigurere virtuelt](./media/active-directory-domain-services-admin-guide/create-windows-vm-config.png)

5. Det tredje skærmbillede kan du konfigurere ressourcer til netværk, storage og tilgængelighed. Kontrollér, at du har valgt den virtuelle netværk, hvor du har aktiveret Azure AD-domænetjenester på rullelisten **Område/forbindelse gruppe/virtuelle netværk** . Angiv en **Skybaseret tjeneste DNS-navn** , der passer til den virtuelle maskine.

    ![Vælg virtuelt netværk for virtuelt](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [AZURE.WARNING]
    Sørg for, at du deltager i den virtuelle maskine til det samme virtuelle netværk, hvor du har aktiveret Azure AD-domænetjenester. Som et resultat, kan den virtuelle maskine "se' domænet og udføre opgaver som tilslutter sig domænet. Hvis du vælger at oprette den virtuelle maskine i et andet virtuelle netværk, kan du oprette forbindelse, virtuelt netværk til det virtuelle netværk, hvor du har aktiveret Azure AD-domænetjenester.

6. Det fjerde skærmbillede kan du installere VM Agent og konfigurere nogle af de tilgængelige udvidelser.

    ![Færdig](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)

7. Når den virtuelle maskine er oprettet, viser portalen klassisk den nye virtuelle maskine under noden **virtuelle computere** . Både den virtuelle computer og en skybaseret tjeneste der startes automatisk, og deres status er angivet som **kører**.

    ![Virtual machine er oppe at køre](./media/active-directory-domain-services-admin-guide/create-windows-vm-running.png)


## <a name="step-2-connect-to-the-windows-server-virtual-machine-using-the-local-administrator-account"></a>Trin 2: Oprette forbindelse til Windows Server virtual machine ved hjælp af den lokale administratorkonto
Nu skal vi oprette forbindelse til den nyoprettede Windows Server virtuelle maskine, at deltage i den til domænet. Brug de lokale administratorrettigheder, du har angivet, når du opretter den virtuelle maskine, til at oprette forbindelse til den.

Udfør følgende trin for at oprette forbindelse til den virtuelle maskine.

1. Gå til **virtuelle maskiner** node i portalen klassisk. Vælg den virtuelle maskine, du oprettede i trin 1, og klik på **Opret forbindelse** på kommandolinjen i bunden af vinduet.

    ![Oprette forbindelse til Windows virtuelt](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. Portalen klassisk beder dig om at åbne eller gemme en fil med filtypenavnet 'RDP', som bruges til at oprette forbindelse til den virtuelle maskine. Klik på for at åbne filen, når den er færdig med at hente.

3. Logon kommandoprompten, Angiv dine **legitimationsoplysninger for lokale administrator**, som du har angivet, mens du opretter den virtuelle maskine. For eksempel har vi brugt 'localhost\mahesh' i dette eksempel.

På dette tidspunkt, skal du være logget det nyoprettede Windows virtuelt ved hjælp af lokale legitimationsoplysninger for Administrator. Næste trin er at deltage i den virtuelle maskine til domænet.


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-aad-ds-managed-domain"></a>Trin 3: Deltag i Windows Server virtuelt AAD DS administrerede domænet
Udfør følgende trin for at deltage i Windows Server virtuelt AAD DS administrerede domænet.

1. Oprette forbindelse til Windows Server, som vist i trin 2. Åbn **Server Manager**på startskærmen.

2. Klik på **Lokalserver** i venstre rude i vinduet Server Manager.

    ![Start Server Manager på virtuel maskine](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)

3. Klik på **arbejdsgruppe** under sektionen **Egenskaber** . Klik på **Skift** for at deltage i domænet på siden **Egenskaber for System** egenskab.

    ![Siden med egenskaber for system](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)

4. Angiv navnet på det domæne af Azure AD-domænetjenester administrerede domæne i tekstfeltet **domæne** , og klik på **OK**.

    ![Angiv domænet, der skal sammenkædes](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)

5. Du bliver bedt om at angive dine legitimationsoplysninger for at deltage i domænet. Kontrollér, at du **angive legitimationsoplysninger for en bruger, der hører til AAD DC administratorer** gruppen. Kun medlemmer af denne gruppe har rettigheder til at tilslutte computere til administrerede domænet.

    ![Angiv legitimationsoplysninger for forbindelse til et domæne](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)

6. Du kan angive legitimationsoplysninger på en af følgende måder:

    - UPN-formatet: angive UPN-suffiks for brugerkontoen, som er konfigureret i Azure AD. I dette eksempel UPN-suffiks for brugeren, "bob" er 'bob@domainservicespreview.onmicrosoft.com'.

    - SAMAccountName format: Du kan angive kontonavnet i formatet SAMAccountName. I dette eksempel skal bruger 'bob' angive 'CONTOSO100\bob'.

        > [AZURE.NOTE] **Vi anbefaler ved hjælp af UPN-formatet til at angive legitimationsoplysninger.** SAMAccountName kan være automatisk oprettede Hvis en bruger UPN præfiks er alt for lang tid (for eksempel 'joereallylongnameuser'). Hvis flere brugere har samme UPN præfikset (eksempelvis "bob") i din Azure AD-lejer, være deres SAMAccountName format automatisk oprettede af tjenesten. I disse tilfælde bruges UPN-formatet pålideligt til at logge på domænenavn.

7. Når der oprettes forbindelse til et domæne, kan du se følgende meddelelse velkomstmeddelelse til domænet. Genstart den virtuelle maskine for at fuldføre domæne Deltag i handlingen.

    ![Velkommen til domænet](./media/active-directory-domain-services-admin-guide/join-domain-done.png)


## <a name="troubleshooting-domain-join"></a>Foretage fejlfinding af forbindelse til et domæne
### <a name="connectivity-issues"></a>Forbindelsesproblemer
Hvis den virtuelle maskine er i stand til at finde domænenavn, kan du referere til følgende fejlfindingstrin:

- Sørg for, at den virtuelle maskine er forbundet til det samme virtuelle netværk, som du har aktiveret Domain Services i. Hvis ikke, den virtuelle maskine er kunne ikke oprettes forbindelse til domænet, og derfor kan ikke oprette forbindelse til domænet.

- Hvis den virtuelle maskine er forbundet med en anden virtuelt netværk, kan du sikre dig, at dette virtuelle netværk har forbindelse til det virtuelle netværk, hvor du har aktiveret Domain Services.

- Prøv at pinge det domæne, ved hjælp af domænenavnet på det administrerede domæne (for eksempel ' ping contoso100.com'). Hvis du ikke gøre det, kan du forsøge at pinge IP-adresser for det domæne, der vises på siden, hvor du har aktiveret Azure AD-domænetjenester (for eksempel ' ping 10.0.0.4'). Hvis du er i stand til at pinge IP-adressen, men ikke domænet, kan DNS, muligvis ikke konfigureret korrekt. Du kan ikke har konfigureret IP-adresser på domænet, som DNS-servere for det virtuelle netværk.

- Prøv at Træk DNS resolver-cache på den virtuelle maskine (' ipconfig/flushdns').

Hvis du kommer til den dialogboks, der beder om legitimationsoplysninger for at deltage i domænenavn, har du ikke problemer med serverforbindelsen.


### <a name="credentials-related-issues"></a>Legitimationsoplysninger-relaterede problemer
Henvise til følgende trin, hvis du har problemer med legitimationsoplysninger og ikke kan deltage i domænet.

- Prøv at bruge UPN-formatet til at angive legitimationsoplysninger. SAMAccountName for din konto muligvis automatisk oprettede, hvis der er flere brugere med det samme UPN præfiks i din lejer, eller hvis dit UPN præfiks er alt for lange. Derfor være SAMAccountName formatet for din konto forskellige fra hvad du forventer eller bruger i dit lokale domæne.

- Prøv at bruge legitimationsoplysningerne for en brugerkonto, der hører til gruppen 'AAD DC administratorer' at tilslutte computere til administrerede domænet.

- Sørg for, at du har [aktiveret synkronisering af adgangskoder](active-directory-ds-getting-started-password-sync.md) i overensstemmelse med de trin, der er beskrevet i Introduktion.

- Sørg for, at du bruger UPN på brugeren, som er konfigureret i Azure AD (for eksempel 'bob@domainservicespreview.onmicrosoft.com') til at logge på.

- Sørg for, at du har ventet længe nok til synkronisering af adgangskoder til at fuldføre som angivet i guiden Introduktion.


## <a name="related-content"></a>Relateret indhold

- [Azure AD-domænetjenester - Introduktion](./active-directory-ds-getting-started.md)

- [Administrere en administreret Azure AD Domain Services-domæne](./active-directory-ds-admin-guide-administer-domain.md)
