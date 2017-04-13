<properties
    pageTitle="Oprette Hadoop, HBase eller Storm klynger på Linux i HDInsight ved hjælp af krøllet og Azure REST-API | Microsoft Azure"
    description="Få mere at vide, hvordan du opretter Linux-baserede HDInsight klynger med krøllet, Azure ressourcestyring skabeloner og Azure REST-API. Du kan angive, hvilke klynge (Hadoop, HBase eller Storm), eller du kan bruge scripts til at installere brugerdefinerede komponenter."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/11/2016"
    ms.author="larryfr"/>

#<a name="create-linux-based-clusters-in-hdinsight-using-curl-and-the-azure-rest-api"></a>Oprette Linux-baserede klynger i HDInsight ved hjælp af krøllet og Azure REST-API

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Azure REST-API giver dig mulighed at udføre administration handlinger på tjenester, der findes i Azure platformen, herunder oprettelse af nye ressourcer som Linux-baserede HDInsight klynger. I dette dokument lærer du, hvordan du opretter Azure ressourcestyring skabeloner for at konfigurere en HDInsight klynge og tilknyttede lager og derefter bruge krøllet skal installeres skabelonen til Azure REST-API til at oprette en ny HDInsight klynge.

> [AZURE.IMPORTANT] Trinnene i dette dokument bruge standardantallet af arbejder noder (4) til en HDInsight klynge. Hvis du planlægger på mere end 32 arbejder noder, enten ved klynge oprettelse eller ved at skalere klyngen efter oprettelse, skal du vælge en hoved node størrelse med mindst 8 kerner og 14GB ram.
>
> Se [HDInsight priser](https://azure.microsoft.com/pricing/details/hdinsight/)kan finde flere oplysninger om node størrelser og det tilknyttede omkostninger.

##<a name="prerequisites"></a>Forudsætninger

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Azure CLI__. Azure CLI bruges til at oprette en tjeneste ydelsen, som skal bruges til at generere godkendelsestokens for anmodninger om at Azure REST-API.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

- __cURL__. Denne funktion er tilgængelige via din pakke administrationssystem eller kan hentes fra [http://curl.haxx.se/](http://curl.haxx.se/).

    > [AZURE.NOTE] Hvis du bruger PowerShell til at køre kommandoerne i dette dokument, skal du først fjerne den `curl` alias, der oprettes som standard. Dette alias bruger Aktiver-WebRequest, en PowerShell-cmdlet i stedet for krøllet, når du bruger den `curl` kommando fra en PowerShell-prompt, og vil returnere en fejl for mange af de kommandoer, som bruges i dette dokument.
    > 
    > Hvis du vil fjerne dette alias, skal du bruge følgende fra PowerShell-prompt:
    >
    > `Remove-item alias:curl`
    >
    > Når aliaset er blevet fjernet, skal du kunne bruge versionen af krøllet, du har installeret på computeren.

### <a name="access-control-requirements"></a>Krav til Access

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="create-a-template"></a>Oprette en skabelon

Azure ressourcestyring skabeloner er JSON-dokumenter, der beskriver en __ressourcegruppe__ og alle de ressourcer i den (såsom HDInsight.) Denne skabelon baseret fremgangsmåde giver dig mulighed for at definere alle de ressourcer, som du har brug for HDInsight i en skabelon, og til at administrere ændringer i gruppen som helhed gennem __installationer__ , der anvender ændringer til gruppen.

Skabeloner leveres normalt i to dele selve skabelonen, og en parametre-fil, som du har placeret objekterne med bestemte værdier til din konfiguration. For exmaple, klyngenavnet, administratornavn og adgangskode. Når du bruger direkte REST-API, skal du kombinere nøgleordene i én fil. Formatet for dokumentet JSON er:

    {
        "properties": {
            "template": {
                contents of template file
            },
            "mode": "deploymentmode",
            "Parameters": {
                contents of the parameters element from parameters file
            }
        }
    }

For eksempel er følgende en sammenlægning af skabelonen og parametre filerne fra [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password), der opretter en Linux-baserede klynge ved hjælp af en adgangskode til at sikre SSH brugerkontoen.

    {
        "properties": {
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {
                    "location": {
                        "type": "string",
                        "allowedValues": ["Central US",
                        "East Asia",
                        "East US",
                        "Japan East",
                        "Japan West",
                        "North Europe",
                        "South Central US",
                        "Southeast Asia",
                        "West Europe",
                        "West US"],
                        "metadata": {
                            "description": "The location where all azure resources will be deployed."
                        }
                    },
                    "clusterType": {
                        "type": "string",
                        "allowedValues": ["hadoop",
                        "hbase",
                        "storm",
                        "spark"],
                        "metadata": {
                            "description": "The type of the HDInsight cluster to create."
                        }
                    },
                    "clusterName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the HDInsight cluster to create."
                        }
                    },
                    "clusterLoginUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                        }
                    },
                    "clusterLoginPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "sshUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to remotely access the cluster."
                        }
                    },
                    "sshPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "clusterStorageAccountName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the storage account to be created and be used as the cluster's storage."
                        }
                    },
                    "clusterWorkerNodeCount": {
                        "type": "int",
                        "defaultValue": 4,
                        "metadata": {
                            "description": "The number of nodes in the HDInsight cluster."
                        }
                    }
                },
                "variables": {
                    "defaultApiVersion": "2015-05-01-preview",
                    "clusterApiVersion": "2015-03-01-preview"
                },
                "resources": [{
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('defaultApiVersion')]",
                    "dependsOn": [],
                    "tags": {
                        
                    },
                    "properties": {
                        "accountType": "Standard_LRS"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('clusterApiVersion')]",
                    "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                    "tags": {
                        
                    },
                    "properties": {
                        "clusterVersion": "3.2",
                        "osType": "Linux",
                        "clusterDefinition": {
                            "kind": "[parameters('clusterType')]",
                            "configurations": {
                                "gateway": {
                                    "restAuthCredential.isEnabled": true,
                                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                                }
                            }
                        },
                        "storageProfile": {
                            "storageaccounts": [{
                                "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                                "isDefault": true,
                                "container": "[parameters('clusterName')]",
                                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                            }]
                        },
                        "computeProfile": {
                            "roles": [{
                                "name": "headnode",
                                "targetInstanceCount": "2",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            },
                            {
                                "name": "workernode",
                                "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            }]
                        }
                    }
                }],
                "outputs": {
                    "cluster": {
                        "type": "object",
                        "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                    }
                }
            },
            "mode": "incremental",
            "Parameters": {
                "location": {
                    "value": "North Europe"
                },
                "clusterName": {
                    "value": "newclustername"
                },
                "clusterType": {
                    "value": "hadoop"
                },
                "clusterStorageAccountName": {
                    "value": "newstoragename"
                },
                "clusterLoginUserName": {
                    "value": "admin"
                },
                "clusterLoginPassword": {
                    "value": "changeme"
                },
                "sshUserName": {
                    "value": "sshuser"
                },
                "sshPassword": {
                    "value": "changeme"
                }
            }
        }
    }

