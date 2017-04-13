### <a name="prerequisites"></a>Forudsætninger

- En [SFTP](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol) -konto  


Før du kan bruge kontoen SFTP i en logik app, skal du tillade logik app til at oprette forbindelse til kontoen SFTP. Heldigvis kan du gøre dette nemt fra i din logik app på Azure-portalen.  

Her er trinnene for at tillade, at din logik app til at oprette forbindelse til kontoen SFTP:  
1. For at oprette en forbindelse til SFTP, i logik app designer skal du markere **Vis Microsoft administrerede API'er** i på rullelisten derefter angive *SFTP* i søgefeltet. Vælg **SFTP - når en fil er tilføjet eller ændret** udløseren:  
![Billede af SFTP online forbindelse 1](./media/connectors-create-api-sftp/sftp-1.png)  
2. Hvis du ikke har oprettet en hvilken som helst forbindelser til SFTP før, får du bedt om at angive din SFTP legitimationsoplysninger. Disse legitimationsoplysninger bruges til at tillade, at din logik app til at oprette forbindelse til, og få adgang til kontoen SFTP data:  
![Billede af SFTP online forbindelse 2](./media/connectors-create-api-sftp/sftp-2.png)  
3. Meddelelse om forbindelsen er oprettet, og du kan nu frit fortsætte med trin i din app, logik:   
 ![Billede af SFTP online forbindelse 3](./media/connectors-create-api-sftp/sftp-3.png) 
