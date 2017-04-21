#<a name="how-to-use-the-azure-command-line-tools-for-mac-and-linux"></a>Hvordan du kan bruge værktøjerne Azure kommandolinjeparametre til Mac og Linux

Denne vejledning beskriver, hvordan du bruger værktøjerne Azure kommandolinjeparametre til Mac og Linux til at oprette og administrere tjenester i Azure. Scenarier dækket omfatter **installere værktøjer**, **importere indstillingerne for publicering**, **oprettelse og administration af Azure websteder**og **oprettelse og administration af virtuelle Azure-computere**. Se [Azure værktøj på kommandolinjen til Mac og Linux dokumentation]til omfattende referencedokumentation[reference-docs]. 

##<a name="table-of-contents"></a>Indholdsfortegnelse
* [Hvad er Azure kommandolinjeparametre værktøjerne til Mac og Linux](#Overview)
* [Sådan installeres Azure kommandolinjeparametre værktøjerne til Mac og Linux](#Download)
* [Sådan oprettes en Azure-konto](#CreateAccount)
* [Sådan hentes og importere publicere indstillinger](#Account)
* [Hvordan du kan oprette og administrere et Azure-websted](#WebSites)
* [Hvordan du kan oprette og administrere en Azure Virtual Machine](#VMs)


<h2><a id="Overview"></a>Hvad er Azure kommandolinjeparametre værktøjerne til Mac og Linux</h2>

Azure kommandolinjeparametre værktøjerne til Mac og Linux er et sæt af kommandolinjen værktøjer til installation og administration af Azure-tjenester.
 
De understøttede opgaver omfatter følgende:

* Importere indstillinger for udgivelse.
* Oprette og administrere Azure websteder.
* Oprette og administrere virtuelle Azure-computere.

En komplet liste over understøttede kommandoer, Skriv `azure -help` på kommandolinjen når du har installeret værktøjerne, eller se [referencedokumentation][reference-docs].

<h2><a id="Download">Sådan installeres Azure kommandolinjeparametre værktøjerne til Mac og Linux</a></h2>

Følgende liste indeholder oplysninger til installation af kommandolinjen værktøjerne, afhængigt af dit operativsystem:

* **Mac**: hente [Azure SDK Installer][mac-installer]. Åbn filen hentede .pkg og fuldføre trinnene til installation, når du får besked.

* **Linux**: installere den nyeste version af [Node.js] [ nodejs-org] (se [Installere Node.js via Package Manager][install-node-linux]), derefter køre følgende kommando:

        npm install azure-cli -g

    **Bemærk**: Du skal muligvis kører denne kommando med administratorrettigheder:

        sudo npm install azure-cli -g

* **Windows**: Kør Winows installationsprogrammet (.msi-fil), der findes her: [Azure kommandolinjeværktøjer][windows-installer].


Hvis du vil teste installationen, skal du skrive `azure` ved kommandoprompten. Hvis installationen er fuldført, får du vist en liste over alle de tilgængelige `azure` kommandoer.

<h2><a id="CreateAccount"></a>Sådan oprettes en Azure-konto</h2>

Hvis du vil bruge værktøjerne Azure kommandolinjeparametre til Mac og Linux, skal du en Azure-konto.

Åbn en webbrowser og gå til [http://www.windowsazure.com] [ windowsazuredotcom] og klik på **gratis prøveversion** i øverste højre hjørne.

![Azure websted][Azure Web Site]

Følg vejledningen for at oprette en konto.

<h2><a id="Account"></a>Sådan hentes og importere publicere indstillinger</h2>

For at komme i gang, skal du først hente og importere dine indstillinger for publicering. Dette kan du bruge værktøjerne til at oprette og administrere Azure Services. Hente dine indstillinger for publicering, skal du bruge den `account download` kommandoen:

    azure account download

Dette åbner din standardbrowser og bede dig om at logge på portalen administration. Efter du har logget på, din `.publishsettings` fil, der hentes. Noter hvor denne fil er gemt.

Derefter importere den `.publishsettings` fil ved at køre følgende kommando, erstatter `{path to .publishsettings file}` med stien til din `.publishsettings` fil:

    azure account import {path to .publishsettings file}

Du kan fjerne alle de oplysninger, der er gemt ved den <code>import</code> kommando ved hjælp af den <code>account clear</code> kommandoen:

    azure account clear

Se en liste over indstillinger for `account` kommandoer skal du bruge den `-help` indstilling:

    azure account -help

Når du har importeret dine indstillinger for publicering, skal du slette den `.publishsettings` fil af sikkerhedsmæssige årsager.

> [AZURE.NOTE] Når du importerer publicere indstillinger, legitimationsoplysninger for at få adgang til abonnementet Azure er gemt i din `user` mappe. Din `user` mappe er beskyttet af dit operativsystem. Men det anbefales at udføre yderligere trin til at kryptere din `user` mappe. Du kan gøre det på følgende måder:    
> 
> - Ændre egenskaberne for mappen på Windows, eller bruge BitLocker.
> - På Mac, skal du slå FileVault til mappen.
> - Brug funktionen krypteret hjem directory på Ubuntu. Andre Linux salgsdistributioner tilbyder tilsvarende funktioner.

Du er nu klar til at blive oprettelse og administration af Azure websteder og virtuelle Azure-computere.  

<h2><a id="WebSites"></a>Hvordan du kan oprette og administrere et websted, der Azure</h2>

###<a name="create-a-website"></a>Oprette et websted

Hvis du vil oprette et websted, der Azure, skal du først oprette en tom mappe med navnet `MySite` og gå til denne mappe.

Derefter køre følgende kommando:

    azure site create MySite --git

Output fra denne kommando skal indeholde standard URL-adressen for det nyoprettede websted. Den `--git` indstilling kan du bruge ciffer til at udgive dit websted ved at oprette typer lagre ciffer i begge adresselisten lokalt program og i dit websted datacenter. Bemærk, at hvis din lokale mappe er allerede et ciffer lager, kommandoen føjer en ny fjernbetjening til den eksisterende lager, der peger på lager i dit websted datacenter.

Bemærk, at du kan udføre på `azure site create` kommando med en af følgende indstillinger:

* `--location [location name]`. Denne indstilling kan du angive placeringen af det datacenter, hvor dit websted er oprettet (f.eks. "Vest os"). Hvis du udelader denne indstilling, bliver du promted at vælge en placering.
* `--hostname [custom host name]`. Denne indstilling kan du angive en brugerdefineret hostname til dit websted.

Du kan derefter føje indhold til adresselisten websted. Bruge almindelige ciffer flow (`git add`, `git commit`) til at bekræfte dit indhold. Brug følgende ciffer kommando til at overføre din webstedsindhold til Azure: 

    git push azure master

###<a name="set-up-publishing-from-github"></a>Konfigurer udgivelse fra GitHub

For at konfigurere fortløbende udgivelse fra et GitHub lager skal bruge den `--GitHub` indstilling, når du opretter et websted:

    auzre site create MySite --github --githubusername username --githubpassword password --githubrepository githubuser/reponame

Hvis du har en lokal kopi af et GitHub lager, eller hvis du har et lager med en enkelt ekstern reference til et GitHub lager, publicere denne kommando automatisk kode i GitHub lager til dit websted. Derefter vil de ændringer, der overføres til GitHub lager automatisk offentliggjort til dit websted.

Når du konfigurerer udgivelse fra GitHub, er den standard-gren, bruges den overordnede gren. Hvis du vil angive en anden gren, skal du udføre følgende kommando fra din lokale lager:

    azure site repository <branch name>

###<a name="configure-app-settings"></a>Konfigurere indstillinger for

Indstillinger for App er nøgle-værdi-par, der er tilgængelige i dit program på kørselstidspunktet. Når du angiver for en Azure-websted, tilsidesætter app indstillingsværdier indstillinger med den samme nøgle, der er defineret i webstedets Web.config-fil. App indstillinger er tilgængelige som miljøvariabler til Node.js og PHP-programmer. I følgende eksempel viser, hvordan du angive et par nøgle-værdi:

    azure site config add <key>=<value> 

For at se en liste over alle nøgleværdi par, skal du bruge følgende:

    azure site config list 

Eller hvis du kender nøglen og vil have vist værdien, kan du bruge:

    azure site config get <key> 

Hvis du vil ændre værdien af en eksisterende nøgle skal du først fjerne den eksisterende nøgle og derefter tilføje den igen. Kommandoen Ryd er:

    azure site config clear <key> 

###<a name="list-and-show-sites"></a>Liste og Vis websteder

Brug følgende kommando for at få vist dine websteder:

    azure site list

For at få detaljerede oplysninger om et websted skal du bruge den `site show` kommandoen. I følgende eksempel viser detaljer for `MySite`:

    azure site show MySite

###<a name="stop-start-or-restart-a-site"></a>Stoppe, starte eller genstarte et websted

Du kan stoppe, starte eller genstarte et websted med den `site stop`, `site start`, eller `site restart` kommandoer:

    azure site stop MySite
    azure site start MySite
    azure site restart MySite

###<a name="delete-a-site"></a>Slette et websted

Til sidst skal du kan slette et websted med den `site delete` kommandoen:

    azure site delete MySite

Bemærk, at hvis du kører en af over kommandoer fra i den mappe, hvor du kørte `site create`, du behøver ikke at angive navnet på webstedet `MySite` som den sidste parameter.

Se en komplet liste over `site` kommandoer skal du bruge den `-help` indstilling:

    azure site -help 

<h2><a id="VMs"></a>Hvordan du kan oprette og administrere en Azure Virtual Machine</h2>

en Azure Virtual Machine er oprettet ud fra et virtuelt billede (en .vhd-fil), du angiver, eller, der er tilgængelige i galleriet billede. For at se billeder, der er tilgængelige, skal du bruge den `vm image list` kommandoen:

    azure vm image list

Du kan klargøre og starter en virtuel maskine fra en af de tilgængelige billeder med den `vm create` kommandoen. Følgende eksempel viser, hvordan du opretter en virtuel maskine Linux (kaldet `myVM`) fra et billede i galleriet billede (CentOS 6.2). Rod brugernavnet og adgangskoden til den virtuelle maskine er `myusername` og `Mypassw0rd` henholdsvis. (Bemærk, at den `--location` parameter angiver den datacenter, som den virtuelle maskine oprettes. Hvis du udelader de `--location` parameter, bliver du bedt om at vælge en placering.)

    azure vm create myVM OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd myusername --location "West US"

Kan du overveje at overføre den `--ssh` flag (Linux) eller `--rdp` flag (Windows) for at `vm create` til at aktivere remote forbindelser til den nyligt oprettede virtuelle maskine.

Hvis du hellere vil klargøre en virtuel maskine fra et brugerdefineret billede, kan du oprette et billede fra en .vhd-fil med den `vm image create` kommando, og brug den `vm create` kommandoen til at klargøre den virtuelle maskine. Følgende eksempel viser, hvordan du opretter et Linux-billede (kaldet `myImage`) fra en lokal .vhd-fil. (Den `--location` parameter angiver de data, hvor billedet er gemt.)

    azure vm image create myImage /path/to/myImage.vhd --os linux --location "West US"

I stedet for at oprette et billede fra en lokal .vhd, kan du oprette et billede fra en .vhd, der er gemt i Azure Blob-lager. Du kan gøre dette med den `blob-url` parameter:

    azure vm image create myImage --blob-url <url to .vhd in Blob Storage> --os linux

Når du har oprettet et billede, kan du klargør et virtuelt fra billedet ved hjælp af `vm create`. Kommandoen nedenfor opretter en virtuel maskine, kaldet `myVM` fra det billede, der er oprettet ovenfor (`myImage`).

    azure vm create myVM myImage myusername --location "West US"

Når du har klargjort en virtuel maskine, kan du vil oprette slutpunkter for at tillade fjernadgang til din virtuelle maskine (for eksempel). I følgende eksempel bruges den `vm create endpoint` kommando for at åbne eksterne port 22 og lokale port 22 på `myVM`:

    azure vm endpoint create myVM 22 22

Du kan få detaljerede oplysninger om en virtuel maskine (herunder IP-adresse, DNS-navn og oplysninger om slutpunkt) med den `vm show` kommandoen:

    azure vm show myVM

Lukke, starter, eller genstart den virtuelle maskine skal du bruge en af følgende kommandoer:

    azure vm shutdown myVM
    azure vm start myVM
    azure vm restart myVM

Og til sidst skal bruge for at slette VM, skal den `vm delete` kommandoen:

    azure vm delete myVM

Brug af en komplet liste over kommandoer til oprettelse og administration af virtuelle maskiner, den `-h` indstilling:

    azure vm -h

<!-- IMAGES -->
[Azure Web Site]: ./media/crossplat-cmd-tools/freetrial.png

<!-- LINKS -->
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/?LinkID=275464
[reference-docs]: http://go.microsoft.com/fwlink/?LinkId=252246
[windowsazuredotcom]: http://www.windowsazure.com

