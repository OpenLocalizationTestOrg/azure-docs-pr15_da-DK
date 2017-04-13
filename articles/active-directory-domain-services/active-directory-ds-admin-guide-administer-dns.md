<properties
    pageTitle="Azure Active Directory Domain Services: Administrere DNS på administrerede domæner | Microsoft Azure"
    description="Administrere DNS på Azure Active Directory Domain Services administrerede domæner"
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
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="administer-dns-on-an-azure-ad-domain-services-managed-domain"></a>Administrere DNS på en Azure AD-domænetjenester administrerede domæne
Azure Active Directory Domain Services indeholder et DNS (Domain Name opløsning)-server, der leverer DNS-opløsning for administrerede domænet. Nogle gange kan du muligvis konfigurere DNS på administrerede domænet. Du skal oprette DNS-poster for computere, der ikke er tilsluttet til domænenavn, konfigurere virtuelle IP-adresser for indlæsning balancere eller konfigurere eksterne DNS-videresendelser. Derfor er brugere, der hører til gruppen 'AAD DC administratorer' tilladelse til DNS-administration på administrerede domænet.


## <a name="before-you-begin"></a>Inden du går i gang
For at udføre de opgaver, der er angivet i denne artikel, skal du:

1. Et gyldigt **Azure-abonnement**.

2. Et **directory Azure AD** - enten synkroniseret med en lokal adresseliste eller en kun skyen mappe.

3. **Azure AD-domænetjenester** skal være aktiveret for Azure AD-mappen. Hvis du ikke har gjort det, skal du følge alle opgaverne i [Introduktion til](./active-directory-ds-getting-started.md).

4. Et **medlem af et domæne virtuelt** , hvorfra du administrerer Azure AD-domænetjenester administrerede domænet. Hvis du ikke har et virtuelt, skal du følge alle de opgaver, der er beskrevet i artiklen [deltage i en Windows virtuel maskine til en administreret domæne](./active-directory-ds-admin-guide-join-windows-vm.md).

5. Du skal have legitimationsoplysninger for en **brugerkonto, der hører til gruppen ' AAD DC administratorer '** i mappen, til at administrere DNS for dit administrerede domæne.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-dns-for-the-managed-domain"></a>Opgave 1 - klargøring virtuelt et medlem af et domæne til at administrere DNS for det domæne, administrerede fra en fjernplacering
Azure AD-domænetjenester administrerede domæner kan administreres ved hjælp af velkendte Active Directory-administration som Active Directory Administrative Center (ADAC) eller AD PowerShell. DNS-Posterne for administrerede domænet kan på samme måde, administreres, ved hjælp af administrationsværktøjer DNS-Server.

Administratorer i Azure AD-biblioteket har ikke rettigheder til at oprette forbindelse til domænecontrollere på administrerede domænet via Fjernskrivebord. Medlemmer af gruppen 'AAD DC administratorer' kan administrere DNS for administrerede domæner ved hjælp af DNS-Server værktøjer fra en Windows Server/klientcomputer, der er knyttet til administrerede domænet. DNS-Server-værktøjer kan installeres som en del af funktionen fjernserver værktøjer (RSAT) valgfrit på Windows Server og klientcomputere, der er knyttet til administrerede domænet.

Den første opgave er at klargøre en Windows Server virtuel maskine, der er knyttet til administrerede domænet. Finde en vejledning, referere til i artiklen [deltage i en Windows Server virtuel maskine til et Azure AD Domain Services managed-domæne](active-directory-ds-admin-guide-join-windows-vm.md).


