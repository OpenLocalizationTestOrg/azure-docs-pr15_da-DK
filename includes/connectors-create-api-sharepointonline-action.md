Nu hvor du har tilføjet en udløser, interessant dens tid til at gøre noget med de data, der er oprettet af udløseren. Følg disse trin for at føje handlingen **SharePoint Online - oprette fil** . Denne handling kan oprette en fil i SharePoint Online, hver gang den nye element udløser udløses. 

Konfigurere denne handling, skal du angive følgende oplysninger. Når du angiver disse oplysninger, vil du bemærke, at det er let at bruge data, der genereres af udløseren som input til nogle af egenskaberne for den nye fil:

|Oprette filegenskab|Beskrivelse|
|---|---|
|Webstedets URL-adresse|Dette er URL-adressen for SharePoint Online-webstedet hvor du vil oprette den nye fil. Vælg stedet på listen præsenteres.|
|Mappesti|Dette er mappen (i den webstedets URL-adresse markeret i ovenstående trin) hvor den nye fil skal placeres. Søge efter, og vælg mappen.|
|Filnavn|Dette er navnet på den fil, der oprettes.|
|Arkivere indhold|Det indhold, der skal skrives til filen.|

1. Vælg **+ nyt trin** at føje handlingen.  
![SharePoint online-handlingen billede 1](./media/connectors-create-api-sharepointonline/action-1.png)  
- Vælg linket **Tilføj en handling** . Dette åbner søgefeltet, hvor du kan søge efter noget, du vil foretage. I dette eksempel skal har SharePoint handlinger særlig interesse.    
![SharePoint online-handlingen billede 2](./media/connectors-create-api-sharepointonline/action-2.png)    
- Angiv *sharepoint* for at søge efter handlinger, der er relateret til SharePoint.
- Vælg **SharePoint Online - oprette fil** som handlingen, der skal udføres.   **Bemærk**: bliver du bedt om at tillade, at din logik app til at få adgang til dine SharePoint-konto, hvis du ikke har oprettet en forbindelse til SharePoint Online på et tidligere.    
![Billede af SharePoint online-handlingen 3](./media/connectors-create-api-sharepointonline/action-3.png)    
- Kontrolelementet **Opret fil** åbnes.   
![Billede af SharePoint online-handlingen 4](./media/connectors-create-api-sharepointonline/action-4.png)     
- Vælg **Webstedets URL-adresse** , og Søg efter det websted, hvor du vil have til at oprette filen.     
![Billede af SharePoint online-handlingen 5](./media/connectors-create-api-sharepointonline/action-5.png)  
- Vælg **mappesti** , og Find den mappe, hvor den nye fil skal placeres.  
![Billede af SharePoint online-handlingen 6](./media/connectors-create-api-sharepointonline/action-6.png)  
- Vælg kontrolelementet **filnavn** , og skriv navnet på den fil, du vil oprette. Her kan du enten indtaste filnavnet direkte eller du kan bruge en af egenskaberne fra den udløser, du tidligere har oprettet. Du kan gøre dette ved at vælge egenskaber på listen over **udskriver fra når der oprettes et nyt element**. Denne liste er kun visning, når du har valgt kontrolelementet **filnavn** . I denne walkthough markeret jeg ID (ID'ET for det nye listeelement) som navnet på den fil, der oprettes af handlingen **SharePoint Online - oprette fil** .    
![Billede af SharePoint online-handlingen 7](./media/connectors-create-api-sharepointonline/action-7.png)  
- Vælg kontrolelementet **filens indhold** , og skriv det indhold, der skal skrives på den fil, der oprettes. Bemærk, at du kan bruge en af egenskaberne fra den udløser, du tidligere har oprettet for indholdet af filen. Vælg blot egenskaberne på listen, vises. Du kan også angive **filens indhold** tekst direkte i kontrolelementet. I dette eksempel jeg valgte nogle egenskaber og tilføjet mellemrum og en bindestreg mellem hver egenskab.        
![Billede af SharePoint online-handlingen 8](./media/connectors-create-api-sharepointonline/action-8.png)  
- Gemme ændringerne i arbejdsprocessen  
- Tillykke, du har nu en fuldt funktionelle logik app, der udløses, når der føjes et nyt element til en SharePoint Online-liste. Appen opretter derefter en fil ved hjælp af nogle af egenskaberne fra det nye listeelement.  Du kan nu tester den ved at oprette et nyt element i SharePoint-listen. 
