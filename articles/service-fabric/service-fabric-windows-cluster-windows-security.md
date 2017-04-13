<properties
   pageTitle="Sikre en klynge, der kører på Windows ved hjælp af Windows-sikkerhed | Microsoft Azure"
   description="Få mere at vide, hvordan du konfigurerer node-til-node og klient-til-node sikkerhed på en enkeltstående klynge, der kører på Windows ved hjælp af Windows-sikkerhed."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>


# <a name="secure-a-standalone-cluster-on-windows-using-windows-security"></a>Sikre en enkeltstående klynge på Windows ved hjælp af Windows-sikkerhed

Hvis du vil forhindre uautoriseret adgang til en tjeneste-strukturen klynge skal du sikre, især hvis den har fremstilling arbejdsbelastninger, som kører på den. I denne artikel beskrives, hvordan du konfigurerer node-til-node og klient-til-node sikkerhed ved hjælp af Windows-sikkerhed i filen *ClusterConfig.JSON* og svarer til trinnet Konfigurer sikkerhed i [oprette en enkeltstående klynge kører på Windows](service-fabric-cluster-creation-for-windows-server.md). Du kan finde flere oplysninger om hvordan tjenesten strukturen bruger Windows-sikkerhed, [klynge sikkerhed scenarier](service-fabric-cluster-security.md).

>[AZURE.NOTE]
Du skal overveje valget sikkerhed for node-til-node sikkerhed omhyggeligt, da der er ingen klynge opgradering fra én sikkerhed valg til en anden. Ændre markeringen sikkerhed kræver en fuld klynge Genopbyg.

## <a name="configure-windows-security"></a>Konfigurere Windows-sikkerhed
Eksempel på *ClusterConfig.Windows.JSON* konfigurationsfil overført sammen med [Microsoft.Azure.ServiceFabric.WindowsServer.<version>. ZIP](http://go.microsoft.com/fwlink/?LinkId=730690) enkeltstående klynge pakken indeholder en skabelon til konfiguration af Windows-sikkerhed.  Windows-sikkerhed er konfigureret i sektionen **Egenskaber** :

```
"security": {
            "ClusterCredentialType": "Windows",
            "ServerCredentialType": "Windows",
            "WindowsIdentities": {
        "ClusterIdentity" : "[domain\machinegroup]",
                "ClientIdentities": [{
                    "Identity": "[domain\username]",
                    "IsAdmin": true
                }]
            }
        }
```

|**Denne indstilling**|**Beskrivelse**|
|-----------------------|--------------------------|
|ClusterCredentialType|Windows-sikkerhed er aktiveret ved at angive parameteren **ClusterCredentialType** til *Windows*.|
|ServerCredentialType|Windows-sikkerhed for klienter, der aktiveres ved at angive parameteren **ServerCredentialType** til *Windows*. Dette angiver, at klienter i klyngen, og klyngen sig selv kører i en Active Directory-domæne.|
|WindowsIdentities|Indeholder klynge og klienten identiteter.|
|ClusterIdentity|Konfigurerer node-til-node sikkerhed. En kommasepareret liste over gruppen administrerede tjenestekonti eller maskine navne.|
|ClientIdentities|Konfigurerer klient-til-node sikkerhed. En matrix med klient-brugerkonti.|
|Identitet|Klient identitet, domænebruger et.|
|IsAdmin|SAND angiver, at domænebrugeren har administratoren klientadgang FALSK til bruger client access.|

[Knude til node sikkerhed](service-fabric-cluster-security.md#node-to-node-security) konfigureres ved hjælp af indstillingen ved hjælp af **ClusterIdentity**. For at opbygge tillidsforhold mellem noder, skal de gøres bekendt med hinanden. Dette kan gøres på to forskellige måder: angive den gruppe administrerede tjenestekonto, som indeholder alle noder i klyngen eller domæne node identiteter af alle noder i klyngen. Vi anbefaler ved hjælp af metoden [Gruppe administrerede tjenestekonto (gMSA)](https://technet.microsoft.com/library/hh831782.aspx) , særligt for større klynger (mere end 10 noder) eller klynger, der har sandsynligvis Forøg eller Formindsk.
Denne metode kan knuder der skal tilføjes eller fjernes fra gMSA, uden at der skal ændres til klynge manifestet. Denne fremgangsmåde kræver ikke oprettelse af en domænegruppe, hvortil klynge administratorer har fået tildelt adgangsrettigheder til at tilføje og fjerne medlemmer. Se [Introduktion til gruppen administrerede tjenestekonti](http://technet.microsoft.com/library/jj128431.aspx)kan finde flere oplysninger.

[Klient til node sikkerhed](service-fabric-cluster-security.md#client-to-node-security) er konfigureret til brug af **ClientIdentities**. For at oprette tillid mellem en klient og klyngen, skal du konfigurere klynge kan fortælle dig, hvilken klient identiteter, det kan have tillid til. Dette kan gøres på to forskellige måder: angive, hvilke domæne gruppe brugere, der kan oprette forbindelse eller angive, hvilke domæne node brugere, der kan oprette forbindelse. Tjenesten strukturen understøtter to forskellige access kontrolelementtyper til kunder, der er forbundet til en tjeneste-strukturen klynge: administrator og bruger. Adgangskontrol giver mulighed for klyngeadministratoren for at begrænse adgangen til bestemte typer klyngehandlinger for forskellige grupper af brugere ved at gøre klyngen mere sikker.  Administratorer har fuld adgang til funktioner til administration (herunder læse-og skriveadgang funktioner). Brugere, har som standard kun læseadgang til administrationsfunktioner (for eksempel forespørgsel funktioner), og muligheden for at løse programmer og tjenester.

Følgende eksempel **sikkerhed** afsnit konfigurerer Windows sikkerhed og angiver, at computere i *ServiceFabric/clusterA.contoso.com* er en del af klyngen og at der *CONTOSO\usera* administrator klientadgang:

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
        "IsAdmin": true
        }]
    }
},
```

## <a name="next-steps"></a>Næste trin

Når du har konfigureret Windows sikkerhed i filen *ClusterConfig.JSON* , genoptage oprettelsen af klynge i [oprette en enkeltstående klynge kører på Windows](service-fabric-cluster-creation-for-windows-server.md).

Finde flere oplysninger om, hvordan node-til-node sikkerhed, klient-til-node og rollebaseret adgangskontrol [klynge sikkerhed scenarier](service-fabric-cluster-security.md).

Se [oprette forbindelse til en sikker klynge](service-fabric-connect-to-secure-cluster.md) eksempler på at oprette forbindelse via PowerShell eller FabricClient.
