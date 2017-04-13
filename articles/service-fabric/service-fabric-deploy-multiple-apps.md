<properties
   pageTitle="Installere en Node.js-program, der bruger MongoDB | Microsoft Azure"
   description="Gennemgang om, hvordan du pakke flere gæst eksekverbare filer for at installere på en Azure Service strukturen klynge"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/22/2016"
   ms.author="msfussell;mikhegn"/>


# <a name="deploy-multiple-guest-executables"></a>Installere flere gæst eksekverbare filer

I denne artikel viser, hvordan du pakke og installere flere gæst eksekverbare filer til Azure Service struktur. Opbygge og implementere en enkelt Service-strukturen pakke læse hvordan du kan [installere gæst eksekverbare til tjenesten struktur](service-fabric-deploy-existing-app.md).

Mens denne gennemgang viser, hvordan du installerer et program med en Node.js front-end, der bruger MongoDB som datalager, kan du anvende trinnene på alle programmer, der er afhængig af et andet program.   

Du kan bruge Visual Studio til at producere programpakke, der indeholder flere gæst eksekverbare filer. Se [Brug af Visual Studio til at pakke et eksisterende program](service-fabric-deploy-existing-app.md#using-visual-studio-to-package-an-existing-executable). Når du har tilføjet den første gæst eksekverbare fil, skal du højreklikke på programmet projektet og vælge **Tilføj ny tjeneste strukturen service ->** at tilføje den anden gæst eksekverbare projekt til løsningen. Bemærk: Hvis du vælger at sammenkæde kilden i Visual Studio-projekt, opbygning af Visual Studio-løsning sikrer, at din programpakke er opdateret med ændringer i kilden. 

## <a name="manually-package-the-multiple-guest-executable-application"></a>Manuelt pakke flere gæst eksekverbart program
Alternativt kan du manuelt pakke den eksekverbare gæst. I denne artikel bruges til manuel emballagen Service-strukturen emballagen værktøjet, som findes på [http://aka.ms/servicefabricpacktool](http://aka.ms/servicefabricpacktool).

### <a name="packaging-the-nodejs-application"></a>Emballagen programmet Node.js
Denne artikel forudsætter, at Node.js ikke er installeret på knuderne i tjenesten strukturen klynge. Derfor skal du tilføje Node.exe til rodmappen programmets node før emballagen. Mappestrukturen for programmet Node.js (ved hjælp af Express web framework og Jade skabelon program), skal ligne til den nedenfor:

```
|-- NodeApplication
  	|-- bin
        |-- www
  	|-- node_modules
        |-- .bin
        |-- express
        |-- jade
        |-- etc.
  	|-- public
        |-- images
        |-- etc.
  	|-- routes
        |-- index.js
        |-- users.js
  	|-- views
        |-- index.jade
        |-- etc.
  	|-- app.js
  	|-- package.json
  	|-- node.exe
```

Som et næste trin opretter du en programpakke til programmet Node.js. Nedenstående kode opretter en tjeneste-strukturen programpakke, der indeholder programmet Node.js.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Nedenfor vises en beskrivelse af de parametre, bruges:

- **/Source** peger på mappen i det program, der skal pakkes.
- **/ TARGET** definerer den mappe, hvor pakken skal oprettes. Denne mappe skal være forskellige fra kildemappen.
- **appname** definerer programmet navnet på det eksisterende program. Det er vigtigt at forstå, at dette omsættes til navnet på tjenesten i manifestet og ikke til navnet på tjenesten strukturen programmet.
- **/exe** definerer den eksekverbare fil, der er meningen Service-strukturen til Start, i dette tilfælde `node.exe`.
- **/ma** definerer det argument, der bruges til at starte den eksekverbare fil. Som Node.js ikke er installeret, Service-strukturen skal Start webserveren Node.js ved at udføre `node.exe bin/www`.  `/ma:'bin/www'`fortæller værktøjet emballagen bruge `bin/ma` som argumentet for node.exe.
- **/AppType** definerer navnet på tjenesten strukturen programmet på computeren.

Hvis du gå til den mappe, der blev angivet i parameteren/Target, kan du se, at værktøjet har oprettet et fuldt funktionelt Service-strukturen pakke, som vist nedenfor:

```
|--[yourtargetdirectory]
  	|-- NodeApplication
        |-- C
              |-- bin
              |-- data
              |-- node_modules
              |-- public
              |-- routes
              |-- views
              |-- app.js
              |-- package.json
              |-- node.exe
        |-- config
              |--Settings.xml
        |-- ServiceManifest.xml
  	|-- ApplicationManifest.xml
```
Den oprettede ServiceManifest.xml har nu en sektion, der beskriver, hvordan webserveren Node.js skal startes, som vist i nedenstående kodestykket:

```xml
<CodePackage Name="C" Version="1.0">
    <EntryPoint>
        <ExeHost>
            <Program>node.exe</Program>
            <Arguments>'bin/www'</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
        </ExeHost>
    </EntryPoint>
</CodePackage>
```
I dette eksempel lytter webserveren Node.js til port 3000, så du behøver at opdatere slutpunktsoplysningerne i filen ServiceManifest.xml, som vist nedenfor.   

```xml
<Resources>
      <Endpoints>
        <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>Pakke programmet MongoDB
Nu hvor du har pakket Node.js programmet, kan du gå videre og pakke MongoDB. Som nævnt før, er de trin, som du gennemfører nu er ikke specifikke for Node.js og MongoDB. Faktisk gælder de for alle programmer, der er beregnet til at pakkes sammen som én tjeneste strukturen program.  

Hvis du vil pakke MongoDB, vil du at sikre, at du har pakket Mongod.exe og Mongo.exe. Begge binære filer er placeret i den `bin` mappe i adresselisten MongoDB installation. Mappestrukturen ligner et nedenfor.

```
|-- MongoDB
  	|-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Tjenesten strukturen skal starte MongoDB med en kommando, der svarer til den, herunder, så du skal bruge den `/ma` parameter når emballagen MongoDB.

```
mongod.exe --dbpath [path to data]
```
> [AZURE.NOTE] Dataene der bevares ikke tale om en node mislykket Hvis du placere mappen MongoDB data i den lokale mappe på noden. Du skal bruge robust lagerplads eller implementere et MongoDB replikasæt for at undgå tab af data.  

Vi Kør værktøjet emballagen med følgende parametre i PowerShell eller kommandoshell:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Hvis du vil tilføje MongoDB din tjeneste strukturen programpakke, skal du kontrollere, at / target parameter for peger på den samme mappe, som allerede indeholder programmet manifestet sammen med programmet Node.js. Du skal også sikre dig, at du bruger det samme ApplicationType navn.

Lad os gå til mappen, og Undersøg hvad værktøjet har oprettet.

```
|--[yourtargetdirectory]
  	|-- MyNodeApplication
  	|-- MongoDB
        |-- C
            |--bin
                |-- mongod.exe
                |-- mongo.exe
                |-- etc.
        |-- config
            |--Settings.xml
        |-- ServiceManifest.xml
  	|-- ApplicationManifest.xml
```
Som du kan se, tilføjes en ny mappe, MongoDB, til den mappe, der indeholder de MongoDB binære filer i værktøjet. Hvis du åbner den `ApplicationManifest.xml` fil, du kan se, at pakken indeholder nu både Node.js program og MongoDB. Nedenstående kode viser indholdet af programmanifestet.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MongoDB" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeService" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MongoDBService">
         <StatelessService ServiceTypeName="MongoDB">
            <SingletonPartition />
         </StatelessService>
      </Service>
      <Service Name="NodeServiceService">
         <StatelessService ServiceTypeName="NodeService">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
</ApplicationManifest>  
```

### <a name="publishing-the-application"></a>Publicering af programmet
Det sidste trin er at udgive programmet til den lokale Service-strukturen klynge ved hjælp af PowerShell-scripts nedenfor:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Når programmet er publiceret på den lokale klynge, kan du få adgang til Node.js-programmet på den port, der er angivet i tjenestemanifestet af programmet Node.js – for eksempel http://localhost:3000.

I dette selvstudium har du set hvordan du kan nemt pakke to eksisterende programmer som én tjeneste strukturen program. Du har også lært, hvordan du installerer det til tjenesten struktur, så den kan få glæde af nogle af funktionerne Service-strukturen som høj tilgængelighed og systemtilstand-integration.

## <a name="next-steps"></a>Næste trin

- Få mere at vide om installation af objektbeholdere med [Oversigt over Service-strukturen og beholdere](service-fabric-containers-overview.md)
