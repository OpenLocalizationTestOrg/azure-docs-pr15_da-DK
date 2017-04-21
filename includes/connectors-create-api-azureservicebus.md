Nu hvor du har tilføjet en udløser, interessant dens tid til at gøre noget med de data, der er oprettet af udløseren. Følg disse trin for at tilføje en handlingen **SharePoint Online - oprette fil** . Denne handling kan oprette en fil i SharePoint Online, hver gang den nye element udløser udløses. 

Konfigurere denne handling, skal du angive følgende oplysninger. Du vil bemærke, at det er let at bruge data, der genereres af udløseren som input til nogle af egenskaberne for den nye fil:

|Oprette filegenskab|Beskrivelse|
|---|---|
|Webstedets URL-adresse|Dette er URL-adressen for SharePoint Online-webstedet hvor du vil oprette den nye fil. Vælg stedet på listen præsenteres.|
|Mappesti|Dette er den mappe (i webstedets URL-adresse), hvor den nye fil skal placeres. Søge efter, og vælg mappen.|
|Filnavn|Dette er navnet på den fil, der oprettes.|
|Filens indhold|Det indhold, der skal skrives til filen.|

1. Vælg **+ nyt trin** at føje handlingen.  
![](./media/connectors-create-api-sharepointonline/action-1.png)  
- Vælg **Tilføj en handling** link. Dette åbner søgefeltet, hvor du kan søge efter noget, du vil foretage. I dette eksempel skal har SharePoint handlinger særlig interesse.    
![](./media/connectors-create-api-sharepointonline/action-2.png)    
- Angiv *sharepoint* for at søge efter handlinger, der er relateret til SharePoint.
- Vælg **SharePoint Online - oprette fil** som handlingen, der skal udføres.   **Bemærk**: bliver du bedt om at godkende din logik app til at få adgang til dine SharePoint-konto, hvis du ikke har gjort det tidligere.    
![](./media/connectors-create-api-sharepointonline/action-3.png)    
- Kontrolelementet **Opret fil** åbnes.   
![](./media/connectors-create-api-sharepointonline/action-4.png)     
- Vælg **Webstedets URL-adresse** , og Søg efter det websted, hvor du vil have til at oprette filen.     
![](./media/connectors-create-api-sharepointonline/action-5.png)  
- Vælg **mappesti** , og Find den mappe, hvor den nye fil skal placeres.  
![](./media/connectors-create-api-sharepointonline/action-6.png)  
- Vælg kontrolelementet **filnavn** , og skriv navnet på den fil, du vil oprette. Bemærk, kan du bruge en af egenskaberne fra den udløser, du tidligere har oprettet ved blot at markere den på listen præsenteres som filnavn.     
![](./media/connectors-create-api-sharepointonline/action-7.png)  
- Vælg kontrolelementet **filens indhold** , og skriv det indhold, der skal skrives på den fil, der oprettes. Bemærk, at du kan bruge en af egenskaberne fra den udløser, du tidligere har oprettet for indholdet af filen. Vælg blot egenskaberne på listen, vises. Du kan også angive **filens indhold** tekst direkte i kontrolelementet. I dette eksempel jeg valgte nogle egenskaber og tilføjet mellemrum og en bindestreg mellem hver egenskab.        
![](./media/connectors-create-api-sharepointonline/action-8.png)  
- Gemme ændringerne i arbejdsprocessen  
