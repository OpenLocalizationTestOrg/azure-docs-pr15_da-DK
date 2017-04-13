
1. Gå til [Azure-portalen]. Klik på **Gennemse alt** > **Mobile-Apps** > backend-version, du lige har oprettet. Mobile-app-indstillinger, klik på **Hurtig start** > **Android)**. Klik på **Hent**under **konfigurere klientprogrammet**. Dette henter en komplet Android projekt til en allerede konfigureret til at oprette forbindelse til din back-end-app. 

2. Åbn det projekt ved hjælp af **Android Studio**, ved hjælp af **Importér projekt (Eklipse ADT, Gradle osv.)**. Kontrollér, at du har foretaget dine valg Importér for at undgå JDK fejl.

3. Tryk på knappen **Kør 'app'** til at opbygge projektet og starte appen i Android simulator.

4. Skriv beskrivende tekst, som _færdigt selvstudiet_ i appen, og klik derefter på knappen 'Tilføj'. Dette sender en anmodning om INDLÆG til den Azure backend-version du har installeret tidligere. Indsætter back end-data fra anmodningen er til TodoItem SQL-tabel, og returnerer oplysninger om de nyligt gemte elementer tilbage til mobilappen. Mobilappen viser disse data på listen. 

    ![](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)

[Azure-portalen]: https://portal.azure.com/
