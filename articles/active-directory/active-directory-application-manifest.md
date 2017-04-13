<properties
   pageTitle="Om programmanifestet Azure Active Directory | Microsoft Azure"
   description="Detaljerede beskrivelser af programmanifestet Azure Active Directory, som repræsenterer et programs identitet konfiguration i en Azure AD-lejer, og der bruges til at lette OAuth godkendelse, samtykke oplevelse og meget mere."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/11/2016"
   ms.author="dkershaw;bryanla"/>

# <a name="understanding-the-azure-active-directory-application-manifest"></a>Forstå programmanifestet Azure Active Directory

Programmer, der integreres med Azure Active Directory (AD) skal være registreret hos en Azure AD-lejer, give en fast identitet konfiguration for programmet. Denne konfiguration er hørt på kørselstidspunktet, aktivere scenarier, der tillader et program udlicitere og broker godkendelse/godkendelse gennem Azure AD. Finde flere oplysninger om Azure AD-Programmodellen, [tilføjelse, opdatering, og fjerne et program] [ ADD-UPD-RMV-APP] artikel.

## <a name="updating-an-applications-identity-configuration"></a>Opdatere et programs identitet konfiguration

Der er faktisk flere muligheder for at opdatere de egenskaber på et programs identitet konfiguration, som varierer i funktioner og grader for problemer, herunder følgende:

- Den ** [Azure klassisk portal] [ AZURE-CLASSIC-PORTAL] Web-brugergrænsefladen** gør det muligt at opdatere de mest almindelige egenskaber for et program. Dette er den hurtigste og mindst fejl giver risiko måde for at opdatere dit program egenskaber, men giver ikke fuld adgang til alle egenskaber, som de næste to metoder.
- Til mere avancerede scenarier, hvor du vil opdatere egenskaber, der ikke vises i portalen Azure klassisk, kan du ændre den **programmanifestet**. Dette er fokus i denne artikel og er beskrevet mere detaljeret Start i næste afsnit.
- Det er også muligt at **skrive et program, der bruger [Graph API] [ GRAPH-API] ** til at opdatere dit program, der kræver mest indsats. Det kan være en flot indstilling, selvom, hvis du skriver software til administration, eller har brug for at opdatere programmet egenskaber med jævne mellemrum i en automatiseret måde.

## <a name="using-the-application-manifest-to-update-an-applications-identity-configuration"></a>Brug af programmanifestet til at opdatere et programs identitet konfiguration
Via [Azure klassisk portal][AZURE-CLASSIC-PORTAL], kan du administrere dit program identitet konfiguration, ved at hente og overføre en JSON fil repræsentation, som kaldes et progammanifest. Ingen faktiske filen er gemt i mappen. Programmanifestet er blot en HTTP GET handling på objektet Azure AD Graph API-program, og overførslen er en HTTP programrettelse handling på objektet programmet på computeren.

Som et resultat for at kunne forstå format og egenskaber for programmanifestet, skal du referere til den Graph API [programmet enhed] [ APPLICATION-ENTITY] dokumentation. Eksempler på opdateringer, der kan udføres, selvom programmanifestet Overfør omfatter:

