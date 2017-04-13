<properties
    pageTitle="Sådan bruger du Azure slaven plug-in'en med Jenkins fortløbende Integration | Microsoft Azure"
    description="Beskriver, hvordan du bruger Azure slaven plug-in'en med Jenkins fortløbende Integration."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor="" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robmcm"/>

# <a name="how-to-use-the-azure-slave-plug-in-with-jenkins-continuous-integration"></a>Sådan bruger du Azure slaven plug-in'en med kontinuert Jenkins-Integration

Du kan bruge Azure slaven plug-in'en til Jenkins til klargøring slave noder på Azure, når du kører fordelt opbygger.

## <a name="install-the-azure-slave-plug-in"></a>Installere plug-in'en Azure slaven

1. Klik på **Administrer Jenkins**i dashboardet Jenkins.

1. Klik på **Administrer plug-ins**på siden **Administrer Jenkins** .

1. Klik på fanen **tilgængelig** .

1. Skriv **Azure** , hvis du vil begrænse listen på relevante plug-ins, i filterfeltet over listen med tilgængelige plug-ins.

    Hvis du vælger for at rulle gennem listen over tilgængelige plug-ins, kan du finde den Azure slave plug-in'en under afsnittet **klyngeadministration og distribueret opbygge** .

1. Markér afkrydsningsfeltet **Azure Slave plug-in'en** .

1. Klik på **Installer uden genstart** eller **Hent nu og installere efter genstart**.

Nu, hvor plug-in'en er installeret, er næste trin til at konfigurere plug-in'en med din Azure abonnement profil og til at oprette en skabelon, der skal bruges i at oprette den virtuelle maskine til noden slave.


## <a name="configure-the-azure-slave-plug-in-with-your-subscription-profile"></a>Konfigurere Azure slaven plug-in'en med dit abonnement-profil

Et abonnement-profil, der også kaldes publicere indstillinger er en XML-fil, der indeholder sikker legitimationsoplysninger og nogle flere oplysninger, skal du arbejde med Azure i dit udviklingsmiljø. Hvis du vil konfigurere Azure slaven plug-in'en, skal du:

* Dit abonnement-id
* Et management certifikat til dit abonnement

Disse kan findes i dit [abonnement profil]. Nedenfor vises et eksempel på et abonnement profil.

    <?xml version="1.0" encoding="utf-8"?>

        <PublishData>

        <PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

        <Subscription

            ServiceManagementUrl="https://management.core.windows.net"

            Id="<Subscription ID value>"

            Name="Pay-As-You-Go"
            ManagementCertificate="<Management certificate value>" />

        </PublishProfile>

    </PublishData>

Når du har dit abonnement profil, skal du følge disse trin for at konfigurere Azure slaven plug-in'en:

1. Klik på **Administrer Jenkins**i dashboardet Jenkins.

1. Klik på **Konfigurer System**.

1. Rul ned på siden til at finde sektionen **skyen** .

1. Klik på **Tilføj ny skyen > Microsoft Azure**.

    ![skyen sektion][cloud section]

    Dette viser felterne, der hvor du vil skrive dine abonnementsoplysninger.

    ![konfiguration af abonnement][subscription configuration]

1. Kopiér abonnement-id og administration af certifikat værdier fra dit abonnement profil og sæt dem ind i de relevante felter.

    Når du kopierer abonnement-id og administration af certifikatet, du skal ikke medtage de tilbud, der omgiver værdierne.

1. Klik på **Bekræft konfiguration**.

1. Når konfigurationen er bekræftet til at være korrekt, kan du klikke på **Gem**.

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>Konfigurere et virtuelt skabelon til Azure slave plug-in'en

Et virtuelt skabelon definerer de parametre, plug-in'en vil bruge til at oprette en slave node på Azure. I de følgende trin, skal vi oprette en skabelon til en Ubuntu virtual machine.

1. Klik på **Administrer Jenkins**i dashboardet Jenkins.

1. Klik på **Konfigurer System**.

1. Rul ned på siden til at finde sektionen **skyen** .

1. Find **Tilføj Azure virtuelt skabelon**i sektionen **skyen** , og klik derefter på **Tilføj**.

    ![tilføje vm skabelon][add vm template]

    Dette viser de felter, hvor du angiver oplysninger om den skabelon, du opretter.

    ![tom generelle konfiguration][blank general configuration]

1. Angiv en Azure skyen service navn i feltet **navn** . Hvis det indtastede navn refererer til en eksisterende skybaseret tjeneste, klargøres den virtuelle maskine i pågældende tjeneste. Ellers opretter Azure en ny.

1. Skriv tekst, der beskriver den skabelon, du opretter, i feltet **Beskrivelse** . Dette er kun til posterne og bruges ikke i klargøring af en virtuel maskine.

1. **Feltet** bruges til at identificere den skabelon, du opretter og bruges efterfølgende refereres skabelonen, når du opretter et Jenkins job til. Indtast **linux** i dette felt til vores formål.

1. Klik på det område, hvor den virtuelle maskine skal oprettes i listen **område** .

1. Klik på den relevante størrelse på listen **Virtuelt størrelse** .

