<properties 
   pageTitle="Sikring af data, der er gemt i Azure datalager sø | Microsoft Azure" 
   description="Lær at sikre data i Azure sø lageret med Data ved hjælp af grupper og adgangskontrollister" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/29/2016"
   ms.author="nitinme"/>

# <a name="securing-data-stored-in-azure-data-lake-store"></a>Sikring af data, der er gemt i Azure sø datalager

Sikring af data i Azure datalager sø er en måde, tre trin.

1. Starte med at oprette sikkerhedsgrupper i Azure Active Directory (AAD). Disse sikkerhedsgrupper bruges til at implementere rollebaseret adgangskontrol (RBAC) Azure-portalen. Flere oplysninger under [Rollebaseret adgangskontrol i Microsoft Azure](../active-directory/role-based-access-control-configure.md).

2. Tildele sikkerhedsgrupper AAD til kontoen Azure sø datalager. Dette styrer adgang til kontoen sø datalager fra portalen og administration af handlinger fra portalen eller API'er.

3. Tildele sikkerhedsgrupper AAD som access adgangskontrollister (ACLs) i filsystemet sø datalager.

4. Desuden kan du også angive en IP-adresseområder for klienter, der kan få adgang til dataene i sø datalager.

I denne artikel indeholder en vejledning til, hvordan du bruger portalen Azure til at udføre de ovenfor opgaver. Du kan finde detaljerede oplysninger om, hvordan sø datalager implementerer sikkerhed på niveauet for konto og data, [sikkerhed i Azure sø datalager](data-lake-store-security-overview.md). Du kan finde gå i dybden oplysninger om, hvordan ACLs er implementeret i Azure datalager sø [Oversigt over adgangskontrol i sø datalager](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Forudsætninger

Inden du starter selvstudiet, skal du have følgende:

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).
- **En Azure sø datalager konto**. Flere oplysninger om, hvordan du opretter en, under [Introduktion til Azure sø datalager](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Oprette sikkerhedsgrupper i Azure Active Directory

Du kan finde oplysninger om, hvordan du opretter AAD sikkerhedsgrupper og hvordan du tilføjer brugere til gruppen, [administrere sikkerhedsgrupper i Azure Active Directory](../active-directory/active-directory-accessmanagement-manage-groups.md).

## <a name="assign-users-or-security-groups-to-azure-data-lake-store-accounts"></a>Tildele brugere eller sikkerhedsgrupper til Azure sø datalager konti

Når du tildeler brugere eller sikkerhedsgrupper til Azure datalager sø konti, du kan kontrollere adgang til administration af handlinger på den konto, bruger Azure portalen og Azure ressourcestyring API'er. 

1. Åbn en Azure datalager sø-konto. Klik på **Gennemse**i venstre rude, skal du klikke på **Sø datalager**, og klik på kontonavnet, som du vil tildele en bruger eller sikkerhed gruppe fra bladet sø datalager.

2. Klik på **Indstillinger**i din sø datalager konto blade. Klik på **brugere**fra bladet **Indstillinger** .

    ![Tildele sikkerhedsgruppe til Azure sø datalager konto] (./media/data-lake-store-secure-data/adl.select.user.icon.png "Tildele sikkerhedsgruppe til Azure sø datalager konto")

3. Bladet **bruger** som standard vises **abonnement** administratorgruppen som en ejer. 

    ![Tilføj brugere og roller] (./media/data-lake-store-secure-data/adl.add.group.roles.png "Tilføj brugere og roller")
 
    Der er to måder at tilføje en gruppe og tildele relevante roller.

    * Føje en bruger/gruppe til kontoen og derefter tildele en rolle, eller
    * Tilføje en rolle, og derefter tildele brugere/grupper til rolle.

    I dette afsnit, er vi se på den første tilgang, hvordan du føjer en gruppe og derefter tildele roller. Du kan udføre lignende trin for at du først markere en rolle og grupper tildeles rollen.
    
4. Klik på **Tilføj** for at åbne bladet **Tilføj access** i bladet **brugere** . Klik på **Vælg en rolle**i bladet **Tilføj access** , og derefter vælge en rolle til brugeren/gruppen.

     ![Tilføj en rolle til brugeren] (./media/data-lake-store-secure-data/adl.add.user.1.png "Tilføj en rolle til brugeren")

    **Ejeren** og rollen **bidragyder** giver adgang til en række administrationsfunktioner på kontoen data sø. For brugere, der kan interagere med data i data sø, kan du føje dem til rollen **læser **. Omfanget af disse roller er begrænset til at håndteringen, der er relateret til kontoen Azure sø datalager.

    Handlinger individuelle filer systemtilladelser definerer hvad brugerne kan gøre for data. Derfor kan en bruger har en læser rolle kun få vist administrationsindstillinger, der er knyttet til kontoen, men kan potentielt læse og skrive data, der er baseret på tildelt tilladelser til filsystemet. Sø datalager tilladelser til filsystemet er beskrevet på [tildele sikkerhedsgruppe som ACLs til Azure datalager sø-filsystemet](#filepermissions).



5. Klik på **Tilføj brugere** for at åbne bladet **Tilføj brugere** i bladet **Tilføj adgang** . Se denne blade for den sikkerhedsgruppe, du oprettede tidligere i Azure Active Directory. Hvis du har en masse grupper at søge i, kan du bruge tekstfeltet øverst til at filtrere på gruppenavnet. Klik på **Vælg**.

    ![Tilføje en sikkerhedsgruppe] (./media/data-lake-store-secure-data/adl.add.user.2.png "Tilføje en sikkerhedsgruppe")

    Hvis du vil tilføje en gruppe/bruger, der ikke vises, kan du invitere dem ved hjælp af ikonet **Inviter** og angive mailadressen for brugeren eller gruppen.

6. Klik på **OK**. Du bør se sikkerhedsgruppen tilføjet som vist nedenfor.

    ![Sikkerhedsgruppe tilføjet] (./media/data-lake-store-secure-data/adl.add.user.3.png "Sikkerhedsgruppe tilføjet")

7. Dit bruger-/ sikkerhedsgruppe har nu adgang til kontoen Azure sø datalager. Hvis du vil give adgang til bestemte brugere, kan du føje dem til sikkerhedsgruppen. Hvis du vil tilbagekalde adgang for en bruger, kan du også fjerne dem fra sikkerhedsgruppen. Du kan også tildele flere sikkerhedsgrupper til en konto. 

## <a name="filepermissions"></a>Tildele brugere eller sikkerhedsgruppe som ACLs til Azure datalager sø-filsystemet

Ved at tildele brugere/sikkerhedsgrupper til Azure Data sø-filsystemet, kan du angive adgangskontrol på de data, der er gemt i Azure sø datalager.

1. Klik på **Data Explorer**i din sø datalager konto blade.

    ![Opret mapper i sø datalager konto] (./media/data-lake-store-secure-data/adl.start.data.explorer.png "Opret mapper i Data sø konto")

2. Klik på den fil eller mappe, du vil konfigurere ACL i bladet **Data Explorer** , og klik derefter på **Access**. Hvis du vil tildele ACL til en fil, skal du klikke på **Access** fra bladet **Filgennemgang** .

    ![Angive ACLs på Data sø filsystem] (./media/data-lake-store-secure-data/adl.acl.1.png "Angive ACLs på Data sø filsystem")

3. Bladet **Access** viser de standard adgang og brugerdefinerede access, der allerede er tildelt til i roden. Klik på ikonet **Tilføj** for at tilføje brugerdefineret niveau ACLs.

    ![Listen standardfarver og brugerdefinerede access] (./media/data-lake-store-secure-data/adl.acl.2.png "Listen standardfarver og brugerdefinerede access")

    * **Standard adgang til** er UNIX-typografi access, hvor du kan angive læse, skrive, udføre (rwx) til tre forskellige bruger klasser: ejer, gruppen og andre.
    * **Brugerdefineret access** svarer til de POSIX adgangskontrollister, der gør det muligt at angive tilladelser for bestemte navngivne brugere eller grupper, og ikke kun filens ejer eller gruppe. 
    
    Du kan finde flere oplysninger [HDFS ACLs](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Du kan finde flere oplysninger om, hvordan ACLs er implementeret i sø datalager, [Adgangskontrol i sø datalager](data-lake-store-access-control.md).

4. Klik på ikonet **Tilføj** for at åbne bladet **Tilføje brugerdefineret Access** . I denne blade, skal du klikke på **Vælg bruger eller gruppe**og derefter se i **Vælg bruger eller gruppe** blade for den sikkerhedsgruppe, du oprettede tidligere i Azure Active Directory. Hvis du har en masse grupper at søge i, kan du bruge tekstfeltet øverst til at filtrere på gruppenavnet. Klik på den gruppe, du vil tilføje, og klik derefter på **Vælg**.

    ![Tilføj en gruppe] (./media/data-lake-store-secure-data/adl.acl.3.png "Tilføj en gruppe")

5. Klik på **Select-tilladelser**, Vælg tilladelserne, og adgang til ACL eller begge dele, uanset om du vil tildele tilladelserne, der som standard ACL. Klik på **OK**.

    ![Tildele tilladelser til gruppe] (./media/data-lake-store-secure-data/adl.acl.4.png "Tildele tilladelser til gruppe")

    Du kan finde flere oplysninger om tilladelser i sø datalager og standard/adgang til ACL, [Adgangskontrol i sø datalager](data-lake-store-access-control.md).


6. Klik på **OK**i bladet **Tilføje brugerdefineret Access** . Gruppen nyligt tilføjede med de tilknyttede tilladelser, vises nu i bladet **adgang** .

    ![Tildele tilladelser til gruppe] (./media/data-lake-store-secure-data/adl.acl.5.png "Tildele tilladelser til gruppe")

    > [AZURE.IMPORTANT] I den aktuelle udgave, kan du kun har 9 poster under **Brugerdefineret adgang**. Hvis du vil tilføje mere end 9 brugere, skal du oprette sikkerhedsgrupper kan tilføje brugere til sikkerhedsgrupper, giver adgang til disse sikkerhedsgrupper for kontoen sø datalager.

7. Hvis det er nødvendigt, kan du også ændre adgangstilladelserne, når du har tilføjet gruppen. Markér afkrydsningsfeltet for hver tilladelsestype (Læs, Skriv, Execute) baseret på, om du vil fjerne eller tildele denne tilladelse til sikkerhedsgruppen eller fjern markeringen. Klik på **Gem** at gemme ændringerne eller på **Slet** for at fortryde ændringerne.

## <a name="set-ip-address-range-for-data-access"></a>Angive IP-adresseområder for dataadgang

Azure datalager sø gør det muligt at yderligere låsning adgang til dit datalager på netværk niveau. Du kan aktivere firewall, angiver en IP-adresse, eller du kan definere en IP-adresseområder for de klienter, der er tillid til. Når aktiveret, kan kun klienter, der har IP-adresser i defineret område kan oprette forbindelse til store.

![Firewall-indstillinger og IP-adgang] (./media/data-lake-store-secure-data/firewall-ip-access.png "Firewall-indstillinger og IP-adresse")

## <a name="remove-security-groups-for-an-azure-data-lake-store-account"></a>Fjerne sikkerhedsgrupper for en Azure datalager sø-konto

Når du fjerner sikkerhedsgrupper fra Azure datalager sø konti, ændrer du kun adgang til administration af handlinger på den konto, bruger Azure portalen og Azure ressourcestyring API'er.

1. Klik på **Indstillinger**i din sø datalager konto blade. Klik på **brugere**fra bladet **Indstillinger** .

    ![Tildele sikkerhedsgruppe til Azure Data sø konto] (./media/data-lake-store-secure-data/adl.select.user.icon.png "Tildele sikkerhedsgruppe til Azure Data sø konto")

2. Klik på den sikkerhedsgruppe, du vil fjerne i bladet **brugere** .

    ![Sikkerhedsgruppe for at fjerne] (./media/data-lake-store-secure-data/adl.add.user.3.png "Sikkerhedsgruppe for at fjerne")

3. Klik på **Fjern**i bladet for sikkerhedsgruppen.

    ![Sikkerhedsgruppe fjernet] (./media/data-lake-store-secure-data/adl.remove.group.png "Sikkerhedsgruppe fjernet")

## <a name="remove-security-group-acls-from-azure-data-lake-store-file-system"></a>Fjerne sikkerhedsgruppe ACLs fra Azure datalager sø-filsystemet

Når du fjerner sikkerhedsgrupper ACLs fra Azure datalager sø-filsystemet, kan du ændre adgang til dataene i sø datalager.

1. Klik på **Data Explorer**i din sø datalager konto blade.

    ![Opret mapper i Data sø konto] (./media/data-lake-store-secure-data/adl.start.data.explorer.png "Opret mapper i Data sø konto")

2. Klik på den fil eller mappe, du vil fjerne ACL i bladet **Data Explorer** , og derefter klikke på ikonet **adgang** i din konto blade. Hvis du vil fjerne ACL for en fil, skal du klikke på **Access** fra bladet **Filgennemgang** .

    ![Angive ACLs på Data sø filsystem] (./media/data-lake-store-secure-data/adl.acl.1.png "Angive ACLs på Data sø filsystem")

3. Klik på den sikkerhedsgruppe, du vil fjerne i bladet **adgang** fra sektionen **Brugerdefineret adgang** . Klik på **Fjern** i bladet **Brugerdefineret Access** , og klik derefter på **OK**.

    ![Tildele tilladelser til gruppe] (./media/data-lake-store-secure-data/adl.remove.acl.png "Tildele tilladelser til gruppe")


## <a name="see-also"></a>Se også

- [Oversigt over Azure sø datalager](data-lake-store-overview.md)
- [Kopiere data fra Azure-lager BLOB til sø datalager](data-lake-store-copy-data-azure-storage-blob.md)
- [Bruge Azure Data sø Analytics med sø datalager](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Bruge Azure HDInsight med sø datalager](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Komme i gang med datalager sø ved hjælp af PowerShell](data-lake-store-get-started-powershell.md)
- [Komme i gang med datalager sø ved hjælp af .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Access diagnosticeringslogfiler for sø datalager](data-lake-store-diagnostic-logs.md)
