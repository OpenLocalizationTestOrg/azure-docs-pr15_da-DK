<properties
    pageTitle="Professionel WordPress på Azure App Service | Microsoft Azure"
    description="Lær at være vært for en professionel WordPress websted på Azure App Service"
    services="app-service\web"
    documentationCenter=""
    authors="sunbuild"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.devlang="php"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="web"
    ms.date="10/24/2016"
    ms.author="sumuth"/>

# <a name="enterprise-class-wordpress-on-azure-app-service"></a>Professionel WordPress på Azure App Service

Azure App Service giver et SVG, sikker og nem at bruge miljø til kommunikations kritiske, store skala [WordPress] [ wordpress] websteder. Microsoft selve kører professionel websteder som [Office] [ officeblog] og [Bing] [ bingblog] blogge. Dette dokument viser, hvordan du kan bruge Azure App Service Web Apps til at oprette og vedligeholde en professionel, skybaseret WordPress websted, der kan håndtere en stor mængde besøgende.

## <a name="architecture-and-planning"></a>Arkitektur og planlægning

En grundlæggende WordPress installation er kun to krav.

* **MySQL-Database** - gennem [ClearDB i Azure Marketplace][cdbnstore], eller du kan administrere dine egne MySQL-installation på virtuelle Azure-computere ved hjælp af enten [Windows] [ mysqlwindows] eller [Linux][mysqllinux].

  > [AZURE.NOTE] ClearDB indeholder flere MySQL-konfigurationer, med forskellige ydeevne egenskaber for hver konfiguration. Se [Azure Store] [ cdbnstore] oplysninger om tilbud via Azure store eller direkte som set på [ClearDB websted](http://www.cleardb.com/pricing.view).

* **PHP 5.2.4 eller større** -Azure App Service giver aktuelt [PHP version 5.4, 5,5 og 5,6][phpwebsite].

    > [AZURE.NOTE] Vi anbefaler, at du altid kører på den seneste version af PHP at sikre, at du har de nyeste sikkerhedsrettelser.

### <a name="basic-deployment"></a>Grundlæggende installation

Ved hjælp af de grundlæggende krav, kan du oprette en grundlæggende løsning inden for et Azure område.

![en Azure WebApp og MySQL-Database, der er placeret i et enkelt Azure område][basic-diagram]

Mens dette giver dig tilladelse til skala fra dit program ved at oprette flere Web Apps-forekomster af webstedet, er alt hostet i datacentre i et bestemt geografisk område. Besøgende fra uden for dette område kan du se langsom svar gange når du bruger webstedet, og hvis datacentre i dette område går ned, så påvirker dit program.


### <a name="multi-region-deployment"></a>Flere områder installation

Ved hjælp af Azure [Trafik Manager][trafficmanager], det er muligt at tilpasse webstedet WordPress på tværs af flere geografiske områder samtidig med at kun én URL-adresse til besøgende. Alle besøgende kommer via trafik Manager og distribueres derefter til et område, der er baseret på den justering af belastning konfiguration.

![en Azure WebApp hostet i flere områder ved hjælp af CDBR høj tilgængelighed router til at dirigere til MySQL på tværs af områder][multi-region-diagram]

Webstedet WordPress vil stadig skaleret på tværs af flere forekomster af Web Apps i hvert område, men denne skalering er område, der er specifikke. høj trafik områder kan skaleres anderledes end lav trafik dem.

Replikering og routing til flere MySQL-databaser kan udføres ved hjælp af Cleardb's [CDBR høj tilgængelighed Router] [ cleardbscale] (vist venstre) eller [MySQL klynge CGE][cge].

### <a name="multi-region-deployment-with-media-storage-and-caching"></a>Flere områder-installation med medier lager og cachelagring
Hvis webstedet har accepteret overførsler og host mediefiler, kan du bruge Azure Blob-lager. Hvis du har brug for cachelagring, kan du overveje at [Redis cache][rediscache], [Memcache skyen](http://azure.microsoft.com/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/gallery/store/memcachier/memcachier/)eller en af de andre cachelagring tilbud i [Azure Store](http://azure.microsoft.com/gallery/store/).

![en Azure WebApp hostet i flere områder ved hjælp af CDBR høj tilgængelighed router til MySQL med administrerede cachen, Blob-lager og CDN][performance-diagram]

BLOB-lager er geografisk fordeles over områder som standard, så du ikke behøver at bekymre dig om replikering filer på tværs af alle websteder. Du kan også aktivere Azure [Indhold fordeling netværk (CDN)] [ cdn] til Blob-lager, som distribuerer filer for at afslutte noder tættere til besøgende.

### <a name="planning"></a>Planlægning

#### <a name="additional-requirements"></a>Ekstra krav

Du gør dette... | Brug dette...
------------------------|-----------
**Overføre eller gemme store filer** | [WordPress-plug-in til brug af Blob-lager][storageplugin]
**Sende mail** | [SendGrid] [ storesendgrid] og [WordPress-plug-in til brug af SendGrid][sendgridplugin]
**Brugerdefinerede domænenavne** | [Konfigurere et brugerdefineret domænenavn i Azure App Service][customdomain]
**HTTPS** | [Aktivere HTTPS for en WebApp i Azure App Service][httpscustomdomain]
**Foreløbig fremstilling validering** | [Konfigurere arrangere miljøer til webapps i Azure App Service][staging] <p>Skifte en web-app fra den midlertidige fremstilling også flytter WordPress konfigurationen. Du skal sikre, at alle indstillingerne er opdateret til kravene til din fremstilling app inden du skifter faseinddelt app til fremstilling.</p>
**Overvågning og fejlfinding** | [Aktivere logføring af diagnosticering til webapps i Azure App Service] [ log] og [Overvåge Web Apps i Azure App Service][monitor]
**Installere dit websted** | [Installere en WebApp i Azure App Service][deploy]

#### <a name="availability-and-disaster-recovery"></a>Tilgængelighed og genoprettelse efter nedbrud

Du gør dette... | Brug dette...
------------------------|-----------
**Indlæse balance websteder** eller **geografisk-distribuere websteder** | [Omdirigere trafik med Azure trafik Manager][trafficmanager]
**Sikkerhedskopiere og gendanne** | [Sikkerhedskopiere en WebApp i Azure App Service] [ backup] og [gendanne en WebApp i Azure App Service][restore]

#### <a name="performance"></a>Ydeevne

Ydeevne i skyen opnås primært gennem cachelagring og skala ud; men hukommelsen, båndbredde og andre attributter for online vært skal betragtes.

Du gør dette... | Brug dette...
------------------------|-----------
**Forstå App Service forekomst funktioner** | [Prisoplysninger, herunder funktionerne i App Service niveauer][websitepricing]
**Cache ressourcer** | [Redis cache][rediscache], [Memcache skyen](/gallery/store/garantiadata/memcached/), [MemCachier](/gallery/store/memcachier/memcachier/)eller en af de andre cachelagring tilbud i [Azure Store](/gallery/store/)
**Skalere dit program** | [Skalere et online i Azure App Service] [ websitescale] og [ClearDB høj tilgængelighed Routing][cleardbscale]. Hvis du vælger at administrere dine egne MySQL-installation og være vært, skal du overveje at [MySQL klynge CGE] [ cge] for skala ud

#### <a name="migration"></a>Overførsel

Der er to måder at overføre et eksisterende websted WordPress til Azure App Service.

* ** [WordPress eksportere] [ export] ** -dette eksporterer indholdet af din blog, som kan importeres til et nyt WordPress websted på Azure App tjenesten ved hjælp af [WordPress importer plug-in][import].

    > [AZURE.NOTE] Mens denne proces gør det muligt at overflytte indhold, overføres det ikke alle plug-ins, temaer eller andre tilpasninger. Disse skal være installeret igen manuelt.

* **Manuel overførsel** - [sikkerhedskopiere webstedet] [ wordpressbackup] og [database][wordpressdbbackup], manuelt gendanne den til en WebApp i Azure App-tjeneste, og der er knyttet MySQL-database for at overføre meget brugertilpassede websteder og undgå kedsommelige manuel installation af plug-ins, temaer og andre tilpasninger.

## <a name="step-by-step-instructions"></a>En trinvis vejledning

### <a name="create-a-wordpress-site"></a>Oprette et WordPress websted

1. Bruge [Azure Marketplace] [ cdbnstore] til at oprette en MySQL-database af størrelsen, du har identificeret i sektionen [arkitektur og planlægning](#planning) i det eller de områder, du er vært for dit websted.

2. Følg trinnene i [oprette en WordPress WebApp i Azure App Service] [ createwordpress] til at oprette en WordPress WebApp. Når du opretter WebApp, Vælg **Brug en eksisterende MySQL-Database** , og vælg den database, der oprettede i trin 1.

Hvis du overfører et eksisterende WordPress-websted, skal du se [overføre et eksisterende WordPress websted til Azure](#Migrate-an-existing-WordPress-site-to-Azure) efter oprettelse af en ny WebApp.

### <a name="migrate-an-existing-wordpress-site-to-azure"></a>Overføre et eksisterende websted WordPress til Azure

Som nævnt i afsnittet [arkitektur og planlægning](#planning) , er der to måder at overføre et WordPress websted.

* **eksportere og importere** - til-websteder uden meget tilpasning, eller hvor du blot ønsker at flytte indholdet.

* **sikkerhedskopiere og gendanne** - for websteder med en masse tilpasning, hvor du vil flytte alt.

Brug en af de følgende afsnit til at overføre dit websted.

#### <a name="the-export-and-import-method"></a>Metoden eksport og import

1. Brug [WordPress eksportere] [ export] eksportere dit eksisterende websted.

2. Oprette en WebApp, ved hjælp af trinnene i afsnittet [oprette et WordPress websted](#Create-a-new-WordPress-site) .

3. Log på webstedet WordPress på Web Apps, og klik på **plug-ins** -> **Add New**. Søge efter og installere plug-in'en til **WordPress Importer** .

4. Efter importer plug-in'et er blevet installeret, skal du klikke på **værktøjer** -> **importere**, og vælg derefter **WordPress** for at bruge WordPress importer plug-in'et.

5. Klik på **Vælg fil**på siden **Importér WordPress** . Gå til den WXR-fil, der er eksporteret fra dit eksisterende websted WordPress, og vælg derefter **Overfør fil og importere**.

6. Klik på **Send**. Du bliver bedt om, at importen blev fuldført.

8. Når du har udført disse trin, genstart webstedet fra dens web app-blade i [Azure portal][mgmtportal].

Når du har importeret webstedet, kan du vil udføre følgende trin for at aktivere indstillinger, der ikke findes i importfilen.

Hvis du brugte dette... | Gør dette?
------------------ | ----------
**Permalinks** | Klik på **Indstillinger**fra dashboardet WordPress for det nye websted -> **Permalinks** og derefter opdatere Permalinks strukturen
**Billede af/medier links** | For at opdatere links til den nye placering, skal du bruge [Velvet blå opdatere URL-adresser-plug-in][velvet], et Søg og Erstat værktøj eller manuelt i databasen
**Temaer** | Gå til **udseende** -> **tema** og Opdater webstedstemaet efter behov
**Menuer** | Hvis dit tema understøtter menuer, links til din startside muligvis gamle undermappen integreret i dem. Gå til **udseende** -> **menuer** og opdatere dem.

#### <a name="the-backup-and-restore-method"></a>Metoden sikkerhedskopiering og gendannelse

1. Sikkerhedskopiere din eksisterende WordPress webstedet ved hjælp af oplysningerne på [WordPress sikkerhedskopier][wordpressbackup].

2. Sikkerhedskopiere din eksisterende database ved hjælp af oplysningerne på [sikkerhedskopiere databasen][wordpressdbbackup].

3. Oprette en database og gendanne sikkerhedskopien.

    1. Købe en ny database fra [Azure Marketplace][cdbnstore], eller konfigurere en MySQL-database på en [Windows] [ mysqlwindows] eller [Linux] [ mysqllinux] VM.

    2. Ved hjælp af en MySQL-klient som [MySQL Workbench][workbench], oprette forbindelse til den nye database og importere databasen WordPress.

    3. Opdatere databasen for at ændre domæne posterne til din nye Azure App Service-domæne. For eksempel mywordpress.azurewebsites.net. Brug [Søg og erstat til WordPress databaser Script] [ searchandreplace] sikkert ændre alle forekomster.

4. Oprette en WebApp i portalen Azure og Publicer WordPress sikkerhedskopien.

    1. Oprette en WebApp i [Azure portal] [ mgmtportal] med en database ved hjælp af **Ny** -> **Web + Mobile** -> **Azure Marketplace** -> **Onlines** -> **Online + SQL** (eller **WebApp + MySQL**) -> **Opret**. Konfigurere alle de nødvendige indstillinger for at oprette en tom WebApp.

    2. Find filen **wp config.php** i sikkerhedskopien WordPress og åbne den i en editor. Erstat følgende poster med oplysningerne for den nye MySQL-database.

        * **DB_NAME** - brugernavnet på databasen

        * **DB_USER** - brugernavnet bruges til at få adgang til databasen

        * **DB_PASSWORD** - brugeradgangskode

        Efter ændring af disse poster, Gem og Luk filen **wp config.php** .

    3. Bruge [Implementer en WebApp i Azure App Service] [ deploy] oplysninger til at aktivere installationsmetode, du vil bruge, og derefter installere sikkerhedskopien WordPress til din online i Azure App-tjeneste.

5. Når webstedet WordPress er blevet installeret, skal du have adgang til det nye websted (som en App Service WebApp) ved hjælp af den *. azurewebsite.net URL-adressen til webstedet.

### <a name="configure-your-site"></a>Konfigurere dit websted

Når webstedet WordPress har oprettet eller overflyttet, kan du bruge følgende oplysninger til at forbedre ydeevnen eller aktivere yderligere funktioner.

Du gør dette... | Brug dette...
------------- | -----------
**Angive App Service plan tilstand, størrelse og Aktivér skalering** | [Skalere et online i Azure App Service][websitescale]
**Aktivere databaseforbindelser til fast** | Som standard bruger WordPress ikke fast databaseforbindelser, hvilket kan medføre din forbindelse til databasen til at blive begrænset efter flere forbindelser. For at aktivere vedvarende forbindelser skal installere (vedvarende forbindelser adapter plug-in'et) [https://wordpress.org/plugins/persistent-database-connection-updater/installation/]. 
**Forbedre ydeevnen** | <ul><li><p><a href="http://ppe.blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">Deaktivere ARR cookie</a> - kan forbedre ydeevnen, når der benyttes WordPress flere forekomster af Web Apps</p></li><li><p>Aktivér cachelagring. <a href="http://msdn.microsoft.com/library/azure/dn690470.aspx">Redis cache</a> (preview) kan bruges med <a href="https://wordpress.org/plugins/redis-object-cache/">Redis objekt cache WordPress plug-in</a>eller bruge en af de andre cachelagring tilbud fra <a href="/gallery/store/">Azure Store</a></p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Sådan gør WordPress hurtigere med Wincache</a> - Wincache er aktiveret som standard for Web Apps</p></li><li><p><a href="../web-sites-scale/">Skala en WebApp i Azure App Service</a> og bruge <a href="http://www.cleardb.com/developers/cdbr/introduction">ClearDB høj tilgængelighed Routing</a> - eller <a href="http://www.mysql.com/products/cluster/">MySQL klynge CGE</a></p></li></ul>
**Bruge BLOB til lager** | <ol><li><p><a href="../storage-create-storage-account/">Oprette en Azure-lager-konto</a></p></li><li><p>Lær, hvordan du kan <a href="../cdn-how-to-use/">bruge indhold fordeling netværk (CDN)</a> til geografisk-distribuere data gemt i BLOB.</p></li><li><p>Installer og Konfigurer <a href="https://wordpress.org/plugins/windows-azure-storage/">Azure-lager for WordPress plug-in</a>.</p><p>Detaljeret konfiguration og konfigurationsoplysninger for plug-in'et under <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">brugervejledningen</a>.</p> </li></ol>
**Aktivere mail** | Aktivere <a href="https://azure.microsoft.com/en-us/marketplace/partners/sendgrid/sendgrid-azure/">SendGrid</a> bruger Azure-lager. Installere <a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified">plug-in SendGrid</a> til WordPress.
**Konfigurere et brugerdefineret domænenavn** | [Konfigurere et brugerdefineret domænenavn i Azure App Service][customdomain]
**Aktivere HTTPS til et brugerdefineret domænenavn** | [Aktivere HTTPS for en WebApp i Azure App Service][httpscustomdomain]
**Indlæse saldo eller geografisk-distribuere dit websted** | [Omdirigere trafikken med Azure trafik Manager][trafficmanager]. Hvis du bruger et brugerdefineret domæne, kan du se [konfigurere et brugerdefineret domænenavn i Azure App Service] [ customdomain] oplysninger om brug af trafik Manager med brugerdefinerede domænenavne
**Aktivere automatiske sikkerhedskopier** | [Sikkerhedskopiere en WebApp i Azure App Service][backup]
**Aktivere logføring af diagnostik** | [Aktivere logføring af diagnosticering til webapps i Azure App Service][log]

## <a name="next-steps"></a>Næste trin

* [WordPress optimering](http://codex.wordpress.org/WordPress_Optimization)

* [Konvertere WordPress til flere lokationer i Azure App-tjeneste](web-sites-php-convert-wordpress-multisite.md)

* [Guiden til Azure ClearDB opgradering](http://www.cleardb.com/store/azure/upgrade)

* [Vært WordPress i en undermappe til din online i Azure App Service](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)

* [Trinvis vejledning: Oprette et WordPress websted med Azure](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)

* [Placere din eksisterende WordPress blog på Azure](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)

* [Aktivere flotte permalinks i WordPress](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)

* [Hvordan du kan overføre og køre bloggen WordPress på Azure App Service](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)

* [Sådan køres WordPress på Azure App Service gratis](http://architects.dzone.com/articles/how-run-wordpress-azure)

* [WordPress på Azure på to minutter eller mindre](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)

* [Flytte en WordPress blog til Azure - del 1: oprette en WordPress blog på Azure](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)

* [Flytte en WordPress blog til Azure - del 2: overføre dit indhold](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)

* [Flytte en WordPress blog til Azure - del 3: konfigurere dit brugerdefinerede domæne](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)

* [Flytte en WordPress blog til Azure - del 4: flotte permalinks og URL-adressen på omskrivning regler](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)

* [Flytte en WordPress blog til Azure - del 5: flytte fra en undermappe i roden](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)

* [Hvordan du konfigurerer en WordPress WebApp i din Azure-konto](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)

* [Propping op WordPress på Azure](http://www.johnpapa.net/wordpress-on-azure/)

* [Tip til WordPress på Azure](http://www.johnpapa.net/azurecleardbmysql/)

>[AZURE.NOTE] Hvis du vil komme i gang med Azure App Service før tilmelding til en Azure-konto, skal du gå til [Prøve App Service](http://go.microsoft.com/fwlink/?LinkId=523751), hvor du straks kan oprette en forbigående starter WebApp i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.

## <a name="whats-changed"></a>Hvad er ændret
* Finde en vejledning til ændring fra websteder til App-tjenesten: [Azure App Service og dets indvirkning på eksisterende Azure Services](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- URL List -->

[performance-diagram]: ./media/web-sites-php-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-php-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-php-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: web-sites-php-configure.md
[customdomain]: web-sites-custom-domain-name.md
[trafficmanager]: ../traffic-manager/traffic-manager-overview.md
[backup]: web-sites-backup.md
[restore]: web-sites-restore.md
[rediscache]: https://azure.microsoft.com/documentation/services/redis-cache/
[managedcache]: http://msdn.microsoft.com/library/azure/dn386122.aspx
[websitescale]: web-sites-scale.md
[managedcachescale]: http://msdn.microsoft.com/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: web-sites-staged-publishing.md
[monitor]: web-sites-monitor.md
[log]: web-sites-enable-diagnostic-log.md
[httpscustomdomain]: web-sites-configure-ssl-certificate.md
[mysqlwindows]: ../virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md
[mysqllinux]: ../virtual-machines/virtual-machines-linux-classic-mysql-on-opensuse.md
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: /pricing/details/app-service/
[export]: http://en.support.wordpress.com/export/
[import]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: web-sites-php-web-site-gallery.md
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://portal.azure.com/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[deploy]: web-sites-deploy.md
[posh]: ../powershell-install-configure.md
[Azure CLI]: ../xplat-cli-install.md
[storesendgrid]: https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/
[cdn]: ../cdn/cdn-overview.md