- **Declare tilladelse områder (oauth2Permissions)** , der vises af webstedet API. Se emnet "Udsætte API'er til andre webprogrammer" i [Integrering af programmer med Azure Active Directory] [ INTEGRATING-APPLICATIONS-AAD] finde oplysninger om implementering brugerrepræsentation ved hjælp af oauth2Permissions delegerede tilladelse omfang. Som tidligere nævnt programmet objektegenskaber er beskrevet i Graph API [enhed og kompleks Type] [ APPLICATION-ENTITY] referenceartikel, herunder den oauth2Permissions egenskab, der er en samling af typen [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION].
- **Declare programmet roller (appRoles), der vises af din app**. Objektet programmet appRoles egenskaben er en samling af typen [AppRole][APPLICATION-ENTITY-APP-ROLE]. Se indstillingen [Rollebaseret adgangskontrol i skyen-programmer ved hjælp af Azure AD] [ RBAC-CLOUD-APPS-AZUREAD] artikel for at få et implementering eksempel.
- **Declare kendte klient programmer (knownClientApplications)**, som gør det muligt at logisk knytter samtykke fra den angivne klient program(mer) til ressource/web API.
- **Anmode om Azure AD til udstedelse gruppe medlemskaber gøre krav** om logget brugeren (groupMembershipClaims).  Dette kan også være konfigureret til at udstede krav om brugerens directory roller medlemskaber. Se den [autorisation i skyen-programmer ved hjælp af AD grupper] [ AAD-GROUPS-FOR-AUTHORIZATION] artikel for at få et implementering eksempel.
- **Tillad dit program til at understøtte OAuth 2.0 Implicit give** flyder (oauth2AllowImplicitFlow). Denne type Giv flow bruges sammen med integreret JavaScript-websider eller enkelt side-programmer (SPA). Se [forstå den implicit OAuth2 give flow i Azure Active Directory]kan finde flere oplysninger om implicit tilladelse Giv[IMPLICIT-GRANT].
- **Aktiver brug af X509 certifikater, som den hemmelige nøgle** (keyCredentials). Se [oprette tjeneste og daemon apps i Office 365] [ O365-SERVICE-DAEMON-APPS] og [Developer's guide til auth med Azure ressourcestyring API] [ DEV-GUIDE-TO-AUTH-WITH-ARM] artikler implementering eksempler.
- **Tilføj en ny App ID URI** for dit program (identifierURIs[]). App-ID URI'er bruges til at entydigt identificerer et program i dets Azure AD-lejer (eller på tværs af flere Azure AD-lejere for flere lejer scenarier, når den er kvalificeret via bekræftet eget domæne). De bruges, når der anmodes om tilladelser til en ressource-programmet, eller hente et adgangstoken for en ressource-program. Når du opdaterer dette element, tildeles den samme opdatering tilsvarende service hovedstolens servicePrincipalNames [] samling, som findes i programmets privat lejer.

Programmanifestet indeholder også en god metode til at registrere tilstanden for dit program registrering. Da den er tilgængelig i JSON-format, kan fil, der repræsenterer kontrolleres i din versionsstyring sammen med dit program kildekode.

## <a name="step-by-step-example"></a>Trin efter trin eksempel
Nu kan gennemgå de trin, der kræves for at opdatere dit program identitet konfigurationen gennem programmanifestet. Vi vil fremhæve et af de foregående eksempler viser, hvordan du definere et nyt tilladelsesniveau område on-program, en ressource:

1. Gå til [Azure klassisk portal] [ AZURE-CLASSIC-PORTAL] og logge på med en konto, der har rettigheder, service-administrator eller samtidig administrator.

2. Når du har godkendt, Rul ned, og vælg filtypenavnet Azure "Active Directory" i det venstre navigationspanel (1) og derefter klikke på registreret den Azure AD-lejer, hvor dit program er (2).

    ![Vælg Azure AD-lejer][SELECT-AZURE-AD-TENANT]

3. Når siden med mappen dukker op, skal du klikke på "Programmer" (1) øverst på siden for at se en liste over programmer, der er registreret i lejeren. Find det program, du vil opdatere på listen, og klik på den (2).

    ![Vælg Azure AD-lejer][SELECT-AZURE-AD-APP]

4. Nu, hvor du har valgt hovedsiden i det program, skal du se funktionen "Administrere manifestet" nederst på siden (1). Hvis du klikker på dette link, bliver du bedt om enten at downloade eller uploade JSON manifestfilen. Klik på "Hent Manifest" (2). Dette efterfølges med det samme med dialogboksen Hent bekræftelse, hvor du skal bekræfte ved at klikke på "Hent manifestet" (3), og klik derefter enten at åbne eller gemme filen lokalt (4).

    ![Administrere manifestet, kan du hente indstilling][MANAGE-MANIFEST-DOWNLOAD]

    ![Hente manifestet][DOWNLOAD-MANIFEST]

5. I dette eksempel skal vi har gemt filen lokalt, så os til at åbne i en editor, foretage ændringer i JSON, og Gem igen. Her er, hvordan JSON strukturen ser ud i Visual Studio JSON-editor. Bemærk, at den følger skemaet for [programmet enhed] [ APPLICATION-ENTITY] som nævnt tidligere:

    ![Opdatere manifest JSON][UPDATE-MANIFEST]

    Eksempelvis skal Hvis vi vil implementere/visning et ny tilladelsesniveau kaldet "Employees.Read.All" på vores ressource program (API), du blot tilføje et nyt/andet element til samlingen oauth2Permissions Internet Explorer:

        "oauth2Permissions": [
        {
        "adminConsentDescription": "Allow the application to access MyWebApplication on behalf of the signed-in user.",
        "adminConsentDisplayName": "Access MyWebApplication",
        "id": "aade5b35-ea3e-481c-b38d-cba4c78682a0",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to access MyWebApplication on your behalf.",
        "userConsentDisplayName": "Access MyWebApplication",
        "value": "user_impersonation"
        },
        {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
        }
        ],

    Posten skal være entydige, og derfor skal du oprette en ny globalt entydigt ID (GUID) for den `"id"` egenskab. I dette tilfælde, da vi angivet `"type": "User"`, denne tilladelse kan være accepteret at af enhver konto, der er godkendt af Azure AD-lejer i programmet ressource/API er registreret. Dette giver klienten program tilladelse til at få adgang til det på firmaets vegne. Beskrivelse og viste navn strengene bruges under samtykke og få dem vist i portalen Azure klassisk.

6. Når du er færdig med at opdatere manifestet vende tilbage til siden Azure AD-program i portalen Azure klassisk, og klik på "Administrere manifestet" funktionen igen (1). Men denne gang vælge indstillingen "Overføre manifestet" (2). Svarende til overførslen, du vil vist igen med en anden dialogboks, bliver du spurgt om placeringen af JSON-fil. Klik på "Søg efter fil..." (3), derefter bruge dialogboksen "Vælg fil til at overføre" for at markere filen JSON (4), og tryk på "Åbn". Når dialogboksen går ikke til stede, Markér afkrydsningsfeltet "OK" (5) og din manifest overføres.  

    ![Administrere manifestet, overføre indstilling][MANAGE-MANIFEST-UPLOAD]

    ![Overføre manifest JSON][UPLOAD-MANIFEST]

    ![Overføre manifest JSON - bekræftelse][UPLOAD-MANIFEST-CONFIRM]

Nu hvor manifestet er gemt, kan du give en registreret klient programmet adgang til det nye tilladelsesniveau, som vi har tilføjet ovenfor. Denne gang kan du bruge den Azure klassisk portal Webbrugergrænseflade i stedet for at redigere klient progammanifest:  

1. Først gå til siden "Konfigurer" af klientprogrammet, du vil føje adgang til det nye API, og klik på knappen "Tilføj programmet".
2. Du kan derefter præsenteres med på listen over registrerede ressource programmer (API'er) i lejeren. Klik på plus / + symbol ud for ressource programmets navn for at vælge den.  
3. Klik derefter på markeringen i det nederste højre hjørne.
4. Når du vender tilbage til afsnittet "Tilføj program" i din klient konfigurationssiden, vil du se det nye ressource program på listen. Hvis du placerer markøren over afsnittet "Delegerede tilladelser" til højre for rækken, vises en rulleliste, vises. Klik på listen og derefter vælge det nye tilladelsesniveau for at føje den til kundens anmodede liste over tilladelser. Bemærk: denne nye tilladelse vil blive gemt i den klientprogrammet identitet konfiguration i egenskaben "requiredResourceAccess" af websteder.

![Tilladelser til andre programmer][PERMS-TO-OTHER-APPS]

![Tilladelser til andre programmer][PERMS-SELECT-APP]

![Tilladelser til andre programmer][PERMS-SELECT-PERMS]

Det var det. Nu dine programmer kører, ved hjælp af deres nye identitet konfiguration.

## <a name="next-steps"></a>Næste trin
- Finde flere oplysninger om relationen mellem et program-programmet på computeren og Service hovedstolen eller objekter i [webprogrammet og service vigtigste objekter i Azure AD][AAD-APP-OBJECTS].
- Se [Azure AD udvikler ordliste] [ AAD-DEVELOPER-GLOSSARY] for definitioner af nogle af de grundlæggende Azure Active Directory (AD) udvikler begreber.

Brug DISQUS kommentarer afsnittet nedenfor til at give feedback og Hjælp os med at tilpasse og forme vores indhold.

<!--Image references-->
[DOWNLOAD-MANIFEST]: ./media/active-directory-application-manifest/download-manifest.png
[MANAGE-MANIFEST-DOWNLOAD]: ./media/active-directory-application-manifest/manage-manifest-download.png
[MANAGE-MANIFEST-UPLOAD]: ./media/active-directory-application-manifest/manage-manifest-upload.png
[PERMS-SELECT-APP]: ./media/active-directory-application-manifest/portal-perms-select-app.png
[PERMS-SELECT-PERMS]: ./media/active-directory-application-manifest/portal-perms-select-perms.png
[PERMS-TO-OTHER-APPS]: ./media/active-directory-application-manifest/portal-perms-to-other-apps.png
[SELECT-AZURE-AD-APP]: ./media/active-directory-application-manifest/select-azure-ad-application.png
[SELECT-AZURE-AD-TENANT]: ./media/active-directory-application-manifest/select-azure-ad-tenant.png
[UPDATE-MANIFEST]: ./media/active-directory-application-manifest/update-manifest.png
[UPLOAD-MANIFEST]: ./media/active-directory-application-manifest/upload-manifest.png
[UPLOAD-MANIFEST-CONFIRM]: ./media/active-directory-application-manifest/upload-manifest-confirm.png

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-CLASSIC-PORTAL]: https://manage.windowsazure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

