<properties
   pageTitle="Oprette forbindelse til en sikker privat klynge | Microsoft Azure"
   description="I denne artikel beskrives, hvordan til at sikre kommunikationen på enkeltstående eller privat klynge samt mellem klienter og klyngen."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/08/2016"
   ms.author="dkshir"/>

# <a name="secure-a-standalone-cluster-on-windows-using-x509-certificates"></a>Sikre en enkeltstående klynge på Windows ved hjælp af x.509-certifikater

I denne artikel beskrives, hvordan til at sikre kommunikationen mellem de forskellige noder af din enkeltstående Windows klynge, samt hvordan til at godkende klienter opretter forbindelse til denne klynge ved hjælp af X.509 certifikater. Dette sikrer, at kun godkendte brugere kan få adgang til klynge, de installerede programmer og udføre administrationsopgaver.  Certifikatsikkerhed skal aktiveres på klyngen, når klyngen oprettes.  

Du kan finde flere oplysninger om klynge sikkerhed som node-til-node sikkerhed, klient-til-node og rollebaseret adgangskontrol, [klynge sikkerhed scenarier](service-fabric-cluster-security.md).

## <a name="which-certificates-will-you-need"></a>Hvilke certifikater har du brug for?

For at starte med, [Hent pakken enkeltstående klynge](service-fabric-cluster-creation-for-windows-server.md#downloadpackage) til en af noderne i din klynge. Du kan finde en **ClusterConfig.X509.MultiMachine.json** fil i pakken hentede. Åbn fil, og gennemgå afsnittet om **sikkerhed** i afsnittet **Egenskaber** :

    "security": {
        "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        "CertificateInformation": {
            "ClusterCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ServerCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ClientCertificateThumbprints": [{
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            }, {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }],
            "ClientCertificateCommonNames": [{
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint" : "[Thumbprint]",
                "IsAdmin": true
            }]
            "HttpApplicationGatewayCertificate":{
                "Thumbprint": "[Thumbprint]",
                "X509StoreName": "My"
            }
        }
    }

I dette afsnit beskrives de certifikater, som du har brug for til sikring af din enkeltstående Windows klynge. Angiv værdier for **ClusterCredentialType** og **ServerCredentialType** til *X509*for at aktivere certifikat-baseret sikkerhed.

