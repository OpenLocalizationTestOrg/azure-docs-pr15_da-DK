<properties
    pageTitle="Oprette en Hej verden skybaseret tjeneste til Azure i Eklipse"
    description="Lær at oprette en simpel Hej verden-program ved hjælp af værktøjerne Azure for Eklipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690944.aspx -->

# <a name="create-a-hello-world-cloud-service-for-azure-in-eclipse"></a>Oprette en Hej verden skybaseret tjeneste til Azure i Eklipse #

Følgende trin viser, hvordan du opretter og installerer en grundlæggende JSP program tilladelse til at Azure med Azure-værktøjskassen til Eklipse. Et eksempel på JSP vises for enkel, men meget lignende trin der ville være relevante for en Java servlet omfang er bekymrede for Azure-installation.

Programmet vil se ud som følger:

![][ic600360]

## <a name="prerequisites"></a>Forudsætninger ##

* En Java udvikler Kit (JDK), v 1,7 eller nyere.
* Eklipse IDE for Java EE udviklere småt eller nyere. Dette kan hentes fra <http://www.eclipse.org/downloads/>.
* En fordeling af en Java-baseret webserver eller programserver som Apache Tomcat, GlassFish, JBoss programserver, Jetty eller IBM® WebSphere® programmet Server frit Core.
* Et Azure abonnement, som kan hentes fra <http://azure.microsoft.com/pricing/purchase-options/>.
* Azure værktøjskassen til Eklipse. Du kan finde flere oplysninger, kan du se [installere Azure-værktøjskassen til Eklipse][].

## <a name="to-create-a-hello-world-application"></a>Sådan oprettes et Hej verden program ##

Først skal begynder vi til at oprette et Java-projekt.

*  Start Eklipse, og klik på menuen **filer**, klik på **Ny**, og klik derefter på **Dynamiske webprojekt**. (Hvis du ikke kan se **Dynamisk webprojekt** er angivet som en tilgængelig project ved klik på **filer**, **Ny**gør derefter følgende: Klik på **filer**, klik på **Ny**, klik på **projekt...**, udvide **Web**, klik på **Dynamisk webprojekt**og klikke på **Næste**.)
*  Navngive projektet **MyHelloWorld**henblik på dette selvstudium. (Sikre, at du bruger dette navn, efterfølgende trin i dette selvstudium forvente KRIG filen til navngives MyHelloWorld). Skærmen vises som følger: ![][ic589576]
* Klik på **Udfør**.
* Udvid **MyHelloWorld**i Eklipses Projektstifinder visning. Højreklik på **WebContent**, klik på **Ny**, og klik derefter på **JSP fil**.
* Navngiv filen **index.jsp**i dialogboksen **Ny JSP-fil** . Du kan huske den overordnede mappe som **MyHelloWorld/WebContent**, som vist i følgende:  ![][ic659262]
* Vælg **Ny JSP fil (html)** for anvendelse af dette selvstudium i dialogboksen **Vælg JSP skabelon** , og klik på **Udfør**.
* Tilføj, når filen index.jsp åbner i Eklipse i tekst skal vises dynamisk **Hej verden!** inden for den nuværende `<body>` element. Din opdaterede `<body>` indhold skal vises som følgende:
```
    <body>
    <b><% out.println("Hello World!"); %></b>
    </body>
```
* Gem index.jsp.

## <a name="to-deploy-your-application-to-azure-the-quick-and-simple-way"></a>Installere programmet til Azure, hurtig og nem måde ##

Så snart du har et Java webprogram, der er klar til at teste, kan du bruge følgende genvej til at prøve det direkte på Azure skyen.

1. Klik på **MyHelloWorld**i Eklipses Projektstifinder.
1. Klik på knappen **Publicer** rullelisten værktøjslinjen Eklipse, og klik derefter på **Publicer som Azure skybaseret tjeneste**
    ![][publishDropdownButton]
1. Hvis du udgiver dette program til Azure for første gang, og du ikke har oprettet en Azure-installationsprojekt til dette program, før, oprettes en Azure-installation project automatisk for dig. Du bør se følgende meddelelse, hvor du kan også se pakken JDK og programserver, som skal installeres automatisk for at køre programmet.
    ![][ic789598]

    Denne genvej fremgangsmåde gør det muligt for en hurtig og nem måde at teste dit program i Azure uden at konfigurere en bestemt server eller JDK, der er forskellig fra standardindstillingerne. Hvis du er tilfreds med standardindstillingerne, kan du klikke på **OK** for at fortsætte med følgende trin.
    Men hvis du vil ændre JDK eller programserver skal bruges til dit program, kan du gøre det senere ved at redigere projektet Azure-installation, der er oprettet automatisk for dig, eller du kan klikke på **Annuller** nu og Læs **om Azure installation projekter sektion** af dette selvstudium.
