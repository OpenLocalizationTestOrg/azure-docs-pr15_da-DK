## <a name="send-messages-to-event-hubs"></a>Sende meddelelser til begivenhed hubber

Biblioteket Java klient for begivenhed Hubs er tilgængelig til brug i Maven projekter fra [Maven centrallager](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)og kan refereres til ved hjælp af følgende afhængighed erklæring i projektfilen Maven:    

``` XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
```
 
For forskellige typer build-miljøer, kan du eksplicit få de seneste frigivne glas filer fra [Maven centrallager](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) eller fra [release fordeling punkt på GitHub](https://github.com/Azure/azure-event-hubs/releases).  

Importere *com.microsoft.azure.eventhubs* -pakken til begivenhed Hubs klient klasser og *com.microsoft.azure.servicebus* for utility klasser som almindelige undtagelser, der deles med Azure Service Bus SMS-klienten til en enkelt begivenhed publisher. 

Det følgende eksempel først oprette et nyt Maven projekt til et konsol/shell-program i din foretrukne Java udviklingsmiljø. Klassen bliver kaldt ```Send```.     

``` Java

import java.io.IOException;
import java.nio.charset.*;
import java.util.*;
import java.util.concurrent.ExecutionException;

import com.microsoft.azure.eventhubs.*;
import com.microsoft.azure.servicebus.*;

public class Send
{
    public static void main(String[] args) 
            throws ServiceBusException, ExecutionException, InterruptedException, IOException
    {
```

Erstat navneområde og begivenhed Hub navne med de værdier, der bruges, når du har oprettet den begivenhed Hub.

``` Java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";
    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";
    ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
```

Derefter skal du oprette en enkelt begivenhed ved at gøre en streng til dens UTF-8 byte kodning. Vi derefter oprette en ny begivenhed Hubs klientforekomst fra forbindelsesstrengen og send meddelelsen.   

``` Java 
                
    byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
    EventData sendEvent = new EventData(payloadBytes);
    
    EventHubClient ehClient = EventHubClient.createFromConnectionStringSync(connStr.toString());
    ehClient.sendSync(sendEvent);
    }
}

``` 
