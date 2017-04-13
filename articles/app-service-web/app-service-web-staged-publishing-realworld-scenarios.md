<properties
  pageTitle="Bruge DevOps miljøer effektivt til din online"
  description="Lær, hvordan du bruger installation pladser til at konfigurere og administrere flere miljøer til softwareudvikling for dit program"
  services="app-service\web"
  documentationCenter=""
  authors="sunbuild"
  manager="yochayk"
  editor=""/>

<tags
  ms.service="app-service"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="web"
  ms.date="10/24/2016"
  ms.author="sumuth"/>

# <a name="use-devops-environments-effectively-for-your-web-apps"></a>Bruge DevOps miljøer effektivt til din online

I denne artikel viser dig til at konfigurere og administrere web application installationer for flere versioner af dit program som udvikling, arrangere QA og fremstilling. Hver version af dit program kan betragtes som udviklingsmiljø til bestemte brug af i installationsprocessen. QA-miljø kan for eksempel bruges af dit team af udviklere til at kontrollere kvaliteten af programmet, før du overføre ændringerne til fremstilling.
Konfiguration af flere udviklingsmiljøer kan være en udfordring opgave, som du vil have til at registrere, administrere ressourcer (Beregn online database, og cachen osv.) og installere kode på tværs af miljøer.

## <a name="setting-up-a-non-production-environment-stagedevqa"></a>Konfiguration af et ikke-produktionsmiljø (fase, Udviklingscenter, QA)
Når du har en fremstilling WebApp op at køre, er næste trin at oprette et ikke-produktionsmiljø. Hvis du vil bruge Sørg installation pladser for, at du kører i tilstanden **Standard** eller **Premium** App Service plan. Installation slot er virkeligheden findes online med deres egen værtsnavne. Web app-indhold og konfiguration elementer kan byttes mellem to installation pladser, herunder fremstilling slot. Implementering af dit program til en installation plads har følgende fordele:

