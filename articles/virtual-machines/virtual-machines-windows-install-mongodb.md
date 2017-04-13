<properties
    pageTitle="Installere MongoDB på en Windows-VM | Microsoft Azure"
    description="Lær, hvordan du installerer MongoDB på en Azure VM, der kører Windows Server 2012 R2, der er oprettet med implementeringsmodel ressourcestyring."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="iainfou"/>

# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Installere og konfigurere MongoDB på en Windows-VM i Azure
[MongoDB](http://www.mongodb.org) er en populære open source-, høj ydeevne NoSQL database. I denne artikel hjælper dig med installation og konfiguration af MongoDB på en Windows Server 2012 R2 virtuel maskine (VM) i Azure. Du kan også [installere MongoDB på en Linux VM i Azure](virtual-machines-linux-install-mongodb.md).


## <a name="prerequisites"></a>Forudsætninger

Før du installerer og konfigurerer MongoDB, skal du oprette en VM og føje ideelt disken data til den. Se følgende artikler for at oprette en VM og tilføje en datadisk:

- [Oprette en Windows Server VM ved hjælp af portalen Azure](virtual-machines-windows-hero-tutorial.md) eller [oprette en Windows Server-VM ved hjælp af Azure PowerShell](virtual-machines-windows-ps-create.md)
- [Vedhæft en datadisk til en Windows Server VM ved hjælp af portalen Azure](virtual-machines-windows-attach-disk-portal.md) eller [vedhæfte en datadisk til en Windows Server VM ved hjælp af Azure PowerShell](https://msdn.microsoft.com/library/mt603673.aspx)
    
For at starte installation og konfiguration af MongoDB, [logge på din Windows Server VM](virtual-machines-windows-connect-logon.md) ved hjælp af Remote Desktop.


## <a name="install-mongodb"></a>Installere MongoDB

> [AZURE.IMPORTANT] MongoDB sikkerhedsfunktioner som godkendelses- og IP-adresse indbinding, er ikke aktiveret som standard. Funktioner for sikkerhed skal være aktiveret, før du anvender MongoDB til et produktionsmiljø. Yderligere oplysninger finder du se [MongoDB sikkerhed og godkendelse](http://www.mongodb.org/display/DOCS/Security+and+Authentication).

1. Når du har forbindelse til din VM ved hjælp af Fjernskrivebord, Åbn Internet Explorer fra menuen **Start** på VM.

2. Vælg **det anbefales at bruge sikkerhed, beskyttelse af personlige oplysninger, og kompatibilitetsindstillinger** når Internet Explorer åbnes første gang, og klik på **OK**.

3. Konfiguration af øget sikkerhed i Internet Explorer er aktiveret som standard. Føje webstedet MongoDB til listen over tilladte websteder:

    - Vælg ikonet **værktøjer** i øverste højre hjørne.
    - Vælg fanen **sikkerhed** i **Internetindstillinger**, og vælg derefter ikonet **Pålidelige websteder** .
    - Klik på knappen **websteder** . Tilføje _https://\*. mongodb.org_ til listen over pålidelige websteder, og derefter lukke dialogboksen.

    ![Konfigurere sikkerhedsindstillinger for Internet Explorer](./media/virtual-machines-windows-install-mongodb/configure-internet-explorer-security.png)

4. Gå til siden [MongoDB - overførsler](http://www.mongodb.org/downloads) (http://www.mongodb.org/downloads).

5. Som standard, skal den Vælg **Community Server** edition og seneste aktuelle stabil overgang til Windows Server 2008 R2 64-bit eller nyere. Klik på **Hent (msi)**for at hente installationsprogrammet.

    ![Hente MongoDB installationsprogram](./media/virtual-machines-windows-install-mongodb/download-mongodb.png)

    Når overførslen er fuldført, skal du køre installationsprogrammet.

6. Læs og accepter licensaftalen. Når du bliver bedt om det skal du vælge Installer for **færdigt** .

7. Klikke på **installation**i det sidste skærmbillede.


## <a name="configure-the-vm-and-mongodb"></a>Konfigurere VM og MongoDB

1. Stien variabler opdateres ikke af MongoDB installationsprogrammet. Uden at MongoDB `bin` placering i path-variabel, skal du angive den fulde sti, hver gang du bruger en MongoDB eksekverbar fil. Sådan føjes placeringen til path-variabel:

    - Højreklik på menuen **Start** , og vælg derefter **System**.
    - Klik på **Avancerede systemindstillinger**, og klik derefter på **Miljøvariabler**.
    - Vælg **sti**under **Systemvariabler**, og klik derefter på **Rediger**.

    ![Konfigurere stivariabler](./media/virtual-machines-windows-install-mongodb/configure-path-variables.png)

    Føje stien til din MongoDB `bin` mappe. MongoDB installeres typisk i `C:\Program Files\MongoDB`. Kontrollér installationssti på din VM. I følgende eksempel adderes standard MongoDB installere placering til den `PATH` variabel:

    ```
    ;C:\Program Files\MongoDB\Server\3.2\bin
    ```

    > [AZURE.NOTE] Sørg for at tilføje foranstillet semikolon (`;`) til at angive, at du tilføjer en placering til dine `PATH` variabel.

2. Opret MongoDB data og logfiler mapper på harddisken data. Vælg **kommandoprompt**fra menuen **Start** . I følgende eksempler Opret mapperne på drev F:

    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```

3. Start en forekomst af MongoDB med følgende kommando, justere stien til dine data, og log kataloger tilsvarende:

    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```

    Det kan tage flere minutter, før MongoDB allokere journal-filer, og begynd at lytte til forbindelser. Alle meddelelser bliver bedt om at filen *F:\MongoLogs\mongolog.log* som `mongod.exe` server starter og allokerer journal-filer.

    > [AZURE.NOTE] Kommandoprompten forbliver fokus på denne opgave, mens der kører din MongoDB forekomst. Lad kommandopromptvinduet være åben for at fortsætte med at køre MongoDB. Eller installere MongoDB som tjeneste, som beskrevet i næste trin.

4. For at få en mere solid MongoDB oplevelse installere den `mongod.exe` som en tjeneste. Oprette en tjeneste betyder, at du ikke behøver at lade en kommandoprompt, der kører, hver gang, du vil bruge MongoDB. Oprette tjenesten, justere stien til dine data og logfiler adresselister tilsvarende:

    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log `
        --logappend  --install
    ```

    Kommandoen foregående opretter en tjeneste med navnet MongoDB, med en beskrivelse af "Mongo DB". Følgende parametre angives også:

    - Den `--dbpath` indstilling angiver placeringen af datamappen.
    - Den `--logpath` indstilling skal bruges til at angive en logfil, da tjenesten kører ikke har et kommandovindue til at vise output.
    - Den `--logappend` indstilling angiver, at en genstart af tjenesten forårsager output skal føjes til den eksisterende logfil.

  For at starte tjenesten MongoDB skal du køre følgende kommando:

    ```
    net start MongoDB
    ```

    Du kan finde flere oplysninger om oprettelse af tjenesten MongoDB [konfigurere en Windows-tjeneste til MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>Teste forekomsten MongoDB

Med MongoDB kører som en enkelt forekomst eller installeret som en tjeneste, kan du nu starte oprette og bruge dine databaser. Åbn en anden kommandopromptvindue fra menuen **Start** for at starte MongoDB administrative shell skal, og Skriv følgende kommando:

```
mongo  
```

Du kan få vist databaserne med den `db` kommandoen. Indsætte nogle data på følgende måde:

```
db.foo.insert( { a : 1 } )
```

Søge efter data på følgende måde:

```
db.foo.find()
```

Output ligner i følgende eksempel:

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

Afslut den `mongo` konsol på følgende måde:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Konfigurere firewall og netværk sikkerhedsgruppe regler
Nu, hvor MongoDB er installeret og køre, skal du åbne en port i Windows Firewall, så du kan få fjernadgang forbindelse til MongoDB. Åbne en administrative PowerShell-prompt for at oprette en ny indgående regel for at tillade TCP-port 27017, og Skriv følgende kommando:

```powerShell
New-NetFirewallRule -DisplayName "Allow MongoDB" -Direction Inbound `
    -Protocol TCP -LocalPort 27017 -Action Allow
```

Du kan også oprette reglen ved hjælp af værktøjet **Windows Firewall med Avanceret sikkerhed** grafiske administration. Oprette en ny indgående regel for at tillade TCP-port 27017.

Hvis det er nødvendigt, kan du oprette en netværk sikkerhedsgruppe regel for at give adgang til MongoDB fra uden for det eksisterende Azure virtuelle netværksundernet. Du kan oprette netværk sikkerhedsgruppe reglerne ved hjælp af [Azure portal](virtual-machines-windows-nsg-quickstart-portal.md) eller [Azure PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md). Tillad TCP-port 27017 grænsefladen virtuelt netværk for din MongoDB VM som i Windows Firewall-regler.

> [AZURE.NOTE] TCP-port 27017 er standardporten bruges af MongoDB. Du kan ændre denne port ved hjælp af den `--port` parameter, når du starter `mongod.exe` manuelt eller fra en tjeneste. Hvis du ændrer porten, skal du sørge for at opdatere Windows Firewall og netværk sikkerhedsgruppe regler i de forrige trin.


## <a name="next-steps"></a>Næste trin
I dette selvstudium lært du, hvordan du installerer og konfigurerer MongoDB på din Windows-VM. Du kan nu få adgang til MongoDB på din Windows-VM, ved at følge de avancerede emner i [MongoDB dokumentation](https://docs.mongodb.com/manual/).
