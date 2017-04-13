<properties 
    pageTitle="Oprette en PHP MySQL WebApp i Azure App Service og installere ved hjælp af FTP" 
    description="Et selvstudium, der viser, hvordan du opretter en PHP WebApp, der lagrer data i MySQL og bruge FTP-installation til Azure." 
    services="app-service\web" 
    documentationCenter="php" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>


#<a name="create-a-php-mysql-web-app-in-azure-app-service-and-deploy-using-ftp"></a>Oprette en PHP MySQL WebApp i Azure App Service og installere ved hjælp af FTP

Dette selvstudium viser, hvordan du opretter en PHP MySQL WebApp og hvordan du kan installere det ved hjælp af FTP. Dette selvstudium forudsætter, at du har [PHP][install-php], [MySQL][install-mysql], en webserver, og en FTP-klient, der er installeret på computeren. Vejledningen i dette selvstudium kan efterfølges af alle operativsystemer, herunder Windows, Mac og Linux. Når denne vejledning, har du en PHP/MySQL-web-app, der kører i Azure.
 
Du kan få mere at vide:

* Sådan oprettes en WebApp og en MySQL-database ved hjælp af portalen Azure. Da PHP er aktiveret i Web Apps som standard, er noget speciel kræves for at køre din PHP kode.
* Hvordan du publicerer dit program til Azure ved hjælp af FTP.
 
Ved at følge dette selvstudium, kan du oprette en enkel registrering WebApp i PHP. Programmet skal placeres i en Web App. Et skærmbillede af det færdige program er nedenfor:

![Azure PHP-websted][running-app]

>[AZURE.NOTE] Hvis du vil komme i gang med Azure App Service før tilmelding til en konto, skal du gå til [Prøve App Service](http://go.microsoft.com/fwlink/?LinkId=523751), hvor du straks kan oprette en forbigående starter WebApp i App-tjeneste. Ingen kreditkort er påkrævet, ingen forpligtelser. 


##<a name="create-a-web-app-and-set-up-ftp-publishing"></a>Oprette en WebApp og konfigurere FTP-udgivelse

Følg disse trin for at oprette en WebApp og en MySQL-database:

1. Logge på [Azure Portal][management-portal].
2. Klik på ikonet **+ Ny** øverst til venstre på portalen Azure.

    ![Oprette nyt Azure-websted][new-website]

3. Skriv **Online + MySQL** i feltet Søg, og klik på **WebApp + MySQL**.

    ![Brugerdefineret Opret et nyt websted][custom-create]

4. Klik på **Opret**. Angiv et entydigt app service name, et gyldigt navn til ressourcegruppen og en ny tjeneste plan.

    ![Angiv ressource gruppenavn][resource-group]


6. Angiv værdier for den nye database, herunder accepterer at juridiske betingelser.

    ![Oprette nye MySQL-database][new-mysql-db]
    
7. Når webappen er blevet oprettet, vil du se bladet nye app-tjenesten.


6. Klik på **Indstillinger** > **installation legitimationsoplysninger**. 

    ![Angiv installation legitimationsoplysninger][set-deployment-credentials]

7. Hvis du vil aktivere FTP-udgivelse, skal du angive et brugernavn og adgangskode. Gem legitimationsoplysningerne, og notere det brugernavn og adgangskode, du opretter.

    ![Oprette publicering legitimationsoplysninger][portal-ftp-username-password]

##<a name="build-and-test-your-app-locally"></a>Opbygge og teste din app lokalt

Programmet registrering er en enkelt PHP-program, som gør det muligt at registrere for en begivenhed ved at indsende dit navn og e-mail-adresse. Oplysninger om tidligere registrerede vises i en tabel. Registreringsoplysninger er gemt i en MySQL-database. Appen består af to filer:

* **Index.php**: Viser en formular til registrering og en tabel, der indeholder registrantens side oplysninger.
* **CreateTable.php**: opretter tabellen MySQL for programmet. Denne fil kan kun bruges én gang.

Følg nedenstående trin for at oprette og køre appen lokalt. Bemærk, at disse trin antager, du har PHP, MySQL, og en webserver, der er installeret på din lokale computer, og som du har aktiveret [PDO udvidelse til MySQL][pdo-mysql].

1. Oprette en MySQL-database, der hedder `registration`. Du kan gøre dette fra kommandoprompten MySQL med denne kommando:

        mysql> create database registration;

2. Oprette en mappe med navnet i din webserver rodmappen, `registration` og oprette to filer i det - en kaldet `createtable.php` og én kaldet `index.php`.

3. Åbn den `createtable.php` filer i et tekstredigeringsprogram eller IDE og tilføje nedenstående kode. Denne kode bruges til at oprette den `registration_tbl` tabel i den `registration` database.

        <?php
        // DB connection info
        $host = "localhost";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        try{
            $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            $sql = "CREATE TABLE registration_tbl(
                        id INT NOT NULL AUTO_INCREMENT, 
                        PRIMARY KEY(id),
                        name VARCHAR(30),
                        email VARCHAR(30),
                        date DATE)";
            $conn->query($sql);
        }
        catch(Exception $e){
            die(print_r($e));
        }
        echo "<h3>Table created.</h3>";
        ?>

    > [AZURE.NOTE] 
    > Du skal opdatere værdierne for <code>$user</code> og <code>$pwd</code> med dit lokale MySQL-brugernavn og din adgangskode.

