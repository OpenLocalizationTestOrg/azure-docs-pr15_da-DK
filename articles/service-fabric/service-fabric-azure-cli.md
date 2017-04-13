<properties
   pageTitle="Arbejde med tjenesten strukturen klynger ved hjælp af CLI | Microsoft Azure"
   description="Sådan bruges Azure CLI til at interagere med en tjeneste-strukturen klynge"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/24/2016"
   ms.author="subramar"/>


# <a name="using-the-azure-cli-to-interact-with-a-service-fabric-cluster"></a>Brug af Azure CLI til at interagere med en tjeneste-strukturen klynge

Du kan interagere med tjenesten strukturen klynge fra Linux-computere ved hjælp af Azure CLI på Linux.

Det første trin er Hent den nyeste version af CLI fra ciffer sælger og konfigurere det i din sti ved hjælp af følgende kommandoer:

```sh
 git clone https://github.com/Azure/azure-xplat-cli.git
 cd azure-xplat-cli
 npm install
 sudo ln -s \$(pwd)/bin/azure /usr/bin/azure
 azure servicefabric
```

For hver kommando, du understøtter, kan du skrive navnet på kommandoen til at få hjælp til denne kommando. Automatisk fuldførelse understøttes for kommandoerne. Følgende kommando får du hjælp eksempelvis for alle kommandoerne programmet på computeren. 

```sh
 azure servicefabric application 
```

Du kan yderligere filtrere hjælp til en bestemt kommando, som vist i følgende eksempel:

```sh
 azure servicefabric application  create
```

For at aktivere automatisk-fuldførelse i CLI skal du køre følgende kommandoer:

```sh
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.sh\_profile
source ~/azure.completion.sh
```

Følgende kommandoer oprette forbindelse til klyngen og vise knuderne i klyngen:

```sh
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric node show
```

Hvis du vil bruge navngivne parametre, og finde de er, kan du skrive – Hjælp efter en kommando. Eksempel:

```sh
 azure servicefabric node show --help
 azure servicefabric application create --help
```

Når du opretter forbindelse til en flere maskiner klynge fra en computer **, som er ikke en del af klyngen**, kan du bruge følgende kommando:

```sh
 azure servicefabric cluster connect http://PublicIPorFQDN:19080
```

Erstat PublicIPorFQDN mærket med den reelle IP-adresse eller det fulde Domænenavn efter behov. Når du opretter forbindelse til en flere maskiner klynge fra en computer **, som er en del af klyngen**, kan du bruge følgende kommando:

```sh
 azure servicefabric cluster connect --connection-endpoint http://localhost:19080 --client-connection-endpoint PublicIPorFQDN:19000
```

Du kan bruge PowerShell eller CLI til at interagere med din Linux Service-strukturen klynge oprettet via Azure-portalen. 

**Advarsel:** Disse klynger ikke sikkert, og som derfor, du kan åbne din én boks ved at tilføje den offentlige IP-adresse i klynge manifestet.



## <a name="using-the-azure-cli-to-connect-to-a-service-fabric-cluster"></a>Brug af Azure CLI til at oprette forbindelse til en tjeneste-strukturen klynge

Følgende Azure CLI kommandoer beskriver, hvordan du opretter forbindelse til en sikker klynge. Certifikatdetaljer skal svare til et certifikat på klyngenoderne.

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert
```
 
Hvis dit certifikat har nøglecentre (nøglecentre), skal du tilføje parameteren – ca-cert-sti som i følgende eksempel: 

```
 azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```
Hvis du har flere nøglecentre, kan du bruge et komma som afgrænseren.
 
Hvis dit almindelige navn i certifikatet, der ikke svarer til slutpunktet forbindelse, kan du bruge parameteren `--strict-ssl` kan springe bekræftelsen, som vist i følgende kommando: 

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl false 
```
 
Hvis du vil springe CA bekræftelsen, kan du tilføje – Afvis uautoriseret parameteren som vist i følgende kommando: 

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized false 
```
 
Når du opretter forbindelse, skal du kunne køre andre CLI-kommandoer til at interagere med klyngen. 

## <a name="deploying-your-service-fabric-application"></a>Implementering af programmets Service-strukturen

Udfør følgende kommandoer for at kopiere, registrere, og start-strukturen tjenesteprogrammet:

```
azure servicefabric application package copy [applicationPackagePath] [imageStoreConnectionString] [applicationPathInImageStore]
azure servicefabric application type register [applicationPathinImageStore]
azure servicefabric application create [applicationName] [applicationTypeName] [applicationTypeVersion]
```


## <a name="upgrading-your-application"></a>Opgradering af dit program

Processen, der ligner [proces i Windows](service-fabric-application-upgrade-tutorial-powershell.md)).

Oprette, kopiere, Registrer og oprette dit program fra project rodmappe. Hvis din forekomst af programmet hedder strukturen: / MySFApp, og typen er MySFApp, kommandoerne, der ville være på følgende måde:

```
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
 azure servicefabric application create fabric:/MySFApp MySFApp 1.0
```

Foretag ændringer i dit program og genopbygge tjenesten ændret.  Opdatere tjenesten ændret manifestfilen (ServiceManifest.xml) med de opdaterede versioner for den tjeneste (og kode eller Config eller Data efter behov). Ændre også progammanifest (ApplicationManifest.xml) med opdaterede versionsnummeret for programmet, og den ændrede tjeneste.  Nu, kopiere og tilmelde dig din opdaterede program ved hjælp af følgende kommandoer:

```
 azure servicefabric cluster connect http://localhost:19080>
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
```

Nu kan du starte programmet opgraderingen med følgende kommando:

```
 azure servicefabric application upgrade start -–application-name fabric:/MySFApp -–target-application-type-version 2.0  --rolling-upgrade-mode UnmonitoredAuto
```

Nu kan du overvåge programmet opgraderingen ved hjælp af SFX. I et par minutter, ville programmet er blevet opdateret.  Du kan også prøve en opdaterede app med en fejl og kontrollere automatisk rollback funktionaliteten i tjenesten struktur.

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

### <a name="copying-of-the-application-package-does-not-succeed"></a>Kopiering af pakken programmet lykkes ikke

Markér, hvis `openssh` er installeret. Som standard har Ubuntu computeren ikke installeret den. Installere det ved hjælp af følgende kommando:

```
 sudo apt-get install openssh-server openssh-client**
```

Hvis problemet fortsætter, kan du prøve at deaktivere PAM for ssh ved at ændre filen **sshd_config** ved hjælp af følgende kommandoer:

```sh
 sudo vi /etc/ssh/sshd_config
#Change the line with UsePAM to the following: UsePAM no
 sudo service sshd reload
```

Hvis problemet fortsætter stadig, kan du prøve at øge antallet af ssh sessioner ved at udføre følgende kommandoer:

```sh
 sudo vi /etc/ssh/sshd\_config
# Add the following to lines:
# MaxSessions 500
# MaxStartups 300:30:500
 sudo service sshd reload
```
Hjælp til ssh godkendelse (i modsætning til adgangskoder) endnu ikke understøttes (da platformen bruger ssh til at kopiere pakker), så i stedet bruge godkendelse af adgangskode.


## <a name="next-steps"></a>Næste trin

Konfigurere udviklingsmiljøet og implementere et Service-strukturen program til en Linux klynge.
