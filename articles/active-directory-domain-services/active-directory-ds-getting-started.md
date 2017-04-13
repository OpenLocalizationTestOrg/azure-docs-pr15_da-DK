<properties
    pageTitle="Azure AD-domænetjenester: Oprette AAD DC administratorgruppen | Microsoft Azure"
    description="Introduktion til Azure Active Directory Domain Services"
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

# <a name="get-started-with-azure-ad-domain-services"></a>Introduktion til Azure AD-domænetjenester

I denne artikel vejledes gennem de konfigurationsopgaver, der kræves for at aktivere Azure AD-domænetjenester for din Azure AD-lejer.

## <a name="task-1-create-the-aad-dc-administrators-group"></a>Opgave 1: Oprette ' AAD DC administratorgruppen '
Den første opgave er at oprette en administrativ gruppe i din Azure Active Directory-lejer. Denne specielle administrative gruppe kaldes **AAD DC administratorer**. Medlemmer af denne gruppe er tildelt administratorrettigheder på computere, der er medlem af et domæne til Azure AD-domænetjenester administrerede domænet. På maskiner der er medlem af et domæne, føjes denne gruppe til gruppen 'Administratorer'. Medlemmer af denne gruppe kan desuden bruge Fjernskrivebord til at oprette forbindelse fra en fjernplacering for computere, der er medlem af et domæne.  

> [AZURE.NOTE] Du har ikke tilladelser som domæneadministrator eller virksomhedens Administrator på den administrerede domæne, der er oprettet ved hjælp af Azure AD-domænetjenester. Administrerede domæner disse rettigheder er reserveret af tjenesten og gøres ikke tilgængelige for brugere i lejeren. Du kan dog bruge speciel administratorgruppen oprettet i denne Konfigurationsopgave til at udføre nogle privilegerede handlinger. Disse handlinger omfatter deltagelse i computere til det domæne, der hører til gruppen Administratorer på medlem af et domæne maskiner, konfigurere gruppe politik osv.

I denne Konfigurationsopgave, du opretter den administrative gruppe og føje en eller flere brugere i mappen til gruppen. Udfør følgende trin for at oprette den administrative gruppe til Azure AD Domain Services:

1. Gå til **Azure klassisk portal** ([https://manage.windowsazure.com](https://manage.windowsazure.com))

2. Vælg noden **Active Directory** i den venstre rude.

3. Vælg Azure AD-lejer (bibliotek), du vil aktivere Azure AD-domænetjenester. Du kan kun oprette ét domæne for hver Azure AD-mappe.

    ![Vælg Azure AD-mappe](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Klik på fanen **grupper** .

5. Hvis du vil tilføje en gruppe til din Azure AD-lejer, skal du klikke på **Tilføj gruppe** fra opgaveruden nederst på siden.

    ![Tilføj knappen gruppe](./media/active-directory-domain-services-getting-started/add-group-button.png)

6. Oprette en gruppe med navnet **AAD DC administratorer**. Angiv **GRUPPETYPEN** til **sikkerhed**.

    > [AZURE.WARNING] For at aktivere adgang til på Azure AD-domænetjenester administrerede domæne, skal du oprette en gruppe med dette nøjagtige navn.

    ![Oprette administratorgruppe](./media/active-directory-domain-services-getting-started/create-admin-group.png)

7. Tilføje en beskrivelse af denne gruppe, så andre forstå, denne gruppe bruges til at give administratorrettigheder i Azure AD-domænetjenester.

8. Når gruppen er blevet oprettet, kan du klikke på navnet på gruppen for at se egenskaberne for denne gruppe. Klik på knappen **Tilføj medlemmer** på panelet bunden for at tilføje brugere som medlemmer af denne gruppe.

    ![Gruppen medlemmer knappen Tilføj](./media/active-directory-domain-services-getting-started/add-group-members-button.png)

9. Vælg de brugere, der skal være medlem af denne gruppe og marker afkrydsningsfeltet, når du er færdig, i dialogboksen **Tilføj medlemmer** .

    ![Føje brugere til administratorgruppen](./media/active-directory-domain-services-getting-started/add-group-members.png)

<br>

## <a name="task-2-create-or-select-an-azure-virtual-network"></a>Opgave 2: Oprette eller vælge en Azure virtuelt netværk
Den næste Konfigurationsopgave er at [oprette eller vælge en Azure virtuelt netværk](active-directory-ds-getting-started-vnet.md).