1. Angiv en lagerplads-konto, hvor den virtuelle maskine skal oprettes i boksen **Kontonavn-lager** . Sørg for, at det er i samme område som skytjenesten du skal bruge. Hvis du vil nye lagerplads, der skal oprettes, kan du lade dette felt være tomt.

1. Opbevaring klokkeslæt angiver antallet minutter, før Jenkins sletter en inaktiv slave. Lad dette standardværdi 60. Du kan også vælge at lukke slave i stedet for at slette den, når den er inaktiv. For at gøre, skal du markere afkrydsningsfeltet **Lukning kun (Slet ikke) efter opbevaring gang** .

1. Klik på den relevante betingelse på listen **brugen** når skal anvendes denne slave node. Nu skal du klikke på **Utilize denne node som muligt**.

    På dette tidspunkt bør formularen se nogenlunde nogenlunde sådan ud:

    ![kontrolpunkt generelle skabelon config][checkpoint general template config]

    Næste trin er at tilvejebringe oplysninger om operativsystem billedet, du vil din slave skal oprettes i.

1. I feltet **billede familie eller -Id** , du har til at angive, hvilket systembillede skal være installeret på din virtuelle maskine. Du kan enten vælge på en liste over billede familier eller angive et brugerdefineret billede.

    Hvis du vil vælge på en liste over billede familier, Angiv det første tegn (store og små bogstaver) i efternavn billede. For eksempel kan at skrive **U** få vist en liste over Ubuntu Server familier. Når du har valgt på listen, bruge Jenkins den seneste version af system billedet fra pågældende familie, når din virtuelle maskine.

    ![Billede af OS listen eksempel][OS Image list sample]

    Hvis du har et brugerdefineret billede, du vil bruge i stedet, Skriv navnet på den brugerdefinerede billede. Af brugerdefineret billednavne vises ikke på en liste, så du behøver at sikre, at navnet er indtastet korrekt.

    Skriv **U** at få vist en liste over Ubuntu billeder til dette selvstudium, og klik derefter på **Ubuntu Server 14.04 LTS**.

1. Klik på **SSH**på listen **Start metode** .

1. Kopiér scriptet nedenfor og Indsæt det i feltet **Initialisering Script** .

        # Install Java

        sudo apt-get -y update

        sudo apt-get install -y openjdk-7-jdk

        sudo apt-get -y update --fix-missing

        sudo apt-get install -y openjdk-7-jdk

        # Install git

        sudo apt-get install -y git

        #Install ant

        sudo apt-get install -y ant

        sudo apt-get -y update --fix-missing

        sudo apt-get install -y ant

    Initialisering scriptet vil blive udført, når den virtuelle maskine er blevet oprettet. I dette eksempel installeres scriptet Java, ciffer og vant.

1. Angiv dine foretrukne værdier i felterne **brugernavn** og **din adgangskode** for administratorkontoen, der oprettes på din virtuelle maskine.

1. Klik på **Bekræft skabelon** for at kontrollere, om de specificerede er gyldige.

1. Klik på **Gem**.


## <a name="create-a-jenkins-job-that-runs-on-a-slave-node-on-azure"></a>Oprette et Jenkins job, der kører på en slave node på Azure

I dette afsnit, skal du oprette en Jenkins opgave, som kører på en slave node på Azure. Du skal have dit eget projekt på GitHub kan følge med i.

1. Klik på **Nyt element**i dashboardet Jenkins.

1. Angiv et navn for den opgave, du opretter.

1. Klik på **Freestyle projekt**for projekttypen.

1. Klik på **Ok**.

1. Vælg **begrænset, hvor du kan køre dette projekt**i konfigurationssiden opgave.

1. Angiv **linux**i feltet **Navn udtryk** . I det forrige afsnit oprettede vi en slave skabelon, som vi med navnet **linux**, som er, hvad angiver vi her.

1. Klik på **Tilføj opbygge trin** i afsnittet **oprette** og vælg **Execute shell**.

1. Redigere følgende script, erstatte **(GitHub navnet på din konto)**, **(dit projektnavn)**og **(project-bibliotek)** med de relevante værdier, og Indsæt det redigerede script i tekstområdet, der vises.

        # Clone from git repo

        currentDir="$PWD"

        if [ -e (your project directory) ]; then

            cd (your project directory)

            git pull origin master

        else

            git clone https://github.com/(your GitHub account name)/(your project name).git

        fi

        # change directory to project

        cd $currentDir/(your project directory)

        #Execute build task

        ant

1. Klik på **Gem**.

1. Hold markøren over den opgave, du lige har oprettet dashboardet Jenkins, og klik på pil ned for at få vist Opgaveindstillinger.

1. Klik på **Generer nu**.

Jenkins derefter opretter en slave node ved hjælp af skabelonen oprettede i forrige afsnit og udføre script, du har angivet i trinnet build til denne opgave.

## <a name="next-steps"></a>Næste trin

Du kan finde flere oplysninger om brug af Azure med Java, [Azure Java Developer Center].

<!-- URL List -->

[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[abonnement-profil]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[cloud section]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-cloud-section.png
[subscription configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-account-configuration-fields.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-add-vm-template.png
[blank general configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration-blank.png
[checkpoint general template config]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration.png
[OS Image list sample]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-os-family-list-sample.png