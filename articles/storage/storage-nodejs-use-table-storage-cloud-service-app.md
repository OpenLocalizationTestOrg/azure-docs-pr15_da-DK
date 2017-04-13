<properties 
    pageTitle="WebApp med tabellagring (Node.js) | Microsoft Azure" 
    description="En introduktion, der bygger på Web App med Express selvstudium ved at tilføje tjenester Azure-lager og modulet Azure." 
    services="cloud-services, storage" 
    documentationCenter="nodejs" 
    authors="tamram" 
    manager="carmonm" 
    editor="tysonn"/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="robmcm"/>

# <a name="nodejs-web-application-using-storage"></a>Node.js webprogrammet ved hjælp af lager

## <a name="overview"></a>Oversigt

I dette selvstudium skal du udvide det program, der er oprettet i [Node.js webprogrammet ved hjælp af Express] selvstudiet ved hjælp af Microsoft Azure-klientbiblioteker for Node.js til at arbejde med data management services. Du kan udvide dit program til at oprette en webbaseret-opgaveliste programmet, som du kan installere til Azure. Opgavelisten kan en bruger til at hente opgaver, tilføje nye opgaver, og Markér opgaver som fuldført.

Opgaveelementer gemmes i Azure-lager. Azure-lager indeholder ustrukturerede datalager, der er fejlsikret og høj tilgængelighed. Azure-lager indeholder flere datastrukturer, hvor du kan gemme og få adgang til data, og du kan udnytte lagerplads tjenester fra de API'er, der er inkluderet i Azure SDK Node.js eller via REST API'er. Se [gemme og få adgang til Data i Azure]kan finde flere oplysninger.

Dette selvstudiet antages, at du har fuldført [Node.js-webprogrammet] og [Node.js med Express][Node.js webprogram med Express] selvstudierne.

Du kan få mere at vide:

-   Sådan arbejder du med Jade skabelon-program
-   Sådan arbejder du med Azure Data Management services

Et skærmbillede af det færdige program er nedenfor:

![Færdige websiden i internet explorer](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## <a name="setting-storage-credentials-in-webconfig"></a>Angive lagerplads legitimationsoplysninger i Web.Config

For at få adgang til Azure-lager, skal du overfører i lagerplads legitimationsoplysninger. For at gøre dette skal anvende du web.config programindstillinger.
Disse indstillinger vil blive overført som miljøvariabler til Node, der læses derefter ved Azure SDK.

> [AZURE.NOTE] Lagerplads legitimationsoplysninger anvendes kun, når programmet installeres i Azure. Når du kører i emulatoren, bruger at programmet emulatoren lagerplads.

Udfør følgende trin for at hente kontolegitimationsoplysninger lager og føje dem til indstillingerne for web.config:

1.  Hvis den ikke allerede er åbne, start Azure PowerShell fra menuen **Start** ved at udvide **alle programmer, Azure**, skal du højreklikke på **Azure PowerShell**, og vælg derefter **Kør som Administrator**.

2.  Skifte til den mappe, der indeholder dit program. For eksempel C:\\node\\vha\\WebRole1.

3.  Angiv følgende cmdlet for at hente kontooplysningerne lagerplads fra vinduet Azure Powershell:

        PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts

    Dette henter listen over lagerplads konti og taster, der er knyttet til din hostet service-konto.

    > [AZURE.NOTE] Da Azure SDK opretter en konto med lagerplads, når du installerer en tjeneste, skal der allerede findes en lagerplads konto fra implementering af dit program i de forrige hjælpelinjer.

4.  Åbn filen **ServiceDefinition.csdef** , der indeholder de miljøindstillinger, der bruges, når programmet installeres i Azure:

        PS C:\node\tasklist> notepad ServiceDefinition.csdef

5.  Indsæt følgende blokering under **miljø** element, erstatter {LAGERPLADS konto} og {LAGERPLADS HURTIGTAST} med kontonavnet og den primære nøgle for kontoen lagerplads, du vil bruge til installation:

        <Variable name="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}" />
        <Variable name="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}" />

    ![Filindholdet web.cloud.config](./media/storage-nodejs-use-table-storage-cloud-service-app/node37.png)

6.  Gem filen, og Luk Notesblok.

### <a name="install-additional-modules"></a>Installere yderligere moduler

2. Brug følgende kommando til at installere [azure], [node-uuid], [nconf] og [asynkron] moduler lokalt samt som for at gemme en post for dem i filen **package.json** :

        PS C:\node\tasklist\WebRole1> npm install azure-storage node-uuid async nconf --save

    Output fra denne kommando skal se ud som følger:

        node-uuid@1.4.1 node_modules\node-uuid

        nconf@0.6.9 node_modules\nconf
        ├── ini@1.1.0
        ├── async@0.2.9
        └── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.8)

        azure-storage@0.1.0 node_modules\azure-storage
        ├── extend@1.2.1
        ├── xmlbuilder@0.4.3
        ├── mime@1.2.11
        ├── underscore@1.4.4
        ├── validator@3.1.0
        ├── node-uuid@1.4.1
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

