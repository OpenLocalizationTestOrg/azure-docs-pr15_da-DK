Når du har overført posterne for dit domænenavn, skal du knytte dem til din online. Brug følgende trin til at aktivere domænenavne, ved hjælp af din webbrowser.

> [AZURE.NOTE] Det kan tage noget tid for TXT-poster, der er oprettet i de forrige trin at overføre via DNS-systemet. Du kan ikke tilføje domænenavn til din online, indtil TXT-posten er blevet overført. Hvis du bruger en A-post, kan du ikke føje A record domænenavnet til din online, indtil den TXT-post, der er oprettet i ovenstående trin er blevet overført.
>
> Du kan bruge en tjeneste, såsom <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> til at kontrollere, at TXT-posten er tilgængelig.

1. I din browser skal du åbne [Azure-portalen](https://portal.azure.com).

2. Klik på navnet på din online under fanen **Web Apps** , og derefter vælge **brugerdefinerede domæner**

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. Klik på **Tilføj hostname**i bladet **Custom domains** .
    
4. Bruge tekstbokse **Hostname** til at angive domænenavne skal knyttes til denne WebApp.

    ![](./media/custom-dns-web-site/add-custom-domain.png)

6.  Klik på **Valider**.

7.  Hvis du klikker på **Valider** Azure vil starte bekræfte domænet arbejdsproces. Dette søger efter ejerskab af domænet samt Hostname tilgængelighed og rapport lykkes eller detaljerede fejl med præskriptive guidence om, hvordan du retter fejlen.    

Du skal på dette tidspunkt ikke kunne Angiv navnet på det brugerdefinerede domæne i din browser, og se, at den er blevet tager dig til din online.
