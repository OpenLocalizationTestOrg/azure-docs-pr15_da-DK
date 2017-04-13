<properties
    pageTitle="Konfigurere en virtuel maskine, som en server til IPython notesbog | Microsoft Azure"
    description="Angive op en Azure Virtual Machine til brug i et data videnskabelige miljø med IPython serveren for avanceret analyse."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="xibingao;bradsev" />

# <a name="set-up-an-azure-virtual-machine-as-an-ipython-notebook-server-for-advanced-analytics"></a>Konfigurere en Azure virtuelt som en server til IPython notesbog til avanceret analyse

Dette emne viser, hvordan du klargør og konfigurere en Azure virtuel maskine til avanceret analyse, der kan bruges som en del af et data videnskabelige miljø. Virtuelt Windows er konfigureret med understøtter funktioner som som IPython notesbog, Azure Storage Explorer, AzCopy samt andre funktioner, der er nyttige til avanceret analyse projekter. Azure-lager Explorer og AzCopy, for eksempel indeholder praktiske funktioner til at overføre data til Azure blob-lager fra din lokale computer eller for at hente den til din lokale computer fra blob-lager.

## <a name="create-vm"></a>Trin 1: Oprette et generelt formål Azure virtuelt

Hvis du allerede har en Azure virtuelt og blot ønsker at konfigurere en IPython notesbog server på den, kan du springe dette trin over og gå videre til [trin 2: føje et slutpunkt for IPython notesbøger til en eksisterende virtuelt](#add-endpoint).

Før du starter processen med at oprette en virtuel maskine på Azure, skal du bestemme størrelsen på den computer, der skal bruges til at behandle dataene til deres projekt. Mindre maskiner har mindre hukommelse og færre CPU-kerner end større maskiner, men de er også billigere. Finde en liste over maskine filtyper og priser, på siden <a href="http://azure.microsoft.com/pricing/details/virtual-machines/" target="_blank">Virtuelle maskiner priser</a>

1. Log på <a href="https://manage.windowsazure.com" target="_blank">Klassisk Azure</a>-portalen, og klik på **Ny** i det nederste venstre hjørne. Et vindue kommer frem. Vælg **beregne** -> **VIRTUELT** -> **Fra GALLERIET**.

    ![Opret arbejdsområde][24]

2. Vælg en af følgende billeder:

    * Windows Server 2012 R2 Datacenter
    * Windows Server Essentials-oplevelsen (Windows Server 2012 R2)

    Klik på pilen, der peger mod højre nederst til højre til at gå til konfigurationssiden af næste.

    ![Opret arbejdsområde][25]

3. Angiv et navn til den virtuelle maskine, du vil oprette, skal du vælge størrelsen på maskinen (standard: A3) baseret på størrelse af dataene maskinen skal behandles, og hvordan effektive ønskede maskinen (hukommelsesstørrelse og antallet af Beregn kerner), skal du angive et brugernavn og adgangskode til computeren. Klik på pilen, der peger mod højre for at gå til konfigurationssiden af næste.

    ![Opret arbejdsområde][26]

4. Markere det **Område/forbindelse gruppe/VIRTUELLE netværk** , der indeholder den **LAGERPLADS konto** , du planlægger at bruge til denne virtuelt, og vælg derefter kontoen, lagerplads. Tilføje et slutpunkt nederst i feltet **SLUTPUNKTER** ved at skrive navnet på slutpunktet ("IPython" her). Du kan vælge en hvilken som helst streng som **navnet** på slutpunktet og et heltal mellem 0 og 65536, der er **tilgængelige** som **Offentlige PORT**. **Privat PORT** skal være **9999**. Brugere skal **undgå** at bruge offentlige porte, der allerede er tildelt til internet services. <a href="http://www.chebucto.ns.ca/~rakerman/port-table.html" target="_blank">Portene efter Internet Services</a> indeholder en liste over porte, der har fået tildelt og undgås.

    ![Opret arbejdsområde][27]

5. Klik på markeringen i afkrydsningsfeltet for at starte den virtuelle maskine klargøring af processen.

    ![Opret arbejdsområde][28]


Det kan tage 15-25 minutter at gennemføre den virtuelle maskine klargøring af processen. Når den virtuelle maskine er blevet oprettet, bør status for denne computer vises som **kører**.

![Opret arbejdsområde][29]

## <a name="add-endpoint"></a>Trin 2: Føje et slutpunkt for IPython notesbøger til en eksisterende virtuelt

Hvis du har oprettet den virtuelle maskine ved at følge instruktionerne i trin 1, slutpunkt for IPython notesbog er blevet tilføjet, og dette trin kan blive ignoreret.

Hvis den virtuelle maskine allerede findes, og du vil tilføje et slutpunkt for IPython notesbog, du vil installere i trin 3 under første log klassisk Azure-portalen, Vælg den virtuelle maskine, og Tilføj slutpunkt for IPython notesbog server. I følgende figur vises indeholder et skærmbillede af portalen, når slutpunktet for IPython notesbog er blevet føjet til en Windows virtuel maskine.

![Opret arbejdsområde][17]

## <a name="run-commands"></a>Trin 3: Installer IPython notesbog og andre supplerende værktøjer

Når den virtuelle maskine er oprettet, skal du bruge RDP Remote Desktop Protocol () til at logge på virtual machine til Windows. Finde en vejledning, se, [hvordan du logger på virtuelt kører Windows Server](../virtual-machines/virtual-machines-windows-classic-connect-logon.md). Åbn **kommandoprompten** (**ikke Powershell-kommandovindue**) som en **Administrator** og køre følgende kommando.

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Når installationen er fuldført, IPython notesbog serveren startes automatisk i den *C:\\brugere\\\<brugernavn\>\\dokumenter\\IPython notesbøger* directory.

Når du bliver bedt om det, kan du angive en adgangskode til IPython notesbogen og adgangskoden til den maskine administrator. Dette gør det muligt for den IPython notesbog for at køre som en tjeneste på computeren.

## <a name="access"></a>Trin 4: Åbne IPython notesbøger fra en webbrowser
Åbn et websted for at få adgang til IPython notesbog-serveren, browseren og input *https://&#60;virtual DNS computernavn >: & #60; offentlige portnummeret >* i tekstfeltet URL-adresse. Her, den *& #60; offentlige portnummeret >* skal være det portnummer, du angav, da IPython notesbog slutpunktet blev føjet.

Den *& #60; virtuelt DNS-navn >* kan findes på den klassiske Portal Azure. Når logføring i på klassisk portalen, klik på **VIRTUELLE maskiner**, Vælg den computer, du har oprettet, og vælg derefter **DASHBOARD**, DNS-navnet vises på følgende måde:

![Opret arbejdsområde][19]

Du kan støde på en advarsel angiver, _der er problemer med dette websteds sikkerhedscertifikat_ (Internet Explorer), eller _forbindelsen er ikke privat_ (Chrome), som vist i følgende tallene. Klik på **Fortsæt for at dette websted (anbefales ikke)** (Internet Explorer) eller **Avanceret** og derefter * *Fortsæt til & #60;* DNS-navn*> (usikre) ** (Chrome) for at fortsætte. Skriv derefter den adgangskode, du tidligere har angivet for at få adgang til notesbøgerne IPython.

**Internet Explorer:**
![Opret arbejdsområde][20]

**Chrome:**
![Opret arbejdsområde][21]

Når du logger den IPython notesbog, en mappe *DataScienceSamples* vises i browseren. Denne mappe indeholder eksempel IPython notesbøger, der deles af Microsoft kan hjælpe brugerne med forestå data videnskabelige opgaver. Følgende eksempel IPython notesbøger er tjekket ud fra [**Github lager**](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) til virtuelle maskiner under IPython notesbog serveren konfigurere proces. Microsoft vedligeholder og opdaterer denne lager ofte. Brugere kan besøge Github lager for at få de senest opdateret eksempel IPython notesbøger.
![Opret arbejdsområde][18]

## <a name="upload"></a>Trin 5: Overføre en eksisterende notesbog på IPython fra en lokal computer til serveren IPython notesbog

IPython notesbøger er en nem måde for brugerne at overføre en eksisterende notesbog IPython på deres lokale computere til IPython notesbog server på virtuelle maskiner. Når brugere logger på IPython notesbogen i en webbrowser, klik på den **mappe** , der IPython notesbogen vil blive overført til. Vælg derefter en IPython notesbog .ipynb fil for at overføre fra den lokale computer i **Stifinder**, og træk og slip den til mappen IPython notesbog i webbrowseren. Klik på knappen **Overfør** for at overføre filen .ipynb til serveren IPython notesbog. Andre brugere kan derefter begynde at bruge det i fra deres webbrowser.

![Opret arbejdsområde][22]

![Opret arbejdsområde][23]


##<a name="shutdown"></a>Lukning og deaktivere fordele virtuelt når den ikke er i brug

Azure virtuelle computere er prisen som **betale for kun at du bruger**. For at sikre, at du ikke er blive faktureret når du ikke bruger din virtuelle maskine, skal den være i tilstanden **stoppet (Deallocated)** når du ikke er i brug.

> [AZURE.NOTE] Hvis du lukker den virtuelle maskine fra i VM (ved hjælp af indstillinger for Windows power), hvor VM stoppes men forbliver tildelte. For at sikre, at du ikke fortsat blive faktureret, altid stop virtuelle maskiner fra [Azure klassisk Portal](http://manage.windowsazure.com/). Du kan også stoppe VM via Powershell ved at ringe **ShutdownRoleOperation** med "PostShutdownAction" lig med "StoppedDeallocated".

At lukke og Dealloker den virtuelle maskine:

1. Log på [Azure klassisk Portal](http://manage.windowsazure.com/) ved hjælp af din konto.  

2. Vælg **VIRTUELLE maskiner** venstre navigationslinje.

3. Klik på navnet på din virtuelle maskine på listen over virtuelle maskiner, og gå til siden **DASHBOARD** .

4. Klik på **Luk computeren**nederst på siden.

![VM lukning][15]

Den virtuelle maskine bliver deallokeres, men ikke slettet. Du kan genstarte computeren virtuelle når som helst fra portalen Azure klassisk.

## <a name="your-azure-vm-is-ready-to-use-whats-next"></a>Din Azure VM er klar til brug: Hvad skal der ske nu?

Din virtuelle maskine er nu klar til brug i dine data videnskabelige øvelser. Den virtuelle maskine er også klar til brug som en server til IPython notesbog til udforske og behandling af data og andre opgaver sammen med Azure maskine læring og Team Data videnskabelige processen.

De næste trin i processen Team Data videnskabelige er tilknyttet i [Læringssti](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) og kan indeholde trin, flytte data til HDInsight, proces, og lyt det der forberedelse til learning fra dataene med Azure maskine læ.


[15]: ./media/machine-learning-data-science-setup-virtual-machine/vmshutdown.png
[17]: ./media/machine-learning-data-science-setup-virtual-machine/add-endpoints-after-creation.png
[18]: ./media/machine-learning-data-science-setup-virtual-machine/sample-ipnbs.png
[19]: ./media/machine-learning-data-science-setup-virtual-machine/dns-name-and-host-name.png
[20]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning-ie.png
[21]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning.png
[22]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-1.png
[23]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-2.png
[24]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-1.png
[25]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-2.png
[26]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-3.png
[27]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-4.png
[28]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-5.png
[29]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-6.png
