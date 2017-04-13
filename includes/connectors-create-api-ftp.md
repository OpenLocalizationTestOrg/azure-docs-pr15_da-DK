### <a name="prerequisites"></a>Forudsætninger

- Et [FTP-](https://wikipedia.org/wiki/File_Transfer_Protocol) konto  


Før du kan bruge din FTP-konto i en logik app, skal du Godkend logik app til at oprette forbindelse til din FTP-konto. Heldigvis kan du gøre dette nemt fra i din logik app på Azure-portalen.  

Her er trinnene for at tillade, at din logik app til at oprette forbindelse til din FTP-konto:  
1. For at oprette en forbindelse til FTP, i logik app designer skal du markere **Vis Microsoft administrerede API'er** i på rullelisten derefter angive *FTP* i søgefeltet. Vælg den udløser eller handling, du vil gerne bruge:  
![Trin i oprettelsen af FTP-forbindelse](./media/connectors-create-api-ftp/ftp-1.png)  
2. Hvis du ikke har oprettet en hvilken som helst forbindelser til FTP før, får du bedt om at angive dine FTP-legitimationsoplysninger. Disse legitimationsoplysninger bruges til at tillade, at din logik app til at oprette forbindelse til, og få adgang til kontoen FTP-data:  
![Trin i oprettelsen af FTP-forbindelse](./media/connectors-create-api-ftp/ftp-2.png)  
3. Bemærk forbindelsen er oprettet, og du kan nu frit fortsætte med trin i din app, logik:  
 ![Trin i oprettelsen af FTP-forbindelse](./media/connectors-create-api-ftp/ftp-3.png)  