1. I dialogboksen **Udgiv til Azure** :
    1. Hvis der ikke er nogen abonnementer til at vælge på listen **abonnement** endnu, skal du følge disse trin for at importere dine abonnementsoplysninger:
        1. Klik på **Importer fra fil med PUBLICER-indstillinger**.
        1. Klik på **Hent PUBLICER – indstillinger fil**i dialogboksen **Importér abonnementsoplysninger** . Hvis du endnu ikke er logget på din Azure-konto, bliver du bedt om at logge på. Og du bliver bedt om at publicere indstillingsfil Gem en Azure. Gemme den på din lokale computer.
        1. Stadig i dialogboksen **Importér abonnementsoplysninger** , skal du klikke på knappen **Gennemse** , Vælg Publicer indstillinger-fil, du har gemt lokalt i det forrige trin og klik derefter på **Åbn**. Skærmen skal ligne følgende: ![][ic644267]
        1. Klik på **OK**.
    1. Vælg det abonnement, du vil bruge til din installation **abonnement**.
    1. Vælg den konto, lagerplads, du vil bruge for **lagerplads konto**, eller klik på **Ny** for at oprette en ny konto lagerplads.
    1. Vælg skytjenesten, du vil bruge til **navnet på tjenesten**, eller klik på **Ny** for at oprette en ny tjeneste i skyen.
    1. **Target OS**, skal du ved at vælge versionen af det operativsystem, du vil bruge til din installation.
    1. Vælg **midlertidige**for anvendelsen af dette selvstudium for **Målgruppens miljø**. (Når du er klar til at installere på webstedet fremstilling, du vil ændre dette **fremstilling**).
    1. Valgfrit: Kontrollér, at **overskrive tidligere installation** er markeret, hvis du vil nye installationen til automatisk at overskrive den forrige installation. Når du aktiverer denne indstilling, skal du ikke oplevelse "409 konflikt" problemer, når du udgiver på den samme placering.
        Bemærk, at dialogboksen **Udgiv til Azure** indeholder en sektion til **Fjernadgang**. Fjernadgang ikke er aktiveret som standard, og vi, at det ikke i dette eksempel. Hvis du vil aktivere fjernadgang, ville du angive et brugernavn og adgangskode for at bruge, når du logger på, fra en fjernplacering. Du kan finde flere oplysninger om fjernadgang, [Aktivere fjernadgang til Azure installationer i Eklipse][].
        Dialogboksen **Udgiv til Azure** vises som følger: ![][ic719488]
1. Klik på **Publicer** publicere til det midlertidige miljø.
    Klik på **Ja**, når du bliver bedt om at udføre en komplet build. Dette kan tage flere minutter, før det første build.
    En **Azure aktivitetslog** vises i sektionen Eklipse faner visninger.
    ![][ic719489]
   Du kan bruge denne log samt visningen **Console** at få vist status for din installation. Et alternativ er at logge på [Azure Management-portalen][], og Brug sektionen **Cloud Services** til at overvåge statussen.
1. Når installationen er blevet installeret, vises **Azure aktivitetslog** statussen **udgivet**. Klik på **Published**, som vist i det følgende billede og åbnes en forekomst af din installation i din webbrowser.
    ![][ic719490]

Fordi det var en installation til et midlertidigt miljø, bliver DNS-navnet i formatet http://&lt;*guid*&gt;. cloudapp.net, og URL-adressen er indeholder DNS-navnet plus et suffiks for dit program. For eksempel http://447564652c20426f6220526f636b7321.cloudapp.net/MyHelloWorld. ( **MyHelloWorld** del er store og små bogstaver). Du kan også se DNS-navnet, hvis du klikker på navnet på installation i portalen Azure Platform Management (inden for Skytjenester del af portalen management).

Selvom denne gennemgang fulgte en installation for at det midlertidige miljø, en installation for at fremstilling følger samme fremgangsmåde, undtagen i dialogboksen **Udgiv til Azure** skal du vælge **fremstilling** i stedet for at **arrangere** **Målgruppens miljø**. En installation til fremstilling resultater i en URL-adresse, der er baseret på dine valg i stedet for en GUID, som bruges til midlertidige DNS-navn.

>[AZURE.WARNING] På dette tidspunkt har du installeret dit Azure program til skyen. Men opdager, at en udløst programmet, selvom den ikke kører, fortsætter med at periodisere fakturerbar tid for dit abonnement før du fortsætter. Det er meget vigtigt, at du sletter uønskede installationer fra abonnementet Azure.

## <a name="about-azure-deployment-projects"></a>Om Azure-installation projekter ##

