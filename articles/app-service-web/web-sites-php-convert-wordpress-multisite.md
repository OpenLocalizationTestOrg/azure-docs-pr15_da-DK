<properties 
    pageTitle="Konvertere WordPress til flere lokationer i Azure App-tjeneste" 
    description="Lær, hvordan du tager en eksisterende WordPress WebApp, der er oprettet via galleriet i Azure og konvertere den til flere lokationer WordPress" 
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



# <a name="convert-wordpress-to-multisite-in-azure-app-service"></a>Konvertere WordPress til flere lokationer i Azure App-tjeneste

## <a name="overview"></a>Oversigt

*Ved [Ben Lobaugh][ben-lobaugh], [Microsoft Åbn Technologies Inc.][ms-open-tech]*

I dette selvstudium lærer du, hvordan du tager en eksisterende WordPress WebApp, der er oprettet via galleriet i Azure og konvertere den til en WordPress flere lokationer-installation. Desuden kan lære du, hvordan du tildele et brugerdefineret domæne til hver af de underordnede websteder i din installation.

Det antages, at du har en eksisterende installation af WordPress. Hvis du ikke gør det, skal du følge vejledningen i [oprette et WordPress websted fra galleriet i Azure][website-from-gallery].

Konvertere en eksisterende WordPress enkelt websted Installer til flere lokationer er normalt ganske enkelt og mange af de første trin her kommer direkte fra [Oprette et netværk] [ wordpress-codex-create-a-network] side på [WordPress Codex](http://codex.wordpress.org).

Lad os komme i gang.

## <a name="allow-multisite"></a>Tillad flere lokationer

Du skal først aktivere flere lokationer gennem den `wp-config.php` fil med den **WP\_Tillad\_flere LOKATIONER** konstant. Der er to metoder til at redigere din web app-filer: først er igennem FTP, og andet gennem ciffer. Hvis du ikke har kendskab til, hvordan du konfigurerer en af følgende metoder, kan du se følgende selvstudier:

* [PHP websted med MySQL- og FTP-][website-w-mysql-and-ftp-ftp-setup]

* [PHP websted med MySQL og ciffer][website-w-mysql-and-git-git-setup]

Åbn den `wp-config.php` fil med editor efter eget valg, og Tilføj følgende ovenfor den `/* That's all, stop editing! Happy blogging. */` linje.

    /* Multisite */

    define( 'WP_ALLOW_MULTISITE', true );

Sørg for at gemme filen, og Overfør det tilbage til serveren!

## <a name="network-setup"></a>Opsætning af netværk

Log til området *wp administrator* i web-app, og du står i et nyt element i menuen **Funktioner** , der hedder **Opsætning af netværk**. Klik på **Opsætning af netværk** , og Udfyld detaljerne for dit netværk.

![Netværk konfigurationssiden][wordpress-network-setup]

Dette selvstudium bruger *undermapper* websted skemaet, fordi det altid skal fungere, og vi vil konfigurere brugerdefinerede domæner for hver underordnede websted senere i selvstudiet. Det skal dog være muligt til konfiguration af et underdomæne installere, hvis du tilknytter et domæne via jokertegnet [Azure Portal](https://portal.azure.com) og konfiguration af DNS-korrekt.

Yderligere oplysninger om underdomæne vs af undermappe konfigurationer vist, hvilke [typer af netværk med flere steder] [ wordpress-codex-types-of-networks] artikel om WordPress Codex.

## <a name="enable-the-network"></a>Aktivere netværket

Netværket er nu konfigureret i databasen, men der er et resterende trin for at aktivere funktionen netværk. Færdiggøre den `wp-config.php` indstillinger, og Sørg for `web.config` korrekt dirigerer hvert websted.


Når du klikker på knappen **Installer** på siden *Konfiguration af netværk* , WordPress vil forsøge at opdatere den `wp-config.php` og `web.config` filer. Du bør altid se filer for at sikre, at opdateringerne blev gennemført. Hvis ikke, dette skærmbillede Præsenter du med de nødvendige opdateringer. Redigere og gemme filerne.


Efter foretage tilbage disse opdateringer, skal du logge af og logge på dashboardet wp-administrator.

Der skulle nu være en ekstra menuen Gå til admin liggende mærket **Mine websteder**. Denne menu kan du kontrollere din nye netværk via dashboardet **Netværksadministrator** .

## <a name="adding-custom-domains"></a>Tilføje brugerdefinerede domæner

[Tilknytning af WordPress MU domæne] [ wordpress-plugin-wordpress-mu-domain-mapping] -plug-in gør det lynhurtigt tilføje brugerdefinerede domæner til en hvilken som helst sted i dit netværk. I rækkefølge for plug-in til at fungere korrekt, skal du gøre nogle yderligere opsætning på portalen og hos din domæneregistrator.

## <a name="enable-domain-mapping-to-the-web-app"></a>Aktivere domænetilknytning til online

Tilstanden **gratis** [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) plan understøtter ikke tilføje brugerdefinerede domæner til Web Apps. Skal du skifte til **delt** eller **Standard** -tilstand. Sådan gør du:

* Log på portalen Azure, og find din online. 
* Klik på fanen **skalere op** i **Indstillinger**.
* Vælg *delt* eller *STANDARD* under **Generelt**,
* Klik på **Gem**

Du modtager muligvis en meddelelse beder dig om at bekræfte ændringen og bekræfte din online nu kan afholde en omkostning, afhængigt af brug og de andre konfiguration, du angiver.

Det tager et par sekunder at behandle de nye indstillinger, så nu er det et godt tidspunkt at begynde at konfigurere dit domæne.

## <a name="verify-your-domain"></a>Bekræfte dit domæne

Før Azure Web Apps gør det muligt at tilknytte et domæne til webstedet, skal du først bekræfte, at du har tilladelse til at tilknytte domænet. Hvis du vil gøre det, skal du føje en ny CNAME-post til din DNS-post.

* Log på domænets DNS manager
* Oprette en ny CNAME- *awverify*
* Peg *awverify *awverify* . YOUR_DOMAIN.azurewebsites.NET*

Det kan tage lidt tid, før DNS-ændringer for at gå til fuld effekt, så hvis følgende trin ikke fungerer med det samme, gå foretage en Kaffekop, og derefter vende tilbage og prøve igen.

## <a name="add-the-domain-to-the-web-app"></a>Tilføje domænet til WebApp

Vende tilbage til din online via Azure-portalen, klik på **Indstillinger**og derefter klikke på **brugerdefinerede domæner og SSL**.

Når *SSL indstillinger* vises, kan du se de felter, hvor du vil angive alle domæner, du vil knytte til din online. Hvis et domæne, der ikke vises her, vil det ikke være tilgængelig for tilknytning i WordPress, uanset hvordan domæne DNS er konfigureret.

![Administrere brugerdefinerede domæner dialogboksen][wordpress-manage-domains]

Når du har skrevet dit domæne i tekstfeltet, kontrollere Azure den CNAME-post, du tidligere har oprettet. Hvis DNS-Posterne ikke er fuldt overført, vises en rød indikator. Hvis det lykkedes, får du vist et grønt flueben. 

Tage noter IP-adresse vises nederst i dialogboksen. Du skal bruge denne indstilling for at konfigurere A-posten for dit domæne.

## <a name="setup-the-domain-a-record"></a>Konfigurere domænet en post

Hvis andre trinnene blev gennemført, kan du nu tildele domænet til din Azure online via en DNS-A-post. 

Det er vigtigt at bemærke her, Azure Onlines acceptere både CNAME og en poster, men du *skal* bruge en A-post til at aktivere stort domæne kort. En CNAME kan ikke videresendes til en anden CNAME, hvilket er, hvad Azure oprettet for dig med YOUR_DOMAIN.azurewebsites.net.

Ved hjælp af IP-adressen fra det forrige trin, gå tilbage til din DNS manager og Konfigurer A-posten til at pege på IP-adresse.


## <a name="install-and-setup-the-plugin"></a>Installer og Konfigurer plug-in'en

Flere lokationer WordPress har i øjeblikket ikke en indbygget metode til at overføre brugerdefinerede domæner. Der er dog et plug-in kaldet [WordPress MU domæne tilknytning] [ wordpress-plugin-wordpress-mu-domain-mapping] , der tilføjer funktionaliteten for dig. Log på netværksadministrator del af dit websted og installere **WordPress MU domæne tilknytning** plug-in'et.

Efter installation og aktivering af plug-in'en, gå til **Indstillinger for** > **Domæne tilknytning** til at konfigurere plug-in'et. Skriv den IP-adresse, du brugte til at konfigurere A-posten for domænet, i tekstfeltet første skal *IP-adresse*. Angiv eventuelle *Indstillinger for domæne* , du ønsker (standardindstillingerne er ofte fint) og klikke på **Gem**.

## <a name="map-the-domain"></a>Tilknytte domænet

Gå til **Dashboard** til det websted, du vil tilknytte domæne til. Klik på **Funktioner** > **Domæne tilknytning** og skrive det nye domæne i tekstfeltet, og klik på **Tilføj**.

Som standard, bliver det nye domæne omskrives genereret automatisk websted domænet. Hvis du vil have alle trafik, der sendes til det nye domæne, skal du markere feltet *primært domæne til denne blog* før du gemmer. Du kan føje et ubegrænset antal domæner til et websted, men kun én kan være primære.

## <a name="do-it-again"></a>Gøre det igen

Azure Web Apps giver dig mulighed at føje et ubegrænset antal domæner til en WebApp. Hvis du vil føje et andet domæne skal du udføre sektionerne **bekræfte dit domæne** og **konfigurere domænet en post** for hvert domæne.  

>[AZURE.NOTE] Hvis du vil komme i gang med Azure App Service før tilmelding til en Azure-konto, skal du gå til [Prøve App Service](http://go.microsoft.com/fwlink/?LinkId=523751), hvor du straks kan oprette en forbigående starter WebApp i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.

## <a name="whats-changed"></a>Hvad er ændret
* Finde en vejledning til ændring fra websteder til App-tjenesten: [Azure App Service og dets indvirkning på eksisterende Azure Services](http://go.microsoft.com/fwlink/?LinkId=529714)

[ben-lobaugh]: http://ben.lobaugh.net
[ms-open-tech]: http://msopentech.com
[website-from-gallery]: https://www.windowsazure.com/develop/php/tutorials/website-from-gallery/
[wordpress-codex-create-a-network]: http://codex.wordpress.org/Create_A_Network
[website-w-mysql-and-ftp-ftp-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-ftp/#header-0
[website-w-mysql-and-git-git-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-git/#header-1
[wordpress-network-setup]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-network-setup.png
[wordpress-codex-types-of-networks]: http://codex.wordpress.org/Before_You_Create_A_Network#Types_of_multisite_network
[wordpress-plugin-wordpress-mu-domain-mapping]: http://wordpress.org/extend/plugins/wordpress-mu-domain-mapping/

[wordpress-manage-domains]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-manage-domains.png

 