##<a name="using-the-table-service-in-a-node-application"></a>Ved hjælp af tjenesten tabel i et node til computeren

I dette afsnit kan du udvide det grundlæggende program, der er oprettet af kommandoen **udtrykkelig** ved at tilføje en **task.js** -fil, der indeholder modellen for dine opgaver. Du kan også ændre den eksisterende **app.js** og oprette en ny **tasklist.js** -fil, der bruger modellen.

### <a name="create-the-model"></a>Oprette modellen

1. Oprette en ny mappe med navnet **modeller**i mappen **WebRole1** .

2. Oprette en ny fil med navnet **task.js**i mappen **modeller** . Denne fil indeholder modellen for de opgaver, der er oprettet af programmet.

3. Føj følgende kode for at referere til påkrævede biblioteker i starten af filen **task.js** :

        var azure = require('azure-storage');
        var uuid = require('node-uuid');
        var entityGen = azure.TableUtilities.entityGenerator;

4. Dernæst skal tilføje du kode for at definere og eksportere objektet opgave. Dette objekt er ansvarlig for at oprette forbindelse til tabellen.

        module.exports = Task;

        function Task(storageClient, tableName, partitionKey) {
          this.storageClient = storageClient;
          this.tableName = tableName;
          this.partitionKey = partitionKey;
          this.storageClient.createTableIfNotExists(tableName, function tableCreated(error) {
            if(error) {
              throw error;
            }
          });
        };

5. Derefter skal du tilføje følgende kode for at definere flere metoder til objektet opgave, der giver mulighed for interaktion med data, der gemmes i tabellen:

        Task.prototype = {
          find: function(query, callback) {
            self = this;
            self.storageClient.queryEntities(query, function entitiesQueried(error, result) {
              if(error) {
                callback(error);
              } else {
                callback(null, result.entries);
              }
            });
          },

          addItem: function(item, callback) {
            self = this;
            // use entityGenerator to set types
            // NOTE: RowKey must be a string type, even though
            // it contains a GUID in this example.
            var itemDescriptor = {
              PartitionKey: entityGen.String(self.partitionKey),
              RowKey: entityGen.String(uuid()),
              name: entityGen.String(item.name),
              category: entityGen.String(item.category),
              completed: entityGen.Boolean(false)
            };

            self.storageClient.insertEntity(self.tableName, itemDescriptor, function entityInserted(error) {
              if(error){  
                callback(error);
              }
              callback(null);
            });
          },

          updateItem: function(rKey, callback) {
            self = this;
            self.storageClient.retrieveEntity(self.tableName, self.partitionKey, rKey, function entityQueried(error, entity) {
              if(error) {
                callback(error);
              }
              entity.completed._ = true;
              self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
                if(error) {
                  callback(error);
                }
                callback(null);
              });
            });
          }
        }

6. Gem og Luk filen **task.js** .

### <a name="create-the-controller"></a>Oprette controlleren

1. Oprette en ny fil med navnet **tasklist.js** i mappen **WebRole1/omdirigerer** og åbne den i et tekstredigeringsprogram.

2. Tilføj følgende kode til **tasklist.js**. Azure og asynkron moduler, der bruges af **tasklist.js**indlæses. Dette definerer også funktionen **vha** , som der overføres en forekomst af objektet **opgave** , som vi definerede tidligere:

        var azure = require('azure-storage');
        var async = require('async');

        module.exports = TaskList;

        function TaskList(task) {
          this.task = task;
        }

