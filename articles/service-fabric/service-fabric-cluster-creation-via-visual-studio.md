<properties
   pageTitle="Konfiguration af en tjeneste-strukturen klynge, ved hjælp af Visual Studio | Microsoft Azure"
   description="Beskriver, hvordan du konfigurerer en tjeneste-strukturen klynge ved hjælp af Azure ressourcestyring skabelon, der er oprettet af et Azure ressourcegruppe projekt i Visual Studio"
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/06/2016"
   ms.author="karolz@microsoft.com"/>

# <a name="set-up-a-service-fabric-cluster-by-using-visual-studio"></a>Konfigurere en tjeneste-strukturen klynge ved hjælp af Visual Studio
I denne artikel beskrives, hvordan du konfigurerer en Azure Service strukturen klynge ved hjælp af Visual Studio og en Azure ressourcestyring skabelon. Vi bruger et Visual Studio Azure ressource gruppeprojekt til oprettelse af skabelonen. Når skabelonen er blevet oprettet, kan det være installeret direkte til Azure fra Visual Studio. Det kan også bruges fra et script, eller som en del af fortløbende integration (CI) facilitet.

## <a name="create-a-service-fabric-cluster-template-by-using-an-azure-resource-group-project"></a>Oprette en tjeneste-strukturen klynge skabelon ved hjælp af et Azure ressource gruppeprojekt
For at komme i gang skal du åbne Visual Studio og oprette en Azure ressource gruppeprojekt (den er tilgængelig i mappen **skyen** ):

![Dialogboksen nyt projekt med Azure ressourcegruppe project markeret][1]

Du kan oprette en ny Visual Studio-løsning til dette projekt eller føje den til en eksisterende løsning.

