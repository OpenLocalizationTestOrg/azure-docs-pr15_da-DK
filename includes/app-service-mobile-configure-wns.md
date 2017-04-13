
1. [Azure-portalen](https://portal.azure.com/), klik på **Gennemse alt** > **App Services** > din Mobile-App backend-version. Klikke på **App Service Push**under **Indstillinger**, og derefter klikke på navnet på din meddelelse hub.

2. Gå til **Windows (WNS)**, Angiv **Security key** (klient hemmeligt) og **Pakke SID** , som du har hentet fra webstedet Live-tjenester og derefter klikke på **Gem**.

    ![Indstille nøglen WNS i portalen](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

Din backend-version er nu konfigureret til at bruge WNS til at sende pushmeddelelser.
