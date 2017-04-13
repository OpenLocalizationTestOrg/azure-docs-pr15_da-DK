<properties
   pageTitle="Azure virtuelt-installation med kok | Microsoft Azure"
   description="Lær at bruge kok til at gøre automatiseret virtuelt installation og konfiguration på Microsoft Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="diegoviso"
   manager="timlt"
   tags="azure-service-management,azure-resource-manager"
   editor=""/>

<tags ms.service="virtual-machines-windows" ms.workload="infrastructure-services"
ms.tgt_pltfrm="vm-multiple"
ms.devlang="na"
ms.topic="article"
ms.date="05/19/2015"
ms.author="diviso"/>

# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatisere Azure virtuelt-installation med kok

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Kok er et godt værktøj til at levere automatisering og beskedteksten tilstand konfigurationer.

Med vores nyeste version i skyen-api indeholder kok nem integration med Azure, hvorved du får mulighed for at klargøre og installere konfiguration stater gennem en enkelt kommando.

I denne artikel viser jeg dig, hvordan du konfigurerer dit kok miljø til at klargøre Azure virtuelle maskiner og hjælper dig med at oprette en politik eller "Kogebog" og derefter anvender denne kogebog for en Azure virtuelt.

Lad os starte!

## <a name="chef-basics"></a>Grundlæggende om kok

Før du begynder, anbefaler jeg, du har gennemgået de grundlæggende begreber i kok. Der er velegnet materiale <a href="http://www.chef.io/chef" target="_blank">her</a> , og jeg anbefaler, at du har en hurtig læse, før du forsøger denne gennemgang. Jeg vil dog opsummere grundlæggende, før vi går i gang.

Følgende diagram viser den overordnede kok arkitektur.

![][2]

Kok består af tre primære arkitektonisk komponenter: kok Server, kok klienten (node) og kok arbejdsstation.

Kok serveren er vores management punkt, og der er to muligheder for kok Server: en hostet løsning eller en lokal løsning. Vi skal bruge en hostet løsning.

Kok klienten (node) er den agent, der er placeret på servere du administrerer.

Kok arbejdsstationen er vores administrator hvor vi oprette vores politikker og udføre vores management-kommandoer. Vi køre kommandoen **Kniv** fra kok arbejdsstationen til at administrere vores infrastruktur.

Der findes også som "Cookbooks" og "Opskrifter" repræsenterer. Dette er effektivt politikker vi definere og anvende på vores servere.

## <a name="preparing-the-workstation"></a>Forberede arbejdsstationen

Først skal du kan forberede arbejdsstationen. Jeg bruger en standard Windows-arbejdsstation. Vi har brug at oprette en mappe for at gemme vores config filer og cookbooks.

Først oprette en mappe med navnet C:\chef.

Oprette en anden mappe med navnet c:\chef\cookbooks.

Vi skal nu hente vores Azure indstillingsfil, så kok kan kommunikere med vores Azure abonnement.

