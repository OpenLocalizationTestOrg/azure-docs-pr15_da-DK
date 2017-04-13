<properties
    pageTitle="Azure Active Directory Domain Services: Administrere en administreret domæne | Microsoft Azure"
    description="Administrere Azure Active Directory Domain Services administrerede domæner"
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

# <a name="administer-an-azure-active-directory-domain-services-managed-domain"></a>Administrere en administreret Azure Active Directory Domain Services-domæne
I denne artikel beskrives, hvordan til at administrere en administreret Azure Active Directory (AD) Domain Services-domæne.


## <a name="before-you-begin"></a>Inden du går i gang
For at udføre de opgaver, der er angivet i denne artikel, skal du:

1. Et gyldigt **Azure-abonnement**.

2. Et **directory Azure AD** - enten synkroniseret med en lokal adresseliste eller en kun skyen mappe.

3. **Azure AD-domænetjenester** skal være aktiveret for Azure AD-mappen. Hvis du ikke har gjort det, skal du følge alle opgaverne i [Introduktion til](./active-directory-ds-getting-started.md).

4. Et **medlem af et domæne virtuelt** , hvorfra du administrerer Azure AD-domænetjenester administrerede domænet. Hvis du ikke har et virtuelt, skal du følge alle de opgaver, der er beskrevet i artiklen [deltage i en Windows virtuel maskine til en administreret domæne](./active-directory-ds-admin-guide-join-windows-vm.md).

5. Du skal have legitimationsoplysninger for en **brugerkonto, der hører til gruppen ' AAD DC administratorer '** i mappen, til at administrere dit administrerede domæne.

<br>


## <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>Administrative opgaver, du kan udføre på et administreret domæne
Medlemmer af gruppen 'AAD DC administratorer' gives tilladelser på den administrerede domæne, der aktiverer dem til at udføre opgaver som f.eks.:

- Tilslutte computere til administrerede domænet.

- Konfigurere de indbyggede GPO til 'AADDC computere' og 'AADDC brugere' beholdere i administrerede domænet.

- Administrere DNS på administrerede domænet.

- Oprette og administrere brugerdefinerede organisatoriske enheder (afdelinger) på den administrerede domæne.

- Gevinst administratoradgang til computere, der er knyttet til administrerede domænet.


## <a name="administrative-privileges-you-do-not-have-on-a-managed-domain"></a>Administratorrettigheder, du ikke har et administreret domæne
Domænenavn administreres af Microsoft, herunder aktiviteter som rettelse, overvågning og udføre sikkerhedskopier. Derfor domænet er låst, og du har ikke rettigheder til at udføre bestemte administrative opgaver på domænet. Der er nogle eksempler på opgaver, du ikke kan udføre under.

- Du er ikke tilladelse til domæneadministrator eller virksomhedens Administrator for administrerede domænet.

- Du kan udvide skemaet for administrerede domænet.

- Du kan ikke oprette forbindelse til domænecontrollere for administrerede domænet ved hjælp af Fjernskrivebord.

- Du kan ikke tilføje domæne enheder til administrerede domænet.


## <a name="task-1---provision-a-domain-joined-windows-server-virtual-machine-to-remotely-administer-the-managed-domain"></a>Opgave 1 - klargøring en medlem af et domæne Windows Server virtuel maskine til at administrere fra en fjernplacering administrerede domænet
Azure AD-domænetjenester administrerede domæner kan administreres ved hjælp af velkendte Active Directory-administration som Active Directory Administrative Center (ADAC) eller AD PowerShell. Lejer administratorer har ikke rettigheder til at oprette forbindelse til domænecontrollere på administrerede domænet via Fjernskrivebord. Medlemmer af gruppen 'AAD DC administratorer' kan derfor administrere administrerede domæner ved hjælp af AD Administration fra en Windows Server/klientcomputer, der er knyttet til administrerede domænet. AD administration kan installeres som en del af funktionen fjernserver værktøjer (RSAT) valgfrit på Windows Server og klientcomputere, der er knyttet til administrerede domænet.

Det første trin er at konfigurere en Windows Server virtuel maskine, der er knyttet til administrerede domænet. Finde en vejledning, referere til i artiklen [deltage i en Windows Server virtuel maskine til et Azure AD Domain Services managed-domæne](active-directory-ds-admin-guide-join-windows-vm.md).

### <a name="remotely-administer-the-managed-domain-from-a-client-computer-for-example-windows-10"></a>Administrere fra en fjernplacering administrerede domænet fra en klientcomputer (for eksempel Windows 10)
Vejledningen i denne artikel bruge en Windows Server virtuel maskine til at administrere AAD-Katalogtjenesten administrerede domæne. Dog kan du også vælge at bruge en Windows-klienten (for eksempel Windows 10) virtuel maskine for at gøre dette.

