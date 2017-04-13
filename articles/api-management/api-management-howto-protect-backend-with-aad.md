<properties
    pageTitle="Hvordan du beskytter en Web API back-end med Azure Active Directory og API administration"
    description="Lær, hvordan du beskytter en Web API back-end med Azure Active Directory og API-styring." 
    services="api-management"
    documentationCenter=""
    authors="steved0x"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="how-to-protect-a-web-api-backend-with-azure-active-directory-and-api-management"></a>Hvordan du beskytter en Web API back-end med Azure Active Directory og API administration

Den følgende video viser, hvordan du oprette en Web API back-end og beskytte den med OAuth 2.0 protocol med Azure Active Directory og API-styring.  Denne artikel indeholder en oversigt over og yderligere oplysninger om trinnene i videoen. Denne 24 minutters video viser dig hvordan til:

-   Opbygge en Web API back-end og sikre dem med AAD - starter ved 1:30
-   Importere API til administration af API – fra og med 7:10
-   Konfigurere portalen udvikler for at ringe API - fra og med 9:09
-   Konfigurere en på computeren til at ringe til API - fra og med 18:08
-   Konfigurere en politik til validering af JWT forhånd tillade anmodninger - fra og med 20:47

>[AZURE.VIDEO protecting-web-api-backend-with-azure-active-directory-and-api-management]

## <a name="create-an-azure-ad-directory"></a>Oprette en Azure AD-mappe

