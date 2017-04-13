<properties
    pageTitle="Aktivere fjernadgang til Azure installationer i Eklipse"
    description="Lær at aktivere fjernadgang til Azure-installationer med Azure-værktøjskassen til Eklipse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690951.aspx -->

# <a name="enabling-remote-access-for-azure-deployments-in-eclipse"></a>Aktivere fjernadgang til Azure installationer i Eklipse

Hjælp til fejlfinding af dine installationer, kan du aktivere og bruge fjernadgang til at oprette forbindelse til den virtuelle maskine, der er vært for din installation. Funktionen fjernadgang er baseret på RDP Remote Desktop Protocol (). Du kan konfigurere fjernadgang til din installation, når du har udgivet til Azure, eller hvis du bruger Eklipse med et Windows-operativsystem, kan du konfigurere fjernadgang, før du udgiver på Azure. Bemærk, at du en ekstern klient til stationære computere, der er kompatibelt med dit operativsystem for at oprette forbindelse til din installation virtuel maskine i Azure.

## <a name="how-to-enable-remote-access-before-you-deploy-to-azure"></a>Sådan aktiveres fjernadgang, inden du installerer til Azure

> [AZURE.NOTE] Hvis du vil aktivere fjernadgang, før du installerer programmet til Azure, skal du køre Eklipse i Windows.

Følgende billede viser dialogboksen bruges til at aktivere fjernadgang til **Fjernadgang** egenskaberne.

![][ic719494]

Der er to måder at få vist dialogboksen Egenskaber for **Fjernadgang** :

* Klik på linket **Avanceret** i afsnittet **Fjernadgang** i dialogboksen **Udgiv til Azure** .
* Åbn dialogboksen **Egenskaber** for projektet Azure.

Når du opretter et nyt projekt Azure-installation, vil projektet ikke have fjernadgang til som standard aktiveret. Du kan dog nemt aktivere fjernadgang til ved at angive brugernavn og din adgangskode i dialogboksen **Udgiv til Azure** . Fjernadgang adgangskoden er krypteret med x.509-certifikater. Hvis du ikke bruger give dit eget certifikat, der er baseret på kryptering på et selvsigneret certifikat, der er afsendt med Azure-plug-in til Eklipse. Denne selvsigneret certifikat er i mappen **cert** af projektet Azure, gemt både som en offentlig certifikatfil (SampleRemoteAccessPublic.cer) og som en personlige oplysninger Exchange (PFX) certifikatfil (SampleRemoteAccessPrivate.pfx). De indeholder den private nøgle til certifikatet, og den har en standardadgangskode, **Adgangskode1**. Da denne adgangskode er offentligheden bekendt, skal der dog bruges standardcertifikatet kun til at blive fortrolig formål ikke for et produktionsmiljø. Det andet end til at blive fortrolig formål, skal når du vil aktiveret remote sessioner for dine installationer, du klikke linket **Avanceret** i dialogboksen **Udgiv til Azure** til at angive dit eget certifikat. Bemærk, at du skal overføre PFX version af certifikatet til din hostet tjeneste i portalen Azure administration, så Azure kan dekryptere brugeradgangskode.

Resten af selvstudiet viser, hvordan du aktivere fjernadgang til et projekt til Azure-installation, som oprindeligt blev oprettet med fjernadgang til deaktiveret. Vi vil oprette et nyt selvsigneret certifikat til formål af dette selvstudium, og dens .pfx-fil har en adgangskode efter eget valg. Du har også mulighed for at bruge et certifikat, der er udstedt af et nøglecenter.

## <a name="how-to-enable-remote-access-after-you-have-deployed-to-azure"></a>Sådan aktiveres fjernadgang, efter du har installeret til Azure

Hvis du vil aktivere fjernadgang, når du har installeret til Azure, skal du følge nedenstående trin:

1. Log på administrationsportalen Azure ved hjælp af din Azure-konto
1. På listen over **Cloud Services**, Vælg din udløst skytjeneste
1. Klik på linket **Konfigurer** i skyen service websiden
1. Nederst på konfigurationssiden, skal du klikke på linket **Remote**
1. Når dialogboksen pop op-vises:
    * Angiv den rolle, du vil aktivere fjernadgang
    * Klik for at markere afkrydsningsfeltet **Aktivér Fjernskrivebord**
    * Angive et brugernavn og adgangskode, du vil bruge til fjernadgang
    * Vælg certifikatet, der skal bruges
1. Klik på **OK** 

Du får vist en meddelelse om, at din konfigurationsændring er i gang, hvilket kan tage et par minutter at gennemføre. Når ændringen konfigurationen er fuldført, skal du følge trinnene i afsnittet **til at logge på eksternt** senere i denne artikel.
    
## <a name="how-to-enable-remote-access-in-your-package"></a>Sådan aktiveres fjernadgang i pakken

