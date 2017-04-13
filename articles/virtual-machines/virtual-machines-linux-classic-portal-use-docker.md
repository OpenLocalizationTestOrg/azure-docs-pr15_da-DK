<properties
    pageTitle="Ved hjælp af Docker VM lokalnummer til Linux | Microsoft Azure"
    description="Beskriver Docker og virtuelle Azure-computere filtypenavne, og hvordan du kan oprette virtuelle Azure-computere, der er docker værter ved hjælp af Azure CLI i klassisk implementeringsmodel."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="05/27/2016"
    ms.author="rasquill"/>


# <a name="using-the-docker-vm-extension-with-the-azure-classic-portal"></a>Ved hjælp af filtypenavnet Docker VM ved hjælp af Azure klassisk portal

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


[Docker](https://www.docker.com/) er et af de mest populære virtualization metoder, der bruger [Linux beholdere](http://en.wikipedia.org/wiki/LXC) i stedet for virtuelle maskiner som en metode til at isolere data og databehandling med på delte ressourcer. Du kan bruge filtypenavnet Docker VM administreres af [Azure Linux Agent] til at oprette en Docker VM, der er vært et vilkårligt antal beholdere til dine programmer på Azure.

> [AZURE.NOTE] Dette emne beskrives, hvordan du opretter en Docker VM fra Azure klassisk portal. Hvis du vil se, hvordan du opretter en Docker VM på kommandolinjen skal se, [hvordan du bruger filtypenavnet Docker VM fra Azure kommandolinjen (Azure CLI)]. For at se en overordnet beskrivelse af objektbeholdere og deres fordele skal du se [Docker høj niveau Whiteboard](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).

## <a name="create-a-new-vm-from-the-image-gallery"></a>Oprette en ny VM fra galleriet billede
Det første trin kræver en Azure VM fra et Linux-billede, der understøtter filtypenavnet Docker VM, med et Ubuntu 14.04 LTS billede fra galleriet billede som et billede med eksempel på server og Ubuntu 14.04 computeren som en klient. I portalen, skal du klikke på **+ Ny** i det nederste venstre hjørne for at oprette en ny forekomst af VM og vælge et Ubuntu 14.04 LTS billede fra de tilgængelige indstillinger eller fra galleriet færdigt billede, som vist nedenfor.

> [AZURE.NOTE] På nuværende tidspunkt understøtter kun Ubuntu 14.04 LTS billeder nyere end juli 2014 filtypenavnet Docker VM.

![Oprette et nyt Ubuntu billede](./media/virtual-machines-linux-classic-portal-use-docker/ChooseUbuntu.png)

## <a name="create-docker-certificates"></a>Oprette Docker certifikater

Når VM er blevet oprettet, kan du sikre dig, at Docker er installeret på klientcomputeren. (Få mere at vide under [Docker installationsvejledning](https://docs.docker.com/installation/#installation)).

Oprette certifikatet og nøglen filerne til Docker kommunikation ifølge [Kører Docker med https] og sætte dem i den **`~/.docker`** mappe på klientcomputeren.

> [AZURE.NOTE] Filtypenavnet Docker VM i portalen kræver legitimationsoplysninger, der er base64-kodet i øjeblikket.

På kommandolinjen skal bruge **`base64`** eller et andet foretrukne kodning værktøj til at oprette base64-kodet emner. Gør dette med en enkelt række certifikat og nøgle filer vil se nogenlunde sådan ud:

```
 ~/.docker$ ls
 ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
 ~/.docker$ base64 ca.pem > ca64.pem
 ~/.docker$ base64 server-cert.pem > server-cert64.pem
 ~/.docker$ base64 server-key.pem > server-key64.pem
 ~/.docker$ ls
 ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
 ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem
```

## <a name="add-the-docker-vm-extension"></a>Tilføje filtypenavnet Docker VM
Find den VM-forekomst, du har oprettet for at tilføje filtypenavnet Docker VM, og Rul ned til **filtypenavne** og klikke på for at få vist VM filtypenavne, som vist nedenfor.
> [AZURE.NOTE] Denne funktion understøttes i portalen preview: https://portal.azure.com/

![](./media/virtual-machines-linux-classic-portal-use-docker/ClickExtensions.png)
### <a name="add-an-extension"></a>Tilføje et filtypenavn
Klik på **+ Tilføj** for at få vist de mulige VM udvidelser, du kan føje til denne VM.

![](./media/virtual-machines-linux-classic-portal-use-docker/ClickAdd.png)
### <a name="select-the-docker-vm-extension"></a>Vælg filtypenavnet Docker VM
Vælg Docker VM udvidelse, som viser Docker beskrivelsen og vigtige links, og klik derefter på **Opret** nederst til at starte installationen.

![](./media/virtual-machines-linux-classic-portal-use-docker/ChooseDockerExtension.png)

![](./media/virtual-machines-linux-classic-portal-use-docker/CreateButtonFocus.png)
### <a name="add-your-certificate-and-key-files"></a>Tilføje dit certifikat og vigtige filer:

Angiv de base64-kodet versioner af dit CA-certifikat, dit servercertifikat og produktnøglen Server i formularfelter, som vist i følgende illustration.

![](./media/virtual-machines-linux-classic-portal-use-docker/AddExtensionFormFilled.png)

> [AZURE.NOTE] Bemærk, at (som i det foregående billede) på 2376 er udfyldt som standard. Du kan angive en hvilken som helst slutpunkt her, men det næste trin bliver at åbne det tilsvarende slutpunkt. Hvis du ændrer standard, skal du sørge for at åbne det tilsvarende slutpunkt i næste trin.

## <a name="add-the-docker-communication-endpoint"></a>Tilføje Docker kommunikation slutpunktet
Når du får vist den ressourcegruppe, du har oprettet, Vælg sikkerhedsgruppen netværk, der er knyttet til din VM, og klik på **Indgående sikkerhedsregler** for at få vist reglerne, som vist her.

![](./media/virtual-machines-linux-classic-portal-use-docker/AddingEndpoint.png)

Klik på **+ Tilføj** for at tilføje en anden regel, og indtast et navn til slutpunktet (i dette eksempel **Docker**) og 2376 'Port destinationsområde' i standard store og små bogstaver. Angiv værdien fra protocol, der viser **TCP**, og klik på **OK** for at oprette reglen.

![](./media/virtual-machines-linux-classic-portal-use-docker/AddEndpointFormFilledOut.png)


## <a name="test-your-docker-client-and-azure-docker-host"></a>Teste din Docker klient og Azure Docker Host
Finde og kopiere og indsætte navnet på din VM domæne, og på kommandolinjen på klientcomputeren, type `docker --tls -H tcp://` *dockerextension* `.cloudapp.net:2376 info` (hvor *dockerextension* erstattes af underdomænet til din VM).

Resultatet skal vises nogenlunde sådan ud:

```
$ docker --tls -H tcp://dockerextension.cloudapp.net:2376 info
Containers: 0
Images: 0
Storage Driver: devicemapper
 Pool Name: docker-8:1-131214-pool
 Pool Blocksize: 65.54 kB
 Data file: /var/lib/docker/devicemapper/devicemapper/data
 Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
 Data Space Used: 305.7 MB
 Data Space Total: 107.4 GB
 Metadata Space Used: 729.1 kB
 Metadata Space Total: 2.147 GB
 Library Version: 1.02.82-git (2013-10-04)
Execution Driver: native-0.2
Kernel Version: 3.13.0-36-generic
WARNING: No swap limit support
```

Når du har fuldført de ovenstående trin, har du nu en fuldt funktionelle Docker vært, der kører på en Azure VM, der er konfigureret til at være forbundet med fra en fjernplacering fra andre klienter.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Næste trin

Du er klar til at gå til [Docker brugervejledningen] og bruge din Docker VM. Hvis du vil automatisere oprettelse Docker værter på Azure FOS gennem linje kommandogrænseflade, se, [hvordan du bruger filtypenavnet Docker VM fra Azure kommandolinjen (Azure CLI)]

<!--Anchors-->
[Create a new VM from the Image Gallery]: #createvm
[Create Docker Certificates]: #dockercerts
[Add the Docker VM Extension]: #adddockerextension
[Test Docker Client and Azure Docker Host]: #testclientandserver
[Next steps]: #next-steps

<!--Image references-->
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Hvordan du bruger filtypenavnet Docker VM fra Azure kommandolinjen (Azure CLI)]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Azure Linux Agent]: virtual-machines-linux-agent-user-guide.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md

[Køre Docker med https]: http://docs.docker.com/articles/https/
[Docker brugervejledningen]: https://docs.docker.com/userguide/
