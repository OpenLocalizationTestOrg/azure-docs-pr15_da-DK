#### <a name="prerequisites"></a>Forudsætninger
- En Azure konto Du kan oprette en [gratis konto](https://azure.microsoft.com/free)
- En [Dynamics CRM Online](https://www.microsoft.com/en-us/dynamics/crm-free-trial-overview.aspx) -konto 

Før du bruger din Dynamics konto i en logik app, Godkend logik app til at oprette forbindelse til kontoen CRM Online. Du kan gøre dette nemt i din logik app på Azure-portalen. 

Tillade din logik app til at oprette forbindelse til kontoen CRM Online at benytte følgende fremgangsmåde:

1. Oprette en logik app. Vælg **Vis Microsoft administrerede API'er** i på rullelisten i designvisningen logik Apps, og angiv derefter "dynamics" i søgefeltet. Vælg en af udløsere eller handlinger:  
  ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. Hvis du ikke tidligere har oprettet en forbindelse til Dynamics, bliver du bedt om at logge på med legitimationsoplysningerne Dynamics:  
  ![](./media/connectors-create-api-crmonline/dynamics-signin.png)
3. Vælg **Log på**, og Angiv dit brugernavn og din adgangskode. Vælg **Log på**. 

    Disse legitimationsoplysninger anvendes til at tillade, at din logik app til at oprette forbindelse til, og få adgang til dataene i din Dynamics-konto. 
4. Bemærk, at forbindelsen er oprettet. Fortsæt nu med andre trinnene i din app, logik:  
  ![](./media/connectors-create-api-crmonline/dynamics-properties.png)
