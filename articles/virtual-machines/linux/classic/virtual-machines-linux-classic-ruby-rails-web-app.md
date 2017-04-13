<properties
    pageTitle="Være vært for en fonetisk på gør websted på en Linux VM | Microsoft Azure"
    description="Konfigurere og være vært for en fonetisk på gør-baserede websted på Azure ved hjælp af en Linux virtuel maskine."
    services="virtual-machines-linux"
    documentationCenter="ruby"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="web"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="ruby-on-rails-web-application-on-an-azure-vm"></a>Fonetisk på gør webprogram på en Azure VM

Dette selvstudium viser, hvordan du være vært for en fonetisk på gør websted på Azure ved hjælp af en Linux virtuel maskine.  

Dette selvstudium blev bekræftet ved at anvende Ubuntu Server 14.04 LTS. Hvis du bruger en anden Linux fordeling, skal du muligvis ændre trinnene for at installere gør.

> [AZURE.IMPORTANT] Azure har to forskellige installation modeller til oprettelse og arbejde med ressourcer: [ressourcestyring og klassisk](../../../resource-manager-deployment-model.md).  Denne artikel omhandler ved hjælp af den klassiske implementeringsmodel. Microsoft anbefaler, at de fleste nye installationer bruger ressourcestyring modellen.

## <a name="create-an-azure-vm"></a>Oprette en Azure VM

Starte med at oprette en Azure VM med et Linux billede.

Hvis du vil oprette VM, kan du bruge Azure klassisk portalen eller Azure kommandolinjen (CLI).

### <a name="azure-management-portal"></a>Azure Management-portalen

1. Logge på [Azure klassisk portal](http://manage.windowsazure.com)
2. Klik på **nyt** > **beregne** > **virtuelt** > **Hurtig oprettelse**. Vælge et billede af Linux.
3. Angiv en adgangskode.

Efter VM er klargjort, klik på VM navn, og klik på **Dashboard**. Find SSH slutpunktet, er angivet under **SSH detaljer**.

### <a name="azure-cli"></a>Azure CLI

Følg trinnene i [oprette et virtuelt kører Linux][vm-instructions].

Efter VM er klargjort, kan du få SSH slutpunktet ved at køre følgende kommando:

    azure vm endpoint list <vm-name>  

## <a name="install-ruby-on-rails"></a>Installere fonetisk på gør

1. Brug SSH til at oprette forbindelse til VM.

2. Brug følgende kommandoer til at installere fonetisk på VM fra session SSH:

        sudo apt-get update -y
        sudo apt-get upgrade -y
        sudo apt-get install ruby ruby-dev build-essential libsqlite3-dev zlib1g-dev nodejs -y

    Installationen kan tage et par minutter. Når den er fuldført, kan du bruge følgende kommando for at bekræfte, at fonetisk er installeret:

        ruby -v

    Dette returnerer versionen af fonetisk, der blev installeret.

3. Brug følgende kommando for at installere gør:

        sudo gem install rails --no-rdoc --no-ri -V

    Brug – ingen rdoc og – ingen ri flagene at springe installerer dokumentation, hvilket er hurtigere.
    Denne kommando tager sandsynligt lang tid at udføre, så tilføje V - Viser oplysninger om status for installationen.

## <a name="create-and-run-an-app"></a>Oprette og køre en app

Mens stadig logget via SSH, skal du køre følgende kommandoer:

    rails new myapp
    cd myapp
    rails server -b 0.0.0.0 -p 3000

Kommandoen [nyt](http://guides.rubyonrails.org/command_line.html#rails-new) opretter en ny gør app. Kommandoen [server](http://guides.rubyonrails.org/command_line.html#rails-server) starter webserveren WEBrick, der følger med gør. (Til daglig drift, du vil sandsynligvis vil bruge en anden server, som Unicorn eller passager.)

Du bør se output, der svarer til følgende.

    => Booting WEBrick
    => Rails 4.2.1 application starting in development on http://0.0.0.0:3000
    => Run `rails server -h` for more startup options
    => Ctrl-C to shutdown server
    [2015-06-09 23:34:23] INFO  WEBrick 1.3.1
    [2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
    [2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000

## <a name="add-an-endpoint"></a>Tilføje et slutpunkt

1. Gå til [Azure klassisk portal] [ management-portal] og vælge din VM.

    ![virtuelt liste][vmlist]

2. Vælg **SLUTPUNKTER** øverst på siden, og klik derefter på **+ Tilføj SLUTPUNKT** nederst på siden.

    ![slutpunkter side][endpoints]

3. Vælg "Tilføj et enkeltstående slutpunkt", og klik på pilen **Næste** i dialogboksen **Tilføj SLUTPUNKT** .

    ![dialogboksen Ny slutpunkt][new-endpoint1]

3. Angiv følgende oplysninger i den næste dialogboksside:

    * **Navn**: HTTP

    * **Protokol**: TCP

    * **Offentlige PORT**: 80

    * **Privat PORT**: 3000

    Dette vil oprette en offentlig port 80, der bruges til at distribuere trafik til den private port af 3000, hvor serveren, der gør lytter.

    ![dialogboksen Ny slutpunkt][new-endpoint]

4. Klik på markeringen i afkrydsningsfeltet for at gemme slutpunktet.

5. Skal vises en meddelelse, hvor der står **Opdatering igangværende**. Når denne meddelelse forsvinder, er slutpunktet er aktiv. Du kan nu teste dit program ved at navigere til DNS-navnet på din virtuelle maskine. Webstedet skal se ud som følger:

    ![gør standardside][default-rails-cloud]

## <a name="next-steps"></a>Næste trin

I dette selvstudium har du de fleste af trinnene manuelt. I et produktionsmiljø, skal du skrive din app på en udvikling maskine og installerer det til Azure VM. De fleste fremstilling miljøer hoste også gør programmet sammen med en anden serverproces som Apache eller NginX, hvilke håndtag anmode om routing til flere forekomster af gør programmet og fungerer statisk ressourcer. Du kan finde flere oplysninger http://rubyonrails.org/deploy/.

Hvis du vil vide mere om fonetisk på gør skal du besøge [fonetisk på gør hjælpelinjer][rails-guides].

For at bruge Azure tjenester fra din fonetisk program skal du se:

* [Gemme ustrukturerede data ved hjælp af BLOB][blobs]

* [Store nøgle/værdi-par ved hjælp af tabeller][tables]

* [Levere stor båndbredde indhold med netværk til levering af indhold][cdn-howto]

<!-- WA.com links -->
[blobs]: ../../../storage/storage-ruby-how-to-use-blob-storage.md
[cdn-howto]: https://azure.microsoft.com/develop/ruby/app-services/
[management-portal]: https://manage.windowsazure.com/
[tables]: ../../../storage/storage-ruby-how-to-use-table-storage.md
[vm-instructions]: ../../virtual-machines-linux-classic-createportal.md

<!-- External Links -->
[rails-guides]: http://guides.rubyonrails.org/
[sqlite3]: http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/basicrailscloud.png
[vmlist]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/vmlist.png
[endpoints]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/endpoints.png
[new-endpoint]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint.png
[new-endpoint1]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint1.png
