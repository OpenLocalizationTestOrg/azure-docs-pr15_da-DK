
1. Gå til [Azure-portalen]. Klik på **Gennemse alt** > **Mobile-Apps** > backend-version, du lige har oprettet. Mobile-app-indstillinger, klik på **Hurtig start** > **Cordova**. Vælg **Opret en ny App**under **Konfigurer klientprogrammet**, og derefter på **Hent**. Dette henter et færdigt projekt Cordova for en allerede konfigureret til at oprette forbindelse til din back-end-app.

2. Pak hentede ZIP-filen til en mappe på harddisken, gå til fil (.sln) og åbne den ved hjælp af Visual Studio.

5. I Visual Studio, skal du vælge løsning platformen (Android, iOS eller Windows) fra rullemenuen ud for pilen start, og derefter vælge en bestemt installation enhed eller emulator ved at klikke på rullepilen på den grønne pil. Bemærk, at du kan bruge den standard Android platform og Ripple emulator. Mere avancerede selvstudier kræver, at du kan vælge en understøttede enheder eller emulator. 

6. Tryk på F5 eller klikke på den grønne pil til at opbygge og og køre din Cordova app. Hvis du ser en dialogboksen Sikkerhed i emulator anmoder om adgang til netværket, skal du acceptere den.   

7. Når du har den appen startes på enheden eller emulator, Skriv beskrivende tekst i **Enter ny tekst**, som _færdigt selvstudiet_ , og klik derefter på knappen **Tilføj** .  
Dette sender en anmodning om INDLÆG til den Azure backend-version du har installeret tidligere. Indsætter back end-data fra anmodningen er i tabellen TodoItem i SQL-Database, og returnerer oplysninger om de nyligt gemte elementer tilbage til mobilappen. Mobilappen viser disse data på listen.

    ![](./media/app-service-mobile-cordova-quickstart/quickstart-startup.png)
    
8. Gentag de forrige tre trin for hver enhed platform, som du planlægger at understøtte.

[Azure-portalen]: https://portal.azure.com/