4. Åbn en webbrowser og gå til [http://localhost/registration/createtable.php][localhost-createtable]. Dette vil oprette den `registration_tbl` tabel i databasen.

5. Åbn filen **index.php** i et tekstredigeringsprogram eller IDE og tilføje den grundlæggende HTML og CSS-kode for siden (PHP koden, føjes i senere trin).

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

        ?>
        </body>
        </html>

6. Tilføje PHP kode til at oprette forbindelse til databasen i mærker PHP.

        // DB connection info
        $host = "localhost";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        // Connect to database.
        try {
            $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
        }
        catch(Exception $e){
            die(var_dump($e));
        }

    > [AZURE.NOTE]
    > Igen, skal du opdatere værdierne for <code>$user</code> og <code>$pwd</code> med dit lokale MySQL-brugernavn og din adgangskode.

7. Efter koden forbindelse skal du tilføje kode til at indsætte registreringsoplysninger i databasen.

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

8. Til sidst skal følge ovenstående kode, tilføje kode for at hente data fra database.

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

Du kan nu gå til [http://localhost/registration/index.php] [ localhost-index] til at teste appen.

##<a name="get-mysql-and-ftp-connection-information"></a>Få oplysninger om MySQL og FTP-forbindelse

Oprette forbindelse til den MySQL-database, der kører på Web Apps, din, har du brug for forbindelsesoplysningerne. For at få MySQL forbindelsesoplysninger skal du følge disse trin:

1. Fra tjenesten app web app blade ved at klikke på linket ressource gruppe:

    ![Vælg ressourcegruppe][select-resourcegroup]

1. Klik på databasen, fra din ressourcegruppe:

    ![Vælg database][select-database]

2. Vælg **Indstillinger**fra databasen oversigt, > **Egenskaber**.

    ![Vælg egenskaber][select-properties]
    
2. Noter værdierne for `Database`, `Host`, `User Id`, og `Password`.

    ![Bemærk egenskaber][note-properties]

3. Klik på linket **Download publicere profil** til nederste højre hjørne på siden fra din online:

    ![Hent publicere profil][download-publish-profile]

4. Åbn den `.publishsettings` fil i en XML-editor. 

3. Finde den `<publishProfile >` element med `publishMethod="FTP"` , der ser nogenlunde sådan ud:

        <publishProfile publishMethod="FTP" publishUrl="ftp://[mysite].azurewebsites.net/site/wwwroot" ftpPassiveMode="True" userName="[username]" userPWD="[password]" destinationAppUrl="http://[name].antdf0.antares-test.windows-int.net" 
            ...
        </publishProfile>
    
Noter på `publishUrl`, `userName`, og `userPWD` attributter.

##<a name="publish-your-app"></a>Publicere din app

Når du har kontrolleret din app lokalt, kan du publicere den til din online ved hjælp af FTP. Skal du først at opdatere oplysningerne om databaseforbindelsen i programmet på computeren. Brug af oplysningerne om databaseforbindelsen du har hentet tidligere (i **få MySQL og FTP-forbindelsesoplysninger** sektionen), opdatere følgende oplysninger i **både** den `createdatabase.php` og `index.php` filer med de tilsvarende værdier:

    // DB connection info
    $host = "value of Data Source";
    $user = "value of User Id";
    $pwd = "value of Password";
    $db = "value of Database";

Du er nu klar til at publicere din app, ved hjælp af FTP.

1. Åbn din FTP-klient efter valg.

2. Angiv den *host name del* fra den `publishUrl` attribut, du er nævnt ovenfor i din FTP-klient.

3. Angiv den `userName` og `userPWD` attributter, du er nævnt ovenfor uændret til din FTP-klient.

4. Oprette en forbindelse.

Når du har forbundet vil du kunne uploade og downloade filer efter behov. Sørg for, at du overfører filer til rodmappen, som er `/site/wwwroot`.

Når du har overført begge `index.php` og `createtable.php`, gå til **http://[site name].azurewebsites.net/createtable.php** til at oprette tabellen MySQL for programmet og derefter gå til **http://[site name].azurewebsites.net/index.php** til at begynde at bruge programmet.
 
## <a name="next-steps"></a>Næste trin

Du kan finde yderligere oplysninger finder [PHP Developer Center](/develop/php/).

[install-php]: http://www.php.net/manual/en/install.php
[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[localhost-createtable]: http://localhost/tasklist/createtable.php
[localhost-index]: http://localhost/tasklist/index.php
[running-app]: ./media/web-sites-php-mysql-deploy-use-ftp/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-ftp/new_website2.png
[custom-create]: ./media/web-sites-php-mysql-deploy-use-ftp/create_web_mysql.png
[website-details]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-ftp/create_db.png
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-ftp/select_webapp.png
[set-deployment-credentials]: ./media/web-sites-php-mysql-deploy-use-ftp/set_credentials.png
[portal-ftp-username-password]: ./media/web-sites-php-mysql-deploy-use-ftp/save_credentials.png
[resource-group]: ./media/web-sites-php-mysql-deploy-use-ftp/set_group.png
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-ftp/create_wa.png
[select-database]: ./media/web-sites-php-mysql-deploy-use-ftp/select_database.png
[select-resourcegroup]: ./media/web-sites-php-mysql-deploy-use-ftp/select_resourcegroup.png
[select-properties]: ./media/web-sites-php-mysql-deploy-use-ftp/select_properties.png
[note-properties]: ./media/web-sites-php-mysql-deploy-use-ftp/note-properties.png

[connection-string-info]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/connection_string_info.png
[management-portal]: https://portal.azure.com
[download-publish-profile]: ./media/web-sites-php-mysql-deploy-use-ftp/download_publish_profile_3.png
 
