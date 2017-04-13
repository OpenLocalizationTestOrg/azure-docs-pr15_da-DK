Her er, hvordan du bruger **Service Bus - når der modtages en meddelelse i en kø** ved for at starte en arbejdsproces for logik app, når et nyt element, der sendes til en tjeneste Bus kø.  

>[AZURE.NOTE]Du bliver bedt om at logge på med din tjeneste Bus forbindelsesstreng, hvis du ikke allerede har oprettet en forbindelse til tjenesten Bus.  

1. Angiv **service bus**i søgefeltet på logik apps designer. Vælg **Service Bus - når der modtages en meddelelse i en kø** udløser.  
![Billede af Service Bus udløser 1](./media/connectors-create-api-servicebus/trigger-1.png)   
- **Når der modtages en meddelelse i en kø** dialogboksen vises.  
![Billede af Service Bus udløser 2](./media/connectors-create-api-servicebus/trigger-2.png)   
- Skriv navnet på den tjeneste Bus kø, du vil udløse til at overvåge.   
![Billede af Service Bus udløser 3](./media/connectors-create-api-servicebus/trigger-3.png)   

På dette tidspunkt er din logik app konfigureret med en udløser. Når der modtages et nyt element i køen du har valgt, begynder udløseren en kørsel af de andre udløsere og handlinger i arbejdsprocessen.    
