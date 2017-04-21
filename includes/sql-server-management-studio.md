
   * Log på Azure-konto ved at angive dine legitimationsoplysninger.

     Denne metode er hurtigere og nemmere, men hvis du bruger denne metode kan du ikke se Azure SQL-Database eller Mobile-tjenester i vinduet **Server Stifinder** .

     Klik på knappen **Opret forbindelse til Azure** i **Server Explorer**. Der er et alternativ Højreklik på noden **Azure** , og klik derefter på **Opret forbindelse til Azure** i genvejsmenuen.

   * Installere et management-certifikat, der giver adgang til din konto.

     Højreklik på noden **Azure** i **Server Explorer**, og klik derefter på **Administrer abonnementer** i genvejsmenuen. Klik på fanen **certifikater** i dialogboksen **Administrer Azure abonnementer** , og klik derefter på **Importer**. Følg vejledningen for at hente og derefter importere abonnementsfilen (også kaldet en *.publishsettings* -fil) til din Azure-konto.

     > [AZURE.NOTE] Hent abonnementsfilen til en mappe uden for din datakilde kode kataloger (for eksempel i mappen overførsler), og derefter slette den, når importen er færdig. En skadelig bruger, der får adgang til abonnementsfilen kan redigere, oprette og slette dine Azure tjenester.

    Se, [hvordan du oprette forbindelse til Azure fra Visual Studio](http://go.microsoft.com/fwlink/?LinkId=324796)kan finde flere oplysninger.
