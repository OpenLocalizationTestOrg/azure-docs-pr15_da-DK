
Som standard kan API'er i en Mobile-App back-end aktiveres anonymt. Derefter skal vil du begrænse adgangen til kun godkendte klienter.  

+ **Node.js backend-version (via portalen)** :  
    
    Klik **Nemt tabeller** i Mobile-App- **Indstillinger**, og Vælg tabellen. Klik på **Rediger tilladelser**, Vælg **godkendt adgang** til alle tilladelser og derefter klikke på **Gem**. 

+ **.NET backend-version (C#)**:  

    Gå til **enheder**i project server > **TodoItemController.cs**. Tilføje den `[Authorize]` attribut til klassen **TodoItemController** på følgende måde. Hvis du vil begrænse adgangen kun til bestemte metoder, kan du også anvende denne attribut til disse metoder i stedet for klassen. Genudgive server projektet.


        [Authorize]
        public class TodoItemController : TableController<TodoItem>

+ **Node.js backend-version (via Node.js kode)** :  
    
    For at kræve godkendelse for tabeladgang, du tilføje følgende linje i scriptet Node.js server:


        table.access = 'authenticated';

    Få mere at vide referere til [Sådan: kræver godkendelse for at få adgang til tabeller](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). Hvis du vil lære at hente Kodeprojektet Hurtig start fra dit websted, skal du se [Sådan: hente Node.js back end-Hurtig start Kodeprojektet ved hjælp af ciffer](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).

