#### <a name="prerequisites"></a>Forudsætninger
- En Azure konto Du kan oprette en [gratis konto](https://azure.microsoft.com/free)
- En [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) -konto 

Før du kan bruge din OneDrive-konto i en logik app, Godkend logik app til at oprette forbindelse til din OneDrive-konto.  Du kan gøre dette nemt i din logik app på Azure-portalen. 

Tillade din logik app til at oprette forbindelse til din OneDrive konto at benytte følgende fremgangsmåde:

1. Oprette en logik app. Vælg **Vis Microsoft administrerede API'er** i på rullelisten i designvisningen logik Apps, og angiv derefter "onedrive" i søgefeltet. Vælg en af udløsere eller handlinger:  
  ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Hvis du ikke tidligere har oprettet en forbindelse til OneDrive, bliver du bedt om at logge på med dine OneDrive-legitimationsoplysninger:  
  ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Vælg **Log på**, og Angiv dit brugernavn og din adgangskode. Vælg **Log på**:  
  ![](./media/connectors-create-api-onedrive/onedrive-3.png)   

    Disse legitimationsoplysninger anvendes til at tillade, at din logik app til at oprette forbindelse til, og få adgang til dataene i din OneDrive-konto. 
4. Vælg **Ja** for at tillade logik app til at bruge din OneDrive-konto:  
  ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Bemærk, at forbindelsen er oprettet. Fortsæt nu med andre trinnene i din app, logik:  
  ![](./media/connectors-create-api-onedrive/onedrive-5.png)
