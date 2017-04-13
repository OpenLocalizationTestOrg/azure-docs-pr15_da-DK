I denne gennemgang lærer du, hvordan du bruger **Salesforce - når der oprettes et objekt** ved for at starte en arbejdsproces for logik app, når der oprettes et nyt kundeemne i din Salesforce.

>[AZURE.NOTE]Du kan få bedt om at logge på din Salesforce-konto, hvis du ikke allerede har oprettet en *forbindelse* til Salesforce.  

1. Angive *salesforce* i søgefeltet i logik apps designer og vælg derefter **Salesforce - når der oprettes et objekt** udløser.  
![Billede af Salesforce udløser 1](./media/connectors-create-api-salesforce/trigger-1.png)   
- **Når der oprettes et objekt** kontrolelementet til lydafspilning vises.  
![Salesforce udløser billede 2](./media/connectors-create-api-salesforce/trigger-2.png)   
- Vælg den **Objekttype** og vælg derefter *kundeemne* på listen over objekter. I dette trin du angiver, at du opretter en udløser, der giver besked din logik app, når der oprettes et nyt kundeemne i Salesforce.   
![Billede af Salesforce udløser 3](./media/connectors-create-api-salesforce/trigger-3.png)   
- Det var det. Du har oprettet udløseren. Du skal oprette mindst én handling for at gøre dette til et gyldigt logik app.    
![Billede af Salesforce udløser 4](./media/connectors-create-api-salesforce/trigger-4.png)   

På dette tidspunkt er din logik app konfigureret med en udløser, der starter en kørsel af de andre udløsere og handlinger i arbejdsprocessen, når der oprettes et nyt element i din Salesforce.  