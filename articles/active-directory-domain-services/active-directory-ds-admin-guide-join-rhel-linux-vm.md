<properties
    pageTitle="Azure Active Directory Domain Services: Deltage i et RHEL VM til en administreret domæne | Microsoft Azure"
    description="Deltage i en Red Hat Enterprise Linux virtuel maskine til Azure AD-domænetjenester"
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

# <a name="join-a-red-hat-enterprise-linux-7-virtual-machine-to-a-managed-domain"></a>Deltage i en rød Hat Enterprise Linux 7 virtuel maskine til en administreret domæne
I denne artikel beskrives, hvordan til at deltage i en rød Hat Enterprise Linux (RHEL) 7 virtuel maskine til en administreret Azure AD Domain Services-domæne.

## <a name="provision-a-red-hat-enterprise-linux-virtual-machine"></a>Klargøre en Red Hat Enterprise Linux virtuel maskine
Udfør følgende trin for at klargøre en RHEL 7 virtuel maskine, ved hjælp af portalen Azure.

1. Log på [Azure-portalen](https://portal.azure.com).

    ![Azure portalen dashboard](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-dashboard.png)

2. Klik på **Ny** i venstre rude, og skriv **Red Hat** i søgepanelet, som vist på følgende skærmbillede. Poster for Red Hat Enterprise Linux vises i søgeresultaterne. Klik på **Red Hat Enterprise Linux 7.2**.

    ![Vælg RHEL i resultater](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-find-rhel-image.png)

3. Søgeresultaterne i ruden **alt** skal indeholde rød Hat Enterprise Linux 7.2 billedet. Klik på **rød Hat Enterprise Linux 7.2** for at få vist flere oplysninger om virtuelt billedet.

    ![Vælg RHEL i resultater](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-select-rhel-image.png)

4. I ruden **rød Hat Enterprise Linux 7.2** , skal du se flere oplysninger om virtuelt billedet. Vælg **Klassisk**på rullelisten **Vælg en implementeringsmodel** . Klik derefter på knappen **Opret** .

    ![Se detaljer om billede](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-clicked.png)

5. Angiv **Værtsnavn** til den nye virtuelle maskine i ruden **Opret VM** . Også angive en lokal administratorbrugernavn i feltet **brugernavn** og en **adgangskode**. Du kan også vælge at bruge en SSH nøgle til at godkende brugeren lokal administrator. Også vælge en **Priser niveau** til den virtuelle maskine.

    ![Oprette VM - grundlæggende oplysninger](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-basic-details.png)

6. Klik på **valgfri konfiguration**. Klik på **netværk**i ruden **valgfrit config** .

    ![Oprette VM - konfigurere virtuelt netværk](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-configure-vnet.png)

7. Dette viser en rude med titlen **netværk**. Klik på **Virtuelt netværk** for at markere det virtuelle netværk, der skal installeres Linux VM i ruden **netværk** . Dette åbner ruden **Virtuelt netværk** . Vælg indstillingen **Brug en eksisterende virtuelt netværk** i ruden **Virtuelt netværk** . Vælg derefter det virtuelle netværk, hvori Azure AD-domænetjenester er tilgængeligt. I dette eksempel skal Vælg vi det virtuelle netværk 'MyPreviewVNet'.

    ![Oprette VM - Vælg virtuelt netværk](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-select-vnet.png)

8. Klik på knappen **OK** på ruden **valgfrit config** .

    ![Oprette VM - virtuelt netværk, der er markeret](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-vnet-selected.png)

9. Du er nu klar til at oprette den virtuelle maskine. Klik på knappen **Opret** i ruden **Opret VM** .

    ![Oprette VM - klar](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm.png)

10. Installation af den nye virtuelle maskine, der er baseret på RHEL 7.2 billedet skal starte.

  ![Oprette VM - installation, der er i gang](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployment-started.png)

11. Når du har et par minutter, skal den virtuelle maskine installeres korrekt og klar til brug.

  ![Oprette VM - installeret](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployed.png)



## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Oprette forbindelse fra en fjernplacering til den nyligt klargjort Linux virtuelle maskine
Virtuelt RHEL 7.2 er klargjort i Azure. Den næste opgave er at oprette forbindelse fra en fjernplacering til den virtuelle maskine.

**Opret forbindelse til den RHEL 7.2 virtuelle maskine** Følg vejledningen i artiklen [Sådan logger en virtuel maskine kører Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md).

Resten af trinnene forudsætter, at du bruger trykfarver SSH-klienten til at oprette forbindelse til virtuelt RHEL. Se [siden trykfarver overførsel](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)kan finde flere oplysninger.

1. Åbn programmet trykfarver.

2. Angiv **Værtsnavn** til nyoprettede RHEL virtual machine. I dette eksempel er vores virtuelt værtsnavn 'contoso-rhel.cloudapp .net'. Hvis du ikke er sikker på værtsnavnet på din VM, kan du referere til VM dashboard på Azure-portalen.

    ![Oprette forbindelse trykfarver](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-connect.png)

3. Log på den virtuelle maskine ved hjælp af de lokale administratorrettigheder, du angav, da den virtuelle maskine blev oprettede. I dette eksempel brugte vi den lokale administratorkonto "mahesh".

    ![Trykfarver logon](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-login.png)


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Installere påkrævede pakker på Linux virtuel maskine
Når du har forbindelse til den virtuelle maskine, er den næste opgave at installere pakker, der kræves til forbindelse til et domæne på den virtuelle maskine. Udfør følgende trin:

1. **Installere realmd:** Pakken realmd bruges til forbindelse til et domæne. I din trykfarver terminal skal du skrive følgende kommando:

    sudo yum Installer realmd

    ![Installere realmd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-realmd.png)

    Efter et par minutter, skal pakken realmd få installeret på den virtuelle maskine.

    ![realmd, der er installeret](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-installed.png)

3. **Installere sssd:** Pakken realmd afhænger af sssd til at udføre domæne joinhandlinger. I din trykfarver terminal skal du skrive følgende kommando:

    sudo yum Installer sssd

    ![Installere sssd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-sssd.png)

    Efter et par minutter, skal pakken sssd få installeret på den virtuelle maskine.

    ![realmd, der er installeret](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-sssd-installed.png)

4. **Installere kerberos:** I din trykfarver terminal skal du skrive følgende kommando:

    sudo yum Installer krb5 arbejdsstation krb5-libs

    ![Installere kerberos](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-kerberos.png)

    Efter et par minutter, skal pakken realmd få installeret på den virtuelle maskine.

    ![Kerberos, der er installeret](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kerberos-installed.png)


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Deltage i virtuelt Linux til administrerede domænet
Nu, hvor de nødvendige pakker er installeret på den Linux virtuelle maskine, er den næste opgave at deltage i den virtuelle maskine til administrerede domænet.

1. Find den administrerede AAD Domain Services-domæne. I din trykfarver terminal skal du skrive følgende kommando:

    sudo ressource opdage CONTOSO100.COM

    ![Opdag ressource](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-discover.png)

    Hvis **startdomænet opdage** er i stand til at finde dit administrerede domæne, kan du sikre dig, at domænet kan nås fra den virtuelle maskine (prøve ping). Også sørge for, at den virtuelle maskine faktisk er blevet installeret på det samme virtuelle netværk, hvori administrerede domænet er tilgængeligt.

2. Initialiseret kerberos. I din trykfarver terminal skal du skrive følgende kommando. Sørg for, at du angiver en bruger, der hører til gruppen 'AAD DC administratorer'. Kun disse brugere kan deltage i computere til administrerede domænet.

    kinitbob@CONTOSO100.COM

    ![Kinit](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kinit.png)

    Sørg for, at du angiver navnet på det domæne med store bogstaver, ellers kinit mislykkes.

3. Deltage i maskinen til domænet. I din trykfarver terminal skal du skrive følgende kommando. Angiv den samme bruger, du har angivet i det foregående trin (kinit).

    sudo ressource joinforbindelse – detaljeret CONTOSO100.COM -U'bob@CONTOSO100.COM'

    ![Ressource joinforbindelse](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-join.png)

Du bør får en meddelelse ("korrekt tilmeldte maskine i ressource") når maskinen blev knyttet til administrerede domænet.


## <a name="verify-domain-join"></a>Bekræfte forbindelse til et domæne
Du kan hurtigt kontrollere, om computeren er blevet sluttet til administrerede domænet. Oprette forbindelse til den nyligt domæne joinforbundne RHEL VM bruge SSH og en brugerkonto for domænet og markér derefter til at se, om brugerkontoen løst korrekt.

1. I din trykfarver terminal skal du skrive følgende kommando for at oprette forbindelse til den nyligt domæne joinforbundne RHEL virtuelt ved hjælp af SSH. Bruge en domænekonto, der hører til administrerede domænet (for eksempel 'bob@CONTOSO100.COM' i dette tilfælde.)

    SSH -l bob@CONTOSO100.COM contoso-rhel.cloudapp.net

2. I din trykfarver terminal skal du skrive følgende kommando for at se, hvis mappen privat blev initialiseret korrekt.

    PWD

3. I din trykfarver terminal skal du skrive følgende kommando for at se, om gruppemedlemskaber er der løst korrekt.

    id

Et eksempel på output af disse kommandoer er:

![Bekræfte forbindelse til et domæne](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-verify-domain-join.png)


## <a name="troubleshooting-domain-join"></a>Foretage fejlfinding af forbindelse til et domæne
Se artiklen [forbindelse til fejlfinding i forbindelse med et domæne](active-directory-ds-admin-guide-join-windows-vm.md#troubleshooting-domain-join) .


## <a name="related-content"></a>Relateret indhold
- [Azure AD-domænetjenester - Introduktion](./active-directory-ds-getting-started.md)

- [Deltage i en Windows Server virtuel maskine til en administreret Azure AD Domain Services-domæne](active-directory-ds-admin-guide-join-windows-vm.md)

- [Sådan logger en virtuel maskine kører Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md).

- [Installation af Kerberos](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)

- [Red Hat Enterprise Linux 7 - vejledning til Windows-Integration](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)
