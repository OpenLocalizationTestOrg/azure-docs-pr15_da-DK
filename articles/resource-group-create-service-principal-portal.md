<properties
   pageTitle="Oprette tjeneste vigtigste i portalen | Microsoft Azure"
   description="I denne artikel beskrives, hvordan du opretter et nyt Active Directory-program og service-konto, som kan bruges sammen med rollebaseret adgangskontrollen i Azure ressourcestyring til at administrere adgang til ressourcer."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/07/2016"
   ms.author="tomfitz"/>

# <a name="use-portal-to-create-active-directory-application-and-service-principal-that-can-access-resources"></a>Bruge portal til at oprette Active Directory-program og service-konto, som kan få adgang til ressourcer

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Azure CLI](resource-group-authenticate-service-principal-cli.md)
- [Portal](resource-group-create-service-principal-portal.md)


Når du har et program, der skal åbne eller redigere ressourcer, skal du konfigurere et program i Active Directory (AD) og tildele de nødvendige tilladelser til den. Dette emne beskrives, hvordan til at udføre disse trin til portalen. I øjeblikket, skal du bruge portalen klassisk til at oprette et nyt Active Directory-program, og derefter skifte til Azure portalen for at tildele en rolle til programmet. 

> [AZURE.NOTE] Trinnene i dette emne gælder kun, når du bruger **Klassisk portal** til at oprette AD-program. **Hvis du bruger portalen Azure til oprettelse af programmet AD, lykkes ikke disse trin.** 
>
> Du kan finde det nemmere at konfigurere dine AD-program og tjeneste, der er primære via [PowerShell](resource-group-authenticate-service-principal.md) eller [Azure CLI](resource-group-authenticate-service-principal-cli.md), især hvis du vil bruge et certifikat til godkendelse. Sådan bruger du et certifikat vises ikke i dette emne.

En forklaring af Active Directory begreber, skal du se [program og Service hovedstolen objekter](./active-directory/active-directory-application-objects.md). Du kan finde flere oplysninger om Active Directory-godkendelse [Godkendelse scenarier, hvor Azure AD](./active-directory/active-directory-authentication-scenarios.md).

