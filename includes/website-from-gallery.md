Azure Marketplace gør tilgængelige et bredt udvalg af populære Onlines udviklet af Microsoft, fra tredjepart firmaer, og Åbn kilde software initiativer. Webapps, der er oprettet fra Azure Marketplace kræver ikke installation af software end browseren bruges til at oprette forbindelse til [Azure Preview Portal](http://go.microsoft.com/fwlink/?LinkId=529715). 

I dette selvstudium lærer du:

- Sådan oprettes en ny online via Azure Marketplace.

- Sådan installeres online via Azure Preview-portalen.
 
Du skal oprette en WordPress blog, der bruger en standardskabelon. Følgende illustration viser det færdige program:


![WordPress blog][13]

>[AZURE.NOTE] Hvis du vil komme i gang med Azure App Service før tilmelding til en Azure-konto, skal du gå til [Prøve App Service](http://go.microsoft.com/fwlink/?LinkId=523751), hvor du straks kan oprette en forbigående starter WebApp i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.

## <a name="create-a-web-app-in-the-portal"></a>Oprette en WebApp i portalen

1. Log på portalen Azure Preview.

2. Åbn Azure Marketplace enten ved at klikke på ikonet **Marketplace** :

    ![Ikon for Marketplace][marketplace]

    Eller ved at klikke på ikonet **Ny** på øverst til højre på dashboardet til og vælge **Marketplace** på bottow på listen.
    
    ![Opret et nyt][5]
    
3. Vælg **Web + Mobile**. Søg efter **WordPress** , og klik på ikonet **WordPress** .

    ![WordPress listen][7]
    
5. Når du har læst beskrivelsen af appen WordPress, Vælg **Opret**.

6. Klik på **WebApp**, og Angiv de ønskede værdier til konfiguration af din online.
    
    ![konfigurere din app][8]

7. Klik på **databasen**, og Angiv de ønskede værdier til konfiguration af din MySQL-database. 

    ![Konfigurer database][database]

8. Angiv et navn til en ny ressourcegruppe.

    ![Angiv ressourcegruppe][groupname]

8. Hvis det er nødvendigt, skal du klikke på **abonnement**, og angive abonnementet, der skal bruges. 

7. Når du er færdig med at definere WebApp, skal du klikke på **Opret**, og Vent, mens der oprettes den nye WebApp.

   Når appen er blevet oprettet, vises den ressourcegruppe, der indeholder WebApp og database.

   ![Vis gruppe][resourcegroup]

## <a name="launch-and-manage-your-wordpress-web-app"></a>Start og administrere din WordPress online
    
1. Klik på dit nye WebApp til at se oplysninger om din app.

    ![Start dashboard][10]

2. Klik på **Gennemse** eller linket under **URL-adressen** til at åbne den online velkomstsiden på siden **Essentials** .

    ![webstedets URL-adresse][browse]

3. Hvis du ikke har installeret WordPress, Angiv de korrekte konfigurationsoplysninger, der kræves, før WordPress og klikke på **Installer WordPress** for at færdiggøre konfiguration og åbne den WebApp-logonside.

4. Klik på **Login** og angive dine logonoplysninger.  

5. Har du en ny WordPress WebApp, der ligner den nedenstående WebApp.    

    ![webstedet WordPress][13]






[5]: ./media/website-from-gallery/start-marketplace.png
[6]: ./media/website-from-gallery/wordpressgallery-02.png
[7]: ./media/website-from-gallery/search-web-app.png
[8]: ./media/website-from-gallery/set-web-app.png
[9]: ./media/website-from-gallery/wordpressgallery-05.png
[10]: ./media/website-from-gallery/select-web.png
[13]: ./media/website-from-gallery/wordpressgallery-09.png
[webapps]: ./media/website-from-gallery/selectwebapps.png
[database]: ./media/website-from-gallery/set-db.png
[resourcegroup]: ./media/website-from-gallery/show-rg.png
[browse]: ./media/website-from-gallery/browse-web.png
[marketplace]: ./media/website-from-gallery/marketplace-icon.png
[groupname]: ./media/website-from-gallery/set-rg.png
