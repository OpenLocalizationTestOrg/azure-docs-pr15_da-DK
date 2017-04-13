#### <a name="prerequisites"></a>Forudsætninger
- En Azure konto Du kan oprette en [gratis konto](https://azure.microsoft.com/free)
- En [Office 365](https://office365.com) -konto  

Godkend logik app til at oprette forbindelse til din Office 365-konto, før du bruger Office 365-konto i en logik app. Du kan gøre dette nemt i din logik app på Azure-portalen.  

Tillade din logik app til at oprette forbindelse til din Office 365-konto, der er at benytte følgende fremgangsmåde:

1. Oprette en logik app. Vælg **Vis Microsoft administrerede API'er** i på rullelisten i designvisningen logik Apps, og angiv derefter "office 365" i søgefeltet. Vælg en af udløsere eller handlinger:  
    ![Trin i oprettelsen af Office 365-forbindelse](./media/connectors-create-api-office365-outlook/office365-sendemail.png)  

2. Hvis du ikke tidligere har oprettet en forbindelse til Office 365, bliver du bedt om at logge på med din Office 365-legitimationsoplysninger:  
    ![Trin i oprettelsen af Office 365-forbindelse](./media/connectors-create-api-office365-outlook/office365-signin.png)  

3. Vælg **Log på**, og Angiv dit brugernavn og din adgangskode. Vælg **Log på**:  
    ![Trin i oprettelsen af Office 365-forbindelse](./media/connectors-create-api-office365-outlook/office365-usernamepassword.png)

    Disse legitimationsoplysninger anvendes til at tillade, at din logik app til at oprette forbindelse til, og få adgang til din Office 365-konto. 

4. Bemærk, at forbindelsen er oprettet. Fortsæt nu med andre trinnene i din app, logik:   
    ![Trin i oprettelsen af Office 365-forbindelse](./media/connectors-create-api-office365-outlook/office365-sendemailproperties.png)  
  