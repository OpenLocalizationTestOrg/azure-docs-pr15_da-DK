<properties
    pageTitle="Håndtering af tilstand i ressourcestyring skabeloner | Microsoft Azure"
    description="Viser anbefalede fremgangsmåder til brug af komplekse objekter dele tilstand data med Azure ressourcestyring skabeloner og sammenkædede skabeloner"
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="tomfitz"/>

# <a name="sharing-state-in-azure-resource-manager-templates"></a>Deling af tilstand i Azure ressourcestyring skabeloner

Dette emne viser bedste fremgangsmåder til at administrere og dele tilstand inden for skabeloner. Parametre og variabler, der vises i dette emne er eksempler på typen af objekter, du kan definere nemt organisere dine krav til installation. Du kan implementere din egen objekter med egenskabsværdier, der giver mening for dit miljø fra disse eksempler.

Dette emne er en del af en større hvidbog. Download [verden klasse ressource Manager skabeloner overvejelser og dokumenterede fremgangsmåder](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf)for at læse den fulde papir.


## <a name="provide-standard-configuration-settings"></a>Angive standard konfigurationsindstillinger

I stedet for tilbyder en skabelon, der giver samlede fleksibilitet og utallige variationer, er et almindelige mønster at tilvejebringe et udvalg af kendte konfigurationer. Det betyder, kan brugerne vælge standardstørrelser t-shirt som sandkassetilstand, lille, mellemstore og store. Andre eksempler på en t-shirt størrelser er produkttilbud, som community edition eller enterprise edition. I andre tilfælde kan det være arbejdsbelastningen-specifikke konfigurationer af en teknologi – som kort reducere eller ingen sql.

Du kan oprette variabler, der indeholder samlinger af data, nogle gange kaldet "egenskaben forsendelser" og bruge disse data til at drive angivelsen ressource i skabelonen med komplekse objekter. Denne fremgangsmåde giver god, kendt konfigurationer af forskellige størrelser, der er forudkonfigureret til kunder. Uden kendte konfigurationer, skal brugere af skabelonen bestemme klynge sidetilpasning på egen hånd, angive platform ressourcebegrænsninger og foretage beregninger for at identificere den resulterende opdeling af lagerplads konti og andre ressourcer (på grund af klynge størrelse og ressource begrænsninger). Ud over at foretage en bedre oplevelse for kunden, et par kendte konfigurationer er nemmere at understøtter og kan hjælpe dig med at levere et højere niveau af tæthed.

I følgende eksempel viser, hvordan du definerer variabler, der indeholder komplekse objekter til at repræsentere samlinger af data. Samlingerne definere værdier, der bruges til virtuelt størrelse, netværksindstillinger, operativsystemindstillinger og indstillinger for tilgængelighed.

    "variables": {
      "tshirtSize": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",
      "tshirtSizeSmall": {
        "vmSize": "Standard_A1",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
        "vmCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 1,
          "pool": "db",
          "map": [0,0],
          "jumpbox": 0
        }
      },
      "tshirtSizeMedium": {
        "vmSize": "Standard_A3",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-8disk-resources.json')]",
        "vmCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 2,
          "pool": "db",
          "map": [0,1],
          "jumpbox": 0
        }
      },
      "tshirtSizeLarge": {
        "vmSize": "Standard_A4",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
        "vmCount": 3,
        "slaveCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 2,
          "pool": "db",
          "map": [0,1,1],
          "jumpbox": 0
        }
      },
      "osSettings": {
        "scripts": [
          "[concat(variables('templateBaseUrl'), 'install_postgresql.sh')]",
          "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
        ],
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "14.04.2-LTS",
          "version": "latest"
        }
      },
      "networkSettings": {
        "vnetName": "[parameters('virtualNetworkName')]",
        "addressPrefix": "10.0.0.0/16",
        "subnets": {
          "dmz": {
            "name": "dmz",
            "prefix": "10.0.0.0/24",
            "vnet": "[parameters('virtualNetworkName')]"
          },
          "data": {
            "name": "data",
            "prefix": "10.0.1.0/24",
            "vnet": "[parameters('virtualNetworkName')]"
          }
        }
      },
      "availabilitySetSettings": {
        "name": "pgsqlAvailabilitySet",
        "fdCount": 3,
        "udCount": 5
      }
    }

Bemærk, at variablen **tshirtSize** sammenkæder den t-shirt størrelse, du har angivet via en parameter (**lille**, **Medium**, **stor**) til tekst **tshirtSize**. Du kan bruge denne variabel til at hente den tilknyttede komplekse objektvariabel for den pågældende t-shirt størrelse.

