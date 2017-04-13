Når du har overført posterne for dit domænenavn, skal du kunne Brug browseren til at bekræfte, at dit eget domænenavn kan bruges til at få adgang til din online i Azure App-tjeneste.

> [AZURE.NOTE] Det kan tage lidt tid, før din CNAME at overføre via DNS-systemet. Du kan bruge en tjeneste, såsom <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> til at kontrollere, at CNAME er tilgængelig.

Hvis du ikke allerede har tilføjet din online som et slutpunkt trafik Manager, skal du gøre dette, før navneoversættelse fungerer som brugerdefineret domæne navn omdirigerer til trafik Manager. Trafik Manager dirigerer derefter til din online. Brug oplysningerne i [Tilføj eller Slet slutpunkter](../articles/traffic-manager/traffic-manager-endpoints.md) til at tilføje din online som et slutpunkt i din profil trafik Manager.

> [AZURE.NOTE] Hvis din online ikke vises, når du tilføjer et slutpunkt skal du kontrollere, at den er konfigureret til **Standard** App Service plan tilstand. Hvis du vil arbejde med trafik Manager skal du bruge **standardtilstand for din online** .

1. I din browser skal du åbne [Azure-portalen](https://portal.azure.com).

1. Fanen **Web Apps** , klik på navnet på din online skal du vælge **Indstillinger**og derefter vælge **brugerdefinerede domæner**

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

1. Klik på **Tilføj hostname**i bladet **Custom domains** .
    
1. Bruge tekstbokse **Hostname** til at angive domænenavnet trafik Manager skal knyttes til denne WebApp.

    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)

1. Klik på **Valider** for at gemme den domæ navn konfiguration.

7.  Hvis du klikker på **Valider** Azure vil starte bekræfte domænet arbejdsproces. Dette søger efter ejerskab af domænet samt Hostname tilgængelighed og rapport lykkes eller detaljerede fejl med præskriptive guidence om, hvordan du retter fejlen.    

8.  Når bekræftelsen er fuldført **Tilføj hostname** knappen træder i kraft, og du vil kunne Tildel hostname. Gå nu til dit eget domænenavn i en browser. Du bør nu se din app, der kører på dit eget domænenavn. 

    Når konfigurationen er fuldført, vises det brugerdefinerede domænenavn i sektionen **domain names** for din online.

Du skal på dette tidspunkt ikke kunne Indtast trafik Manager navn domænenavnet i din browser og se, at den er blevet tager dig til din online.
