<properties 
    pageTitle="Forespørge længerevarende handlinger | Microsoft Azure" 
    description="Dette emne viser, hvordan du afstemning længerevarende handlinger." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="delivering-live-streaming-with-azure-media-services"></a>Fremvisning af direkte Streaming med Azure Media Services

##<a name="overview"></a>Oversigt

Microsoft Azure Media Services indeholder API'er, som sender anmodninger til Media Services til at starte handlinger (for eksempel: oprette, starte, stoppe eller slette en kanal). Disse handlinger er længerevarende.

Media Services .NET SDK indeholder API'er, sender anmodningen, og vent på handlingen er fuldført (internt, API'erne forespørges efter status for operation med nogle mellemrum). For eksempel, når du ringer kanal. Start(), metoden returnerer efter kanalen er startet. Du kan også bruge den asynkrone version: venter på at blive kanal. StartAsync() (se oplysninger om opgavebaseret asynkron mønster, [Tryk på](https://msdn.microsoft.com/library/hh873175(v=vs.110).aspx)). API'er, sender en anmodning om en handling og derefter afstemning for status, indtil handlingen er fuldført, kaldes "afstemning metoder". Disse metoder (især asynkron version) anbefales til RTF-klientprogrammer og/eller med høj sikkerhed tjenester.

Der findes scenarier, hvor et program ikke kan vente en længerevarende HTTP-anmodning og ønsker at afstemning for status for operation manuelt. Et typisk eksempel ville være en browser, arbejde med en uden status webtjeneste: når browseren anmoder om at oprette en kanal, webtjenesten starter en længerevarende handling og returnerer handlings-ID'ET til browseren. Browseren kan derefter bede webtjenesten til at få af driftsstatus, der er baseret på-ID. Media Services .NET SDK indeholder API'er, der er nyttige til dette scenario. Disse API'er kaldes "ikke-afstemning metoder".
"Ikke-afstemning metoder" har følgende naming mønster: sende*OperationName*handling (for eksempel SendCreateOperation). Sende*OperationName*handlingen metoder returnere **IOperation** objektet. det returnerede objekt indeholder oplysninger, der kan bruges til at spore handlingen. Send*OperationName*OperationAsync metoder returnere **opgave<IOperation>**.

I øjeblikket følgende klasser understøtter ikke afstemning metoder: **kanal**, **StreamingEndpoint**og **Program**.

Bruge metoden **GetOperation** i klassen **OperationBaseCollection** til afstemning for af driftsstatus. Brug følgende intervaller til at kontrollere af driftsstatus: **kanal** og **StreamingEndpoint** handlinger for brug af 30 sekunder **Program** handlinger, brug af 10 sekunder.


##<a name="example"></a>Eksempel

I følgende eksempel definerer en klasse, kaldet **ChannelOperations**. Denne klassedefinition kan være et udgangspunkt til din web service klassedefinition. I følgende eksempler bruges til enkel, ikke asynkrone versioner af metoder.

I eksempel vises også, hvordan klienten kan bruge denne klasse.

###<a name="channeloperations-class-definition"></a>Definition af ChannelOperations klasse

    /// <summary> 
    /// The ChannelOperations class only implements 
    /// the Channel’s creation operation. 
    /// </summary> 
    public class ChannelOperations
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
    
        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;
    
        public ChannelOperations()
        {
                _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName,
                    _mediaServicesAccountKey);
    
                _context = new CloudMediaContext(_cachedCredentials);    }
    
        /// <summary>  
        /// Initiates the creation of a new channel.  
        /// </summary>  
        /// <param name="channelName">Name to be given to the new channel</param>  
        /// <returns>  
        /// Operation Id for the long running operation being executed by Media Services. 
        /// Use this operation Id to poll for the channel creation status. 
        /// </returns> 
        public string StartChannelCreation(string channelName)
        {
            var operation = _context.Channels.SendCreateOperation(
                new ChannelCreationOptions
                {
                    Name = channelName,
                    Input = CreateChannelInput(),
                    Preview = CreateChannelPreview(),
                    Output = CreateChannelOutput()
                });
    
            return operation.Id;
        }
    
        /// <summary> 
        /// Checks if the operation has been completed. 
        /// If the operation succeeded, the created channel Id is returned in the out parameter.
        /// </summary> 
        /// <param name="operationId">The operation Id.</param> 
        /// <param name="channel">
        /// If the operation succeeded, 
        /// the created channel Id is returned in the out parameter.</param>
        /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
        public bool IsCompleted(string operationId, out string channelId)
        {
            IOperation operation = _context.Operations.GetOperation(operationId);
            bool completed = false;
    
            channelId = null;
    
            switch (operation.State)
            {
                case OperationState.Failed:
                    // Handle the failure. 
                    // For example, throw an exception. 
                    // Use the following information in the exception: operationId, operation.ErrorMessage.
                    break;
                case OperationState.Succeeded:
                    completed = true;
                    channelId = operation.TargetEntityId;
                    break;
                case OperationState.InProgress:
                    completed = false;
                    break;
            }
            return completed;
        }
    
    
        private static ChannelInput CreateChannelInput()
        {
            return new ChannelInput
            {
                StreamingProtocol = StreamingProtocol.RTMP,
                AccessControl = new ChannelAccessControl
                {
                    IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelInput001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
                }
            };
        }
    
        private static ChannelPreview CreateChannelPreview()
        {
            return new ChannelPreview
            {
                AccessControl = new ChannelAccessControl
                {
                    IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelPreview001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
                }
            };
        }
    
        private static ChannelOutput CreateChannelOutput()
        {
            return new ChannelOutput
            {
                Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
            };
        }
    }

###<a name="the-client-code"></a>Klient-kode

    ChannelOperations channelOperations = new ChannelOperations();
    string opId = channelOperations.StartChannelCreation("MyChannel001");
    
    string channelId = null;
    bool isCompleted = false;
    
    while (isCompleted == false)
    {
        System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
        isCompleted = channelOperations.IsCompleted(opId, out channelId);
    }
    
    // If we got here, we should have the newly created channel id.
    Console.WriteLine(channelId);
 


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
