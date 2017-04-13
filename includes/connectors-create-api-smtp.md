### <a name="prerequisites"></a>Forudsætninger

- En [SMTP](https://wikipedia.org/wiki/Simple_Mail_Transfer_Protocol) -konto  


Før du kan bruge din SMTP-konto i en logik app, skal du tillade logik app til at oprette forbindelse til din SMTP-konto. Heldigvis kan du gøre dette nemt fra i din logik app på Azure-portalen.  

Her er trinnene for at tillade, at din logik app til at oprette forbindelse til din SMTP-konto:  
1. Klik på **Vis Microsoft administrerede API'er** i på rullelisten for at oprette en forbindelse til SMTP i logik app designer, og angiv *SMTP* i søgefeltet. Vælg den udløser eller handling, du vil gerne bruge:  
![](./media/connectors-create-api-smtp/smtp-1.png)  
2. Hvis du ikke har oprettet en forbindelse til SMTP før, får du bedt om at angive din SMTP-legitimationsoplysninger. Disse legitimationsoplysninger bruges til at tillade, at din logik app til at oprette forbindelse til, og få adgang til kontoen SMTP data:  
![](./media/connectors-create-api-smtp/smtp-2.png)  
3. Meddelelse om forbindelsen er oprettet, og du kan nu frit fortsætte med trin i din app, logik:  
 ![](./media/connectors-create-api-smtp/smtp-3.png)  

