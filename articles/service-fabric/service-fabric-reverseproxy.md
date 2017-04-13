<properties
   pageTitle="Vedligeholde strukturen omvendt Proxy | Microsoft Azure"
   description="Brug Service-strukturen omvendt proxy til kommunikation til microservices fra i og uden for klyngen"
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/04/2016"
   ms.author="vturecek"/>

# <a name="service-fabric-reverse-proxy"></a>Tjenesten strukturen omvendt Proxy

Tjenesten strukturen modsat proxyen er en omvendt proxy indbyggede i tjenesten struktur, der kan håndtere microservices i tjenesten strukturen klynge, der viser HTTP slutpunkter.

## <a name="microservices-communication-model"></a>Microservices kommunikation model

Microservices i tjenesten strukturen typisk kører på et undersæt af VMS i klyngen og kan skifte fra én VM til en anden af forskellige årsager. Så slutpunkterne for microservices kan ændre dynamisk. Typisk mønstret til at kommunikere med microservice er nedenfor, løs løkke

1. Løse placeringen af indledningsvis gennem tjenesten navngive.
2. Oprette forbindelse til tjenesten.
3. Årsagen til mislykkede forbindelser, og at finde placeringen af når det er nødvendigt.

Denne proces involverer generelt ombryde klientsiden kommunikation biblioteker i et nyt forsøg løkke, der implementerer politikker for tjenesten opløsning, og prøv igen.
Se [kommunikere med tjenester](service-fabric-connect-and-communicate-with-services.md)kan finde flere oplysninger om dette emne.

### <a name="communicating-via-sf-reverse-proxy"></a>Kommunikere via SF omvendt proxy
Tjenesten strukturen omvendt proxy kører på alle noder i klyngen. Det udfører tjenesten hele processen opløsning på vegne af en klient og derefter videresender klientens anmodning. Så kan klienter, der kører på klyngen kun bruge en hvilken som helst klientsiden HTTP kommunikation biblioteker til at tale med tjenesten target via SF modsat proxyen kører lokalt på den samme node.

![Intern kommunikation][1]

## <a name="reaching-microservices-from-outside-the-cluster"></a>At kontakte Microservices fra uden for klyngen
Standard ekstern kommunikation model til microservices er **tilmelding** , hvor hver tjeneste som standard kan ikke åbnes direkte fra eksterne kunder. [Azure justering af belastning](../load-balancer/load-balancer-overview.md) er netværk rammen mellem microservices og eksterne kunder, der udfører NAT og videresender eksterne anmodninger til interne **IP:port** slutpunkter. For at gøre en microservice slutpunkt direkte adgang til eksterne kunder, skal den Azure justering af belastning først være konfigureret til at videresende trafik til hver port, der bruges af tjenesten i klyngen. Desuden de fleste microservices (esp. med høj sikkerhed microservices) live ikke på alle noderne af klyngen og de kan flytte mellem noder på failover, så i så fald ikke belastning Azure rent faktisk fastlægger noden target af replikaerne er placeret for at videresende trafik til den.

### <a name="reaching-microservices-via-the-sf-reverse-proxy-from-outside-the-cluster"></a>At kontakte Microservices via SF modsat proxyen fra uden for klyngen

I stedet for konfiguration af individuelle service porte i azure justering af belastning, kan lige SF Reverse proxy port konfigureres i Azure belastning. Dette giver mulighed for kunder uden for klyngen når tjenester i klynge via modsat proxyen uden en yderligere konfigurationer.

![Ekstern kommunikation][0]

>[AZURE.WARNING] Konfiguration af den omvendt proxy port på justering af belastning, gør alle micro tjenester i den klynge, der viser en http-slutpunkt, skal være addressible fra uden for klyngen.


## <a name="uri-format-for-addressing-services-via-the-reverse-proxy"></a>URI-format for bekæmpelse af tjenester via omvendt proxy

