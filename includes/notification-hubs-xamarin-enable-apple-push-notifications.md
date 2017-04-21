

Hvis du vil registrere appen til pushmeddelelser gennem Apple Push meddelelse Service (APNS), skal du oprette et nyt push-certifikat, App-ID og klargøring profil for projektet på Apples udvikler portal. App-ID indeholder de konfigurationsindstillinger, der aktiverer din app til at sende og modtage pushmeddelelser. Disse indstillinger vil indeholde det push meddelelse certifikat, der er behov for at godkende med Apple Push meddelelse Service (APNS) ved afsendelse og modtagelse pushmeddelelser. Yderligere oplysninger om disse begreber dokumentationen til officielle [Apple Push Beskedtjeneste](http://go.microsoft.com/fwlink/p/?LinkId=272584) .


####<a name="generate-the-certificate-signing-request-file-for-the-push-certificate"></a>Generere filen certifikat logge anmodning om push-certifikat

Denne fremgangsmåde hjælper dig til at oprette den certifikatunderskrift. Dette kan bruges til at oprette en push-certifikat, der skal bruges sammen med APNS.

1. På din Mac skal du køre værktøjet Hovednøglering. Det kan åbnes fra mappen **programmer** eller **anden** mappen på det Start tastatur.

2. Klik på **Hovednøglering**, udvide **Certifikat assistent**, og klik derefter **anmode om et certifikat fra et nøglecenter …**.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. Vælg dit **Bruger-mailadresse** og **Almindelige navn** , Sørg for, **gemt på disken** er markeret, og klik derefter på **Fortsæt**. Lad feltet **CA mailadresse** tomme, da det ikke er påkrævet.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. Skriv et navn til filen certifikat logge anmode om (CSR) i **Gem som**, Vælg placeringen, i **hvor**og derefter klikke på **Gem**.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Dette gemmer CSR file i den valgte placering. Standardplaceringen er i computeren. Husk, at den placering, der er valgt for denne fil.


####<a name="register-your-app-for-push-notifications"></a>Registrere din app til pushmeddelelser

Oprette et nyt eksplicitte App-ID for dit program med Apple og også konfigurere det til pushmeddelelser.  

1. Gå til [iOS artikel Provisioning Portal](http://go.microsoft.com/fwlink/p/?LinkId=272456) på Apple Developer Center, logge på med dit Apple-ID, skal du klikke på **id'er**, og derefter klikke på **App-id'er**, og klik til sidst på den **+** logge til at registrere en ny app.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Opdater følgende tre felter til din nye app, og klik derefter på **Fortsæt**:

    * **Navn**: Skriv et beskrivende navn for din app i feltet **navn** i sektionen **App ID beskrivelse** .

    * **Samlet-id**: Angiv et **Samlet id** Under afsnittet **Eksplicitte App-ID** i formularen `<Organization Identifier>.<Product Name>` som nævnt i [App fordeling vejledning](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8). Dette skal svare til Hvad bruges også i XCode, Xamarin eller Cordova projektet til din app.

    * **Push-beskeder**: Kontroller indstillingen **Push-beskeder** i sektionen **App Services** .

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

3.  Gennemse indstillingen på siden Bekræft din App-ID-skærm, og når du har bekræftet dem på **Send**

4.  Når du har sendt det nye App-ID, vises skærmbilledet **registreringen er fuldført** . Klik på **udført**.

5. Find den app-ID, du lige har oprettet, og klik på dens række i Developer Center, under App id'er. At klikke på rækken app-ID vises app-oplysninger. Klik på knappen **Rediger** nederst.

6. Rul til bunden af skærmen, og klik på knappen **Opret certifikat** i afsnittet **Development Push SSL-certifikat**.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Derved vises assistenten "Tilføj iOS certifikat".

    > [AZURE.NOTE] Dette selvstudium bruger et udvikling certifikat. Den samme fremgangsmåde bruges, når du registrerer et fremstilling certifikat. Kun Sørg for, at du bruger den samme certifikattype, når du sender meddelelser.

7. Klik på **Vælg fil**, gå til den placering, hvor du gemte Kundeservicerepræsentanten for dit push-certifikat. Klik derefter på **Opret**.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

8. Når certifikatet, der er oprettet af portal, skal du klikke på knappen **Hent** .

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Dette overførsler det certifikat, som gemmes på computeren i mappen overførsler.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [AZURE.NOTE] Som standard den hentede fil et udvikling certifikat hedder **aps_development.cer**.

9. Dobbeltklik på hentede push certificate **aps_development.cer**. Derved installeres det nye certifikat i Nøgleringen, som vist nedenfor:

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [AZURE.NOTE] På navnet på dit certifikat kan være forskellige, men det, indledes med **Apple udvikling iOS Push tjenester:**.

10. Højreklik på det nye push-certifikat, du lige har oprettet i kategorien **certifikater** i Hovednøglering. Klik på **Eksporter**, give filen et navn, Vælg **.p12** format, og klik derefter på **Gem**.

    Husk, at det filnavn og placeringen af den eksporterede .p12 push-certifikat. Det bruges til at aktivere godkendelse med APNS ved at uploade det på klassisk Azure-portalen.



####<a name="create-a-provisioning-profile-for-the-app"></a>Oprette en klargøring profil for appen

1. Tilbage i <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS artikel Provisioning Portal</a>vælge **Klargøring profiler**, Vælg **alle**og derefter klikke på den **+** knappen for at oprette en ny profil. Dette starter guiden **Tilføj iOS Provisiong profil**

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. Markér **iOS App udvikling** under **udvikling** som typen provisiong profil, og klik på **Fortsæt**.


3. Derefter vælge den app-ID, du lige har oprettet fra rullelisten **App-ID** , og klik på **Fortsæt**

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)


4. Vælg din udvikling certifikat, der anvendes til kodesignering af på skærmbilledet **Vælg certifikater** , og klik på **Fortsæt**. Dette er et certifikat til signering, ikke det push-certifikat, du lige har oprettet.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)


5. Derefter skal du markere **enheder** , der skal bruges til test og klikke på **Fortsæt**

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)


6. Til sidst skal du vælge et navn til profilen i **Profilnavn**, klik på **Generer**.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)