2. Fortsæt med at tilføje til fil, **tasklist.js** ved at tilføje **showTasks**, **addTask**og **completeTasks**metoder:

        TaskList.prototype = {
          showTasks: function(req, res) {
            self = this;
            var query = azure.TableQuery()
              .where('completed eq ?', false);
            self.task.find(query, function itemsFound(error, items) {
              res.render('index',{title: 'My ToDo List ', tasks: items});
            });
          },

          addTask: function(req,res) {
            var self = this      
            var item = req.body.item;
            self.task.addItem(item, function itemAdded(error) {
              if(error) {
                throw error;
              }
              res.redirect('/');
            });
          },

          completeTask: function(req,res) {
            var self = this;
            var completedTasks = Object.keys(req.body);
            async.forEach(completedTasks, function taskIterator(completedTask, callback) {
              self.task.updateItem(completedTask, function itemsUpdated(error) {
                if(error){
                  callback(error);
                } else {
                  callback(null);
                }
              });
            }, function goHome(error){
              if(error) {
                throw error;
              } else {
               res.redirect('/');
              }
            });
          }
        }

3. Gem filen **tasklist.js** .

### <a name="modify-appjs"></a>Ændre app.js

1. Åbn filen **app.js** i et tekstredigeringsprogram i mappen **WebRole1** . 

2. I starten af fil, skal du tilføje følgende for at indlæse modulet azure og indstille Tabelnøglen navn og partition:

        var azure = require('azure-storage');
        var tableName = 'tasks';
        var partitionKey = 'hometasks';

3. Rul ned til hvor der står følgende linje i filen app.js:

        app.use('/', routes);
        app.use('/users', users);

    Erstat linjerne ovenfor med den kode, der er vist nedenfor. Dette til starte en forekomst af <strong>opgaven</strong> med en forbindelse til kontoen lagerplads. Dette er overføres til <strong>vha</strong>, som skal bruge det til at kommunikere med tjenesten tabel:

        var TaskList = require('./routes/tasklist');
        var Task = require('./models/task');
        var task = new Task(azure.createTableService(), tableName, partitionKey);
        var taskList = new TaskList(task);

        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));
    
4. Gem filen **app.js** .

### <a name="modify-the-index-view"></a>Ændre visningen indeks

1. Skifte til mappen, **visninger** , og Åbn filen **index.jade** i et tekstredigeringsprogram.

2. Erstat indholdet af filen **index.jade** med nedenstående kode. Dette definerer visningen til at vise eksisterende opgaver samt en formular til at tilføje nye opgaver og markere eksisterende som fuldført.

        extends layout

        block content
          h1= title
          br
        
          form(action="/completetask", method="post")
            table.table.table-striped.table-bordered
              tr
                td Name
                td Category
                td Date
                td Complete
              if tasks != []
                tr
                  td 
              else
                each task in tasks
                  tr
                    td #{task.name._}
                    td #{task.category._}
                    - var day   = task.Timestamp._.getDate();
                    - var month = task.Timestamp._.getMonth() + 1;
                    - var year  = task.Timestamp._.getFullYear();
                    td #{month + "/" + day + "/" + year}
                    td
                      input(type="checkbox", name="#{task.RowKey._}", value="#{!task.completed._}", checked=task.completed._)
            button.btn(type="submit") Update tasks
          hr
          form.well(action="/addtask", method="post")
            label Item Name: 
            input(name="item[name]", type="textbox")
            label Item Category: 
            input(name="item[category]", type="textbox")
            br
            button.btn(type="submit") Add item

3. Gem og Luk **index.jade** fil.

### <a name="modify-the-global-layout"></a>Ændre layoutet af globale