Du kan derefter henvise til disse variabler senere i skabelonen. Muligheden for at henvise til navngivne variabler og deres egenskaber forenkler syntaksen skabelon, og gør det nemt at forstå kontekst. I følgende eksempel definerer en ressource til at udrulle ved hjælp af de objekter, der er vist tidligere til at angive værdier. For eksempel størrelsen på VM angives ved at hente værdien for `variables('tshirtSize').vmSize` mens værdien for diskstørrelsen er hentet fra `variables('tshirtSize').diskSize`. Desuden URI-adressen for en sammenkædet skabelon er konfigureret med værdien for `variables('tshirtSize').vmTemplate`.

    "name": "master-node",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared')]"
    ],
    "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[variables('tshirtSize').vmTemplate]",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "value": "[parameters('adminPassword')]"
          },
          "replicatorPassword": {
            "value": "[parameters('replicatorPassword')]"
          },
          "osSettings": {
            "value": "[variables('osSettings')]"
          },
          "subnet": {
            "value": "[variables('networkSettings').subnets.data]"
          },
          "commonSettings": {
            "value": {
              "region": "[parameters('region')]",
              "adminUsername": "[parameters('adminUsername')]",
              "namespace": "ms"
            }
          },
          "storageSettings": {
            "value":"[variables('tshirtSize').storage]"
          },
          "machineSettings": {
            "value": {
              "vmSize": "[variables('tshirtSize').vmSize]",
              "diskSize": "[variables('tshirtSize').diskSize]",
              "vmCount": 1,
              "availabilitySet": "[variables('availabilitySetSettings').name]"
            }
          },
          "masterIpAddress": {
            "value": "0"
          },
          "dbType": {
            "value": "MASTER"
          }
        }
      }
    }

## <a name="pass-state-to-a-template"></a>Overføre tilstand til en skabelon

Du kan dele tilstand til en skabelon til parametre, du angiver direkte under installationen.

I følgende tabel vises ofte anvendte parametre i skabeloner.

Navn | Værdi | Beskrivelse
---- | ----- | -----------
placering    | En streng, fra en begrænset liste over Azure områder   | Den placering, hvor ressourcerne, der er installeret.
storageAccountNamePrefix    | Streng    | Entydigt DNS-navn til kontoen lagerplads, hvor den VM diske er placeret
Domænenavn  | Streng    | Domænenavn af offentligt tilgængelige jumpbox VM i formatet: **{domainName}. {} placeringen}.cloudapp.com** For eksempel: **mydomainname.westus.cloudapp.azure.com**
adminUsername   | Streng    | Brugernavn for FOS
adminPassword   | Streng    | Adgangskoden til FOS
tshirtSize  | Streng fra en begrænset liste over tilbydes t-shirt størrelser   | Størrelsen på navngivne skala at blive klargjort. For eksempel "Lille", "Medium", "Store"
virtualNetworkName  | Streng    | Navnet på det virtuelle netværk, som forbrugeren ønsker at bruge.
enableJumpbox   | En streng, fra en begrænset liste (aktiveret/deaktiveret) | Parameter, der identificerer, om du vil aktivere en jumpbox for miljøet. Værdier: "aktiveret", "deaktiveret"

Parameteren **tshirtSize** bruges i det forrige afsnit defineres som:

    "parameters": {
      "tshirtSize": {
        "type": "string",
        "defaultValue": "Small",
        "allowedValues": [
          "Small",
          "Medium",
          "Large"
        ],
        "metadata": {
          "Description": "T-shirt size of the MongoDB deployment"
        }
      }
    }


## <a name="pass-state-to-linked-templates"></a>Overføre tilstand til sammenkædede skabeloner

Når du opretter forbindelse til sammenkædede skabeloner, du ofte bruge en blanding af statisk og genereres variabler.

### <a name="static-variables"></a>Statiske variabler

Statiske variabler bruges ofte til at levere grundlæggende værdier, som URL-adresser, der bruges i en skabelon.

I den følgende skabelon uddrag `templateBaseUrl` angiver rodplaceringen til skabelonen i GitHub. Den næste linje opbygger en ny variabel `sharedTemplateUrl` , der sammenkæder den grundlæggende URL-adresse med kendte navnet på skabelonen delte ressourcer. Under linjen, en kompleks objektvariabel bruges til at gemme en t-shirt størrelse, hvor den grundlæggende URL-adresse er kædet sammen med den kendte konfiguration skabelonplacering og gemmes i den `vmTemplate` egenskab.