Hente din publicere indstillinger fra [her](https://manage.windowsazure.com/publishsettings/).

Gemme Publicer indstillingsfilen i C:\chef.

##<a name="creating-a-managed-chef-account"></a>Oprette en administreret kok-konto

Tilmelde sig en hostet kok konto [her](https://manage.chef.io/signup).

Under tilmeldingsprocessen, bliver du bedt om at oprette en ny organisation.

![][3]

Når din organisation er oprettet, kan du hente starter kit.

![][4]

> [AZURE.NOTE] Hvis du bliver spurgt, om advarsel om, at dine nøgler nulstilles, er det ok at fortsætte, som vi har ingen eksisterende infrastruktur konfigureret endnu.

Dette starter kit zip-filen indeholder din organisation config filer og taster.

##<a name="configuring-the-chef-workstation"></a>Konfigurere kok arbejdsstationen

Udtrække indholdet af kok-starter.zip til C:\chef.

Kopiere alle filer under kok-starter\chef-repo\.kok til adresselisten c:\chef.

Mappen skal nu ligne eksemplet nedenfor.

![][5]

Nu skal du have fire filer herunder Azure publicering filen i roden af c:\chef.

PEM-filerne indeholder din organisation og administrator private nøgler til kommunikation, mens filen knife.rb indeholder konfigurationen af Kniv. Vi skal redigere filen knife.rb.

Åbn filen i din editor valg og ændre "cookbook_path" ved at fjerne den /.. / fra sti, så den vises som vist næste.

    cookbook_path  ["#{current_dir}/cookbooks"]

Tilføj også følgende linje afspejler navnet på din Azure publicere indstillingsfil.

    knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

Filen knife.rb skal nu ligne eksemplet nedenfor.

![][6]

Disse linjer sikrer, at Kniv refererer til mappen cookbooks under c:\chef\cookbooks, og også bruger vores Azure publiceringsindstillinger fil under Azure handlinger.

## <a name="installing-the-chef-development-kit"></a>Installere kok Development Kit

Næste [downloade og installere](http://downloads.getchef.com/chef-dk/windows) ChefDK (kok Development Kit) til at konfigurere din kok arbejdsstation.

![][7]

Installere på standardplaceringen af c:\opscode. Denne installation tager omkring 10 minutter.

Bekræft din PATH-variabel indeholder poster for C:\opscode\chefdk\bin; C:\opscode\chefdk\embedded\bin;c:\users\yourusername\.chefdk\gem\ruby\2.0.0\bin

Hvis de ikke er der, skal du kontrollere du tilføje disse stier!

*BEMÆRK RÆKKEFØLGEN AF STIEN ER VIGTIGT!* Hvis din opscode stier ikke er i den rigtige rækkefølge, har du problemer.

Genstart computeren, før du fortsætter.

Dernæst skal installeres vi filtypenavnet Kniv Azure. Dette giver Kniv med "Azure plug-in'et".

Kør følgende kommando.

    chef gem install knife-azure ––pre

> [AZURE.NOTE] Argumentet – pre sikrer, at du modtager den seneste SALGSLISTE version af Kniv Azure plug-in'et som giver adgang til de seneste API'er.

Det er muligt, at et antal afhængigheder også er installeret på samme tid.

![][8]


For at sikre, at alt er konfigureret korrekt, skal du køre følgende kommando.

    knife azure image list

Hvis alt er konfigureret korrekt, får du vist en liste over tilgængelige Azure billeder Rul gennem.

Tillykke. Arbejdsstationen er konfigureret!

##<a name="creating-a-cookbook"></a>Oprette en Kogebog

En Kogebog bruges af kok til at definere en række kommandoer, du vil udføre på din administrerede klient. Det er nemt at oprette en Kogebog, og vi bruge kommandoen **kok generere kogebog** til at generere vores Kogebog skabelon. Jeg vil kalder min Kogebog webserver som jeg gerne vil have en politik, der automatisk installerer IIS.

Kør følgende kommando under mappen C:\Chef.

    chef generate cookbook webserver

Der oprettes der en række filer under mappen C:\Chef\cookbooks\webserver. Vi skal nu definere sættet af kommandoer, ønsker vi vores kok klienten til at udføre på vores administrerede virtuel maskine.

Kommandoerne, der er gemt i filen default.rb. I denne fil, får jeg definerer en række kommandoer, der installerer IIS, starter IIS og kopierer en skabelonfil til mappen wwwroot.

Redigere filen C:\chef\cookbooks\webserver\recipes\default.rb, og Tilføj følgende linjer.

    powershell_script 'Install IIS' do
        action :run
        code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
        action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
        source 'Default.htm.erb'
        rights :read, 'Everyone'
    end

Gem filen, når du er færdig.

## <a name="creating-a-template"></a>Oprette en skabelon

Som tidligere nævnt skal vi oprette en skabelonfil, der skal bruges som vores default.html side.

Kør følgende kommando for at generere skabelonen.

    chef generate template webserver Default.htm

Gå til filen C:\chef\cookbooks\webserver\templates\default\Default.htm.erb. Redigere filen ved at tilføje nogle enkle "Hej verden" HTML-kode, og Gem derefter filen.



## <a name="upload-the-cookbook-to-the-chef-server"></a>Overføre Kogebog til serveren kok

I dette trin skal vil vi tage en kopi af den Kogebog, vi har oprettet på vores lokale computer og overføre den til kok Hosted serveren. Når overført, vises Kogebog under fanen **politik** .

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Installere en virtuel maskine med Kniv Azure

Vi vil nu installere en Azure virtuelt og anvende det "Webserver" Kogebog som installerer vores IIS-tjenesten og standard web webside.

Gøre dette, skal du bruge kommandoen **Kniv azure serveren oprette** .

Er eksempel på kommandoen vises næste.

    knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

Parametrene, der er ingen forklaring. Erstatte dine bestemte variabler, og Kør.

> [AZURE.NOTE] Gennem på kommandolinjen, jeg også automatisere slutpunkt netværk filter reglerne ved hjælp af parameteren – tcp-slutpunkter. Jeg har åbnet op port 80 og 3389 giver adgang til min webside og RDP-session.

Når du kører kommandoen, gå til Azure-portalen, og du får vist din computer og begynder at blive klargjort.

![][13]

Kommandoprompten vises næste.

![][10]

Når installationen er fuldført, skal vi ikke kunne oprette forbindelse til webtjenesten via port 80, som vi havde åbnet port, når vi klargjort virtuelt med kommandoen Kniv Azure. Denne virtuelt er den eneste virtuelle maskine i min skybaseret tjeneste, kan jeg oprette forbindelse med URL-adressen i skyen.

![][11]

Som du kan se, har jeg kreativ med min HTML-kode.

Glem ikke vi kan også oprette forbindelse via en RDP-session fra Azure klassisk portalen via port 3389.

Jeg håber dette har været nyttige! Gå og starte infrastrukturen som kode rejse med Azure i dag!


<!--Image references-->
[2]: ./media/virtual-machines-windows-chef-automation/2.png
[3]: ./media/virtual-machines-windows-chef-automation/3.png
[4]: ./media/virtual-machines-windows-chef-automation/4.png
[5]: ./media/virtual-machines-windows-chef-automation/5.png
[6]: ./media/virtual-machines-windows-chef-automation/6.png
[7]: ./media/virtual-machines-windows-chef-automation/7.png
[8]: ./media/virtual-machines-windows-chef-automation/8.png
[9]: ./media/virtual-machines-windows-chef-automation/9.png
[10]: ./media/virtual-machines-windows-chef-automation/10.png
[11]: ./media/virtual-machines-windows-chef-automation/11.png
[13]: ./media/virtual-machines-windows-chef-automation/13.png


<!--Link references-->