Filen **layout.jade** i mappen **visninger** bruges som en global skabelon til andre **.jade** filer. I dette trin kan du ændre den for at bruge [Twitter-Bootstrap](https://github.com/twbs/bootstrap), som er en værktøjskassen, der gør det nemt at designe et flot udseende websted.

1. Hente og udpakke filerne til [Twitter-Bootstrap](http://getbootstrap.com/). Kopiere filen **bootstrap.min.css** fra den **bootstrap\\fordeling\\css** mappe til den **offentlige\\typografiark** directory programmets vha.

2. Åbn **layout.jade** i tekstredigeringsprogrammet fra mappen **visninger** , og Erstat indholdet med følgende:

        doctype html
        html
          head
            title= title
            link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
            link(rel='stylesheet', href='/stylesheets/style.css')
          body.app
            nav.navbar.navbar-default
              div.navbar-header
                a.navbar-brand(href='/') My Tasks
            block content

3. Gem filen **layout.jade** .

### <a name="running-the-application-in-the-emulator"></a>Køre programmet i emulatoren

Brug følgende kommando for at starte programmet i emulatoren.

    PS C:\node\tasklist\WebRole1> start-azureemulator -launch

Browseren åbnes og viser den efterfølgende side:

![Et websted, gruppe med titlen min opgaveliste med en tabel, der indeholder opgaver og felter for at tilføje en ny opgave.](./media/storage-nodejs-use-table-storage-cloud-service-app/node44.png)

Brug formularen for at tilføje elementer eller fjerne eksisterende elementer ved at markere dem som fuldført.

## <a name="publishing-the-application-to-azure"></a>Publicering af programmet til Azure


Ring til følgende cmdlet for at geninstallere din hostet tjeneste til Azure i Windows PowerShell-vinduet.

    PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch

Erstat **myuniquename** med et entydigt navn til dette program. Erstat **datacentername** med navnet på en Azure datacenter, som **Vest USA**.

Når installationen er fuldført, skal du se et svar, der ligner følgende:

    PS C:\node\tasklist> publish-azureserviceproject -servicename tasklist -location "West US"
    WARNING: Publishing tasklist to Microsoft Azure. This may take several minutes...
    WARNING: 2:18:42 PM - Preparing runtime deployment for service 'tasklist'
    WARNING: 2:18:42 PM - Verifying storage account 'tasklist'...
    WARNING: 2:18:43 PM - Preparing deployment for tasklist with Subscription ID: 65a1016d-0f67-45d2-b838-b8f373d6d52e...
    WARNING: 2:19:01 PM - Connecting...
    WARNING: 2:19:02 PM - Uploading Package to storage service larrystore...
    WARNING: 2:19:40 PM - Upgrading...
    WARNING: 2:22:48 PM - Created Deployment ID: b7134ab29b1249ff84ada2bd157f296a.
    WARNING: 2:22:48 PM - Initializing...
    WARNING: 2:22:49 PM - Instance WebRole1_IN_0 of role WebRole1 is ready.
    WARNING: 2:22:50 PM - Created Website URL: http://tasklist.cloudapp.net/.

Som før, fordi du har angivet den **-Start** indstillingen browseren åbnes og viser dit program, der kører i Azure, når publicering er fuldført.

![Et browservindue, der viser siden min opgaveliste. URL-adressen angiver, at siden er nu blive hostes på Azure.](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## <a name="stopping-and-deleting-your-application"></a>Stoppe og slette dit program

Når du anvender dit program, kan du vil deaktivere den, så du kan undgå omkostninger eller opbygge og anvende andre programmer i gratis prøveversion tidsperiode.

Azure regninger web rolle forekomster i timen server tid consumed.
Server tidsforbrug, når dit program er installeret, selvom forekomster ikke kører og er i stoppet tilstand.

Følgende trin viser, hvordan du stoppe og slette dit program.

1.  I vinduet Windows PowerShell stop tjenesten installationen oprettede i forrige afsnit med følgende cmdlet:

        PS C:\node\tasklist\WebRole1> Stop-AzureService

    Stoppe tjenesten kan tage flere minutter. Når tjenesten er stoppet, modtager du en meddelelse om, at det længere.

3.  For at slette tjenesten, skal du ringe til følgende cmdlet:

        PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist

    Når du bliver bedt om det, kan du angive **Y** for at slette tjenesten.

    Slette tjenesten, kan det tage flere minutter. Du modtager en meddelelse om, at tjenesten er blevet slettet, når tjenesten er blevet slettet.

  [Node.js webprogrammet ved hjælp af Express]: http://azure.microsoft.com/develop/nodejs/tutorials/web-app-with-express/
  [Gemme og få adgang til Data i Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Node.js webprogram]: http://azure.microsoft.com/develop/nodejs/tutorials/getting-started/
 
 