Detaljeret vejledning under integrere et program i Azure til administration af ressourcer, skal du se [Developer's guide til godkendelse med Azure ressourcestyring API](resource-manager-api-authentication.md).

## <a name="create-an-active-directory-application"></a>Oprette en Active Directory-program

1. Log på kontoen Azure via [Klassisk portal](https://manage.windowsazure.com/). 

2. Kontrollér, at du kender standard Active Directory for dit abonnement. Du kan kun give adgang til programmer i den samme mappe som dit abonnement. Vælg **Indstillinger** , og se efter navnet på den mappe, der er knyttet til dit abonnement.  Se, [hvordan Azure abonnementer er knyttet til Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md), kan finde flere oplysninger.
   
     ![finde standardmappen](./media/resource-group-create-service-principal-portal/show-default-directory.png)

2. Vælg **Active Directory** i venstre rude.

     ![Vælg Active Directory](./media/resource-group-create-service-principal-portal/active-directory.png)
     
3. Vælg Active Directory, du vil bruge til oprettelse af programmet. Hvis du har mere end én Active Directory, kan du oprette programmet i standardmappen for dit abonnement.   

     ![Vælg mappe](./media/resource-group-create-service-principal-portal/active-directory-details.png)
     
3. Vælg **programmer**for at få vist programmerne i mappen.

     ![få vist programmer](./media/resource-group-create-service-principal-portal/view-applications.png)

4. Hvis du ikke har oprettet et program i mappen før, skal du se noget svarende til følgende billede. Vælg **Tilføj et program**

     ![tilføje program](./media/resource-group-create-service-principal-portal/create-application.png)

     Eller klik på **Tilføj** i den nederste rude.

     ![tilføje](./media/resource-group-create-service-principal-portal/add-icon.png)

5. Vælg typen program, du vil oprette. Dette selvstudium skal du vælge **Føj et program, udvikling af min organisation**. 

     ![nyt program](./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png)

6. Angiv et navn til programmet, og vælg typen program, du vil oprette. Oprette en **WEB APPLICATION og/eller WEB API** og klikke på knappen Næste dette selvstudium. Hvis du vælger **Oprindelige KLIENTPROGRAMMET**, vil de resterende trin i denne artikel ikke svarer til din oplevelse.

     ![navn program](./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png)

7. Udfyld egenskaberne for din app. **Log på URL-adressen**til at give URI til et websted, der beskriver dit program. Eksistensen af webstedet valideres ikke. Angiv den URI, der identificerer dit program til **APP ID URI**.

     ![Egenskaber for tjenesteprogram](./media/resource-group-create-service-principal-portal/app-properties.png)

Du har oprettet dit program.

## <a name="get-client-id-and-authentication-key"></a>Få klient-id og godkendelse

Når logføring fra et program i, skal du id'et for dit program. Hvis programmet kører under sin egen legitimationsoplysninger, skal du også en godkendelsesnøgle.

1. Vælg fanen **Konfigurer** at konfigurere dit program adgangskode.

     ![konfigurere program](./media/resource-group-create-service-principal-portal/application-configure.png)

2. Kopiere **klient-ID**.
  
     ![klient-id](./media/resource-group-create-service-principal-portal/client-id.png)

3. Hvis programmet kører under sin egen legitimationsoplysninger, Rul ned til sektionen **nøgler** og vælge, hvor lang tid du vil have din adgangskode skal gælde.

     ![Taster](./media/resource-group-create-service-principal-portal/create-key.png)

4. Vælg **Gem** til at oprette din nøgle.

     ![Gem](./media/resource-group-create-service-principal-portal/save-icon.png)

     Den gemte nøgle vises, og du kan kopiere den. Du kan ikke hente tasten senere så kopiere det nu.

     ![gemt nøgle](./media/resource-group-create-service-principal-portal/save-key.png)

## <a name="get-tenant-id"></a>Hente lejer id

Når du logger fra et program, skal du overfører lejer-id med din anmodning om godkendelse. Du kan hente lejer id for Web Apps og Web API Apps, ved at vælge **Vis slutpunkter** nederst på skærmen og hentning af id, som vist på følgende billede.  

   ![lejer-id](./media/resource-group-create-service-principal-portal/save-tenant.png)

Du kan også hente-id lejer via PowerShell:

    Get-AzureRmSubscription

Eller Azure CLI:

    azure account show --json

## <a name="set-delegated-permissions"></a>Angive delegerede tilladelser

Hvis dit program har adgang til ressourcer på vegne af en bruger, der er logget på, skal du give dit program tilladelse til at få adgang til andre programmer. Du tildeler denne adgang i sektionen **tilladelser til andre programmer** under fanen **Konfigurer** . Som standard er en delegeret tilladelse allerede aktiveret til Azure Active Directory. Lad denne delegeret tilladelse være uændret.

1. Vælg **Tilføj program**.

2. Vælg **Windows Azure Service Management API**på listen. Vælg derefter ikonet fuldført.

      ![Vælg app](./media/resource-group-create-service-principal-portal/select-app.png)

3. Vælg **Access Azure Service Management som organisation**på rullelisten delegeret tilladelser.

      ![Vælg tilladelse](./media/resource-group-create-service-principal-portal/select-permissions.png)

4. Gemme ændringen.

## <a name="assign-application-to-role"></a>Tildele program til rolle

Hvis dit program kører under sin egen legitimationsoplysninger, skal du tildele programmet til en rolle. Bestem, hvilken administratorrolle, der repræsenterer de rette tilladelser til programmet. For at få mere for at vide om de tilgængelige roller, se [RBAC: indbygget i roller](./active-directory/role-based-access-built-in-roles.md). 

Hvis du vil tildele en rolle til et program, skal du har de rette tilladelser. Nærmere betegnet kan du som minimum have `Microsoft.Authorization/*/Write` access, der tildeles via [ejer](./active-directory/role-based-access-built-in-roles.md#owner) rollen eller [bruger Access](./active-directory/role-based-access-built-in-roles.md#user-access-administrator) -administratorrolle. Rollen Bidragyder har ikke adgang til det korrekte.

Du kan angive omfanget på niveauet i det abonnement, ressourcegruppe eller ressource. Tilladelser nedarves til lavere niveauer af omfang. For eksempel at føje et program til rollen Læser for en ressourcegruppe betyder, at det kan læse ressourcegruppen og de ressourcer, den indeholder.

1. Skifte fra portalen klassisk til [Azure portal](https://portal.azure.com)for at tildele programmet til en rolle.

1. Kontrollér dine tilladelser for at sikre, at du kan tildele service hovedstolen til en rolle. Vælg **Mine tilladelser** for din konto.

    ![Vælg Mine tilladelser](./media/resource-group-create-service-principal-portal/my-permissions.png)

1. Få vist de tildelte tilladelser for din konto. Som nævnt tidligere, skal du tilhører rollerne, der ejer eller Brugeradministrator Access eller har en tilpasset rolle, der giver skrive-adgang til Microsoft.Authorization. Følgende billede viser en konto, der er tildelt rollen bidragyder for det abonnement, som ikke er tilstrækkelige tilladelser til at tildele et program til en rolle.

    ![Vis mine tilladelser](./media/resource-group-create-service-principal-portal/show-permissions.png)

     Hvis du ikke har de rette tilladelser til at give adgang til et program, skal du enten anmodning om, at administratoren abonnement føjer dig til rollen Brugeradministrator adgang, eller anmode om, at giver en administrator adgang til programmet.

1. Gå til det niveau af område, du vil tildele programmet til. Hvis du vil tildele en rolle i området abonnement, skal du vælge **abonnementer**.

     ![Vælg abonnement](./media/resource-group-create-service-principal-portal/select-subscription.png)

     Vælg det bestemte abonnement tildele program til.

     ![Vælg abonnement for tildeling](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

     Vælg ikonet **adgang** i øverste højre hjørne.

     ![Vælg access](./media/resource-group-create-service-principal-portal/select-access.png)
     
     Eller hvis du vil tildele en rolle på ressource gruppen omfang, skal du gå til en ressourcegruppe. Vælg **adgangskontrol**bladet ressource gruppe.

     ![Vælg brugere](./media/resource-group-create-service-principal-portal/select-users.png)

     Følgende trin er de samme for en hvilken som helst omfang.

2. Vælg **Tilføj**.

     ![Vælg Tilføj](./media/resource-group-create-service-principal-portal/select-add.png)

3. Vælg rollen, **læser** (eller andet rolle, du vil tildele programmet til).

     ![Vælg rolle](./media/resource-group-create-service-principal-portal/select-role.png)

4. Når du først se listen over brugere, du kan føje til rollen, kan du ikke se programmer. Du får kun vist grupper og brugere.

     ![vise brugere](./media/resource-group-create-service-principal-portal/show-users.png)

5. Du kan finde dit program, skal du søge efter den. Begynd at skrive navnet på dit program, og listen over tilgængelige indstillinger vil ændre. Vælg dit program, når det vises på listen.

     ![tildele rolle](./media/resource-group-create-service-principal-portal/assign-to-role.png)

6. Vælg **OK** at afslutte tildeling af rollen. Du bør nu se dit program på listen tildelt til en rolle til ressourcegruppen formål.


Du kan finde flere oplysninger om at tildele brugere og programmer til roller via portalen [Brug rolletildelinger til at administrere adgang til dine Azure abonnementsressourcer](role-based-access-control-configure.md#manage-access-using-the-azure-management-portal).

## <a name="sample-applications"></a>Eksempler på programmer

Følgende eksempelprogrammer viser, hvordan du logge på som tjeneste hovedstolen.

**.NET**

- [Installere en SSH aktiveret VM med en skabelon med .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [Administrere Azure ressourcer og grupper med .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [Komme i gang med ressourcer - installere ved hjælp af Azure ressourcestyring skabelon - Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [Introduktion til ressourcer - administrere ressourcegruppe - i Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [Installere en SSH aktiveret VM med en skabelon i Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Administrere Azure ressource og ressourcegrupper med Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

- [Installere en SSH aktiveret VM med en skabelon i Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Administrere Azure ressourcer og grupper med Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Fonetisk**

- [Installere en SSH aktiveret VM med en skabelon i fonetisk](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [Administrere Azure ressource og ressourcegrupper med fonetisk](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)


## <a name="next-steps"></a>Næste trin

- Se [Azure rollebaseret adgangskontrol](./active-directory/role-based-access-control-configure.md)for at få mere for at vide om at angive sikkerhedspolitikker.  
- En videodemonstration af disse trin, skal du se [Aktivere programmeringsmæssig styring af en Azure-ressource med Azure Active Directory](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory).

