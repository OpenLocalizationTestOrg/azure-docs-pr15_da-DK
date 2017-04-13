####<a name="configuring-the-ios-project-in-xamarin-studio"></a>Konfiguration af iOS projektet i Xamarin Studio

1. Åbn **Info.plist**i Xamarin.Studio, og Opdater **Samlet id** med den samlet-ID, du oprettede tidligere med din nye App-ID.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)

2. Rul ned til **Baggrunden funktionsmåder** og markere feltet **Aktiver baggrund tilstande** og feltet **eksterne meddelelser** . 

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)

3. Dobbeltklik på dit projekt i panelet løsning for at åbne **Indstillinger for Project**.

4.  Vælg **iOS samlet logge** under **Opret**, og vælg de tilsvarende **identitet** og **artikel Provisioning profil** , du lige har oprettet til dette projekt. 

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

    Dette sikrer, at projektet bruger den nye profil til kodesignering af. Officiel Xamarin enheden klargøring dokumentation, finde [Xamarin klargøring af enheden].

####<a name="configuring-the-ios-project-in-visual-studio"></a>Konfiguration af iOS projektet i Visual Studio

1. Højreklik på projektet i Visual Studio, og klik derefter på **Egenskaber**.

2. Klik på fanen **iOS program** på siderne egenskaber, og Opdater **id** med det ID, du oprettede tidligere.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)

3. Vælg den tilsvarende **identitet** og **artikel Provisioning profil** , du lige har oprettet til dette projekt i fanen **iOS samlet logge** . 

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Dette sikrer, at projektet bruger den nye profil til kodesignering af. Officiel Xamarin enheden klargøring dokumentation, finde [Xamarin klargøring af enheden].

4. Dobbeltklik på Info.plist for at åbne den og derefter aktivere **RemoteNotifications** under baggrund funktionsmåder. 



[Xamarin enhed klargøring]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/