>[AZURE.NOTE] En [miniature](https://en.wikipedia.org/wiki/Public_key_fingerprint) er den primære identitet af et certifikat. Læs, [hvordan du kan hente miniature af et certifikat](https://msdn.microsoft.com/library/ms734695.aspx) til at finde ud af miniature af certifikater, som du opretter.

I følgende tabel vises de certifikater, som skal du på din klyngeinstallation:

|**CertificateInformation indstilling**|**Beskrivelse**|
|-----------------------|--------------------------|
|ClusterCertificate|Dette certifikat er kræves for at sikre kommunikationen mellem noder på en klynge. Du kan bruge to forskellige certifikater, en primær og sekundær til opgradering. Angive miniature for det primære certifikat i sektionen **miniature** og adressen på sekundært i **ThumbprintSecondary** variabler.|
|ServerCertificate|Dette certifikat præsenteres til klienten, når den forsøger at oprette forbindelse til denne klynge. Du kan vælge at bruge det samme certifikat til *ClusterCertificate* og *ServerCertificate*fordel. Du kan bruge to forskellige servercertifikater, en primær og sekundær til opgradering. Angive miniature for det primære certifikat i sektionen **miniature** og adressen på sekundært i **ThumbprintSecondary** variabler. |
|ClientCertificateThumbprints|Dette er et sæt af certifikater, du vil installere på de godkendte klienter. Du kan have et antal forskellige klientcertifikater, der er installeret på de computere, du vil give adgang til klyngen. Angiv miniature af hvert certifikat i variablen **CertificateThumbprint** . Hvis du indstiller **IsAdmin** til *Sand*, kan derefter klienten med dette certifikat, der er installeret på den gøre administrator management aktiviteter på klyngen. Hvis **IsAdmin** er *Falsk*, kan klienten med dette certifikat kun udføre de handlinger, der er tilladt for brugeradgangsrettigheder, typisk skrivebeskyttet. Læs mere om roller [Rollebaseret adgangskontrol (RBAC)](service-fabric-cluster-security.md/#role-based-access-control-rbac)  |
|ClientCertificateCommonNames|Angive almindelige navnet på den første klientcertifikat for **CertificateCommonName**. **CertificateIssuerThumbprint** er miniature til udsteder af dette certifikat. Læs [arbejde med certifikater](https://msdn.microsoft.com/library/ms731899.aspx) vil vide mere om almindelige navne og udstederen.|
|HttpApplicationGatewayCertificate|Dette er et valgfrit certifikat, der kan være angivet, hvis du vil sikre din Http Application Gateway. Sørg for, at reverseProxyEndpointPort er angivet i nodeTypes, hvis du bruger dette certifikat.|

Her er eksempel klynge konfiguration, hvor klynge, Server og klient certifikater har fået.

 ```
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2016-09-26",
    "nodes": [{
        "nodeName": "vm0",
        "metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
      "nodeName": "vm1",
            "metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
      "iPAddress": "10.7.0.6",
            "metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
        "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
        }
        "security": {
            "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificate": {
                    "Thumbprint": "a8 13 67 58 f4 ab 89 62 af 2b f3 f2 79 21 be 1d f6 7f 43 26",
                    "X509StoreName": "My"
                },
                "ServerCertificate": {
                    "Thumbprint": "a8 13 67 58 f4 ab 89 62 af 2b f3 f2 79 21 be 1d f6 7f 43 26",
                    "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpoint": "19001",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## <a name="aquire-the-x509-certificates"></a>Få X.509 certifikater
For at sikre kommunikationen inden for klyngen, skal du først får x.509-certifikater til din klyngenoder. Desuden, hvis du vil begrænse forbindelse til denne klynge til autoriserede computere/brugere, skal du kan hente og installere certifikater til på klientcomputere.

Du skal bruge et [Certifikat nøglecenter (CA)](https://en.wikipedia.org/wiki/Certificate_authority) for klynger, der kører fremstilling arbejdsbelastninger, logget x.509-certifikat til at sikre klyngen. Få at vide om hentning af disse certifikater, gå til [Sådan: få et certifikat](http://msdn.microsoft.com/library/aa702761.aspx).

Til klynger, som du bruger til testformål, kan du vælge at bruge et selvsigneret certifikat.

## <a name="optional-create-a-self-signed-certificate"></a>Valgfrit: Oprette et selvsigneret certifikat
En metode til at oprette et selvsigneret certifikat, der kan sikres korrekt er at bruge *CertSetup.ps1* scriptet i mappen Service-strukturen SDK i mappen *C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure*. Redigere denne fil, og brug dette til at oprette et certifikat med et passende navn.

Nu eksportere certifikatet til en PFX-fil med en beskyttet adgangskode. Du skal først hente miniature certifikatets. Kør programmet certmgr.exe. Gå til mappen **Lokale Computer\Personal** og finde det certifikat, du lige har oprettet. Dobbeltklik på certifikatet for at åbne det, skal du vælge fanen *Detaljer* og Rul ned til feltet *miniature* . Kopiér miniatureværdien til PowerShell-kommando nedenfor, fjerne mellemrummene.  Ændre værdien *$pswd* til en sikker adgangskode er egnet til at beskytte den og køre PowerShell:

```   
$pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
```

For at se detaljerne for et certifikat, der er installeret på computeren kan du køre følgende PowerShell-kommando:

```
$cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
Write-Host $cert.ToString($true)
```

Du kan også hvis du har et Azure-abonnement, skal du følge sektionen [Tilføj certifikater til tasten samling](service-fabric-cluster-creation-via-arm.md#add-certificate-to-key-vault).

## <a name="install-the-certificates"></a>Installere certifikater
Når du har certifikater, kan du installere dem på klyngenoderne. Din noder skal have den nyeste Windows PowerShell 3.x, der er installeret på dem. Du skal gentage disse trin på hver node for både klynge og Server certifikater og en sekundær certifikater.

1. Kopiere .pfx filer til noden.
2. Åbne et vindue som administrator i PowerShell og skrive følgende kommandoer. Erstat *$pswd* med den adgangskode, du brugte til at oprette certifikatet. Erstat *$PfxFilePath* med den fulde sti til .pfx kopieret til denne node.

    ```
    $pswd = "1234"
    $PfcFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```

3. Derefter skal du angive adgangskontrol på dette certifikat, så processen Service struktur, der kører under kontoen Netværkstjeneste, kan bruge den ved at køre følgende script. Angiv miniature af certifikat og "NETVÆRKSTJENESTE" til service-kontoen. Du kan kontrollere, at ACLs på certifikatet er korrekte, ved hjælp af værktøjet certmgr.exe og kigge på Administrer Private nøgler på certifikatet.

    ```
    param
    (
        [Parameter(Position=1, Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [string]$pfxThumbPrint,

        [Parameter(Position=2, Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [string]$serviceAccount
        )

        $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; };

        # Specify the user, the permissions and the permission type
        $permission = "$($serviceAccount)","FullControl","Allow"
        $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission;

        # Location of the machine related keys
        $keyPath = $env:ProgramData + "\Microsoft\Crypto\RSA\MachineKeys\";
        $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName;
        $keyFullPath = $keyPath + $keyName;

        # Get the current acl of the private key
        $acl = (Get-Item $keyFullPath).GetAccessControl('Access')

        # Add the new ace to the acl of the private key
        $acl.SetAccessRule($accessRule);

        # Write back the new acl
        Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop

        #Observe the access rights currently assigned to this certificate.
        get-acl $keyFullPath| fl
        ```

4. Repeat the steps above for each server certificate. You can also use these steps to install the client certificates on the machines that you want to allow access to the cluster.

## Create the secure cluster
After configuring the **security** section of the **ClusterConfig.X509.MultiMachine.json** file, you can proceed to [Create your cluster](service-fabric-cluster-creation-for-windows-server.md#createcluster) section to configure the nodes and create the standalone cluster. Remember to use the **ClusterConfig.X509.MultiMachine.json** file while creating the cluster. For example, your command might look like the following:

```
.\CreateServiceFabricCluster.ps1 - ClusterConfigFilePath.\ClusterConfig.X509.MultiMachine.json - MicrosoftServiceFabricCabFilePath.\MicrosoftAzureServiceFabric.cab - AcceptEULA $true
```

Once you have the secure standalone Windows cluster successfully running, and have setup the authenticated clients to connect to it, follow the section [Connect to a secure cluster using PowerShell](service-fabric-connect-to-secure-cluster.md#connectsecurecluster) to connect to it. For example:

```
Oprette forbindelse ServiceFabricCluster - ConnectionEndpoint 10.7.0.4:19000 - KeepAliveIntervalInSec 10 - X509Credential - ServerCertThumbprint 057b9544a6f2733e0c8d3a60013a58948213f551 - FindType FindByThumbprint - FindValue 057b9544a6f2733e0c8d3a60013a58948213f551 - StoreLocation CurrentUser - StoreName min
```

If you are logged on to one of the machines in the cluster, since this already has the certificate installed locally you can simply run the Powershell command to connect to the cluster and show a list of nodes:

```
Oprette forbindelse ServiceFabricCluster Get-ServiceFabricNode
```
To remove the cluster call the following command:

```
.\RemoveServiceFabricCluster.ps1 - ClusterConfigFilePath.\ClusterConfig.X509.MultiMachine.json - MicrosoftServiceFabricCabFilePath.\MicrosoftAzureServiceFabric.cab
```
