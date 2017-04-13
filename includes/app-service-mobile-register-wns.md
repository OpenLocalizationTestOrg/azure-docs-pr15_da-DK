
1. Højreklik på Windows Store app projektet i Visual Studio Solution Explorer, skal du klikke på **Store** > **Knytte App med Store …**.

    ![Knytte app til Windows Store](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)

2. Klik på **Næste**i guiden, logge på med din Microsoft-konto, Skriv et navn for din app i **Reserver en ny app-navn**og derefter klikke på **Reserver**.

3. Når app registrering er blevet oprettet, Vælg appnavnet på det nye, skal du klikke på **Næste**og derefter klikke på **knytte**. Dette indsætter de nødvendige oplysninger for Windows Store registrering programmanifestet.

7. Gentag trin 1 og 3 for Windows Phone Store app projektet ved hjælp af den samme registrering, du tidligere har oprettet til Windows Store-app.  

7. Gå til [Windows-Udviklercenter](https://dev.windows.com/en-us/overview), logge på med din Microsoft-konto, skal du klikke på den nye app registrering i **Mine apps**, og derefter udvide **Services** > **Push-beskeder**.

8. Klik på **Live Services-websted** under **Windows Push meddelelse tjenester (WNS) og Microsoft Azure Mobile-Apps**på siden **Push-beskeder** , og notere værdierne i **Pakke SID** og den *aktuelle* værdi i **Programmet Secret**. 

    ![App indstilling i developer center](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

    > [AZURE.IMPORTANT] Hemmeligt programmet og pakke SID er vigtige sikkerhedslegitimationsoplysninger. Undlad at dele disse værdier med alle eller dele dem med din app.
