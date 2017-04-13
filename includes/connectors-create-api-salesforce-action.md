Nu hvor du har føjet en betingelse, interessant dens tid til at gøre noget med de data, der er oprettet af udløseren. Følg disse trin for at føje handlingen **Salesforce - Hent objekt** . Denne handling hente dataene, hver gang du opretter et nyt kundeemne. Du kan også tilføje endnu en handling, som skal bruge data fra Salesforce - Hent en Objekthandling at sende en mail via Office 365-forbindelsen.  

Konfigurere denne handling, skal du angive følgende oplysninger. Du vil bemærke, at det er let at bruge data, der genereres af udløseren som input til nogle af egenskaberne for den nye fil:

|Oprette filegenskab|Beskrivelse|
|---|---|
|Objekttype|Dette er typen Salesforce-objekt, du er interesseret i. Eksempler er kundeemne, konto osv.|
|Objekt-ID|Dette repræsenterer et id til objektet.|


1. Vælg **Tilføj en handling** link. Dette åbner søgefeltet, hvor du kan søge efter noget, du vil foretage. I dette eksempel skal har Salesforce handlinger særlig interesse.      
![Salesforce handlingen billede 1](./media/connectors-create-api-salesforce/action-1.png)  
- Angiv *salesforce* til at søge efter handlinger, der er relateret til salesforce.
- Vælg **Salesforce - Hent objekt** som handlingen, der skal udføres.   **Bemærk**: bliver du bedt om at tillade, at din logik app til at få adgang til din Salesforce-konto, hvis du ikke har gjort det tidligere.    
![Salesforce handlingen billede 2](./media/connectors-create-api-salesforce/action-2.png)    
- Kontrolelementet **Hent objekt** åbnes.  
- Vælg *lede* efter objekttypen.
- Vælg kontrolelementet **Objekt-ID** .
- Vælg **...** for at udvide listen over tokens, som kan bruges som input til handlinger.       
![Billede af Salesforce handlingen 3](./media/connectors-create-api-salesforce/action-3.png)    
- Vælg **Kundeemne ID** -objekt åbnes.   
![Billede af Salesforce handlingen 4](./media/connectors-create-api-salesforce/action-4.png)     
- Bemærk, at lede ID Tokenet er nu i kontrolelementet objekt-ID, der angiver, at få Objekthandling vil søge efter et kundeemne med et ID, der er lig med overlappende ID kundeemne, som udløste denne logik app.  
![Billede af Salesforce handlingen 5](./media/connectors-create-api-salesforce/action-5.png)  
- Gemme dit arbejde. Det var det, du har føjet Objekthandling få til din logik app. Få objekt styr ser sådan ud:    
![Billede af Salesforce handlingen 6](./media/connectors-create-api-salesforce/action-6.png)  

Nu hvor du har føjet en handling for at få et kundeemne, kan du gøre noget interessante med den nyligt oprettet kundeemne. I en virksomhed, kan du vil sende en mail til at informere en distributionsliste, et nyt kundeemne er blevet oprettet. Lad os bruge Office 365-forbindelsen til at sende en mail med nogle af de relevante oplysninger fra det nye kundeemne objekt i Salesforce.  

1. Klik på **Tilføj en handling** , og angiv *mail* kontrolelement. Dette felt kan afgrænse handlinger til dem, der er relateret til at sende og modtage mail.  
- Vælg listeelementet **Office 365 Outlook - sende en mail** . Hvis du ikke allerede har oprettet en *forbindelse* til din Office 365-konto, bliver du bedt om at angive din Office 365-legitimationsoplysninger for at oprette den nu. Når du er færdig, åbnes kontrolelementet til **at sende en mail** .        
![Billede af Salesforce handlingen 7](./media/connectors-create-api-salesforce/action-7.png)  
- Angiv den mailadresse, du vil sende mail til i kontrolelementet **til** .
-  I kontrolelementet **emne** , Skriv *Ny overlappende oprettet* - og vælg derefter tokenet *firma* . Derved vises feltet *firma* fra det nye kundeemne, der er oprettet i Salesforce.  
-  Du kan vælge nogen af tokens fra det nye kundeemne objekt i **brødteksten** kontrolelementet, og du kan også angive den tekst, du gerne vil have vist i brødteksten i mailen. Her er et eksempel:  
![Billede af Salesforce handlingen 8](./media/connectors-create-api-salesforce/action-8.png)   
- Gem arbejdsprocessen.  

Det var det. Din logik app er nu færdig.  

Nu kan du teste din logik app: i Salesforce, oprette et nyt kundeemne, der opfylder betingelsen, du har oprettet.  Hvis du har fulgt denne gennemgang fuldt ud, lige Opret et kundeemne med en e-mailadresse, der indeholder *amazon.com* i den. Efter et par sekunder din app logik skal udløses, og resultaterne kan ser nogenlunde sådan ud:  
![Billede af Salesforce handlingen 9](./media/connectors-create-api-salesforce/action-9.png)  