Der er behov for at installere en eller flere Java-programmer til Azure, et projekt til installation af Azure. Afspilning på "pakke", som dine programmer skal der skal ombrydes i for at kunne udgives på Azure rolle.

Ud over oplysninger om dine programmer, en Azure-installation project indeholder også oplysninger om andre vigtige komponenter af din installation vigtigst: objektbeholderen programmet server til at køre din online i, og at Java runtime skal køre den på. Azure understøtter et stort udvalg af Java eksekverbar kode og Java application servere, kan du vælge mellem.

Selvom eksemplet bruges her er meget nemmere for nemmere at forstå, kan et projekt til Azure-installation også indeholde andre vigtige konfigurationsoplysninger, der gør det muligt at oprette næsten vilkårligt komplekse SVG meget tilgængelige, og med flere lag skytjenester med dine programmer. Du kan aktivere **session forbindelse ("sticky sessioner")**, **Hurtig cachelagring**, **ekstern fejlfinding**, **SSL overføre**, **firewall/port routing**, **fjernadgang**og et antal andre avancerede funktioner.

Hvis du har fuldført den forrige sektion i dette selvstudium ("til at udrulle dit program til Azure, hurtig og nem måde"), du får nu vist et projekt med Azure-installation i Projektstifinder genereres automatisk for dig og med navnet "**MyHelloWorld_onAzure**".

Du kan også har startet dette selvstudium ved først at oprette en tom Azure-installation project dig selv og derefter tilføje din program(mer) til den. Det er en længere proces, men giver dig bedre kontrol over den indledende konfiguration fra begyndelsen.

Hvis du vil oprette et nyt projekt Azure-installation fra bunden, skal du klikke på knappen **Nyt Azure installationsprojekt** ![][ic710876].

Uanset om du arbejder med et eksisterende projekt Azure-installation, eller oprette en fra bunden, er det muligt at ændre dets konfigurationsindstillinger og -komponenter, som JDK eller programserver, lige så nemt når som helst.

Sådan ændrer du JDK eller application server eller listen program i et eksisterende projekt Azure-installation:

1. Udvid projektnoden (fx **MyHelloWorld_onAzure**) i Projektstifinder
2. Højreklik på **WorkerRole1**
3. Udvid undermenuen **Azure** i genvejsmenuen
4. Klik på **serverkonfiguration**

Uanset om du i gang disse trin til konfiguration af server ved at redigere et eksisterende projekt Azure-installation, som vist ovenfor, eller opretter en ny fra bunden, får du vist den samme type dialogbokse, så du kan konfigurere din JDK, server og programmet komponenter. Få mere for at vide hvordan du kan ændre indstillingerne i dialogboksene, for eksempel for at ændre JDK, application server og tilføje eller fjerne programmer i en installation, artiklen [Egenskaber for Server-konfiguration][] .

## <a name="windows-only-to-deploy-your-application-to-the-compute-emulator"></a>Kun Windows: installere programmet til Beregn emulatoren ##

>[AZURE.NOTE] Azure emulatoren er kun tilgængelig i Windows. Ignorere dette afsnit, hvis du bruger et operativsystem end Windows.

Hvis du har oprettet et projekt med Azure-installation fremgangsmåde, som er beskrevet tidligere, det vil sige implicit ved at publicere dit program til Azure, JDK og programmet er servere, der blevet konfigureret til skyen, men ikke til lokale emulering. For at forberede dit projekt til test i lokale emulatoren skal du følge disse trin:

1. Klik på **MyHelloWorld_onAzure**i Eklipses Projektstifinder.
1. Højreklik på **WorkerRole1**.
1. Udvid undermenuen **Azure** i genvejsmenuen.
1. Klik på **serverkonfiguration**.
1. Kontrollere Hvis værktøjerne allerede har konfigureret en standard under fanen **JDK** lokale JDK for dig. Hvis ikke, eller hvis du vil ændre formodet standardindstillingerne, sikre, at afkrydsningsfeltet **Brug JDK fra denne filsti til test lokalt** er markeret, og den JDK installationsplacering, du vil bruge er angivet. Hvis du vil ændre det, skal du klikke på knappen **Gennemse** , og ved hjælp af kontrolelementet Gennemse, Vælg mappeplaceringen for JDK til brug.
1. Klik på fanen **Server** .
1. Angiv stien til et lokalt installerede server, der svarer til den type og overordnet versionsnummeret på den server, der er valgt øverst i dialogboksen under afkrydsningsfeltet **Implementer en server af denne type** i tekstboksen **lokale server sti** nederst i dialogboksen. Hvis du vil bruge en anden type eller overordnet version af application server, skal du ændre markeringen under pågældende afkrydsningsfelt først.
1. Klik på **OK**.
1. Klik på knappen **Kør i Azure Emulator** i værktøjslinjen Eklipse ![][ic710879]. Hvis knappen **Kør i Azure Emulator** ikke er aktiveret, Sørg for, at **MyHelloWorld_onAzure** er valgt i Eklipses Projektstifinder, og Sørg for, at Eklipses Projektstifinder har fokus som det aktuelle vindue. Dette først starter en fuld version af dit projekt og derefter starte dit Java webprogram i Beregn emulatoren. (Note, afhængigt af computerens ydeevne karakteristika første Opret kan tage mellem et par sekunder at et par minutter, men efterfølgende opbygger får hurtigere.) Når det første build trin er fuldført, kan du blive spurgt ved Windows kontrolelement (Brugerkonti) tillade denne kommando til at foretage ændringer til din computer. Klik på **Ja**.

