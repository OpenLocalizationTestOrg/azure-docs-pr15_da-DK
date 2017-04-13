###<a name="server-auth"></a>Sådan: godkende med en udbyder (Server Flow)

Hvis du vil have Mobile-Apps administrere godkendelsesprocessen i din app, skal du registrere din app til din identitetsudbyder. I din Azure App-tjeneste skal du konfigurere program-ID og hemmeligt fået af din udbyder.
Du kan finde yderligere oplysninger finder selvstudiet [Tilføj godkendelse til din app].

Når du har registreret dit identitetsudbyder, skal du blot kalde metoden .login() med navnet på din udbyder. For eksempel, logge på med Facebook-Brug følgende kode.

```
client.login("facebook").done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

Hvis du bruger en identitetsudbyder end Facebook, ændre den værdi, der overføres til logon metoden herover til et af følgende: `microsoftaccount`, `facebook`, `twitter`, `google`, eller `aad`.

I dette tilfælde håndterer Azure App Service OAuth 2.0 godkendelse strømmen ved at vise logonsiden for den valgte provider og oprette en App Service godkendelse tokenet efter vellykket logon til identitetsudbyder. Funktionen logon, returnerer når du er færdig, et JSON objekt (bruger), som viser både bruger-ID og App Service godkendelse token i felterne bruger-id og authenticationToken henholdsvis. Dette token kan cachelagret og bruges igen, før det udløber.

###<a name="client-auth"></a>Sådan: godkende med en udbyder (klient Flow)

Din app kan også uafhængigt Kontakt udbyderen af identitet og derefter angive det returnerede token til din App-tjeneste til godkendelse. Dette klient flow gør det muligt at angive en enkelt logonoplevelse for brugere eller til at hente yderligere brugerdata fra identitetsudbyder.

#### <a name="social-authentication-basic-example"></a>Sociale godkendelse grundlæggende eksempel

I dette eksempel bruges Facebook klient-SDK til godkendelse:

```
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```
I dette eksempel antages, at tokenet leveres af provideren respektive SDK er gemt i variablen token.

#### <a name="microsoft-account-example"></a>Microsoft-Account eksempel

I følgende eksempel bruges i Live SDK, som understøtter enkeltlogon til Windows Store-apps ved hjælp af Microsoft-Account:

```
WL.login({ scope: "wl.basic"}).then(function (result) {
      client.login(
            "microsoftaccount",
            {"authenticationToken": result.session.authentication_token})
      .done(function(results){
            alert("You are now logged in as: " + results.userId);
      },
      function(error){
            alert("Error: " + err);
      });
});
```

I dette eksempel henter et token fra Live Connect, som leveres til din App-tjenesten ved at ringe til funktionen login.

###<a name="auth-getinfo"></a>Sådan: få oplysninger om den godkendte bruger

Oplysningerne om godkendelse for den aktuelle bruger kan hentes fra den `/.auth/me` slutpunktet ved hjælp af en AJAX metode.  Kontrollér, at du har sat den `X-ZUMO-AUTH` sidehoved til din godkendelse token.  Godkendelse Tokenet er gemt i `client.currentUser.mobileServiceAuthenticationToken`.  For eksempel bruge Hent API:

```
var url = client.applicationUrl + '/.auth/me';
var headers = new Headers();
headers.append('X-ZUMO-AUTH', client.currentUser.mobileServiceAuthenticationToken);
fetch(url, { headers: headers })
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

Hent er tilgængelig, som en npm pakke eller browseren hentes fra CDNJS. Du kan også bruge jQuery eller en anden AJAX-API til at hente oplysningerne.  Data vil blive modtaget som en JSON-objekt.