## <a name="task-2---install-dns-server-tools-on-the-virtual-machine"></a>Opgave 2 - Installer DNS Server værktøjer på den virtuelle maskine
Udfør følgende trin for at installere DNS-administration på virtuelt domæne tilsluttet. Du kan finde flere oplysninger om [installation og brug af værktøjer til fjernadministration Server](https://technet.microsoft.com/library/hh831501.aspx), Technet.

1. Gå til **virtuelle maskiner** node i portalen Azure klassisk. Vælg den virtuelle maskine, du oprettede i opgave 1, og klik på **Opret forbindelse** på kommandolinjen i bunden af vinduet.

    ![Oprette forbindelse til Windows virtuelt](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. Portalen klassisk beder dig om at åbne eller gemme en fil med filtypenavnet 'RDP', som bruges til at oprette forbindelse til den virtuelle maskine. Klik på fil, når den er færdig med at hente.

3. Bruge legitimationsoplysningerne for en bruger, der hører til gruppen 'AAD DC administratorer' kommandoprompten login. For eksempel vi bruge 'bob@domainservicespreview.onmicrosoft.com' i dette tilfælde.

4. Åbn **Server Manager**på startskærmen. Klik på **Tilføj roller og funktioner** i den midterste rude i vinduet Server Manager.

    ![Start Server Manager på virtuel maskine](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)

5. Klik på **Næste**på siden **Inden du går i gang** i **tilføje roller og guiden funktioner**.

    ![Før du går i gang side](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)

6. Lad indstillingen **rollebaseret eller funktion-baseret installation** , der er markeret, og klik på **Næste**på siden **Installationstype** .

    ![Installationsside Type](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)

7. Vælg den aktuelle virtuelle maskine fra puljen server på siden **Valg af Server** , og klik på **Næste**.

    ![Side til valg af Server](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)

8. Klik på **Næste**på siden **Serverroller** . Vi gå videre denne side, da vi ikke installerer en hvilken som helst roller på serveren.

9. På siden **Funktioner** på Klik for at udvide noden **Værktøjer til fjernadministration Server** , og klik derefter på for at udvide noden **Rolle administrationsværktøjer** . Vælg **DNS Server Tools** funktion på listen over rolle administrationsværktøjer.

    ![Siden funktioner](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

10. Klik på **Installer** for at installere funktionen DNS-Server-værktøjer på den virtuelle maskine på siden **Confirmation** . Når funktionsinstallationen er fuldført, skal du klikke på **Luk** for at afslutte guiden **Tilføj roller og funktioner** .

    ![Bekræftelsessiden](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-confirmation.png)


## <a name="task-3---launch-the-dns-management-console-to-administer-dns"></a>Opgave 3 - Start DNS management console til at administrere DNS
Nu, hvor funktionen DNS Server Tools er installeret på domænenavn joinforbundne virtuelt, kan vi bruge DNS-værktøjer til at administrere DNS på administrerede domænet.

> [AZURE.NOTE] Du skal være medlem af gruppen 'AAD DC administratorer', til at administrere DNS på administrerede domænet.

1. Klik på **Administration**fra startskærmen. Du bør se **DNS-** konsollen installeret på den virtuelle maskine.

    ![Administration - DNS-konsollen](./media/active-directory-domain-services-admin-guide/install-rsat-dns-tools-installed.png)

2. Klik på **DNS** for at starte DNS Management console.

3. Klik på indstillingen med titlen **følgende computer**i dialogboksen **Opret forbindelse til DNS-Server** , og Indtast domænenavn DNS på den administrerede domæne (for eksempel ' contoso100.com').

    ![DNS Console - oprette forbindelse til domæne](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

4. DNS-konsollen opretter forbindelse til administrerede domænet.

    ![DNS Console - administrere domæne](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

5. Du kan nu bruge DNS-konsollen til at tilføje DNS-poster for computere i det virtuelle netværk, hvor du har aktiveret AAD Domain Services.

> [AZURE.WARNING] Vær forsigtig, når du administrerer DNS for det administrerede domæne ved hjælp af DNS-administrationsværktøjer. Sørg for, du **ikke slette eller redigere de indbyggede DNS-poster, der anvendes af Domain Services i domænet**. Indbyggede DNS-poster omfatter domæne DNS-poster, navneserverposter og andre poster, der bruges til DC placering. Hvis du ændrer disse poster, er domænetjenester afbrudt på det virtuelle netværk.


Se [DNS-værktøjer artikel på Technet](https://technet.microsoft.com/library/cc753579.aspx) for at få flere oplysninger om administration af DNS.


## <a name="related-content"></a>Relateret indhold

- [Azure AD-domænetjenester - Introduktion](./active-directory-ds-getting-started.md)

- [Deltage i en Windows Server virtuel maskine til en administreret Azure AD Domain Services-domæne](active-directory-ds-admin-guide-join-windows-vm.md)

- [Administrere en administreret Azure AD Domain Services-domæne](active-directory-ds-admin-guide-administer-domain.md)

- [DNS-administrationsværktøjer](https://technet.microsoft.com/library/cc753579.aspx)
