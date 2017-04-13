App Service Mobilapps bruger [Meddelelse Hubs] til at sende skubber, så du vil konfigurere en besked om hub til din mobile-app.

1. Gå til at **lede** [Azure Portal], > **App tjenester**, klik derefter på din app backend-version. Klik på **App Service Push**under **Indstillinger**.

2. Klik på **Konfigurer** for at konfigurere en besked om hub. Du kan enten oprette en hub eller oprette forbindelse til en eksisterende liste.

    ![](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Du har nu forbindelse en meddelelse om hub til din Mobile-App backend-version. Senere kan du konfigurere denne meddelelse hub til at oprette forbindelse til et platform meddelelse (PNS) til at overføre til enheder.

[Azure-portalen]: https://portal.azure.com/
[Meddelelse om Hubs]: https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-push-notification-overview/