1. Højreklik på projektet Azure fra ruden med Eklipses Projektstifinder, og klik på **Egenskaber**.

1. Udvid **Azure** i den venstre rude i dialogboksen **Egenskaber** , og klik på **Fjernadgang**.

1. I dialogboksen **Fjernadgang** , sikre, at **aktivere alle roller til at acceptere Remote Desktop forbindelser med disse logonoplysninger** er markeret.

1. Angive et brugernavn for forbindelsen til Fjernskrivebord.

1. Angiv og Bekræft adgangskode til brugeren. Brugernavn og en adgangskode for værdierne i denne dialogboks, der skal bruges, når du foretager en forbindelse til Fjernskrivebord. (Bemærk, at dette er en separat adgangskode fra adgangskoden PFX).

1. Angiv udløbsdatoen for brugerkontoen.

1. Klik på **Ny** for at oprette et nyt selvsigneret certifikat. (Du kan også du kan vælge et certifikat fra dit arbejdsområde eller filsystem gennem knapperne **arbejdsområde** eller **filsystemet** henholdsvis, men henblik på dette selvstudium skal vi oprette et nyt certifikat.)

    * Angiv og bekræft den adgangskode, du skal bruge til filen PFX i dialogboksen **Nyt certifikat** .

    * Acceptere den værdi, der er knyttet til **Navn (CN)**, eller brug et brugerdefineret navn.

    * Angiv stien og navnet hvor det nye certifikat i .cer formularen skal gemmes. Du kan bruge mappen **cert** Azure projektets til dette trin og næste trin, men du er meget Velkommen til at vælge en anden placering. Henblik på dette selvstudium bruger vi **c:\mycert\mycert.cer**. (Oprette mappen **c:\mycert** før du fortsætter, eller brug en eksisterende mappe, hvis det er nødvendigt).

    * Angiv stien og navnet hvor det nye certifikat og dens privat nøgle, i .pfx formularen skal gemmes. Henblik på dette selvstudium bruger vi **c:\mycert\mycert.pfx**. Dialogboksen **Nyt certifikat** skal ligne følgende (Opdater mappesti, hvis du ikke har brugt **c:\mycert**):

        ![][ic712275]

    * Klik på **OK** for at lukke dialogboksen **Nyt certifikat** .

1. **Fjernadgang til** dialogboksen skal se ud som følger:</p>

    ![][ic719495]

1. Klik på **OK** for at lukke dialogboksen **Fjernadgang** .
    
Genopbygge dit program med Opret angive til installation til skyen.

## <a name="to-log-in-remotely"></a>Til at logge på fra en fjernplacering

Når din rolle forekomst er klar, kan du få fjernadgang log på til den virtuelle maskine, der vært for dit program.

* Hvis bruger Eklipse på Windows, og du har valgt indstillingen **Start Fjernskrivebord på installere** under installationen til Azure, kan du der præsenteres med en forbindelse til Fjernskrivebord logonskærmen vises, når din installation starter. Når du bliver bedt om det brugernavn og adgangskode, Angiv de værdier, du har angivet for den eksterne bruger og vil kunne logge på.

* En anden måde at logge på eksternt er via <a href="http://go.microsoft.com/fwlink/?LinkID=512959">Azure Management portalen</a>:

    * I visningen **Cloud Services** for portalen Azure Management, skal du klikke på skybaseret tjeneste, skal du klikke på **forekomster**, skal du klikke på en bestemt forekomst og derefter klikke på knappen **Opret forbindelse** . Knappen **Opret forbindelse** vises som følgende i kommandolinjen:

        ![][ic659273]

    * Når du klikker på knappen **Opret forbindelse** , bliver du bedt om at åbne en RDP-fil. Åbn filen, og følg instruktionerne. (Du kan også gemme filen til din lokale computer, og kør derefter filen ved at dobbeltklikke på den til remote log på din virtuelle maskine uden at skulle først gå til af administrationsportalen.)

    * Når du bliver bedt om det brugernavn og adgangskode, Angiv de værdier, du har angivet for den eksterne bruger og vil kunne logge på.

> [AZURE.NOTE] Hvis du er på et Windows-operativsystem, skal du bruge en Fjernskrivebord-klient, der er kompatibelt med dit operativsystem, og følg trinnene til at konfigurere denne klient med indstillingerne i RDP-fil, du har hentet.

## <a name="see-also"></a>Se også

[Azure-værktøjskassen til Eklipse][]

[Oprette et Hej verden program til Azure i Eklipse][]

[Installation af Azure værktøjerne til Eklipse][] 

Du kan finde flere oplysninger om brug af Azure med Java, [Azure Java Developer Center][].

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure-værktøjskassen til Eklipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Oprette et Hej verden program til Azure i Eklipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installation af Azure værktøjerne til Eklipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic712275]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic712275.png
[ic719495]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719495.png
[ic719494]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719494.png
[ic659273]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic659273.png
