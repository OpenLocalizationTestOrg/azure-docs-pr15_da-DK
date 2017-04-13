<properties
   pageTitle="Installere en 3-node Deis klynge | Microsoft Azure"
   description="I denne artikel beskrives, hvordan du opretter en 3-node Deis klynge på Azure ved hjælp af en skabelon til Azure ressourcestyring"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="HaishiBai"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="06/24/2015"
   ms.author="hbai"/>

# <a name="deploy-a-3-node-deis-cluster"></a>Installere en 3-node Deis klynge

I denne artikel vejledes du gennem klargøring af en [Deis](http://deis.io/) klynge på Azure. Det dækker alle trin i at oprette de nødvendige certifikater til installation og skalering programmet **Gå** et eksempel på den nyligt klargjort klynge.

I følgende diagram vises arkitekturen i det installerede system. En systemadministrator administrerer klynge ved hjælp af Deis funktioner som **deis** og **deisctl**. Forbindelser er oprettet via en Azure justering af belastning, der videresender forbindelserne til en af medlemmet noder på klyngen. Adgang til det klienter installeres programmer via justering af belastning samt. I dette tilfælde justering af belastning videresender trafik til en Deis router maske, som yderligere routs trafik til den tilsvarende Docker beholdere hostes på klyngen.

  ![Arkitektur diagram over udløst Desis klynge](media/virtual-machines-linux-deis-cluster/architecture-overview.png)

For at køre gennem følgende trin, skal du:

 * Et aktivt Azure abonnement. Hvis du ikke har en, kan du få en gratis prøveversion på [azure.com](https://azure.microsoft.com/).
 * En arbejds- eller skolekonto id for at kunne bruge Azure ressourcegrupper. Hvis du har en personlig konto og log på med en Microsoft-id, skal du [oprette en arbejds-id'et fra din personlig skabelon](virtual-machines-windows-create-aad-work-id.md).
 * Enten – afhængigt af din klientoperativsystem – [Azure PowerShell](../powershell-install-configure.md) eller [Azure CLI til Mac, Linux, og Windows](../xplat-cli-install.md).
 * [OpenSSL](https://www.openssl.org/). OpenSSL bruges til at oprette de nødvendige certifikater.
 * En ciffer klient som [Ciffer fest](https://git-scm.com/).
 * Hvis du vil afprøve eksempelprogrammet, skal du også kontakte en DNS-server. Du kan bruge enhver DNS-servere eller tjenester, der understøtter jokertegn A poster.
 * En computer til at køre Deis klientværktøjer. Du kan bruge enten en lokal computer eller en virtuel maskine. Du kan køre disse værktøjer på næsten alle Linux fordeling, men følgende instruktioner bruge Ubuntu.

## <a name="provision-the-cluster"></a>Klargøring af klynge

I dette afsnit, skal du bruge en [Azure ressourcestyring](../azure-resource-manager/resource-group-overview.md) skabelon fra Åbn kilde lager [azure-Hurtig start-skabeloner](https://github.com/Azure/azure-quickstart-templates). Først skal du kopiere ned skabelonen. Derefter skal oprette du et nyt SSH vigtige par til godkendelse. Og derefter skal du konfigurere et nyt id for du klynge. Og til sidst skal du skal bruge enten Shell script eller PowerShell-scriptet til at klargøre klyngen.

1. Klone lageret: [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).

        git clone https://github.com/Azure/azure-quickstart-templates

2. Gå til mappen skabelon:

        cd azure-quickstart-templates\deis-cluster-coreos

3. Oprette en ny nøgle SSH-par ved hjælp af ssh keygen:

        ssh-keygen -t rsa -b 4096 -c "[your_email@domain.com]"

4. Oprette et certifikat ved hjælp af ovenstående privat nøgle:

        openssl req -x509 -days 365 -new -key [your private key file] -out [cert file to be generated]

5. Gå til [https://discovery.etcd.io/new](https://discovery.etcd.io/new) til at oprette et nyt klynge-id, som ser noget i stil:

        https://discovery.etcd.io/6a28e078895c5ec737174db2419bb2f3
<br />
Hver CoreOS klynge skal have et entydigt token fra denne gratis tjeneste. Se [CoreOS dokumentation](https://coreos.com/docs/cluster-management/setup/cluster-discovery/) for at få flere oplysninger.

6. Redigere filen **skyen config.yaml** for at erstatte den eksisterende **registrering** token med det nye token:

        #cloud-config
        ---
        coreos:
          etcd:
            # generate a new token for each unique cluster from https://discovery.etcd.io/new
            # uncomment the following line and replace it with your discovery URL
            discovery: https://discovery.etcd.io/3973057f670770a7628f917d58c2208a
        ...

7. Redigere filen **azuredeploy parameters.json** : åbne det certifikat, du oprettede i trin 4 i et tekstredigeringsprogram. Kopiere al tekst mellem `----BEGIN CERTIFICATE-----` og `-----END CERTIFICATE-----` til parameteren **sshKeyData** (skal du fjerne alle ny linje-tegn).

8. Ændre parameteren **newStorageAccountName** . Dette er lagerplads konto om en VM OS disk. Dette kontonavn skal være globalt entydige.

9. Ændre parameteren **publicDomainName** . Det bliver en del af DNS-navn, der er knyttet til den offentlige IP Indlæs belastningsjusteringstjenesten. Det endelige fulde har formatet af _[værdien af denne parameter]_. _[region]_. cloudapp.azure.com. Eksempelvis hvis du angiver navnet som deishbai32, og ressourcegruppen er installeret på området Vest USA, bliver det endelige fulde til din justering af belastning deishbai32.westus.cloudapp.azure.com.

10. Gem parameterfilen. Og derefter kan du klargør den klynge ved hjælp af Azure PowerShell:

        .\deploy-deis.ps1 -ResourceGroupName [resource group name] -ResourceGroupLocation "West US" -TemplateFile
        .\azuredeploy.json -ParametersFile .\azuredeploy-parameters.json -CloudInitFile .\cloud-config.yaml

  eller Azure CLI:

        ./deploy-deis.sh -n "[resource group name]" -l "West US" -f ./azuredeploy.json -e ./azuredeploy-parameters.json
        -c ./cloud-config.yaml  

11. Når ressourcegruppen er klargjort, kan du se alle ressourcerne i gruppen på Azure klassisk portal. Som vist på følgende skærmbillede, indeholder ressourcegruppen et virtuelt netværk med tre VM'er, der er sammenføjet til de samme tilgængelighed. Gruppen indeholder også en justering af belastning, som har en tilknyttet offentlige IP-adresse.

  ![Den klargjorte ressourcegruppe på Azure klassisk portal](media/virtual-machines-linux-deis-cluster/resource-group.png)

## <a name="install-the-client"></a>Installere klienten

Du skal bruge **deisctl** til at styre din Deis klynge. Selvom deisctl installeres automatisk i alle klyngenoderne, er det en god ide at bruge deisctl på en separat administrative maskine. Desuden, fordi alle noder er konfigureret med private IP-adresser, du skal bruge SSH tunnelføring via den justering af belastning, som har en offentlige IP-adresse, oprette forbindelse til node-computere. Følgende er trinnene, hvordan du konfigurerer deisctl på en separat Ubuntu fysisk eller virtuelt.

1. Installer deisctl:mkdir deis

        cd deis
        curl -sSL http://deis.io/deisctl/install.sh | sh -s 1.6.1
        sudo ln -fs $PWD/deisctl /usr/local/bin/deisctl

2. Tilføje af din private nøgle til ssh agent:

        eval `ssh-agent -s`
        ssh-add [path to the private key file, see step 1 in the previous section]

3. Konfigurere deisctl:

        export DEISCTL_TUNNEL=[public ip of the load balancer]:2223

Skabelonen definerer indgående NAT-regler, der knyttes 2223 til forekomst 1, 2224 på forekomst 2 og 2225 på forekomst 3. Dette giver redundans for ved hjælp af værktøjet deisctl. Du kan undersøge disse regler på Azure klassisk portalen:

![NAT regler på justering af belastning](media/virtual-machines-linux-deis-cluster/nat-rules.png)

> [AZURE.NOTE] Skabelonen understøtter i øjeblikket kun 3-node klynger. Dette er på grund af en begrænsning i Azure ressourcestyring skabelon NAT regel definition, som ikke understøtter løkke syntaks.

## <a name="install-and-start-the-deis-platform"></a>Installer og start den Deis platform

Nu kan du bruge deisctl til at installere og starte den Deis platform:

    deisctl config platform set domain=[some domain]
    deisctl config platform set sshPrivateKey=[path to the private key file]
    deisctl install platform
    deisctl start platform

> [AZURE.NOTE] Starte platformen tager et stykke tid (op til 10 minutter). Især, kan starte tjenesten builder tage lang tid. Og nogle gange det tager et par forsøg at lykkes: Hvis handlingen ser ud til at hænge, kan du prøve at skrive `ctrl+c` bryde udførelse af kommandoen, og prøv igen.

Du kan bruge `deisctl list` at bekræfte, hvis alle tjenester kører:

    deisctl list
    UNIT                            MACHINE                 LOAD    ACTIVE          SUB
    deis-builder.service            ebe3005e.../10.0.0.6    loaded  active          running
    deis-controller.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-database.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logger.service             9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-logspout.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-publisher.service          8d658d5a.../10.0.0.4    loaded  active          running
    deis-publisher.service          9c79bbdd.../10.0.0.5    loaded  active          running
    deis-publisher.service          ebe3005e.../10.0.0.6    loaded  active          running
    deis-registry@1.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@1.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@2.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-router@3.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-daemon.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-gateway@1.service    9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-metadata.service     9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-monitor.service      8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-monitor.service      9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-monitor.service      ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-volume.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-volume.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-volume.service       ebe3005e.../10.0.0.6    loaded  active          running

Tillykke! Nu har du en løbende Deis clsuter på Azure! Dernæst skal installere Lad os en stikprøve gå program tilladelse til at se klynge i aktion.

## <a name="deploy-and-scale-a-hello-world-application"></a>Installere og tilpasse et Hej verden til computeren

Nedenstående fremgangsmåde viser, hvordan du kan installere en "Hej verden" gå programmet til klyngen. Trinnene er baseret på [Deis dokumentation](http://docs.deis.io/en/latest/using_deis/using-dockerfiles/#using-dockerfiles).

1. For routing masken for at fungere korrekt, skal du have et jokertegn A-post for dit domæne, der peger på den offentlige IP-adresse for justering af belastning. Følgende skærmbillede viser A-posten for en eksempel domæne registrering på GoDaddy:

    ![Godaddy A-post](media/virtual-machines-linux-deis-cluster/go-daddy.png)
<p />
2. Installer deis:

        mkdir deis
        cd deis
        curl -sSL http://deis.io/deis-cli/install.sh | sh
        ln -fs $PWD/deis /usr/local/bin/deis
        
3. Oprette en ny SSH nøgle og derefter tilføje den offentlige nøgle til GitHub (naturligvis du kan også genbruge din eksisterende nøgler). For at oprette et nyt SSH vigtige par skal du bruge:

        cd ~/.ssh
        ssh-keygen (press [Enter]s to use default file names and empty passcode)

4. Føje id_rsa.pub eller offentlig nøgle efter eget valg til GitHub. Du kan gøre dette ved hjælp af Tilføj SSH vigtige knappen i skærmen SSH taster konfiguration:

  ![Github nøgle](media/virtual-machines-linux-deis-cluster/github-key.png)
<p />
5. Registrere en ny bruger:

        deis register http://deis.[your domain]
<p />
6. Tilføje tasten SSH:

        deis keys:add [path to your SSH public key]
  <p />      
7. Oprette et program.

        git clone https://github.com/deis/helloworld.git
        cd helloworld
        deis create
        git push deis master
<p />
8.Ciffer opslagsnål udløser Docker billeder for at være bygget og installeret, hvilket kan tage et par minutter. Fra min oplevelse undertiden kan trin 10 (Pushing billede til private lager) hænge. Når dette sker, du kan stoppe processen til, ved at fjerne programmet ved hjælp af ' deis apps: destroy – en <application name> ` to remove the application and try again. You can use `deis apps:list' at finde ud af navnet på dit program. Hvis det hele fungerer, skal du se noget i retning af følgende i slutningen af kommandoen output:

        -----> Launching...
               done, lambda-underdog:v2 deployed to Deis
               http://lambda-underdog.artitrack.com
               To learn more, use `deis help` or visit http://deis.io
        To ssh://git@deis.artitrack.com:2222/lambda-underdog.git
         * [new branch]      master -> master
<p />
9. Kontrollere, om programmet virker:

        curl -S http://[your application name].[your domain]
  Du burde se:

        Welcome to Deis!
        See the documentation at http://docs.deis.io/ for more information.
        (you can use geis apps:list to get the name of your application).
<p />
10. Skalere programmet på forekomster af 3:

        deis scale cmd=3
<p />
11. Du kan også vælge at bruge deis oplysninger for at undersøge detaljerne for dit program. Der er følgende output fra min programmet installation:

        deis info
        === lambda-underdog Application
        {
          "updated": "2015-05-22T06:14:10UTC",
          "uuid": "10c74ee7-b7ff-4786-967a-7e65af7eabc3",
          "created": "2015-05-22T06:07:55UTC",
          "url": "lambda-underdog.artitrack.com",
          "owner": "haishi",
          "id": "lambda-underdog",
          "structure": {
            "cmd": 3
          }
        }

        === lambda-underdog Processes
        --- cmd:
        cmd.1 up (v2)
        cmd.2 up (v2)
        cmd.3 up (v2)

        === lambda-underdog Domains
        No domains

## <a name="next-steps"></a>Næste trin

I denne artikel gennemført du alle trinnene til at klargøre en ny Deis klynge på Azure ved hjælp af en skabelon til Azure ressourcestyring. Skabelonen understøtter redundans i værktøjer forbindelser samt justering af belastning for installerede programmer. Skabelonen undgår også ved hjælp af offentlige IP-adresser på knuder, medlem, der gemmer værdifulde offentlige IP-ressourcer og giver et mere sikkert miljø til host programmer. Hvis du vil vide mere, skal du se følgende artikler:

[Oversigt over Azure ressourcestyring] [resource-group-overview]  
[Sådan bruger du Azure CLI] [azure-command-line-tools]  
[Brug af Azure PowerShell med Azure ressourcestyring] [powershell-azure-resource-manager]  

[azure-command-line-tools]: ../xplat-cli-install.md
[resource-group-overview]: ../azure-resource-manager/resource-group-overview.md
[powershell-azure-resource-manager]: ../powershell-azure-resource-manager.md
