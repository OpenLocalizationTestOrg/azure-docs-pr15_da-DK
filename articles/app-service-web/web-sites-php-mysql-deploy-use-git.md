<properties
    pageTitle="Oprette en PHP MySQL WebApp i Azure App Service og installere ved hjælp af ciffer"
    description="Et selvstudium, der viser, hvordan du opretter en PHP WebApp, der gemmer dataene i MySQL og bruger ciffer installation til Azure."
    services="app-service\web"
    documentationCenter="php"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="mysql"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="create-a-php-mysql-web-app-in-azure-app-service-and-deploy-using-git"></a>Oprette en PHP MySQL WebApp i Azure App Service og installere ved hjælp af ciffer

Dette selvstudium viser, hvordan du opretter en PHP MySQL WebApp og hvordan du installerer det til [App-tjenesten](http://go.microsoft.com/fwlink/?LinkId=529714) ved hjælp af ciffer. Du skal bruge [PHP][install-php], værktøjet MySQL kommandolinjeparametre (en del af [MySQL][install-mysql]), og [ciffer] [ install-git] installeret på computeren. Vejledningen i dette selvstudium kan efterfølges af alle operativsystemer, herunder Windows, Mac og Linux. Når denne vejledning, har du en PHP/MySQL-web-app, der kører i Azure.

Du kan få mere at vide:

* Sådan oprettes en WebApp og en MySQL-database ved hjælp af [Azure Portal][management-portal]. Da PHP er aktiveret i [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) som standard, er noget speciel kræves for at køre din PHP kode.
* Sådan publicerer og publicere dit program til Azure ved hjælp af ciffer.
* Sådan aktiveres filtypenavnet komponist til at automatisere komponist opgaver på hver `git push`.

Ved at følge dette selvstudium, kan du oprette en enkel registrering WebApp i PHP. Programmet skal placeres i Web Apps. Et skærmbillede af det færdige program er nedenfor:

![Azure PHP-websted][running-app]

## <a name="set-up-the-development-environment"></a>Konfigurere udviklingsmiljøet

Dette selvstudium forudsætter, at du har [PHP][install-php], værktøjet MySQL kommandolinjeparametre (en del af [MySQL][install-mysql]), og [ciffer] [ install-git] installeret på computeren.

<a id="create-web-site-and-set-up-git"></a>
## <a name="create-a-web-app-and-set-up-git-publishing"></a>Oprette en web-app, og Konfigurer ciffer udgivelse

Følg disse trin for at oprette en WebApp og en MySQL-database:

1. Logge på [Azure Portal][management-portal].
2. Klik på ikonet **Ny** .

3. Klik på **Se alle** ud for **Marketplace**. 

4. Klik på **Web + Mobile**og derefter **Web app + MySQL**. Klik derefter på **Opret**.

4. Angiv et gyldigt navn til din ressourcegruppe.

    ![Angiv ressource gruppenavn][resource-group]

5. Angiv værdier for din nye online.

    ![Oprette WebApp][new-web-app]

6. Angiv værdier for den nye database, herunder accepterer at juridiske betingelser.

    ![Oprette nye MySQL-database][new-mysql-db]

7. Når webappen er blevet oprettet, vil du se bladet nye web app.

7. Klik på **Fortløbende installation**i **Indstillinger** og derefter klikke på _Konfigurer påkrævet indstillinger_.

    ![Konfigurer ciffer udgivelse][setup-publishing]

8. Vælg **Lokale ciffer lager** for kilden.

    ![Konfigurere ciffer lager][setup-repository]


9. Hvis du vil aktivere ciffer udgivelse, skal du angive et brugernavn og adgangskode. Skal du notere det brugernavn og adgangskode, du opretter. (Hvis du har konfigureret et ciffer lager før, ignoreres dette trin).

    ![Oprette publicering legitimationsoplysninger][credentials]


## <a name="get-remote-mysql-connection-information"></a>Få oplysninger om remote MySQL-forbindelsen

Oprette forbindelse til den MySQL-database, der kører på Web Apps, din, har du brug for forbindelsesoplysningerne. For at få MySQL forbindelsesoplysninger skal du følge disse trin:

1. Klik på databasen, fra din ressourcegruppe:

    ![Vælg database][select-database]

2. Databasen **Indstillinger**, Vælg **Egenskaber**.

    ![Vælg egenskaber][select-properties]

2. Noter værdierne for `Database`, `Host`, `User Id`, og `Password`.

    ![Bemærk egenskaber][note-properties]

## <a name="build-and-test-your-app-locally"></a>Opbygge og teste din app lokalt

Nu hvor du har oprettet en web-app, kan du udvikle dit program lokalt og derefter installere det efter testen.

Programmet registrering er en enkelt PHP-program, som gør det muligt at registrere for en begivenhed ved at indsende dit navn og e-mail-adresse. Oplysninger om tidligere registrerede vises i en tabel. Registreringsoplysninger er gemt i en MySQL-database. Programmet består af én fil (kopiere/indsætte kode tilgængelige nedenfor):

* **Index.php**: Viser en formular til registrering og en tabel, der indeholder registrantens side oplysninger.

Følg nedenstående trin for at oprette og køre programmet lokalt. Bemærk, at disse trin antager, du har PHP og MySQL kommandolinjeparametre værktøjet (en del af MySQL) konfigurere på din lokale computer, og at du har aktiveret [PDO udvidelse til MySQL][pdo-mysql].

1. Oprette forbindelse til fjernserveren i MySQL, ved hjælp af værdien for `Data Source`, `User Id`, `Password`, og `Database` , som du har hentet tidligere:

        mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2. MySQL kommandoprompten vises:

        mysql>

3. Indsæt følgende `CREATE TABLE` kommandoen til at oprette den `registration_tbl` tabel i databasen:

        CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4. Oprette **index.php** fil i roden af din lokale mappe.

5. Åbn filen **index.php** i et tekstredigeringsprogram eller IDE og tilføje følgende kode og udføre de nødvendige ændringer, der er markeret med `//TODO:` kommentarer.


        <html>
        <head>
        <Title>Registration Form</Title>
        <style type="text/css">
            body { background-color: #fff; border-top: solid 10px #000;
                color: #333; font-size: .85em; margin: 20; padding: 20;
                font-family: "Segoe UI", Verdana, Helvetica, Sans-Serif;
            }
            h1, h2, h3,{ color: #000; margin-bottom: 0; padding-bottom: 0; }
            h1 { font-size: 2em; }
            h2 { font-size: 1.75em; }
            h3 { font-size: 1.2em; }
            table { margin-top: 0.75em; }
            th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
            td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
        </style>
        </head>
        <body>
        <h1>Register here!</h1>
        <p>Fill in your name and email address, then click <strong>Submit</strong> to register.</p>
        <form method="post" action="index.php" enctype="multipart/form-data" >
              Name  <input type="text" name="name" id="name"/></br>
              Email <input type="text" name="email" id="email"/></br>
              <input type="submit" name="submit" value="Submit" />
        </form>
        <?php
            // DB connection info
            //TODO: Update the values for $host, $user, $pwd, and $db
            //using the values you retrieved earlier from the Azure Portal.
            $host = "value of Data Source";
            $user = "value of User Id";
            $pwd = "value of Password";
            $db = "value of Database";
            // Connect to database.
            try {
                $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
                $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            }
            catch(Exception $e){
                die(var_dump($e));
            }
            // Insert registration info
            if(!empty($_POST)) {
            try {
                $name = $_POST['name'];
                $email = $_POST['email'];
                $date = date("Y-m-d");
                // Insert data
                $sql_insert = "INSERT INTO registration_tbl (name, email, date)
                           VALUES (?,?,?)";
                $stmt = $conn->prepare($sql_insert);
                $stmt->bindValue(1, $name);
                $stmt->bindValue(2, $email);
                $stmt->bindValue(3, $date);
                $stmt->execute();
            }
            catch(Exception $e) {
                die(var_dump($e));
            }
            echo "<h3>Your're registered!</h3>";
            }
            // Retrieve data
            $sql_select = "SELECT * FROM registration_tbl";
            $stmt = $conn->query($sql_select);
            $registrants = $stmt->fetchAll();
            if(count($registrants) > 0) {
                echo "<h2>People who are registered:</h2>";
                echo "<table>";
                echo "<tr><th>Name</th>";
                echo "<th>Email</th>";
                echo "<th>Date</th></tr>";
                foreach($registrants as $registrant) {
                    echo "<tr><td>".$registrant['name']."</td>";
                    echo "<td>".$registrant['email']."</td>";
                    echo "<td>".$registrant['date']."</td></tr>";
                }
                echo "</table>";
            } else {
                echo "<h3>No one is currently registered.</h3>";
            }
        ?>
        </body>
        </html>

4.  Gå til programmappen i en terminal og skrive følgende kommando:

        php -S localhost:8000

Du kan nu gå til **http://localhost:8000 /** til at afprøve programmet.


## <a name="publish-your-app"></a>Publicere din app

Når du har kontrolleret din app lokalt, kan du publicere det i Web Apps ved hjælp af ciffer. Du kan initialiseret din lokale ciffer lager og publicere programmet.

> [AZURE.NOTE]
> Dette er de samme trin, der vises i portalen Azure i slutningen af Opret en WebApp og angive op ciffer publicering ovenfor.

1. (Valgfrit)  Hvis du har glemt eller forkert placerede din ciffer remote repostitory URL-adresse, skal du gå til egenskaberne web app på Azure-portalen.

1. Åbne GitBash (eller en terminal, hvis ciffer i din `PATH`), skifte til dit program rodmappe, og Kør følgende kommandoer:

        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master

    Du bliver bedt om for den adgangskode, du oprettede tidligere.

    ![Indledende opslagsnål til Azure via ciffer][git-initial-push]

2. Gå til **http://[site name].azurewebsites.net/index.php** til at begynde at bruge programmet (disse oplysninger gemmes på din konto-dashboard):

    ![Azure PHP-websted][running-app]

Når du har udgivet din app, kan du begynde at foretage ændringer af den og bruge ciffer til at publicere dem.

## <a name="publish-changes-to-your-app"></a>Publicere ændringer af din app

For at publicere ændringer af din app, skal du følge disse trin:

1. Foretage ændringer i din app lokalt.
2. Åbne GitBash (eller en terminal it ciffer er i din `PATH`), skifte til din app rodmappe, og Kør følgende kommandoer:

        git add .
        git commit -m "comment describing changes"
        git push azure master

    Du bliver bedt om for den adgangskode, du oprettede tidligere.

    ![Skubbe websted ændres til Azure via ciffer][git-change-push]

3. Gå til **http://[site name].azurewebsites.net/index.php** at se din app og de ændringer, du har foretaget:

    ![Azure PHP-websted][running-app]

>[AZURE.NOTE] Hvis du vil komme i gang med Azure App Service før tilmelding til en Azure-konto, skal du gå til [Prøve App Service](http://go.microsoft.com/fwlink/?LinkId=523751), hvor du straks kan oprette en forbigående starter WebApp i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.

<a name="composer"></a>
## <a name="enable-composer-automation-with-the-composer-extension"></a>Aktivere komponist automatisering med filtypenavnet komponist

Som standard gøre ikke installationsprocessen ciffer i App-tjeneste noget med composer.json, hvis du har en i projektet PHP. Du kan aktivere composer.json behandling under `git push` ved at aktivere filtypenavnet komponist.

1. I din PHP web Apps blade i [Azure portal][management-portal], klik på **værktøjer** > **filtypenavne**.

    ![Komponist lokalnummer indstillinger][composer-extension-settings]

2. Klik på **Tilføj**, og klik derefter på **Komponist**.

    ![Tilføje komponist lokalnummer][composer-extension-add]
    
3. Klik på **OK** for at acceptere juridiske betingelser. Klik på **OK** igen for at tilføje filtypenavnet.

    Bladet **installerede udvidelser** vises nu i komponist udvidelse.  
    ![Komponist lokalnummer visning][composer-extension-view]
    
4. Nu udføre `git add`, `git commit`, og `git push` som i forrige afsnit. Nu får du vist, komponist installerer afhængigheder, der er defineret i composer.json.

    ![Komponist lokalnummer succes][composer-extension-success]

## <a name="next-steps"></a>Næste trin

Du kan finde yderligere oplysninger finder [PHP Developer Center](/develop/php/).

<!-- URL List -->

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[install-mysql]: http://dev.mysql.com/downloads/mysql/
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[management-portal]: https://portal.azure.com
[sql-database-editions]: http://msdn.microsoft.com/library/windowsazure/ee621788.aspx

<!-- IMG List -->

[running-app]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-git/new_website2.png
[custom-create]: ./media/web-sites-php-mysql-deploy-use-git/create_web_mysql.png
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-git/create_db.png
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-git/select_webapp.png
[setup-git-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-mysql-deploy-use-git/save_credentials.png
[resource-group]: ./media/web-sites-php-mysql-deploy-use-git/set_group.png
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-git/create_wa.png
[setup-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_deploy.png
[setup-repository]: ./media/web-sites-php-mysql-deploy-use-git/select_local_git.png
[select-database]: ./media/web-sites-php-mysql-deploy-use-git/select_database.png
[select-properties]: ./media/web-sites-php-mysql-deploy-use-git/select_properties.png
[note-properties]: ./media/web-sites-php-mysql-deploy-use-git/note-properties.png

[git-instructions]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png
[git-change-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png
[git-initial-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png

[composer-extension-settings]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-settings.png
[composer-extension-add]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-add.png
[composer-extension-view]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-view.png
[composer-extension-success]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-success.png
