<properties 
    pageTitle="Node.js program ved hjælp af Socket.io | Microsoft Azure" 
    description="Lær at bruge socket.io i et hostes på Azure node.js til computeren." 
    services="cloud-services" 
    documentationCenter="nodejs" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service"></a>Opbygge et Node.js Chat-program med Socket.IO på en Azure skybaseret tjeneste

Socket.IO indeholder realtid kommunikationen mellem mellem node.js server og klienter. Dette selvstudium fører dig gennem vært for en socket. EY baseret chat program på Azure. Du kan finde flere oplysninger om Socket.IO, <http://socket.io/>.

Et skærmbillede af det færdige program er nedenfor:

![Et browservindue, som viser tjenesten hostes på Azure][completed-app]  

## <a name="prerequisites"></a>Forudsætninger

Sørg for, at følgende produkter og versioner er installeret for at fuldføre eksemplet i denne artikel:

* Installere [Visual Studio 2013](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)
* Installere [Node.js](https://nodejs.org/download/)
* Installere [Python version 2.7.10](https://www.python.org/)

## <a name="create-a-cloud-service-project"></a>Oprette en skybaseret tjeneste projekt

Følgende trin Opret projektets skybaseret tjeneste, der er vært programmet Socket.IO.

1. Søge efter **Windows PowerShell**fra **Menuen Start** eller **Startskærmbilledet**. Til sidst skal du højreklikke på **Windows PowerShell** , og vælg **Kør som Administrator**.

    ![Azure PowerShell-ikon][powershell-menu]

2. Oprette en mappe med navnet **c:\\node**. 
 
        PS C:\> md node

3. Skifte til den **c:\\node** directory
 
        PS C:\> cd node

4. Skriv følgende kommandoer til at oprette en ny løsning med navnet **chatapp** og en kollega rolle med navnet **WorkerRole1**:

        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole

    Du får vist følgende svaret:

    ![Output fra den nye azureservice og tilføje azurenodeworkerrolecmdlets](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>Hente i Chat-eksempel

Vi bruger eksemplet chat fra [Socket.IO GitHub lager]for dette projekt. Udfør følgende trin for at hente eksemplet og føje det til det projekt, du tidligere har oprettet.

1.  Oprette en lokal kopi af lageret ved hjælp af knappen **Klon** . Du kan også bruge knappen **ZIP** hente projektet.

    ![Et browservindue, få vist https://github.com/LearnBoost/socket.io/tree/master/examples/chat, hvor ZIP download-ikonet fremhævet][chat-example-view]

3.  Navigere mappestrukturen for den lokale lager, indtil du kommer til den **eksempler\\chat** directory. Kopiere indholdet af denne mappe til de **C:\\node\\chatapp\\WorkerRole1** directory oprettede tidligere.

    ![Explorer, vise indholdet af eksemplerne\\chat directory ud fra arkivet][chat-contents]

    De fremhævede elementer i skærmbilledet ovenfor er de filer, der er kopieret fra den **eksempler\\chat** directory

4.  I den **C:\\node\\chatapp\\WorkerRole1** directory, Slet **server.js** filen og derefter omdøbe filen **app.js** til **server.js**. Dette fjerner den **server.js** fil, der er oprettet tidligere af Cmdletten **Tilføj AzureNodeWorkerRole** og erstatter det med programfilen fra eksemplet chat.

### <a name="modify-serverjs-and-install-modules"></a>Ændre Server.js og installere moduler

Før du tester programmet i Azure emulatoren, skal vi foretage nogle mindre ændringer. Udfør følgende trin til filen server.js:

1.  Åbn filen **server.js** i Visual Studio eller et andet tekstredigeringsprogram.

2.  Find sektionen **modul afhængigheder** i begyndelsen af server.js, og ret linjen med **sio = require('.. //.. lib//socket.IO')** til **sio = require('socket.io')** som vist nedenfor:

        var express = require('express')
        , stylus = require('stylus')
        , nib = require('nib')
        //, sio = require('..//..//lib//socket.io'); //Original
        , sio = require('socket.io');                //Updated

3.  For at sikre, at programmet lytter på den korrekte port, åbne server.js i Notesblok eller din foretrukne editor og derefter ændre følgende linje ved at erstatte **3000** med **process.env.port** , som vist nedenfor:

        //app.listen(3000, function () {            //Original
        app.listen(process.env.port, function () {  //Updated
          var addr = app.address();
          console.log('   app listening on http://' + addr.address + ':' + addr.port);
        });

Når du har gemt ændringerne til **server.js**, følge nedenstående trin for at installere påkrævede moduler, og derefter teste programmet i Azure emulatoren:

1.  Med **Azure PowerShell**skifte til den **C:\\node\\chatapp\\WorkerRole1** directory og brug følgende kommando til at installere de moduler, der kræves af dette program:

        PS C:\node\chatapp\WorkerRole1> npm install

    Dette vil installere de moduler, der er angivet i filen package.json. Når kommandoen er fuldført, skal du se afsnittet output, der svarer til følgende:

    ![Output fra npm installere kommandoen][The-output-of-the-npm-install-command]

4.  Da dette eksempel blev oprindeligt blev en del af Socket.IO GitHub lager og direkte refererer til biblioteket Socket.IO relativ sti, blev ikke refereres til Socket.IO i filen package.json, så vi skal installere den ved at udstede følgende kommando:

        PS C:\node\chatapp\WorkerRole1> npm install socket.io --save

### <a name="test-and-deploy"></a>Afprøve og implementere

1.  Start emulatoren ved at udstede følgende kommando:

        PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch

2.  Åbn en browser, og gå til **http://127.0.0.1**.

3.  Når browservinduet åbnes, skal du angive et kaldenavn og tryk derefter på enter.
    Dette vil alle du kan sende meddelelser som et bestemt kaldenavn. Åbn yderligere browservinduer ved hjælp af den samme URL-adresse for at teste flere brugere funktionaliteten, og Angiv forskellige kaldenavne.

    ![Visning af chat-meddelelser fra bruger 1 og User2 to vinduer i browseren](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)

3.  Når du tester programmet, du stop emulatoren ved at udstede følgende kommando:

        PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator

4.  Brug cmdlet'en **Publicer AzureServiceProject** , du skal installere programmet til Azure. Eksempel:

        PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch

    > [AZURE.IMPORTANT] Sørg for at anvende et entydigt navn, ellers Publicer processen mislykkes. Når installationen er fuldført, åbnes browseren og gå til tjenesten udløst.
    > 
    > Hvis du modtager en fejlmeddelelse om, at medfølgende Abonnementsnavnet ikke findes i den importerede Publicer profil, skal du hente og importere profilen publicering for dit abonnement, før du anvender for Azure. Se afsnittet **implementering af programmet til Azure** i [opbygge og anvende et Node.js program til en skybaseret tjeneste til Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)

    ![Et browservindue, som viser tjenesten hostes på Azure][completed-app]

    > [AZURE.NOTE] Hvis du modtager en fejlmeddelelse om, at medfølgende Abonnementsnavnet ikke findes i den importerede Publicer profil, skal du hente og importere profilen publicering for dit abonnement, før du anvender for Azure. Se afsnittet **implementering af programmet til Azure** i [opbygge og anvende et Node.js program til en skybaseret tjeneste til Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)

Dit program er nu kører på Azure og kan overføre chatmeddelelser mellem forskellige klienter, der bruger Socket.IO.

> [AZURE.NOTE] Dette eksempel er begrænset til chatte mellem brugere, der er knyttet til den samme forekomst for enkel. Det betyder, at hvis skytjenesten opretter to arbejder rolle forekomster, brugerne kun kan chatte med andre forbindelsen til den samme arbejder rolle forekomst. For at tilpasse programmet til at arbejde med flere forekomster af rolle, kan du bruge en teknologi som Service Bus til at dele tilstanden Socket.IO store på tværs af forekomster. Du kan finde eksempler, Bus servicekøer og emner brugen eksemplerne i [Azure SDK for Node.js GitHub lager](https://github.com/WindowsAzure/azure-sdk-for-node).

##<a name="next-steps"></a>Næste trin

Du har lært hvordan du opretter en grundlæggende chat-program, der er placeret i en Azure skybaseret tjeneste i dette selvstudium. Hvis du vil lære at være vært for dette program i et Azure-websted, skal du se [opbygge et Node.js Chat program med Socket.IO på en Azure-webstedet][chatwebsite].

Du kan finde flere oplysninger, se også [Node.js Developer Center](/develop/nodejs/).

  [chatwebsite]: /develop/nodejs/tutorials/website-using-socketio/

  [Azure SLA]: http://www.windowsazure.com/support/sla/
  [Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
  [completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
  [Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
  [Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
  [Socket.IO GitHub lager]: https://github.com/LearnBoost/socket.io/tree/0.9.14
  [Azure Considerations]: #windowsazureconsiderations
  [Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
  [Summary and Next Steps]: #summary
  [powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

  [chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
  [chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png
  
  
  [chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
  [The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
  [The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png
  
 