Fordelen ved denne fremgangsmåde er, hvis skabelon placeringen ændres, skal du kun vil ændre variablen statisk på ét sted, hvor overfører dem i hele de sammenkædede skabeloner.

    "variables": {
      "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
      "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
      "tshirtSizeSmall": {
        "vmSize": "Standard_A1",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
        "vmCount": 2,
        "slaveCount": 1,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 1,
          "pool": "db",
          "map": [0,0],
          "jumpbox": 0
        }
      }
    }

### <a name="generated-variables"></a>Oprettede variabler

Ud over statiske variabler genereres adskillige variabler dynamisk. I dette afsnit angives nogle af de almindelige typer genererede variabler.

#### <a name="tshirtsize"></a>tshirtSize

Du allerede kender, denne genererede variabel fra ovenstående eksempler.

#### <a name="networksettings"></a>networkSettings

I en kapacitet, muligheden for eller til slut relateret løsning skabelon opretter de sammenkædede skabeloner typisk ressourcer, der findes på et netværk. En enkel metode er at bruge en kompleks objekt til at gemme indstillingerne for netværk og overføre dem til sammenkædede skabeloner.

Et eksempel på kommunikere netværksindstillinger kan ses nedenfor.

    "networkSettings": {
      "vnetName": "[parameters('virtualNetworkName')]",
      "addressPrefix": "10.0.0.0/16",
      "subnets": {
        "dmz": {
          "name": "dmz",
          "prefix": "10.0.0.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        },
        "data": {
          "name": "data",
          "prefix": "10.0.1.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        }
      }
    }

#### <a name="availabilitysettings"></a>availabilitySettings

Ressourcer, der er oprettet i sammenkædede skabeloner er ofte placeret i et sæt tilgængelighed. Navnet på tilgængelighed sættet er angivet i det følgende eksempel og også et domæne og update domain tælle, hvis du vil bruge.

    "availabilitySetSettings": {
      "name": "pgsqlAvailabilitySet",
      "fdCount": 3,
      "udCount": 5
    }

Hvis du har brug for flere sæt tilgængelighed (for eksempel en for master noder) og en anden for data noder, kan du give den et navn som et præfiks, angive flere sæt af tilgængelighed, eller Følg modellen tidligere til oprettelse af en variabel for en bestemt t-shirt størrelse.

#### <a name="storagesettings"></a>storageSettings

Lagerplads detaljer er ofte delt med sammenkædede skabeloner. I eksemplet herunder et *storageSettings* objekt, skal du finde oplysninger om navnene på konto og beholder lagerplads.

    "storageSettings": {
        "vhdStorageAccountName": "[parameters('storageAccountName')]",
        "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
        "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]"
    }

#### <a name="ossettings"></a>osSettings

Med sammenkædede skabeloner skal du overføre-indstillingerne for operativsystemet til forskellige typer af knuder på tværs af forskellige kendte konfiguration typer. En kompleks objekt er en nem måde at gemme og dele oplysninger om operativsystemet og også gør det nemmere at understøtte flere operativsystem valgmuligheder til installation.

I følgende eksempel viser et objekt til *osSettings*:

    "osSettings": {
      "imageReference": {
        "publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "14.04.2-LTS",
        "version": "latest"
      }
    }

#### <a name="machinesettings"></a>machineSettings

En genererede variabel *machineSettings* er en kompleks objekter, der indeholder en blanding af core variabler til oprettelse af en VM. Variablerne omfatter både administratorens brugernavn og din adgangskode, et præfiks for VM navne og en operativsystem billedreference.

    "machineSettings": {
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "machineNamePrefix": "mongodb-",
        "osImageReference": {
            "publisher": "[variables('osFamilySpec').imagePublisher]",
            "offer": "[variables('osFamilySpec').imageOffer]",
            "sku": "[variables('osFamilySpec').imageSKU]",
            "version": "latest"
        }
    },

Bemærk denne *osImageReference* henter værdierne fra den *osSettings* variabel, der er defineret i den primære skabelon. Det betyder, at du kan nemt ændre operativsystemet til en VM – helt eller baseret på indstillingen af en skabelon forbruger.

#### <a name="vmscripts"></a>vmScripts

Objektet *vmScripts* indeholder oplysninger om at hente og udføre på en VM-forekomst, herunder uden for og i referencer scripts. Uden for omfatter referencer infrastrukturen. Indvendige referencer medtager de installerede programmer, der er installeret og konfiguration.

Du kan bruge egenskaben *scriptsToDownload* med listen over scriptene til at hente til VM. Dette objekt indeholder også referencer til kommandolinjeargumenter for forskellige typer handlinger. Disse handlinger omfatter afholde standardinstallation for hver enkelt node, en installation, der afspilles, når alle noder er installeret og eventuelle ekstra scripts, som er specifikke for en bestemt skabelon.

I dette eksempel er fra en skabelon, der bruges til at installere MongoDB, der kræver en ende bestemmer til at levere høj tilgængelighed. *ArbiterNodeInstallCommand* er blevet føjet til at installere den ende bestemmer *vmScripts* .

Sektionen variabler finder du de variabler, der definerer den specifikke tekst for at køre scriptet med de korrekte værdier.

    "vmScripts": {
        "scriptsToDownload": [
            "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
            "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
        ],
        "regularNodeInstallCommand": "[variables('installCommand')]",
        "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
        "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
    },


## <a name="return-state-from-a-template"></a>Returnere tilstand fra en skabelon

Ikke kun kan du overføre data til en skabelon, du kan også dele data tilbage til skabelonen opkald. Du kan angive nøgle/værdi-par, der kan benyttes af skabelonen kilde i afsnittet **skriver** i en sammenkædet skabelon.

I følgende eksempel viser, hvordan til at overføre den private IP-adresse, der er oprettet i en sammenkædet skabelon.

    "outputs": {
        "masterip": {
            "value": "[reference(concat(variables('nicName'),0)).ipConfigurations[0].properties.privateIPAddress]",
            "type": "string"
         }
    }

Du kan bruge dataene i den primære skabelon med følgende syntaks:

    "[reference('master-node').outputs.masterip.value]"

Du kan bruge dette udtryk i afsnittet output eller afsnittet ressourcer i den primære skabelon. Du kan ikke bruge udtrykket i sektionen variabler, fordi den er afhængig af tilstanden runtime. Brug til at returnere denne værdi fra den primære skabelon:

    "outputs": { 
      "masterIpAddress": {
        "value": "[reference('master-node').outputs.masterip.value]",
        "type": "string"
      }
     
Et eksempel på ved hjælp af afsnittet output af en sammenkædet skabelon til at returnere datadisce for en virtuel maskine, i [oprette forskellige data drev og sikrer en virtuel maskine](resource-group-create-multiple.md#creating-multiple-data-disks-for-a-virtual-machine).

## <a name="define-authentication-settings-for-virtual-machine"></a>Definere godkendelsesindstillinger til virtual machine

Du kan bruge det samme mønster, som er vist tidligere indstillinger for søgekonfiguration til at angive godkendelsesindstillingerne for en virtuel maskine. Du kan oprette en parameter for overførsel i typen godkendelse.

    "parameters": {
      "authenticationType": {
        "allowedValues": [
          "password",
          "sshPublicKey"
        ],
        "defaultValue": "password",
        "metadata": {
          "description": "Authentication type"
        },
        "type": "string"
      }
    }

Du tilføjer variabler for de forskellige godkendelsestyper, og en variabel til at gemme hvilken type bruges til denne installation, der er baseret på værdien af parameteren.

    "variables": {
      "osProfile": "[variables(concat('osProfile', parameters('authenticationType')))]",
      "osProfilepassword": {
        "adminPassword": "[parameters('adminPassword')]",
        "adminUsername": "notused",
        "computerName": "[parameters('vmName')]",
        "customData": "[base64(variables('customData'))]"
      },
      "osProfilesshPublicKey": {
        "adminUsername": "notused",
        "computerName": "[parameters('vmName')]",
        "customData": "[base64(variables('customData'))]",
        "linuxConfiguration": {
          "disablePasswordAuthentication": "true",
          "ssh": {
            "publicKeys": [
              {
                "keyData": "[parameters('sshPublicKey')]",
                "path": "/home/notused/.ssh/authorized_keys"
              }
            ]
          }
        }
      }
    }

Når du definerer den virtuelle maskine, kan du angive **osProfile** til den variabel, du har oprettet.

    {
      "type": "Microsoft.Compute/virtualMachines",
      ...
      "osProfile": "[variables('osProfile')]"
    }


## <a name="next-steps"></a>Næste trin
- For at få mere for at vide om sektionerne i skabelonen, se [Redigering Azure ressourcestyring skabeloner](resource-group-authoring-templates.md)
- For at se de funktioner, der er tilgængelige i en skabelon skal du se [Azure ressourcestyring skabelon funktioner](resource-group-template-functions.md)

