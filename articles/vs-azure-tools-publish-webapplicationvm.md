<properties
   pageTitle="Publicere WebApplicationVM | Microsoft Azure"
   description="Lær, hvordan du installerer et webprogram til en virtuel maskine. Dette script opretter de påkrævede ressourcer i abonnementet Azure, hvis de ikke findes."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="publish-webapplicationvm-windows-powershell-script"></a>Publicere-WebApplicationVM (Windows PowerShell-script)

Installerer et webprogram til en virtuel maskine. Scriptet opretter de påkrævede ressourcer i abonnementet Azure, hvis de ikke findes.

```
Publish-WebApplicationVM
–Configuration <configuration>
-SubscriptionName <subscriptionName>
-WebDeployPackage <packageName>
-VMPassword @{Name = "name"; Password = "password")
-DatabaseServerPassword @{Name = "name"; Password = "password"}
-SendHostMessagesToOutput
-Verbose
```

### <a name="configuration"></a>Konfiguration

Stien til filen JSON konfiguration, der beskriver detaljerne for installationen.

|Aliasser|ingen|
|---|---|
|Påkrævet?|SAND|
|Placering|med navnet|
|Standardværdi|ingen|
|Acceptér pipeline-oplysninger?|FALSK|
|Acceptere jokertegn?|FALSK|

### <a name="subscriptionname"></a>SubscriptionName

Navnet på det Azure abonnement, hvor du vil oprette den virtuelle maskine.

|Aliasser|ingen|
|---|---|
|Påkrævet?|FALSK|
|Placering|med navnet|
|Standardværdi|Bruger det første abonnement i abonnementsfilen|
|Acceptér pipeline-oplysninger?|FALSK|
|Acceptere jokertegn?|FALSK|

### <a name="webdeploypackage"></a>WebDeployPackage

Stien til web-installationspakken publicere til den virtuelle maskine. Du kan oprette denne pakke ved hjælp af guiden Udgiv websted i Visual Studio. Se [Sådan: oprette en Web-installationspakke i Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx).

|Aliasser|ingen|
|---|---|
|Påkrævet?|FALSK|
|Placering|med navnet|
|Standardværdi|ingen|
|Acceptér pipeline-oplysninger?|FALSK|
|Acceptere jokertegn?|FALSK|

### <a name="allowuntrusted"></a>AllowUntrusted

Hvis sand, tillade brug af certifikater, der ikke er logget af et rodnøglecenter.

|Aliasser|ingen|
|---|---|
|Påkrævet?|FALSK|
|Placering|med navnet|
|Standardværdi|FALSK|
|Acceptér pipeline-oplysninger?|FALSK|
|Acceptere jokertegn?|FALSK|

### <a name="vmpassword"></a>VMPassword

Legitimationsoplysninger for den virtuelle computerkonto. Eksempel: - VMPassword @{Name = "admin" Adgangskode = "adgangskode"}

|Aliasser|ingen|
|---|---|
|Påkrævet?|FALSK|
|Placering|med navnet|
|Standardværdi|ingen|
|Acceptér pipeline-oplysninger?|FALSK|
|Acceptere jokertegn?|FALSK|

### <a name="databaseserverpassword"></a>DatabaseServerPassword

Legitimationsoplysninger for SQL-database i Azure. Eksempel: - DatabaseServerPassword @{Name = "admin" Adgangskode = "adgangskode"}

|Aliasser|ingen|
|---|---|
|Påkrævet?|FALSK|
|Placering|med navnet|
|Standardværdi|ingen|
|Acceptér pipeline-oplysninger?|FALSK|
|Acceptere jokertegn?|FALSK|

### <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput

Hvis sand, meddelelser Udskriv fra scriptet til output strømmen.

|Aliasser|ingen|
|---|---|
|Påkrævet?|FALSK|
|Placering|med navnet|
|Standardværdi|FALSK|
|Acceptér pipeline-oplysninger?|FALSK|
|Acceptere jokertegn?|FALSK|

## <a name="remarks"></a>Bemærkninger

Finde en fuldstændig beskrivelse af, hvordan du bruger scriptet til at oprette se Udviklingscenter og Test-miljøer [Ved hjælp af Windows PowerShell-Scripts på Udgiv på Udviklingscenter og testmiljøer](vs-azure-tools-publishing-using-powershell-scripts.md).

Konfigurationsfil JSON angiver oplysninger om, hvad der skal installeres. Den indeholder de oplysninger, du angav, da du oprettede i projektet, som det navn, forbindelse gruppe, Virtuelle billede og størrelsen af den virtuelle maskine. Det også indeholder slutpunkterne på den virtuelle maskine, databaser til at klargøre, hvis en hvilken som helst, og web installation parametre. Følgende kode viser et eksempel JSON konfigurationsfil:

```
{
    "environmentSettings": {
        "cloudService": {
            "name": "myvmname",
            "affinityGroup": "",
            "location": "West US",
            "virtualNetwork": "",
            "subnet": "",
            "availabilitySet": "",
            "virtualMachine": {
                "name": "myvmname",
                "vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201404.01-en.us-127GB.vhd",
                "size": "Small",
                "user": "vmuser1",
                "password": "",
                "enableWebDeployExtension": true,
                "endpoints": [
                    {
                        "name": "Http",
                        "protocol": "TCP",
                        "publicPort": "80",
                        "privatePort": "80"
                    },
                    {
                        "name": "Https",
                        "protocol": "TCP",
                        "publicPort": "443",
                        "privatePort": "443"
                    },
                    {
                        "name": "WebDeploy",
                        "protocol": "TCP",
                        "publicPort": "8172",
                        "privatePort": "8172"
                    },
                    {
                        "name": "Remote Desktop",
                        "protocol": "TCP",
                        "publicPort": "3389",
                        "privatePort": "3389"
                    },
                    {
                        "name": "Powershell",
                        "protocol": "TCP",
                        "publicPort": "5986",
                        "privatePort": "5986"
                    }
                ]
            }
        },
        "databases": [
            {
                "connectionStringName": "",
                "databaseName": "",
                "serverName": "",
                "user": "",
                "password": ""
            }
        ],
        "webDeployParameters": {
            "iisWebApplicationName": "Default Web Site"
        }
    }
}
```

Du kan redigere filen JSON konfiguration for at ændre, hvad der klargøres. Der kræves en virtuel maskine og en skybaseret tjeneste, men sektionen database er valgfrit.
