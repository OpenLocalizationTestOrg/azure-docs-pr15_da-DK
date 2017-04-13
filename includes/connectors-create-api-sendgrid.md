### <a name="prerequisites"></a>Forudsætninger
- En [SendGrid](https://www.SendGrid.com/) -konto 

Før du kan bruge kontoen SendGrid i en logik app, skal du tillade logik app til at oprette forbindelse til kontoen SendGrid. Heldigvis kan du gøre dette nemt fra i din logik app på Azure-portalen. 

Her er trinnene for at tillade, at din logik app til at oprette forbindelse til kontoen SendGrid:

1. For at oprette en forbindelse til SendGrid, i logik app designer skal du markere **Vis Microsoft administrerede API'er** i på rullelisten derefter angive *SendGrid* i søgefeltet. Vælg den udløser eller handling, du vil gerne bruge:  
  ![SendGrid trin 1](./media/connectors-create-api-sendgrid/sendgrid-1.png)
2. Hvis du ikke har oprettet en hvilken som helst forbindelser til SendGrid før, får du bedt om at angive din SendGrid legitimationsoplysninger. Disse legitimationsoplysninger bruges til at tillade, at din logik app til at oprette forbindelse til, og få adgang til kontoen SendGrid data:  
  ![SendGrid trin 2](./media/connectors-create-api-sendgrid/sendgrid-2.png)
3. Meddelelse om forbindelsen er oprettet, og du kan nu frit fortsætte med trin i din app, logik:  
  ![SendGrid trin 3](./media/connectors-create-api-sendgrid/sendgrid-3.png)   
