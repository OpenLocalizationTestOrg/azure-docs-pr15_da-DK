<properties
    pageTitle="Sådan aktiveres udgivelsen af Oprindelig klient-apps med proxy programmer | Microsoft Azure"
    description="Omhandler, hvordan du aktiverer oprindelig klient apps til at kommunikere med Azure AD-program-Proxy Connector til at levere sikker fjernadgang til dine lokale apps."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>

# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>Sådan aktiveres oprindelig klient apps til at interagere med proxy programmer

Azure Active Directory-proxyen er meget bruges til at udgive browserprogrammer som SharePoint, Outlook Web Access og brugerdefinerede line of business-applikationer. Det kan også bruges til at publicere oprindelig klient apps, som er forskellige fra webapps, fordi de får installeret på en enhed. Det gør du ved at understøtte Azure AD udstedt tokens, der sendes som standard Godkend HTTP-headere.

![Relation mellem slutbrugere, Azure Active Directory og udgivne programmer](./media/active-directory-application-proxy-native-client/richclientflow.png)

Den anbefalede metode til at publicere sådanne programmer er at bruge den Azure AD Authentication Library, som tager sig af alle godkendelse besvær og understøtter mange forskellige klient-miljøer. Proxyen passer ind i det [Oprindelige program til Web API-scenarie](active-directory-authentication-scenarios.md#native-application-to-web-api). Processen med at løse dette er som følger:

## <a name="step-1-publish-your-application"></a>Trin 1: Publicere dit program

Publicere dit proxy-program, som du ville gøre andre programmer, tildele brugere og give dem premium eller grundlæggende licenser. Flere oplysninger under [Udgiv programmer med proxyen](active-directory-application-proxy-publish.md).

## <a name="step-2-configure-your-application"></a>Trin 2: Konfigurere dit program

Konfigurere din oprindelige program på følgende måde:

1. Log på portalen Azure klassisk.
2. Vælg Active Directory-ikonet på menuen til venstre, og vælg derefter adresselisten.
3. Klik på **programmer**i menuen øverste. Hvis ingen apps er blevet føjet til adresselisten, vises denne side kun linket **Tilføj en App** . Klik på linket, eller også kan du klikke på knappen **Tilføj** på kommandolinjen.
4. Klik på linket for at **tilføje et program, udvikling af min organisation**på siden **Hvad vil du gøre** .
5. Angiv et navn for dit program på siden **fortælle os om dit program,** og vælg **Native client-program**. Tryk på pileikonet for at fortsætte.
6. På siden **programoplysninger** **Omdirigere URI** for oprindelige klientprogrammet, og klik derefter markering for at afslutte.

Dit program er blevet tilføjet, og du føres til Hurtig Start-siden for dit program.

## <a name="step-3-grant-access-to-other-applications"></a>Trin 3: Give adgang til andre programmer

Aktivere det oprindelige program til at være vises til andre programmer i mappen:

1. Klikke på **programmer**i menuen øverste, Vælg det nye oprindelige program, og klik derefter på **Konfigurer**.
2. Rul ned til sektionen **tilladelser til andre programmer** . Klik på knappen **Tilføj program** og vælg det proxy-program, du vil give adgang til oprindelige program, og klik på markeringen i det nederste højre hjørne. Vælg det nye tilladelsesniveau i rullemenuen **Delegerede tilladelser** .

![Føj tilladelser til andre programmer skærmbillede - program](./media/active-directory-application-proxy-native-client/delegate_native_app.png)

## <a name="step-4-edit-the-active-directory-authentication-library"></a>Trin 4: Redigere Active Directory Authentication Library

Redigere den oprindelige programkode i konteksten godkendelse af den Active Directory godkendelse bibliotek (ADAL) til at omfatte følgende:

        // Acquire Access Token from AAD for Proxy Application
        AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<TenantId>");
        AuthenticationResult result = authContext.AcquireToken("< Frontend Url of Proxy App >",
                                                        "< Client Id of the Native app>",
                                                        new Uri("< Redirect Uri of the Native App>"),
                                                        PromptBehavior.Never);

        //Use the Access Token to access the Proxy Application
        HttpClient httpClient = new HttpClient();
        httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
        HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");

Variablerne, der skal erstattes på følgende måde:

- **TenantId** kan findes i GUID i URL-adressen til **programmets konfigurationssiden,** lige efter "/ bibliotek /".
- **Front end URL-adressen** er front-end URL-adressen du har angivet i programmet Proxy og kan findes på siden **konfiguration** af proxy-app.
- **Klient-Id** for den oprindelige app kan findes på siden **Konfigurer** i det oprindelige program.
- **Omdirigere URI af den oprindelige app** kan findes på siden **Konfigurer** i det oprindelige program.

![Ny oprindelige program konfigurere siden skærmbillede](./media/active-directory-application-proxy-native-client/new_native_app.png)

Du kan finde flere oplysninger om det oprindelige program flow, [oprindelige program til web API](active-directory-authentication-scenarios.md#native-application-to-web-api).


## <a name="see-also"></a>Se også

- [Publicere programmer, der bruger dit eget domænenavn](active-directory-application-proxy-custom-domains.md)
- [Aktivere betinget adgang](active-directory-application-proxy-conditional-access.md)
- [Arbejde med krav opmærksom på programmer](active-directory-application-proxy-claims-aware-apps.md)
- [Aktivere enkelt Log på](active-directory-application-proxy-sso-using-kcd.md)

Se [proxyen blog](http://blogs.technet.com/b/applicationproxyblog/) for de seneste nyheder og opdateringer