I dette eksempel bruges i trinnene i dette dokument. Du skal erstatte pladsholder _værdier_ i sektionen __parametre__ i slutningen af dokumentet med de værdier, du vil bruge til din klynge.

##<a name="login-to-your-azure-subscription"></a>Logge på abonnementet Azure

Følg trinnene beskrevet i [oprette forbindelse til et Azure-abonnement fra Azure kommandolinjen (Azure CLI)](../xplat-cli-connect.md) og oprette forbindelse til dit abonnement ved hjælp af den `azure login` kommandoen.

##<a name="create-a-service-principal"></a>Oprette en tjeneste sikkerhedskonto

> [AZURE.NOTE] Følgende er en forkortet version af oplysningerne i sektionen _Godkend service vigtigste med en adgangskode - Azure CLI_ af [godkendelse af en tjeneste sikkerhedskonto med Azure ressourcestyring](../resource-group-authenticate-service-principal.md#authenticate-service-principal-with-password---azure-cli) dokumentet. Disse trin Opret en ny tjeneste ydelsen, der kan bruges til at godkende REST-API anmodningerne bruges til at oprette Azure ressourcer som en HDInsight klynge.

1. Fra kommandoprompten, terminal session, eller shell, skal du bruge følgende kommando til at få vist dine Azure abonnementer.

        azure account list
        
    Vælg det abonnement, du vil bruge, og notér i kolonnen __Id__ på listen. Dette er __abonnement-ID__ og bruges i de fleste af trinnene i dette dokument.

2. Oprette et nyt program i Azure Active Directory.

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    Erstat værdier for den `--name`, `--home-page`, og `--identifier-uris` med dine egne værdier. Angive en adgangskode til den nye Active Directory-post.
    
    > [AZURE.NOTE] Da du opretter dette program til godkendelse gennem en tjeneste sikkerhedskonto den `--home-page` og `--identifier-uris` værdier behøver ikke at referere til en faktisk webside hostes på internettet. de skal kun være entydige URI'er.
    
    Data, der returneres, gemme værdien __sikkerhedskontrol__ .
    
        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK
    
3. Oprette en tjeneste ydelsen ved hjælp af værdien __sikkerhedskontrol__ returneres tidligere.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
     Data, der returneres, gemme værdien __Objekt-Id__ .
     
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
4. Tildele rollen __ejer__ til tjenesten ydelsen ved hjælp af værdien __Objekt-ID__ returneres tidligere. Du skal også bruge __abonnement-ID__ , du har hentet tidligere.
    
        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Owner -c /subscriptions/{SubscriptionID}/
        
    Når denne kommando er fuldført, har tjenesten vigtigste nu ejer adgang til det angivne abonnement-ID.

##<a name="get-an-authentication-token"></a>Få et godkendelse token

1. Brug følgende fremgangsmåde til at finde __Lejer-ID__ for dit abonnement.

        azure account show -s <subscription ID>
        
    Find __Lejer ID__fra data, der returneres.
    
        info:    Executing command account show
        data:    Name                        : MyAzureAccount
        data:    ID                          : 45a1014d-0f27-25d2-b838-b8f373d6d52e
        data:    State                       : Enabled
        data:    Tenant ID                   : 22f988bf-56f1-41af-91ab-3d7cd011db47
        data:    Is Default                  : true
        data:    Environment                 : AzureCloud
        data:    Has Certificate             : No
        data:    Has Access Token            : Yes
        data:    User name                   : myname@contoso.org
        data:    
        info:    account show command OK

2. Oprette et nyt id ved hjælp af Azure REST-API.

        curl -X "POST" "https://login.microsoftonline.com/TenantID/oauth2/token" \
        -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
        -H "Content-Type: application/x-www-form-urlencoded" \
        --data-urlencode "client_id=AppID" \
        --data-urlencode "grant_type=client_credentials" \
        --data-urlencode "client_secret=password" \
        --data-urlencode "resource=https://management.azure.com/"
    
    Erstatte __TenantID__, __sikkerhedskontrol__og __din adgangskode__ med de værdier, der fås eller tidligere har brugt.

    Hvis mødeindkaldelsen er gået igennem, får du respons 200 serie og svar brødteksten indeholder et JSON-dokument.

    JSON-dokumentet, der returneres af denne anmodning skal indeholde et element med navnet __access_token__; Værdien af dette element er adgangstoken, du skal bruge til godkendelse af anmodninger, der bruges i de næste afsnit i dette dokument.
    
        {
            "token_type":"Bearer",
            "expires_in":"3599",
            "expires_on":"1463409994",
            "not_before":"1463406094",
            "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
        }

##<a name="create-a-resource-group"></a>Oprette en ressourcegruppe

Brug følgende fremgangsmåde til at oprette en ny ressourcegruppe. Du skal oprette gruppen først, før du kan oprette ressourcer som HDInsight klynge. 

* Erstat __SubscriptionID__ med det abonnement-ID, der er modtaget under oprettelse af hovedstolen tjeneste.
* Erstat __AccessToken__ med adgangstoken modtaget i ovenstående trin.
* Erstat __DataCenterLocation__ med et datacenter, du vil oprette ressourcegruppe og ressourcer, i. For eksempel "Syd Central os". 
* Erstatte __ResourceGroupName__ med det navn, du vil bruge til denne gruppe:

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName?api-version=2015-01-01" \
    -H "Authorization: Bearer AccessToken" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DataCenterLocation"
}'
```

Hvis mødeindkaldelsen er gået igennem, får du respons 200 serie og svar brødteksten indeholder et JSON-dokument, der indeholder oplysninger om gruppen. Den `"provisioningState"` elementet skal indeholde en værdi på `"Succeeded"`.

##<a name="create-a-deployment"></a>Oprette en installation

Brug følgende til at installere klyngekonfigurationen (skabelon og parameter værdier,) til ressourcegruppen.

* Erstatte __SubscriptionID__ og __AccessToken__ med de værdier, der tidligere har brugt. 
* Erstat __ResourceGroupName__ med ressource gruppenavn, du oprettede i forrige afsnit.
* Erstatte __DeploymentName__ med det navn, du vil bruge til denne installation.

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [AZURE.NOTE] Hvis du har gemt dokumentet JSON med skabelonen og parametre til en fil, kan du bruge følgende i stedet for `-d "{ template and parameters}"`:
>
> `--data-binary "@/path/to/file.json"`

Hvis mødeindkaldelsen er gået igennem, får du respons 200 serie og svar brødteksten indeholder et JSON-dokument, der indeholder oplysninger om handlingen installation.

> [AZURE.IMPORTANT] Bemærk, at installationen er blevet sendt, men har ikke fuldført på nuværende tidspunkt. Det kan tage flere minutter, som regel omkring 15, at fuldføre installationen.

##<a name="check-the-status-of-a-deployment"></a>Kontrollere status for en installation

Hvis du vil kontrollere status for installationen, skal du bruge følgende:

* Erstatte __SubscriptionID__ og __AccessToken__ med de værdier, der tidligere har brugt. 
* Erstat __ResourceGroupName__ med ressource gruppenavn, du oprettede i forrige afsnit.

```
curl -X "GET" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json"
```

Dette vil returnere oplysninger et JSON-dokument, der indeholder oplysninger om handlingen installation. Den `"provisioningState"` elementet skal indeholde status for installationen Hvis det indeholder en værdi af `"Succeeded"`, og derefter installationen er fuldført. På dette tidspunkt skal din klynge være tilgængelig til brug.

##<a name="next-steps"></a>Næste trin

Nu hvor du har oprettet en HDInsight klynge, kan du bruge følgende til at få mere for at vide om at arbejde med din klynge. 

###<a name="hadoop-clusters"></a>Hadoop klynger

* [Bruge Hive med HDInsight](hdinsight-use-hive.md)
* [Brug gris med HDInsight](hdinsight-use-pig.md)
* [Bruge MapReduce med HDInsight](hdinsight-use-mapreduce.md)

###<a name="hbase-clusters"></a>HBase klynger

* [Komme i gang med HBase på HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Udvikle Java-programmer til HBase på HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###<a name="storm-clusters"></a>Storm klynger

* [Udvikle Java topologier for Storm på HDInsight](hdinsight-storm-develop-java-topology.md)
* [Bruge Python komponenter i Storm på HDInsight](hdinsight-storm-develop-python-topology.md)
* [Installere og overvåge topologier med Storm på HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)
