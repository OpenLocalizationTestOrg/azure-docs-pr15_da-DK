<properties
    pageTitle="Sådan bruger du Azure slaven plug-in'en med Hudson fortløbende Integration | Microsoft Azure"
    description="Beskriver, hvordan du bruger Azure slaven plug-in'en med Hudson fortløbende Integration."
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

# <a name="how-to-use-the-azure-slave-plug-in-with-hudson-continuous-integration"></a>Sådan bruger du Azure slaven plug-in'en med kontinuert Hudson-Integration

Azure slaven plug-in til Hudson gør det muligt at klargøre slave noder på Azure, når du kører fordelt opbygger.

## <a name="install-the-azure-slave-plug-in"></a>Installer Azure slaven plug-in'en

1. Klik på **Administrer Hudson**i dashboardet Hudson.

1. Klik på **Administrer plug-ins**på siden **Administrer Hudson** .

1. Klik på fanen **tilgængelig** .

1. Klik på **Søg** , og skriv **Azure** , hvis du vil begrænse listen på relevante plug-ins.

    Hvis du vælger for at rulle gennem listen over tilgængelige plug-ins, kan du finde den Azure slave plug-in'en under afsnittet **klyngeadministration og distribueret opbygge** i fanen **andre** .

1. Markér afkrydsningsfeltet for **Azure Slave plug-in'en**.

1. Klik på **Installer**.

1. Genstart Hudson.

Nu hvor plug-in'en er installeret, bør de næste trin til at konfigurere plug-in'en med din Azure abonnement profil og til at oprette en skabelon, der skal bruges i at oprette VM for noden slave.

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

            Id="<Subscription ID>"

            Name="Pay-As-You-Go"
            ManagementCertificate="<Management certificate value>" />

        </PublishProfile>

    </PublishData>

Når du har profilen abonnement, skal du følge disse trin for at konfigurere Azure slaven plug-in'en.

1. Klik på **Administrer Hudson**i dashboardet Hudson.

1. Klik på **Konfigurer System**.

1. Rul ned på siden til at finde sektionen **skyen** .

1. Klik på **Tilføj ny skyen > Microsoft Azure**.

    ![tilføje nye skyen][add new cloud]

    Dette viser felterne, der hvor du vil skrive dine abonnementsoplysninger.

    ![konfigurere profil][configure profile]

1. Kopiere abonnement-id og administration af certifikat fra dit abonnement profil, og sæt dem ind i de relevante felter.

    Når du kopierer abonnement-id og administration af certifikatet, medtage **du skal ikke** anførselstegn, der omgiver værdierne.

1. Klik på **Bekræft konfiguration**.

1. Klik på **Gem**, når konfigurationen er bekræftet korrekt.

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>Konfigurere et virtuelt skabelon til Azure Slave plug-in'en

Et virtuelt skabelon definerer de parametre, plug-in'en vil bruge til at oprette en slave node på Azure. I følgende trin skal vi oprette skabelon til en Ubuntu VM.

1. Klik på **Administrer Hudson**i dashboardet Hudson.

1. Klik på **Konfigurer System**.

1. Rul ned på siden til at finde sektionen **skyen** .

1. Find **Tilføje Azure virtuelt skabelon** , og klik på knappen **Tilføj** i sektionen **skyen** .

    ![tilføje vm skabelon][add vm template]

1. Angiv en skybaseret tjenestenavn i feltet **navn** . Hvis det navn, du angiver refererer til en eksisterende skybaseret tjeneste, klargøres VM i pågældende tjeneste. Ellers opretter Azure en ny.

1. Skriv tekst, der beskriver den skabelon, du opretter, i feltet **Beskrivelse** . Disse oplysninger bruges kun til dokumentation og bruges ikke i klargøring af en VM.

1. Angiv **linux**i feltet **navne** . Denne etiket bruges til at identificere den skabelon, du opretter og bruges efterfølgende refereres skabelonen, når du opretter et Hudson job til.

1. Vælg et område, hvor VM skal oprettes.

1. Vælg den relevante VM størrelse.