>[AZURE.NOTE] Hvis du ikke kan se Azure ressource gruppe projektet under noden skyen, har du ikke installeret Azure SDK. Start Web Platform Installer ([installere det nu,](http://www.microsoft.com/web/downloads/platform.aspx) Hvis du ikke allerede har), og Søg efter "Azure SDK til .NET" og installere den version, der er kompatibelt med din version af Visual Studio.

Når du klikker på knappen OK, Visual Studio bliver bedt om at vælge den ressourcestyring skabelon, du vil oprette:

![Vælg Azure skabelon dialogboks med Service-strukturen klynge skabelon, der er markeret][2]

Vælg skabelonen, Service-strukturen klynge, og tryk på knappen OK igen. Projektet og skabelonen ressourcestyring har nu oprettet.

## <a name="prepare-the-template-for-deployment"></a>Forberede skabelonen til installation
Før skabelonen, der er installeret for at oprette klyngen, skal du angive værdier for parametrene kræves skabelon. Disse parameterværdier læses fra den `ServiceFabricCluster.parameters.json` fil, som er i den `Templates` mappe med gruppeprojekt ressource. Åbn fil, og Angiv følgende værdier:

|Parameternavn           |Beskrivelse|
|-----------------------  |--------------------------|
|adminUserName            |Navnet på administratorkontoen for tjenesten strukturen maskiner (noder).|
|certificateThumbprint    |Miniature af det certifikat, der sikrer klyngen.|
|sourceVaultResourceId    |*Ressource-ID* for den vigtige samling, hvor det certifikat, der sikrer klyngen er gemt.|
|certificateUrlValue      |URL-adressen til sikkerhedscertifikatet klynge.|

Skabelonen Visual Studio Service strukturen ressourcestyring opretter en sikker klynge, der er beskyttet af et certifikat. Dette certifikat er identificeret med de seneste tre Skabelonparametre (`certificateThumbprint`, `sourceVaultValue`, og `certificateUrlValue`), og det skal findes på en **Azure nøgle samling**. Du kan finde flere oplysninger om, hvordan du opretter klynge sikkerhedscertifikatet, se [Service-strukturen klynge sikkerhed scenarier](service-fabric-cluster-security.md#x509-certificates-and-service-fabric) artikel.

## <a name="optional-change-the-cluster-name"></a>Valgfrit: Skift klyngenavnet
Hver tjeneste strukturen klynge har et navn. Når der oprettes en strukturen klynge i Azure, bestemmer klyngenavn (sammen med det Azure område) navn (Domain Name System) for-klyngen. Hvis du navngive din klynge eksempelvis `myBigCluster`, og placeringen (Azure område) af ressourcegruppen, der er vært ny klynge er af USA, bliver de DNS-navnet på klyngen `myBigCluster.eastus.cloudapp.azure.com`.

Som standard klyngenavnet genereres automatisk og foretaget entydige ved at knytte et tilfældigt suffiks til præfikset "klynge". Dette gør det meget nemt at bruge skabelonen som en del af et **sammenhængende integration** (CI) system. Hvis du vil bruge et bestemt navn til din klynge, en, der giver mening for dig, angive værdien af den `clusterName` variable i ressourcestyring skabelonfilen (`ServiceFabricCluster.json`) til din valgte navn. Det er den første variabel, der er defineret i den pågældende fil.

## <a name="optional-add-public-application-ports"></a>Valgfrit: tilføje offentlige programmet porte
Du kan også ændre de offentlige programmet porte for-klyngen, inden du installerer det. Som standard åbnes skabelonen blot to offentlige TCP-porte, (80 og 8081). Hvis du har brug for mere til dine programmer, kan du ændre Azure justering af belastning definitionen i skabelonen. Definitionen er gemt i den primære skabelonfil (`ServiceFabricCluster.json`). Åbn fil, og Søg efter `loadBalancedAppPort`. Hver port er knyttet til tre elementer:

1. En skabelon variabel, der definerer TCP Portværdien for port:

    ```json
    "loadBalancedAppPort1": "80"
    ```

2. En *efterprøvning af af* , der definerer, hvor ofte og hvor lang tid den Azure belastning belastningsjusteringstjenesten forsøger at bruge en bestemt Service-strukturen node før fejlbehæftede over med en anden. Sonderne er en del af justering af belastning ressourcen. Her er efterprøvning af af definitionen af den første standardporten for programmet:

    ```json
    {
        "name": "AppPortProbe1",
        "properties": {
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "port": "[variables('loadBalancedAppPort1')]",
            "protocol": "Tcp"
        }
    }
    ```

3. En *regel for justering af belastning* , der forbinder sammen port og rør, der giver mulighed for justering af belastning på tværs af et sæt Service-strukturen klyngenoder:

    ```json
    {
        "name": "AppPortLBRule1",
        "properties": {
            "backendAddressPool": {
                "id": "[variables('lbPoolID0')]"
            },
            "backendPort": "[variables('loadBalancedAppPort1')]",
            "enableFloatingIP": false,
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPort": "[variables('loadBalancedAppPort1')]",
            "idleTimeoutInMinutes": 5,
            "probe": {
                "id": "[concat(variables('lbID0'),'/probes/AppPortProbe1')]"
            },
            "protocol": "Tcp"
        }
    }
    ```
Hvis de programmer, du planlægger at implementere til klyngen har brug for flere porte, kan du tilføje dem ved oprettelse af yderligere efterprøvning af af og justering af belastning regel definitionerne bevares. Du kan finde flere oplysninger om at arbejde med Azure belastning ressourcestyring skabeloner igennem, kan du se [få en introduktion til en intern justering af belastning ved hjælp af en skabelon](../load-balancer/load-balancer-get-started-ilb-arm-template.md).

## <a name="deploy-the-template-by-using-visual-studio"></a>Installere skabelonen ved hjælp af Visual Studio
Når du har gemt alle de nødvendige parameterværdier i den`ServiceFabricCluster.param.dev.json` fil, du er klar til at installere skabelonen, og Opret din tjeneste strukturen klynge. Højreklik på projektet resource gruppe i Visual Studio Solution Explorer, og vælg **Implementer | Ny installation...**. Hvis det er nødvendigt, vises Visual Studio dialogboksen **Installer til ressourcegruppe** beder dig om at godkende til Azure:

![Installere til ressourcegruppe dialogboks][3]

Dialogboksen kan du vælge en eksisterende ressourcestyring ressourcegruppe for-klyngen og giver dig mulighed for at oprette en ny. Det giver normalt mening at bruge en separat ressourcegruppe til en tjeneste-strukturen klynge.

Når du klikker på knappen Implementer, beder Visual Studio dig om at bekræfte parameterværdierne skabelon. Tryk på knappen **Gem** . En parameter har ikke en permanente værdi: administratorkonto adgangskoden for-klyngen. Du skal angive en adgangskodeværdi, når Visual Studio beder dig om et.

>[AZURE.NOTE] Starter med Azure SDK 2.9, understøtter Visual Studio læsning adgangskoder fra **Azure nøgle samling** under installationen. I dialogboksen skabelon parametre Bemærk, at den `adminPassword` parameter tekstfelt indeholder et lille ikon for "nøgle" i højre side. Dette ikon kan du vælge en eksisterende vigtige samling hemmeligt som administrative adgangskoden for-klyngen. Kun Sørg for at aktivere Azure ressourcestyring adgang til installation af skabelon i de avancerede Access politikker for dine vigtige samling. 

Du kan overvåge forløbet af installationsprocessen i Visual Studio output-vinduet. Når skabelon installationen er fuldført, er din nye klynge klar til brug!

>[AZURE.NOTE] Hvis PowerShell aldrig blev brugt til at administrere Azure fra den computer, du bruger nu, skal du gøre lidt arbejdsgang.
>1. Aktivere PowerShell scripting ved at køre den [`Set-ExecutionPolicy`](https://technet.microsoft.com/library/hh849812.aspx) kommandoen. For udvikling maskiner accepteres "ubegrænset" politik som regel.
>2. Beslutte, om du vil tillade diagnosticering dataindsamling fra Azure PowerShell-kommandoer, og kør [`Enable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619303.aspx) eller [`Disable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619236.aspx) efter behov. Dette vil undgå unødvendig instruktionerne under installation af skabelon.

Hvis der er fejl, gå til [Azure-portalen](https://portal.azure.com/) , og Åbn den ressourcegruppe, du har installeret på. Klik på **Indstillinger for alle**, og klik derefter på **installationer** på bladet indstillinger. Mislykket ressourcegruppe installation lader detaljerede diagnostiske oplysninger der.

>[AZURE.NOTE] Tjenesten strukturen klynger kræver et bestemt antal noder skal være op til at vedligeholde tilgængelighed og bevare stat - kaldes "vedligeholdelse af quorum." Det er derfor ikke sikkert at lukke alle maskiner i klyngen, medmindre du først har udført en [komplet sikkerhedskopiering af din tilstand](service-fabric-reliable-services-backup-restore.md).

## <a name="next-steps"></a>Næste trin
- [Få mere at vide om konfiguration af tjenesten strukturen klynge ved hjælp af portalen Azure](service-fabric-cluster-creation-via-portal.md)
- [Lær at administrere og installere Service-strukturen programmer ved hjælp af Visual Studio](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[1]: ./media/service-fabric-cluster-creation-via-visual-studio/azure-resource-group-project-creation.png
[2]: ./media/service-fabric-cluster-creation-via-visual-studio/selecting-azure-template.png
[3]: ./media/service-fabric-cluster-creation-via-visual-studio/deploy-to-azure.png