Til at sikre dine Web API sikkerhedskopien ved hjælp af Azure Active Directory, skal du først have en en AAD lejer. I denne video bruges en lejer, med navnet **APIMDemo** . Hvis du vil oprette en AAD lejer, logon til [Azure klassisk Portal](https://manage.windowsazure.com) og klikke på **Ny**->**App Services**->**Active Directory**->**Directory**->**Brugerdefineret oprette**. 

![Azure Active Directory][api-management-create-aad-menu]

I dette eksempel oprettes en mappe med navnet **APIMDemo** med en standarddomænet med navnet **DemoAPIM.onmicrosoft.com**. Denne mappe, der bruges i hele videoen.

![Azure Active Directory][api-management-create-aad]

## <a name="create-a-web-api-service-secured-by-azure-active-directory"></a>Oprette en Web API-tjeneste, sikret af Azure Active Directory

I dette trin skal oprettes en Web API back-end ved hjælp af Visual Studio-2013. I dette trin i videoen starter ved 1:30. Oprette Web API back end-projekt i Visual Studio Klik på **filer**->**Ny**->**Project**, og vælg **ASP.NET-webprogrammet** fra listen **Web** skabeloner. I denne video hedder projektet **APIMAADDemo**. Klik på **OK** for at oprette projektet. 

![Visual Studio][api-management-new-web-app]

Klik på **Web API** fra **vælge en skabelon** til at oprette en Web API-projekt. Klik på **Skift godkendelse**for at konfigurere Azure Directory godkendelse.

![Nyt projekt][api-management-new-project]

Klik på **Organisationskonti**, og angiv **domæne** for din AAD lejer. I dette eksempel er domænenavn **DemoAPIM.onmicrosoft.com**. Domænet for dit bibliotek kan fås fra fanen **domæner** i mappen.

![Domæner][api-management-aad-domains]

Konfigurere de ønskede indstillinger i dialogboksen **Skift godkendelse** , og klik på **OK**.

![Ændre godkendelse][api-management-change-authentication]

Visual Studio vil forsøge at registrere dit program med din Azure AD-mappe, og du muligvis bedt om at logge på ved Visual Studio, når du klikker på **OK** . Log på med en administratorkonto til adresselisten.

![Log på Visual Studio][api-management-sign-in-vidual-studio]

Konfigurere dette projekt som en Azure Web API-Markér afkrydsningsfeltet for **vært i skyen** , og klik derefter på **OK**.

![Nyt projekt][api-management-new-project-cloud]

Du kan blive bedt om at logge på Azure, og derefter kan du konfigurere Web App.

![Konfigurere][api-management-configure-web-app]

En ny **App-serviceaftale** med navnet **APIMAADDemo** er angivet i dette eksempel.

Klik på **OK** for at konfigurere Web App og oprette projektet.

## <a name="add-the-code-to-the-web-api-project"></a>Føje koden til Web API-projekt

Det næste trin i videoen tilføjer koden til Web API-projekt. Dette trin starter på 4:35.

Web API i dette eksempel implementerer en grundlæggende Lommeregner tjeneste ved hjælp af en model og en controller. Højreklik på **modeller** i **Solution Explorer** for at tilføje model for tjenesten, og vælg **Tilføj**, **klasse**. Navngive klassen `CalcInput` og klikke på **Tilføj**.

Tilføj følgende `using` sætningen til toppen af den `CalcInput.cs` fil.

    using Newtonsoft.Json;

 Erstat den oprettede klasses med følgende kode.

    public class CalcInput
    {
        [JsonProperty(PropertyName = "a")]
        public int a;

        [JsonProperty(PropertyName = "b")]
        public int b;
    }

Højreklik på **enheder** i **Solution Explorer** , og vælg **Tilføj**->**Controller**. Vælg **Web API 2 Controller - tom** , og klik på **Tilføj**. Skriv **CalcController** for Controller navnet, og klik på **Tilføj**.

![Tilføje Controller][api-management-add-controller]

Tilføj følgende `using` sætningen til toppen af den `CalcController.cs` fil.

    using System.IO;
    using System.Web;
    using APIMAADDemo.Models;

Erstat klassen genererede controller med følgende kode. Denne kode implementerer den `Add`, `Subtract`, `Multiply`, og `Divide` datahandlinger af grundlæggende Lommeregner API.

    [Authorize]
    public class CalcController : ApiController
    {
        [Route("api/add")]
        [HttpGet]
        public HttpResponseMessage GetSum([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a + b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/sub")]
        [HttpGet]
        public HttpResponseMessage GetDiff([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a - b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/mul")]
        [HttpGet]
        public HttpResponseMessage GetProduct([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a * b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/div")]
        [HttpGet]
        public HttpResponseMessage GetDiv([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a / b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }
    }

Tryk på **F6** for at opbygge og kontrollere løsningen.

## <a name="publish-the-project-to-azure"></a>Publicere projektet til Azure

I dette trin Visual Studio udgives project til Azure. I dette trin i videoen starter på 5:45.

Hvis du vil publicere projektet til Azure, skal du højreklikke på **APIMAADDemo** projektet i Visual Studio og vælge **Publicer**. Beholde standardindstillingerne i dialogboksen **Udgiv websted** , og klik på **Publicer**.

![Web-udgivelse][api-management-web-publish]

## <a name="grant-permissions-to-the-azure-ad-backend-service-application"></a>Giv tilladelser til Azure AD back end-tjenesteprogram

Der oprettes et nyt program til back end-tjenesten i Azure AD-biblioteket som en del af processen konfiguration af og publicering af projektet Web API. I dette trin for den video, fra og med 6:13, tildele tilladelser til Web API backend-version.

![Program][api-management-aad-backend-app]

Klik på navnet på programmet til at konfigurere de nødvendige tilladelser. Gå til fanen **Konfigurer** , og Rul ned til sektionen **tilladelser til andre programmer** . Klik på **Tilladelser for tjenesteprogram** rullelisten ud for **Windows** **Azure Active Directory**, Markér afkrydsningsfeltet for **læse directory data**, og klik på **Gem**.

![Føj tilladelser][api-management-aad-add-permissions]

>[AZURE.NOTE] Hvis **Windows** **Azure Active Directory** ikke er angivet under tilladelser til andre programmer, klik på **Tilføj program** og tilføje den på listen.

Skal du notere **App Id URI** til brug på et senere tidspunkt, når et Azure AD-program er konfigureret til portalen API Management udvikler.

![App-Id URI][api-management-aad-sso-uri]

## <a name="import-the-web-api-into-api-management"></a>Importere Web API til administration af API

API'er konfigureres fra portalen API publisher, som er åbnet via Azure klassisk administrationsportalen. Når portalen publisher, skal du klikke på **Administrer** i portalen Azure klassisk for din API Management-tjenesten. Hvis du endnu ikke har oprettet en API Management service-forekomst, kan du se [oprette en API Management service forekomst][] i [Administrer din første API][] -selvstudium.

![Publisher-portalen][api-management-management-console]

Handlinger kan være [føjet til API'er manuelt](api-management-howto-add-operations.md), eller de kan importeres. I denne video kan importeres handlinger i Swagger format fra og med 6:40.

Oprette en fil med navnet `calcapi.json` med følgende indhold og gemme den til din computer. Sørg for, at den `host` attribut punkter til din Web API backend-version. I dette eksempel `"host": "apimaaddemo.azurewebsites.net"` bruges.

{"swagger": "2.0", "info": {"Titel": "Lommeregner", "beskrivelse": "Arithmetics via HTTP!", "version": "1.0"}, "host": "apimaaddemo.azurewebsites.net", "basePath": "/ api", "farveskemaer": ["http"], "stier": {"/ tilføje? en = {a} og b = {b}": {"Hent": {"beskrivelse": "Svares med en sum af to tal.", "operationId": "Tilføj to heltal", "parametre": [{"navn": "a", "i": "forespørgsel", "beskrivelse": "første operand. Standardværdien er <code>51</code>. ","nødvendige": SAND,"standard":"51","Optæl": ["51"]}, {"navn":"b","i":"forespørgsel","beskrivelse":"anden operand. Standardværdien er <code>49</code>. ","nødvendige": SAND,"standard":"49","Optæl": ["49"]}],"svar": {}}}," / sub?a = {a} & b = {b} ": {"Hent": {"beskrivelse":"Svares med en forskellen mellem to tal.","operationId":"Subtraher to heltal","parametre": [{"navn":"a","i":"forespørgsel","beskrivelse":"første operand. Standardværdien er <code>100</code>. ","nødvendige": SAND,"standard":"100","Optæl": ["100"]}, {"navn":"b","i":"forespørgsel","beskrivelse":"anden operand. Standardværdien er <code>50</code>. ","nødvendige": SAND,"standard":"50","Optæl": ["50"]}],"svar": {}}}," / div?a = {a} & b = {b} ": {"Hent": {"beskrivelse":"Svares med en kvotienten for to tal.","operationId":"Divider to heltal","parametre": [{"navn":"a","i":"forespørgsel","beskrivelse":"første operand. Standardværdien er <code>100</code>. ","nødvendige": SAND,"standard":"100","Optæl": ["100"]}, {"navn":"b","i":"forespørgsel","beskrivelse":"anden operand. Standardværdien er <code>20</code>. ","nødvendige": SAND,"standard":"20","Optæl": ["20"]}],"svar": {}}}," / mul?a = {a} & b = {b} ": {"Hent": {"beskrivelse":"Svarer til et produkt af to tal.","operationId":"Multiplicer to heltal","parametre": [{"navn":"a","i":"forespørgsel","beskrivelse":"første operand. Standardværdien er <code>20</code>. ","nødvendige": SAND,"standard":"20","Optæl": ["20"]}, {"navn":"b","i":"forespørgsel","beskrivelse":"anden operand. Standardværdien er <code>5</code>. ","nødvendige": SAND,"standard":"5","Optæl": ["5"]}],"svar": {}}}}}

Klik på **API'er** i menuen **Administration af API** til venstre for at importere Lommeregner API skal, og klik derefter på **Importere API**.

![Knappen Importér API][api-management-import-api]

Udfør følgende trin for at konfigurere Lommeregner API.

1. Klik på **fra fil**, Naviger til den `calculator.json` fil, du har gemt, og klikke på alternativknappen **Swagger** .
2. Skriv **beregning** i tekstfeltet **URL-adresse API suffiks** .
3. Klik i boksen **produkter (valgfrit)** , og vælg **Starter**.
4. Klik på **Gem** for at importere API.

![Tilføje nye API'ER][api-management-import-new-api]

Når API er importeret, vises siden Oversigt til API i publisher-portalen.

## <a name="call-the-api-unsuccessfully-from-the-developer-portal"></a>Kalde API ikke lykkes fra portalen udvikler

På dette tidspunkt API er blevet importeret under ledelse af API, men kan endnu ikke kaldes korrekt fra portalen udvikler fordi back end-tjenesten er beskyttet med Azure AD-godkendelse. Dette er vist i videoen fra og med 7:40 at benytte følgende fremgangsmåde.

Klik på **udvikler portal** fra øverst til højre side af portalen publisher.

![Udvikler portal][api-management-developer-portal-menu]

Klik på **API'er** og **Lommeregner** API.

![Udvikler portal][api-management-dev-portal-apis]

Klik på **Prøv den**.

![Prøv det][api-management-dev-portal-try-it]

Klik på **Send** , og Bemærk svarstatus for **401 Uautoriseret**.

![Send][api-management-dev-portal-send-401]

Anmodningen er ikke autoriseret, fordi back-end API er beskyttet af Azure Active Directory. Før du korrekt kalder API udvikleren skal være konfigureret portal for at godkende udviklere, der bruger OAuth 2.0. Denne proces er beskrevet i følgende afsnit.

## <a name="register-the-developer-portal-as-an-aad-application"></a>Registrere portalen udvikler som et AAD program

Det første trin til konfiguration af portalen udvikler for at godkende udviklere, der bruger OAuth 2.0 er at registrere portalen udvikler som et AAD program. Dette er vist, fra og med 8:27 i videoen.

Gå til Azure AD-lejer fra det første trin i denne video kan i dette eksempel **APIMDemo** , og gå til fanen **programmer** .

![Nyt program][api-management-aad-new-application-devportal]

Klik på knappen **Tilføj** for at oprette et nyt Azure Active Directory-program, og vælg **Tilføj et program, udvikling af min organisation**.

![Nyt program][api-management-new-aad-application-menu]

Vælg **webprogram og/eller Web API**, Angiv et navn, og klik på pilen Næste. I dette eksempel bruges **APIMDeveloperPortal** .

![Nyt program][api-management-aad-new-application-devportal-1]

Angiv URL-adressen til dit API Management-tjenesten, og Føj **Sign-on URL'EN** til `/signin`. I dette eksempel bruges **https://contoso5.portal.azure-api.net/signin **.

Angiv URL-adressen til dit API Management-tjenesten og tilføje nogle entydige tegn **App Id URL-adresse** . Det kan være en hvilken som helst ønskede tegn og i dette eksempel bruges **https://contoso5.portal.azure-api.net/dp** . Når den ønskede **App egenskaber** er konfigureret, skal du markere afkrydsningsfeltet for at oprette programmet.

![Nyt program][api-management-aad-new-application-devportal-2]

## <a name="configure-an-api-management-oauth-20-authorization-server"></a>Konfigurere en server til godkendelse af API Management OAuth 2.0

Næste trin er at konfigurere en server til godkendelse af OAuth 2.0 API administration. Dette trin er vist i videoen fra og med 9:43.

Klik **sikkerhed** i API Management menuen til venstre, på **OAuth 2.0**, og klik derefter på **Tilføj godkendelse** server.

![Tilføje godkendelse server][api-management-add-authorization-server]

Angiv et navn og en valgfri beskrivelse i felterne **navn** og **Beskrivelse** . Disse felter bruges til at identificere den OAuth 2.0 godkendelse server i forekomsten af API administration. I dette eksempel bruges **godkendelse server demo** . Senere når du angiver en OAuth 2.0-server, der skal bruges til godkendelse for en API, du vælger dette navn.

**Klient registrering URL-adressen** til angive en pladsholderværdi som `http://localhost`.  **Klient registrering sidens URL-adresse** peger på den side, som brugerne kan bruge til at oprette og konfigurere deres egne konti til OAuth 2.0 udbydere, der understøtter brugeradministration af konti. I dette eksempel brugere ikke oprette og konfigurere deres egne konti, så der bruges en pladsholder.

![Tilføje godkendelse server][api-management-add-authorization-server-1]

Næste, Angiv **tilladelse slutpunkt URL-adresse** og **Token slutpunkt URL-adresse**.

![Godkendelse server][api-management-add-authorization-server-1a]

Disse værdier kan hentes fra siden **App slutpunkter** af programmet AAD, du har oprettet for portalen udvikler. Access slutpunkterne gå til fanen **Konfigurer** til programmet AAD, og klik på **Vis slutpunkter**.

![Program][api-management-aad-devportal-application]

![Vis slutpunkter][api-management-aad-view-endpoints]

Kopiér **OAuth 2.0 godkendelse slutpunkt** og sætte det ind i tekstfeltet **godkendelse slutpunkt URL-adresse** .

![Tilføje godkendelse server][api-management-add-authorization-server-2]

Kopiér **OAuth 2.0 token slutpunkt** og sætte det ind i tekstfeltet **Token slutpunkt URL-adresse** .

![Tilføje godkendelse server][api-management-add-authorization-server-2a]

Ud over at indsætte det token slutpunkt, tilføje en ekstra brødtekst parameter med navnet **ressource** og værdi bruges **App Id URI** fra programmet AAD til back end-tjenesten, der blev oprettet hvornår Visual Studio-projekt er udgivet.

![App-Id URI][api-management-aad-sso-uri]

Derefter skal du angive klient legitimationsoplysningerne. Dette er legitimationsoplysningerne for den ressource, du vil åbne, i dette tilfælde back end-tjenesten.

![Klient-legitimationsoplysninger][api-management-client-credentials]

Gå til fanen **Konfigurer** af programmet AAD til back end-tjenesten for at få **Klient-Id**, og Kopiér **Klient-Id**.

Få **Klienten hemmeligt** klik derefter på **Vælg varighed** rullemenuen i sektionen **taster** , og Angiv et interval. I dette eksempel bruges 1 år.

![Klient-ID][api-management-aad-client-id]

Klik på **Gem** for at gemme konfigurationen og vise tasten. 

>[AZURE.IMPORTANT] Skal du notere denne tast. Når du lukker vinduet Azure Active Directory konfiguration, kan du vises tasten igen.

Kopiere nøglen til Udklipsholder, skifte tilbage til portalen publisher, indsætte tasten i **Klienten hemmeligt** tekstfeltet og klikke på **Gem**.

![Tilføje godkendelse server][api-management-add-authorization-server-3]

Give en tilladelse kode er umiddelbart efter klient legitimationsoplysninger. Kopiér denne tilladelse kode og skifte tilbage til Azure AD udvikler portalen programmet Konfigurer siden, og sæt ind i tilladelsen give i feltet **Svar URL-adresse** og klikke på **Gem** igen.

![Svar URL-adresse][api-management-aad-reply-url]

Næste trin er at konfigurere tilladelser for portalen udvikler AAD programmet. Klik på **Tilladelser for tjenesteprogram** , og Markér afkrydsningsfeltet for **læse directory data**. Klik på **Gem** for at gemme ændringen, og klik derefter på **Tilføj program**.

![Føj tilladelser][api-management-add-devportal-permissions]

Klik på søgeikonet, skriver **APIM** i feltet Start med feltet Vælg **APIMAADDemo**og klikke på markeringen til at gemme.

![Føj tilladelser][api-management-aad-add-app-permissions]

Klik på **Delegerede tilladelser** for **APIMAADDemo** , og Markér afkrydsningsfeltet for **Access APIMAADDemo**, og klik på **Gem**. Dette giver mulighed for udvikleren portalen program tilladelse til at få adgang til back end-tjenesten.

![Føj tilladelser][api-management-aad-add-delegated-permissions]

## <a name="enable-oauth-20-user-authorization-for-the-calculator-api"></a>Aktivere OAuth 2.0 bruger tilladelse til Lommeregner API

Nu hvor OAuth 2.0-serveren er konfigureret, kan du angive det i sikkerhedsindstillingerne for din API. Dette trin er vist i videoen fra og med 14:30.

Klik på **API'er** i menuen til venstre, og klik på **Lommeregner** for at få vist og konfigurere indstillingerne for.

![Lommeregner API][api-management-calc-api]

Gå til fanen **sikkerhed** , Markér afkrydsningsfeltet **OAuth 2.0** , Vælg den ønskede godkendelse server **godkendelse server** rullelisten og klik på **Gem**.

![Lommeregner API][api-management-enable-aad-calculator]

## <a name="successfully-call-the-calculator-api-from-the-developer-portal"></a>Ringe korrekt Lommeregner API fra portalen udvikler

Nu hvor OAuth 2.0-godkendelse er konfigureret på API, kan dens handlinger kaldes korrekt i developer Center. Dette trin er vist i videoen fra og med 15:00.

Gå tilbage til handlingen **Tilføj to heltal** til tjenesten Lommeregner i portalen udvikler, og klik på **Prøv det**. Bemærk, at det nye element i afsnittet **Authorization** svarer til den tilladelse server, du lige har tilføjet.

![Lommeregner API][api-management-calc-authorization-server]

Vælg **godkendelseskode** fra rullelisten godkendelse og angive legitimationsoplysninger for kontoen, der skal bruges. Hvis du allerede er logget på med den konto, du ikke kan blive bedt om.

![Lommeregner API][api-management-devportal-authorization-code]

Klik på **Send** , og Bemærk, at **status for mødebesvarelse** for **200 OK** og resultaterne af handlingen i svar indholdet.

![Lommeregner API][api-management-devportal-response]

## <a name="configure-a-desktop-application-to-call-the-api"></a>Konfigurere en på computeren til at ringe til API

Den næste procedure i videoen starter 16:30 og konfigurerer en simpel-programmet på computeren til at ringe til API. Det første trin er at registrere-programmet på computeren i Azure AD og giver den adgang til mappen, og til back end-tjenesten. Der er en demonstration af-programmet på opkald til en handling på Lommeregner API på 18:25.

## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Konfigurere en politik til validering af JWT forhånd tillade anmodninger om

Den endelige procedure i videoen starter ved 20:48 og hvordan du kan bruge politikken [Validere JWT](https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT) at forhånd tillade anmodninger ved at validere access tokens for hver indgående anmodning. Hvis anmodningen ikke er blevet godkendt af politikken validere JWT, anmodningen blev blokeret af API administration og sendes ikke langs til backend-version.

    <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
        <openid-config url="https://login.windows.net/DemoAPIM.onmicrosoft.com/.well-known/openid-configuration" />
        <required-claims>
            <claim name="aud">
                <value>https://DemoAPIM.NOTonmicrosoft.com/APIMAADDemo</value>
            </claim>
        </required-claims>
    </validate-jwt>

Finde en anden demonstration af konfiguration og brug af denne politik [skyen dækker afsnit 177: mere API administrationsfunktioner](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) og hurtig frem til 13:50. Så spoler vi frem til 15:00 for at se de politikker, der er konfigureret i politikeditoren og derefter til 18:50 for en demonstration af kald af en handling fra portalen udvikler både med og uden tokenet krævede godkendelse.

## <a name="next-steps"></a>Næste trin
-   Se flere [videoer](https://azure.microsoft.com/documentation/videos/index/?services=api-management) om administration af API.
-   Se [fælles certifikatgodkendelse](api-management-howto-mutual-certificates.md) og [Opret forbindelse via VPN- eller ExpressRoute](api-management-howto-setup-vpn.md)til andre måder til at sikre din back end-tjeneste.

[api-management-management-console]: ./media/api-management-howto-protect-backend-with-aad/api-management-management-console.png

[api-management-import-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-new-api.png
[api-management-create-aad-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad-menu.png
[api-management-create-aad]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad.png
[api-management-new-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-web-app.png
[api-management-new-project]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project.png
[api-management-new-project-cloud]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project-cloud.png
[api-management-change-authentication]: ./media/api-management-howto-protect-backend-with-aad/api-management-change-authentication.png
[api-management-sign-in-vidual-studio]: ./media/api-management-howto-protect-backend-with-aad/api-management-sign-in-vidual-studio.png
[api-management-configure-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-configure-web-app.png
[api-management-aad-domains]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-domains.png
[api-management-add-controller]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-controller.png
[api-management-web-publish]: ./media/api-management-howto-protect-backend-with-aad/api-management-web-publish.png
[api-management-aad-backend-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-backend-app.png
[api-management-aad-add-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-permissions.png
[api-management-developer-portal-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-developer-portal-menu.png
[api-management-dev-portal-apis]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-apis.png
[api-management-dev-portal-try-it]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-try-it.png
[api-management-dev-portal-send-401]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-send-401.png
[api-management-aad-new-application-devportal]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal.png
[api-management-aad-new-application-devportal-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-1.png
[api-management-aad-new-application-devportal-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-2.png
[api-management-aad-devportal-application]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-devportal-application.png
[api-management-add-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server.png
[api-management-aad-sso-uri]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-sso-uri.png
[api-management-aad-view-endpoints]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-view-endpoints.png
[api-management-aad-client-id]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-client-id.png
[api-management-add-authorization-server-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1.png
[api-management-add-authorization-server-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2.png
[api-management-add-authorization-server-2a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2a.png
[api-management-add-authorization-server-3]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-3.png
[api-management-aad-reply-url]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-reply-url.png
[api-management-add-devportal-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-devportal-permissions.png
[api-management-aad-add-app-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-app-permissions.png
[api-management-aad-add-delegated-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-delegated-permissions.png
[api-management-calc-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-api.png
[api-management-enable-aad-calculator]: ./media/api-management-howto-protect-backend-with-aad/api-management-enable-aad-calculator.png
[api-management-devportal-authorization-code]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-authorization-code.png
[api-management-devportal-response]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-response.png
[api-management-calc-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-authorization-server.png
[api-management-add-authorization-server-1a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1a.png
[api-management-client-credentials]: ./media/api-management-howto-protect-backend-with-aad/api-management-client-credentials.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-aad-application-menu.png

[Oprette en API Management service-forekomst]: api-management-get-started.md#create-service-instance
[Administrere din første API]: api-management-get-started.md
