I dette eksempel viser jeg dig, hvordan du bruger **SharePoint Online - når der oprettes et nyt element** udløser til at starte en arbejdsproces for logik app, når der oprettes et nyt element i en SharePoint Online-liste.

>[AZURE.NOTE]Du kan få bedt om at logge på din SharePoint-konto, hvis du ikke allerede har oprettet en *forbindelse* til SharePoint Online.  

1. Angive *sharepoint* i søgefeltet i logik apps designer og vælg derefter **SharePoint Online - når der oprettes et nyt element** udløser.  
![SharePoint online udløser billede](./media/connectors-create-api-sharepointonline/trigger-1.png)  
- **Når et nyt element oprettes** kontrolelementet til lydafspilning vises.  
![SharePoint online udløser billede 2](./media/connectors-create-api-sharepointonline/trigger-2.png)   
- Vælg en **webstedets URL-adresse**. Dette er den online SharePoint-websted, du vil overvåge for nye emner til at udløse arbejdsprocessen.  
![Billede af SharePoint online udløser 3](./media/connectors-create-api-sharepointonline/trigger-3.png)   
- Vælg et **navn**. Dette er liste på SharePoint Online-webstedet du vil overvåge for nye emner, der skal udløse arbejdsprocessen.  
![Billede af SharePoint online udløser 4](./media/connectors-create-api-sharepointonline/trigger-4.png)   

På dette tidspunkt er din logik app konfigureret med en udløser, der starter en kørsel af de andre udløsere og handlinger i arbejdsprocessen. Dette vil finde sted hver gang et nyt element oprettes i SharePoint Online-liste du har valgt.  