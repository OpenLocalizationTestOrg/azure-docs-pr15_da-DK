

1. Start **Hovednøglering**på din Mac. Åbn **Mine certifikater** under **kategori** på værktøjslinjen venstre navigationn. Find det SSL-certifikat, du har hentet i forrige afsnit og videregive dens indhold. Vælg kun certifikatet (ikke markerer privat nøgle), og [Eksportér](https://support.apple.com/kb/PH20122?locale=en_US).

2. [Azure-portalen](https://portal.azure.com/), klik på **Gennemse alt** > **App Services** > din Mobile-App backend-version. Klikke på **App Service Push**under **Indstillinger**, og derefter klikke på navnet på din meddelelse hub. Gå til **Apple Push meddelelse Services** > **Upload certifikat**. Overfør .p12-fil, du skal vælge den korrekte **tilstand** (afhængigt af om din klient SSL certifikat fra tidligere er fremstilling eller sandkassetilstand). Gem ændringerne.

Tjenesten er nu konfigureret til at arbejde med pushmeddelelser på iOS!

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
