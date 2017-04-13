<properties
    pageTitle="Oprette og oprette forbindelse til en MySQL-database i Azure"
    description="Lær at bruge Azure-portalen til at oprette en MySQL-database og derefter oprette forbindelse til den fra en PHP WebApp i Azure."
    documentationCenter="php"
    services="app-service\web"
    authors="cephalin"
    manager="wpickett"
    editor=""
    tags="mysql"/>

<tags
    ms.service="multiple"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm;cephalin"/>

# <a name="create-and-connect-to-a-mysql-database-in-azure"></a>Oprette og oprette forbindelse til en MySQL-database i Azure

Dette selvstudium viser, hvordan du opretter en MySQL-database i [Azure-portalen](https://portal.azure.com) (udbyder er [ClearDB](http://www.cleardb.com/)), og hvordan du opretter forbindelse til den fra en PHP WebApp, der kører i [Azure App Service](./app-service/app-service-value-prop-what-is.md). 

> [AZURE.NOTE] Du kan også oprette en MySQL-database som en del af en [skabelon til app Marketplace](./app-service-web/app-service-web-create-web-app-from-marketplace.md).

## <a name="create-a-mysql-database-in-azure-portal"></a>Oprette en MySQL-database i Azure-portalen

Hvis du vil oprette en MySQL-database i portalen Azure, skal du gøre følgende:

1. Log på [Azure-portalen](https://portal.azure.com).

2. Klik på **Ny**fra menuen til venstre, > **Data + lagerplads** > **MySQL-Database**.

    ![Oprette en MySQL-database i Azure - start](./media/store-php-create-mysql-database/create-db-1-start.png)

2. I den nye MySQL-Database [blade](azure-portal-overview.md), konfigurere den nye MySQL-database på følgende måde (*blade*: en portal-side, der åbnes vandret):

    - **Databasenavn**: Skriv et identificeres entydigt navn
    - **Abonnement**: Vælg abonnementet, du bruger
    - **Database Type**: Vælg **delt** til lave omkostninger eller gratis niveauer eller **dedikeret** for at få dedikeret ressourcer. 
    - **Ressourcegruppe**: tilføje MySQL-database til en eksisterende [ressourcegruppe](../azure-resource-manager/resource-group-overview.md) eller placere det i en ny. Ressourcer i den samme gruppe kan nemt administreres sammen.
    - **Placering**: Vælg en placering tæt du. Når du føjer til en eksisterende ressourcegruppe, er du låst til ressourcegruppen placering.
    - **Priser niveau**: Klik på **Priser niveau**og derefter vælge en indstilling for priser (**kviksølv** niveau er gratis), og klik derefter på **Vælg**. 
    - **Juridiske begreber**: Klik på **Juridiske begreber**, gennemgå oplysningerne køb, og klik på **Køb**.
    - **Fastgør til dashboard**: Vælg, hvis du vil have adgang til den direkte fra dashboardet. Dette er især nyttig, hvis du ikke kender portalen navigation endnu.
    
    Følgende skærmbillede er bare et eksempel på, hvordan du kan konfigurere din MySQL-database.  
    ![Oprette en MySQL-database i Azure - konfigurere](./media/store-php-create-mysql-database/create-db-2-configure.png)

3. Når du er færdig, klik på **Opret**.

    ![Opret en MySQL-database i Azure - Opret](./media/store-php-create-mysql-database/create-db-3-create.png)

    Du får vist en tilladelsen til pop op-meddelelser, at du ved, at installation er startet.

    ![Oprette en MySQL-database i Azure - igangværende](./media/store-php-create-mysql-database/create-db-4-started-status.png)

    Får du en anden pop op-når installationen er fuldført. Portalen kan også åbne din MySQL-database blade automatisk.

<a name="connect"></a>
## <a name="connect-to-your-mysql-database"></a>Oprette forbindelse til din MySQL-database

For at se forbindelsesoplysningerne for den nye MySQL-database skal du blot klikke på **Egenskaber** i din online blade.
    
![Oprette en MySQL-database i Azure - MySQL-database blade](./media/store-php-create-mysql-database/create-db-5-finished-db-blade.png)

Du kan nu bruge pågældende forbindelsesoplysningerne i en hvilken som helst WebApp. Et eksempel, der viser, hvordan du bruger forbindelsesoplysninger fra en simpel PHP app er tilgængelig [her](https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-mysql).

## <a name="connect-a-laravel-web-app-from-the-php-get-started-tutorial"></a>Forbinde en Laravel online (fra PHP få Introduktion selvstudium)

Antag, at du lige er afsluttet selvstudium [oprette, konfigurere og installere en PHP WebApp til Azure](./app-service-web/app-service-web-php-get-started.md) og har en [Laravel](https://www.laravel.com/) WebApp, der kører i Azure. Du kan nemt tilføje Databasefunktioner i din Laravel app. Ved at følge nedenstående trin:

>[AZURE.NOTE] Følgende trin forudsætter, at du er færdig med selvstudium [oprette, konfigurere og installere en PHP WebApp til Azure](./app-service-web/app-service-web-php-get-started.md).

1. Konfigurere Laravel app'en i din lokale udviklingsmiljø til at pege på MySQL-database. Du kan åbne `.env` fra din Laravel app rodmappen og konfigurere indstillingerne MySQL-database.

        DB_CONNECTION=mysql
        DB_HOST=<HOSTNAME_from_properties_blade>
        DB_PORT=<PORT_from_properties_blade>
        DB_DATABASE=<see_note_below>
        DB_USERNAME=<USERNAME_from_properties_blade>
        DB_PASSWORD=<PASSWORD_from_properties_blade>

    >[AZURE.NOTE] Navnet på din MySQL-database kan eller muligvis ikke den, der vises i feltet **DATABASENAVN** i bladet **Egenskaber** . Det er bedre til at kontrollere parameteren Database i feltet **FORBINDELSESSTRENG** . 
    >
    >![Oprette en MySQL-database i Azure - igangværende](./media/store-php-create-mysql-database/connect-db-1-database-name.png)

2. Den hurtigste måde at bekræfte, at du har MySQL adgang nu er at bruge [Laravels standard godkendelse stilladser](https://laravel.com/docs/5.2/authentication#authentication-quickstart). I kommandolinjen terminal skal du køre følgende kommandoer fra din Laravel app rodmappe:

         php artisan migrate
         php artisan make:auth

    Den første kommando opretter tabellerne i Azure baseret på foruddefinerede overførsler i den `database/migrations` directory og den anden kommando scaffolds grundlæggende visninger og omdirigerer til brugerregistrering og godkendelse.

3. Kør udvikling serveren nu:

        php artisan serve

4. Gå til http://localhost:8000 og registrere en ny bruger, som vist i browseren:

    ![Oprette forbindelse til MySQL-database i Azure - registrere bruger](./media/store-php-create-mysql-database/connect-db-2-development-server.png)

    Følg Brugergrænsefladen prompten fuldført registreringen. Når registrering er fuldført, skal du være logget på.
    
    ![Oprette forbindelse til MySQL-database i Azure - registrere bruger](./media/store-php-create-mysql-database/connect-db-3-registered-user.png)

    Du nu udvikling af din app mod MySQL-database i Azure.

5. Nu, skal du bare gentage din `.env` indstillinger til din Azure online. Kør følgende Azure CLI kommandoer:

        azure site appsetting add DB_CONNECTION=mysql
        azure site appsetting add DB_HOST=<HOSTNAME_from_properties_blade>
        azure site appsetting add DB_PORT=<PORT_from_properties_blade>
        azure site appsetting add DB_DATABASE=<Database_param_from_CONNECTION_INFO_from_properties_blade>
        azure site appsetting add DB_USERNAME=<USERNAME_from_properties_blade>
        azure site appsetting add DB_PASSWORD=<PASSWORD_from_properties_blade>

    Finde ud af, hvordan det fungerer i [konfigurere Azure WebApp](./app-service-web/app-service-web-php-get-started.md#configure).

6. Derefter skal du acceptere og overføre til Azure lokale ændringer tidligere under kørsel `php artisan make:auth`.

        git add .
        git commit -m "scaffold auth views and routes"
        git push azure master

7. Gå til Azure WebApp.

        azure site browse

8. Log på med de brugerlegitimationsoplysninger, du oprettede tidligere.

    ![Oprette forbindelse til MySQL-database i Azure - gå til Azure WebApp](./media/store-php-create-mysql-database/connect-db-4-browse-azure-webapp.png)

    Når du logger på, skal du se det fulde efter logonskærmen.
    
    ![Oprette forbindelse til MySQL-database i Azure - logget på](./media/store-php-create-mysql-database/connect-db-5-logged-in.png)

    Tillykke, din PHP online i Azure nu åbner data fra MySQL-database. 

## <a name="next-steps"></a>Næste trin

Du kan finde yderligere oplysninger finder [PHP Developer Center](/develop/php/).
