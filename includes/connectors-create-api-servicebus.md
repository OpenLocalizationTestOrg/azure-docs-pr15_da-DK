### <a name="prerequisites"></a>Forudsætninger

Du skal have en [Service Bus](https://azure.microsoft.com/services/service-bus/) -konto.  

Før du kan bruge kontoen Azure Service Bus i en logik app, skal du tillade logik app til at oprette forbindelse til kontoen service bus. Heldigvis kan du gøre dette nemt fra i din logik app på Azure-portalen.  

Her er trinnene for at tillade, at din logik app til at oprette forbindelse til kontoen Service Bus:  

1. For at oprette en forbindelse til tjenesten Bus i logik app designer skal du vælge **Vis Microsoft administrerede API'er** i på rullelisten. Angiv derefter **tjenesten bus** i søgefeltet. Vælg den udløser eller handling, du vil bruge.  
    ![Billede af Service Bus forbindelse 1](./media/connectors-create-api-servicebus/servicebus-1.png)  

2. Hvis du ikke har oprettet en forbindelse til tjenesten Bus før, bliver du bedt om at angive din Service Bus legitimationsoplysninger. Disse legitimationsoplysninger anvendes til at tillade, at din logik app til at oprette forbindelse til, og få adgang til kontoen Service Bus data. Service Bus forbindelsen skal forbindelsesstrengen til navneområdet Service Bus. Det kræver også **administrere** tilladelser. En god måde at kende, hvis din forbindelsesstreng til navneområdet eller et bestemt objekt er om den indeholder den `EntityPath` parameter. Hvis det er tilfældet, er det ikke højre forbindelsesstrengen til en logik app.  
    ![Service Bus forbindelsesstreng](./media/connectors-create-api-servicebus/connectionstring.png)

1. Når du har modtaget forbindelsesstrengen for navneområdet, kan du bruge den til API forbindelsen i logik Apps.  
    ![Billede af Service Bus forbindelse 2](./media/connectors-create-api-servicebus/servicebus-2.png)  

3. Bemærk forbindelsen er oprettet, og du kan nu frit fortsætte med trin i din app, logik.  
    ![Billede af Service Bus forbindelse 3](./media/connectors-create-api-servicebus/servicebus-3.png)   
