De fleste af tid godkendelsesfejl skyldes forkert eller inkonsekvent konfigurationsindstillinger. Her er nogle bestemte forslag til ting, du skal kontrollere.

* Sørg for, at du ikke går glip af knappen **Gem** et vilkårligt sted. Er det ofte nemmere at gøre, og resultatet er, at du skal kigge på de korrekte værdier på en portal-side, men de rent faktisk ikke er gemt i Azure-miljø eller Azure AD-programmet.
* Sørg for, at det korrekte API-app eller WebApp er markeret, når indstillingerne for der er angivet for indstillinger, der konfigureres i bladet **Programindstillinger** af portalen Azure.  Også sørge for, at indstillingerne er angivet som **App indstillinger** og ikke **forbindelsesstrenge**, som minder om formatet på de to afsnit.
* Hente manifestet igen for at kontrollere, at til godkendelse til en JavaScript front-end `oauth2AllowImplicitFlow` blev ændret til `true`.
* Kontrollere, at du har brugt HTTPS, uanset hvor du har konfigureret URL-adresser:

    * I Projektkode
    * I CORS
    * I Azure App miljøindstillinger for hver API app og WebApp
    * Azure AD programindstillinger.
    
    Bemærk, at hvis du kopierer en API-app URL-adressen fra portalen, den ofte har `http://` , og du har manuelt ændre den til `https://`.

* Sørg for, at ændringerne kode er blevet installeret. For eksempel i en flere projekter løsning er det muligt at ændre et projekts kode, og vælg en af de andre ved et uheld, når du har til hensigt at implementere ændringen.
* Sørg for, at du går til HTTPS URL-adresser i din browser ikke HTTP URL-adresser. Visual Studio opretter som standard, publicere profiler med HTTP URL-adresser, og det er, hvad åbnes i browseren, når du installerer et projekt.
* Sørg for, at CORS er korrekt konfigureret på appen API, der ringer til JavaScript-kode for godkendelse til en JavaScript front-end. Hvis du er i tvivl om problemet er relateret til CORS, kan du prøve "*" som tilladte origin URL-adresse. 
* Åbn din browser udvikler værktøjer Console fane for at få flere fejloplysninger for et JavaScript front-end-program, og undersøge HTTP-anmodninger på netværket. Console fejlmeddelelser kan dog være vildledende. Hvis du får en meddelelse om fejlen CORS, være reelle problemet godkendelse. Du kan se, hvis det er tilfældet ved at køre appen med godkendelse midlertidigt midlertidigt deaktiveret.
* Kontrollér, at du får så mange oplysninger i fejlmeddelelser som muligt ved at angive [customErrors tilstand til slået fra](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview)for en .NET API-app.
* Starte en [fjernsession fejlfinding](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug)til en .NET API-app, og Undersøg værdierne i de variabler, der sendes til kode, der bruger ADAL til at få fat på en bærertoken eller kode, der kontrollerer krav mod forventede tjenestens primære ID. Bemærk, at din kode kan fortsætte konfigurationsværdier fra mange forskellige kilder, så det er muligt at finde overraskelser denne måde. Eksempelvis hvis du skriver `ida:ClientId` som `ida:ClientID` når du konfigurerer Azure App Service miljøindstillinger, koden kan få den `ida:ClientId` værdi, som det søger efter fra filen Web.config, ignorerer indstillingen Azure App Service. 
* Hvis ting, du ikke arbejder i et normalt vindue i Internet Explorer, kan et eksisterende logonnavn forstyrre; Prøv InPrivate og forsøg Chrome eller Firefox.