1. Du kan kontrollere web app ændringer i en midlertidig installation slot før udskifte det med fremstilling slotten.
2. Installerer en web-app i en slot først, og Skift til fremstilling sikrer, at alle forekomster af slotten varmet op før blive skiftet til fremstilling. Dette fjerner nedetid, når du installerer din online. Trafik omdirigering er problemfri, og ingen anmodninger om udelades på grund af Byt om handlinger. Arbejdsprocessen hele kan automatisk ved at konfigurere [Automatisk Ombyt](web-sites-staged-publishing.md#configure-auto-swap-for-your-web-app) når foreløbig Udskift datavalidering ikke er nødvendigt.
3. Efter en Udskift har slot med tidligere faseinddelt online nu den forrige fremstilling WebApp. Hvis de ændringer, der er skiftet til fremstilling slot er ikke som forventet, kan du udføre de samme Udskift med det samme for at få din "sidste kendte gode online" tilbage.

For at konfigurere en midlertidig installation slot skal du se [konfigurere arrangere miljøer til webapps i Azure App Service](web-sites-staged-publishing.md). Hver miljø skal medtage sit eget sæt ressourcer, for eksempel hvis du web app bruges en database og derefter både fremstilling og midlertidige WebApp skal bruge forskellige databaser. Tilføj midlertidige udvikling miljø ressourcer som database, lagerplads eller cache for at indstille du dine midlertidige udviklingsmiljø.

## <a name="examples-of-using-multiple-development-environments"></a>Eksempler på brug af flere udviklingsmiljøer

Et projekt bør følge en kilde kode management med mindst to miljøer, et udvikling og fremstilling miljø, men når ved hjælp af styring af webindhold systemer, programmet strukturer osv. vi støde på problemer hvor programmet understøtter ikke dette scenario af feltet. Dette gælder for nogle af de populære strukturer, som beskrevet nedenfor. Masser af spørgsmål kommer til at huske, når du arbejder med en CMS strukturer f.eks.

1. Hvordan du skal bryde nedarvningen ud i forskellige miljøer
2. Hvilke filer kan jeg ændre og i stand til vil påvirke framework versionsopdateringer
3. Sådan administreres konfiguration per miljø
4. Sådan administreres moduler/plug-ins versionsopdateringer, core framework versionsopdateringer

Der findes mange måder at konfigurere et miljø med flere til dit projekt og eksemplerne nedenfor er en kun én sådan metode de respektive programmer.

### <a name="wordpress"></a>WordPress
I dette afsnit lærer du, hvordan du konfigurerer en installation arbejdsproces ved hjælp af pladser til WordPress. WordPress som de fleste CMS løsninger understøtter ikke arbejde med udviklingsmiljøer med flere af feltet. App Service Web Apps har nogle funktioner, som gør det nemmere at gemme konfigurationsindstillinger uden for din kode.

Før du opretter en midlertidig slot, du konfigurere din programkode til at understøtte flere miljøer. Til at understøtte flere miljøer i WordPress, skal du redigere `wp-config.php` Tilføj følgende kode i starten af filen på din lokale udvikling WebApp. Er giver mulighed for dit program til at vælge den korrekte konfiguration baseret på det valgte miljø.

```
// Support multiple environments
// set the config file based on current environment
if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) {
// local development
 $config_file = 'config/wp-config.local.php';
}
elseif ((strpos(getenv('WP_ENV'),'stage') !== false) || (strpos(getenv('WP_ENV'),'prod' )!== false ))
//single file for all azure development environments
 $config_file = 'config/wp-config.azure.php';
}
$path = dirname(__FILE__). '/';
if (file_exists($path. $config_file)) {
// include the config file if it exists, otherwise WP is going to fail
require_once $path. $config_file;
```

Oprette en mappe i web app-roden kaldet `config` og tilføje en fil to filer: `wp-config.azure.php` og `wp-config.local.php` henholdsvis der repræsenterer dit azure og lokale miljø.

Kopiere følgende i `wp-config.local.php` :

```
<?php
// MySQL settings
/** The name of the database for WordPress */

define('DB_NAME', 'yourdatabasename');

/** MySQL database username */
define('DB_USER', 'yourdbuser');

/** MySQL database password */
define('DB_PASSWORD', 'yourpassword');

/** MySQL hostname */
define('DB_HOST', 'localhost');
/**
 * For developers: WordPress debugging mode.
 * * Change this to true to enable the display of notices during development.
 * It is strongly recommended that plugin and theme developers use WP_DEBUG
 * in their development environments.
 */
define('WP_DEBUG', true);

//Security key settings
define('AUTH_KEY', 'put your unique phrase here');
define('SECURE_AUTH_KEY','put your unique phrase here');
define('LOGGED_IN_KEY','put your unique phrase here');
define('NONCE_KEY', 'put your unique phrase here');
define('AUTH_SALT', 'put your unique phrase here');
define('SECURE_AUTH_SALT', 'put your unique phrase here');
define('LOGGED_IN_SALT', 'put your unique phrase here');
define('NONCE_SALT', 'put your unique phrase here');

/**
 * WordPress Database Table prefix.
 *
 * You can have multiple installations in one database if you give each a unique
 * prefix. Only numbers, letters, and underscores please!
 */
$table_prefix = 'wp_';
```

Angive tasterne sikkerhed kan hjælpe med at forhindre, at din online blive hacket, så brug entydige værdier. Hvis du har brug for til at generere strengen til sikkerhed taster, der er nævnt ovenfor, kan du gå til den automatiske generator til at oprette nye nøgler/værdier ved hjælp af dette [link] (https://api.wordpress.org/secret-key/1.1/salt)

Kopier den følgende kode i `wp-config.azure.php`:


``` <?php
    // MySQL settings
    /** The name of the database for WordPress */
    
    define('DB_NAME', getenv('DB_NAME'));
    
    /** MySQL database username */
    define('DB_USER', getenv('DB_USER'));
    
    /** MySQL database password */
    define('DB_PASSWORD', getenv('DB_PASSWORD'));
    
    /** MySQL hostname */
    define('DB_HOST', getenv('DB_HOST'));
    
    /**
    * For developers: WordPress debugging mode.
    *
    * Change this to true to enable the display of notices during development.
    * It is strongly recommended that plugin and theme developers use WP_DEBUG
    * in their development environments.
    * Turn on debug logging to investigate issues without displaying to end user. For WP_DEBUG_LOG to
    * do anything, WP_DEBUG must be enabled (true). WP_DEBUG_DISPLAY should be used in conjunction
    * with WP_DEBUG_LOG so that errors are not displayed on the page */
    
    */
    define('WP_DEBUG', getenv('WP_DEBUG'));
    define('WP_DEBUG_LOG', getenv('TURN_ON_DEBUG_LOG'));
    define('WP_DEBUG_DISPLAY',false);
    
    //Security key settings
    /** If you need to generate the string for security keys mentioned above, you can go the automatic generator to create new keys/values: https://api.wordpress.org/secret-key/1.1/salt **/
    define('AUTH_KEY',getenv('DB_AUTH_KEY'));
    define('SECURE_AUTH_KEY', getenv('DB_SECURE_AUTH_KEY'));
    define('LOGGED_IN_KEY', getenv('DB_LOGGED_IN_KEY'));
    define('NONCE_KEY', getenv('DB_NONCE_KEY'));
    define('AUTH_SALT', getenv('DB_AUTH_SALT'));
    define('SECURE_AUTH_SALT', getenv('DB_SECURE_AUTH_SALT'));
    define('LOGGED_IN_SALT',  getenv('DB_LOGGED_IN_SALT'));
    define('NONCE_SALT',  getenv('DB_NONCE_SALT'));
    
    /**
    * WordPress Database Table prefix.
    *
    * You can have multiple installations in one database if you give each a unique
    * prefix. Only numbers, letters, and underscores please!
    */
    $table_prefix = getenv('DB_PREFIX');
```

#### <a name="use-relative-paths"></a>Bruge Relative stier
En sidste ting er at konfigurere WordPress app for at bruge relative stier. WordPress indeholder URL-adressen oplysninger i databasen. Dette gør flytte indhold fra et miljø til en anden sværere, som du har brug at opdatere databasen, hver gang du flytter fra lokale til fase eller fase for fremstilling miljøer. For at reducere risikoen for problemer, der kan opstå med installation af en database, hver gang du installerer fra et miljø til en anden, skal du bruge [Relative rod links plug-in](https://wordpress.org/plugins/root-relative-urls/) som kan installeres ved hjælp af WordPress administrator dashboard eller hente det manuelt fra [her](https://downloads.wordpress.org/plugin/root-relative-urls.zip).


Tilføj følgende poster til din `wp-config.php` filen, før den `That's all, stop editing!` kommentar:

```

  define('WP_HOME', 'http://'. filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
    define('WP_SITEURL', 'http://'. filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
    define('WP_CONTENT_URL', '/wp-content');
    define('DOMAIN_CURRENT_SITE', filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
```

Aktivere plug-in'et gennem den `Plugins` menuen i WordPress Administrator dashboard. Gem dine permalink indstillinger for WordPress app.

#### <a name="the-final-wp-configphp-file"></a>Endeligt `wp-config.php` fil
WordPress Core opdateringer påvirker ikke dine `wp-config.php`, `wp-config.azure.php` og `wp-config.local.php` filer. I sidste ende denne hvordan `wp-config.php` fil ser sådan ud

```
<?php
/**
 * The base configurations of the WordPress.
 *
 * This file has the following configurations: MySQL settings, Table Prefix,
 * Secret Keys, and ABSPATH. You can find more information by visiting
 *
 * Codex page. You can get the MySQL settings from your web host.
 *
 * This file is used by the wp-config.php creation script during the
 * installation. You don't have to use the web web app, you can just copy this file
 * to "wp-config.php" and fill in the values.
 *
 * @package WordPress
 */

// Support multiple environments
// set the config file based on current environment
if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) { // local development
  $config_file = 'config/wp-config.local.php';
}
elseif ((strpos(getenv('WP_ENV'),'stage') !== false) ||(strpos(getenv('WP_ENV'),'prod' )!== false )){
  $config_file = 'config/wp-config.azure.php';
}


$path = dirname(__FILE__). '/';
if (file_exists($path. $config_file)) {
  // include the config file if it exists, otherwise WP is going to fail
  require_once $path. $config_file;
}

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/* That's all, stop editing! Happy blogging. */

define('WP_HOME', 'http://'. $_SERVER['HTTP_HOST']);
define('WP_SITEURL', 'http://'. $_SERVER['HTTP_HOST']);
define('WP_CONTENT_URL', '/wp-content');
define('DOMAIN_CURRENT_SITE', $_SERVER['HTTP_HOST']);

/** Absolute path to the WordPress directory. */
if ( !defined('ABSPATH') )
    define('ABSPATH', dirname(__FILE__). '/');

/** Sets up WordPress vars and included files. */
require_once(ABSPATH. 'wp-settings.php');
```

#### <a name="set-up-a-staging-environment"></a>Konfigurere et midlertidige miljø
Hvis du allerede har en WordPress WebApp, der kører på Azure internettet, login [Azure administration Preview](http://portal.azure.com) -portalen, og gå til din WordPress online. Hvis Apps ikke kan du oprette én fra marketplace. Få mere at vide, [skal du klikke her](web-sites-php-web-site-gallery.md).
Klik på Indstillinger -> installation pladser -> Tilføj for at oprette en installation slot med fasen navn. En installation slot er en anden webprogram dele de samme ressourcer som den primære online, der er oprettet ovenfor.

![Oprette fase installation slot](./media/app-service-web-staged-publishing-realworld-scenarios/1setupstage.png)

Tilføje en anden MySQL-database, sig `wordpress-stage-db` til din ressourcegruppe `wordpressapp-group`.

 ![Føje MySQL-database til ressourcegruppe](./media/app-service-web-staged-publishing-realworld-scenarios/2addmysql.png)

Opdatere forbindelsen strengene for din fase installation slot til at pege på nyligt oprettet databasen, `wordpress-stage-db`. Bemærk, at din fremstilling web app, `wordpressprodapp` og midlertidige online `wordpressprodapp-stage` skal pege på forskellige databaser.

#### <a name="configure-environment-specific-app-settings"></a>Konfigurere indstillinger for miljø-specifikke app
Udviklere kan gemme nøgle-værdi streng par i Azure som en del af de konfigurationsoplysninger, der er knyttet til en WebApp, kaldet App-indstillinger. På kørselstidspunktet, App-tjenesten Web Apps automatisk henter disse værdier for dig og gør dem tilgængelige til kode, der kører i din online. Et værdipapir perspektiv, der er et godt side nyde godt af siden følsomme oplysninger, som strenge af database-forbindelse med adgangskoder aldrig vises som klar tekst i en fil som `wp-config.php`.

Denne proces, der er defineret nedenfor er nyttig, når du udfører det indeholder både filændringer og ændringer i databasen til WordPress app:
- WordPress opgradering
- Tilføje nye eller redigere eller opgradere plug-ins
- Tilføje nye eller redigere eller opgradere temaer

Konfigurere app indstillinger for:

- oplysninger om databasen
- aktivere/deaktivere WordPress logføring
- WordPress sikkerhedsindstillinger

![App-indstillinger for Wordpress WebApp](./media/app-service-web-staged-publishing-realworld-scenarios/3configure.png)

Kontrollér, at du har tilføjet de følgende app-indstillinger for din fremstilling web app og fase slot. Bemærk, at fremstilling WebApp og midlertidige online bruger forskellige databaser.
Fjern markeringen i afkrydsningsfeltet **Slot indstilling** for alle parametre indstillinger undtagen WP_ENV. Dette vil udskifte konfigurationen for din online sammen med filens indhold og database. Hvis **Slot indstilling** er **markeret**, indstillinger for webappen app og konfiguration af forbindelse streng flyttes ikke på tværs af miljøer når du udfører en handling af Udskift, og derfor Hvis ændringerne databasen findes dette skifter ikke din fremstilling WebApp.

Installer lokale udvikling miljø WebApp til fase WebApp og database ved hjælp af WebMatrix eller tool(s) efter eget valg som FTP, ciffer eller PhpMyAdmin.

![Web Matrix publicere dialogboksen WordPress web App](./media/app-service-web-staged-publishing-realworld-scenarios/4wmpublish.png)

Gennemse og test din midlertidige online. I betragtning et scenarie, hvor temaet af WebApp er der skal opdateres, er her midlertidige online.

![Gennemse midlertidige online før Skift pladser](./media/app-service-web-staged-publishing-realworld-scenarios/5wpstage.png)


 Hvis alt ser rigtigt ud, klikke på knappen **Ombyt** på dine midlertidige WebApp til at flytte indholdet til produktionsmiljøet. I dette tilfælde Ombyt du WebApp og databasen på tværs af miljøer under hver **Byt om** handling.

![Udskifte preview ændringer WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/6swaps1.png)

 > [AZURE.NOTE]
 >Hvis du har et scenarie, hvor du skal kun opslagsnål filer (ingen databaseopdateringer), derefter relateret **kontrollere** **Slot indstilling** for alle databasen *app-indstillinger* og *Indstillinger for forbindelse strenge* i web app indstilling blade i portalen Azure preview før du udfører Udskift. I dette tilfælde DB_NAME, DB_HOST, DB_PASSWORD, DB_USER, skal standardindstillingen for forbindelse streng ikke vises i eksempelvisningen af ændres ved at gøre en **Ombyt**. På dette tid, når du har fuldført handlingen **Ombyt** online WordPress har opdateringerne filer **kun**.

Før du udfører en Udskift, er her fremstilling WordPress online ![fremstilling online før Skift pladser](./media/app-service-web-staged-publishing-realworld-scenarios/7bfswap.png)

Efter handlingen Byt om er blevet opdateret temaet på din fremstilling WebApp.

![Fremstilling online efter Skift pladser](./media/app-service-web-staged-publishing-realworld-scenarios/8afswap.png)

I en situation, når du har brug for at **annullere**, kan du gå til indstillinger for fremstilling web app og klikke på knappen **Byt om** du kan udskifte WebApp og -database fra fremstilling til arrangering plads. En vigtig ting at huske er, hvis ændringer i databasen er inkluderet i en **Ombyt** handling på et givet tidspunkt, næste gang du installerer igen til din midlertidige online, skal du installere databasen skifter derefter til den aktuelle database til din midlertidige online som kunne være den forrige fremstilling database eller fase databasen.

#### <a name="summary"></a>Oversigt
Til generalize processen for et program med en database

1. Installere programmet på din lokale miljø
2. Medtage specifikt miljø er konfigureret (lokale og Azure Web App)
3. Konfigurere din miljøer i App-tjenesten Web Apps-midlertidige, fremstilling
4. Hvis du har en fremstilling programmet allerede kører på Azure, synkronisere din fremstilling indhold (filer/kode + database) lokale og midlertidige-miljø.
5. Udvikle programmet på din lokale miljø
6. Placer din fremstilling online under vedligeholdelse eller låst tilstand og synkronisere databasen indholdet fra fremstilling midlertidige og Udviklingscenter miljøer
7. Installere på midlertidige miljø og Test
8. Installere til produktionsmiljø
9. Gentag trin 4 til 6

### <a name="umbraco"></a>Umbraco
I dette afsnit kan du lære, hvordan Umbraco CMS bruger et brugerdefineret modul til at implementere fra på tværs af flere DevOps-miljø. I dette eksempel giver dig en anden fremgangsmåde for at administrere flere udviklingsmiljøer.

[Umbraco CMS](http://umbraco.com/) er et af de popular.NET CMS løsninger, der bruges af mange udviklere som indeholder [Courier2](http://umbraco.com/products/more-add-ons/courier-2) modul skal installeres fra udvikling til midlertidige for fremstilling miljøer. Du kan nemt oprette et lokalt miljø til en Umbraco CMS online ved hjælp af Visual Studio eller WebMatrix.

1. Oprette en Umbraco WebApp med Visual Studio, skal [du klikke her](https://our.umbraco.org/documentation/Installation/install-umbraco-with-nuget).
2. For at oprette en Umbraco WebApp med WebMatrix, [skal du klikke her](http://umbraco.com/help-and-support/video-tutorials/getting-started/working-with-webmatrix).

Altid Husk at fjerne den `install` mappe under dit program og aldrig overføre den til fase eller fremstilling webapps. I dette selvstudium, skal jeg bruge WebMatrix

#### <a name="set-up-a-staging-environment"></a>Konfigurere et midlertidigt miljø
- Oprette en installation slot, som nævnt ovenfor Umbraco CMS web App, hvis du allerede har en Umbraco CMS-WebApp og kører. Hvis du ikke kan du oprette én fra marketplace.

- Opdatere forbindelsesstrengen for din fase installation slot til at pege på nyligt oprettet databasen, **umbraco-fase-db**. Din fremstilling WebApp (umbraositecms-1) og midlertidige WebApp (umbracositecms-1-trin) **skal** du pege på forskellige databaser.

![Opdatere forbindelsesstreng til at arrangere WebApp med ny midlertidig database](./media/app-service-web-staged-publishing-realworld-scenarios/9umbconnstr.png)

- Klik på **få publicere indstillinger** for installation slot **fase**. Dette vil hente en fil med Publicer indstillinger, der gemmer alle de oplysninger, der kræves, før Visual Studio eller Web Matrix publicere dit program fra lokale udvikling WebApp til Azure WebApp.

 ![Hent publicere indstilling af midlertidige WebApp](./media/app-service-web-staged-publishing-realworld-scenarios/10getpsetting.png)

- Åbn din lokale udvikling online i **WebMatrix** eller **Visual Studio**. I dette selvstudium jeg bruger Web Matrix og først skal du importere filen Publicer indstillinger for din midlertidige online

![Importere Publicer indstillingerne for Umbraco ved hjælp af Web Matrix](./media/app-service-web-staged-publishing-realworld-scenarios/11import.png)

- Gennemse ændringer i dialogboksen, og anvende din lokale online til din Azure online *umbracositecms 1 trin*. Når du installerer filer direkte til din midlertidige online kan du udelader alle filer i den `~/app_data/TEMP/` mappe som disse gendannes når online fase er første gang. Du skal også udelade den `~/app_data/umbraco.config` , en fil som dette skal gendannes.

![Gennemse Publicer ændringer i web matrix](./media/app-service-web-staged-publishing-realworld-scenarios/12umbpublish.png)

- Gå til din midlertidige online efter korrekt udgivelse af Umbraco lokale WebApp til at arrangere WebApp, og Kør et par forsøg at udelukke eventuelle problemer.

#### <a name="set-up-courier2-deployment-module"></a>Konfigurere Courier2 installation modul
Med [Courier2](http://umbraco.com/products/more-add-ons/courier-2) modul kan du push indhold, typografiark, udvikling moduler og mere med en enkel Højreklik fra en midlertidig WebApp fremstilling WebApp til en mere besvær gratis installationer og reducere risikoen for Bryd din fremstilling online, når du installerer en opdatering.
Købe en licens til Courier2 for domænet, `*.azurewebsites.net` og dit eget domæne (sig http://abc.com) når du har købt licensen, placere de hentede licens (. LIC-fil) i den `bin` mappe.

![Slippe licensfil under placering mappe](./media/app-service-web-staged-publishing-realworld-scenarios/13droplic.png)

Hent pakken Courier2 fra [her](https://our.umbraco.org/projects/umbraco-pro/umbraco-courier-2/). Logge på din fase online, sig http://umbracocms-site-stage.azurewebsites.net/umbraco, og klik på **udvikler** Menu og vælge **pakker**. Klik på **Installer** lokal pakke

![Umbraco Package installer](./media/app-service-web-staged-publishing-realworld-scenarios/14umbpkg.png)

Overføre pakken courier2 ved hjælp af installationsprogrammet.

![Overføre pakke til courier modul](./media/app-service-web-staged-publishing-realworld-scenarios/15umbloadpkg.png)

Hvis du vil konfigurere, du vil opdatere courier.config fil under **Config** mappe for din online.

```xml
<!-- Repository connection settings -->
 <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
 <repositories>
    <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear: -->
    <repository name="production web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
      <url>http://umbracositecms-1.azurewebsites.net</url>
      <user>0</user>
      <!--<login>user@email.com</login> -->
      <!-- <password>user_password</password>-->
      <!-- <passwordEncoding>Clear</passwordEncoding>-->
      </repository>
 </repositories>
 ```

Under `<repositories>`, Angiv fremstilling webstedets URL-adresse og bruger oplysninger. Hvis du bruger standardudbyderen af Umbraco medlemskab, skal du tilføje ID for Administration af brugeren i <user> sektion. Hvis du bruger en brugerdefineret Umbraco medlemskab udbyder, kan du bruge `<login>`,`<password>` til Courier2 modul vide, hvordan du opretter forbindelse til webstedet fremstilling. Få mere at vide ved at gennemse i [dokumentationen](http://umbraco.com/help-and-support/customer-area/courier-2-support-and-download/developer-documentation) til Courier modul.

På samme måde, installere Courier modul på webstedet fremstilling og konfigurere det punkt til fase WebApp i dets respektive courier.config-fil som vist her

```xml
 <!-- Repository connection settings -->
 <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
 <repositories>
    <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear: -->
    <repository name="Stage web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
      <url>http://umbracositecms-1-stage.azurewebsites.net</url>
      <user>0</user>
      </repository>
 </repositories>
```

Klik på fanen Courier2 i Umbraco CMS web app dashboard, og vælg placeringer. Du bør se lager navnet, som nævnt i `courier.config`. Gøre dette på både dit fremstilling og arrangere webapps.

![Vis web app destinationslageret](./media/app-service-web-staged-publishing-realworld-scenarios/16courierloc.png)

Nu kan installere noget af indholdet fra midlertidige websted til fremstilling websted. Få adgang til indhold og vælge en eksisterende side eller oprette en ny side. Jeg vil vælge en eksisterende side fra min online, hvor titlen på siden er ændret til **Introduktion – ny** og nu Klik på **Gem og Publicer**.

![Ændre titlen på side og publicere](./media/app-service-web-staged-publishing-realworld-scenarios/17changepg.png)

Vælg ændret siden, og *Højreklik* for at få vist alle indstillinger. Klik på **Courier** til at få vist dialogboksen installation. Klik på **Implementer** at starte installationen

![Courier modul installation dialogboksen](./media/app-service-web-staged-publishing-realworld-scenarios/18dialog1.png)

Gennemse ændringerne, og klik på Fortsæt.

![Courier modul installation dialogboksen gennemse ændringer](./media/app-service-web-staged-publishing-realworld-scenarios/19dialog2.png)

Installation log viser Hvis installationen er fuldført.

 ![Få vist installation logfiler fra Courier modul](./media/app-service-web-staged-publishing-realworld-scenarios/20successdlg.png)

Find din fremstilling online for at se, hvis ændringerne, afspejles.

 ![Gennemse fremstilling WebApp](./media/app-service-web-staged-publishing-realworld-scenarios/21umbpg.png)

Hvis du vil vide mere om, hvordan du bruger Courier, gennemse dokumentationen til.

#### <a name="how-to-upgrade-umbraco-cms-version"></a>Sådan opgraderer du Umbraco CMS version

Courier kan ikke installere med opgradering fra én version af Umbraco CMS til en anden. Når du opgraderer Umbraco CMS version, skal du kontrollere for kompatibilitet med din brugerdefinerede moduler eller tredjeparts moduler og Umbraco Core-biblioteker. Som en bedste fremgangsmåde

1. ALTID sikkerhedskopiere din online og database før du udfører en opgradering. På Azure Web App, kan du konfigurere automatiske sikkerhedskopier for dine websteder ved hjælp af sikkerhedskopien af funktioner og gendanne webstedet Hvis det er nødvendigt ved hjælp af gendanne funktion. Se [Sådan sikkerhedskopieres din online](web-sites-backup.md) og [hvordan du kan gendanne din online](web-sites-restore.md)kan finde flere oplysninger.

2. Kontrollér, om de tredjeparts-pakker, du bruger er kompatible med den version, du opgraderer til. Download side på pakken, skal du gennemgå Project-kompatibilitet med Umbraco CMS version.

Følg retningslinjerne for flere oplysninger om, hvordan du kan opgradere din online lokalt, som nævnes [her](https://our.umbraco.org/documentation/getting-started/set up/upgrading/general).

Når udviklingswebstedet lokale er opgraderet, skal du publicere ændringerne til at arrangere WebApp. Test dit program, og hvis alt ser rigtigt ud, kan du bruge **Ombyt** knap til at **udskifte** webstedet midlertidige fremstilling web App. Når du udfører handlingen **Ombyt** , kan du se de ændringer, der påvirkes i din online konfiguration. Vi Skift webapps og databaser med handlingen **Ombyt** . Det betyder, når Udskift fremstilling online nu peger på umbraco-fase-db database og midlertidige online peger på umbraco-Prod.ordre-db database.

![Udskift preview til implementering af Umbraco CMS](./media/app-service-web-staged-publishing-realworld-scenarios/22umbswap.png)

Fordelen ved at udskifte både WebApp og database:
1. Giver dig mulighed for at vende tilbage til den tidligere version af din online med en anden **Ombyt** , hvis der er problemer med programmet.
2. Du skal installere filer og -database fra den midlertidige online til fremstilling WebApp og database til en opgradering. Der er mange ting, der kan gå galt, når du installerer filer og -database. Ved hjælp af funktionen **Udskift** af pladser, kan vi reducere nedetid under en opgradering og reducere risikoen for fejl, der kan opstå, når du installerer ændringer.
3. Giver dig mulighed for at gøre **A / B test** funktionen [tester fremstilling](https://azure.microsoft.com/documentation/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)

I dette eksempel vises du fleksibilitet af platformen sted, hvor du kan oprette brugerdefinerede moduler ligner Umbraco Courier modul til at administrere installation på tværs af miljøer.

## <a name="references"></a>Referencer
[Fleksible softwareudvikling med Azure App Service](app-service-agile-software-development.md)

[Konfigurere arrangere miljøer til webapps i Azure App Service](web-sites-staged-publishing.md)

[Sådan blokeres webadgang til ikke-produktionsmiljø pladser](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)