Omvendt proxy bruger et bestemt URI-format til at identificere, hvilke service partition indgående anmodningen skal videresendes til:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&Timeout=<timeout_in_seconds>
```

 - **http (s):** Omvendt proxyen kan være konfigureret til at acceptere HTTP eller HTTPS trafik. I tilfælde af HTTPS-trafikken optræder SSL opsigelse før i omvendt proxy. Anmodninger, der videresendes af omvendt proxy til tjenester i klyngen er via http.
 - **Klynge fulde | interne IP-adresse:** Til eksterne kunder kan omvendt proxy konfigureres, så det er tilgængelige via klynge domæne (f.eks., mycluster.eastus.cloudapp.azure.com). Som standard omvendt proxy, der kører på hver node så til interne trafik den kan ringes på localhost eller på en hvilken som helst interne node IP-adresse (f.eks., 10.0.0.1).
 - **Port:** Den port, der er angivet for modsat proxyen. F.eks: 19008.
 - **ServiceInstanceName:** Dette er navnet på fuldt kvalificeret udløst tjenesten forekomst af den tjeneste, du prøver at nå den "strukturen: /" farveskema. For eksempel, når tjenesten *strukturen: / myapp/myservice/*, du vil bruge *Mitprogr/myservice*.
 - **Suffiks sti:** Dette er den faktiske URL-sti for den tjeneste, du vil oprette forbindelse til. For eksempel *myapi/værdier/tilføje/3*
 - **PartitionKey:** For en partitioneret tjeneste er dette den beregnede Partitionsnøgle for den partition, du skal have fat. Bemærk, at dette er *ikke* partitionen ID GUID. Denne parameter er ikke påkrævet til services ved hjælp af separat partition skemaet.
 - **PartitionKind:** Tjenesten partition skemaet. Det kan være 'Int64Range' eller "Med navnet". Denne parameter er ikke påkrævet til services ved hjælp af separat partition skemaet.
 - **Timeout:**  Dette angiver timeout for HTTP-anmodningen oprettet af den omvendte proxy til tjenesten på vegne af klientanmodningen. Standardværdien for dette er 60 sekunder. Dette er en valgfri parameter.

### <a name="example-usage"></a>Eksempel på format

Som et eksempel, Lad os service **strukturen: / MyApp/MyService** , der åbnes en HTTP-lytteren på følgende URL-adressen:

```
http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Med følgende ressourcer:

 - `/index.html`
 - `/api/users/<userId>`

Hvis tjenesten bruger separat partitionsskema, forespørgselsparametre streng *PartitionKey* og *PartitionKind* er ikke påkrævet, og tjenesten kan kontaktes via gatewayen som:

 - Eksternt:`http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService`
 - Internt:`http://localhost:19008/MyApp/MyService`

Hvis tjenesten anvender det ensartet Int64 partitionsskema, skal forespørgselsparametre streng *PartitionKey* og *PartitionKind* bruges til at oprette forbindelse til en partition for tjenesten:

 - Eksternt:`http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
 - Internt:`http://localhost:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Når de ressourcer, der vises af tjenesten, skal du blot Placer ressource stien efter navnet på tjenesten i URL-adressen:

 - Eksternt:`http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
 - Internt:`http://localhost:19008/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

Gatewayen videresender derefter disse anmodninger til tjenestens URL-adresse:

 - `http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
 - `http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Særbehandling til deling af port tjenester

Gatewayen programmet forsøger at løse en tjeneste-adresse igen, og prøv igen anmodningen, når en tjeneste ikke kan nås. Dette er en af de vigtigste fordele ved gatewayen, som klienten kode ikke er nødvendigt at implementere sin egen service opløsning og løse løkke.

Når en tjeneste ikke kan nås betyder det normalt den forekomst af tjenesten eller replika er flyttet til en anden node som en del af sin normal livscyklus. Når dette sker, muligvis gatewayen fejlen netværk forbindelse, der angiver et slutpunkt er ikke længere åbne på den adresse, der oprindeligt løst.

