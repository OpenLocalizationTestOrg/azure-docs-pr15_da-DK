
1. I løsning visningen (eller **Solution Explorer** i Visual Studio), skal du højreklikke på mappen **komponenter** , skal du klikke på **Få mere komponenter...**, søge efter komponenten **Google Cloud Messaging klienten** og føje det til projektet.

2. Åbn filen ToDoActivity.cs projektet og tilføje følgende ved hjælp af klassen-sætning:

        using Gcm.Client;

3. Tilføj følgende nye kode i klassen **ToDoActivity** : 

        // Create a new instance field for this activity.
        static ToDoActivity instance = new ToDoActivity();

        // Return the current activity instance.
        public static ToDoActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }
        // Return the Mobile Services client.
        public MobileServiceClient CurrentClient
        {
            get
            {
                return client;
            }
        }

    Det gør muligt at få adgang til mobilklient forekomsten fra tjenesten opslagsnål handler processen.

4.  Når **MobileServiceClient** er oprettet, skal du tilføje følgende kode til metoden **OnCreate** :

        // Set the current instance of TodoActivity.
        instance = this;

        // Make sure the GCM client is set up correctly.
        GcmClient.CheckDevice(this);
        GcmClient.CheckManifest(this);

        // Register the app for push notifications.
        GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);

Din **ToDoActivity** er nu forberedt til at tilføje pushmeddelelser.