Du kan [installere fjernserver værktøjer (RSAT)](http://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) på en Windows-klienten virtuel maskine ved at følge vejledningen på TechNet.


## <a name="task-2---install-active-directory-administration-tools-on-the-virtual-machine"></a>Opgave 2 - Installer Active Directory-administration på den virtuelle maskine
Udfør følgende trin for at installere Active Directory-administrationsværktøjerne på virtuelt domæne tilsluttet. Se Technet for flere [oplysninger om installation og brug af værktøjer til fjernadministration Server](https://technet.microsoft.com/library/hh831501.aspx).

1. Gå til **virtuelle maskiner** node i portalen Azure klassisk. Vælg den virtuelle maskine, du oprettede i opgave 1, og klik på **Opret forbindelse** på kommandolinjen i bunden af vinduet.

    ![Oprette forbindelse til Windows virtuelt](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. Portalen klassisk beder dig om at åbne eller gemme en fil med filtypenavnet 'RDP', som bruges til at oprette forbindelse til den virtuelle maskine. Klik på for at åbne filen, når den er færdig med at hente.

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

9. På siden **Funktioner** på Klik for at udvide noden **Værktøjer til fjernadministration Server** , og klik derefter på for at udvide noden **Rolle administrationsværktøjer** . Vælg **Active Directory-Domænetjenester og AD LDS værktøjer** funktion på listen over rolle administrationsværktøjer.

    ![Siden funktioner](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-ad-tools.png)

10. Klik på **Installer** til installation på AD og AD LDS værktøjer funktion på en virtuel maskine på siden **Confirmation** . Når funktionsinstallationen er fuldført, skal du klikke på **Luk** for at afslutte guiden **Tilføj roller og funktioner** .

    ![Bekræftelsessiden](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-confirmation.png)


## <a name="task-3---connect-to-and-explore-the-managed-domain"></a>Opgave 3 – oprette forbindelse til og udforske administrerede domænet
Nu, hvor AD administrationsværktøjer er installeret på domænenavn joinforbundne virtuelt, kan vi bruge disse værktøjer til at udforske og administrere administrerede domænet.

> [AZURE.NOTE] Du skal være medlem af gruppen 'AAD DC administratorer', til at administrere administrerede domænet.

1. Klik på **Administration**fra startskærmen. Du bør se AD administrationsværktøjer installeret på den virtuelle maskine.

    ![Administrative funktioner, der er installeret på serveren](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)

2. Klik på **Active Directory Administrative Center**.

    ![Active Directory Administrative Center](./media/active-directory-domain-services-admin-guide/adac-overview.png)

3. Hvis du vil undersøge domænenavn, skal du klikke på domænenavnet i venstre rude (for eksempel ' contoso100.com'). Bemærk to beholdere, der kaldes 'AADDC computere' og 'AADDC brugere' henholdsvis.

    ![ADAC - visning-domæne](./media/active-directory-domain-services-admin-guide/adac-domain-view.png)

4. Klik på objektbeholderen kaldet **AADDC brugere** for at få vist alle brugere og grupper, der tilhører administrerede domænet. Skal du se afsnittet brugerkonti og grupper fra din Azure AD lejer Vis i denne beholder. Meddelelse i dette eksempel, en brugerkonto for den bruger, kaldet "bob" og en gruppe med navnet 'AAD DC administratorer' er tilgængelige i denne beholder.

    ![ADAC - domænebrugere](./media/active-directory-domain-services-admin-guide/adac-aaddc-users.png)

5. Klik på objektbeholderen kaldet **AADDC computere** for at få vist de computere, der er knyttet til denne administrerede domæne. Du bør se en post for den aktuelle virtuelle maskine, som er knyttet til domænet. Computerkonti for alle computere, der er knyttet til Azure AD-domænetjenester administrerede domænet er gemt i denne 'AADDC computere' objektbeholder.

    ![ADAC - domæne joinforbundne computere](./media/active-directory-domain-services-admin-guide/adac-aaddc-computers.png)

<br>

## <a name="related-content"></a>Relateret indhold

- [Azure AD-domænetjenester - Introduktion](./active-directory-ds-getting-started.md)

- [Deltage i en Windows Server virtuel maskine til en administreret Azure AD Domain Services-domæne](active-directory-ds-admin-guide-join-windows-vm.md)

- [Installere fjernserver administrationsværktøjer](https://technet.microsoft.com/library/hh831501.aspx)
