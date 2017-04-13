

1. Log på [Firebase console](https://firebase.google.com/console/). Oprette et nyt Firebase projekt, hvis du ikke allerede har en.
2. Når projektet er blevet oprettet skal du klikke på **Tilføj Firebase til din Android-appen** og skal du følge vejledningen.

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. Klik på tandhjulsikonet for projektet i konsollen Firebase, og klik derefter på **Indstillinger for Project**.

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)

4. Klik på fanen **Skyen Messaging** i dine indstillinger for project, og Kopiér værdien af den **Server nøgle** og **Afsender-ID**.  Disse værdier bruges senere til at konfigurere meddelelse hubben Access-politik og din meddelelse handler i appen.
  