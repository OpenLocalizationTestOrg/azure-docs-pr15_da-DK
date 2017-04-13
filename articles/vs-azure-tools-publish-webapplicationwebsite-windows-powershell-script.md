<properties
   pageTitle="Publicere-WebApplicationWebSite (Windows PowerShell-script) | Microsoft Azure"
   description="Lær at udgive et webprojekt, til et websted, Azure. Dette script opretter de påkrævede ressourcer i abonnementet Azure, hvis de ikke findes."
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

# <a name="publish-webapplicationwebsite-windows-powershell-script"></a>Publicere-WebApplicationWebSite (Windows PowerShell-script)

##<a name="syntax"></a>Syntaksen for

Udgiver et webprojekt til et websted, Azure. Scriptet opretter de påkrævede ressourcer i abonnementet Azure, hvis de ikke findes.

    Publish-WebApplicationWebSite
    –Configuration <configuration>
    -SubscriptionName <subscriptionName>
    -WebDeployPackage <packageName>
    -DatabaseServerPassword @{Name = "name"; Password = "password"}
    -SendHostMessagesToOutput
    -Verbose


## <a name="configuration"></a>Konfiguration

Stien til filen JSON konfiguration, der beskriver detaljerne for installationen.

|Parameter|Standardværdi|
|---|---|
|Aliasser|ingen|
|Påkrævet?|SAND|
|Placering|med navnet|
|Standardværdi|ingen|
|Acceptér pipeline-oplysninger?|FALSK|
|Acceptere jokertegn?|FALSK|

## <a name="subscriptionname"></a>SubscriptionName

Navnet på det Azure abonnement, du vil oprette webstedet i.

|Parameter|Standardværdi|
|---|---|
|Aliasser|ingen|
|Påkrævet?|FALSK|
|Placering|med navnet|
|Standardværdi|ingen|
|Acceptér pipeline-oplysninger?|FALSK|
|Acceptere jokertegn?|FALSK|

## <a name="webdeploypackage"></a>WebDeployPackage

Stien til web-installationspakken publicere til webstedet. Du kan oprette denne pakke ved hjælp af guiden Udgiv websted i Visual Studio. Se [Introduktion til Azure Cloud Services og ASP.NET](http://go.microsoft.com/fwlink/p/?LinkID=623089)kan finde flere oplysninger.

|Parameter|Standardværdi|
|---|---|
|Aliasser|ingen|
|Påkrævet?|FALSK|
|Placering|med navnet|
|Standardværdi|ingen|
|Acceptér pipeline-oplysninger?|FALSK|
|Acceptere jokertegn?|FALSK|

## <a name="databaseserverpassword"></a>DatabaseServerPassword

Brugernavnet og adgangskoden til SQL-databasen i Azure.

|Parameter|Standardværdi|
|---|---|
|Aliasser|ingen|
|Påkrævet?|FALSK|
|Placering|med navnet|
|Standardværdi|ingen|
|Acceptér pipeline-oplysninger?|FALSK|
|Acceptere jokertegn?|FALSK|

## <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput

Hvis sand, meddelelser Udskriv fra scriptet til output strømmen.

|Parameter|Standardværdi|
|---|---|
|Aliasser|ingen|
|Påkrævet?|FALSK|
|Placering|med navnet|
|Standardværdi|FALSK|
|Acceptér pipeline-oplysninger?|FALSK|
|Acceptere jokertegn?|FALSK|

## <a name="remarks"></a>Bemærkninger

Finde en fuldstændig beskrivelse af, hvordan du bruger scriptet til at oprette se Udviklingscenter og Test-miljøer [Ved hjælp af Windows PowerShell-Scripts på Udgiv på Udviklingscenter og testmiljøer](vs-azure-tools-publishing-using-powershell-scripts.md).

Konfigurationsfil JSON angiver oplysninger om, hvad der skal installeres. Den indeholder de oplysninger, du angav, da du oprettede i projektet, som navn og brugernavn for det offentlige websted. Den indeholder også databasen til klargøring, hvis en hvilken som helst. Følgende kode viser et eksempel JSON konfigurationsfil:

    {
        "environmentSettings": {
            "webSite": {
                "name": "WebApplication10554",
                "location": "West US"
            },
            "databases": [
                {
                    "connectionStringName": "DefaultConnection",
                    "databaseName": "WebApplication10554_db",
                    "serverName": "iss00brc88",
                    "user": "sqluser2",
                    "password": "",
                    "edition": "",
                    "size": "",
                    "collation": "",
                    "location": "West US"
                }
            ]
        }
    }

Du kan redigere filen JSON konfiguration for at ændre, hvad er installeret. En websted sektion er påkrævet, men sektionen database er valgfrit.

## <a name="next-steps"></a>Næste trin

Kan finde flere oplysninger under [Udgiv-WebApplicationVM (Windows PowerShell-script)](vs-azure-tools-publish-webapplicationvm.md)