Dog replikaer eller forekomster af tjenesten kan dele en host proces og kan også dele en port, når hostet af en http.sys-baseret webserver, herunder:

 - [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
 - [ASP.NET Core WebListener](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
 - [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

I dette tilfælde er det sandsynligvis, webserveren er tilgængelig i host processen og besvare mødeindkaldelser, men den forekomst af løst tjenesten eller replika er ikke længere tilgængelig på værten. I dette tilfælde modtager gatewayen en HTTP 404-svar fra web-serveren. Derfor har en HTTP 404 to forskellige betydning:

 1. Tjenesten adressen er korrekt, men den ressource, der er blevet anmodet af brugeren findes ikke.
 2. Den tjeneste-adresse er forkert, og den ressource, der er blevet anmodet af brugeren kan faktisk findes på en anden node.

I det første tilfælde er dette en normal HTTP 404-fejlmeddelelse, der betragtes som en brugerfejl. Dog brugeren har anmodet om en ressource, der findes i det andet tilfælde, men gatewayen kunne ikke finde det, fordi tjenesten selve er flyttet, i hvilket tilfælde gatewayen skal bruge til at finde adressen, og prøv igen.

Gatewayen, og som derfor skal en metode til at skelne mellem disse to tilfælde. Et tip fra serveren er påkrævet for at gøre Skellet.

 - Som standard Application Gateway antages det tilfælde #2 og forsøger at løse igen og igen udstede anmodningen.
 - Du kan angive sag #1 til gatewayen programmet, skal tjenesten returnere følgende HTTP svarheaderen:

`X-ServiceFabric : ResourceNotFound`

Denne HTTP svarheaderen angiver en normal HTTP 404 situation, hvor den ønskede ressource ikke findes, og gatewayen ikke forsøge at løse adressen til igen.

## <a name="setup-and-configuration"></a>Installation og konfiguration
Tjenesten strukturen omvendt proxy kan aktiveres for klynge via [Azure ressourcestyring skabelon](./service-fabric-cluster-creation-via-arm.md).

Når du har skabelonen for den klynge, du vil installere (enten fra eksempelskabeloner eller ved at oprette en brugerdefineret ressource manager skabelon) omvendt proxy kan aktiveres i skabelonen ved at gøre følgende.

1. Definere en port for modsat proxyen i skabelonen [parametre sektion](../resource-group-authoring-templates.md) .

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19008,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Angive porten for hver af nodetype objekterne i **klynge** [ressource type sektion](../resource-group-authoring-templates.md)

    For Apiversion's før ' 2016-09-01' port er identificeret med parameteren navngive ***httpApplicationGatewayEndpointPort***

    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "httpApplicationGatewayEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```

    For Apiversion's på eller efter ' 2016-09-01' port er identificeret med parameteren navngive ***reverseProxyEndpointPort***

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```

3. Konfigurere **regler for azure indlæsning af belastningsjusteringstjenesten** for den angivne port i trin 1 for at løse modsat proxyen fra uden for azure klynge.

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```
4. Hvis du vil konfigurere SSL-certifikater på porten for modsat proxyen, føje certifikatet til egenskaben httpApplicationGatewayCertificate i **klynge** [ressource type sektion](../resource-group-authoring-templates.md)

    For Apiversion's før ' 2016-09-01' certifikatet, der er identificeret med parameteren navngive ***httpApplicationGatewayCertificate***

    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "httpApplicationGatewayCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```
    For Apiversion's på eller efter ' 2016-09-01' certifikatet, der er identificeret med parameteren navngive ***reverseProxyCertificate***
    
    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

## <a name="next-steps"></a>Næste trin
 - Se et eksempel på HTTP-kommunikation mellem tjenester i et [eksempelprojekt på GitHUb](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/WordCount).

 - [Fjernprocedurekald med pålidelig Services remoting](service-fabric-reliable-services-communication-remoting.md)

 - [Web API, der bruger OWIN i pålidelige Services](service-fabric-reliable-services-communication-webapi.md)

 - [WCF-kommunikation ved hjælp af pålidelig Services](service-fabric-reliable-services-communication-wcf.md)


[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
