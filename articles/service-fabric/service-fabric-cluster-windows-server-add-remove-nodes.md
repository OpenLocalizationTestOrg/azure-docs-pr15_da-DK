<properties
   pageTitle="Tilføje eller fjerne noder til en enkeltstående Service-strukturen klynge | Microsoft Azure"
   description="Lær at tilføje eller fjerne noder til en Azure Service strukturen klynge i en fysisk eller virtuel maskine, der kører Windows Server, som kan være et lokalt eller i en hvilken som helst skyen."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/20/2016"
   ms.author="dkshir;chackdan"/>


# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Tilføje eller fjerne noder til en enkeltstående Service-strukturen klynge, der kører på Windows Server

Når du har [oprettet din enkeltstående Service-strukturen klynge på Windows Server-computere](service-fabric-cluster-creation-for-windows-server.md), vil dine forretningsbehov kan blive ændret, så det være nødvendigt at tilføje eller fjerne flere noder til din klynge. I denne artikel indeholder detaljerede trin til at opnå dette mål.


## <a name="add-nodes-to-your-cluster"></a>Føje noder til din klynge

1. Forberede den VM/maskine, du vil føje til din klynge ved at følge trinnene, der er nævnt i afsnittet [forberede computere til de rette forudsætninger for installation af klynge](service-fabric-cluster-creation-for-windows-server.md#preparemachines) .
2. Planlægge, hvilken et domæne og opgradering domæne, du vil tilføje denne VM machine til.
3. Fjernskrivebord (RDP) i den VM/maskine, du vil føje til klyngen.
4. Kopiér eller [hente den separate pakke til tjenesten strukturen for Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) til denne VM/computer og udpakke pakken.
5. Køre Powershell som administrator, og Naviger til placeringen for den udpakket pakke.
6. Køre *AddNode.ps1* Powershell med de parametre, der beskriver den nye node tilføje. Eksemplet herunder tilføjer en ny node kaldet VM5, med type NodeType0, IP-adresse 182.17.34.52 i UD1 og FD1. *ExistingClusterConnectionEndPoint* er en forbindelse slutpunkt for en node allerede findes i den eksisterende klynge. Du kan vælge IP-adressen på *en hvilken som helst* node i klyngen for dette slutpunkt.

```
.\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain FD1 -AcceptEULA

```

## <a name="remove-nodes-from-your-cluster"></a>Fjerne noder fra din klynge

1. Afhængigt af det Reliablity niveau valgt for-klyngen, kan ikke du fjerne n noderne af typen primære node første (3/5/7/9)
2. Køre RemoveNode kommando på en Udviklingscenter klynge understøttes ikke.
2. Fjernskrivebord (RDP) i den VM/maskine, du vil fjerne fra klyngen.
2. Kopiér eller [hente den separate pakke til tjenesten strukturen for Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) og udpakke pakken til denne VM/computer.
3. Køre Powershell som administrator, og Naviger til placeringen for den udpakket pakke.
4. Køre *RemoveNode.ps1* i PowerShell. I eksemplet nedenfor fjerner den aktuelle node fra klyngen. *ExistingClientConnectionEndpoint* er en klient forbindelse slutpunkt for en node, som forbliver i klyngen. Vælg IP-adressen og port slutpunkt for *en hvilken som helst* **anden node** i klyngen. Denne **anden node** opdateres også klyngekonfigurationen af den fjernede node. 

```
.\RemoveNode.ps1 -ExistingClientConnectionEndpoint 182.17.34.50:19000
```

Vær opmærksom på, når du fjerner en node, det kan vises som værende ned i forespørgsler og SFX. Dette er et kendt defekt og rettes, i en kommende version. 


## <a name="next-steps"></a>Næste trin
- [Konfigurationsindstillinger for enkeltstående Windows klynge](service-fabric-cluster-manifest.md)
- [Sikre en enkeltstående klynge på Windows ved hjælp af Windows-sikkerhed](service-fabric-windows-cluster-windows-security.md)
- [Sikre en enkeltstående klynge på Windows ved hjælp af X509 certifikater](service-fabric-windows-cluster-x509-security.md)
- [Oprette en enkeltstående Service-strukturen klynge med Azure VM'er, der kører Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
