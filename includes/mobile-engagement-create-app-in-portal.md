1. Logge på [Klassisk Azure-portalen](https://manage.windowsazure.com), og klik derefter på **+ Ny** nederst på skærmen.

2. Klik på **App-tjenester**, og klik derefter **Mobile aftale**og derefter på **Opret**.

    ![](./media/mobile-engagement-create-app-in-portal/create-mobile-engagement-app.png)

3. Angiv følgende oplysninger i den viste, der vises:

    ![](./media/mobile-engagement-create-app-in-portal/create-azme-popup.png)

    - **Programnavn**: navnet på dit program. 
    - **Platform**: Target platform til appen. Du skal oprette én Mobile aftale app per platform, som du målretter til din mobilenhed. 
    - **Programmet ressourcenavn**: navn, som dette program er tilgængelige via API'er og URL-adresser. 
    - **Placering**: område/Data center, hvor denne app og app af websteder skal placeres.
    - **Samling**: Vælg en tidligere oprettet af websteder eller vælg 'Ny samling'.
    - **Navn på samling**: repræsenterer din gruppe af programmer. Dette sikrer også alle dine apps findes i en gruppe, som tillader aggregeret beregninger af målepunkter. Du skal bruge din firmanavn eller afdeling her Hvis det er relevant.

4. Vælg den app, du lige har oprettet i fanen **programmer** .

5. Klik på **FORBINDELSESOPLYSNINGER** for at få vist forbindelsesindstillingerne sætte i din SDK-integration i din-mobilappen.

6. Kopiere **FORBINDELSESSTRENGEN** – dette er, hvad du skal identificere denne app i dit programkode og oprette forbindelse til Mobile aftale fra din App.

    ![](./media/mobile-engagement-create-app-in-portal/app-connection-info-page.png)