1. Angiv en lagerplads-konto, hvor VM skal oprettes. Sørg for, at det er i samme område som skytjenesten du skal bruge. Hvis du vil nye lagerplads, der skal oprettes, kan du lade feltet være tomt.

1. Opbevaring klokkeslæt angiver antallet minutter, før Hudson sletter en inaktiv slave. Lad dette standardværdi 60.

1. Vælg den relevante betingelse i **brugen**, når denne slave node der skal bruges. Nu skal du vælge **Utilize denne node som muligt**.

    På dette tidspunkt vil formularen ser nogenlunde nogenlunde sådan ud:

    ![skabelon config][template config]

1. Du har til at angive, hvilket systembillede skal være installeret på din VM i **billede familie eller -Id** . Du kan enten vælge på en liste over billede familier eller angive et brugerdefineret billede.

    Hvis du vil vælge på en liste over billede familier, Angiv det første tegn (store og små bogstaver) i efternavn billede. For eksempel kan at skrive **U** få vist en liste over Ubuntu Server familier. Når du vælger på listen, bruge Jenkins den seneste version af system billedet fra pågældende familie, når din VM.

    ![OS family liste][OS family list]

    Hvis du har et brugerdefineret billede, du vil bruge i stedet, Skriv navnet på den brugerdefinerede billede. Af brugerdefineret billednavne vises ikke på en liste, så du behøver at sikre, at navnet er indtastet korrekt.    

    Dette selvstudium skal du skrive **U** for at få vist en liste over Ubuntu billeder og vælg **Ubuntu Server 14.04 LTS**.

1. Vælg **SSH**for **Start metode**.

1. Kopiere scriptet nedenfor og indsætte i feltet **initialisering script** .

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

    **Initialisering script** vil blive udført, når VM er blevet oprettet. I dette eksempel installeres scriptet Java, ciffer og vant.

1. Angiv dine foretrukne værdier i felterne **brugernavn** og **din adgangskode** for administratorkontoen, der oprettes på din VM.

1. Klik på **Bekræft skabelon** for at kontrollere, om de specificerede er gyldig.

1. Klik på **Gem**.

## <a name="create-a-hudson-job-that-runs-on-a-slave-node-on-azure"></a>Oprette et Hudson job, der kører på en slave node på Azure

I dette afsnit, skal du oprette en Hudson opgave, som kører på en slave node på Azure.

1. Klik på **Nyt Job**i dashboardet Hudson.

1. Angiv et navn for den sag, du opretter.

1. Vælg **opbygge et gratis typografi software job**type job.

1. Klik på **OK**.

1. Vælg **begrænset, hvor du kan køre dette projekt**i konfigurationssiden job.

1. Vælg **menuen Node og etiket** , og vælg **linux** (vi angivet denne etiket, når du opretter skabelonen virtuelt i forrige afsnit).

1. Klik på **Tilføj opbygge trin** i afsnittet **oprette** og vælg **Execute shell**.

1. Redigere følgende script, erstatter **{github navnet på din konto}**, **{Projektnavn}**og **{adresselisten project}** med relevante værdier, og Indsæt det redigerede script i tekstområdet, der vises.

        # Clone from git repo

        currentDir="$PWD"

        if [ -e {your project directory} ]; then

            cd {your project directory}

            git pull origin master

        else

            git clone https://github.com/{your github account name}/{your project name}.git

        fi

        # change directory to project

        cd $currentDir/{your project directory}

        #Execute build task

        ant

1. Klik på **Gem**.

1. Find den sag, du lige har oprettet og klik på ikonet **tidsplan et build** i dashboardet Hudson.

Hudson derefter opretter en slave node ved hjælp af skabelonen oprettede i forrige afsnit og udføre script, du har angivet i trinnet build til denne opgave.

## <a name="next-steps"></a>Næste trin

Du kan finde flere oplysninger om brug af Azure med Java, [Azure Java Developer Center].

<!-- URL List -->

[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[abonnement-profil]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[add new cloud]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addcloud.png
[configure profile]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-configureprofile.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplate.png
[template config]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdata.png
[OS family list]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-oslist.png

