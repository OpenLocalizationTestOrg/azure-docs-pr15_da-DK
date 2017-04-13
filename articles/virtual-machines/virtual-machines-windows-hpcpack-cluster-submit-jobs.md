<properties
 pageTitle="Sende job til en HPC sprogpakke klynge i Azure | Microsoft Azure"
 description="Lær, hvordan du konfigurerer en lokale computer til at sende job til en HPC Pack klynge i Azure"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="10/14/2016"
 ms.author="danlep"/>

# <a name="submit-hpc-jobs-from-an-on-premises-computer-to-an-hpc-pack-cluster-deployed-in-azure"></a>Sende HPC job fra en lokal computer til en HPC Pack klynge, der er implementeret i Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Konfigurere en lokal klientcomputer for at sende job til en [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) klynge i Azure. I denne artikel beskrives, hvordan du konfigurerer en lokal computer med klientværktøjer til at sende jobbet over HTTPS til klynge i Azure. På denne måde kan flere klynge brugere sende job til en skybaseret HPC Pack klynge, men uden at tilslutte direkte til noden hoved VM eller få adgang til et Azure-abonnement.

![Sende et job til en klynge i Azure][jobsubmit]

## <a name="prerequisites"></a>Forudsætninger

* **HPC Pack hoved node implementeret i en Azure VM** - vi anbefaler, at du bruger automatiserede værktøjer som en [skabelon til Azure Hurtig start](https://azure.microsoft.com/documentation/templates/) eller en [Azure PowerShell-script](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) til at udrulle hoved node og klynge. Du har brug for DNS-navnet på noden hoved og legitimationsoplysningerne for en Klyngeadministrator for at fuldføre trinnene i denne artikel.

* **Klientcomputeren** – du har brug for en Windows eller Windows Server klientcomputer, som kan køre HPC Pack klient værktøjer (se [systemkrav](https://technet.microsoft.com/library/dn535781.aspx)). Hvis du kun vil bruge HPC Pack webportal eller REST-API til at sende job, kan du bruge en hvilken som helst klientcomputeren efter eget valg.

* **HPC Pack installationsmedier** - installere HPC Pack klient værktøjer, gratis installationspakken til den nyeste version af HPC Pack (HPC Pack 2012 R2) er tilgængelig fra [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=328024). Sørg for, at du henter den samme version af HPC Pack, der er installeret på noden hoved VM.

## <a name="step-1-install-and-configure-the-web-components-on-the-head-node"></a>Trin 1: Installere og konfigurere Microsoft web components på noden hoved

For at aktivere en RESTEN grænseflade til at sende job til klyngen over HTTPS skal du sikre, HPC Pack web components er konfigureret på noden HPC Pack hoved. Hvis de ikke allerede er installeret, skal du først installere web components ved at køre installationsfilen HpcWebComponents.msi. Derefter konfigurere komponenterne ved at køre scriptet HPC PowerShell **Sæt HPCWebComponents.ps1**.

Du finder detaljerede procedurer i [installere Microsoft HPC Pack Web Components](http://technet.microsoft.com/library/hh314627.aspx).

>[AZURE.TIP] Visse Azure Hurtig start skabeloner til HPC Pack Installer og Konfigurer web components automatisk. Hvis du bruger [scriptet til implementering af HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) til at oprette klyngen, kan du eventuelt installerer og konfigurerer web components som en del af installationen.

**Installere Microsoft web components**

1. Oprette forbindelse til noden hoved VM ved hjælp af en Klyngeadministrator af legitimationsoplysninger.

2. Fra mappen HPC installation skal du køre HpcWebComponents.msi på noden hoved.

3. Følg trinnene i guiden for at installere web components

**Konfigurere web components**

1. Start HPC PowerShell som administrator på noden hoved.

2. For at ændre directory til placeringen af scriptet til konfiguration, skal du skrive følgende kommando:

    ```powershell
    cd $env:CCP_HOME\bin
    ```
3. For at konfigurere grænsefladen RESTEN og starte tjenesten HPC Web skal du skrive følgende kommando:

    ```powershell
    .\Set-HPCWebComponents.ps1 –Service REST –enable
    ```

4. Når du bliver bedt om at vælge et certifikat, Vælg det certifikat, der svarer til det offentlige DNS-navn for noden hoved. Eksempelvis hvis du installerer noden hoved VM ved hjælp af den klassiske implementeringsmodel, navnet på certifikatet ligner CN =&lt;*HeadNodeDnsName*&gt;. cloudapp.net. Hvis du bruger implementeringsmodel Ressourcestyring, navnet på certifikatet ligner CN =&lt;*HeadNodeDnsName*&gt;. &lt; *område*&gt;. cloudapp.azure.com.

    >[AZURE.NOTE] Du vælger dette certifikat senere, når du sender job til noden hoved fra en lokal computer. Ikke markere eller konfigurere et certifikat, der svarer til navnet på den hovedsæde node i Active Directory-domæne (for eksempel CN =*MyHPCHeadNode.HpcAzure.local*).

5. For at konfigurere webportalen til jobbet skal du skrive følgende kommando:

    ```powershell
    .\Set-HPCWebComponents.ps1 –Service Portal -enable
    ```
6. Når scriptet er fuldført, stop og genstart tjenesten HPC Job Scheduler ved at skrive følgende kommandoer:

    ```powershell
    net stop hpcscheduler
    net start hpcscheduler
    ```

## <a name="step-2-install-the-hpc-pack-client-utilities-on-an-on-premises-computer"></a>Trin 2: Installer HPC Pack klient programmerne på en lokal computer

Hvis du vil installere af HPC Pack klient-funktionerne på din computer, skal du hente installationsfiler HPC Pack (fuld installation) fra [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=328024). Når du går i gang med installationen, kan du vælge indstillingen konfiguration til **HPC Pack klient værktøjer**.

Hvis du vil bruge klientværktøjer HPC Pack til at sende job til noden hoved VM, skal du også eksportere et certifikat fra noden hoved og installere den på klientcomputeren. Certifikatet, der skal være i. Virksomheden format.

**Sådan eksporteres certifikatet fra noden hoved**

1. Tilføj snap-in-programmet Certifikater til en Microsoft Management Console for den lokale computerkonto på noden hoved. Få en vejledning til at tilføje snap-in i [tilføjer Snap-in certifikater til MMC](https://technet.microsoft.com/library/cc754431.aspx).

2. Udvid **certifikater – lokalcomputer**i konsoltræet > **personlige**, og klik derefter på **certifikater**.

3. Find det certifikat, du har konfigureret til HPC Pack web components i [trin 1: installere og konfigurere Microsoft web components på noden hoved](#step-1:-install-and-configure-the-web-components-on-the-head-node) (for eksempel CN =&lt;*HeadNodeDnsName*&gt;. cloudapp.net).

4. Højreklik på certifikatet, og klik på **Alle opgaver** > **eksportere**.

5. Klik på **Næste**i guiden eksport, og Sørg for, at **Nej, Eksporter ikke den private nøgle** er valgt.

6. Følg de tilbageværende trin i guiden for at eksportere certifikat i DER-kodet binære X.509 (. Virksomheden) format.


**Importere certifikatet på klientcomputeren**


1. Kopiere det certifikat, du har eksporteret fra noden hoved til en mappe på klientcomputeren.

2. Kør certmgr.msc på klientcomputeren.

3. I Certifikatstyring Udvid **Certifikater – aktuel bruger** > **Der er tillid til rodnøglecentre**, højreklik på **certifikater**, og klik derefter på **Alle opgaver** > **Import**.

4. Klik på **Næste** i guiden Import og følge trinnene for at importere det certifikat, du har eksporteret fra noden hoved til rodnøglecentre store.



>[AZURE.TIP] Du kan se en sikkerhed advarsel om, fordi nøglecenter på noden hoved ikke kan genkendes af klientcomputeren. Til testformål, kan du ignorere denne advarsel og fuldføre importen certifikat.

## <a name="step-3-run-test-jobs-on-the-cluster"></a>Trin 3: Kør testjob på klyngen

For at bekræfte din konfiguration, kan du prøve at køre job på klynge i Azure fra den lokale computer. Du kan for eksempel bruge HPC Pack grafiske værktøjer eller kommandolinjen kommandoer til at sende job til klyngen. Du kan også bruge en webbaseret portal til at sende job.


**Køre job bidrag kommandoer på klientcomputeren**


1. På en klientcomputer, hvor HPC Pack klientfunktioner er installeret, kan du starte en kommandoprompt.

2. Skriv en eksempel-kommando. For eksempel for at få vist alle job på klyngen, Skriv en kommando, der ligner en af følgende, afhængigt af det fulde DNS-navn på noden hoved:

    ```command
    job list /scheduler:https://<HeadNodeDnsName>.cloudapp.net /all
    ```
    
    eller
    
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.<region>.cloudapp.azure.com /all
    ```

    >[AZURE.TIP] Brug det fulde DNS-navn på noden hoved, ikke IP-adressen, Opgavestyring URL-adressen. Hvis du angiver IP-adressen, vises en fejl svarer til "på servercertifikat skal bruge til enten har en gyldig kæde af sikkerhed og rettighedsadministration eller skal placeres i store der er tillid til."

3. Når du bliver bedt om det, Skriv brugernavnet (i formularen &lt;DomainName&gt;\\&lt;brugernavn&gt;) og adgangskoden til HPC klyngeadministratoren eller en anden klynge bruger, du har konfigureret. Du kan vælge at gemme legitimationsoplysninger lokalt for flere job handlinger.

    Der vises en liste over job.


**Du kan bruge HPC Job Manager på klientcomputeren**

1. Hvis du ikke tidligere gemmer legitimationsoplysninger til domænet for en bruger, klynge, når du sender et job, kan du tilføje legitimationsoplysningerne i Legitimationsstyring.

    en. Start Legitimationsstyring i Kontrolpanel på klientcomputeren.

    b. Klik på **Windows-legitimationsoplysninger** > **Tilføj en generisk legitimationsoplysninger**.

    c. Angiv URL-adressen (for eksempel https://&lt;HeadNodeDnsName&gt;.cloudapp.net/HpcScheduler eller https://&lt;HeadNodeDnsName&gt;.&lt; område&gt;.cloudapp.azure.com/HpcScheduler), og brugernavnet (&lt;DomainName&gt;\\&lt;brugernavn&gt;) og adgangskoden til klyngeadministratoren eller en anden klynge bruger, du har konfigureret.

2. Start HPC Job Manager på klientcomputeren.

3. Skriv URL-adressen til noden hoved i Azure i dialogboksen **Vælg hoved Node** (for eksempel https://&lt;HeadNodeDnsName&gt;. cloudapp.net eller https://&lt;HeadNodeDnsName&gt;.&lt; område&gt;. cloudapp.azure.com).

    HPC Job Manager åbnes og viser en liste over job på noden hoved.

**Bruge webportalen, der kører på den hovedsæde node**

1. Start en webbrowser på klientcomputeren, og skriv et af følgende adresser, afhængigt af det fulde DNS-navn på noden hoved:

    ```
    https://<HeadNodeDnsName>.cloudapp.net/HpcPortal
    ```
    
    eller
    
    ```
    https://<HeadNodeDnsName>.<region>.cloudapp.azure.com/HpcPortal
    ```
2. Skriv legitimationsoplysningerne til domænet HPC klynge administrator i sikkerhedsdialogboksen. (Du kan også tilføje andre klynge brugere i forskellige roller. Se [Administration af klynge-brugere](https://technet.microsoft.com/library/ff919335.aspx)).

    Webportalen åbnes til jobbet listevisningen.

3. Hvis du vil sende et eksempel job, der returnerer strengen "Hej verden" fra klyngen, skal du klikke på **nyt job** i navigationslinjen til venstre.

4. Klik på **HelloWorld**under **fra bidrag sider**, på siden **Nyt Job** . Siden job bidrag vises.

5. Klik på **Send**. Hvis du bliver bedt om det, kan du give domæne legitimationsoplysningerne for HPC klyngeadministratoren. Jobbet sendes, og job-ID vises på siden **Mine opgaver** .

6. For at få vist resultaterne på den sag, du har sendt, skal du klikke på job-ID, og derefter klikke på **Få vist opgaver** for at få vist kommandooutput (under **Output**).

## <a name="next-steps"></a>Næste trin

* Du kan også sende job til Azure klynge med [HPC Pack REST-API](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx).

* Hvis du vil sende klynge job fra en Linux-klient, skal du se eksemplet Python i [HPC Pack 2012 R2 SDK og eksempler på kode](https://www.microsoft.com/download/details.aspx?id=41633).


<!--Image references-->
[jobsubmit]: ./media/virtual-machines-windows-hpcpack-cluster-submit-jobs/jobsubmit.png
