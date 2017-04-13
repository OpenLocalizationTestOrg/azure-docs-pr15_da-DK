Azure kunder kan låse op 25.000 gratis mails hver måned. Disse 25.000 gratis månedlige mails kan give dig adgang til avancerede rapportering og analyse og [alle API'er][] (Web, SMTP, begivenhed, analysér og meget mere). Finde oplysninger om ekstra tjenester, der leveres af SendGrid, på siden [SendGrid funktioner][] .

### <a name="to-sign-up-for-a-sendgrid-account"></a>Til at tilmelde sig en SendGrid-konto

1. Log på [Azure Management Portal][].

2. Klik på **Ny**i ruden nederst i administrationsportalen.

    ![kommando-linjen nye][command-bar-new]

3. Klik på **Marketplace**.

    ![sendgrid store][sendgrid-store]

4. Vælg **SendGrid** i dialogboksen **Vælg et program og -tjenesten** , og klik på højre pil.

5. Vælg den ønskede at tilmelde sig **SendGrid** plan i dialogboksen **tilpasse programmer og tjenester** .

6. Angiv et navn, der identificerer din **SendGrid** tjeneste i indstillingerne for Azure, eller brug af standardværdien for **SendGridEmailDelivery.Simplified.SMTPWebAPI**. Navne skal være mellem 1 og 100 tegn og indeholder kun alfanumeriske tegn, stiplet, prikker og understregninger. Navnet skal være entydigt i listen over abonnerer på Azure Store elementer.

    ![store-skærmen-2][store-screen-2]

7. Vælge en værdi til et område for eksempel Vest USA.

8. Klik på den højre pil.

9. Gennemse planen og prisoplysninger under fanen **Gennemse køb** , og gennemgå de juridiske vilkår. Hvis du accepterer vilkårene, skal du markere afkrydsningsfeltet. Når du klikker på markeringen, begynder kontoen SendGrid [SendGrid klargøring proces].

    ![store-skærmen-3][store-screen-3]

10. Når du har bekræftet dit køb bliver du omdirigeret til dashboardet tilføjelsesprogrammer, og du får vist meddelelsen **Købe SendGrid**.

    ![sendgrid købe meddelelse][sendgrid-purchasing-message]

    Kontoen SendGrid er klargjort med det samme, og du får vist meddelelsen **blev købt tilføjelsesprogrammet SendGrid**. Din konto og legitimationsoplysninger oprettes nu. Du er klar til at sende mails på dette tidspunkt. 

    Til at ændre din abonnementsplan, eller se SendGrid kontakte indstillinger, klik på navnet på din SendGrid tjeneste til at åbne dashboardet SendGrid Marketplace. 

    ![sendgrid-Tilføj-på-dashboard][sendgrid-add-on-dashboard]

    Hvis du vil sende en mail via SendGrid, skal du angive dine legitimationsoplysninger (brugernavn og din adgangskode).

### <a name="to-find-your-sendgrid-credentials"></a>Finde dine SendGrid legitimationsoplysninger ###

1. Klik på **forbindelsesoplysninger**.

    ![sendgrid-forbindelse-info-knappen][sendgrid-connection-info-button]

2. Kopiere **adgangskode** og brugernavn for at bruge senere i dette selvstudium i dialogboksen *forbindelsesoplysninger* .

    ![sendgrid forbindelsesoplysninger][sendgrid-connection-info]

    Angiv din mail deliverability indstillinger, klik på knappen **Administrer** . Dette omdirigerer til din SendGrid i Kontrolpanel. 

    ![sendgrid Kontrolpanel][sendgrid-control-panel]

    Yderligere oplysninger om at komme i gang med SendGrid, se [SendGrid Kom godt i gang][].

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/sendgrid_BAR_NEW.PNG
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid_offerings_store.png
[store-screen-2]: ./media/sendgrid-sign-up/sendgrid_store_scrn2.png
[store-screen-3]: ./media/sendgrid-sign-up/sendgrid_store_scrn3.png
[sendgrid-purchasing-message]: ./media/sendgrid-sign-up/sendgrid_purchasing_message.png
[sendgrid-add-on-dashboard]: ./media/sendgrid-sign-up/sendgrid_add-on_dashboard.png
[sendgrid-connection-info]: ./media/sendgrid-sign-up/sendgrid_connection_info.png
[sendgrid-connection-info-button]: ./media/sendgrid-sign-up/sendgrid_connection_info_button.png
[sendgrid-control-panel]: ./media/sendgrid-sign-up/sendgrid_control_panel.png

<!--Links-->

[SendGrid funktioner]: http://sendgrid.com/features
[Azure Management-portalen]: https://manage.windowsazure.com
[SendGrid Introduktion]: http://sendgrid.com/docs
[SendGrid klargøring proces]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[alle API'er]: https://sendgrid.com/docs/API_Reference/index.html