>[AZURE.IMPORTANT] Hvis du ikke kan se bliver bedt om det kontrol af Brugerkonti, Markér proceslinjen i Windows til ikonet for kontrol af Brugerkonti og klikke på den første. Nogle gange kontrol af Brugerkonti prompt vises ikke som et øverste vindue, men vises kun som et ikon på proceslinjen.

1. Undersøge output fra Beregn emulatoren Brugergrænsefladen til at finde ud af, om der er problemer med dit projekt. Afhængigt af indholdet af din installation, kan det tage et par minutter for dit program startes fuldt ud i Beregn emulatoren.
1. Start din browser, og brug af URL-adressen `http://localhost:8080/MyHelloWorld` som adressen (den `MyHelloWorld` del af URL-adressen er store og små bogstaver). Skal du se afsnittet MyHelloWorld programmet (output fra index.jsp), svarende til følgende billede: ![][ic589579]

Når du er klar til at stoppe med at dit program i at køre i Beregn emulatoren, værktøjslinjen Eklipse, klikke på knappen **Nulstil Azure Emulator** ![][ic710880].

## <a name="to-delete-your-deployment"></a>Slette din installation ##

Hvis du vil slette din installation i Azure-værktøjskassen til Eklipse, Sørg for, at **MyHelloWorld_onAzure** er valgt i Eklipses Projektstifinder, sikre Projektstifinder Eklipse har det aktuelle vindue fokusere, og klik derefter på knappen **Annuller publicering** ![][ic710883], på værktøjslinjen Eklipse. (Du kan gøre den samme handling ved at højreklikke på **MyHelloWorld_onAzure** i Eklipses Projektstifinder, klikke på **Azure** og derefter klikke på **Undeploy fra Azure skyen**). Derved vises dialogboksen **Annullere publiceringen af Azure Project** .

![][ic719491]

Vælge tjenesten abonnement og skyen, der indeholder din installation, Vælg den installation, du vil slette, og klik derefter på **Annuller publicering**.

(I stedet for at bruge værktøjerne til at slette installationen, er at bruge afsnittet **Cloud Services** i portalen Azure administration: gå til din installation, markere den, og klik derefter på knappen **Slet** . Dette stopper, og slet derefter installationen. Hvis du kun vil stoppe installationen, og du ikke slette den, skal du klikke på knappen **Stop** i stedet for knappen **Slet** , men som nævnt ovenfor, hvis du ikke sletter installationen, fortsat fakturerbar gebyrer periodiseres til din installation, selvom det ikke er længere).

## <a name="see-also"></a>Se også ##

[Azure-værktøjskassen til Eklipse][]

[Installation af Azure værktøjerne til Eklipse][] 

[Hvad er nyt i Azure værktøjerne til Eklipse][]

Du kan finde flere oplysninger om brug af Azure med Java, [Azure Java Developer Center][].

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Management-portalen]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Role Properties]: http://go.microsoft.com/fwlink/?LinkID=699525
[Azure-værktøjskassen til Eklipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Aktivere fjernadgang til Azure installationer i Eklipse]: http://go.microsoft.com/fwlink/?LinkID=699538
[Installation af Azure værktøjerne til Eklipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Egenskaber for Server-konfiguration]: http://go.microsoft.com/fwlink/?LinkID=699525#server_configuration_properties
[Hvad er nyt i Azure værktøjerne til Eklipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic589576]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589576.png
[ic589579]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589579.png
[ic600360]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic600360.png
[ic644267]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic644267.png
[ic659262]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic659262.png
[ic710876]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710876.png
[ic710879]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710879.png
[ic710880]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710880.png
[ic710882]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710882.png
[ic710883]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710883.png
[ic719488]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719488.png
[ic719489]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719489.png
[ic719490]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719490.png
[ic719491]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719491.png
[ic789598]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic789598.png
[publishDropdownButton]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/publishDropdownButton.png
